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
* Use curl to download the Kafka binaries:
```
$ curl https://www-eu.apache.org/dist/kafka/2.3.0/kafka_2.12-2.3.0.tgz
```
* Extract the archive you downloaded using the tar command:
```
$ tar -xzvf kafka_2.12-2.3.0.tgz
```
