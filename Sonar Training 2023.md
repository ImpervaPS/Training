**For Training Only! Not For Production!**
## Diagram
### AWS
![](_attachments/Pasted%20image%2020231017181757.png)
### Azure
![](_attachments/Pasted%20image%2020231029162238.png)

### GCP
![](_attachments/Pasted%20image%2020231029162403.png)

## Services

## Basic Configuration
### GCP VM Instance

Machine Type: `e2-standard-8`
OS: `Centos 8 Stream`
Disk Space: `240GB`

Check the firewall that allow **port 8443 & 22** open to your current IP and to your agentless gw.
GOTO 'Firewall' to configure

```
## howto check your current IP address
curl ifconfig.me
```

### AWS VM Instance

AMI OS: `CentOS-Stream-ec2-8-20220919.1.x86_64-a5911e94-1971-4697-9bc5-02904340f1df`
AMI ID: `ami-09bca320521273916`
Instance Type: `t2.2xlarge`
Key authentication.

### Azure VM Instance
Will add shortly.
### Download installation from FTP-Downloads

Ask admin to unblock the public access of the bucket, and the policy change to all accounts.

In terminal, run the command such as:
```
curl -o sonar4.13.tar.gz http://impervaps.s3-website-ap-southeast-1.amazonaws.com/ftp-downloads/jsonar-4.13.0.10.0.tar.gz

sudo tar -xvf sonar4.13.tar.gz -C /opt/
sudo chmod a+rx /opt/jsonar/
```

Start the installation:
```sh
[impervaps_lake@instance-2 opt]$ sudo /opt/jsonar/apps/4.13.0.10.0/bin/sonarg-setup 

===========
Enter a concise, descriptive, and unique display name for this machine.
Display name [instance-2]: dsfgw413-gcp
Is this a remote machine for a federated gateway system? [y/N]: y

Enter the IP Address or FQDN of this host. Sonar uses this IP/FQDN to provide access to reports that users may receive as email links.
IP address or FQDN [34.170.16.34.bc.googleusercontent.com]: dsfgw413-gcp.impervaps.com

Enter the IP Address or FQDN of this host. Sonar uses this IP/FQDN for communication between the DSF Hub and Agentless Gateways.
IP address or FQDN [34.170.16.34.bc.googleusercontent.com]: dsfgw413-gcp.impervaps.com

===========
Email Setup
===========
Configure email settings [y/N]: N

Advanced Setup
==============
- Product selection
- Sonar node UID
- Sonar HADR is_primary port
- Custom directory locations

Configure advanced settings [y/N]: N
```

Using this command to test the service start working on port 8443
```
curl https://localhost:8443 -kv

---
* Connection #0 to host localhost left intact</html>
```

No need to disable selinux or configure the firewall rules.
Configure the Firewall rules (just in case)
```
sudo firewall-cmd --zone=public --add-port=8443/tcp --add-port=22/tcp --permanent
sudo firewall-cmd --reload
sudo firewall-cmd --zone=public --list-ports
```

Reminder: 
In case you need to reboot, stop the sonar services firstly.
```
sudo /opt/jsonar/apps/4.13.0.10.0/bin/sonarg-setup services stop
```

Make sure you can visit it through public ip/ DNS hostname of the **agentless-gw** we just installed.
![](_attachments/Pasted%20image%2020231020204354.png)

**Conduct the same operation in aws/azure.**

### Install DBeaver

Download and install it. We will use it to generate some queries.
https://dbeaver.io/download/

## Federation
Using any cloud to install one Sonar Hub(warehouse), and configure static public ip for the federation with agentless gateways, or you can use DNS.

GOTO 


## GCP CloudSQL

### GCP Cloud SQL Instance

Create the Cloud SQL MySQL 8.0
![](_attachments/Pasted%20image%2020231028203255.png)
Allow the connection via the public IP during the configuration.

Configure the firewall, add your local IP to it.
![](_attachments/Pasted%20image%2020231028204450.png)

Testing the connection using DBeaver

![](_attachments/Pasted%20image%2020231028204924.png)
Make sure the 'Test Connection' succeeds before moving on.
**Create DB and tables, generate some queries.**
### DSF Cloud Account

