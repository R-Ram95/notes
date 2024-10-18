---
tags:
  - Blog-Posts
---
Goal:

Write blog post about hosting a website with a custom domain using SSL/TLS (https)

  

### 1. **Introduction**

- **purpose of the blog post:**
    - deploy static files on S3, served with CloudFront using a custom domain from Route53
    - to make a setup that is re-useable and easy to set up
    - to learn how:
        - to set up a custom domain
        - to set up SSL/TLS for HTTPS
- **Costs associated with the setup:**
    - $14 dollars / year for the Domain name
    - CloudFront costs: - unless your site becomes one of the most popular sites in the world, the cost is negligible
        - 1 TB of data transfer out to the internet per month
        - 10,000,000 HTTP or HTTPS Requests per month
        - 2,000,000 CloudFront Function invocations per month
        - 2,000,000 CloudFront KeyValueStore reads per month
        - Free SSL certificates
        - No limitations, all features available
    - S3 Standard Costs:
        
        - based on object size and how long you store it ⇒ i.e. if you have five 1GB objects stored for a month, you would be charged 5GB * $0.023$/GB = $1.14/month
        - size of an FE bundle is on the order of magnitude of MB the cost is essentially 0
        
        |   |   |
        |---|---|
        |First 50 TB / Month|$0.023 per GB|
        |Next 450 TB / Month|$0.022 per GB|
        |Over 500 TB / Month|$0.021 per GB|
        
    - Route53:
        - $0.50 per hosted zone per month for the first 25 hosted zones
        - because we use a custom domain, we have 1 hosted zone so the cost is $0.5/month
    - ACM:
        - public SSL/TLS certs are free
    - Total cost = $20/year depending on the cost of your domain
- Tools + Technologies used:
    - AWS S3, CloudFront, Route 53, ACM, and Terraform.

### 2. **Setting Up the Infrastructure as Code (IaC) with Terraform**

- **Overview of Terraform Modules**:
    - Purpose of modularizing Terraform code:
        - reusable, maintainable, and testable code
        - creates an abstraction for deploying static sites to AWS ⇒ you don’t need to know that you need to set up an S3 bucket, add the necessary permissions, Set up Cloudfront, add DNS records, etc. The modules remove all of this over head
    - **Introduce your reusable Terraform backend and static web hosting module.**
        - **Purpose of Terraform BE**
            - terraform keeps track of the resources that you have deployed in a terraform state file (`terraform.tfstate`)
                - JSON document
                - stores info about resources created by your configuration (resource ids, attributes, and dependencies)
                - creates a mapping of your configuration to real world resources which allows Terraform to know which resources it manages, which ones need to be created, updated, or destroyed and how they relate to each other
                - performance: terraform stores a cache of the attribute values for all resources so that instead of going to every provider and querying every resources to determine the diff between deployed resources and the current configuration, terraform simply uses the `terraform.tfstate` file
            - by default, this terreaform.tfstate file is created locally, on your machine
            - Another option for team oriented projects with the need for collaboration or even for automated deployments with CICD pipelines is to use a remote terraform backend.
