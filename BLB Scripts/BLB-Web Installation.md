#!/bin/bash

# Variable Declaration
K3S_VER="v1.29.4+k3s1"
HTTP_PORT=""
HTTPS_PORT=""
IP_ADDR=$(hostname -I | awk '{print $1}')
BANK="nmb"
PRODUCT="blb"
ENV="live"
NAMESPACE=$BANK-$PRODUCT-$ENV

DOMAIN="nimb.com.np"
FQDN="blbapp.$DOMAIN"
CERT=$(echo "<insert wildcard certificate here>" | base64 -w 0)
KEY=$(echo "<insert private key here>" | base64 -w 0)

DB=$BANK-$PRODUCT-$ENV
DB_IP="10.0.132.47"
DB_PORT="5432"
KAFKA_SVC="my-kafka"
KAFKA_NS="kafka"

DB_NAME=$(echo -n $DB | base64)
DB_PASSWORD=$(echo -n "blb@123#$" | base64)
DB_URL=$(echo -n "jdbc:postgresql://$DB_IP:$DB_PORT/$DB?stringtype=unspecified" | base64 -w 0)
DB_USER_NAME=$(echo -n "blbadmin" | base64)
DEPLOYMENT_NAMESPACE=$(echo -n "$NAMESPACE" | base64)
GATEWAY_URL=$(echo -n $FQDN | base64)
JWT_KEY=$(echo -n "RHQ0UkhqanFqVlhwTzlqZ0R0NFJIampxalZYcE85amcK" | base64)
KAFKA_SERVER=$(echo -n "$KAFKA_SVC.$KAFKA_NS.svc.cluster.local:9092" | base64)


BLB_BE_IMAGE="harbor.finpos.global/finpulse/blb-nmb:PROD.1"
BLB_FE_IMAGE="harbor.finpos.global/finpulse/blb-portal-nmb:PROD.1"


echo
echo "========== K3S INSTALLATION =========="

echo
echo "Disabling Swap..."
swapoff -a
sed -i '/ swap / s/^/#/' /etc/fstab
echo "--------------------------------"

###### Checking OS ######
yum --help > /dev/null
if [ $? -eq 0 ];
then
	echo
	echo "Installing policycoreutils-python package for semanage..."
	yum install -y policycoreutils-python > /dev/null
	echo "--------------------------------"
	echo
	echo "Allowing Ports in Selinux..."
	#semanage port -l
	semanage port --add --type http_port_t --proto tcp 6443
	semanage port --add --type http_port_t --proto tcp 80
	semanage port --add --type http_port_t --proto tcp 443
	echo "--------------------------------"
	echo
	echo "Enabling and Starting firewalld"
	systemctl enable firewalld
	systemctl start firewalld
	echo "--------------------------------"
	echo
	echo "Setting up firewall rules..."
	#firewall-cmd --list-ports
	firewall-cmd --permanent --add-port=6443/tcp #apiserver
	firewall-cmd --permanent --add-port=80/tcp #haproxy
	firewall-cmd --permanent --add-port=443/tcp #haproxy
	firewall-cmd --permanent --zone=trusted --add-source=10.42.0.0/16 #pods
	firewall-cmd --permanent --zone=trusted --add-source=10.43.0.0/16 #services
	firewall-cmd --reload
	echo "--------------------------------"
	
	echo
	echo "Installing curl..."
	yum install curl -y > /dev/null
	echo "--------------------------------"
	
	echo
	echo "Installing haproxy..."
	yum install -y epel-release > /dev/null
	yum install haproxy -y > /dev/null
	echo "--------------------------------"
	
