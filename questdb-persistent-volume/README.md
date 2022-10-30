# Monk & QuestDB

This repository contains Monk.io template to deploy QuestDB system either locally or on cloud of your choice (AWS, GCP, Azure, Digital Ocean).


## Start

Set up Monk - https://docs.monk.io/docs/monk-in-10/

Start `monkd` and login.

```bash
monk login --email=<email> --password=<password>
```

## Clone Monk QuestDB repository

In order to load templates and change configuration simply use below commands: 
```bash
git clone https://github.com/kaganmersin/monk-questdb

# and change directory to the monk-questdb/questdb template folder
cd monk-questdb/questdb-persistent-volume

```

## Configuration

You can add/remove configuration of the template.

The current variables can be found in `questdb/stack/variables` section

```yaml
  variables:
    questdb-image-tag: 6.5.4
```

### QuestDB configuration files

You can find configuration files in `/files` directory in repository and can edit before the running kit. There are 2 configuration files which bind to the container while run monk-questdb kit 


| Configuration File	 | Format Used | Directory in Container | Purpose 
|----------|-------------|------|---------|
| **log.conf** | conf | `/var/lib/questdb/conf/log.conf` |  Configuration file for QuestDB log settings
| **server.conf** | conf | `/var/lib/questdb/conf/server.conf` | Primer configuration file for QuestDB server | 





##  Template variables

| Variable | Description | Type | Example |
|----------|-------------|------|---------|
| **questdb-image-tag** | QuestDB image version. | string | 6.5.4 |



## Local Deployment

First clone the repository simply run below command after launching `monkd`:
:

```bash
➜  monk load MANIFEST

✨ Loaded:
 ├─🔩 Runnables:
 │  └─🧩 questdb/questdb
 ├─🔗 Process groups:
 │  └─🧩 questdb/stack
 └─⚙️ Entity instances:
    └─🧩 questdb/questdb/metadata
✔ All templates loaded successfully

➜  monk list -l questdb

✔ Got the list
Type      Template         Repository  Version  Tags
runnable  questdb/questdb  local       -        -
group     questdb/stack    local       -        -


➜  monk run  questdb/stack

✔ Started local/questdb/stack

```

This will start the entire questdb/stack with a Nginx reverse proxy. 


## Cloud Deployment

To deploy the above system to your cloud provider, create a new Monk cluster and provision your instances.

```bash
➜  monk cluster new
? New cluster name questdb
✔ Cluster created
Your cluster has been created successfully.

➜  monk cluster provider add -p gcp -f <path/to/your-key.json>
✔ Provider added successfully

➜  monk cluster grow -p gcp
? Cloud provider gcp
? Name of the new instance questdb-instance
? Tags (split by whitespace) questdb
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
 │  └─🧩 questdb/questdb
 ├─🔗 Process groups:
 │  └─🧩 questdb/stack
 └─⚙️ Entity instances:
    └─🧩 questdb/questdb/metadata
✔ All templates loaded successfully

➜  monk list -l elk

✔ Got the list
Type      Template         Repository  Version  Tags
runnable  questdb/questdb  local       -        -
group     questdb/stack    local       -        -


➜  monk run  questdb/stack

✔ Started local/questdb/stack

```

## Logs & Shell

```bash
# show QuestDB logs
➜  monk logs -l 1000 -f questdb/questdb

# access shell in the container running QuestDB
➜  monk shell questdb/questdb

```

## Stop, remove and clean up workloads and templates

```bash
➜ monk purge -x questdb/stack questdb/questdb

✔ questdb/stack purged
✔ questdb/questdb purged

```