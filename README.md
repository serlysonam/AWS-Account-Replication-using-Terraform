# AWS-Account-Replication-using-Terraform
AWS Account Replication for Persistent State

For persistence state we need to copy individual services.
 
To migrate compute resources that have a persistent state, we can use one of the following approaches.

 1) AWS Backup:

 AWS Backup is a fully managed data protection service that centralizes and automates data across AWS services, in the cloud. We can leverage cross-account management in AWS backup service.

Cross-account management feature manages, monitors, restores the backup, and copies jobs across AWS accounts.

For instance,
we have account A which is our source account and account B which is our target account.

In order to set up Cross-Account Management below steps needs to be followed:

 a)  In Account B, create an IAM role that Account A will assume to back up its resources.
 b)  Create a Backup Vault in Account B.
 c)  Allow Account A to Assume the Role in Account B
 d)  Set Up Backup Plan in Account A.
 e)  Perform Backup Jobs.
 f)  Once everything is set up, a backup job initiated from Account A will assume the role in Account B and back up the resources into the backup vault in Account B.
 g)  Once the backups are available in the target account, you can restore EC2 instances.

 

Amazon Machine Image (AMI) :
AMI provides the information required to launch an instance. Specify an AMI and then launch multiple instances from a single AMI with the same configuration.

   Steps:
   a) Create an AMI from our source account EC2 instance.
   b) Modify AMI permissions to allow sharing.
   c) Share associated snapshots.
   d) In the target AWS account, we need to copy the AMI.
   e) Launch the EC2 instance in the target account.

 

Migrating storage resources:

 There are different ways to copy data from one s3 accounts to another account.

Use CLI or browser to copy

Cross-account bulk transfer of files using Amazon S3 Batch Operations

Cross account replication.

 1) Use CLI or browser to copy

 a) In the source account, the S3 bucket owner needs to grant access to the target account. modify the bucket policy of the source bucket to allow the destination account to read objects from the source bucket.
 b) In the target account, the  role performing the copy needs permission to write objects to the target bucket, so add grants write permissions to a specific bucket in the target account's policy.
 c) Copy the object:
    aws s3 cp s3://source-bucket-name/object-key s3://target-bucket-name/object-key
    aws s3 sync s3://source-bucket-name/ s3://target-bucket-name/

Cross-account bulk transfer of files using Amazon S3 Batch Operations

 This is used for large-scale operations of bulk file transfers.

 a) set up bucket policies and IAM roles to allow source account to access and transfer files to destination account's bucket. to do that you need to change bucket policy of both accounts.
 b) Set Up the IAM Role for Batch Operations.
 c) now we must create a S3 Batch Operations job to copy files from the source bucket to the destination bucket.
 d) After setting everything up, execute the batch operation job. S3 Batch Operations will handle the transfer of objects from the source bucket to the destination bucket as defined.
e) We also can configure cross account replication if we want to sync both account's object after initial copy.

Cross account replication

we use when we want to sync one s3 bucket(or folder) with another bucket in different account. We will set up the replication from source and destination bucket and once first handshake is done it will start replicating data between different accounts.

In order to transfer data from EFS file system from one account to another we can use  AWS DataSync. AWS DataSync is an online transfer service that simplifies moving, copying, and synchronizing large amounts of data between on-premises storage systems and AWS storage services

Migrating database resources:

If we want to migrate database, we can use AWS Database Migration Service (AWS DMS) to replicate data from source account to destination AWS accounts. We can set up a DMS task for either one-time migration or on-going replication.
An on-going replication task keeps your source and target databases in sync. Once set up, the on-going replication task will continuously apply source changes to the target with minimal latency.

Steps:
       a) Set up database in both source and destination account.
       b) Then we need to Set Up DMS Replication Instance. Create an AWS DMS replication instance in Source account.
       c) To allow cross-account DMS replication, we need to configure the replication instance in source account to be able to access the target database in target account.
       d) Create proper I am role for both the accounts along with proper security group and subnet.
       e) Create Replication Endpoints and Replication Task.
       f) Start replication.

We also can use RDS Snapshots to create and share database backups across AWS accounts. We can the shared snapshots to launch new Amazon Relational Database Service (RDS) instances in the target account.
