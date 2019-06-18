## Step 0. Preparation
[ Setting SSHD ]

[ Get root user ]
```
sudo -i
passwd
```
*typing password*


[ yum update ]
```
sudo yum update
sudo yum install -y wget
```

[ Check Hostname]
```
hostname
```

[ Setting /etc/hosts ]
```
sudo vi /etc/hosts
```

***Add*** -> [ip] [FQDN] [shortcut]
>172.31.32.127 ip-172-31-32-127.ap-northeast-1.compute.internal host1
172.31.46.117 ip-172-31-46-117.ap-northeast-1.compute.internal host2
172.31.47.240 ip-172-31-47-240.ap-northeast-1.compute.internal host3
172.31.37.27  ip-172-31-37-27.ap-northeast-1.compute.internal host4
172.31.37.135 ip-172-31-37-135.ap-northeast-1.compute.internal host5


[ Disable Firewall ]
```
sudo systemctl disable firewalld
sudo systemctl status firewalld
```

[ Disable SELinux ]
```
sudo vi /etc/selinux/config
```
***Change***  
>SELINUX=disabled
```
sudo setenforce 0
```

[ Disable Transparent hugepage ]
```
sudo vi /etc/rc.local  
```
***Add***  
>echo never > /sys/kernel/mm/transparent_hugepage/defrag

```
sudo sh -c "echo 'never' > /sys/kernel/mm/transparent_hugepage/defrag" 
```

[ Setting swappiness ]
```
sudo vi /etc/sysctl.conf  
```
***Add***   
>vm.swappiness=1

```
sudo sysctl -w vm.swappiness=1
```


## Step 1. Configure a Repository

[ setting yum, rpm repo ]
```
sudo wget https://archive.cloudera.com/cm5/redhat/7/x86_64/cm/cloudera-manager.repo -P /etc/yum.repos.d/

sudo rpm --import https://archive.cloudera.com/cm5/redhat/7/x86_64/cm/RPM-GPG-KEY-cloudera
```


## Step 2. Install JDK
[ Install JDK ]  
**( In All hosts )**
```
sudo yum install oracle-j2sdk1.7
```

## Step 3. Instamll Cloudera Manager Server

[ Install CM Server ]  
**( In Master server )**
```
sudo yum install cloudera-manager-daemons cloudera-manager-server
```

>[ if Oracle DB ]  
>```
>sudo vi /etc/default/cloudera-scm-server  
>```
>
>***change***  
>>export CM_JAVA_OPTS = -Xmx4G

[ Install CM agent ]  
**( In All hosts )**
```
sudo yum install cloudera-manager-daemons cloudera-manager-agent
```

[ Configure Setting ]
```
sudo vi /etc/cloudera-scm-agent/config.ini  
```
>server_host = host1 (host2, host3, ...)  
server_port = 7182

[ Agent start ]
```
sudo service cloudera-scm-agent start
```

## Step 4. Install Databases
[ Install MySQL]

[ CM Server ]
```
sudo wget http://repo.mysql.com/mysql-community-release-el7-5.noarch.rpm

sudo rpm -ivh mysql-community-release-el7-5.noarch.rpm

sudo yum update

sudo yum install mysql-server

sudo systemctl start mysqld
```

[ Install MySQL secure ]
```
sudo /usr/bin/mysql_secure_installation
```

[ Install JDBC Driver ]  
**( In All hosts )**
```
sudo wget https://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-5.1.47.tar.gz

tar zxvf mysql-connector-java-5.1.47.tar.gz

sudo mkdir -p /usr/share/java/

cd mysql-connector-java-5.1.47

sudo cp mysql-connector-java-5.1.47-bin.jar /usr/share/java/mysql-connector-java.jar
```

[ Creating Database for Cloudera Software ]
```
sudo mysql -u root -p
```

```sql
mysql>

CREATE DATABASE scm DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
GRANT ALL ON scm.* TO 'scm'@'%' IDENTIFIED BY 'training';

CREATE DATABASE amon DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
GRANT ALL ON amon.* TO 'amon'@'%' IDENTIFIED BY 'training';

CREATE DATABASE rman DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
GRANT ALL ON  rman.* TO 'rman'@'%' IDENTIFIED BY 'training';

CREATE DATABASE hue DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
GRANT ALL ON hue.* TO 'hue'@'%' IDENTIFIED BY 'training';

CREATE DATABASE metastore DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
GRANT ALL ON metastore.* TO 'hive'@'%' IDENTIFIED BY 'training';

CREATE DATABASE sentry DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
GRANT ALL ON sentry.* TO 'sentry'@'%' IDENTIFIED BY 'training';

CREATE DATABASE nav DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
GRANT ALL ON nav.* TO 'nav'@'%' IDENTIFIED BY 'training';

CREATE DATABASE navms DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
GRANT ALL ON navms.* TO 'navms'@'%' IDENTIFIED BY 'training';

CREATE DATABASE oozie DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
GRANT ALL ON oozie.* TO 'oozie'@'%' IDENTIFIED BY 'training';
```

[ For every query ]
```
Query OK, 1 row affected (0.00 sec)
Query OK, 0 rows affected (0.00 sec)
```

[ Check databases, grants ]
```sql
SHOW DATABASES;
SHOW GRANTS FOR '<user>'@'%';
```

## Step 5. Set up the Cloudera Manager Database

[ DB Configuration ]
```
sudo /usr/share/cmf/schema/scm_prepare_database.sh mysql scm scm training
sudo /usr/share/cmf/schema/scm_prepare_database.sh mysql amon amon training
sudo /usr/share/cmf/schema/scm_prepare_database.sh mysql rman rman training
sudo /usr/share/cmf/schema/scm_prepare_database.sh mysql hue hue training
sudo /usr/share/cmf/schema/scm_prepare_database.sh mysql metastore hive training
sudo /usr/share/cmf/schema/scm_prepare_database.sh mysql sentry sentry training
sudo /usr/share/cmf/schema/scm_prepare_database.sh mysql nav nav training
sudo /usr/share/cmf/schema/scm_prepare_database.sh mysql navms navms training
sudo /usr/share/cmf/schema/scm_prepare_database.sh mysql oozie oozie training
```


[ Setting permission /var/lib ]
```
sudo chmod 777 -R /var/lib
```
## Step 6. Install CDH and Other Software
```
sudo systemctl start cloudera-scm-server

sudo tail -f /var/log/cloudera-scm-server/cloudera-scm-server.log
```


# Sqoop, Spark, Kafka
## Sqoop Version = 1

## Kafka Install
[ Kafka 3.1.0 Parcel ]  
호스트 -> Parcel -> 구성 -> 원격 Parcel 레포지터리  
>http://archive.cloudera.com/kafka/parcels/3.1.0/

[ Setting Kafka java heap size ]  
>Java Heap Size of Broker = 1 GiB