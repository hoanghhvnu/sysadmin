# Elasticsearch guide
## 1. Definition
ES is a Search engine open source, powerfull,...
## 2. Download and install
Go to [link](http://www.elasticsearch.org/download/) and download.
Unzip and injoy!
## 3. Start and shutdown
#### a. Start

Inside ES directory, run:

In **foreground**
```
./bin/elasticsearch
```
or
In **background** (run as daemon)
```
./bin/elasticsearch -d
```

#### b. Shutdown

Inside terminal, run:

If it's running in **Foreground**:

Press `Ctrl + C`

If it's running in **Background**: run
```
curl -XPOST 'http://localhost:9200/_shutdown'
```
## 4. Talking to ES
#### a. Java API
Both Java clients talk to the cluster over **port 9300**, using the native Elasticsearch transport protocol
#### b. Restful API
(with JSON over HTTP  )
All other languages can communicate with Elasticsearch over port 9200 using a RESTful API, accessible with your favorite web clien.
Also, talk to ES via `curl` command.
