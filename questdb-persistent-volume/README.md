# Monk & ELK Stack

This repository contains Monk.io template to deploy ELK Stack (Elasticsearch, kibana and logstash) system either locally or on cloud of your choice (AWS, GCP, Azure, Digital Ocean).

This template includes Nginx as a reverse proxy  with ELK Stack  out of box.

## Start

Set up Monk - https://docs.monk.io/docs/monk-in-10/

Start `monkd` and login.

```bash
monk login --email=<email> --password=<password>
```

## Clone Monk ELK repository

In order to load templates and change configuration simply use below commands: 
```bash
git clone https://github.com/kaganmersin/monk-elk

# and change directory to the monk-elk template folder
cd monk-elk

```

## Configuration

You can add/remove configuration of the template.

The current variables can be found in `elk/variables` section

```yaml
  variables:
    elasticsearch-image-tag: 7.9.0
    elasticsearch-jvm-options: "-Xmx256m -Xms256m"
    elasticsearch-http-port: 9200
    elasticsearch-internal-port: 9300
    kibana-http-port: 5601
    kibana-image-tag: 7.9.0
    logstash-image-tag: 7.17.5
    logstash-jvm-options: "-Xmx256m -Xms256m"   
    logstash-http-port: 9600
    nginx-listen-port: 8080
    nginx-image-tag: "latest"
    kibana-server-name: "kibana.example.com"  
```

### ELK Stack configuration files

You can find configuration files in `/files` directory in repository and can edit before the running kit. There are 4 configuration files which bind to the container while run monk-elk kit 


| Configuration File	 | Format Used | Directory in Container | Purpose 
|----------|-------------|------|---------|
| **elasticsearch.yml** | yaml | ` /usr/share/elasticsearch/config/elasticsearch.yml` | Primary configuration file for Elasticsearch
| **kibana.yml** | yaml | `/usr/share/kibana/config/kibana.yml` | The Kibana server reads properties from the kibana.yml file on startup. | 
| **logstash.yml** | yaml | `/usr/share/logstash/config/logstash.yml` | You can set options in the Logstash settings file, logstash.yml, to control Logstash execution |
| **pipeline/logstash.conf** | configuration file | `/usr/share/logstash/pipeline/logstash.conf` | You can create a pipeline by stringing together plugins, inputs, outputs, filters, and sometimes codecs in order to process data. |





##  Template variables

| Variable | Description | Type | Example |
|----------|-------------|------|---------|
| **elasticsearch-image-tag** | Elasticsearch image version. | string | 7.9.0 |
| **elasticsearch-jvm-options** | Elasticsearch jvm options. | string | "-Xmx256m -Xms256m" |
| **elasticsearch-http-port** | Elasticsearch port that will accept requests | int | 9200
| **elasticsearch-internal-port** | Elasticsearch custom port for the node to node communication | int | 9300
| **kibana-http-port** | Kibana http port for UI | int | 9300 |
| **kibana-image-tag** | Kibana image version. | string | 7.9.0 |
| **logstash-image-tag** | Logstash image version. | string | 7.17.5 |
| **logstash-jvm-options** | Logstash jvm options. | string | "-Xmx256m -Xms256m" |
| **logstash-http-port** | Logstash port that will accept requests | int | 9600
| **nginx-listen-port** | Configures the ports that the nginx listens on. | int | 80 |
| **nginx-image-tag** | Nginx image version. | string | latest |
| **kibana-server-name** | Fqdn that nginx will accept and route to. | string | kibana.example.com |



## Local Deployment

First clone the repository simply run below command after launching `monkd`:
:

```bash
➜  monk load MANIFEST

✨ Loaded:
 ├─🔩 Runnables:
 │  ├─🧩 elk/logstash
 │  ├─🧩 elk/nginx
 │  ├─🧩 elk/elasticsearch
 │  └─🧩 elk/kibana
 ├─🔗 Process groups:
 │  └─🧩 elk/stack
 └─⚙️ Entity instances:
    ├─🧩 elk/logstash/metadata
    ├─🧩 elk/kibana/metadata
    └─🧩 elk/elasticsearch/metadata
✔ All templates loaded successfully

➜  monk list -l elk

✔ Got the list
✔ Got the list
Type      Template                         Repository  Version      Tags
runnable  elk/elasticsearch                local       -            self hosted, search platform,
runnable  elk/kibana                       local       -            self hosted, visualization, monitoring
runnable  elk/logstash                     local       -            self hosted, data processing pipeline, logging
runnable  elk/nginx                        local       -            -
group     elk/stack                        local       -            -
runnable  nginx/latest                     elk         -            -
runnable  nginx/reverse-proxy              elk         -            -
runnable  nginx/reverse-proxy-ssl-certbot  elk         1.15-alpine  -


➜  monk run --local-only elk/stack

✔ Started local/elk/stack

```

