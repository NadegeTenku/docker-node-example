# swe-inventory-app-starter
Boilerplate code for Mod 3


 SFTP

ssh-keygen -t rsa

Beehive for testing — cd /root/.ssh to get the keys

/Users/maguina/kstore/sftp-vvc-test

/Users/maguina/kstore ——— for ssh keys (sftp-vvc-test & sftp-vvc-test.pub)

sftp -i <sftp private key> <sftp user name>@<sftp endpoint>

sftp -i sftp-vvc-non-prod sftp-vvc-dev@s-f4f5072a2c294e8da.server.transfer.us-east-1.amazonaws.com (.server.transfer.<region>.amazonaws.com is the standard endpoint for sftp)

sftp -i sftp-vvc-non-prod sftp-vvc-test@s-08cf82c4c47643078.server.transfer.us-east-1.amazonaws.com


Bastion —> Created a tmp-ssh file in the root directory (/root/tmp-ssh). Generated the key (ssh-keygen —> /root/tmp-ssh/id_rsa —>).

Added the pub key to the sftp user. 

Edited the IAM role policy and sftp user policy as well.

Files can be pushed from MFT to SFTP s3 bucket.

Test login from bastion host: sftp -i id_rsa vvc-sftp-us-east-1@vpce-0008ce58a3f5c4107-nwcerfec.vpce-svc-0817350401a009f94.us-east-1.vpce.amazonaws.com   

pwd —> cd into folder —> ls



