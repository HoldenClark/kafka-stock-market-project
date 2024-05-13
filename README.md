# Kafka Stock Market Project 
Created a project to process data in real time using Python with pandas to read data in from a dataset, kafka to create a pipeline for the data on an EC2 instance, stored this data in an S3 bucket, and used Crawler, Glue, and Athena to conduct SQL queries on the data in real-time as it was added to the database.


![architechture](https://github.com/HoldenClark/kafka-stock-market-project/assets/108821413/204092f1-f4e6-4d7f-9d49-b6bcaddca577)


## Steps to Setup the Project##

### 1. Create an EC2 instance using an AWS account and connect to this EC2 instance via SSH

![ec2](https://github.com/HoldenClark/kafka-stock-market-project/assets/108821413/536d2e4c-4957-4e55-a4f4-ab121f01ea82)

### 2. download Java 1.8.0 for your cluster with
   $ sudo yum install java-1.8.0-amazon-corretto.x86_64

### 3. download Kafka for your cluster
   wget https://downloads.apache.org/kafka/3.3.1/kafka_2.12-3.7.0.tgz
   tar -xvf kafka_2.12-3.7.0.tgz

### 4. After unzipping kafka
   cd kafka_2.12-3.7.0
   bin/zookeeper-server-start.sh config/zookeeper.properties

### 5. Open another terminal to run the kafka server
   But first ssh to to your ec2 machine as done above
   export KAFKA_HEAP_OPTS="-Xmx256M -Xms128M"        # this will allocate some memory for us so we can store data
   cd kafka_2.12-3.7.0
   bin/kafka-server-start.sh config/server.properties

### 6. Now you should have two separate terminal running zookeeper and kafka

### 7. Now we will create the topic with which we will use to Produce and Consume our data
   In a new console
   cd kafka_2.12-3.7.0
   bin/kafka-topics.sh --create --topic demo --bootstrap-server {Put the Public IP of your EC2 Instance:9092} --replication-factor 1 --partitions 1

### 8. You will need to adjust security preferences for your EC2 cluster

   ![security](https://github.com/HoldenClark/kafka-stock-market-project/assets/108821413/b610ce65-ee82-49ca-9e54-d8f36adcfa50)

   This is not the best practice but it will work for what we want at the moment. We need to have SSH traffic allowed which should be on by default, then we need to adjust and allow all traffic from our local machine to access the cluster.

### 9. Create an IAM user
   You will create an IAM user with AdminAccess you can do this with by giving the user Programmtic Access

### 10. Next create an S3 bucket
   You will need to make sure that your aws is configured so the default-region matches that of your S3 bucket
   *If you do not have amazon command line installed you will need to download it and create credentials for your admin user you created with IAM earlier. These credentials will need to be used in combination with the default-region during configuration to produce the result.

### 11. Create Crawler with Glue
    
   This will crawl our files created in S3 and will create a Schema from them so that we can create queries with Athena later.
    
   Connect it with the S3 bucket you created earlier. Be sure to add the '/' at the end of the connection before submitting.
    
   You will need to create an IAM role to be used for Glue specifically make sure to give it AdminAccess as a permission.
   *Note the IAM role is different than the IAM user created earlier.
    
   When creating the Crawler it will ask you to select a database. If you do not have one already you can create one for this project and then select it.

### 12. Run the KafkaProducer.ipynb and KafkaConsumer.ipynb simultaneously, this will create our data in real time

   ![kafka](https://github.com/HoldenClark/kafka-stock-market-project/assets/108821413/edec6067-a0e0-424e-be87-64d067537a5b)

### 13. Utilize Athena
   Manage preferences and make sure you set an output for your queries. You can create a new S3 bucket for this if you would like.
   You can now run SQL queries on the database as data is added to it in real time!

   ![athena](https://github.com/HoldenClark/kafka-stock-market-project/assets/108821413/9fd3e7bf-43b7-4ac2-9550-2701d3bd0525)
