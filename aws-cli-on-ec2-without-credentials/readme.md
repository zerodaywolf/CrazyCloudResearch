**This is a study on why the aws cli works in an EC2 instance when there are no credentials configured.**

- Without an IAM role, an EC2 instance can fetch security credentials by querying `http://169.254.169.254/latest/meta-data/identity-credentials/ec2/security-credentials/ec2-instance`
- Without valid aws credentials in `~/.aws/credentials`, the aws cli works only when an IAM role is attached to the instance
- We used the command `aws sts get-caller-identity` for demo purposes

## Creating the IAM role
- Create IAM role with the AWS managed policy AmazonEC2ReadOnlyAccess
- Attach role to ec2 instance. (Instance > Actions > Security > Modify IAM Role)

## HTTP traffic analysis of `aws sts get-caller-identity`
Uses IMDSv2 flow to fetch temporary credentials
   
   1. A PUT is sent to fetch the ec2-metadata-token

   2. A GET is sent to /latest/meta-data/iam/security-credentials/ to check if an iam role is attached

   3. Then the next GET request fetches temp creds of that role

- Dump file: dump1-with_iam_role

## When no IAM role is attached to the instance
IMDSv2 flow fails when there is no IAM role. This is because, in the above flow the 2nd step fails when there is no IAM role attached. So its just because of how the aws cli is built

- Dump file: dump2-withoutiamrole
