# ansible-presto

Setup Presto using Ansible - POC version.

# Prepequisites
On every node you want to install Presto, you must have:
* Linux
* Java 8 - 
  * You may install it using [this playbook](https://gist.github.com/elisska/7730c1252088217a19eb6a0cd190f1e3)
* Python 2.4+
* Hive metastore

# Installation steps
* Edit [inventory-coordinator](inventory-coordinator) and [inventory-worker](inventory-worker) files and list nodes to be used for coordinator and workers
* For both coordinator and worker roles, edit *hive.properties* file and specify correct Hive Metastore address, AWS access key and AWS secret key
  * [hive.properties for coordinator](roles/install-presto-coordinator/files/hive.properties.coordinator)
  * [hive.properties for workers](roles/install-presto-worker/files/hive.properties.worker)
* For both coordinator and worker roles, edit *config.properties* file and specify hostname that you plan to use for coordinator:
  * [config.properties for coordinator](roles/install-presto-coordinator/files/config.properties.coordinator)
  * [config.properties for worker](roles/install-presto-worker/files/config.properties.worker)
* Run installation for coordinator using this command
```
ansible-playbook -i inventory-coordinator setup-presto-coordinator.yml -u <remote_user> --private-key=<path_to_private_key>
```
Note - this playbook will automatically start Presto coordinator.
* Run installation for workers using this command:
```
ansible-playbook -i inventory-worker setup-presto-worker.yml -u <remote_user> --private-key=<path_to_private_key>
```
Note - this playbook will automatically start Presto workers.

# Start and stop Presto
If you need to start/stop presto independently on the installation process, use [start-presto.yml](start-presto.yml) and [stop-presto.yml](stop-presto.yml) playbooks.
Start command:
```
ansible-playbook -i inventory-coordinator start-presto.yml -u <remote_user> --private-key=<path_to_private_key>
ansible-playbook -i inventory-worker start-presto.yml -u <remote_user> --private-key=<path_to_private_key>
```
Stop command:
```
ansible-playbook -i inventory-coordinator stop-presto.yml -u <remote_user> --private-key=<path_to_private_key>
ansible-playbook -i inventory-worker stop-presto.yml -u <remote_user> --private-key=<path_to_private_key>
```

# Connect and run queries
Download Presto command line interface and run queries: 
```
wget https://repo1.maven.org/maven2/com/facebook/presto/presto-cli/0.185/presto-cli-0.185-executable.jar
mv presto-cli-0.185-executable.jar presto
chmod +x presto
./presto --server <COORDINATOR_HOSTNAME>:8080 --catalog hive --schema default
presto:default>
```

