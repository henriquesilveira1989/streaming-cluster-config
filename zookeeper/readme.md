# Zookeeper 

Example of deploying a Zookeeper cluster in Google Cloud Platform. The command below can be run from the [Google Cloud Shell](https://cloud.google.com/shell/docs/quickstart) or from your PC using the [Google Cloud Platform CLI](https://cloud.google.com/sdk/). All you need to substitute are the project and instance name (e.g. zook-1)

| **Variables** | **Examples** | **Instance** |
| ------------- |:------------- | :----- |
| [YOUR-PROJECT] | my-project | my-project |
| [INSTANCE-NAME]     | zook-1, zook-2, zook-3     | zook-1 |
| ZOOKEEPER-ID | 1, 2, 3 | 1 |

```bash
gcloud compute --project "[YOUR-PROJECT]" instances create "[INSTANCE-NAME]" --zone "europe-west1-c" --machine-type "n1-standard-1" --subnet "default" --maintenance-policy "MIGRATE" --service-account "1077112676311-compute@developer.gserviceaccount.com" --scopes "https://www.googleapis.com/auth/devstorage.read_only","https://www.googleapis.com/auth/logging.write","https://www.googleapis.com/auth/monitoring.write","https://www.googleapis.com/auth/servicecontrol","https://www.googleapis.com/auth/service.management.readonly","https://www.googleapis.com/auth/trace.append" --tags "http-server" --image "centos-7-v20170523" --image-project "centos-cloud" --boot-disk-size "10" --boot-disk-type "pd-standard" --boot-disk-device-name "[INSTANCE-NAME]"
```

Once the VM is running, we can ssh to it and run the following command to install some dependencies and download Kafka.

```bash
# Installing dependencies
sudo su
yum install -y wget nano java

# Automatically creating the Zookeeper myid file
mkdir /tmp/zookeeper
touch /tmp/zookeeper/myid
echo "ZOOKEEPER-ID" > zookeeper/myid

# Downloading and extracting Kafka
wget http://mirror.ox.ac.uk/sites/rsync.apache.org/kafka/0.10.2.1/kafka_2.12-0.10.2.1.tgz
tar -xvzf kafka_2.12-0.10.2.1.tgz
cd kafka_2.12-0.10.2.1
```

Modify the zookeeper properties to include all the instance details.

```bash
nano config/zookeeper.properties
```

```properties
# In milliseconds
tickTime=2000

# In ticks
initLimit=10
syncLimit=5
maxClientCnxns=30

# All Zookeeper servers need to be aware of other Zookeepers part of the cluster
server.1=zook-1:2888:3888
server.2=zook-2:2888:3888
server.3=zook-3:2888:3888
```

Once we've update the settings of all Zookeeper server properties, we can start them

```bash
bin/zookeeper-server-start.sh config/zookeeper.properties
```
