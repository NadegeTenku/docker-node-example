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