else
	echo
	echo "Enabling and Starting UFW"
	systemctl enable ufw
	systemctl start ufw
	echo "--------------------------------"
	
	echo
	echo "Setting up firewall rules..."
	#ufw app list
	#ufw status numbered
	ufw allow 6443/tcp
	ufw allow 80,443/tcp
	ufw allow from 10.42.0.0/16
	ufw allow from 10.43.0.0/16
	ufw reload
	echo "--------------------------------"
	
	echo
	echo "Installing curl..."
	apt-get install curl -y > /dev/null
	echo "--------------------------------"
	
	echo
	echo "Installing haproxy..."
	add-apt-repository ppa:vbernat/haproxy-2.8 -y > /dev/null
	apt-get install haproxy -y > /dev/null
	echo "--------------------------------"
	
fi

echo
echo "Backing up haproxy config..."
cp /etc/haproxy/haproxy.cfg /etc/haproxy/haproxy.cfg.bak
echo "--------------------------------"

echo
echo "###### Installing K3S ######"
curl -sfL https://get.k3s.io | INSTALL_K3S_VERSION=$K3S_VER INSTALL_K3S_EXEC="--disable traefik" sh -
mkdir ~/.kube
cp /etc/rancher/k3s/k3s.yaml ~/.kube/config
echo
echo "--------------------------------"

echo
echo "Installing Helm..."
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
#helm version
echo "--------------------------------"

echo
echo "Creating kafka Namespace..."
kubectl create ns kafka
echo "--------------------------------"

echo
echo "Installing Kafka and Kafka UI using Helm..."
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo add kafka-ui https://ui.charts.kafbat.io/
helm install my-kafka bitnami/kafka --version 21.4.4 -n kafka
helm install my-kafka-ui kafka-ui/kafka-ui --version 1.4.0 -n kafka
echo "--------------------------------"

echo
echo "Creating Namespace for Ingress-Nginx..."
kubectl create ns ingress-nginx
echo "--------------------------------"

echo
echo "Installing Ingress-Nginx..."
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm install my-ingress-nginx ingress-nginx/ingress-nginx --version 4.10.1 -n ingress-nginx
#Updating Variable
HTTP_PORT=$(kubectl get svc -n ingress-nginx | grep LoadBalancer | awk -F '[:/ ]+' '{print $6}')
HTTPS_PORT=$(kubectl get svc -n ingress-nginx | grep LoadBalancer | awk -F '[:/ ]+' '{print $8}')
echo "--------------------------------"

echo
echo "Updating HA-Proxy config file to add Nginx route..."
cat <<EOF | sudo tee /etc/haproxy/haproxy.cfg > /dev/null
global
 maxconn 20000

defaults
 log global
 mode tcp
 retries 2
 timeout client 30m
 timeout connect 10s
 timeout server 30m
 timeout check 10s

listen https
 bind *:443
 server kubernetes-master $IP_ADDR:$HTTPS_PORT


listen http
 bind *:80
 server kubernetes-master $IP_ADDR:$HTTP_PORT
EOF
echo "--------------------------------"

echo
echo "Starting haproxy..."
systemctl enable haproxy
systemctl stop haproxy
systemctl start haproxy
echo "--------------------------------"

echo
echo "========== BLB Web BE Deployment =========="

echo
echo "Creating directory /opt/blb for hostpath volume mount..."
mkdir /opt/blb
echo "--------------------------------"

echo
echo "Creating direcoty /opt/blb-yamls for BLB yaml files..."
mkdir /opt/blb-yamls
cd /opt/blb-yamls
echo "--------------------------------"

echo
echo "Creating NS $NAMESPACE.."
kubectl create ns $NAMESPACE
echo "--------------------------------"

echo
echo "Creating PV..."
cat <<EOF | sudo tee finpro-blb-pv.yaml > /dev/null
apiVersion: v1
kind: PersistentVolume
metadata:
    name: finpro-blb-pv
spec:
  accessModes:
  - ReadWriteMany
  capacity:
    storage: 16Gi
  volumeMode: Filesystem
  persistentVolumeReclaimPolicy: Delete
  storageClassName: local-path
  hostPath:
    path: /opt/blb
EOF
kubectl apply -f finpro-blb-pv.yaml
echo "--------------------------------"

