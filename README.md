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
The new system units have been added, so let’s enable Apache Kafka to automatically run on boot, and then run the service.
```
$ sudo systemctl enable kafka
$ sudo systemctl start kafka
```
## Create a topic
Let's create a topic named "First Topic" with a single partition and only one replica:
```
$ bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic FirstTopic
```
We can now see that topic if we run the list topic command:
```
$ bin/kafka-topics.sh --list --bootstrap-server localhost:9092
```
## Send some messages
Kafka comes with a command line client that will take input from a file or from standard input and send it out as messages to the Kafka cluster. By default, each line will be sent as a separate message.

Run the producer and then type a few messages into the console to send to the server.
```
$ bin/kafka-console-producer.sh --broker-list localhost:9092 --topic FirstTopic
> This is a message
> This is another message
```
## Start a consumer
Kafka also has a command line consumer that will dump out messages to standard output.
```
$ bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --from-beginning
This is a message
This is another message
```
If you have each of the above commands running in a different terminal then you should now be able to type messages into the producer terminal and see them appear in the consumer terminal.