This will start the entire elk/stack with a Nginx reverse proxy. 


## Cloud Deployment

To deploy the above system to your cloud provider, create a new Monk cluster and provision your instances.

```bash
➜  monk cluster new
? New cluster name elkstack
✔ Cluster created
Your cluster has been created successfully.

➜  monk cluster provider add -p gcp -f <path/to/your-key.json>
✔ Provider added successfully

➜  monk cluster grow -p gcp
? Cloud provider gcp
? Name of the new instance elk-instance
? Tags (split by whitespace) elkstack
? Region europe-central2
? Zone europe-central2-a
? Instance type e2-medium
? Number of instances (or press ENTER to use default = 1) 3
? Default disk type for gcp is HDD (pd-standard). Would you like to change it? No
? Disk size (or press ENTER to use default = 250 GBs) 50
✔ Start creation of new instance(s) on gcp... DONE
✔ Creating node: my-instance-1 DONE
✔ Initializing node: my-instance-1 DONE
✔ Creating node: my-instance-2 DONE
✔ Initializing node: my-instance-2 DONE
✔ Creating node: my-instance-3 DONE
✔ Initializing node: my-instance-3 DONE
✔ Connecting: my-instance-1 DONE
✔ Syncing peer: my-instance-1 DONE
✔ Connecting: my-instance-2 DONE
✔ Connecting: my-instance-3 DONE
✔ Syncing peer: my-instance-2 DONE
✔ Syncing peer: my-instance-3 DONE
✔ Syncing nodes DONE
✔ Cluster grown successfully
```

Once cluster is ready execute the same command as for local and select your cluster (the option will appear automatically).
```bash
➜  monk load MANIFEST

✨ Loaded:
 ├─🔩 Runnables:
 │  ├─🧩 elk/logstash
 │  ├─🧩 elk/nginx
 │  ├─🧩 elk/elasticsearch
 │  └─🧩 elk/kibana
 ├─🔗 Process groups:
 │  └─🧩 elk/stack
 └─⚙️ Entity instances:
    ├─🧩 elk/logstash/metadata
    ├─🧩 elk/kibana/metadata
    └─🧩 elk/elasticsearch/metadata
✔ All templates loaded successfully

➜  monk list -l elk

✔ Got the list
✔ Got the list
Type      Template                         Repository  Version      Tags
runnable  elk/elasticsearch                local       -            self hosted, search platform,
runnable  elk/kibana                       local       -            self hosted, visualization, monitoring
runnable  elk/logstash                     local       -            self hosted, data processing pipeline, logging
runnable  elk/nginx                        local       -            -
group     elk/stack                        local       -            -
runnable  nginx/latest                     elk         -            -
runnable  nginx/reverse-proxy              elk         -            -
runnable  nginx/reverse-proxy-ssl-certbot  elk         1.15-alpine  -


➜  monk run --local-only elk/stack

✔ Started local/elk/stack


```

## Logs & Shell

```bash
# show Elasticsearch logs
➜  monk logs -l 1000 -f elk/elasticsearch

# show Kibana logs
➜  monk logs -l 1000 -f elk/kibana

# show Logstash logs
➜  monk logs -l 1000 -f elk/logstash

# show nginx logs
➜  monk logs -l 1000 -f elk/nginx


# access shell in the container running Elasticsearch
➜  monk shell elk/elasticsearch

# access shell in the container running Kibana
➜  monk shell elk/kibana

# access shell in the container running Logstash
➜  monk shell elk/logstash

# access shell in the container running Nginx
➜  monk shell elk/nginx

```

## Stop, remove and clean up workloads and templates

```bash
➜ monk purge -x elk/stack elk/elasticsearch elk/kibana elk/logstash elk/nginx

✔ elk/stack purged
✔ elk/elasticsearch purged
✔ elk/kibana purged
✔ elk/logstash purged
✔ elk/nginx purged

```