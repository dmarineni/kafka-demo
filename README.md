# kafka-demo
Demo project for Kafka producer and consumers

# Installation notes
## user creation
sudo useradd kafka -m

sudo passwd kafka

sudo usermod -aG wheel kafka

su -l kafka

## download and installation
curl "https://mirrors.estointernet.in/apache/kafka/2.7.0/kafka_2.13-2.7.0.tgz" -o ~/Downloads/kafka.tgz

mkdir ~/kafka 

cd ~/kafka

tar -xvzf ~/Downloads/kafka.tgz --strip 1

## configuration
vi ~/kafka/config/server.properties

delete.topic.enable = true

sudo vi /etc/systemd/system/zookeeper.service

[Unit]
Requires=network.target remote-fs.target
After=network.target remote-fs.target

[Service]
Type=simple
User=kafka
ExecStart=/home/kafka/kafka/bin/zookeeper-server-start.sh /home/kafka/kafka/config/zookeeper.properties
ExecStop=/home/kafka/kafka/bin/zookeeper-server-stop.sh
Restart=on-abnormal

[Install]
WantedBy=multi-user.target

sudo vi /etc/systemd/system/kafka.service

[Unit]
Requires=zookeeper.service
After=zookeeper.service

[Service]
Type=simple
User=kafka
ExecStart=/bin/sh -c '/home/kafka/kafka/bin/kafka-server-start.sh /home/kafka/kafka/config/server.properties > /home/kafka/kafka/kafka.log 2>&1'
ExecStop=/home/kafka/kafka/bin/kafka-server-stop.sh
Restart=on-abnormal

[Install]
WantedBy=multi-user.target

## Start
sudo systemctl start kafka

journalctl -u kafka

sudo systemctl enable kafka

# Installing KafkaT
sudo yum install ruby ruby-devel make gcc patch

sudo gem install kafkat

vi ~/.kafkatcfg

{
  "kafka_path": "~/kafka",
  "log_path": "/tmp/kafka-logs",
  "zk_path": "localhost:2181"
}

kafkat partitions

# Producer Consumer 
## Topic creation
./bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic HelloTopic
## Producer creation and publish 
echo "Hello World" | ./bin/kafka-console-producer.sh --broker-list localhost:9092 --topic HelloTopic > /dev/null
## Consumer messages from topic
./bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic HelloTopic --from-beginning



