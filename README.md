## Super Mario Deployment Automation on Kubernetes with Terraform........


## Prerequisite
01. You need an aws account and you need to know some basics of aws to be able to complete this project.
02. You will configure Terraform inside an ec2 instance.
03. You also need an IAM ec2 role that provide necessary permission to ec2.

## Completion steps
01. Login and basics setup.
02. Setup Docker ,Terraform ,aws cli , and Kubectl.
03. IAM Role for EC2.
04. Attach IAM role with your EC2.
05. Building Infrastructure Using terraform.
06. Creation of deployment and service for EKS
07. Destroy all the Insrastructure

## Step 1 → Login and basics setup
01. Login into your aws account as a root user.
02. Launch an EC2 instance with the following setting
03. Click on launch Instance
04. Click on connect and you are connected with your machine
05. Run the following commands

    ```bash
    sudo su
    ```
    ```bash
    apt update -y
    ```

## Step 2 → Setup Docker ,Terraform ,aws cli , and Kubectl
01. Run the following command to Setup Docker

    ```bash
    apt install docker.io
    ```
    ```bash
    which docker
    ```
02. Add ubuntu user to the docker group
    ```bash
    usermod -aG docker $USER # Replace with your username e.g ‘ubuntu’
    ```
03. Switch to Docker Group in Current Session 
    ```bash
    newgrp docker
    ```

## Setup Terraform

1. Install wget

    ```bash
    sudo apt install wget -y
    ```
2. Download the GPG key and store it correctly
    ```bash
    wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
    ```
3. Add the repository to the sources list using correct quotes
    ```bash
    echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
    ```
4. Update the package list
    ```bash
    sudo apt update
    ```
5. Install Terraform
    ```bash
    sudo apt install terraform -y
    ```
    ```bash
    which terraform
    ```


## AWSCLI Overview
AWS Command Line Interface (CLI) is a unified tool provided by Amazon Web Services (AWS) for managing AWS services from the command line. It allows users to interact with various AWS services and resources directly through commands, rather than through the AWS Management Console web interface.

The AWS CLI provides commands for a wide range of AWS services, including but not limited to:

01. Compute Services: Amazon EC2 (Elastic Compute Cloud), AWS Lambda, Amazon ECS (Elastic Container Service).
02. Storage Services: Amazon S3 (Simple Storage Service), Amazon EBS (Elastic Block Store), Amazon Glacier.
03. Database Services: Amazon RDS (Relational Database Service), Amazon DynamoDB, Amazon Redshift.
04. Networking Services: Amazon VPC (Virtual Private Cloud), Amazon Route 53 (Domain Name System), Amazon CloudFront (Content Delivery Network).
05. Security Services: AWS IAM (Identity and Access Management), AWS KMS (Key Management Service), AWS Certificate Manager.
06. Monitoring and Management Services: Amazon CloudWatch, AWS Config, AWS CloudFormation.
07. Machine Learning and Analytics Services: Amazon SageMaker, Amazon Athena, Amazon EMR (Elastic MapReduce).

Using the AWS CLI, users can perform a variety of tasks such as creating and managing AWS resources, configuring security settings, monitoring resource usage, and automating workflows. The AWS CLI commands are structured in a hierarchical manner, organized by services and subcommands.

## Setup AWSCLI
1. Download the AWS CLI version 2 installation file
    ```bash
    curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
    ```
2. Install the unzip package if it’s not already installed
    ```bash
    sudo apt-get install unzip -y
    ```
3. Unzip the downloaded file
    ```bash
    unzip awscliv2.zip
    ```
4. Run the AWS CLI install script    
    ```bash 
    sudo ./aws/install
    ```
5. Verify the installation of AWS CLI by checking its version
    ```bash
    aws --version
    ```


## Setup kubectl
1. Install curl if it’s not already installed
    ```bash
    sudo apt install curl -y
    ```
2. Download the latest stable version of kubectl
    ```bash
    curl -LO https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl
    ```
3. Install kubectl to /usr/local/bin with the appropriate permissions
    ```bash
    sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
    ```
4. To verify that kubectl is installed correctly, check its version
    ```bash
    kubectl version --client
    ```
5. To check the path where kubectl is installed
    ```bash
    which kubectl
    ```

At this point, every thing is setup and installed so the next step is to create an IAM EC2 Role.

## Step 3 → IAM Role for EC2
The IAM role for EC2 is essential because it provides the necessary permissions for your EC2 instance to perform various tasks, such as creating an EKS cluster and managing S3 buckets. By assigning this IAM role to your EC2 instance, it grants the necessary authentication for your EC2 to make changes within your AWS account
01. on the search bar type IAM.
02. click on Roles on the left side.
03. click on create role and choose EC2 from the dropdown.
04. click on next.
05. choose administrator access on permission sections.
06. click on next and give a name to your role.
07. click on create role option and your IAM role is created.

## Step 4 →Attach IAM role with your EC2
01. go to EC2 section.
02. click on actions → security → modify IAM role option.
03. choose the role from dropdown and click on update IAM role

