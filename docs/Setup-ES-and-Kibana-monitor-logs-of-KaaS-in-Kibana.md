# Harmony: Setup ES and Kibana , monitor logs of KaaS in Kibana

Following below procedure, you will be able to setup one node Elastic search cluster and then setup kibana to visualize that cluster.

**Pre Requsite:**

Elastic search at least needs java 8\. Please install java 8 on your machine before following next steps.

**Setting up Elastic Search:**

*   Download any 5.x > version of ES from here : [https://www.elastic.co/downloads/elasticsearch](https://www.elastic.co/downloads/elasticsearch)
*   tar -xvf elasticsearch-<version>.tar.gz
*   Go to folder elasticsearch-<version>
*   edit config/elasticsearch.yaml file and provide following details:
    *   cluster.name - name of your cluster
    *   node.name - name of node
    *   network.host: 127.0.0.1 (this is needed for development cluster. For production usage, settings are different.)
    *   http.host: 0.0.0.0 (this is needed for development cluster. For production usage, settings are different.)
    *   http.port: 9200 (default es port)
*   To start ES cluster, **./bin/elasticsearch**
*   To verify cluster, please visit [http://](http://IP)<IP of ES node>:9200
    You will see something like this


<div class="codeHeader panelHeader pdl" style="border-bottom-width: 1px;">**Running ElasticSearch**</div>

<pre class="syntaxhighlighter-pre" data-syntaxhighlighter-params="brush: text; gutter: false; theme: Confluence" data-theme="Confluence">{
  "name" : "cx-es-node-1",
  "cluster_name" : "cx-es",
  "cluster_uuid" : "LFB10ubkTDuCFHZ5i_iFdg",
  "version" : {
    "number" : "5.6.0",
    "build_hash" : "781a835",
    "build_date" : "2017-09-07T03:09:58.087Z",
    "build_snapshot" : false,
    "lucene_version" : "6.6.0"
  },
  "tagline" : "You Know, for Search"
}</pre>

**Setting up Kibana:**

*   Download corresponding Kibana : [https://www.elastic.co/downloads/kibana](https://www.elastic.co/downloads/kibana)
*   tar -xvf kibana-<version>-linux-x86_64
*   Go to folder kibana-<version>/
*   edit config/kibana.yaml file and provide following details:
    *   server.host: "<IP of ES machine>"
    *   elasticsearch.url: "http://<IP of ES machine>:9200"
*   To start kibana, **./bin/kibana**
*   Kibana will be running on http://<IP of ES machine>:5601

**To start hermes monitoring agent:**

*   In control plane k8s cluster, we will run hermes monitoring agent as daemonset. Hermes agent is based on fluentd and it collects logs from all pods and sends to elastic search
*   To run hermes pod : 
    *   helm install [https://repo.ci.dfj.io/harmony_mahl-collector/premerge/hermes-1.1.4-dev23.tgz](https://repo.ci.dfj.io/harmony_mahl-collector/premerge/hermes-1.1.4-dev23.tgz) --name hermes-deploy --set es.ingestion_point=<elasticsearch IP> --set es.ingestion_port=9200
*   After 2-3 mins you will start seeing logs in kibana. You can filter put using namespace and pod name to look for specific logs.