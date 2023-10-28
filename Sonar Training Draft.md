For Training Only! Not For Production!
Installation files download link
http://impervaps.s3-website-ap-southeast-1.amazonaws.com/ftp-downloads/
## Diagram
### AWS
![](_attachments/Pasted%20image%2020231017181757.png)
### Azure

### GCP


## Services

sonargd
sonard
## Onboarding
### GCP
#### 1-VM instance
- [ ] Machine Type is e2-standard-8
![width=400](_attachments/Pasted%20image%2020231020192929.png)

![](_attachments/Pasted%20image%2020231020193229.png)
![](_attachments/Pasted%20image%2020231020203916.png)

- [ ] Check the firewall that allow ==port 8443 & 22== open to your current IP.

```
## check your current IP address
curl ifconfig.me
```
#### GCP Services Accounts
> DSF supports the below authentication mechanisms to access Google Cloud resources:
- **Service Account:** If you opt to use "service_account" authentication mechanism, a JSON-formatted key file needs to be generated. This file holds the credentials that the Agentless Gateway uses to access logs from the Pub/Sub subscription. Ensure that the key file is copied to the Agentless Gateway host and it is owned by the "sonarw" user.  To create a service account key, please see [Creating and managing service account keys](https://cloud.google.com/iam/docs/creating-managing-service-account-keys). **A service account is identified by its email address, which is unique to the account.**
- **Default:** If you use "default" authentication mechanism, then attach the Service Account created above to the Google Cloud Agentless Gateway instance.

We choose the Service Account. This is widely used.
Make sure the following roles granted to the Service Account.

> 1 Create service account

![](_attachments/Pasted%20image%2020231028200702.png)
Skip 'Grant users access to this service account (optional)'
![](_attachments/Pasted%20image%2020231028200751.png)
You can check/edit the roles in the IAM.
![](_attachments/Pasted%20image%2020231021125754.png)

Next, save the key as json format
![](_attachments/Pasted%20image%2020231021131640.png)
And save it into the folder path:
```
/opt/jsonar/local/credentials/xxx.json
chmod 644 xxx.json
```
## Federation
Using any cloud to install one Sonar Hub(warehouse), and configure static public ip for the federation with agentless gateways.

### Download installation from FTP-Downloads

Ask admin to unblock the public access of the bucket, and the policy change to all accounts.

In terminal, run the command such as:
```
curl -o sonar4.13.tar.gz http://impervaps.s3-website-ap-southeast-1.amazonaws.com/ftp-downloads/jsonar-4.13.0.10.0.tar.gz

sudo tar -xvf sonar4.13.tar.gz -C /opt/
sudo chmod a+rx /opt/jsonar/
```
After downloading, block public access again and change the 'Principal' back to root.

Start the installation:
```sh
[impervaps_lake@instance-2 opt]$ sudo /opt/jsonar/apps/4.13.0.10.0/bin/sonarg-setup 

Enter the full path to the JSONAR_DATADIR directory.
**The sonarw user must be able to read and write to this directory
Path [/opt/jsonar/data]: 

Enter the full path to the JSONAR_LOGDIR directory.
**The sonarw user must be able to read and write to this directory
Path [/opt/jsonar/logs]: 

Enter the full path to the JSONAR_LOCALDIR directory.
**The sonarw user must be able to read and write to this directory
Path [/opt/jsonar/local]: 

Running sonarg-setup

Starting initial setup
To continue, please accept the end-user license agreement
--------------------------------------------------------------------------------------------------------------

PROGRAM LICENSE AGREEMENT

---
--------------------------------------------------------------------------------------------------------------

Please type "yes" or "no".
Accept end-user license agreement? [yes/no]: yes
EULA Accepted

Group sonar does not exist.

If you have a CENTRAL USERS/GROUP MANAGEMENT SYSTEM (like Active
Directory or NIS), say NO here, create the group sonar in
that system, then RUN this script AGAIN.

Do you want to create it LOCALLY? [y/N]: y
Adding system group: sonar

User sonarw does not exist.

If you have a CENTRAL USERS/GROUP MANAGEMENT SYSTEM (like Active
Directory or NIS), say NO here, create the user sonarw in
that system, then RUN this script AGAIN.

Do you want to create it LOCALLY? [y/N]: y
Adding system user: sonarw
Updating user profile /home/sonarw/.bash_profile...

User sonargd does not exist.

If you have a CENTRAL USERS/GROUP MANAGEMENT SYSTEM (like Active
Directory or NIS), say NO here, create the user sonargd in
that system, then RUN this script AGAIN.

Do you want to create it LOCALLY? [y/N]: y
Adding system user: sonargd

How would you like to enter the setup information?
    [1]: Enter data manually
    [2]: Use an encrypted file
    [3]: exit
Choice: (1, 2, 3): 1
Entering data manually

Initial Setup
=============

Enter a concise, descriptive, and unique display name for this machine.
Display name [instance-2]: dsfgw413-gcp
Is this a remote machine for a federated gateway system? [y/N]: y

Enter the IP Address or FQDN of this host. Sonar uses this IP/FQDN to provide access to reports that users may receive as email links.
IP address or FQDN [34.170.16.34.bc.googleusercontent.com]: dsfgw413-gcp.impervaps.com

Enter the IP Address or FQDN of this host. Sonar uses this IP/FQDN for communication between the DSF Hub and Agentless Gateways.
IP address or FQDN [34.170.16.34.bc.googleusercontent.com]: dsfgw413-gcp.impervaps.com

SonarW Setup
============
Passwords must be at least 6 characters in length, include a combination of letters, digits, and symbols, and not be based on a dictionary word or be otherwise trivial to guess.

Enter the password for SonarW's admin user.
Password for admin user (will not be shown): 
Repeat for confirmation: 

Enter the password for SonarW's sonargd user (used for the ETL process).
Password for sonargd (will not be shown): 
Repeat for confirmation: 

Enter the password for the secAdmin user.
Password for secAdmin (will not be shown): 
Repeat for confirmation: 

Enter the password for the default SonarG user, sonarg_user (used for DSF Portal access).
Password for sonarg_user (will not be shown): 
Repeat for confirmation: 

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

Review Configurations
=====================

Please review the input values before accepting the following configuration:
    -Passwords and keys will be shown as '*****'.
    -Fields left blank will be shown blank.

 [0].JSONAR_UID_DISPLAY_NAME       dsfgw413-gcp                                      
 [1].REMOTE_MACHINE                True                                              
 [2].IP                            dsfgw413-gcp.impervaps.com                        
 [3].INSTANCE_IP_OR_DNS            dsfgw413-gcp.impervaps.com                        
 [4].NEWADMIN_PASS                 *****                                             
 [5].SONARGD_PASS                  *****                                             
 [6].SECADMIN_PASS                 *****                                             
 [7].SONARG_PASS                   *****                                             
 [8].ADMIN_EMAIL                                                                     
 [9].FROM_EMAIL                                                                      
[10].SMTP_HOST                                                                       
[11].SMTP_PORT                                                                       
[12].SMTP_SSL                      True                                              
[13].SMTP_USER                                                                       
[14].SMTP_PASS                                                                       
[15].PRODUCT                       data-security-fabric                              
[16].JSONAR_UID                    b7ddf758-f1fd-42e2-939b-4d611ca109e8              
[17].SONARD_IS_PRIMARY_PORT        3030                                              
[18].SONARD_BSON_SORT              ${JSONAR_DATADIR}/sonarw/tmp                      
[19].SONARD_EXTERNAL_SORT          ${JSONAR_DATADIR}/sonarw/tmp                      
[20].REPORTS                       ${JSONAR_DATADIR}/sonarfinder/reports             
[21].SONARK_EXPORT                 ${JSONAR_DATADIR}/sonark/tmp/export               

Please enter one of the following:
    -the number of the configuration value you would like to change (example: "2")
    -"continue" if you are satisfied with the configuration values
    -"exit" to quit without saving
Choice: continue

Please choose one of the following options for how to proceed:
    [1] Run data-security-fabric setup without saving a configuration file (Default)
    [2] Save and encrypt a configuration file then run data-security-fabric setup
    [3] Save and encrypt a configuration file then exit without running data-security-fabric setup
    [4] Quit
Option: (1, 2, 3, 4): 1

Installing data-security-fabric
==============================================================================================================

After confirming, you cannot make configuration changes by re-running this script. To make configuration changes, consult the Sonar documentation.
Confirm and configure system? [yes/no]: yes

----
Importing jSonar public key to keyring
Initial configuration completed. See /opt/jsonar/logs/sonar-setup-log for details
```

Using this command to test it's working on port 8443
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

Stop the sonar services before shutdown:
```
sudo /opt/jsonar/apps/4.13.0.10.0/bin/sonarg-setup services stop
```

Make sure you can visit it through public ip/ DNS hostname of the **agentless-gw** we just installed.
![](_attachments/Pasted%20image%2020231020204354.png)

**Conduct the same process in aws/azure.**
## Templates
### IAM

#### Service Endpoints
<https://docs.aws.amazon.com/general/latest/gr/cwl_region.html>


## On-Prem

## Dashboard

## Playbook

## MongoDB

### Install MongoDB Enterprise version 4.4
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
### rsyslog configuration

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
### Onboarding to DSFHub

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

## Oracle DB 19c

the installation files you can find here:

installation steps
download the preinstall rpm package

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

configure the bash_profile of oracle account
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

**Onboarding an Oracle Multitenant Container Database**
And configure the audit policy and audit user 
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

Onboarding the oracle
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