1- Add role for s3 to sftp (https://stash.verizon.com/projects/VZCIN/repos/aws.stacks.vvc/browse/iam/stacks/dev/infra/role_policies_asso.yml)

2- Create sftp role and policy. Deploy via jenkins pipeline VVC_AWS_SERVICES —> deploy-iam (us-east-2 since IAM is global)

3- Create and deploy cfn template (https://stash.../cft/sftp). The template will create the sftp server (vpc hosted with intra subnets & default SG), server user and s3 bucket. 

Jenkins: Go to job —> build with param

AWS_ACCOUNT (vvc-dev) + REGION (us-east-1) + CFT_STACK_NAME (aws-transfer-sftp) + BRANCH_NAME (master) + PRODUCT (VVC) + CF_TEMPLATE_PATH (cft/sftp/aws-transfer-sftp-dev.yaml) + DELETE_STACK (false) + DEPLOY_ENV (dev)

vvc-dev (dev)  vvc-qat (qat)  vvc-stg (stg)  vvc-prd (prod)

4- sftp user —> user configuration —> edit —> Select a policy from IAM —> save

5- Add SSH pub key(s) - my pub key and /or mft pub key

6- Under the VPC security group in the inbound rule, verify/add the ssh/22 rule and add this IP address (159.67.70.0/23) which Connects MFT to SFTP over ssh/22. It is the same for all the environments. 

7- Send the domain name (DNS) to MFT to test connectivity between the MFT server and the SFTP server.

8- Send the sftp user name and s3 bucket name to MFT to send test files to the bucket through sftp.



Send test file to sftp s3 bucket: 
sftp> put /Users/maguina/test.txt 
sftp> ls -l



MongoDB

sudo bash —> cd into right repo —> /home/ssm-user —> curl --output <FILE> mongoldb url (curl --output mongosh-1.10.6-linux-x64.tgz https://downloads.mongodb.com/compass/mongosh-1.10.6-linux-x64.tgz) —> ls —> unzip file (tar -xvzf mongosh-1.10.6-linux-x64.tgz) —> ls —> cd mongosh-1.10.6-linux-x64 —> cd bin —> ls —> chmod +x mongosh && chmod +x mongosh_crypt_v1.so —> cd out back to ssm-user.



Login: connect to mongoldb backend

cd /home/ssm-user/

ls -al

…
-rw-r--r--. 1 ssm-user ssm-user      193 Jul 15  2020 .bash_profile
-rw-r--r--. 1 ssm-user ssm-user      231 Jul 15  2020 .bashrc
drwxr-xr-x. 3 ec2-user ec2-user      130 Aug 24 16:41 mongosh-1.10.6-linux-x64
-rw-r-----. 1 root     root     66220709 Aug 24 18:14 mongosh-1.10.6-linux-x64.tgz
drwxr-xr-x. 4 ssm-user ssm-user       48 Aug  9 02:10 vvc

Go to mongodb —> database —> connect —> Private Endpoint —> shell —> It should be on ‘I don't have the MongoDB Shell installed’ —>  select OS (amazon linux 2) —> copy cmd line string —> mongosh-1.10.6-linux-x64/bin/ADD HERE (mongosh "mongodb+srv://prod-pl-0.l94e8.mongodb.net/" --apiVersion 1 --username vvcprodadmin) i.e (mongosh-1.10.6-linux-x64/bin/mongosh "mongodb+srv://prod-pl-0.l94e8.mongodb.net/" --apiVersion 1 --username vvcprodadmin)




Atlas atlas-pt8xa2-shard-0 [primary] test> use vvc
switched to db vvc
Atlas atlas-pt8xa2-shard-0 [primary] vvc> show collections
devices
usersettings
Atlas atlas-pt8xa2-shard-0 [primary] vvc> use usersettings
switched to db usersettings


Kibana


Kibana is a visual interface tool that allows you to explore, visualize, and build a dashboard over the log data massed in Elasticsearch Clusters. Kibana is a data visualization and exploration tool used for log and time-series analytics, application monitoring, and operational intelligence use cases.

Give kibana access to new users via aws cognito



Opensearch is an internal tool. Its failure doesn't affect the application externally but be cautious about pushing pipelines to Prod without properly verifying the non-prod environments.



Fluentd is deployed with opensearch and it reports its logs to kibana. We are deploying our own fluentd containers that are connected to our opensearch to kibana.



Log into Kibana to verify logs
Go to Dev Tools —> Console to run some commands like:      Google to get codes

GET _cat/shards

GET _cat/Allocation?v (query to check kibana cluster disk space)-(info on shards, disk.indices, disk.used, disk.avail, disk.total disk.percent, host, ip, and node)

GET _cat/indices?v&s=index - (to view all indices/logs and other info for the field you choose)

GET isaac-2023.09.12/ _settings


GET _cluster/settings - (to see fields (default is 1000))
GET / _cluster/settings

GET isaac-2023.09.14/_settings - (to see the field limit - search total_fields to locate limit)

GET _search
{
  "query": {
    "exists": {
      "field": "body.Ajax"
    }
  }
} - (to get elaborate logs output for the application)

PUT isaac-2023.09.11/ _settings
{
	“index.mapping.tool_fields.limit”: 2000
} - (to increase field limit (get the API from the internet research))

DELETE /vvc-2022.*  ( logs that you want to delete)


When kibana no longer receives logs: Check the the disk space to delete the older logs/shards if need be. 
		Check and compare cm and ds to see if anything was reverted.


Shards: The shard is the unit at which Elasticsearch distributes data around the cluster. The speed at which Elasticsearch can move shards around when rebalancing data, e.g. following a failure, will depend on the size and number of shards as well as network and disk performance.


Fields limit is per day. The fields are renew each day so deleting older logs do not create space on the present day’s field. 
You can’t delete fields. Fields increase based on the logs they carry. Delete logs and the fields will reduce and your limit will not be reached soon.



Send logs to Daniel and tell him we can’t connect to the elasticsearch servers. Network unreachable 

 An index/indices is a whole name that we put to get or delete something e.g logs-2023.01.08 or logs-2023.*

Dev Tools

GET /_cat/indices
DELETE /isaac-2022.*
GET _cat/indices?v&s=index
GET _cluster/settings - to see fields limit (default is 1000)
PUT isaac-2023.09.11/ _settings
{
	“API”: 2000
} - to increase field limit

GET _search
{
  "query": {
    "exists": {
      "field": "body.Ajax"
    }
  }
}
DELETE /vvc-2023.03.*

Go to Open on the top right of kibana dashboard to look for Fluentd logs




Path to logs from eks nodes

Do describe pod for application pod (isaac with vvc ns)
Get the ip address of the pod on which the Isaac app is running on 
Log into this node
sudo -i
docker ps -a | grep isaac
ls -lrt  
cd /var/log/containers/                                                              
ls -lrt
cd /var/log/pods/vvc-test_isaac-f976c765d-cv7nm_915b47aa-9e4e-4848-9093-129745f4e4ba/isaac/ 
ls -lrt  
ls
cd /var/lib/docker/containers/e5edf77da3c104cb87209f8b6797e812a9135fc955209e4eeea884cb831ba6ec/ 
ls -lrt  
tail e5edf77da3c104cb87209f8b6797e812a9135fc955209e4eeea884cb831ba6ec-json.log
tail e5edf77da3c104cb87209f8b6797e812a9135fc955209e4eeea884cb831ba6ec-json.log -f


Location/Path = /var/log/containers/*.log

There is a symbolic link created under /var/log/containers. The application pod logs have a symbolic link under containers which in turn pushes reports to the containers. This is the right path to follow.


New Relic

New Relic is a Software as a Service offering that focuses on performance and availability monitoring. It uses a standardized Apdex (application performance index) score to set and rate application performance across the environment in a unified manner.

New Relic lets you check the transaction traces and logs. Both data are essential to understand the root cause of your app's performance issues. Traces and logs give you precise records of errors occurring on your application. It is difficult to fix performance issues without them due to a lack of information.

Users report that it is easy to expand and that there are no limitations to scalability. The solution has been used for both small and large projects, and there have not been any performance issues reported. The user base is not very large, but there are different teams who use New Relic in different ways.


Request new relic admin access from OKTA for the specified project e.g newrelic.vvc.adm.usr

A new relic yaml file was created under the aws.stacks.vvc repo https://stash.verizon.com/projects/VZCIN/repos/aws.stacks.vvc/browse/newrelic.yaml

A new relic yaml file was created under folder was created under helm-charts which has values.yaml, templates and more https://stash.verizon.com/projects/VZCIN/repos/aws.stacks.vvc/browse/helm_charts/vvc-newrelic

Create a NR namespace on the AWS Console: kubectl create namespace newrelic —> kubectl get ns —> run pipeline on Jenkins

Go to Cloudbees —> VVC_APPLICATIONS —> Deployment —> EKS-Deployment —> Build with Parameters —> EKS_SERVICE_NAME (vvc-newrelic or vvc-newrelic-prd) —> IMAGE_VERSION (1) —> ENV_NAME (dev, qat, stg, prd) —> REGION (us-east-1 & us-west-2) —> Build.
https://jenkins.inf.aws.tpd-soe.net/vvc/job/VVC_APPLICATIONS/job/Deployment/job/EKS-Deployment/
Running on vz-cbj-agents-vvc-v3rj3

 in /home/jenkins/agent/workspace/VVC_APPLICATIONS/Deployment/EKS-Deployment
 
Go to the new relic portal https://one.newrelic.com/ —> Kubernetes to see the available accounts —> go into one account 





Versions/Images updates


Check cluster name from the new relic portal and update the deployment.yaml, daemonset and configmap files accordingly.

https://stash.verizon.com/projects/VZCIN/repos/aws.stacks.vvc/browse/helm_charts/vvc-newrelic/templates/deployment.yaml x2

https://stash.verizon.com/projects/VZCIN/repos/aws.stacks.vvc/browse/helm_charts/vvc-newrelic/templates/daemonset.yaml x3

https://stash.verizon.com/projects/VZCIN/repos/aws.stacks.vvc/browse/helm_charts/vvc-newrelic/templates/configmap.yaml x5

Make sure to use the right cluster name.

pm-vvc-eks-dev,  pm-vvc-eks-qat,  pm-vvc-eks-stg-UE1,  pm-vvc-eks-stg-UW2

Run the pipeline with the respective env name and region.
https://jenkins.inf.aws.tpd-soe.net/vvc/job/VVC_APPLICATIONS/job/Deployment/job/EKS-Deployment/23/parameters/


aws login

C02F47DBMD6M:beehive maguina$ python3 aws_cli_sso.py

Enter your SSO login information:
----------------------------------------------------------------

Username: maguina	
Password: 
----------------------------------------------------------------


Your current proxy settings are....
----------------------------------------------------------------
HTTP_PROXY=
HTTPS_PROXY=
----------------------------------------------------------------


Please choose the role you would like to assume:
----------------------------------------------------------------
[ 0 ]:  arn:aws:iam::260047150861:role/Vz-SAML-NetworkAdministrator_R
[ 1 ]:  arn:aws:iam::260047150861:role/Vz-SAML-Administrator_R
[ 2 ]:  arn:aws:iam::260047150861:role/Vz-SAML-SUPERAdministrator_R
[ 3 ]:  arn:aws:iam::767693278295:role/Vz-SAML-SUPERAdministrator_R
[ 4 ]:  arn:aws:iam::...
[ 5 ]:  arn:aws:iam::...
[ 6 ]:  arn:aws:iam::...
[ 7 ]:  arn:aws:iam::...
Selection:  2
----------------------------------------------------------------


Detecting Operating System...
----------------------------------------------------------------
Using Mac/Linux Environment
----------------------------------------------------------------


Credentials stored
----------------------------------------------------------------
Your new access key pair has been stored in the AWS configuration file /Users/maguina/.aws/credentials under the saml profile.
Note that it will expire at 2024-06-26 17:40:27+00:00 (UTC).
After this time, you may safely rerun this script to refresh your access key pair.
To use this credential, call the AWS CLI with the --profile option (e.g. aws --profile saml ec2 describe-instances).
----------------------------------------------------------------


Testing credentials using STS get-caller-identity.....
----------------------------------------------------------------
UserId: ...
AccountId: ...
ARN: arn:aws:sts::...
----------------------------------------------------------------



—————————————————————————————————————————




cat ~/.aws/credentials
[357858158490_pact-sso-superadmin-preprod]
aws_access_key_id = ...
aws_secret_access_key = ...
aws_session_token = ...

[pstest]
aws_access_key_id = ...
aws_secret_access_key = ...

[saml]
output = json
region = us-east-1
aws_access_key_id = ...
aws_secret_access_key = ...
aws_session_token = ...
aws_security_token = ...
x_principal_arn = arn:aws:sts::...
x_security_token_expires = ...

[default]
aws_access_key_id = ...
aws_secret_access_key = ...


——————————————————————————————————————————



python3 aws_cli_sso.py --profile beehive-prod

aws --profile beehive-prod eks list-clusters

kubectl config get-contexts

kubectl get pods

aws --profile beehive-prod eks --region us-east-1 update-kubeconfig --name virginia-prod

aws --profile beehive-prod eks --region us-west-2 update-kubeconfig --name oregon-prod

aws --profile beehive-prod eks list-clusters

aws --profile beehive-prod eks list-clusters --region us-west-2

kubectl get deployment openid-springboot -o yaml

kubectl edit deployment openid-springboot -o yaml



To switch context and region
kubectl config use-context arn:aws:eks:us-west-2:767693278295:cluster/oregon-prod

kubectl config use-context arn:aws:eks:us-east-1:767693278295:cluster/virginia-prod
Switched to context "arn:aws:eks:us-east-1:767693278295:cluster/virginia-prod".


 which aws cli
  416  env
  417  pip3 install bs4
  418  pip3 install requests
  419  pip3 install boto3
  420  python
  421  aws_cli_sso.py
  422  ls
  423  ls -al
  424  echo $HTTP_PROXY
  425  echo $HTTPS_PROXY
  426  python3 aws_cli_sso.py --help
  427  vi aws_cli_sso.py
  428  python3 aws_cli_sso.py --help
  429  python3.9 aws_cli_sso.py --user jdoe1234 --profile saml-production
  430  python3.6 aws_cli_sso.py --user jdoe1234 --profile saml-production
  431  python3.6 aws_cli_sso.py
  432  python3.9 aws_cli_sso.py
  433  python3 aws_cli_sso.py
  434  python3 aws_cli_sso.py
  435  echo $HTTP_PROXY
  436  python3.6 aws_cli_sso.py
  437  python3.9 aws_cli_sso.py
  438  pip install boto3
  439  python3.9 aws_cli_sso.py
  440  python3.6 aws_cli_sso.py
  441  python3.9 aws_cli_sso.py --user jdoe1234 --profile saml-production
  442  python -V
  443  python -m pip install boto3
  444  python3.9 aws_cli_sso.py --user jdoe1234 --profile saml-production
  445  python3 aws_cli_sso.py --help
  446  python3 aws_cli_sso.py
  447  export HTTP_PROXY=http://vzproxy.verizon.com:80
  448  export HTTPS_PROXY=http://vzproxy.verizon.com:80
  449  python3 aws_cli_sso.py
  450  cat ~/.aws/credentials
  451  python3 aws_cli_sso.py
  452  cat ~/.aws/credentials
  453  aws --profile saml sts get-caller-identity
  454  which kubectl
  455  kubectl -V
  456  kubectl 
  457  python3 aws_cli_sso.py
  458  aws eks list-clusters
  459  ls -al
  460  aws --profile saml eks list-clusters
  461  python3 aws_cli_sso.py --prrofile beehive-prod
  462  python3 aws_cli_sso.py --profile beehive-prod
  463  python3 aws_cli_sso.py --profile beehive-prod
  464  unset HTTP_PROXY
  465  unset HTTPS_PROXY
  466  python3 aws_cli_sso.py --profile beehive-prod
  467  python3 aws_cli_sso.py --profile beehive-prod
  468  python3 aws_cli_sso.py --profile beehive-prod
  469  aws --profile beehive-prod eks list-clusters
  470  kubectl get context
  471  kubectl config get-contexts
  472  aws --profile beehive-prod eks --region us-east-1 update-kubeconfig --name virginia-prod
  473  ls -al
  474  kubectl config get-contexts
  475  kubectl get pods
  476  aws --profile beehive-prod eks list-clusters
  477  aws --profile beehive-prod eks list-clusters --region us-west-2
  478  aws --profile beehive-prod eks --region us-west-2 update-kubeconfig --name oregon-prod
  479  kubectl config get- contexts
  480  kubectl config get- context
  481  kubectl config get-context
  482  kubectl config get-contexts
  483  kubectl config use-context virginia-prod
  484  kubectl config use-context arn:aws:eks:us-east-1:767693278295:cluster/virginia-prod
  485  kubectl config get-contexts
  486  kubectl get deployment openid-springboot -o yaml
  487  python3 aws_cli_sso.py --profile beehive-prod
  488  kubectl config get-contexts
  489  aws --profile beehive-prod eks list-clusters
  490  kubectl get pods
  491  kubectl get pods
  492  kubectl get deployment openid-springboot -o yaml
  493  kubectl config use-context arn:aws:eks:us-west-2:767693278295:cluster/oregon-prod
  494  kubectl config get-contexts
  495  kubectl config use-context arn:aws:eks:us-east-1:767693278295:cluster/virginia-prod
  496  kubectl config get-contexts
  497  kubectl config current-context
  498  kubectl cluster-info