echo
echo "Creating PVC..."
cat <<EOF | sudo tee finpro-blb-pvc.yaml > /dev/null
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: finpro-blb-pvc
  namespace: $NAMESPACE
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 16Gi
  storageClassName: local-path
  volumeMode: Filesystem
EOF
kubectl apply -f finpro-blb-pvc.yaml
echo "--------------------------------"

echo
echo "Creating BLB Secrets..."
cat <<EOF | sudo tee finpro-blb-secret.yaml > /dev/null
apiVersion: v1
kind: Secret
metadata:
  name: finpro-blb-secret
  namespace: $NAMESPACE
data:
  DB_NAME: $DB_NAME
  DB_PASSWORD: $DB_PASSWORD
  DB_URL: $DB_URL
  DB_USER_NAME: $DB_USER_NAME
  DEPLOYMENT_NAMESPACE: $DEPLOYMENT_NAMESPACE
  GATEWAY_URL: $GATEWAY_URL
  JWT_KEY: $JWT_KEY
  KAFKA_SERVER: $KAFKA_SERVER
type: Opaque
EOF
kubectl apply -f finpro-blb-secret.yaml
echo "--------------------------------"

echo
echo "Creating secret to Store SSL Certificates..."
cat <<EOF | sudo tee finpro-blb-certs.yaml > /dev/null
apiVersion: v1
kind: Secret
metadata:
  name: finpro-blb-certs
  namespace: $NAMESPACE
type: kubernetes.io/tls
data:
  tls.crt: $CERT
  tls.key: $KEY
EOF
kubectl apply -f finpro-blb-certs.yaml
echo "--------------------------------"

echo
echo "========== BLB Web BE Deployment =========="

echo
echo "Creating Deployment and Service for Web BE..."
cat <<EOF | sudo tee finpro-blb.yaml > /dev/null
apiVersion: apps/v1
kind: Deployment
metadata:
  name: finpro-blb
  namespace: $NAMESPACE
  labels:
    app: finpro-blb
spec:
  replicas: 1
  selector:
    matchLabels:
      app: finpro-blb
  template:
    metadata:
      labels:
        app: finpro-blb
    spec:
      volumes:
        - name: finproblb
          persistentVolumeClaim:
            claimName: finpro-blb-pvc
      containers:
        - name: finpro-blb
          image: $BLB_BE_IMAGE
          ports:
            - containerPort: 8080
              protocol: TCP
            - containerPort: 443
              protocol: TCP
          env:
            - name: JWT_KEY
              valueFrom:
                secretKeyRef:
                  name: finpro-blb-secret
                  key: JWT_KEY
            - name: DB_URL
              valueFrom:
                secretKeyRef:
                  name: finpro-blb-secret
                  key: DB_URL
            - name: DB_USER_NAME
              valueFrom:
                secretKeyRef:
                  name: finpro-blb-secret
                  key: DB_USER_NAME
            - name: DB_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: finpro-blb-secret
                  key: DB_PASSWORD
            - name: KAFKA_SERVER
              valueFrom:
                secretKeyRef:
                  name: finpro-blb-secret
                  key: KAFKA_SERVER
            - name: DEPLOYMENT_NAMESPACE
              valueFrom:
                secretKeyRef:
                  name: finpro-blb-secret
                  key: DEPLOYMENT_NAMESPACE
            - name: GATEWAY_URL
              valueFrom:
                secretKeyRef:
                  name: finpro-blb-secret
                  key: GATEWAY_URL
            - name: JVM_OPTS
              value: -XX:+UseContainerSupport -XX:MaxRAMPercentage=80
          resources:
            limits:
              cpu: 500m
              memory: 1Gi
            requests:
              cpu: 400m
              memory: 500Mi
          volumeMounts:
            - name: finproblb
              mountPath: /opt/blb
          livenessProbe:
            httpGet:
              path: /v1/ping
              port: 8080
              scheme: HTTP
            initialDelaySeconds: 40
            timeoutSeconds: 40
            periodSeconds: 40
            successThreshold: 1
            failureThreshold: 3
          readinessProbe:
            httpGet:
              path: /v1/ping
              port: 8080
              scheme: HTTP
            initialDelaySeconds: 40
            timeoutSeconds: 40
            periodSeconds: 40
            successThreshold: 1
            failureThreshold: 3
          terminationMessagePath: /dev/termination-log
          terminationMessagePolicy: File
          imagePullPolicy: IfNotPresent
      restartPolicy: Always
      terminationGracePeriodSeconds: 30
      dnsPolicy: ClusterFirst
      securityContext: {}
      schedulerName: default-scheduler
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 25%
      maxSurge: 25%
  revisionHistoryLimit: 10
  progressDeadlineSeconds: 600

