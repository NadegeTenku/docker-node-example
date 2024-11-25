# swe-inventory-app-starter
Boilerplate code for Mod 3


to move a file from one folder to another
mv NadegeTenku.github.io /Users/maguina/Desktop/Perso/Portfolio 
—> mv repo /path to destination do pwd first to get the right destination folder path

to copy/download a folder from the s3 bucket into the pwd
aws s3 cp s3://pm-vvc-eks-config-test/vz-vvc-eks-sysdig-manifests-us2-v2/ . --recursive

to copy/download an object/file from Amazon S3 to your instance 
aws s3 cp s3://pm-vvc-eks-config-dev/vz-vcc-eks-sysdig-manifests-v2/vcc-eks-us2-sana-se.yaml vcc-eks-us2-sana-se.yaml 
—> aws s3 cp s3://my_bucket/my_folder/my_file.ext my_copied_file.ext

scp is another command to copy a file from desktop to a server (ec2 instance). scp will not work if you are not connected to the server via ssh (e.g ssh -i "virginia-sbx-kp.pem" ubuntu@ec2-34-207-95-32.compute-1.amazonaws.com). You must have the pem key downloaded on your system to be able to ssh into the server. scp and ssh access work together. 

to list all s3 buckets from the aws console
aws s3 ls

to list files in an s3 bucket from the aws console
aws s3 ls s3://<bucketname>
aws s3 ls s3://vvc-sftp-prod-us-east-1

to copy a file into your present directory from the s3 bucket
aws s3 cp s3://<bucketname>/<filename> .
aws s3 cp s3://vvc-sftp-prod-us-east-1/PROD_VERIZON_PUSH_NOTIFICATION.DAT.txt .

to deploy sysdig manifest files
kubectl -n kube-system apply -f sa-cr.yaml

to create, name and update script
vi vvc-sysdig-script.sh

Get script from https://github.com/kadkins-sysdig/sysdig-tools/blob/main/sysdig-gather-rca-data.sh
Change ns to appropriate ns used during installation and update kubectl -n ${NAMESPACE} -c sysdig cp ${pod}:/opt/draios/logs/draios.log ./draios.log

to execute bash script.sh
bash vvc-sysdig-script.sh

Request url from Kendall to upload the resulting tar.gz file so he can review the contents of the RCA file to validate that Sysdig is running as expected.
curl -s -S -X PUT --url "$SYSDIG_UPLOAD_URL" -H "Content-Disposition: attachment; filename=FILENAME" -T FILENAME

to test firewalls
curl -LO “https://download.sysdig.com/scanning/bin/sysdig-cli-scanner/$(curl -L -s https://download.sysdig.com/scanning/sysdig-cli-scanner/latest_version.txt)/linux/amd64/sysdig-cli-scanner”

to get only binary files with collector
grep -r collector .

to get only files with mentioned cluster name
grep -nr pm-vvc-eks

Recommended namespace is kube-system bc all the core components related to the infrastructure, security and monitoring reside within kube-system. We use one centralized ns that will allow anything running as root and regular account.

kubectl get events -n kube-system
./install
In vi, insert first, then #(to comment out) hit j (to go one line down) and  . (to redo on line below) e.g vi sa-cm.yaml   i # esc j.    esc:wq!


In vi, insert first, then x (to undo) hit k (to go one line up) and . (to redo on line above) e.g vi sa-cm.yaml  x k x


In vi, do i, then esc /? Find within vi / whatever you want to search e.g vi sa-cm.yaml   /proxy   then n to move  to the next


ctrl r for history shortcut on cli e.g ctrl r kubectl
kubectl logs sysdig-agent-x4czn -n kube-system | grep connection_manager

tar -cvzf vvc-prod-west-sysdig-manifests.gz ./vvc-prod-west-sysdig-manifests/ ———taking all the files from vvc-dev-sysdig-manifests directory and putting them in a file vvc… .gz. it compresses them up using the gz format into gz.tar file



ps -ef  —— to find tenable which is installed by default upon the creation of ec2 instances.      cloud strike & more
ps -e | grep falcon-sensor — to verify a particular running service

