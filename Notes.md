## AWS CLI Installation

<details>
  <summary>Show</summary>

Verify that Python is installed by running the following command:

    python3 --version

Run the following command to install the unzip utility.

    sudo yum install -y unzip

Download and install AWS CLI :

    curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
    unzip awscliv2.zip
    sudo ./aws/install

Verify that AWS CLI is now working by running the following command:

    aws help

</details>

## AWS CLI Configure

<details>
  <summary>Show</summary>

Run the configuration command for AWS CLI as follows:

    aws configure

</details>

## S3 bucket

<details>
  <summary>Show</summary>

Create bucket:

    aws s3api create-bucket --bucket <BUCKET NAME> --region <REGION> --createbucket-configuration LocationConstraint=<REGION>

Edit bucket ownership controls:

    aws s3api put-bucket-ownership-controls \
    --bucket <BUCKET NAME> \
    --ownership-controls "Rules=[{ObjectOwnership=BucketOwnerPreferred}]"

Edit bucket ACL configuration:

    aws s3api put-public-access-block \
    --bucket <BUCKET NAME> \
    --public-access-block-configuration "BlockPublicAcls=false,IgnorePublicAcls=false,BlockPublicPolicy=false,RestrictPublicBuckets=false"

Set S3 bucket as static website:

    aws s3 website s3://<BUCKET NAME>/ --index-document index.html

Upload files to S3 bucket (from current working directory):

    aws s3 cp . s3://<BUCKET NAME>/ --recursive --acl public-read

List bucket files:

    aws s3 ls <BUCKET NAME>

Enable versioning:

    aws s3api put-bucket-versioning --bucket <BUCKET NAME> --versioning-configuration Status=Enabled

Synchronize (copy) files to S3 bucket:

    aws s3 sync <FILES> s3://<BUCKET NAME>/files/

List synchronized files:

    aws s3 ls s3://<BUCKET NAME>/files/

Remove file from local drive:

    rm <FILE>

Delete the same file from the server:

    aws s3 sync files s3://<BUCKET NAME>/files/ --delete

View past versions of the versioned file:

    aws s3api list-object-versions --bucket <BUCKET NAME> --prefix <FILEPATH/FILE>

Download previous version of the file:

    aws s3api get-object --bucket <BUCKET NAME> --key <FILEPATH/FILE> --version-id <VERSION ID> <FILEPATH/FILE>


</details>

## Batch file to update website

<details>
  <summary>Show</summary>

Change to home directory and create empty file:

    cd ~
    touch update-website.sh

Standard first line for bash:

    #!/bin/bash
    aws s3 cp ~/<FILEPATH>/<FILE> s3://<BUCKET NAME>/ --recursive --acl public-read

Make batch file executable:

    chmod +x update-website.sh

Run batch file:

    ./update-website.sh

</details>

## MISC

<details>
  <summary>Show</summary>

List IAM users:

    aws iam list-users

List information about EC2 instances:

    aws ec2 describe-instances

Unzip .tar.gz -file:

    tar xvzf <FILE NAME>.tar.gz

</details>
