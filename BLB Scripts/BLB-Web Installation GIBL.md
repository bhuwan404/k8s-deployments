INSTALLATION SCRIPT WITH K3S
```
#!/bin/bash

# Variable Declaration
K3S_VER="v1.29.4+k3s1"
HTTP_PORT=""
HTTPS_PORT=""
IP_ADDR=$(hostname -I | awk '{print $1}')
BANK="gibl"
PRODUCT="blb"
ENV="live"
NAMESPACE=$BANK-$PRODUCT-$ENV

DOMAIN="gibl.com.np"
FQDN="blbapp.$DOMAIN"
CERT=$(echo "-----BEGIN CERTIFICATE-----
MIIG0DCCBbigAwIBAgIQAp+13blJc7PWOt2UptLyCjANBgkqhkiG9w0BAQsFADBZ
MQswCQYDVQQGEwJVUzEVMBMGA1UEChMMRGlnaUNlcnQgSW5jMTMwMQYDVQQDEypE
aWdpQ2VydCBHbG9iYWwgRzIgVExTIFJTQSBTSEEyNTYgMjAyMCBDQTEwHhcNMjMx
MjIyMDAwMDAwWhcNMjUwMTIxMjM1OTU5WjBXMQswCQYDVQQGEwJOUDESMBAGA1UE
BxMJS2F0aG1hbmR1MRwwGgYDVQQKExNHbG9iYWwgSU1FIEJhbmsgTHRkMRYwFAYD
VQQDDA0qLmdpYmwuY29tLm5wMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKC
AQEAwpSIufV0XbMgk+UwE5/lOVCHG68lVcidlExjgI06RH3ZnhET5I2DKkXlNK8R
9Z8yVePrJ69lxOS0hjiORnsggkmnZQCqKxmfsL9MxjZ7bRVK+XaJhJWaKI7Q4r9q
SC7Oi05jDJAWOnalV55/VNlY4fdWzcw5vKHCnPBals/1zSioBRgaHhmKyVK6egs0
c89BUSNvVoGkZRpL1oLMdXc8ktib6pdE50g09cTlDUA51busKjfG10qALP2m7eF2
By/S9nSIBVn7Wbh2ZzD+Y8ITdv9iJTIUK98oZvo/OzEmrZNjoCCN/bS6JdPBpNKs
XLpKQN//EWfhlqAXSQN+TOEjwwIDAQABo4IDlDCCA5AwHwYDVR0jBBgwFoAUdIWA
wGbH3zfez70pN6oDHb7tzRcwHQYDVR0OBBYEFLZ2RWSZ5ugtmSiGtIbG1VaCUCtQ
MCUGA1UdEQQeMByCDSouZ2libC5jb20ubnCCC2dpYmwuY29tLm5wMD4GA1UdIAQ3
MDUwMwYGZ4EMAQICMCkwJwYIKwYBBQUHAgEWG2h0dHA6Ly93d3cuZGlnaWNlcnQu
Y29tL0NQUzAOBgNVHQ8BAf8EBAMCBaAwHQYDVR0lBBYwFAYIKwYBBQUHAwEGCCsG
AQUFBwMCMIGfBgNVHR8EgZcwgZQwSKBGoESGQmh0dHA6Ly9jcmwzLmRpZ2ljZXJ0
LmNvbS9EaWdpQ2VydEdsb2JhbEcyVExTUlNBU0hBMjU2MjAyMENBMS0xLmNybDBI
oEagRIZCaHR0cDovL2NybDQuZGlnaWNlcnQuY29tL0RpZ2lDZXJ0R2xvYmFsRzJU
TFNSU0FTSEEyNTYyMDIwQ0ExLTEuY3JsMIGHBggrBgEFBQcBAQR7MHkwJAYIKwYB
BQUHMAGGGGh0dHA6Ly9vY3NwLmRpZ2ljZXJ0LmNvbTBRBggrBgEFBQcwAoZFaHR0
cDovL2NhY2VydHMuZGlnaWNlcnQuY29tL0RpZ2lDZXJ0R2xvYmFsRzJUTFNSU0FT
SEEyNTYyMDIwQ0ExLTEuY3J0MAwGA1UdEwEB/wQCMAAwggF8BgorBgEEAdZ5AgQC
BIIBbASCAWgBZgB2AE51oydcmhDDOFts1N8/Uusd8OCOG41pwLH6ZLFimjnfAAAB
jJDkRS8AAAQDAEcwRQIhALU/N9bXfrkVXIc4qqNG2s2QxSi6J7k/vmQkhgsT6w1Y
AiB5EkLDzro/b0h/KGKNJGnmx/EDATU2X6HylhWHFnE5OwB1AH1ZHhLheCp7HGFn
fF79+NCHXBSgTpWeuQMv2Q6MLnm4AAABjJDkRRcAAAQDAEYwRAIgLw5GXL8wkk89
FvI3illGJKETZZdobVwziRNYd+7j06ECIFdKRldSDLgJ2yBfY7oibFGBgEZqiLgx
vbiujtPkvrHEAHUA5tIxY0B3jMEQQQbXcbnOwdJA9paEhvu6hzId/R43jlAAAAGM
kORFQwAABAMARjBEAiBdsIgZexAc19Ck+olgLjghMGUc1Mya47h+C5hl1LvO+AIg
HAekf/sKJmoOzUsvxu5BuBHuFdcWR6ZvwdBKsUpwyIEwDQYJKoZIhvcNAQELBQAD
ggEBAKNA/dK4CqTxDHD1z7310WB9DAewiYNDi5WWc8ZunyAMi9qwTatYQnG4lLXr
JpHKQEQ2RLsWZqWYDHrPJNSLb+6mAPC/NlRRCCuhszUp/di8pkWm5miPISRiOMLR
g3XOgJwcLuy8Tb+2GqUZ1Pt7YuLNkjU3sthNAuKGUTs00JkdZCPtHTwZofwY0lB7
YAPUUQ8lzf4iD0qy7uCElRVCJpODsLmOUdN7UtIW99VcsnLxZazmnK0S6LHChBcr
+bvATFqYxUcqMj3A4GT3g7SwyxT/YtBU7X/fxZe/O8zutT2TnAJPLclET0bEEK6Y
7nsboaiYLdj8Je8tdj1lteDdv/g=
-----END CERTIFICATE-----
-----BEGIN CERTIFICATE-----
MIIEyDCCA7CgAwIBAgIQDPW9BitWAvR6uFAsI8zwZjANBgkqhkiG9w0BAQsFADBh
MQswCQYDVQQGEwJVUzEVMBMGA1UEChMMRGlnaUNlcnQgSW5jMRkwFwYDVQQLExB3
d3cuZGlnaWNlcnQuY29tMSAwHgYDVQQDExdEaWdpQ2VydCBHbG9iYWwgUm9vdCBH
MjAeFw0yMTAzMzAwMDAwMDBaFw0zMTAzMjkyMzU5NTlaMFkxCzAJBgNVBAYTAlVT
MRUwEwYDVQQKEwxEaWdpQ2VydCBJbmMxMzAxBgNVBAMTKkRpZ2lDZXJ0IEdsb2Jh
bCBHMiBUTFMgUlNBIFNIQTI1NiAyMDIwIENBMTCCASIwDQYJKoZIhvcNAQEBBQAD
ggEPADCCAQoCggEBAMz3EGJPprtjb+2QUlbFbSd7ehJWivH0+dbn4Y+9lavyYEEV
cNsSAPonCrVXOFt9slGTcZUOakGUWzUb+nv6u8W+JDD+Vu/E832X4xT1FE3LpxDy
FuqrIvAxIhFhaZAmunjZlx/jfWardUSVc8is/+9dCopZQ+GssjoP80j812s3wWPc
3kbW20X+fSP9kOhRBx5Ro1/tSUZUfyyIxfQTnJcVPAPooTncaQwywa8WV0yUR0J8
osicfebUTVSvQpmowQTCd5zWSOTOEeAqgJnwQ3DPP3Zr0UxJqyRewg2C/Uaoq2yT
zGJSQnWS+Jr6Xl6ysGHlHx+5fwmY6D36g39HaaECAwEAAaOCAYIwggF+MBIGA1Ud
EwEB/wQIMAYBAf8CAQAwHQYDVR0OBBYEFHSFgMBmx9833s+9KTeqAx2+7c0XMB8G
A1UdIwQYMBaAFE4iVCAYlebjbuYP+vq5Eu0GF485MA4GA1UdDwEB/wQEAwIBhjAd
BgNVHSUEFjAUBggrBgEFBQcDAQYIKwYBBQUHAwIwdgYIKwYBBQUHAQEEajBoMCQG
CCsGAQUFBzABhhhodHRwOi8vb2NzcC5kaWdpY2VydC5jb20wQAYIKwYBBQUHMAKG
NGh0dHA6Ly9jYWNlcnRzLmRpZ2ljZXJ0LmNvbS9EaWdpQ2VydEdsb2JhbFJvb3RH
Mi5jcnQwQgYDVR0fBDswOTA3oDWgM4YxaHR0cDovL2NybDMuZGlnaWNlcnQuY29t
L0RpZ2lDZXJ0R2xvYmFsUm9vdEcyLmNybDA9BgNVHSAENjA0MAsGCWCGSAGG/WwC
ATAHBgVngQwBATAIBgZngQwBAgEwCAYGZ4EMAQICMAgGBmeBDAECAzANBgkqhkiG
9w0BAQsFAAOCAQEAkPFwyyiXaZd8dP3A+iZ7U6utzWX9upwGnIrXWkOH7U1MVl+t
wcW1BSAuWdH/SvWgKtiwla3JLko716f2b4gp/DA/JIS7w7d7kwcsr4drdjPtAFVS
slme5LnQ89/nD/7d+MS5EHKBCQRfz5eeLjJ1js+aWNJXMX43AYGyZm0pGrFmCW3R
bpD0ufovARTFXFZkAdl9h6g4U5+LXUZtXMYnhIHUfoyMo5tS58aI7Dd8KvvwVVo4
chDYABPPTHPbqjc1qCmBaZx2vN4Ye5DUys/vZwP9BFohFrH/6j/f3IL16/RZkiMN
JCqVJUzKoZHm1Lesh3Sz8W2jmdv51b2EQJ8HmA==
-----END CERTIFICATE-----
-----BEGIN CERTIFICATE-----
MIIDjjCCAnagAwIBAgIQAzrx5qcRqaC7KGSxHQn65TANBgkqhkiG9w0BAQsFADBh
MQswCQYDVQQGEwJVUzEVMBMGA1UEChMMRGlnaUNlcnQgSW5jMRkwFwYDVQQLExB3
d3cuZGlnaWNlcnQuY29tMSAwHgYDVQQDExdEaWdpQ2VydCBHbG9iYWwgUm9vdCBH
MjAeFw0xMzA4MDExMjAwMDBaFw0zODAxMTUxMjAwMDBaMGExCzAJBgNVBAYTAlVT
MRUwEwYDVQQKEwxEaWdpQ2VydCBJbmMxGTAXBgNVBAsTEHd3dy5kaWdpY2VydC5j
b20xIDAeBgNVBAMTF0RpZ2lDZXJ0IEdsb2JhbCBSb290IEcyMIIBIjANBgkqhkiG
9w0BAQEFAAOCAQ8AMIIBCgKCAQEAuzfNNNx7a8myaJCtSnX/RrohCgiN9RlUyfuI
2/Ou8jqJkTx65qsGGmvPrC3oXgkkRLpimn7Wo6h+4FR1IAWsULecYxpsMNzaHxmx
1x7e/dfgy5SDN67sH0NO3Xss0r0upS/kqbitOtSZpLYl6ZtrAGCSYP9PIUkY92eQ
q2EGnI/yuum06ZIya7XzV+hdG82MHauVBJVJ8zUtluNJbd134/tJS7SsVQepj5Wz
tCO7TG1F8PapspUwtP1MVYwnSlcUfIKdzXOS0xZKBgyMUNGPHgm+F6HmIcr9g+UQ
vIOlCsRnKPZzFBQ9RnbDhxSJITRNrw9FDKZJobq7nMWxM4MphQIDAQABo0IwQDAP
BgNVHRMBAf8EBTADAQH/MA4GA1UdDwEB/wQEAwIBhjAdBgNVHQ4EFgQUTiJUIBiV
5uNu5g/6+rkS7QYXjzkwDQYJKoZIhvcNAQELBQADggEBAGBnKJRvDkhj6zHd6mcY
1Yl9PMWLSn/pvtsrF9+wX3N3KjITOYFnQoQj8kVnNeyIv/iPsGEMNKSuIEyExtv4
NeF22d+mQrvHRAiGfzZ0JFrabA0UWTW98kndth/Jsw1HKj2ZL7tcu7XUIOGZX1NG
Fdtom/DzMNU+MeKNhJ7jitralj41E6Vf8PlwUHBHQRFXGU7Aj64GxJUTFy8bJZ91
8rGOmaFvE7FBcf6IKshPECBV1/MUReXgRPTqh5Uykw7+U0b6LJ3/iyK5S9kJRaTe
pLiaWN0bfVKfjllDiIGknibVb63dDcY3fe0Dkhvld1927jyNxF1WW6LZZm6zNTfl
MrY=
-----END CERTIFICATE-----" | base64 -w 0)
KEY=$(echo "-----BEGIN PRIVATE KEY-----
MIIEvAIBADANBgkqhkiG9w0BAQEFAASCBKYwggSiAgEAAoIBAQDClIi59XRdsyCT
5TATn+U5UIcbryVVyJ2UTGOAjTpEfdmeERPkjYMqReU0rxH1nzJV4+snr2XE5LSG
OI5GeyCCSadlAKorGZ+wv0zGNnttFUr5domElZoojtDiv2pILs6LTmMMkBY6dqVX
nn9U2Vjh91bNzDm8ocKc8FqWz/XNKKgFGBoeGYrJUrp6CzRzz0FRI29WgaRlGkvW
gsx1dzyS2Jvql0TnSDT1xOUNQDnVu6wqN8bXSoAs/abt4XYHL9L2dIgFWftZuHZn
MP5jwhN2/2IlMhQr3yhm+j87MSatk2OgII39tLol08Gk0qxcukpA3/8RZ+GWoBdJ
A35M4SPDAgMBAAECgf9nXoFESSGIpaCo8RiZVFhyGlWsX192Mx2DqxExOdW6jSEO
2AO5Z3RHANrDIj5oPip/dpV2f7eQ4e8FXwY2azLaBEbpYXEFdJdhTj97DXaEfMtX
u6FSzicXZMeTrhzIPBV97CayhdlJNb6gzZLXgJ+3a66xpc0GaOj6drWpaNEsK3/X
jwi6el/zHK8tnebdRwlZTnmR9/tv/2RJrE8tWTqkikAM/SOzMepM3eQdJQgoIC77
jmBRMk2pNUIIK4iUJ39zVHfdNANFW42iE3ZvVyH/qCzayyEDdnCM0iqCB4JaOfPt
1XFIVnmrhafzKCuWIosgPq1nAULzjpT3ZcZkMYECgYEA5ijU2wmj/8OjSIa7vREd
+RI/rl93TMcsCxhNukuj1KZ6+/CgBhi1GhEGKr4Nh2tQ71FZsMB955gclDUHhF2Z
ggaAazwGf/CojJdqWddlk+9DCZvu/6HGhZ4vb9ALaPslrCw6HVC0d1+RYHlPfYk1
/q3wfDgaoOjQK2U9qP8s9JsCgYEA2G0YL3sGSjeXqd/rjRFp8Hp6rH28IEOdohaO
TMxaK7RUaiiTH2SIiQUnx4W4Edkt3KSm0pAS9RXMhRgLtASHHPmwgC+XeoSZp/Jb
Kx2sXuRd5qHKo/YouVk+KwjltXLtJFYvJwuIPv68HmY3qamH0LrdDs6bD9HrJyPP
u1kOu/kCgYEAs99hSrWkF7S7nWi+vCnudMNQRWSShcx6nUknJdsLdJuPLeqGBX21
u2NmoGzbgePa4s1+/OXS//YfD6zJ7SaBW97c6fGFWuOntgh3szLlTdIFYDMfe2Th
E3fmtsmuwSLV9FK0MEjsYQatROpJt2BOdHVXppzqIqsQVXnP0I1sgtUCgYEAovsc
0Hy70t4kh1fKIfSwK9mAUi9poX5p0etcg/cMHIdzXnJpwKVLsKvCNh277Sz4vYT8
3+qBbDFbUAs4nPsf6LEbupycXTZIJFJ9V23EJb2h/RFv8aNpLZNIHs5Xdhoy/T8t
ySCFHLNmZRoi5tB1J7ngyMrLuNEjXdk5EWPSlbkCgYB41eaRrUs5mJoaHFA4dA8c
bmK6Ow5rnV/6gC47YoM1Ojl1svbGOK5SiN9lWAp+Wzhc3iLKm8kaXaNri34KAkpa
luAtq2Qohhm91QePQjbXmFvsAtwflOFK7B659StK/EFM9gYqaZPQws6IHkKwGu/Q
OvNCQzaM3wW/qZM8hLeP3w==
-----END PRIVATE KEY-----" | base64 -w 0)

DB=$BANK-$PRODUCT-$ENV
DB_IP="192.168.213.86"
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


BLB_BE_IMAGE="repo.gibl.com.np/gibl/blb-gibl:PROD.37"
BLB_FE_IMAGE="repo.gibl.com.np/gibl/blb-portal-gibl:PROD.28"


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
helm install my-kafka bitnami/kafka --version 28.2.5 -n kafka
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
```