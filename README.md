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

## Mac

```bash

brew install elasticsearch
```

To start, run

```
brew services start elasticsearch # will start the service in the background

# or just run
elasticsearch # this works too!
```

Use curl to make http request

```bash

curl -H "Content-Type: application/json" -X GET <URL> -d '<body>'
```

## Connect to Elasticsearch thru SSH

1. Forward port from virtualbox

- Go to virtualbox and your image for elasticsearch
- click on settings
- Go to network -> advance
- Click on port forwarding
- add three rules:
  - name -> SSH, host -> 127.0.0.1 -> PORT -> 2222 (for mac) Guest PORT -> 2222
  - Elasticsearch, 127.0.0.1 9200 9200
  - Kibana, 127.0.0.1 5601 5601

2. Start the image in virtualbox
3. In terminal, type

```bash
ssh 127.0.0.1
```

## Mapping

A mapping is a schema definition.

## CRUD

```
curl -H "Content-Type: application/json" -XPUT 127.0.0.1:9200/movies --data-binary @movies_mapping.json

# now to see the mapping
curl -H "Content-Type: application/json" -XGET 127.0.0.1:9200/movies/_mapping

# post one record
curl -H "Content-Type: application/json" -XPOST 127.0.0.1:9200/movies/_doc/109487 --data-binary @movie_one_record.json

# update a record
curl -H "Content-Type: application/json" -XPOST 127.0.0.1:9200/movies/_doc/109487/_update --data-binary @movie_one_record.json

# get results
curl -H "Content-Type: application/json" -XGET '127.0.0.1:9200/movies/_search?pretty'  # in mac you need to put '' for your url

# bulk insert
# to do bulk insert the pyload has a special format , basically two lines for each record like {"create": {"_index": ....}}
curl -H "Content-Type: application/json" -XPUT 127.0.0.1:9200/movies/_bulk --data-binary @movies.json
```

Update record

```bash
curl -H "Content-Type: application/json" -XPUT 127.0.0.1:9200/movies/_doc/109487 --data-binary @updated_payload.json
```

## Analyzer

Search on analyzed text fields will return anything remotely relevant. This is really neat!

There are different text analyzers.

### match

Give you multiple records with scores. This gives you back partial match results.

```bash
curl -H "Content-Type: application/json" -XGET '127.0.0.1:9200/movies/_search?pretty' -d '{"query": {"match":{"title": "Star Trek"}}}'

```

### match phrase

Standardize the case for you

```bash
curl -H "Content-Type: application/json" -XGET '127.0.0.1:9200/movies/_se    arch?pretty' -d '{"query": {"match_phrase":{"genre": "action"}}}'

```

If you want a field to perform exact match (no analyzer) then you would need to specify that in the mapping stage before the index, for example:

```json
{
  "mapping": {
    "properties": {
      "id": { "type": "integer" },
      "year": { "type": "date" },
      "genre": { "type": "keyword" },
      "title": { "type": "text", "analyzer": "english" }
    }
  }
}
```

genre will only perform exact match.

## Query

```bash
# use query
curl -H "Content-Type: application/json" -XGET '127.0.0.1:9200/movies/_search?q=Dark'

# you can also use wildcard
curl -H "Content-Type: application/json" -XGET '127.0.0.1:9200/movies/_search?q=inter*'

# query by id
curl -H "Content-Type: application/json" -XGET '127.0.0.1:9200/movies/_doc/122886&pretty=true'

```

### partial match

Use query as data

```
# prefix
curl -H "Content-Type: application/json" -XGET 127.0.0.1:9200/movies/_search -d '{"query" :{"prefix": {"title": "int"}}}'

# wildcard
curl -H "Content-Type: application/json" -XGET 127.0.0.1:9200/movies/_search -d '{"query" :{"wildcard": {"title": "*star*"}}}'

```

### Query time search as you type

```bash
# as the use type "star tr" it will return the one say "Star Trek"
curl -H "Content-Type: application/json" -XGET 127.0.0.1:9200/movies/_search -d '{"query" :{"match_phrase_prefix": {"title": {"query": "star", "slop": 10}}}}'
```
