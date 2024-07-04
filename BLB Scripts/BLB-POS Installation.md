WildFly installation Script for BLB POS
```
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

```