kubectl get deployment/isaac -n vvc-prod —————vvc-prod for prod env only
kubectl top pod -n  vvc-prod —  
systemctl status docker

OTHERS:
to list a specific file 
ls -al | grep deployment
to restart a specific file
kubectl rollout restart <filename> -n ns

to find specific processes
ps -ef | grep falcon*

to get word count per line
wc -l

to get pods with particular ns
kubectl get pod -n sysdig-agent

to get all pods on all namespaces
kubectl get pods -A
kubectl get pods -A -o wide

If Pod is in Creation state and want to check what it is doing
kubectl describe po podname -n fm-test

to get more details on a pod
kubectl describe po podname -n namespace

to watch pods/nodes
watch kubectl get pods -n kube-system
kubectl get pods -n kube-system -w

to find cluster ip address of k8s pod
kubectl get pod -n ns -o wide

to get the ip address of a pod
kubectl get po <pod-name> -o wide -n ns

to get a particular kind of pods
kubectl get po -n kube-system | grep fluentd

to get the target percentages of pods and the min and max pods count
kubectl get hpa -n ns

to get more details about the pod’s cpu, memory and count info
kubectl describe hpa -n ns

to get all running pods running in a particular node
kubectl get pods --all-namespaces -o wide | grep <node name>
kubectl get pods --all-namespaces -o wide | grep ip-172-21-56-114.ec2.internal

to get node
kubectl get node

to get more details on nodes
kubectl get no -o wide

To get details of nodes’ allocated resources, # of pods in it, capacity & more
kubectl describe node <nodeName> 
You can describe a pod to get the node name or get it from instances under Hostname type (IP name)


to get the cpu and memory allocation for each pod
cat the deployment yaml file

to get the cpu and memory usage for pods
kubectl top pods -n vvc-prod  — for the app ns
kubectl top pods -n kube-system  —for kube-system ns
kubectl top pods  — for default

Kubernetes creates it's own cgroup called kubernetes-pods which has a cpu.share value of 1024 (No limitations).


to get all namespaces
kubectl get ns or kubectl get namespace

to get namespace information
kubectl get namespaces --show-labels

to see everything within a particular namespace
kubectl get all -n <namespace>
kubectl get all -n kube-system

to get node group of a cluster
—cluster virginia-prod —profile sail-prod — region us-east-1

to get node group of a cluster
kubectl get service

to check the current context
kubectl config view

to recreate the config file since the ec2 instance has to connect to the cluster
aws eks --region us-east-1 update-kubeconfig --name pm-vvc-eks
mkdir ~/.kube
cd ~/.kube/config
Added new context arn:aws:eks:us-east-1:950010138774:cluster/pm-vvc-eks to /root/.kube/config

Export the config file with the right path 
export KUBECONFIG=/root/.kube/config

to get all/specific configmaps
kubectl get cm -A
Kubectl get cm -n kube-system

to get/describe detailed configmap info
kubectl get cm fluentd-config -n kube-system -o yaml
kubectl get cm sysdig-agent -n kube-system -o yaml

to edit any one of the configmaps or cm
kubectl edit cm
kubectl edit cm fluentd-config -n kube-system -o yaml
kubectl edit cm sysdig-agent -n kube-system -o yaml

to get all/specific daemonsets
kubectl get ds -A
Kubectl get ds -n kube-system

to get daemonset info
kubectl get ds -n kube-system
kubectl get ds fluentd -n kube-system -o yaml
kubectl get ds sysdig-agent -n kube-system -o yaml
kubectl -n kube-system get ds

to edit any one of the daemonsets
kubectl edit ds
kubectl edit ds fluentd -n kube-system
kubectl edit ds sysdig-agent -n kube-system

to restart the daemonset (mostly for elasticsearch, fluentd and kibana related stuff)
kubectl rollout restart ds <name> -n ns
kubectl rollout restart ds sysdig-agent -n kube-system
kubectl rollout restart ds fluentd -n kube-system


