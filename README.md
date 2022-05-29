# Elasticsearch setup

### Pull elasticsearch docker image
docker pull docker.elastic.co/elasticsearch/elasticsearch:7.1.0

### Run elasticsearch docker image
docker run --name es71 -p 9200:9200 -p 9300:9300 -it docker.elastic.co/elasticsearch/elasticsearch:7.1.0


# Kibana setup
### Pull kibana docker image
docker pull docker.elastic.co/kibana/kibana:7.1.0


### Run kibana docker image
docker run --name kibana -p 5601:5601 docker.elastic.co/kibana/kibana:7.1.0

### changes in kibana.yml file
fileLocation: /usr/share/kibana/config/kibana.yml
# Default Kibana configuration for docker target
server.name: kibana
server.host: "0"
elasticsearch.hosts: ["http://172.0.0.1:9200"] // this is the docker internal ip of elasticsearch container
xpack.monitoring.ui.container.elasticsearch.enabled: false  //xpack needs to be disable
xpack.security.enabled: false                               //xpack needs to be disbaled


# APM setup
### Download jar from maven url
https://search.maven.org/search?q=g:co.elastic.apm%20AND%20a:elastic-apm-agent
### package your jar and run bellow command
java -javaagent:<jar location>/elastic-apm-agent-1.31.0.jar -jar <application jar location>


