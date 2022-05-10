# ubuntu-docker-hadoop

# Credit
Taken from the following github project https://github.com/big-data-europe/docker-hadoop and adopted to Ubuntu20.04 Image.

## Connect to namenode , datanode, or other dockers
```
  docker exec -it namenode bash
```

# If your docker containers are installations(applications) fail or slow. Add add google dns 
vi /etc/resolv.conf
nameserver 8.8.8.8

## Create a HDFS directory /dis_materials and Copy data to hdfs 
```
# Copies data to docker
  docker cp dis_materials namdenode:/
# Connect to namenode
  docker exec -it namenode bash
# Create directory in hdfs
  hdfs dfs -mkdir -p /dis_materials
# Copy data from namenode to hdfs
  hdfs dfs -put dis_materials/*.txt dis_materials/*.csv /dis_materials
```

## Analyzing unstructured data with Hadoop Streaming from namenode only
```
cd /dis_materials

hadoop jar /opt/hadoop-3.3.2/share/hadoop/tools/lib/hadoop-streaming-3.3.2.jar \
    -mapper email_count_mapper.py \
    -reducer email_count_reducer.py \
    -input /dis_materials/hadoop_1m.txt \
    -output /dis_materials/output1 \
    -file email_count_mapper.py \
    -file email_count_reducer.py

hadoop fs -text /dis_materials/output1/part*
```

## Analysing unstructured data with MRJob
```
python3 count_sum.py --hadoop-streaming-jar /opt/hadoop-3.2.1/share/hadoop/tools/lib/hadoop-streaming-3.2.1.jar -r hadoop hdfs:///dis_materials/hadoop_1m.txt --output-dir hdfs:///dis_materials/output2 --no-output

hadoop fs -text /dis_materials/output2/part* | less
```

#Testing code without any Hadoop installation
```
cat hadoop_1m.txt | ./email_count_mapper.py | sort -k1,1 | ./email_count_reducer.py
python count_sum.py -r inline hadoop_1m.txt
```
