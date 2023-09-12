#Terraweek Challenge: Day 1 - Introduction to Terraform

Hello everyone, It’s my first day of #Terraweek challenge, where we will dive into the basic concepts of Terraform. 🚀

-  What is Terraform and how does it revolutionize infrastructure management?
-  Why do we need it and how does it simplify provisioning?
-  Learn crucial Terraform terminologies with examples.
-  Understand Terraform Lifecycle.
-  Easily install Terraform and configure environments for AWS.

Let’s start with understanding **Infrastructure as Code(IaC).**

### What Is Infrastructure as Code (IaC)? 🕋

Infrastructure as code (IaC) is the process of managing and provisioning computer data centers through machine-readable definition files, rather than physical hardware configuration or interactive configuration tools.

There are various IaC tools available in market like Terraform, Chef, Puppet, and Ansible. Terraform is widely. As we proceed further with the blogs - #TerraWeek challenge, we will understand "Why".

![image](https://github.com/ashmisinha/TerraWeek/assets/66667107/bcf9b0b0-d7d2-46b9-bb50-3e4b070eff81)


## What Is Terraform?

Terraform is an open-source "Infrastructure as a Code" tool, created by Hashicorp, where you can manage any resource in your cloud environment (AWS, Azure, GCP, OpenStack, VMware, etc.) using code to create, update and destroy. Terraform uses Declarative language, HCL (HashiCorp Configuration Language).

Terraform is one of the most popular open-source Infrastructure-as-code (IaC) tools, used to automate infrastructure tasks. It is used to automate the provisioning of your cloud resources.

With Terraform, we can create infrastructure using a set of instructions (code) that can be used again and again without any clash. Moreover, it will keep track of changes made and we can collaborate for work.

### Why do we need Terraform and How Does it Simplify Infrastructure Provisioning?

Let's go back in time, when Terraform were not used. We faced several challenges, like, time taken for launching instances, running containers took a lot of time and was prone to human errors (as all the processes were handled manually by a dedicated like). It was an expensive operation and had limited automation. 
To overcome these challenges, we started using Terradorm which is IaC. With Terraform, just one DevOps engineer can automate the entire task of Infrastructure provisioning. 

Some of the benefits of using Terraform are:
1. Cost reduction
2. Increase in speed of deployments
3. Reduce errors
4. Improve infrastructure consistency
5. Avoid wasting resources

**Let's see how it simplifies infrastructure provisioning**

Terraform lets you use the same workflow to manage multiple providers and handle cross-cloud dependencies. This simplifies management and orchestration for large-scale, multi-cloud infrastructures.

Terraform enables automation with the help of scripts (written in HCL). This makes the provisioning of infrastructure rapid and easy. Using a single easy-to-run script, you can manage the infrastructure for multiple similar projects.

Terraform helps you plan ahead before making any changes to your infrastructure. It analyzes your code and shows you a preview of what will happen when you apply it. This way, you can review and double-check everything, ensuring a smooth and error-free provisioning process.

Terraform **keeps track of the current state of your infrastructure in a file (.tfstate). This state file is used by the IaC tool to record information about what has been deployed by the tool. It helps Terraform make intelligent decisions when you modify or destroy resources.

### Some important Terminologies in Terraform

- **Provider:** Providers are responsible for interacting with APIs and services of various cloud and infrastructure platforms, such as AWS, Azure, Google Cloud, and more.
Here’s an example configuration block for the AWS provider:

    ```
    provider "aws" {
      region = "us-east-1"
    }
    ```


- **Resource:** **Resources** are the building blocks of your infrastructure. They represent the individual components you want to create or manage, such as servers, databases, or networks. Resources are defined within a Terraform configuration file and are associated with a specific provider. Example:

    ```terraform
    resource "aws_instance" "my_instance" {
      ami           = "ami-053b0d53c279acc90"
      instance_type = "t2.micro"
    }
    ```

- **Variable:** **Variables** are like placeholders that allow you to pass dynamic values to your Terraform code. They provide flexibility by allowing you to change values without modifying the code itself. Variables can be defined within your Terraform configuration files or in separate variable files. Example:

    ```terraform
    variable "region" {
      default = "us-east-1"
    }
    ```

- **Module:** Modules allow you to encapsulate and reuse Terraform configurations. They are particularly useful for organizing and abstracting complex infrastructure code.
  
    ```terraform
    module "vpc" {
      source = "terraform-aws-modules/vpc/aws"
      version = "2.0.0"
    
      # Additional configurations specific to your project
      vpc_name = "my-vpc"
      cidr_block = "10.0.0.0/16"
    }
    ```

- **State:** The **state** is like a memory bank for your infrastructure. It keeps track of the current state of your resources and their configurations. Terraform uses the state to understand what’s already created, making it easier to manage and update your infrastructure. Example:

    ```terraform
    terraform {
      backend "s3" {
        bucket = "my-terraform-state"
      }
    }
    ```

- **Outputs:** Outputs are used to expose values from your Terraform configuration. This is helpful for retrieving information about the infrastructure after it has been created.
   ```terraform
   output "instance_public_ip" {
     value = aws_instance.example.public_ip
   }
   ```

## Terraform Workflow

Terraform lifecycle consists of — **init, validate, plan, apply, and destroy.**

![image](https://github.com/ashmisinha/TerraWeek/assets/66667107/e42fdc66-5876-4e55-8aa5-df1a2a55f569)


- **Terraform init:** initializes the working directory (local) which consists of all the configuration files. (.terraform)

- **Terraform validate:** checks the code for syntax errors and ensures that it is internally consistent. It only looks at the configuration files (*.tf) in the current working directory

- **Terraform plan:** compares the Terraform state with the as-is state in the cloud, builds and displays an execution plan. This does not change the deployment (read-only).

- **Terraform apply:** executes the plan. This potentially changes the deployment.

- **Terraform destroy:** deletes all resources that are governed by this specific Terraform environment.

## Steps to install Terraform and set up the environment for AWS

To install Terraform and set up the environment for AWS, you can follow these steps:

1. **Install Terraform:**
   - Download the Terraform binary suitable for your operating system from the official Terraform website (https://www.terraform.io/downloads.html).
   - Verify the installation using `terraform --version`.

**In this demo, I’m setting up Terraform in an AWS EC2 instance (Ubuntu AMI):**

```
wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install terraform
```

Verify the installation using **terraform — version:**

![image](https://github.com/ashmisinha/TerraWeek/assets/66667107/0c639c6f-1411-45c3-b301-0d2cd72de68e)


2. **Set Up AWS Credentials:**
   - Sign in to the AWS Management Console.
   - Create an IAM user or use an existing one with appropriate permissions for managing AWS resources. Ensure the user has permissions for EC2, VPC, S3, and other services you plan to work with.
   - Generate an access key and secret key for the IAM user. Make a note of these credentials as they will be required for Terraform to interact with AWS.
   - Configure the AWS CLI on your EC2 machine by running the following command in your terminal:

  ```shell
    aws configure
  ```
![image](https://github.com/ashmisinha/TerraWeek/assets/66667107/104fb252-5846-45fb-bdf2-0304cafebebc)


3. **Create Terraform configuration file:**
   - Create a new directory for your Terraform configuration files.
   - Open a terminal and navigate to the new directory.
   - Create a new Terraform configuration file with a .tf extension (e.g., main.tf). This file will contain your infrastructure code.
   - In the configuration file, specify the AWS provider and any resources you want to create or manage.

![image](https://github.com/ashmisinha/TerraWeek/assets/66667107/63c4697c-9045-4577-976a-d77b4d90b67d)


4. **Initialize Terraform:**
   - In the terminal, navigate to your Terraform directory (where your configuration file is located).
   - Run the following command to initialize Terraform and download the necessary provider plugins:

 ```shell
    terraform init
 ```
![image](https://github.com/ashmisinha/TerraWeek/assets/66667107/29eb7a83-204b-4b7f-aa2c-a5571cab2ca5)


5. **Deploy Infrastructure:**
   - After initialization, run the following command to preview the changes Terraform will make to your AWS environment:

```shell
    terraform plan
```
![image](https://github.com/ashmisinha/TerraWeek/assets/66667107/bd7cf537-0a8c-4746-9583-e8537a961ba8)


   - Review the output and ensure it matches your intentions.
   - If everything looks good, execute the following command to apply the changes and create the infrastructure:

 ```shell
    terraform apply
```
![image](https://github.com/ashmisinha/TerraWeek/assets/66667107/8cb23e3f-1a92-4666-92cc-6dfd082ef50e)

**Here you go, our EC2 instance is up and running!**

![image](https://github.com/ashmisinha/TerraWeek/assets/66667107/2ae2f614-c030-480a-bc61-2af1351564e8)


6. **Destroy Infrastructure:**
   - Destroying resources is an integral part of Terraform. Deploying consistent and reproducible infrastructure implies that you will be creating and destroying resources when needed.
   - We used this command extensive in the practice section of this series because we do not want to leave any technical debt behind. It also ensures that we do not incur any unnecessary charges from AWS.
   - Just like with plan and apply commands, Terraform will show you what exactly it will attempt to destroy. 
 
 ```shell
    terraform destroy
```

With one single command, you can destroy your whole infrastructure.🤖

![image](https://github.com/ashmisinha/TerraWeek/assets/66667107/c516eca3-45e1-431c-8f84-8c22278d32a1)

If you recheck the EC2 dashboard, you will see the instance got terminated.
![image](https://github.com/ashmisinha/TerraWeek/assets/66667107/e3384ef3-0838-4a5b-8497-b03defa1d930)


## Conclusion

Terraform is an infrastructure as code tool that enables you to safely and predictably provision and manage infrastructure in any cloud. It let's you manage infrastructure with simple code, automate tasks, and work seamlessly across multiple cloud platforms.

Thank you for reading! At this point, we have covered basics of Terraform and its capabilities.

Happy Provisioning folks!:)