kubectl delete cm fluentd-config -n kube-system ; kubectl delete ds fluentd -n kube-system

kubectl apply -f fluentd-configmap.yaml ; kubectl apply -f fluentd-daemonset.yaml

to update the metrics server when code commit pipeline doesn’t update it
kubectl get deployment -A
kubectl edit deployment metrics-server -n kube-system
kubectl describe deployment metrics-server -n kube-system
kubectl get po -n kube-system
					OR
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/high-availability.yaml

to update the Isaac for things like CPU/memory
kubectl get deployment -A
kubectl edit deployment isaac -n vvc-prod
kubectl describe deployment isaac -n vvc-prod
kubectl get po -n k vvc-prod

to get priorityclasses
kubectl get priorityclass
kubectl apply -f priorityclass.yaml

to look for logs
Kubectl logs podname -n namespace
Kubectl logs -n namespace <nodeanalyzer>
kubectl logs sysdig-agent-hsm7s -n kube-system
kubectl logs deployment/isaac -n vvc-prod

to view particular logs / info of a pod by word
kubectl get logs podname -n ns -f | grep Error 
kubectl get logs podname -n ns -f | grep Warning
kubectl get logs podname -n ns -f | grep -i Error (case insensitive)
kubectl get po -n kube-system | grep fluentd
… | grep proxy

to view particular logs info of a pod by word and see count (double grep option)
kubectl logs po -n ns -f | grep “specific item" | wc -l

to look for rolling logs
Kubectl logs podname -n fm-test -f

to get logs for all deployment files of a particular application
Kubectl logs deployment/isaac -n vvc-prod (-f or | grep …)

to get info of an ingress-nginx file
kubectl get po -n ingress-nginx
ls -al | grep ingress

to list all helm files in a cluster
helm ls -n kube-system

to view ingress-nginx controller’s external IP and all the pods, ports, deployment apps, replicasets, job batches
Kubectl get all -n ingress-nginx
flunetd

to restart/redeploy ingress-nginx controller
kubectl apply -f nginx-ingress-controller.yaml

to get info of an ingress-nginx file
nslookup <External IP>

to look for ip addresses of an app
nslookup app name (ingest-us2-app.sysdig.com)

When you do nslookup, your machine will go from your wifi to contact your DNS server saying I need an IP ads for this domain. Do you have that record? The DNS may say, I don’t have that record but know another server which may have the record and so on until you find the DNS with the IP address/Record. Once they get it, they cache it.

kubectl get deployment -n ingress-nginx


kubectl get deployment ingress-nginx-controller -n ingress-nginx -o yaml 
kubectl edit deployment ingress-nginx-controller -n ingress-nginx -o yaml

kubectl get po -n ingress-nginx

kubectl get all -n ingress-nginx

kubectl rollout restart deployment ingress-nginx-controller -n ingress-nginx (restart nginx controller after updating the the version & image)

kubectl logs ingress-nginx-controller-55494cf6fc-vnhf5 -n ingress-nginx -f (tail logs of new pod)


to get the ip address of an app
nslookup svcs.verizon.com
nslookup http://svcs.verizon.com
nslookup api-isaac-health-west.svcs.verizon.com

to check the health of an app
curl https://api-isaac-health-west.svcs.verizon.com/health
curl https://api-isaac.svcs.verizon.com/health

Curl -kv www.google.com


The endpoint we are trying to hit for the SPC is isc-vasp.myvzw.com. The full endpoint is https://isc-vasp.myvzw.com:8220/authSubSSO/ISC
curl -v https://isc-vasp.myvzw.com

VVC is on PACT and not egress.

to know the lates supported API versions
kubectl api-versions

to apply the config
kubectl apply -f <filname.yaml>

to list the service
kubectl get svc -n fm-test

to list ingress
kubectl get ingress -n fm-test

to view configmap file
kubectl get configmap ns -n ns -o yaml

to get the config file details including the cluster name and namespace
kubectl config view

to view the cluster you are pointing to (verify if you are at the right place)
kubectl config get-contexts
kubectl config current-context

