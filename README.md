# elastic search

## Install ubantu

Download `ubuntu server` from: https://ubuntu.com/download/server/

Once downloaded, we will use it in the virtualbox.

Refer to the video for installation and set up for virtualbox.

username: hsin
password: password

Once in ubuntu, download elasticsearch
```bash
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -

# then you will be promoted to enter password
# then run
sudo apt-get install apt-transport-https

# then run
# echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-7.x.list

# fianlly
sudo apt-get update && sudo apt-get install elasticsearch

# once installed
sudo vi /etc/elasticsearch/elasticsearch.yml

#uncoment some stuff.  Refer to video

# then run reload
sudo /bin/systemctl daemon-reload
sudo /bin/systemctl enable elasticsearch.service
sudo /bin/systemctl start elasticsearch.service

# test connection
curl -XGET 127.0.0.1:9200

```

Now let's try it
```bash
# get schema to work with
wget http://media.sundog-soft.com/es7/shakes-mapping.json

# send the mapping to elasticsearch
curl -H "Content-Type: application/json" -XPUT 127.0.0.1:9200/shakespeare --data-binary @shakes-mapping.json

# get the actual data
wget http://media.sundog-soft.com/es7/shakespeare_7.0.json

# send to es7. This will run for like 15 mins to get everything indexed
curl -H "Content-Type: application/json" -XPUT '127.0.0.1:9200/shakespeare/_bulk' --data-binary @shakespeare_7.0.json

# let's make a search!

curl -H "Content-Type: application/json" -XGET '127.0.0.1:9200/shakespeare/_search?pretty' -d '{"query" :{"match_phrase": {"text_entry": "to be or not to be"}}}'



### Overview
#### Kibana 
A GUI for elasticsearch that allows you to show dashboard, aggregation etc.

```