> DSF supports the below authentication mechanisms to access Google Cloud resources:
- **Service Account:** If you opt to use "service_account" authentication mechanism, a JSON-formatted key file needs to be generated. This file holds the credentials that the Agentless Gateway uses to access logs from the Pub/Sub subscription. Ensure that the key file is copied to the Agentless Gateway host and it is owned by the "sonarw" user.  To create a service account key, please see [Creating and managing service account keys](https://cloud.google.com/iam/docs/creating-managing-service-account-keys). **A service account is identified by its email address, which is unique to the account.**
- **Default:** If you use "default" authentication mechanism, then attach the Service Account created above to the Google Cloud Agentless Gateway instance.

In our example, we adopt the Service Account. This is widely used.
Make sure the following roles granted to the Service Account.

GOTO IAM, create service account and assign these roles:
`Pub/Sub Viewer`
`Pub/Sub Subscriber`
`Viewer`

Skip 'Grant users access to this service account (optional)'

You can verify and edit the roles in IAM.
![](_attachments/Pasted%20image%2020231021125754.png)

Next, create and save the key as json format.
![](_attachments/Pasted%20image%2020231021131640.png)
Then upload it into the $JSONAR_LOCALDIR folder path:
```
/opt/jsonar/local/credentials/xxx.json
chmod 644 xxx.json
```

GOTO DSFHub to configure the Cloud Account.
![](_attachments/Pasted%20image%2020231029103517.png)

the Asset naming convention:
Asset_id: `my_service_account@project-name.iam.gserviceaccount.com:project-name`
Server IP & Server Hostname: `cloud.google.com`
Server Port : `443`
Auth mechanism: `service_account`
key_file: `/path/to/gcp/credentials/service_account.json`

Example:
Asset_id: `training-demo@e-centaur-394913.iam.gserviceaccount.com:e-centaur-394913`
key_file: `/opt/jsonar/local/credentials/e-centaur-394913-f89bccf37300.json`

![](_attachments/Pasted%20image%2020231029111216.png)
Sometimes, click Refresh and test again. Make sure it's successful before moving on.
### GCP Enable Audit Logging

GOTO IAM/Audit logs, then enable Cloud SQL Audit log
- Admin Read
- Data Read
- Data Writes
![](_attachments/Pasted%20image%2020231028222524.png)

GOTO Cloud SQL instance/Overview/Edit, then Flags:
- log_output : File
- general_log : on

![](_attachments/Pasted%20image%2020231028223335.png)

GOTO Logs Explorer to filter out logs with a LogName that ends with 'mysql-general.log'.
Please make sure you can see this type of LogName before moving on.
```
{
  "textPayload": "2023-10-28T14:34:48.262698Z\troot[root] @  [127.0.0.1] 1437 1709744194 Query\tSelect 1",
  "insertId": "2#809179008544#8535845228719947301#general#1698503688262698000#0000000000002af9-0@a1",
  "resource": {
    "type": "cloudsql_database",
    "labels": {
      "project_id": "e-centaur-394913",
      "region": "us-central",
      "database_id": "e-centaur-394913:gcp-demo-mysql"
    }
  },
  "timestamp": "2023-10-28T14:34:48.262698Z",
  "labels": {
    "INSTANCE_UID": "1-a869952c-fd4e-4534-8905-5de03e293389",
    "LOG_BUCKET_NUM": "9"
  },
  "logName": "projects/e-centaur-394913/logs/cloudsql.googleapis.com%2Fmysql-general.log",
  "receiveTimestamp": "2023-10-28T14:34:49.183129418Z"
}
```
### GCP PubSub

Understand the basic concept:
https://cloud.google.com/logging/docs/routing/overview?hl=en
![](_attachments/Pasted%20image%2020231028212755.png)
logging APi -> user-defined Sink to route the logs -> topic + subscription in PubSub
In our example, we won't write logs to 'Log Buckets'.

GOTO Logging -> Log Router
Create the sink in 'log router' and create topics at the same time.
![](_attachments/Pasted%20image%2020231028215016.png)
Enter the contents below in the filter.
Replace with your database_id and your logName.
```
resource.type="cloudsql_database"
resource.labels.database_id="e-centaur-394913:gcp-demo-mysql"
logName: "projects/e-centaur-394913/logs/cloudsql.googleapis.com%2Fmysql-general.log"
```

![](_attachments/Pasted%20image%2020231028221721.png)

Create subscription
GOTO Pub/Sub -> Topics, right click to create Subscriptions
![](_attachments/Pasted%20image%2020231029141711.png)

Subscription name: projects/e-centaur-394913/subscriptions/gcp-demo-mysql
![](_attachments/Pasted%20image%2020231029142005.png)
Write down the subscription name: `projects/e-centaur-394913/subscriptions/gcp-demo-mysql` for later use.
## AWS RDS
### AWS IAM

#### Service Endpoints
<https://docs.aws.amazon.com/general/latest/gr/cwl_region.html>


## Templates
### GCP CloudSQL MySQL

#### GCP PubSub

Template:
asset_id: `projects/my-gcp-project/subscriptions/my-mysql-subscription`
asset_display_name: `my_gcp_mysql_pubsub_display_name`
pubsub_subscription: `projects/your-gcp-project/subscriptions/some-mysql-subscription`
audit_type: `MYSQL`
Server Port: `443`
auth_mechanism: `service_account`

Example:
pubsub_subscription: `projects/e-centaur-394913/subscriptions/gcp-demo-mysql`

> the public api provided by Google:
![](_attachments/Pasted%20image%2020231029120149.png)

<https://cloud.google.com/pubsub/docs/reference/service_apis_overview>
#### GCP MySQL
Template:
asset_id: `my-gcp-project:location:my-gcp-mysql-db`
asset_display_name: `my_gcp_mysql_display_name`
auth_mechanism: `password`
logs_destination_asset_id: `projects/my-gcp-project/subscriptions/my-mysql-subscription`
server_port: `3306`
database_name: `mysql`

Optional (leave them empty or default value):
username:
password:

Example:
logs_destination_asset_id: `projects/e-centaur-394913/subscriptions/gcp-demo-mysql`

#### Onboarding

USC and Template.
Prepare the template.
After uploading, validate before importing the asset & connections.

In the agentless gw -> Asset Details,  select the PubSub and click `Connect Gateway` 

Enable audit in Hub -> USC. And configure the parent asset of PubSub.
![](_attachments/Pasted%20image%2020231029151323.png)
Make sure the 'Audit collection' turns green before moving on.

Check the logs for any issue. 
After the enablement, you should see the log file under gateway/syslog.
![](_attachments/Pasted%20image%2020231029151643.png)

Or you can check the service that is running in agentless gw.
Service Name:
`systemctl status -l gateway-gcp@mysql.service`
And the log file path:
`$JSONAR_LOGDIR/gateway/syslog/mysql_pubsub.log`


## On-Prem or Self Managed Instance

### MongoDB

OnPrem Database onboarding
#### Install MongoDB Enterprise version 4.4
https://www.mongodb.com/docs/v4.4/tutorial/install-mongodb-enterprise-on-red-hat/

```bash
vi /etc/yum.repos.d/mongodb-enterprise-4.4.repo

[mongodb-enterprise-4.4]
name=MongoDB Enterprise Repository
baseurl=https://repo.mongodb.com/yum/redhat/$releasever/mongodb-enterprise/4.4/$basearch/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-4.4.asc
```

```sh
sudo yum install -y mongodb-enterprise
sudo systemctl start mongod
sudo systemctl enable mongod
## Next, execute the command to initiate the db session.
mongo
```

Configure the /etc/mongod.conf to enable the bind ip to 0.0.0.0 to allow remote connection.
```
# network interfaces
net:
  port: 27017
  bindIp: 0.0.0.0  # Enter 0.0.0.0,:: to bind to all IPv4 and IPv6 addresses or, alternatively, use the net.bindIpAll setting.
```

Follow the official documentation for audit logging.
<https://docs.imperva.com/bundle/onboarding-databases-to-sonar-reference-guide/page/212012395.html>

Or you can refer to mongodb for more details.
https://www.mongodb.com/docs/v4.4/tutorial/configure-auditing/

Configure the mongod.conf to enable auditing:
```
security:
  authorization: enabled
  
auditLog:
  destination: file
  format: JSON
  path: /var/lib/mongo/auditLog.json
```
Log rotation part(optional):
Using the default admin user to create user that has root role

```sh
## run mongo command to start session
[root@mvp ~]# mongo
MongoDB shell version v4.4.25
connecting to: mongodb://127.0.0.1:27017/?compressors=disabled&gssapiServiceName=mongodb
Implicit session: session { "id" : UUID("aa020135-8105-4835-b5c8-f4b409b2b61b") }
MongoDB server version: 4.4.25
MongoDB Enterprise > use admin;
switched to db admin

## run command to creat user that has root role
db.createUser({
   user: "root",
   pwd: "default",
   roles: ["root"]
});

## exit mongo and restart mongod
systemctl restart mongod

## Should you receive the error as shown below:
MongoDB shell version v4.4.25
connecting to: mongodb://127.0.0.1:27017/?compressors=disabled&gssapiServiceName=mongodb
Error: couldn't connect to server 127.0.0.1:27017, connection attempt failed: SocketException: Error connecting to 127.0.0.1:27017 :: caused by :: Connection refused :
connect@src/mongo/shell/mongo.js:374:17

## try this command to solve:

ls -l /tmp/mongodb-27017.sock
## If the file exists, you should remove it and restart mongod
sudo rm /tmp/mongodb-27017.sock
sudo systemctl restart mongod

## login using root role and run the log rotation command
[root@mvp mongo]# mongo -u root -p
MongoDB shell version v4.4.25
Enter password: 
connecting to: mongodb://127.0.0.1:27017/?compressors=disabled&gssapiServiceName=mongodb
Implicit session: session { "id" : UUID("19570a18-9a97-49d8-8669-97febe92fbd6") }
MongoDB server version: 4.4.25
---
The server generated these startup warnings when booting: 
        2023-10-22T10:22:42.206+08:00: /sys/kernel/mm/transparent_hugepage/enabled is 'always'. We suggest setting it to 'never'
        2023-10-22T10:22:42.206+08:00: /sys/kernel/mm/transparent_hugepage/defrag is 'always'. We suggest setting it to 'never'
---
MongoDB Enterprise > db.adminCommand( { logRotate: "audit", comment: "Rotating audit log" } )
{ "ok" : 1 }
MongoDB Enterprise > exit
bye
```
Now check the result of your auditLog.json file in the path /var/lib/mongo
```
-rw------- 1 mongod mongod 67126 Oct 22 11:19 auditLog.json.2023-10-22T03-19-40
-rw------- 1 mongod mongod     0 Oct 22 11:19 auditLog.json
```
#### rsyslog configuration

refer to the link: <https://docs.imperva.com/bundle/onboarding-databases-to-sonar-reference-guide/page/212012395.html>

```
sudo vi /etc/rsyslog.d/mongodb_audit_forward.conf
```
Add the following lines, and replace the necessary parameter values:

- **target** - the Agentless Gateway host IP 
- **File** - the full path of audit log file, could be "/var/lib/mongo/auditLog.json"
- If you are not using the default Server Port, replace "**27017**" in the code below with your port value.
```
$MaxMessageSize 18000000
 
module(load="imfile")
input(type="imfile" Tag="jsonaraudit:" File="<audit log path>/auditLog.json" ruleset="pRuleset")
  
template(name="mongo_rawmsg" type="list") {
    constant(value="{ ")
    constant(value="\"Server Port\":\"27017\"")
    constant(value=" }")
    constant(value="PR3N0RM")     
    property(name="rawmsg")
}
  
ruleset(name="pRuleset") {
action(type="omfwd"
     keepalive="on"
     protocol="tcp"
     target="<Agentless-Gateway-IP>"
     port="10501"
     template="mongo_rawmsg")
stop
}
```

Run this command to verify the rsyslog configuration file is correct:
```
rsyslogd -N1
```
Restart the rsyslog service:
```
sudo systemctl restart rsyslog
```
#### Onboarding to DSFHub

Import assets using spreadsheet, the hard way.

log viewer in actions.log
![](_attachments/Pasted%20image%2020231022142043.png)
key words to filter the logs: import_actions
![](_attachments/Pasted%20image%2020231022142159.png)

Enable the audit
- USC
- ![](_attachments/Pasted%20image%2020231022142458.png)
- or through 'asset details', click 'Connect Gateway' then check the playbook history in the pop-up windows.
- ![](_attachments/Pasted%20image%2020231022142712.png)
- ![](_attachments/Pasted%20image%2020231022142842.png)
Now generate some traffic using `mongo`:
```
MongoDB Enterprise > show dbs
```

Use `tcpdump` to trace connection
```bash
[root@mvp ~]# tcpdump -i any port 10501 and host 192.168.43.67 -nn
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on any, link-type LINUX_SLL (Linux cooked), capture size 262144 bytes
14:33:17.556176 IP 192.168.43.72.53020 > 192.168.43.67.10501: Flags [P.], seq 2630361006:2630361620, ack 1121687932, win 229, options [nop,nop,TS val 452446545 ecr 2541268608], length 614
14:33:17.556259 IP 192.168.43.67.10501 > 192.168.43.72.53020: Flags [.], ack 614, win 246, options [nop,nop,TS val 2541328655 ecr 452446545], length 0
^C
2 packets captured
2 packets received by filter
0 packets dropped by kernel
```

Use the `nc` command to verify that the network connection is working.

```
[root@mvp ~]# nc -zv 192.168.43.67 8443
Ncat: Version 7.50 ( https://nmap.org/ncat )
Ncat: Connected to 192.168.43.67:8443.
Ncat: 0 bytes sent, 0 bytes received in 0.01 seconds.
```

And check the syslog folder, you should see the new log file
![](_attachments/Pasted%20image%2020231022144223.png)

### Oracle DB 19c

#### Install Oracle 19C
Download the preinstall rpm package

```bash
curl -o oracle-database-preinstall-19c-1.0-1.el7.x86_64.rpm https://yum.oracle.com/repo/OracleLinux/OL7/latest/x86_64/getPackage/oracle-database-preinstall-19c-1.0-1.el7.x86_64.rpm

## or use the rpm in s3 bucket
curl -o oracle-database-preinstall-19c-1.0-1.el7.x86_64.rpm http://impervaps.s3-website-ap-southeast-1.amazonaws.com/ftp-downloads/training_installation_db/oracle_19c/oracle-database-preinstall-19c-1.0-1.el7.x86_64.rpm

sudo yum -y localinstall oracle-database-preinstall-19c-1.0-1.el7.x86_64.rpm
```

Refer to the official link to download the rpm or download directly from the s3 bucket.

```bash
curl -o oracle-database-ee-19c-1.0-1.x86_64.rpm http://impervaps.s3-website-ap-southeast-1.amazonaws.com/ftp-downloads/training_installation_db/oracle_19c/oracle-database-ee-19c-1.0-1.x86_64.rpm
sudo yum -y oracle-database-ee-19c-1.0-1.x86_64.rpm
```

Initiate the DB
```
[INFO] Oracle home installed successfully and ready to be configured.
To configure a sample Oracle Database you can execute the following service configuration script as root: /etc/init.d/oracledb_ORCLCDB-19c configure
```

Error you may encounter:
```
[root@dbs ~]#  /etc/init.d/oracledb_ORCLCDB-19c configure
Configuring Oracle Database ORCLCDB.
[FATAL] [DBT-06103] The port (1,521) is already in use.
   ACTION: Specify a free port.

Database configuration failed.
```

You need to add the ip/hostname in the /etc/hosts then run again above command to initiate.
```
192.168.43.70 dbs.hcl dbs
```

Now you will see logs similar to the ones below.
```
[ 2023-09-04 21:14:02.677 CST ] Database creation complete. For details check the logfiles at:
 /opt/oracle/cfgtoollogs/dbca/ORCLCDB.
Database Information:
Global Database Name:ORCLCDB
System Identifier(SID):ORCLCDB

Look at the log file "/opt/oracle/cfgtoollogs/dbca/ORCLCDB/ORCLCDB.log" for further details.

Database configuration completed successfully. The passwords were auto generated, you must change them by connecting to the database using 'sqlplus / as sysdba' as the oracle user.
```

Configure the bash_profile of oracle account
```
sudo su
su oracle
vi ~/.bash_profile
## append these lines to the profile:
export ORACLE_HOME=/opt/oracle/product/19c/dbhome_1
export PATH=$PATH:/opt/oracle/product/19c/dbhome_1/bin
export ORACLE_SID=ORCLCDB

## then source the profile
source ~/.bash_profile

## Create PLUGGABLE DATABASE

## Create the pdb file named playground
mkdir /opt/oracle/oradata/ORCLCDB/playground
## (optional) check the ownership of this folder. make sure it's oracle.oinstall
chown oracle.oinstall opt/oracle/oradata/ORCLCDB/playground

## running the sqlplus
[oracle@dbs ~]$ sqlplus /nolog

SQL*Plus: Release 19.0.0.0.0 - Production on Mon Oct 23 08:43:00 2023
Version 19.3.0.0.0

Copyright (c) 1982, 2019, Oracle.  All rights reserved.

SQL> conn sys/sys as sysdba
Connected to an idle instance.
SQL> startup

## update the password
SQL> alter user system identified by system; 
```

Create PDBs, make sure we are in CDB$ROOT instance.
```
SQL> show con_name

CON_NAME
------------------------------
CDB$ROOT

## Create PDB using the pdbseed file and create the ADMIN USER superdemo

SQL> CREATE PLUGGABLE DATABASE playground ADMIN USER superdemo IDENTIFIED BY superdemo FILE_NAME_CONVERT = ('/opt/oracle/oradata/ORCLCDB/pdbseed/', '/opt/oracle/oradata/ORCLCDB/playground/');

## Show connection and change it to PDB playground

SQL> alter session set container=playground;
SQL> alter database open;
SQL> show pdbs;
    CON_ID CON_NAME           OPEN MODE  RESTRICTED
---------- ------------------------------ ---------- ----------
     4 PLAYGROUND             READ WRITE NO
```

Create sample table for query
```
GRANT CREATE TABLE TO superdemo;
ALTER USER superdemo QUOTA UNLIMITED ON system;

CREATE TABLE cities (
    city_id NUMBER PRIMARY KEY,
    city_name VARCHAR2(100)
);

INSERT INTO cities (city_id, city_name) VALUES (1, 'New York');
INSERT INTO cities (city_id, city_name) VALUES (2, 'London');
INSERT INTO cities (city_id, city_name) VALUES (3, 'Paris');
```

#### Audit Policy Configuration
Configure the audit policy and audit user 
```
SQL> ALTER SESSION SET CONTAINER = CDB$ROOT;
##Create Audit Pull User:
CREATE USER c##auditmanager IDENTIFIED BY "P@ssw0rd";
GRANT CREATE SESSION TO c##auditmanager;

GRANT AUDIT_VIEWER TO c##auditmanager;
ALTER USER c##auditmanager SET container_data=all CONTAINER = CURRENT;
GRANT SELECT ON sys.v_$pdbs TO c##auditmanager;
GRANT SELECT ON V_$DATABASE TO c##auditmanager;

##Create the Audit Management User

CREATE USER c##auditpolicy IDENTIFIED BY "P@ssw0rd";
GRANT CREATE SESSION TO c##auditpolicy;
GRANT AUDIT_ADMIN TO c##auditpolicy CONTAINER=ALL;

GRANT SELECT ON DBA_TABLES TO c##auditpolicy;
GRANT SELECT ON V_$DATABASE TO c##auditpolicy;
GRANT SELECT ON V_$PARAMETER to c##auditpolicy;
```

Check the auditing mode:
```
## True means 'pure unified', false means 'mixed mode'
SQL> SELECT VALUE FROM V$OPTION WHERE PARAMETER = 'Unified Auditing';

VALUE
----------------------------------------------------------------
FALSE

## we will configure the auditing to mixed mode. Run SQL command as sys.
SQL> ALTER SYSTEM SET audit_trail=DB,EXTENDED scope=SPFILE;
ALTER SYSTEM SET audit_sys_operations=true scope=SPFILE;
AUDIT CONTEXT NAMESPACE USERENV ATTRIBUTES db_name, service_name;
## restart the database
SHUTDOWN IMMEDIATE;
STARTUP;

## Show parameters to verify
SQL> SHOW PARAMETERS audit;
audit_trail     string DB, EXTENDED

SQL> show parameters audit_sys_operations;
NAME                     TYPE    VALUE
------------------------------------ ----------- ------------------------------
audit_sys_operations             boolean     TRUE
```

Configure the audit policy
run this SQL command in the specific PDB.
```
CREATE AUDIT POLICY all_actions_pol ACTIONS ALL ONLY TOPLEVEL;
AUDIT POLICY all_actions_pol;
SELECT POLICY_NAME FROM audit_unified_enabled_policies;
```

#### Onboarding the DB
Template or the USC.

Use 'asset details' to 'Connect Gateway' and select the =='audit_type'== to 'multi-unified'.
Check the log files:
```
tail -f -n 200 /opt/jsonar/logs/gateway/cloud/odbc/oracle_unified_multi/sonargateway.log
```

Possible Error message:
2023-10-23 07:40:20,724 ERROR Asset sh-training-oracle-19c:mff3080:1521: source UNIFIED_AUDIT_TRAIL_0: ODBC query exception: std::exception

Check the service name, should be the $root, i.e. orclcdb.
## Pipelines

target?

```
## find the bind_ip
[root@dsfhub413 ~]# locate sonard.conf
/opt/jsonar/apps/4.13.0.10.0/etc/sonar/sonard.conf
/opt/jsonar/local/sonarw/sonard.conf
```

edit the one in sonarw
$JSONAR_LOCALDIR/sonarw
Add the bind_ip=0.0.0.0
then restart service
`systemctl restart sonard`

insert some data into mongodb
```
use training;
db.inventory.insertMany([
   { item: "journal", qty: 25, size: { h: 14, w: 21, uom: "cm" }, status: "A" },
   { item: "notebook", qty: 50, size: { h: 8.5, w: 11, uom: "in" }, status: "A" },
   { item: "paper", qty: 100, size: { h: 8.5, w: 11, uom: "in" }, status: "D" },
   { item: "planner", qty: 75, size: { h: 22.85, w: 30, uom: "cm" }, status: "D" },
   { item: "postcard", qty: 45, size: { h: 10, w: 15.25, uom: "cm" }, status: "A" }
]);
show dbs;
show collections;
## select the first 2 documents
db.inventory.find().limit(2).pretty()
{ _id: ObjectId("653741495a05e27d8fd07aa3"),
  item: 'journal',
  qty: 25,
  size: { h: 14, w: 21, uom: 'cm' },
  status: 'A' }
{ _id: ObjectId("653741495a05e27d8fd07aa4"),
  item: 'notebook',
  qty: 50,
  size: { h: 8.5, w: 11, uom: 'in' },
  status: 'A' }

## find/update/delete with condition

## delete with condition
condition: {item: "notebook"}
db.inventory.deleteOne({ item: "notebook" })

##If you intend to delete all documents matching the condition, you can use the deleteMany() method instead.
db.inventory.deleteMany({ status: "A" })

## Condition is case sensitive. so make no mistake when deleteMany().
db.inventory.deleteMany({status: "d"})
{ acknowledged: true, deletedCount: 0 }
db.inventory.deleteMany({status: "D"})
{ acknowledged: true, deletedCount: 2 }
```
add more operations to update:
```
## suppose you want to update the stauts:
db.inventory.updateOne({"item":"paper"}, {$set: {"status":"B"}})
```

more complex method:
```
db.inventory.find({ "status": { $in: ["A", "B"] } })
db.inventory.find({ "qty": { $gt: 75 } })
db.inventory.find({$or:[{"status":"B"},{"qty":{$lt:75}}]})
```

Now let's switch to the Sonar Hub Analyzer:
![](_attachments/Pasted%20image%2020231024150005.png)

How to practice?

Add stage : count
To count by ID:

Create pipeline:
start with match:
And more complex : add multiple $and condition to the pipeline.
```
$and: [{"Server Type" : {$ne: "ORACLE" }},{"Database Name" : "admin"}] 
```

Period Start
Between, it only affects the first date. Edit the second date to make a change.

Or the relative to when the query runs

```
## You may want the query to filter based on dates relative to when the query runs
$$LMRM_NOW for the time the query runs
$$LMRM_NOW-10DAYS  + or – days (even if one day)
$$LMRM_NOW-60000 means 60 seconds (number is in milliseconds) 

"Period Start" : {$gt: "$$LMRM_NOW-5DAYS"}
1 Hour
{$gte: "$$LMRM_NOW-36000000"}
10 Minutes
{$gte: "$$LMRM_NOW-600000"}
```

## Workflow