to get the current cluster info ONLY not the cluster name
kubectl cluster-info

to switch context
kubectl cluster use-context <cluster’s arn or name of context>
kubectl config use-context arn:aws:eks:us-east-1:767693278295:cluster/virginia-prod

to get the deployment files
kubectl get deployment -n vvc-dev

to read the deployment file
kubectl get deployment isaac -n vvc-dev -o yaml

to display information about the currently running processes on a Linux system. It shows the PID, PPID, EUID, RUID, command name and cmd line arguments used to start the process
ps -ef
Htop / top

to check for connectivity 
nc -vz google.com 443
nc -vz ip adr port


to check for connectivity to the splunk server
nc -vz dallvsplcm01sp.tpd-soe.net 8089
Ncat: Version 7.50 ( https://nmap.org/ncat )
Ncat: Connected to 10.160.0.176:8089.
Ncat: 0 bytes sent, 0 bytes received in 0.31 seconds.

nc -vz deployment.splunk.aws.vz-connect.net 8089
Ncat: Version 7.50 ( https://nmap.org/ncat )
Ncat: Connected to 10.56.34.53:8089.
Ncat: 0 bytes sent, 0 bytes received in 0.14 seconds.


to install netcat (nc) 
sudo yum install nc

to check if I have authorization to everything with my pwd
kubectl auth can-i "*" “*"

to verify and updated the role groups for authentication 
kubectl get cm aws-auth -n kube-system -o yaml

kubectl edit cm aws-auth -n kube-system

to change .txt to .sh
mv install.txt install.sh

to make a script executable
chmod +x vvc-sysdig-script.sh

to run a script or file 
chmod +x vvc-sysdig-script.sh
./vvc-sysdig-script.sh
./install.sh

to change ownership from one place to another 
chmod -R td-agent:td-agent /demologs
(<filename>:<filename> into <destination directory>)

to view first 10/20 lines of a file 
cat vvc-sysdig-script.sh | less


to list files according to creation date/time
ls -lrt

Command to copy a folder from aws s3 to ec2 instance
aws s3 cp s3://pm-vvc-eks-config-prod-east/pm-vvc-eks-prod-east-26704-i2rv-2023-04-06/ /home/ssm-user/vvc/kubernetes --recursive
to search by a specific word
grep -i proxy sa*.yaml

to add data to an existing file
history >> myhistory.txt

to move all files into a directory 
mv vcc-eks*.yaml old-manifest-files/
mv sa*.yaml vvc-prod-west-sysdig-manifests


to run .sh, log into bash shell 
sudo bash

sudo commands
sudo -i brings you to an interactive session as root. su means to switch to a particular user. Just typing su switches to the root user.
sudo su -
sudo -I

grep -nr vvc-int-ex .

to list all files with ingress 
ls -al | grep ingress

to view ingress-nginx controller’s external IP and replicas  
kubectl get all -n ingress-nginx


to view destination ip addresses  
nslookup <EXTERNAL-IP>

to view all logs of the pods at the same time  
kubectl logs deployment/isaac -n vvc-prod -f

to view logs nonstop as traffic is coming in  
kubectl logs sysdig-agent-6m9m4 -n sysdig-agent -f   — helps to differentiate active from Passive region

to view logs
kubectl logs sysdig-agent-6m9m4 -n sysdig-agent

Restart the Isaac server or any particular server or restart all the pods at once
kubectl rollout restart deployment/isaac -n vvc-prod
kubectl rollout restart deployment/vvc-be -n vvc-prod

to check health status and deployed version of the container
kubectl exec -it <podname> -n vvc-prod -- curl localhost:3000/health

to check container for proxy and more
kubectl exec -it <podname> -n <ns> -- env

to set, unset and check proxy
export HTTP_PROXY=http://vzproxy.verizon.com:80
export =HTTPS_PROXY=http://vzproxy.verizon.com:80

unset HTTP_PROXY
unset HTTPS_PROXY

echo $HTTP_PROXY
echo $HTTPS_PROXY

to log into container
kubectl exec -it <podname> -n <ns> -- /bin/bash
ls -al

to log view latest log
kubectl describe po <podname> -n ns
Get the ip address of the pod
Log into the node
sudo -i
docker ps -a | grep isaac
ls -lrt  
cd /var/log/containers/ 
ls -lrt
cd /var/log/pods/vvc-test_isaac-f976c765d-cv7nm_915b47aa-9e4e-4848-9093-129745f4e4ba/isaac/ 
ls -lrt  
cd /var/lib/docker/containers/e5edf77da3c104cb87209f8b6797e812a9135fc955209e4eeea884cb831ba6ec/ 
ls -lrt  
tail e5edf77da3c104cb87209f8b6797e812a9135fc955209e4eeea884cb831ba6ec-json.log
tail e5edf77da3c104cb87209f8b6797e812a9135fc955209e4eeea884cb831ba6ec-json.log -f


to check all the ports the server is listening on
lsof -i -P -n | grep LISTEN


to switch java version
alternatives --config java
sudo alternatives --config java
update-alternatives --config java


to install nodejs & npm and check their versions
sudo yum update
yum remove libuv -y
sudo yum install -y nodejs
node -v
sudo yum install npm
npm -v


to get the ip address of the instance you are accessing
ifconfig
ifconfig | grep inet


to copy a folder one level up (cd into the source directory then cp recursively the folder you want to move into the destination directory including the path. You can delete the other folder if not needed anymore)
cp -r source_folder /path/to/destination_folder/


to move a folder one level up 
mv source_folder /path/to/destination_folder/


to find a word when you do not know exactly where it is located in a directory
grep -ir incomingRequest


to change bitbucket url from https to ssh
Log into the local terminal
cd ~/.ssh —> ls —> cat id_rsa.pub —> paste pub key in bitbucket access key section
cat /Users/maguina/.ssh/id_rsa (to view the private key)
                                             OR
cd ~/.ssh  OR cd /Users/maguina/.ssh/ (to access the ssh key)
ssh-keygen -t rsa (to create new authentication key pairs for SSH) —> ls 
cat id_rsa.pub —> vi ~/.ssh/config (to verify the config file) —> chmod 600 ~/.ssh/config —> chmod 400 ~/.ssh/id_rsa —> cat id_rsa.pub 

to generate a new csr for digicert renewal
I do not necessarily need to recreate the entire csr, private key and config files. I can simply use the existing 
./gencert.sh <domain name> (a new csr, private key and config will be created)
./gencert.sh vvc-kibana-test-east.svcs.verizon.com
Do you wish to encrypt private key? (y/n)n
Do you wish to view request? (y/n)y

to check expired certs
openssl x509 -noout -text -in <cert> | grep Not
openssl x509 -noout -text -in api-vvc.stg.svcs.verizon.com.crt | grep Not
openssl x509 -noout -text -in vvc-apigee.ebiz.verizon.com.crt | grep Not


to check secrets
Kubectl get secret -n ns


to recreate tls secrets
Client Secret
kubectl create secret generic client-secret --from-file=tls-secret=vvc-apigee-stg.ebiz.verizon.com.crt --from-file=tls.key=vvc-apigee-stg.ebiz.verizon.com.key --from-file=ca.crt=vvc-apigee-stg.ebiz.verizon.com.ca.crt -n vvc-preprod

Server Secret
kubectl create secret tls tls-secret --key api-vvc.stg.svcs.verizon.com.key --cert api-vvc.stg.svcs.verizon.com.crt -n vvc-preprod

kubectl create secret tls tls-secret --key api-vvc.stg.west.svcs.verizon.com.key --cert api-vvc.stg.west.svcs.verizon.com.crt -n vvc-preprod


to list files according to creation date/time
ls -lrt

to view Buffer and Cache Usage
free -m

to clear/free up PageCache
echo 1 > /proc/sys/vm/drop_caches


to clear/free up Dentries and Inodes
echo 2 > /proc/sys/vm/drop_caches


to clear/free up PageCache, Dentries, and Inodes
echo 3 > /proc/sys/vm/drop_caches


to empty buffer and cache (chain command)
free && sync && echo 3 > /proc/sys/vm/drop_caches && free


The sudo is used to execute the command as a superuser.
The sync will flush the file system buffer.
The “;” semicolon is used to separate multiple commands on a single line.
The echo 3 > /proc/sys/vm/drop_caches command is used to drop the page cache, a temporary storage area for recently accessed files.
Note: The drop_caches file controls which type of cached data should be cleared and the values are as follows:
1 – Clears only the page cache.
2 – Clears dentries and inodes.
3 – Clears page cache, dentries, and inodes.





to delete all of particular kind of file
rm *.gz

to delete the config
kubectl delete -f filename.yaml

to delete po
kubectl delete po podname -n fm-test

to delete a group of pods
kubectl delete po -l app=splunk-otel-collector

to delete svc
kubectl delete svc servicename -n fm-test

to delete ingress
kubectl delete ingress ingressname -n fm-test

to delete/remove an empty file
rm -rf filename

to delete/remove a file
rm -rf filename

to delete namespace
kubectl delete ns <namespace>






/etc/init.d/jenkins status  —  to check the daemon files

systemctl status jenkins  —  to check if jenkins is installed and working

systemctl status jenkins_agent.service  —  

For backend (BE) to Production
kubectl get po -n vvc-prod — check pods to see their status
vi vvc-be-deployment.yaml  — change version/release number for deployment as per ticket/provided by Geeta, then 
kubectl apply -f vvc-be-deployment.yaml to deploy the change
kubectl get po -n vvc-prod — check pods until all are running again
kubectl logs <podname> -n vvc-prod -f (-f here means we are tailing the logs)
kubectl logs <podname> -n vvc-prod -f | grep -v health (to tail logs excluding health {-v = exclude})
kubectl logs <podname> -n vvc-prod -f | grep -i health (To tail logs excluding health {-i = include only})
kubectl logs deployment/isaac -n vvc-prod - check all pods at once
kubectl logs deployment/isaac -n vvc-prod -f | grep -i <MDN> (To check for logs (MDN) from any pod since traffic could be going to any of the containers)
kubectl exec -it <podname> -n <ns> -- /bin/bash (log into container)
curl localhost:3000/health (check localhost heath) - from within the container
kubectl exec -it <podname> -n vvc-prod -- curl localhost:3000/health (check localhost heath)

MUST FAIL OVER TRAFFIC and make sure traffic starts going to new active and stops on new passive before continuing with the deployment changes.

Send requests from various devices; ios and android. Check mdns(numbers)

Send out completion email at the end of the call.


For Config change only
First run s3 config job (VVC-Prod-S3-Config-Sync.deploy) with given branch (by dev)
kubectl rollout restart deployment/isaac -n vvc-prod — only restart deployment for config change. No need to change release number if not on the ticket.                     NO NEED TO FAIL OVER TRAFFIC BUT DO PASSIVE FIRST.
kubectl get po -n vvc-prod — check pods until all are running again
kubectl logs deployment/isaac -n vvc-prod -f - check all pods at once
kubectl logs deployment/isaac -n vvc-prod -f | grep -i <MDN> 
kubectl exec -it <podname> -n <ns> -- /bin/bash (log into container)
curl localhost:3000/health (check localhost heath) - from within the container

NO NEED TO FAIL OVER TRAFFIC

Verify changes on various devices; ios and android.

Send out completion email at the end of the call.


For both Config and BE
kubectl get po -n vvc-prod — check pods to see their status
First run s3 config job (s3-config-prd) with given branch (by dev)
Verify the s3 config bucket to see the updates that were pushed 
Go to eks-promotion-prd  —> build with parameters and select the right region and branch as per ticket/provided by Dev, then
kubectl get po -n vvc-prod — check pods to see their status of the new pods
kubectl exec -it <podname> -n <ns> -- /bin/bash (log into container)
curl localhost:3000/health (check localhost heath)
kubectl exec -it <podname> -n vvc-prod -- curl localhost:3000/health (check localhost heath)
