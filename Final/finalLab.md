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




## Step 1. Configure a Repository

```
$ sudo wget https://archive.cloudera.com/cm5/redhat/7/x86_64/cm/cloudera-manager.repo -P /etc/yum.repos.d/
```


## Step 2. Install JDK

```
$ sudo yum install oracle-j2sdk1.7
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

## Step 3. Install Cloudera Manager Server

[ Master ]
```
$ sudo yum install cloudera-manager-daemons cloudera-manager-server
```

[ All Hosts ]
```
$ sudo yum install cloudera-manager-daemons cloudera-manager-agent
```

#### Configure Setting
> /etc/cloudera-scm-agent/config.ini  
- server_host
- server_port

#### Agent start
```
$ sudo service cloudera-scm-agent start
Starting cloudera-scm-agent (via systemctl):               [  OK  ]
```

## Step 4. Install Databases
#### Install MySQL
```
$ wget http://repo.mysql.com/mysql-community-release-el7-5.noarch.rpm
$ sudo rpm -ivh mysql-community-release-el7-5.noarch.rpm
$ sudo yum update
$ sudo yum install mysql-server
$ sudo systemctl start mysqld
```

```
$ sudo systemctl enable mysqld
$ sudo systemctl start mysqld
```

#### Install JDBC Driver

> http://www.mysql.com/downloads/connector/j/5.1.html

[ Local ]
```
$ pscp -i C:\Users\SKCC\Downloads\SKCC.ppk C:\Users\SKCC\Downloads\mysql-connector-java-5.1.47.tar.gz centos@15.164.82.192:mysql-connector-java-5.1.47.tar.gz
```

[ Server ]
```
$ tar zxvf mysql-connector-java-5.1.47.tar.gz
$ sudo mkdir -p /usr/share/java/
$ cd mysql-connector-java-5.1.47
$ sudo cp mysql-connector-java-5.1.47-bin.jar /usr/share/java/mysql-connector-java.jar
```

#### Creating Database for Cloudera Software

```
$ mysql -u root -p
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

