# Elasticsearch setup

### Pull elasticsearch docker image
docker pull docker.elastic.co/elasticsearch/elasticsearch:7.1.0
docker pull docker.elastic.co/elasticsearch/elasticsearch:7.5.2


### Run elasticsearch docker image
docker run --name es71 -p 9200:9200 -p 9300:9300 -it docker.elastic.co/elasticsearch/elasticsearch:7.1.0
docker run --name es71 -p 9200:9200 -p 9300:9300 -it docker.elastic.co/elasticsearch/elasticsearch:7.5.2

### Elasticsearch config changes in /usr/share/elasticsearch/config/elasticsearch.yml
cluster.name: "docker-cluster"
network.host: 0.0.0.0
discovery.type: single-node
discovery.seed_hosts : 127.0.0.1:9300



# Kibana setup
### Pull kibana docker image
docker pull docker.elastic.co/kibana/kibana:7.1.0
docker pull docker.elastic.co/kibana/kibana:7.5.2


### Run kibana docker image
docker run --name kibana -p 5601:5601 docker.elastic.co/kibana/kibana:7.1.0
docker run -it --name kibana -p 5601:5601 docker.elastic.co/kibana/kibana:7.5.2


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

# logstash setup
  download logstash 7.5.2
  in order to recieve logs at logstash on tcp level below is the configuration
  ### configuration in logstash.yml file
    input {
       tcp {
          port => 8089
          codec => json
       }
    }
    output {
      if [application] == "far" {
        elasticsearch {
            index => "far"
            hosts => ["http://localhost:9200"]
        }
      }

    }
### configuration in logstash.yml file for multiple log files
    input {
    file {
        path => "D:/programming/java/spring-boot-micrometer-master/logback/redditApp.log"
        codec => "json"
        type => "logback"
    }
    file {
        path => "D:/programming/java/spring-boot-micrometer-master-qib/logback/redditApp-saqib.log"
        codec => "json"
        type => "saqib"
    }
}

output {
    if [type]=="logback" {
         elasticsearch {
             hosts => [ "localhost:9200" ]
             index => "kaddu-%{+YYYY.MM.dd}"
        }
    }
    if [type]=="saqib" {
         elasticsearch {
             hosts => [ "localhost:9200" ]
             index => "saqib-%{+YYYY.MM.dd}"
        }
    }
}

  
  ### changes we need to send logs from java application to logstash
  <properties>
		<logback.contrib.version>0.1.5</logback.contrib.version>
	</properties>
  <dependency>
			<groupId>ch.qos.logback.contrib</groupId>
			<artifactId>logback-jackson</artifactId>
			<version>${logback.contrib.version}</version>
		</dependency>

		<!-- ch.qos.logback.contrib.json.classic.JsonLayout -->
		<dependency>
			<groupId>ch.qos.logback.contrib</groupId>
			<artifactId>logback-json-classic</artifactId>
			<version>${logback.contrib.version}</version>
		</dependency>
  
  <dependency>
			<groupId>net.logstash.logback</groupId>
			<artifactId>logstash-logback-encoder</artifactId>
			<version> 7.1.1</version>
		</dependency>
  
  logback.xml file
  further configuration can be done in logback.xml file placed in resource folder
 


