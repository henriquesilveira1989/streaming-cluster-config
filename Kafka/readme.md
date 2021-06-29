# Kafka

Example of deploying a Kafka cluster in Google Cloud Platform. The command below can be run from the [Google Cloud Shell](https://cloud.google.com/shell/docs/quickstart) or from your PC using the [Google Cloud Platform CLI](https://cloud.google.com/sdk/). All you need to substitute are the project and instance name (e.g. kafka-1)

| **Variables** | **Examples** | **Instance**  |
| ------------- |:------------- | :----- |
| [YOUR-PROJECT]	| my-project | my-project |
| [INSTANCE-NAME]	| kafka-1, kafka-2, kafka-3    | kafka-1 |
| [HOSTNAME]	| kafka-1, kafka-2, kafka-3 | kafka-1 |
| BROKER-ID	| 0, 1, 2 | 0 |


```bash
gcloud compute --project "[YOUR-PROJECT]" instances create "[INSTANCE-NAME]" --zone "europe-west1-c" --machine-type "n1-standard-1" --subnet "default" --maintenance-policy "MIGRATE" --service-account "1077112676311-compute@developer.gserviceaccount.com" --scopes "https://www.googleapis.com/auth/devstorage.read_only","https://www.googleapis.com/auth/logging.write","https://www.googleapis.com/auth/monitoring.write","https://www.googleapis.com/auth/servicecontrol","https://www.googleapis.com/auth/service.management.readonly","https://www.googleapis.com/auth/trace.append" --tags "http-server" --image "centos-7-v20170523" --image-project "centos-cloud" --boot-disk-size "10" --boot-disk-type "pd-standard" --boot-disk-device-name "[INSTANCE-NAME]"
```

Lets ssh to the VM and run the following command to install some dependencies and download Kafka.

```bash
# Installing dependencies
sudo su
yum install -y wget nano java

# Downloading and extracting Kafka
wget http://mirror.ox.ac.uk/sites/rsync.apache.org/kafka/0.10.2.1/kafka_2.12-0.10.2.1.tgz
tar -xvzf kafka_2.12-0.10.2.1.tgz
cd kafka_2.12-0.10.2.1
```

Now, update the Kafka server properties with the property values below:

```bash
nano config/server.properties
```

```properties
# Broker id needs to be unique for each Kafka server
broker.id=[BROKER-ID]

# This will be the Zookeeper servers created before
zookeeper.connect=zook-1:2181,zook-2:2181,zook-3:2181

# Examples of hostnames - kafka-1, kafka-2, kafka-3
host.name=[HOSTNAME]
```

Once we've update the settings of all Kafka server properties, we can start them

```bash
bin/kafka-server-start.sh config/server.properties
```
