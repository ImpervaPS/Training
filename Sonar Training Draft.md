For Training Only! Not For Production!
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
#### VM instance
- [ ] Machine Type is e2-standard-8
![](_attachments/Pasted%20image%2020231020192929.png | width=400)

![](_attachments/Pasted%20image%2020231020193229.png)
![](_attachments/Pasted%20image%2020231020203916.png)

- [ ] Check the firewall that allow ==port 8443 & 22== open to your IP.
#### GCP Services Accounts
> DSF supports the below authentication mechanisms to access Google Cloud resources:
- **Service Account:** If you opt to use "service_account" authentication mechanism, a JSON-formatted key file needs to be generated. This file holds the credentials that the Agentless Gateway uses to access logs from the Pub/Sub subscription. Ensure that the key file is copied to the Agentless Gateway host and it is owned by the "sonarw" user.  To create a service account key, please see [Creating and managing service account keys](https://cloud.google.com/iam/docs/creating-managing-service-account-keys). **A service account is identified by its email address, which is unique to the account.**
- **Default:** If you use "default" authentication mechanism, then attach the Service Account created above to the Google Cloud Agentless Gateway instance.

We choose the Service Account. This is widely used.
Make sure the following roles granted to the Service Account.
![](_attachments/Pasted%20image%2020231021113918.png)
You can check/edit the roles:
![](_attachments/Pasted%20image%2020231021125754.png)

Next, save the key as json format and place it into the folder:
![](_attachments/Pasted%20image%2020231021131640.png)
### FileDownload from FTP-Downloads

```
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Sid": "PublicReadGetObject",
			"Effect": "Allow",
			"Principal": {
				"AWS": "arn:aws:iam::56305927xxxx:root"
			},
			"Action": "s3:GetObject",
			"Resource": "arn:aws:s3:::impervaps/ftp-downloads/*"
		}
	]
}

```
Replace "AWS": "arn:aws:iam::56305927xxxx:root" with " * "

then in gcp, run the command such as:
```
curl -o sonar4.13.tar.gz http://impervaps.s3-website-ap-southeast-1.amazonaws.com/ftp-downloads/jsonar-4.13.0.10.0.tar.gz

sudo tar -xvf sonar4.13.tar.gz -C /opt/
sudo chmod a+rx /opt/jsonar/
```
after downloading, block public access again and change the 'Principal' back.

start the installation:
```
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
Configure the Firewall rules
```
sudo firewall-cmd --zone=public --add-port=8443/tcp --add-port=22/tcp --permanent
sudo firewall-cmd --reload
sudo firewall-cmd --zone=public --list-ports
```

Stop the sonar services before shutdown:
```
sudo /opt/jsonar/apps/4.13.0.10.0/bin/sonarg-setup services stop
```

Make sure you can visit it through public ip/ DNS hostname
![](_attachments/Pasted%20image%2020231020204354.png)
### Templates
### IAM

#### Service Endpoints
<https://docs.aws.amazon.com/general/latest/gr/cwl_region.html>


## On-Prem

## Dashboard

## Playbook

## MongoDB
## Pipelines