---
apiVersion: v1
kind: Service
metadata:
  name: finpro-blb
  namespace: $NAMESPACE
spec:
  selector:
    app: finpro-blb
  ports:
    - name: http
      protocol: TCP
      port: 80
      targetPort: 8080
  type: ClusterIP
EOF
kubectl apply -f finpro-blb.yaml
echo "--------------------------------"

echo
echo "========== BLB Web FE Deployment =========="

echo
echo "Creating Deployment and Service for BLB Web FE..."
cat <<EOF | sudo tee finpro-blb-portal.yaml > /dev/null
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: finpro-blb-portal
  namespace: $NAMESPACE
  labels:
    app: finpro-blb-portal
spec:
  replicas: 1
  selector:
    matchLabels:
      app: finpro-blb-portal
  template:
    metadata:
      labels:
        app: finpro-blb-portal
    spec:
      containers:
        - name: finpro-blb-portal-pod
          image: $BLB_FE_IMAGE
          imagePullPolicy: Always
          ports:
            - containerPort: 80
            - containerPort: 443
          resources:
            limits:
              cpu: 200m
              memory: 256Mi
            requests:
              cpu: 100m
              memory: 256Mi
          livenessProbe:
            tcpSocket:
              port: 80
            initialDelaySeconds: 3
            periodSeconds: 10
            timeoutSeconds: 5
            failureThreshold: 3
          readinessProbe:
            tcpSocket:
              port: 80
            initialDelaySeconds: 5
            periodSeconds: 5
            timeoutSeconds: 5
      restartPolicy: Always
      terminationGracePeriodSeconds: 30
      dnsPolicy: ClusterFirst
      securityContext: {}
      schedulerName: default-scheduler
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 25%
      maxSurge: 25%

---
apiVersion: v1
kind: Service
metadata:
  name: finpro-blb-portal
  namespace: $NAMESPACE
spec:
  selector:
    app: finpro-blb-portal
  ports:
    - name: http
      protocol: TCP
      port: 80
      targetPort: 80
  type: ClusterIP
EOF
kubectl apply -f finpro-blb-portal.yaml
echo "--------------------------------"

echo
echo "========== Ingress Resource for BLB =========="

echo
echo "Creating Ingress resource for BLB..."
cat <<EOF | sudo tee finpro-blb-ingress.yaml > /dev/null
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  annotations:
    nginx.ingress.kubernetes.io/add-base-url: "true"
    nginx.ingress.kubernetes.io/rewrite-target: /\$2
    nginx.ingress.kubernetes.io/ssl-redirect: "true"
    nginx.org/client-max-body-size: 50M
  labels:
    k8slens-edit-resource-version: v1
  name: finpro-blb-ingress
  namespace: $NAMESPACE
spec:
  ingressClassName: nginx
  tls:
    - hosts:
        - $DOMAIN
      secretName: finpro-blb-certs
  rules:
    - host: $FQDN
      http:
        paths:
          - path: /blb(/|$)(.*)
            pathType: ImplementationSpecific
            backend:
              service:
                name: finpro-blb-portal
                port:
                  number: 80
          - path: /web-api(/|$)(.*)
            pathType: ImplementationSpecific
            backend:
              service:
                name: finpro-blb
                port:
                  number: 8080
EOF
kubectl apply -f finpro-blb-ingress.yaml
echo "--------------------------------"
