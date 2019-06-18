sudo yum update
sudo yum install -y wget

sudo visudo
add -> ec2-user ALL=(ALL)

sudo systemctl disable firewalld
sudo systemctl status firewalld

cat /proc/sys/vm/swappiness
sudo sysctl -w vm.swappiness=1

sudo vi /etc/sysctl.conf
  add
vm.swappiness=1

sudo vi /etc/selinux/config
  change
SELINUX=disabled

sudo vi /etc/rcd/rc/local
  add
echo "never" > /sys/kernel/mm/transparent_hugepage/enabled
echo "never" > /sys/kernel/mm/transparent_hugepage/defrag



## Preparation

sudo -i
passwd

$ hostname

sudo vi /etc/hosts

172.31.32.127 ip-172-31-32-127.ap-northeast-1.compute.internal host1
172.31.46.117 ip-172-31-46-117.ap-northeast-1.compute.internal host2
172.31.47.240 ip-172-31-47-240.ap-northeast-1.compute.internal host3
172.31.37.27  ip-172-31-37-27.ap-northeast-1.compute.internal host4
172.31.37.135 ip-172-31-37-135.ap-northeast-1.compute.internal host5


sudo yum update
sudo yum install -y wget

#### DTH

cat /sys/kernel/mm/transparent_hugepage/defrag

[ /etc/rc.local ] - add
echo never > /sys/kernel/mm/transparent_hugepage/defrag

sudo sh -c "echo 'never' > /sys/kernel/mm/transparent_hugepage/defrag" 


#### Swappiness
sudo sysctl -w vm.swappiness=1
cat /proc/sys/vm/swappiness

> 1


## Step 1. Configure a Repository

```
sudo wget https://archive.cloudera.com/cm5/redhat/7/x86_64/cm/cloudera-manager.repo -P /etc/yum.repos.d/

sudo rpm --import https://archive.cloudera.com/cm5/redhat/7/x86_64/cm/RPM-GPG-KEY-cloudera
```


## Step 2. Install JDK

```
sudo yum install oracle-j2sdk1.7
```

#### * Manually Install
```
$ getconf LONG_BIT 
```
64 (64 bit 또는 32 bit 확인)

Oracle 에서 JDK 8 다운로드 후 서버에 전송  

[ Local ]
```
$ pscp -i C:\Users\SKCC\Downloads\SKCC.ppk C:\Users\SKCC\Downloads\jdk-8u211-linux-x64.tar.gz centos@15.164.82.192:jdk.tar.gz
```

[ Server ]
```
$ sudo tar zxvf jdk.tar.gz -C /usr/java/
```

## Step 3. Instamll Cloudera Manager Server

[ Master ]
```
sudo yum install cloudera-manager-daemons cloudera-manager-server
```

if Oracle DB :  
/etc/default/cloudera-scm-server  
  change  
export CM_JAVA_OPTS = -Xmx4G

[ All Hosts ]
```
sudo yum install cloudera-manager-daemons cloudera-manager-agent
```

#### Configure Setting
> sudo vi /etc/cloudera-scm-agent/config.ini  
- server_host
- server_port

#### Agent start
```
$ sudo service cloudera-scm-agent start
Starting cloudera-scm-agent (via systemctl):               [  OK  ]
```

## Step 4. Install Databases
#### Install MySQL

[ CM Server ]
```
sudo wget http://repo.mysql.com/mysql-community-release-el7-5.noarch.rpm

sudo rpm -ivh mysql-community-release-el7-5.noarch.rpm

sudo yum update

sudo yum install mysql-server

sudo systemctl start mysqld
```


```
sudo systemctl stop mysqld

 mkdir ~/innoDBbackup
 sudo mv /var/lib/mysql/ib_logfile0 ~/innoDBbackup/ib_logfile0
sudo mv /var/lib/mysql/ib_logfile1 ~/innoDBbackup/ib_logfile1

sudo systemctl enable mysqld
sudo systemctl start mysqld

sudo /usr/bin/mysql_secure_installation
```

#### Install JDBC Driver

sudo wget https://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-5.1.47.tar.gz

tar zxvf mysql-connector-java-5.1.47.tar.gz

sudo mkdir -p /usr/share/java/

cd mysql-connector-java-5.1.47

sudo cp mysql-connector-java-5.1.47-bin.jar /usr/share/java/mysql-connector-java.jar

#### Creating Database for Cloudera Software

```
sudo mysql -u root -p
```

```sql
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

For every query
```
Query OK, 1 row affected (0.00 sec)
Query OK, 0 rows affected (0.00 sec)
```

Check databases, grants
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


### 권한 설정
sudo chmod 777 -R /var/lib

## Step 6. Install CDH and Other Software
```
sudo systemctl start cloudera-scm-server

sudo tail -f /var/log/cloudera-scm-server/cloudera-scm-server.log
```

## Kafka Parcel
호스트 -> Parcel -> 구성 -> 원격 Parcel 레포지터리  
http://archive.cloudera.com/kafka/parcels/3.1.0/