- **Terraform Backend Setup**:
    - Explain the importance of using a backend for storing Terraform state.
        - a remote backend stores the terraform state file in a centralized and remote location so that no matter who is deploying your resources, terraform has a consistent reference for which resources need to be created, updated, or destroyed.
        - for AWS, this is an S3 bucket which is where the `terraform.tfstate` file is stored
    - Optionally, you can use state locking which ensures that there are race conditions, which could corrupt the state file, occur if two parties try to deploy at the same time. For the S3 BE, we use a dynamoDB table
    - Provide and discuss the code for setting up the S3 bucket and DynamoDB table using your Terraform module.
        - [https://github.com/R-Ram95/terraform-modules/tree/main/aws_terraform_be](https://github.com/R-Ram95/terraform-modules/tree/main/aws_terraform_be)
        - interface for the module is in the `[variables.tf](http://variables.tf)` file
            - includes the inputs required to use the module
            - `[main.tf](http://main.tf)` is the entry point of the module
                - declare that we are using the aws provider
                - creates S3 bucket for the state file
                - creates a DynamoDB if the `enable_state_locking` field is set
        - `[locals.tf](http://locals.tf)` is used for resource tagging
        - `[outputs.tf](http://outputs.tf)` is the outputs after terraform runs (includes the S3 bucket and DynamoDB tables names

### 3. Static Web Hosting on AWS

In order to host a static website, we need to store our javascript, html, and any other files needed for our web application. On AWS, we store these files in an S3 bucket. We then use a CloudFront distribution to serve those files to end users. Later on, we will add our own custom domain using Route53 and ACM.

3.1 **Creating the S3 Bucket for Website Hosting**

- **S3 Bucket Configuration**:
    - Create the s3 bucket:
        
        ```JavaScript
        resource "aws_s3_bucket" "web_bucket" {
          bucket        = var.bucket_name
          force_destroy = true
          tags          = local.web_bucket_tags
        }
        ```
        
        - The `aws_s3_bucket` resource creates the S3 bucket using a name specified by the `var.bucket_name` variable.
        - `force_destroy = true` ensures that all objects within the bucket are deleted when the bucket is destroyed, avoiding any potential conflicts.
        - `tags = local.web_bucket_tags` applies custom tags to the bucket, which can be useful for tracking and managing AWS resources.
    - Configure Cross Origin Resource Sharing (CORS)
        
        - specify which HTTP methods (GET, POST, PUT, etc.) are allowed and which domains can make the requests → i.e. which requests will CF be allowed to make
        
        ```JavaScript
        resource "aws_s3_bucket_cors_configuration" "web_bucket_cors_config" {
          bucket = aws_s3_bucket.web_bucket.id
          cors_rule {
            allowed_headers = ["*"]
            allowed_methods = ["GET", "HEAD"]
            allowed_origins = ["*"]
          }
        }
        ```
        
        - The `aws_s3_bucket_cors_configuration` resource sets up CORS rules, which are essential for controlling how resources hosted on the S3 bucket can be accessed by external domains.
        - This configuration allows all origins (`allowed_origins = ["*"]`) to send GET and HEAD requests to the bucket, which is typically sufficient for static websites.
    - Server-side Encryption - adding an extra layer of security
        
        ```JavaScript
        resource "aws_s3_bucket_server_side_encryption_configuration" "s3_bucket_sse" {
          bucket = aws_s3_bucket.web_bucket.id
          rule {
            apply_server_side_encryption_by_default {
              sse_algorithm = "AES256"
            }
          }
        }
        ```
        
        - The `aws_s3_bucket_server_side_encryption_configuration` resource ensures that all objects stored in the bucket are encrypted using the AES-256 algorithm. This adds a layer of security by encrypting data at rest.
    - Settings Access Control List (ACL):
        
        ```JavaScript
        resource "aws_s3_bucket_acl" "s3_bucket_acl" {
          bucket     = aws_s3_bucket.web_bucket.id
          acl        = "private"
          depends_on = [aws_s3_bucket_ownership_controls.s3_bucket_acl_ownership]
        }
        
        resource "aws_s3_bucket_ownership_controls" "s3_bucket_acl_ownership" {
          bucket = aws_s3_bucket.web_bucket.id
          rule {
            object_ownership = "ObjectWriter"
          }
        }
        ```
        
        - Our bucket should not be accessible to public via the internet, it should only be accessed by our CF distribution
        - The `aws_s3_bucket_acl` resource sets the ACL of the bucket to "private," ensuring that the bucket is not publicly accessible.
        - The `aws_s3_bucket_ownership_controls` resource is required to avoid errors related to ACLs by setting the object ownership to `ObjectWriter`
    - Blocking public access
        
        - The `aws_s3_bucket_public_access_block` resource is used to block public access to the bucket by default, in order to maintain security
        
        ```JavaScript
        resource "aws_s3_bucket_public_access_block" "block_public_access" {
          bucket                  = aws_s3_bucket.web_bucket.id
          block_public_acls       = true
          block_public_policy     = true
          ignore_public_acls      = true
          restrict_public_buckets = true
        }
        ```
        
    - Create and attach a bucket policy to define which users or services ARE allowed to access the resources
        
        ```JavaScript
        resource "aws_s3_bucket_policy" "web_bucket_policy" {
          bucket = aws_s3_bucket.web_bucket.id
          policy = data.aws_iam_policy_document.web_s3_policy.json
        }
        ```
        
        - The policy → this allows our CF distribution to access the resources
            
            ```JavaScript
            // Enables Cloudfront distribution to access objects in S3
            data "aws_iam_policy_document" "web_s3_policy" {
              statement {
                actions   = ["s3:GetObject"]
                resources = ["${aws_s3_bucket.web_bucket.arn}/*"]
            
                principals {
                  type        = "AWS"
                  identifiers = [aws_cloudfront_origin_access_identity.oai.iam_arn]
                }
              }
            }
            ```
            
    - Finally upload the files from your local machine (or cicd executor) to the S3 bucket
        
        ```JavaScript
        resource "aws_s3_object" "uploaded_files" {
          bucket = aws_s3_bucket.web_bucket.id
        
          for_each = fileset(var.path_to_bundle, "**/*.*")
          key      = each.value
          source   = "${var.path_to_bundle}/${each.value}"
          etag     = filemd5("${var.path_to_bundle}/${each.value}")
          content_type        = lookup(var.content_types, ".${element(split(".", each.value), 1)}", "application/octet-stream")
          content_disposition = "inline"
        }
        ```
        

3.2 **Configuring CloudFront for Content Delivery**

- **Why Use CloudFront**:
    - CloudFront is AWS’s global content delivery network, it ensures that end-users can access your website quickly from anywhere on the globe
    - CloudFront supports caching and SSL support so that your site is served quickly and securely
- **CloudFront Distribution Setup**:
    - Creating the Origin Access Identity (OAI)
        
        ```JavaScript
        resource "aws_cloudfront_origin_access_identity" "oai" {
          comment = "OAI for Cloudfront"
        }
        ```
        
        - The `aws_cloudfront_origin_access_identity` resource creates an OAI, which allows CloudFront to access the S3 bucket securely. This ensures that the S3 bucket is not publicly accessible, as the content is served through CloudFront.
        - Defining the distribution:
            
            ```JavaScript
            resource "aws_cloudfront_distribution" "cf_distribution" {
              enabled             = true
              default_root_object = var.default_root_object
              tags                = local.cf_distribution_tags
              price_class         = var.cf_price_class
            ```
            
            - The `aws_cloudfront_distribution` resource configures the CloudFront distribution that will serve content from the S3 bucket.
            - The `enabled` parameter ensures the distribution is active, and `default_root_object` sets the default file (like `index.html`) to be served when users access the domain.
            - The `tags` parameter applies custom tags to the CloudFront distribution for management and tracking.
            - The `price_class` parameter allows you to choose a pricing tier that defines the regions where your content will be delivered from.
            
            ```JavaScript
            origin {
              domain_name = aws_s3_bucket.web_bucket.bucket_regional_domain_name
              origin_id   = aws_s3_bucket.web_bucket.bucket_regional_domain_name
            
              s3_origin_config {
                origin_access_identity = aws_cloudfront_origin_access_identity.oai.cloudfront_access_identity_path
              }
            }
            ```
            
            - The `origin` block links the CloudFront distribution to the S3 bucket we created above using the bucket’s regional domain name.
            - The `s3_origin_config` section uses the previously created OAI to ensure secure access to the S3 bucket.
            - default cache behaviour → I will be skipping this part but please checkout the AWS docs for more information
            - Viewer Certificate Configuration ⇒ configuring SSL/TLS for https with a Custom Domain ⇒ else you do not need this block and can use the url that cloudfront assigns, something like: `https://d111111abcdef8.`_`**cloudfront**`_`.net`
                
                ```JavaScript
                viewer_certificate {
                  acm_certificate_arn            = var.root_domain_name != "" ? aws_acm_certificate.site_cert[0].arn : null
                  ssl_support_method             = "sni-only"
                  minimum_protocol_version       = "TLSv1.2_2018"
                  cloudfront_default_certificate = var.root_domain_name != "" ? false : true
                }
                ```
                
                - If a custom domain name is provided (`var.root_domain_name`), an ACM certificate is used to secure the connection. Otherwise, CloudFront’s default certificate is used.
                - `ssl_support_method` is set to `sni-only`, which is the most common method for supporting HTTPS.
                - `minimum_protocol_version` is set to `TLSv1.2_2018` to enforce a secure protocol.

  

**3.3 Setting Up Route 53 for Domain Management**

- **Route 53 and ACM Overview**:
    
    - Route 53 is AWS's DNS service.
    - It connects user requests to internet applications running on AWS by translating domain names into the corresponding IP addresses or endpoint URLs.
    - It can be used to create complex routing policies and relationships between DNS records, such as latency-based routing, geolocation routing, and failover configurations.
    - **For our use case**, we need a simple mapping: mapping our custom domain (e.g., `example.com`) to the URL generated by CloudFront (e.g., `https://d111111abcdef8.cloudfront.net`).
    - We also need SSL/TLS certificates to serve our content over HTTPS instead of HTTP for security and encryption purposes.
    - **AWS Certificate Manager (ACM)** can be used to provision, manage, and deploy both public and private SSL/TLS certificates.
    - **For our use case**, we only need public certificates, as these are required to secure communication between end users and the CloudFront distribution using HTTPS.
    
      
    
- Code explanation → this code assumes that you have already bought a domain and AWS has provisioned a Route53 hosted zone for you in the same AWS account in which you will create resources for the static web module
    - **Route 53 Hosted Zone Lookup**
        
        ```JavaScript
        data "aws_route53_zone" "hosted_zone" {
          count = var.root_domain_name != "" ? 1 : 0
        
          name         = var.root_domain_name
          private_zone = false
        }
        ```
        
        - look up the hosted zone using your domain name  
              
            
    - **ACM SSL/TLS Certificate**
        
        ```JavaScript
        resource "aws_acm_certificate" "site_cert" {
          count = var.root_domain_name != "" ? 1 : 0
        
          domain_name               = var.root_domain_name
          subject_alternative_names = ["www.${var.root_domain_name}"] // creates root and sub-domain i.e. example.com and www.example.com
          validation_method         = "DNS"
          lifecycle {
            create_before_destroy = true // if cert already exists, create the new one before destroying the old one
          }
        
          tags = local.acm_cert_tags
        }
        ```
        
        - an SSL/TLS certificate from ACM for both the primary domain (example.com) and the www. subdomain (www.example.com)
        - DNS validation will be used to prove ownership of the domain. The other option is email verification but AWS recommends using domain validation
        - the lifecycle field indicates that if ACM must replace the certificate, the new one will be created before the old one is destroyed
    - **Route53 Alias records for the custom domain and the www. sub domain**
        
        - here we add records DNS records in Route53 to point to the CloudFront distribution. I.e. we are creating a mapping between our custom url (example.com and [www.example.com](http://www.example.com)) to the CloudFront distributions
        - we create alias records for the root domain to the cloudfront distributions domain
        - we create alias records for the sub domain to the cloudfront distributions domain
        - why use alias records as opposed to CNAME or A records
            - Alias records are unique to Route53
            - they allow you to map directly to AWS resources like CF distributions, S3 buckets, ELBs
            - A records require an IP address but Alias records do not ⇒ alias records allow you to use domain names directly which is useful for resources that do not have static ip address, e.g. CloudFront distributions which change dynamically
            - Alias records can be used for the root domain whereas A records cannot be used
            - CNAME records incur costs for DNS queries but Alias records do not
            - CNAME records are not able to map to the root domain
        
        ```JavaScript
        // Creates Route53 alias records for the root domain and maps it to the Cloudfront domain name
        resource "aws_route53_record" "root_domain" {
          count   = var.root_domain_name != "" ? 1 : 0
          zone_id = data.aws_route53_zone.hosted_zone[0].id
          name    = var.root_domain_name
          type    = "A"
        
          alias {
            name                   = aws_cloudfront_distribution.cf_distribution.domain_name
            zone_id                = aws_cloudfront_distribution.cf_distribution.hosted_zone_id
            evaluate_target_health = false
          }
        }
        
        // Creates Route53 alias records for the www subdomain and maps it to the Cloudfront domain name
        resource "aws_route53_record" "www" {
          count   = var.root_domain_name != "" ? 1 : 0
          zone_id = data.aws_route53_zone.hosted_zone[0].id
          name    = "www"
          type    = "A"
        
          alias {
            name                   = aws_cloudfront_distribution.cf_distribution.domain_name
            zone_id                = aws_cloudfront_distribution.cf_distribution.hosted_zone_id
            evaluate_target_health = false
          }
        }
        ```
        
    - ACM certificate validation
        
        - We need to prove that we own the domain for which ACM will issue the certificate ⇒ these DNS records server as the proof
        - ACM provides CNAME records that must be added to the DNS zone ⇒ the records include a validation token that ACM checks to confirm that we own the domain
        
        ```JavaScript
        // Create Route 53 DNS records for ACM certificate validation
        resource "aws_route53_record" "cert_validation_record" {
          // Create records for the domain and all sub-domains 
          for_each = var.root_domain_name != "" ? {
            for dvo in aws_acm_certificate.site_cert[0].domain_validation_options : dvo.domain_name => {
              name   = dvo.resource_record_name
              record = dvo.resource_record_value
              type   = dvo.resource_record_type
            }
          } : {}
        
          allow_overwrite = true
          name            = each.value.name
          records         = [each.value.record]
          ttl             = 60 // DNS records cached for 60 seconds before re-requesting
          type            = each.value.type
          zone_id         = data.aws_route53_zone.hosted_zone[0].id
        }
        
        
        // Validate ACM certificates
        resource "aws_acm_certificate_validation" "cert_validation" {
          count = var.root_domain_name != "" ? 1 : 0
        
          certificate_arn         = aws_acm_certificate.site_cert[0].arn
          validation_record_fqdns = [for record in aws_route53_record.cert_validation_record : record.fqdn] // list of FQDNs that implement the validation
        }
        ```
        
          
        
- Code for the Terraform module is located here: [https://github.com/R-Ram95/terraform-modules/tree/main/aws_static_web](https://github.com/R-Ram95/terraform-modules/tree/main/aws_static_web)
- example of the usage:
    
    ```JavaScript
    module "static_website" {
      source = "./path-to-aws_static_web-module"
    
      // required
      project_name         = "my-project"
      region               = "us-east-1"
      bucket_name          = "my-static-website-bucket"
      cf_dist_name         = "my-cloudfront-distribution"
      path_to_bundle       = "./frontend/build"
    
      // optional
      aws_profile          = "default"
      env                  = "production"
      root_domain__name    = "example.com"
      cf_price_class       = "PriceClass_100"
      default_root_object = "index.html"
      content_types = {
        ".html" = "text/html",
        ".css"  = "text/css",
        ".js"   = "application/javascript",
        ".jpg"  = "image/jpeg",
        ".png"  = "image/png",
        ".gif"  = "image/gif",
        ".svg"  = "image/svg+xml",
        ".pdf"  = "application/pdf",
        ".woff" = "font/woff",
        ".woff2" = "font/woff2"
      }
    }
    ```
    

### 4. **Deploying the Website**

- I used these modules for the deployment of my personal web portfolio: https://rohineshram.com
- I purchased a domain from Route 53 in the same AWS account that I will be deploying my static web hosting resources
- I set up the FE project using Vite + Typescript + Tailwind CSS + pnpm (npm would work too)
- I create a folder in the root called `infrastructure` which is where we will pull in the terraform modules for the static web hosting resources and the terraform remote backend:
    
    ```JavaScript
    terraform {
      required_providers {
        aws = {
          source  = "hashicorp/aws"
          version = "~> 4.67.0"
        }
      }
    
      // COMMENT OUT WHEN BOOTSTRAPPING
      backend "s3" {
        bucket         = "web-portfolio-tf-state"
        key            = "terraform.tfstate"
        region         = "us-east-1"
        dynamodb_table = "web-portfolio-tf-lock"
        encrypt        = true
        profile        = "default"
      }
    }
    
    module "web-portfolio-static-web" {
      source           = "git::https://github.com/R-Ram95/terraform-modules//aws_static_web"
      project_name     = "web-portfolio"
      bucket_name      = "web-portfolio-rram-bucket"
      cf_dist_name     = "web-portfolio-cf"
      root_domain_name = var.domain_name
      path_to_bundle   = "../dist"
    }
    
    // UNCOMMENT WHEN BOOTSTRAPPING
    module "terraform-be" {
      source               = "git::https://github.com/R-Ram95/terraform-modules//aws_terraform_be"
      region               = "us-east-1"
      aws_profile          = "default"
      bucket_name          = "web-portfolio-tf-state"
      project_name         = "web-portfolio"
      enable_state_locking = true
      dynamodb_name        = "web-portfolio-tf-lock"
    }
    ```
    
    - note that I added a variable for the root domain because I didn’t want to hard code it ⇒ I actually made this an environment variable in the CircleCI project and so the root domain will be injected at deploy time from the CICD executor
- command sequence for deploying - make sure you `cd` into the infrastructure folder:
    - `terraform init`
        - setups local directory where there terraform configuration files are located (`infrastructure` folder)
        - downloads the provider plugin, here we are using AWS
        - initialized the terraform backend configuration
        - validates the configuration files
        - downloads the module dependencies ⇒ terraform goes to the github repos where our modules are located, and downloads them
            - you will see a `.terraform` folder which will contain the AWS provider, and the two modules I discussed in this blog post
        - `terraform plan`
            - generates an execution plan and shows you which resources will be created, updated, or destroyed
        - `terraform apply`
            - actually provisions your resources in the cloud
- in my project, I used a basic CircleCI CICD pipeline to:
    
    - build the front end bundle
    - initialize terraform
    - provision the resources and upload the FE bundle to the S3 bucket
    - I won’t talk about the details of the pipeline but I will show the code:  
          
        
    
    ```JavaScript
    anchors:
      configure-aws-access: &configure-aws-access
        run:
          name: Configure AWS access
          command: |
            mkdir -p ~/.aws
            file=~/.aws/credentials
            echo "[default]" > $file
            echo "aws_access_key_id = $AWS_ACCESS_KEY_ID" >> $file
            echo "aws_secret_access_key = $AWS_SECRET_ACCESS_KEY" >> $file
    
      # Installs front end dependencies
      install-fe-dependencies: &install-fe-dependencies
        run:
          name: Install front end dependencies
          command: |
            npm install
    
    # Creates a centralized directory to store build artifacts, allowing them to be persisted between jobs
    # and builds to the FE package
      build-fe-package: &build-fe-package
        run:
          name: Run Build
          command: |
            mkdir ~/workspace
            npm run build
            cp -r dist ~/workspace
    
      # Initializes infrastructure
      install-infrastructure-dependencies: &install-infrastructure-dependencies 
        run:
          name: Install Infrastructure Dependencies
          command: |
            cd ./infrastructure
            terraform init
    
      cache-dependencies: &cache-dependencies
        save_cache:
          key: dependency-cache-{{ checksum "package.json" }}
          paths:
            - ./node_modules
            - ./infrastructure/.terraform
    
      load-dependency-cache: &load-dependency-cache
        restore_cache:
          key: dependency-cache-{{ checksum "package.json" }}
    
    version: 2.1
    
    jobs:
      build:
        docker:
          - image: cimg/node:16.18.1
        steps:
          - checkout
          - *load-dependency-cache
          - *install-fe-dependencies
          - *cache-dependencies
          - *build-fe-package
          - persist_to_workspace:
              root: ~/
              paths: [workspace]
    
      deploy:
        docker:
          - image: hashicorp/terraform:light
          - image: cimg/aws:2024.03
        steps:
          - checkout
          - *load-dependency-cache
          - *configure-aws-access
          - *install-infrastructure-dependencies
          - attach_workspace:
              at: ~/
          - run:
              name: Set domain env variable
              command: |
                echo "domain_name = \"$DOMAIN_NAME\"" > ./infrastructure/terraform.tfvars
          - run:
              name: Deploy to AWS
              command: |
                cp -r ~/workspace/dist ./
                cd ./infrastructure
                terraform apply -auto-approve
          - run: 
              name: Create Cloudfront Invalidation
              command: |
                aws cloudfront create-invalidation --distribution-id $CLOUDFRONT_DIST_ID --paths "/*"
    
    workflows:
      build-and-deploy:
        jobs:
          - build
          - deploy:
              context: aws-account
              requires:
                - build
    ```
    

  

### 5. **Conclusion**

- Summarize the steps taken to host a website on AWS using CloudFront, S3, Route 53, ACM, and Terraform.
- Mention any additional resources or next steps (e.g., scaling, monitoring).
- Encourage readers to explore your reusable Terraform modules for their own projects.

### 6. **Code Repository and Resources**

- Provide a link to your GitHub repository with the Terraform modules.
- Offer any additional resources or documentation for readers to learn more.