> [!important]  
> Commandsterraform planpreview changes that Terraform plans to make to your infrastructureterraform applyexecutes the action proposed in the Terraform planterraform destroydestroys all remote objects managed by a Terraform Configuration  

  

> [!important]  
> Termsterraform configurationdocument that tells Terraform how to manage a given collection of infrastructurecan consist of multiple files most commonly: main.tf, locals.tf, variables.tf, outputs.tfmodulecollection of .tf files kept together in a directorye.g. AWS TF Backend module consisting of S3 bucket for tf.state file and DynamoDB table for state locking (optional)main.tfterraform {  
required_providers {  
aws = {  
source = "hashicorp/aws"  
version = "~> 4.67.0"  
}  
}  
}  
  
provider "aws" {  
region = var.region  
profile = var.aws_profile  
}  
  
resource "aws_s3_bucket" "tf_state_bucket" {  
bucket = var.bucket_name  
force_destroy = false  
tags = local.tags  
}  
  
resource "aws_s3_bucket_versioning" "terraform_state_bucket_versioning" {  
bucket = aws_s3_bucket.tf_state_bucket.id  
  
versioning_configuration {  
status = "Enabled"  
}  
}  
  
resource "aws_dynamodb_table" "tf_locks" {  
name = var.dynamodb_name  
billing_mode = "PAY_PER_REQUEST"  
hash_key = "LockID"  
tags = local.tags  
attribute {  
name = "LockID"  
type = "S"  
}  
}locals.tflocals {  
tags = {  
project = var.project_name  
}  
}outputs.tfoutput "bucket_name" {  
description = "S3 Bucket name for tf state file"  
value = aws_s3_bucket.tf_state_bucket.id  
}  
  
output "dynamodb_name" {  
description = "DynamoDB name for tf lockig"  
value = aws_dynamodb_table.tf_locks.id  
}  
variables.tfvariable "region" {  
description = "AWS Region"  
type = string  
default = "us-east-1"  
}  
  
variable "bucket_name" {  
description = "Terraform State Bucket Name"  
type = string  
}  
  
variable "dynamodb_name" {  
description = "Terraform Locking DB Name"  
type = string  
}  
  
variable "aws_profile" {  
description = "AWS Account to use"  
type = string  
default = "default"  
}  
  
variable "project_name" {  
description = "The project name that will be applied to tags"  
type = string  
}  
  
  
statea snapshot of the infrastructure that Terraform managesa file, the terraform.tfstate, stores information about the deployed resourcesit is a json documentmaps terraform configuration to real world resourcesstores metadata such as resource dependenciesperformance - terraform stores a cache of the attribute values for all resources so that instead of going to every provider and querying every resources to determine the diff between deployed resources and the current configuration, terraform simply uses the terraform.tfstate fileBackendlocal backendterraform stores the state file on your local file systemPros:Simple to set up and use, with no additional configuration required.Useful for small projects or individual developers.Cons:Not ideal for teams, as sharing state between multiple users requires additional processes.Vulnerable to loss if the local machine is compromised or the state file is accidentally deleted.remote backendthe state file is stored in a remote location, i.e. an S3 bucketPros:Facilitates collaboration among teams by allowing shared access to the state.Offers better security and reliability, with options for encryption, access control, and backups.Some remote backends offer locking mechanisms to prevent concurrent operations from corrupting the state.Cons:Requires additional configuration and possibly extra costs for the storage service.Introduces slight latency due to network communication when reading or writing the state.state lockinga mechanism designed to prevent multiple users or processes from making simultaneous updates to the Terraform state file  

  

> [!important]  
> Configuring a backendterraform uses the local backend by default → state files are stored on your local machineTo configure, add a backend block within the top level terraform blockterraform {  
backend "remote" {  
organization = "example_corp"  
  
workspaces {  
name = "my-app-prod"  
}  
}  
}Initializationrun terraform init when you change BE configurations to validate and configure before you can perform plan, apply, or state operationsterraform creates a .terraform/ local directory after initialization that contains the most recent BE configurationdo not check this directory into Git as it will contain sensitive auth credentials for the remote BEthe local BE config is separate from the terraform.tfstate file that contains state data about your real-world infraAWS BEuses S3 to store the terraform.tfstate fileuses DynamoDB for state locking (optional)example configterraform {  
backend "s3" {  
bucket = "mybucket"  
key = "path/to/my/key"  
region = "us-east-1"  
}  
}key = path where state data is stored in an S3 objectbucket = bucket nameS3 permissions: https://developer.hashicorp.com/terraform/language/settings/backends/s3\#s3-bucket-permissionsDynamoDB permissions: https://developer.hashicorp.com/terraform/language/settings/backends/s3\#dynamodb-table-permissions  

  

> [!important]  
> Terraform Bootstrapping StepsThese steps will guide you through setting up a remote Terraform backend in AWS. Initially, Terraform stores its state locally, but to improve state management and facilitate team collaboration, you’ll want to migrate this state to a remote backend. This process is often seen as a "chicken and egg" problem because you need the remote backend infrastructure (like an S3 bucket and DynamoDB table) to store your state, but Terraform manages infrastructure with a state file that doesn't yet exist in the remote backend.The first three steps outline how to create the necessary infrastructure using a local state file, and the final two steps demonstrate how to migrate this state to the newly created remote backend for future deployments.Create entry file to create a BE module:terraform {  
required_providers {  
aws = {  
source = "hashicorp/aws"  
version = "~> 4.67.0"  
}  
}  
}  
  
provider "aws" {  
region = "aws-region"profile = "your-aws-profile"  
}  
  
module "terraform-be" {  
source = "git::https://github.com/R-Ram95/terraform-modules//aws_terraform_be"region = "aws-region"aws_profile = "your-aws-profile"bucket_name = "tf-bucket-name"project_name = "my-project"# optionalenable_state_locking = true  
dynamodb_name = "tf-state-lock-name"  
}Initialize Terraform:Downloads the BE module from the remote source (this repo)Downloads the providerterraform initCreate the infrastructure:Optionally run terraform plan to verify the resources that will be created:terraform applyThis creates the S3 bucket and DynamoDB table (if you specified true to enable state locking in step 1) in your AWS account.Comment out or delete the terraform-be module block:Terraform will try to recreate the backend resources if you don't. Add an S3 backend block:bucket = name of the bucket from step 1dynamodb_table = name of the table from step 1terraform {  
required_providers {  
aws = {  
source = "hashicorp/aws"  
version = "~> 4.67.0"  
}  
}  
  
backend "s3" {  
bucket = "tf-bucket-name"key = "terraform.tfstate"region = "aws-region"dynamodb_table = "tf-state-lock-name"encrypt = true  
profile = "your-aws-profile"  
}  
}  
  
provider "aws" {  
region = "aws-region"profile = "your-aws-profile"  
}  
  
// COMMENT THIS OUT# module "terraform-be" {# source = "git::https://github.com/R-Ram95/terraform-modules//aws_terraform_be"# region = "aws-region"# aws_profile = "your-aws-profile"# bucket_name = "tf-bucket-name"# project_name = "project-name"# enable_state_locking = true# dynamodb_name = "tf-state-lock-name"# }Migrate local Terraform BE to the new remote Terraform BE hosted on AWS:terraform initEnter "yes" when prompted with: "Do you want to copy existing state to the new backend?"This step migrates the local terraform.tfstate file to the S3 bucket you created in step 3.
