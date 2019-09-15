# Install-Apache-Kafka-on-Ubuntu
### Prerequisites
Ubuntu server and a non-root user with sudo privileges & OpenJDK 8 installed on your server
* Logged in as your non-root sudo user, create a user called kafka with the useradd command:
```
$ sudo useradd kafka -m
$ sudo passwd kafka
```
* Add the kafka user to the sudo group
```
$ sudo adduser kafka sudo
```
* Log into this account
```
$ su -l kafka
```
* Use wget to download the Kafka binaries:
```
$ wget https://www-eu.apache.org/dist/kafka/2.3.0/kafka_2.12-2.3.0.tgz
```
* Extract the archive you downloaded using the tar command:
```
$ tar -xzvf kafka_2.12-2.3.0.tgz
```
* Create the unit file for zookeeper:
```
$ vim /etc/systemd/system/zookeeper.service

[Unit]
Requires=network.target remote-fs.target
After=network.target remote-fs.target

[Service]
Type=simple
User=kafka
ExecStart=/home/kafka/kafka_2.12-2.3.0/bin/zookeeper-server-start.sh /home/kafka/kafka_2.12-2.3.0/config/zookeeper.properties
ExecStop=/home/kafka/kafka_2.12-2.3.0/bin/zookeeper-server-stop.sh
Restart=on-abnormal

[Install]
WantedBy=multi-user.target
```
* create the systemd service file for kafka:
```
$ vim /etc/systemd/system/kafka.service

[Unit]
Requires=zookeeper.service
After=zookeeper.service

[Service]
Type=simple
User=kafka
ExecStart=/bin/sh -c '/home/kafka/kafka_2.12-2.3.0/bin/kafka-server-start.sh /home/kafka/kafka_2.12-2.3.0/config/server.properties > /home/kafka/kafka_2.12-2.3.0/kafka.log 2>&1'
ExecStop=/home/kafka/kafka_2.12-2.3.0/bin/kafka-server-stop.sh
Restart=on-abnormal

[Install]
WantedBy=multi-user.target
```
* The new system units have been added, so let’s enable Apache Kafka to automatically run on boot, and then run the service.
```
$ sudo systemctl enable kafka
$ sudo systemctl start kafka
```
