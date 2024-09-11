#!/bin/bash

# Variable Declaration
K3S_VER="v1.30.4+k3s1"
HTTP_PORT=""
HTTPS_PORT=""
IP_ADDR=$(hostname -I | awk '{print $1}')
BANK="nmb"
PRODUCT="blb"
ENV="live"
NAMESPACE=$BANK-$PRODUCT-$ENV

DOMAIN="nimb.com.np"
FQDN="blbapp.$DOMAIN"
CERT=$(echo "-----BEGIN CERTIFICATE-----
MIIGyTCCBbGgAwIBAgIQBhkflNYhm9yW1JQgZ6qF7DANBgkqhkiG9w0BAQsFADBZ
MQswCQYDVQQGEwJVUzEVMBMGA1UEChMMRGlnaUNlcnQgSW5jMTMwMQYDVQQDEypE
aWdpQ2VydCBHbG9iYWwgRzIgVExTIFJTQSBTSEEyNTYgMjAyMCBDQTEwHhcNMjQw
ODA3MDAwMDAwWhcNMjUwOTA3MjM1OTU5WjBPMQswCQYDVQQGEwJOUDESMBAGA1UE
BxMJS2F0aG1hbmR1MRUwEwYDVQQKEwxOTUIgQkFOSyBMVEQxFTATBgNVBAMMDCou
bm1iLmNvbS5ucDCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKL3Ucb7
bT6MvarPN1xfuQSlCmr4GtF81eBmWqvknFqvRrrFJ2LgyEQImfDI7fzl9aSvpTq8
WOwkt7L48Bs8bQOMovT2bol5r8oNytz8VZnx/WVsPl1X9GVAj091Wnvx25Fo16Dg
hX8rc4T4g4vHCnp9amXrtMXz6sIfYeN2+P3GOoyCUd/i2f/GEnbH8D1SCTq3vtno
8AWhJNaomwWgaqgorAFWYSWW6AzwBkKnSdZW+VFGNc+nt0qanfSQccpbO4WbPmCV
dwd2XbPpfqqrvCi48T4BVbkCAUnw/yTRA/DY5jsN+v636CnZUyEcSl9a60jSaO2W
woB6VLt8gdKWKgUCAwEAAaOCA5UwggORMB8GA1UdIwQYMBaAFHSFgMBmx9833s+9
KTeqAx2+7c0XMB0GA1UdDgQWBBSxjLjSDSMPdgUZMIlnW5nzFAS67DAjBgNVHREE
HDAaggwqLm5tYi5jb20ubnCCCm5tYi5jb20ubnAwPgYDVR0gBDcwNTAzBgZngQwB
AgIwKTAnBggrBgEFBQcCARYbaHR0cDovL3d3dy5kaWdpY2VydC5jb20vQ1BTMA4G
A1UdDwEB/wQEAwIFoDAdBgNVHSUEFjAUBggrBgEFBQcDAQYIKwYBBQUHAwIwgZ8G
A1UdHwSBlzCBlDBIoEagRIZCaHR0cDovL2NybDMuZGlnaWNlcnQuY29tL0RpZ2lD
ZXJ0R2xvYmFsRzJUTFNSU0FTSEEyNTYyMDIwQ0ExLTEuY3JsMEigRqBEhkJodHRw
Oi8vY3JsNC5kaWdpY2VydC5jb20vRGlnaUNlcnRHbG9iYWxHMlRMU1JTQVNIQTI1
NjIwMjBDQTEtMS5jcmwwgYcGCCsGAQUFBwEBBHsweTAkBggrBgEFBQcwAYYYaHR0
cDovL29jc3AuZGlnaWNlcnQuY29tMFEGCCsGAQUFBzAChkVodHRwOi8vY2FjZXJ0
cy5kaWdpY2VydC5jb20vRGlnaUNlcnRHbG9iYWxHMlRMU1JTQVNIQTI1NjIwMjBD
QTEtMS5jcnQwDAYDVR0TAQH/BAIwADCCAX8GCisGAQQB1nkCBAIEggFvBIIBawFp
AHYAEvFONL1TckyEBhnDjz96E/jntWKHiJxtMAWE6+WGJjoAAAGRK6uFpQAABAMA
RzBFAiEA06BzR0DbGAYiB8WxLwkO13NJ2YVscF6G5KIv4oEM+aoCIHi8atyZ84BR
xML9FzKUN6TuP0fFdxSOX32aOZpXYlYCAHcAfVkeEuF4KnscYWd8Xv340IdcFKBO
lZ65Ay/ZDowuebgAAAGRK6uFpAAABAMASDBGAiEAr+S5U+cV/CP7SurU/OIpRG2j
rzz8NJ35U1fIiIignbwCIQDrTid1fvKzmcux7o+4YBF3Bd0z8ObyuI8ip+oWF/Re
qwB2AObSMWNAd4zBEEEG13G5zsHSQPaWhIb7uocyHf0eN45QAAABkSurhbEAAAQD
AEcwRQIhANJDNVf92s4afBmxVUqesV0EOoVEtqyT2hMnSTuXjJWbAiAcI1sCNYq/
raI0dbSkYOjMFI1zYdCBTt6viCBWWLSehDANBgkqhkiG9w0BAQsFAAOCAQEAYdEd
vavRecfuPEXa/hVIj0cw4MOoABxum3UZds6UzMd05pt75OWoIzlIJSgRPE/Drpe3
QAHXpWA4nBTTSR7lXBzXdBeHEQbNNe6gjS7U7yQN/yjGLZgLYzbtOTaITz8ZvCWn
CB7UMD7W7p8lmQsYff6BkR6J8PrH1vcgNUl4qN6VVsV2T3zIrxF2lbpq5RziHfmr
972OurKzoGT24HTNRwp8iq2+suBV4LTU+pjXdiL2I7hWEyi9E9HbBDmtgyAAbdbQ
DYVfJA3+UyrJkdG1LOuRQP1JyGV/yoGN93NgtoRgQsKwX77aCRrsyWoJJboGncWx
hnAAhMC73YqGUcN5CA==
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
MIIEvAIBADANBgkqhkiG9w0BAQEFAASCBKYwggSiAgEAAoIBAQCi91HG+20+jL2q
zzdcX7kEpQpq+BrRfNXgZlqr5Jxar0a6xSdi4MhECJnwyO385fWkr6U6vFjsJLey
+PAbPG0DjKL09m6Jea/KDcrc/FWZ8f1lbD5dV/RlQI9PdVp78duRaNeg4IV/K3OE
+IOLxwp6fWpl67TF8+rCH2Hjdvj9xjqMglHf4tn/xhJ2x/A9Ugk6t77Z6PAFoSTW
qJsFoGqoKKwBVmEllugM8AZCp0nWVvlRRjXPp7dKmp30kHHKWzuFmz5glXcHdl2z
6X6qq7wouPE+AVW5AgFJ8P8k0QPw2OY7Dfr+t+gp2VMhHEpfWutI0mjtlsKAelS7
fIHSlioFAgMBAAECggEAFI3xKJHJw5voyER+jQ4dvfI7ECbe6xE9wKHoScn51o5I
84GuaBBF8h7Lm80cB0vR4cWtp2zeIlq3OMGhNy416b6xRwhWBMzuWSPINHs3KMWW
2lX+v7M6RhMQgEsi8IMe2IZKvCXVcKbAWUMnBFhEgBKVeulx0Y3kTDu9Ev2MhiVo
6sEPGbc+xm41OUn9MKY770xDDHNv5SBvsmaBK+ttsnX1p+5p5YFVRW+0E4/BKO1Z
zDrMtHZPFypJoWrXuV2UqYZX2m5B2TfFMCkEZjGQotG24T+uIZKmebkAGFOjhnSI
v15Q+poKXuVZIJxZyEPouDT87k7i51Gfp+goTHcLwQKBgQDTgv61T3xau7epl7/D
XT3gyTa8tbx99n4THk6yzPJTg/99J7pjH1VC3LU8/RvLbvzk3JfAZTBdTLlm3E5R
BGsM2DL3AM4+75+bxIS2A9et4tq6RCeexdTrqCyFe32nNFdQ3jn2U4uYHtxc8iMO
TW0yNzriXjIPnZI/FE0cdD+OaQKBgQDFPlme1PUxWtHnjhmgrynhiTiW1pvtfTHo
0EEFMkgWF3lEt+lcfHZ02dq7S76CqBBZKShUHWGAGfVCYIc33twc3cBweoh2+p1j
WqsRpFoRus0BLothD8S2moljwV3lj1mIW64pbs++VHVPsFJK+oZQUuLfOABBd4wi
fUHZDdYDPQKBgEx0y7Gqm98IgClzy0PJjraUxY6NeydlVMmAaR5E60u5KT+KftuZ
1e4nbfQv4j41ToFEJC7N5R+0vkgqVrz+hdvMww96YRNq9x0NepN47BvVJw+x10iT
ZpYQ4pcVvqQUTYPT8MvUA1/nt+x5MqbW4iQGxuhQ+HOgl97pbb5dXyQRAoGAZvmp
QlH9/JlkstYuLQSmAdhpEd7TI0bUUq6+816P4fC5YBYAIEfedBz0pAnkWUQy3Mmv
A1MffwiOUewhTBruoadn+5ENQ8iNeLxySVCbsVvsAWzyWWcpFQhTKCBgzCNt49Gx
eHIgxUZExKjSHdDzZGYRieYrxAIMyEjKou4lSCUCgYARGCE8zijKYeQhdgP9C+x+
rlPHDMJ9NdqmUipqoSIQKv6Cv+5RnpZHPvs6ZrZcKURYgoTcZdVBtAj8asu1Ia3t
5rEHWbWBZtl1C4JXJARXXGlxHDqjAqAMmiSurgPUGkqtfUBT01MdP2HlNhwZoLIY
jOtVhUaYbuJMfP+suxnTJA==
-----END PRIVATE KEY-----" | base64 -w 0)

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
