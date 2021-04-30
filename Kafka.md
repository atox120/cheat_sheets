# Kafka

This document outlines some useful commands and functions for using Apache Kafka[https://kafka.apache.org/]. 

Sources: MIDSw205 course content. 

## Kafka and docker-compose

### Example docker-compose.yml file


```yml
---
version: '2'
services:
  zookeeper:
    image: confluentinc/cp-zookeeper:latest
    environment:
      ZOOKEEPER_CLIENT_PORT: 32181
      ZOOKEEPER_TICK_TIME: 2000
    expose:
      - "2181"
      - "2888"
      - "32181"
      - "3888"

  kafka:
    image: confluentinc/cp-kafka:latest
    depends_on:
      - zookeeper
    environment:
      KAFKA_BROKER_ID: 1
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:32181
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://kafka:29092
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
    volumes:
      - ~/base:/base #<--update if using e.g. ~/mids:/mids
    expose:
      - "9092"
      - "29092"

  base:
    image: atox120/base:latest
    stdin_open: true
    tty: true
    volumes:
      - ~/base:/base      
```

### Spin up containers:

```bash
docker-compose up -d
```


### Check zookeeper

```bash
docker-compose logs zookeeper | grep -i binding
```

### Start logs

Start kafka logs  - press ctrl+c to detatch.

```bash
docker-compose logs -f kafka
```


### Create a topic

Creates a topic called 'topic_name'. Requires Zookeeper service on port 32181. 

```bash
docker-compose exec kafka kafka-topics --create --topic topic_name --partitions 1 --replication-factor 1 --if-not-exists --zookeeper zookeeper:32181
```

### Check the topic:

Checks that the topic was created, shows some config. 

```bash
docker-compose exec kafka kafka-topics --describe --topic topic_name --zookeeper zookeeper:32181
```

#### Example 1. publish event:

Here we publish some numbers to a topic topic_name. We use the kafka container console to do so (see docker-compose file above)' . 

```bash
docker-compose exec kafka \
  bash -c "seq 10 | kafka-console-producer \
    --request-required-acks 1 \
    --broker-list localhost:29092 \
    --topic topic_name && echo 'Produced 10 messages.'"
```

#### Example 2. Consume Messages

Starts a consumer to read the messages just produced. 

```bash
docker-compose exec kafka \
  kafka-console-consumer \
    --bootstrap-server localhost:29092 \
    --topic topic_name \
    --from-beginning \
    --max-messages 10
```    

#### Example 3. Watch Topic

```bash
docker-compose exec base kafkacat -C -b kafka:29092 -t topic_name -o beginning
```


## Kafka running in docker in the cloud

We use this docker-compose.yml file

```yml
---
version: '2'
services:
  zookeeper:
    image: confluentinc/cp-zookeeper:latest
    environment:
      ZOOKEEPER_CLIENT_PORT: 32181
      ZOOKEEPER_TICK_TIME: 2000
    expose:
      - "2181"
      - "32181"

  kafka:
    image: confluentinc/cp-kafka:latest
    depends_on:
      - zookeeper
    environment:
      KAFKA_BROKER_ID: 1
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:32181
      KAFKA_LISTENERS: LISTENER_INTERNAL://kafka:29092,LISTENER_EXTERNAL://kafka:9092
      KAFKA_ADVERTISED_LISTENERS: LISTENER_INTERNAL://kafka:29092,LISTENER_EXTERNAL://<PUBLIC IP>:9092
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: LISTENER_INTERNAL:PLAINTEXT,LISTENER_EXTERNAL:PLAINTEXT
      KAFKA_INTER_BROKER_LISTENER_NAME: LISTENER_INTERNAL
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
    volumes:
      - ~/base:/base #<--update if using e.g. ~/mids:/mids
    expose:
      - "9092"
      - "29092"
    ports:
      - "9092:9092"

  base:
    image: atox120/base:latest
    stdin_open: true
    tty: true
    volumes:
      - ~/base:/base                                       
```

issues: volumes not mapped correctly. 

Note - below is not very secure, where ports are opened to the public. 
Pre-requisites:
Cloud:
- Map Port 9092 of cloud VPC/VCN to port 9092 to instance, with ingress rule.
- Public IP of VCN/VPC or public DNS of your cloud network. 
Instance:
- Open firewall to allow traffic on port 9092

Resources: 
Kafka Listeners: https://www.confluent.io/blog/kafka-listeners-explained/
Opening Ports on Linux Centos: 
[link1](https://www.digitalocean.com/community/tutorials/how-to-set-up-a-firewall-using-firewalld-on-centos-7) [link2](https://www.golinuxcloud.com/linux-open-port/#firewalld_open_port)
