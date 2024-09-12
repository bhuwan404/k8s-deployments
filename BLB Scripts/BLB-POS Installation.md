#!/bin/bash

#Variable Declaration


echo
echo "========== WildFly Installation =========="

###### Checking OS ######
yum --help > /dev/null
if [ $? -eq 0 ];
then
	echo
	echo "Updating system..."
	yum update -y > /dev/null
	echo "--------------------------------"
	
	echo
	echo "Installing openJDK 17..."
	yum install java-17-openjdk-devel -y > /dev/null
	echo "--------------------------------"
	
	echo
	echo "Installing curl and wget..."
	yum install -y curl wget > /dev/null
	echo "--------------------------------"
	
	echo
	echo "Enabling and Starting firewall"
	systemctl enable ufw
	systemctl start firewalld
	echo "--------------------------------"
	
	echo
	echo "Allowing ports in firewall..."
	firewall-cmd --permanent --add-port=9990/tcp
	firewall-cmd --permanent --add-port=443/tcp
	firewall-cmd --reload
	echo "--------------------------------"
	
else
	echo
	echo "Updating system..."
	apt-get update -y > /dev/null
	echo "--------------------------------"
	
	echo
	echo "Upgrading system..."
	apt-get upgrade -y > /dev/null
	echo "--------------------------------"
	
	echo
	echo "Installing openJDK 17..."
	apt-get install openjdk-17-jdk -y > /dev/null
	echo "--------------------------------"
	
	echo
	echo "Installing curl and wget..."
	apt-get install -y curl wget > /dev/null
	echo "--------------------------------"
	
	echo
	echo "Enabling and Starting firewall"
	systemctl enable ufw
	systemctl start ufw
	echo "--------------------------------"

	echo
	echo "Allowing ports in firewall..."
	#ufw app list
	#ufw status numbered
	ufw allow 9990/tcp
	ufw allow 443/tcp
	ufw reload
	echo "--------------------------------"
fi

echo
echo "Changing Directory to /opt"
cd /opt
echo "--------------------------------"

echo
echo "Downloading WildFly and extracting Installation archive..."
wget https://github.com/wildfly/wildfly/releases/download/25.0.1.Final/wildfly-preview-25.0.1.Final.tar.gz
tar xf wildfly-preview-25.0.1.Final.tar.gz
sudo mv wildfly-preview-25.0.1.Final wildfly
echo "--------------------------------"

echo
echo "Copying the WildFly systemd service, configuration file and start scripts templates..."
sudo mkdir /etc/wildfly
sudo cp /opt/wildfly/docs/contrib/scripts/systemd/wildfly.conf /etc/wildfly/
sudo cp /opt/wildfly/docs/contrib/scripts/systemd/wildfly.service /etc/systemd/system/
sudo cp /opt/wildfly/docs/contrib/scripts/systemd/launch.sh /opt/wildfly/bin/
sudo chmod +x /opt/wildfly/bin/launch.sh
echo "--------------------------------"

echo
echo "Using standalone-full.xml rather than standalone.xml file..."
sed -i 's/standalone.xml/standalone-full.xml/' /etc/wildfly/wildfly.conf
echo "--------------------------------"

echo
echo "Managing user and permission for Wildfly startup script..."
sudo useradd wildfly
sudo chown -R wildfly:wildfly /opt/wildfly
sudo systemctl daemon-reload
sudo systemctl start wildfly
sudo systemctl enable wildfly
#sudo systemctl status wildfly
echo "--------------------------------"

echo
echo "Adding WildFly user..."
#echo "/opt/wildfly/bin/add-user.sh"




===
-----BEGIN CERTIFICATE-----
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
-----END CERTIFICATE-----
====
-----BEGIN PRIVATE KEY-----
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
-----END PRIVATE KEY-----

==

<pooled-connection-factory name="activemq-ra" entries="java:/JmsXA java:jboss/DefaultJMSConnectionFactory" connectors="artemis" transaction="xa" user="guest" password="guest"/>
            <external-jms-queue name="blbQueue" entries="queue/blbQueue"/>
            <external-jms-queue name="blbQueue_reversal" entries="queue/blbQueue_reversal"/>
            <external-jms-queue name="blbDisbursementQueue" entries="queue/blbDisbursementQueue"/>
            <external-jms-queue name="blbPreEnrolmentCustomersQueue" entries="queue/blbPreEnrolmentCustomersQueue"/>
            <external-jms-queue name="blb_preEnrolCustomerWithMiscDetailQueue" entries="queue/blb_preEnrolCustomerWithMiscDetailQueue"/>
            <external-jms-queue name="blb_postEnrolCustomerWithMiscDetailQueue" entries="queue/blb_postEnrolCustomerWithMiscDetailQueue"/>
            <external-jms-queue name="blb_offlinePaymentQueue" entries="queue/blb_offlinePaymentQueue"/>
            <external-jms-queue name="blbCardInventoryQueue" entries="queue/blbCardInventoryQueue"/>

===

wget https://jdbc.postgresql.org/download/postgresql-42.7.1.jar

=
sudo cp /opt/wildfly/standalone/configuration/standalone-full.xml /opt/wildfly/standalone/configuration/standalone-full.xml.backupb4ssl
openssl pkcs12 -export -in bundle.crt -inkey private.key -out serverjboss.p12 -name jboss
keytool -importkeystore -destkeystore serverjboss.jks -srckeystore serverjboss.p12 -srcstoretype PKCS12 -alias jboss
cp serverjboss.jks /opt/wildfly/standalone/configuration/keystore.jks

===

cd /opt/wildfly/standalone/configuration/
sudo /opt/wildfly/bin/jboss-cli.sh
/subsystem=elytron/key-store=httpsKS:add(path=keystore.jks,relative-to=jboss.server.config.dir,credential-reference={clear-text=<passoword-used-while-creating-jks>},type=JKS)
/subsystem=elytron/key-manager=httpsKM:add(key-store=httpsKS,credential-reference={clear-text=<passoword-used-while-creating-jks>})
/subsystem=elytron/server-ssl-context=httpsSSC:add(key-manager=httpsKM,protocols=["TLSv1.2","TLSv1.3"])
/subsystem=undertow/server=default-server/https-listener=https:read-attribute(name=security-realm)
batch
/subsystem=undertow/server=default-server/https-listener=https:undefine-attribute(name=security-realm)
/subsystem=undertow/server=default-server/https-listener=https:write-attribute(name=ssl-context,value=httpsSSC)
run-batch
reload

===






