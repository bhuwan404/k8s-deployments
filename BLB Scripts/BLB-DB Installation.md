INSTALLATION SCRIPT FOR POSTGRES DB
```
#!/bin/bash

#Variable Declaration
VER="15"

DATA_DIR_UBUNTU="/var/lib/postgresql/$VER/main"
DATA_DIR_RHEL="/var/lib/pgsql/$VER/data"
DATA_DIR="/usr/lib/pgsql"
DB_PORT="5432"

CONFIG_DIR_UBUNTU="/etc/postgresql/$VER/main"

POSTGRES_SYSTEMD_FILE="/usr/lib/systemd/system/postgresql*.service"

echo
echo "========== POSTGRES INSTALLATION =========="

###### Checking OS ######
yum --help > /dev/null
if [ $? -eq 0 ];
then
	echo
	echo "Updating system..."
	dnf update -y > /dev/null
	echo "--------------------------------"
	
	echo
	echo "Setting up repository..."
	dnf install https://download.postgresql.org/pub/repos/yum/reporpms/EL-9-x86_64/pgdg-redhat-repo-latest.noarch.rpm -y
	echo "--------------------------------"
	
	echo
	echo "Disabling built-in postgresql module..."
	dnf -qy module disable postgresql > /dev/null
	echo "--------------------------------"
	
	echo
	echo "Installing postgresql $VER..."
	dnf install postgresql$VER-server -y > /dev/null
	echo "--------------------------------"
	
	read -p "Do you want to change the data directory? (y/n) " change_dir
	change_dir=$(tr '[:upper:]' '[:lower:]' <<< "$change_dir")
    if [ "$change_dir" == "y" ] || [ "$change_dir" == "yes" ]; then
        read -p "Enter new data directory (default /usr/local/pgsql): " new_dir
        if [ -n "$new_dir" ]; then
            DATA_DIR="$new_dir"
        fi
		echo
		echo "Creating new data directory..."
		mkdir -p $DATA_DIR
		chown postgres:postgres $DATA_DIR
		chmod 700 $DATA_DIR
		
		echo
		echo "Initializing new data directory..."
		sudo -u postgres initdb -D $DATA_DIR > /dev/null
		
		echo
		echo "Updating 'PGDATA' environment variable in systemd file..."
		sed -i "s|$DATA_DIR_RHEL|$DATA_DIR|" $POSTGRES_SYSTEMD_FILE
	
	else		
		/usr/pgsql-16/bin/postgresql-16-setup initdb
	fi
	
	echo "--------------------------------"
	echo
	echo "Updating selinux permission for new direcoty..."
	sudo semanage fcontext -a -t postgresql_db_t "$DATA_DIR(/.*)?"
	sudo restorecon -Rv $DATA_DIR
	echo "--------------------------------"
	
	echo
	echo "Updating firewall rules..."
	firewall-cmd --permanent --add-port=$DB_PORT/tcp
	firewall-cmd --reload
	echo "--------------------------------"
	
	echo
	echo "Starting PostgreSQL..."
	systemctl start postgresql-$VER.service
	systemctl enable postgresql-$VER.service
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
	echo "Setting up repository..."
	apt-get install -y postgresql-common > /dev/null
	/usr/share/postgresql-common/pgdg/apt.postgresql.org.sh
	apt-get update -y > /dev/null
	echo "--------------------------------"
	
	echo
	echo "Installing postgresql $VER..."
	apt-get install -y postgresql-$VER > /dev/null
	echo "--------------------------------"
	
	echo
	read -p "Do you want to change the data directory? (y/n) " change_dir
	change_dir=$(tr [A-Z] [a-z] <<< "$change_dir")
	if [ $change_dir == "y" ] || [ $change_dir == "yes" ]; then
		read -p "Enter new data directory (default /data/pgsql): " new_dir
		if [ $new_dir != "" ]; then
			DATA_DIR=$new_dir
		fi
		echo
		echo "Bakcing up data directory..."
		cp -r $DATA_DIR_UBUNTU $DATA_DIR_UBUNTU.bak
		echo
		echo "Creating new data directory..."
		mkdir -p $DATA_DIR
		chmod 700 $DATA_DIR
		chown postgres:postgres $DATA_DIR
		echo
		echo "Moving data..."
		sudo -i -u postgres mv $DATA_DIR_UBUNTU/* $DATA_DIR
		echo
		echo "Updating Configuration..."
		sudo -i -u postgres sed -i "s|$DATA_DIR_UBUNTU|$DATA_DIR|" $CONFIG_DIR_UBUNTU/postgresql.conf
		echo "--------------------------------"
	fi
	echo
	echo "Starting PostgreSQL..."
	systemctl start postgresql.service
	systemctl enable postgresql.service
	echo "--------------------------------"
fi
```

#### Create database and user
```
CREATE USER blbadmin PASSWORD 'blb@123#$';
CREATE DATABASE swift_blb OWNER blbadmin;
```

#### Export PostgreSQL database
```
pg_dump -h <db-ip> -U <username> -d <db name> -W -v -F t  > blb_bak.tar
```

#### Restore PostgreSQL database
```
pg_restore -h <db-ip> -U <username> -d <db name> -v -F t blb_bak.tar
```