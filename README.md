# Kafka Stock Market Project 
Created a project to process data in real time using Python with pandas to read data in from a dataset, kafka to create a pipeline for the data on an EC2 instance, stored this data in an S3 bucket, and used Crawler, Glue, and Athena to conduct SQL queries on the data in real-time as it was added to the database.


![architechture](https://github.com/HoldenClark/kafka-stock-market-project/assets/108821413/204092f1-f4e6-4d7f-9d49-b6bcaddca577)


## Steps To Setup The Project

### 1. Create A New Folder in Your Documents to Store Your Key

   To connect to SSH you will have to change directory to your folder with the .pem key.
   
   $ cd Documents/my-kafka-project
   
   Then you can connect to SSH.
   
   When you create the EC2 instance you will receive/create a .pem key, be sure to place that in our project folder.

### 2. Create An EC2 Instance Using An AWS Account And Connect To This EC2 Instance Via SSH

![ec2](https://github.com/HoldenClark/kafka-stock-market-project/assets/108821413/536d2e4c-4957-4e55-a4f4-ab121f01ea82)

### 3. Download Java 1.8.0 For Your Cluster

   $ sudo yum install java-1.8.0-amazon-corretto.x86_64

### 4. Download Kafka For Your Cluster

   $ wget https://downloads.apache.org/kafka/3.7.0/kafka_2.12-3.7.0.tgz
   $ tar -xvf kafka_2.12-3.7.0.tgz

### 5. After Unzipping Kafka

   $ cd kafka_2.12-3.7.0
   $ bin/zookeeper-server-start.sh config/zookeeper.properties

### 6. Open Another Terminal To Run The Kafka Server

   But first ssh to to your ec2 machine as done above
   $ export KAFKA_HEAP_OPTS="-Xmx256M -Xms128M"        # this will allocate some memory for us so we can store data
   $ cd kafka_2.12-3.7.0
   $ bin/kafka-server-start.sh config/server.properties

### 7. Create Two Separate Terminals To Run Zookeeper And The Kafka Server Each Connected To Your EC2 Instance

### 8. Create The Topic With Which We Will Use To Produce And Consume Our Data
   In a new console
   $ cd kafka_2.12-3.7.0
   $ bin/kafka-topics.sh --create --topic demo --bootstrap-server {Put the Public IP of your EC2 Instance:9092} --replication-factor 1 --partitions 1

### 9. You Will Need To Adjust Security Preferences For Your EC2 Cluster

   ![security](https://github.com/HoldenClark/kafka-stock-market-project/assets/108821413/b610ce65-ee82-49ca-9e54-d8f36adcfa50)

   This is not the best practice but it will work for what we want at the moment. We need to have SSH traffic allowed which should be on by default, then we need to adjust and allow all traffic from our local machine to access the cluster.

### 10. Create An IAM user
   You will create an IAM user with AdminAccess you can do this with by giving the user Programmtic Access

### 11. Next Create An S3 Bucket
   You will need to make sure that your aws is configured so the default-region matches that of your S3 bucket
   *If you do not have amazon command line installed you will need to download it and create credentials for your admin user you created with IAM earlier. These credentials will need to be used in combination with the default-region during configuration to produce the result.

### 12. Create Crawler With Glue
    
   This will crawl our files created in S3 and will create a Schema from them so that we can create queries with Athena later.
    
   Connect it with the S3 bucket you created earlier. Be sure to add the '/' at the end of the connection before submitting.
    
   You will need to create an IAM role to be used for Glue specifically make sure to give it AdminAccess as a permission.
   *Note the IAM role is different than the IAM user created earlier.
    
   When creating the Crawler it will ask you to select a database. If you do not have one already you can create one for this project and then select it.

### 13. Run The KafkaProducer.ipynb And KafkaConsumer.ipynb Simultaneously, This Will Create Our Data In Real Time

   ![kafka](https://github.com/HoldenClark/kafka-stock-market-project/assets/108821413/edec6067-a0e0-424e-be87-64d067537a5b)

### 14. Utilize Athena To Run Queries
   Manage preferences and make sure you set an output for your queries. You can create a new S3 bucket for this if you would like.
   You can now run SQL queries on the database as data is added to it in real time!

   ![athena](https://github.com/HoldenClark/kafka-stock-market-project/assets/108821413/9fd3e7bf-43b7-4ac2-9550-2701d3bd0525)