## Step 5 → Building Infrastructure Using terraform
Clone the github repo by running the following commands ---->
1. Create a directory named super_mario
    ```bash
    mkdir super_mario
    ```
2. Change to the super_mario directory
    ```bash
    cd super_mario
    ```
3. Clone the Git repository into the super_mario directory
    ```bash
    git clone https://github.com/cloudsolutionstech/mario_project.git
    ```
4. Change to the mario_project directory   
    ```bash
    cd mario_project
    ```
5. Change to the kubectl-terraform directory    
    ```bash
    cd kubectl-terraform
    ```

Edit the backend.tf file by → vi backend.tf
Note:- make sure to provide your bucket and region name in this file otherwise it doesn’t work and IAM role is also associated with your ec2 which helps ec2 to use other services such S3 bucket.

## Now run the following commands

    terraform init


When you execute 'terraform init,' it establishes your workspace, retrieves essential plugins, and verifies all prerequisites to enable seamless utilization of Terraform for infrastructure provisioning, updating, or management. Think of it as preparing your toolkit and gathering materials before embarking on crafting something remarkable using your computer.


    terraform validate


Terraform validate examines our code, identifying any syntax errors or mistakes. It provides a success output only when the file contains no errors.

    terraform plan


Terraform plan allows us to preview the modifications to our infrastructure. This command enables us to review and verify the proposed changes before granting final approval for building or modifying our application's infrastructure. It serves as the blueprint for our construction project, offering insight before making any actual alterations with Terraform.


    terraform apply


    terraform apply --auto-approve


Executing 'terraform apply --auto-approve' is akin to instructing the computer to proceed with building everything precisely as outlined, without prompting for confirmation at every step. This approach automates the deployment of our infrastructure, eliminating the need for constant manual input. Upon execution, Terraform analyzes our code, identifies necessary creations or alterations, and proceeds with construction, bypassing the usual approval check with the user.
The apply takes 10 to 15 min for completion.

Below command is used to update the configuration of EKS


    aws eks update-kubeconfig --name EKS_CLOUD --region ap-south-1


Using the command 'aws eks update-kubeconfig --name EKS_CLOUD --region ap-south-1' is essentially informing our computer that we're utilizing Amazon EKS (Elastic Kubernetes Service) in the 'us-east-1' region and wish to establish a connection to it. You can specify your preferred region accordingly.

## Step 6 → Creation of deployment and service for EKS
01. change the directory where deployment and service files are stored. File name is "deployment.yaml" run the next command
    ```bash
    cd ..
    ```
02. create the deployment

    ```bash
    kubectl apply -f deployment.yaml
    ```

The deployment.yaml file serves as a comprehensive set of instructions for a computer system, outlining how to effectively run and manage a specific application. It furnishes essential details required for deploying and overseeing the application's lifecycle, encompassing its description, desired instance count, and configuration settings crucial for maintaining seamless operation.

03. Now create the service.

    ```bash
    kubectl apply -f service.yaml
    ```
The service.yaml file functions as a structured set of rules facilitating communication among components within a software application. Resembling a directory, it dictates the means for locating and interacting with various segments of our application. This document defines the communication pathways between different parts of the application, as well as outlining accessibility for external services or users.

04. Run this command
    ```bash
    kubectl get all
    ```

05. Run the follwing command to get the load balancer ingress. This command tells all the details of your application.

    ```bash
    kubectl describe service mario-service
    ```

NOTE:- copy the load balancer ingress and paste it on browser and your game is running.

Before you indulge in playing and enjoying your AWS resources, remember to clean up to avoid unnecessary costs and maintain the security of your AWS account.

Load Balancer Ingress:
Load Balancer Ingress is a crucial mechanism for distributing incoming internet traffic across multiple servers or services. It acts as a digital receptionist at a bustling office building entrance, directing visitors to various floors or departments to prevent overcrowding. Similarly, in the digital realm, Load Balancer Ingress ensures a seamless user experience, enhances application performance, and prevents any single server from being overwhelmed by excessive traffic.

## Step 7 → Destroy all the Insrastructure
01. Below commands delete your deployment and service.

    ```bash
    kubectl delete service mario-service
    ```
    ```bash
    kubectl delete deployment mario-deployment
    ```

02. After service and deployment destruction let’s destroy the infrastructure by the following command it will delete your EKS cluster.

    ```bash
    cd kubectl-terraform
    ```
    ```bash
    terraform destroy --auto-approve
    ```

This might take up to 5 - 10 mins before everything is destroyed.
03. After everything is destroyed through terraform, you can now go to your EC2 and terminate your Instance.....

## License
This project is licensed under the MIT License - see the [![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT) for details.

## Contact
For any questions or inquiries, feel free to reach out:
- Email: info@cloudsolutionstech.com
- YouTube: [YouTube Channel](http://www.youtube.com/@cloudsolutionsIT)

## Additional Resources
- Blog Post: [Latest Tech News](https://cloudsolutionstech.com/news/)


This is the end of this project, You have done a great job !!!



