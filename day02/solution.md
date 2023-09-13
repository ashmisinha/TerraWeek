# Terraform Configuration Language (HCL)

Welcome to the 2nd day of #Terraweek challenge, where we will work with the Terraform Hashicorp Configuration Language (HCL). In this blog, we will understand and work with following concepts:

-  HCL blocks, parameters, and arguments
-  Variables, Data Types, and Expressions
-  Writing Terraform Configurations

## HCL blocks, parameters, and arguments

### HCL Syntax:

- HCL is a toolkit for creating structured configuration languages that are both human- and machine-friendly, for use with command-line tools. Although intended to be generally useful, it is primarily targeted towards DevOps tools, servers, etc.
- HCL has both a native syntax, intended to be pleasant to read and write for humans, and a JSON-based variant that is easier for machines to generate and parse.
- The Tarraform language syntax is built around these key syntax constructs: blocks, parameters and arguments.

### Blocks:

HCL uses blocks to define resources, variables, data sources, and more. A block is a container for other content. Blocks have a type that can have zero or more required labels followed by { } brackets that contain block's body. Blocks can be nested inside each other. This a general representation of a block:

Let’s take a simple example of a block that defines an AWS EC2 instance:

```hcl
resource "aws_instance" "my_instance" {
  ami           = "ami-053b0d53c279acc90"
  instance_type = "t2.micro"
}
```

![image](https://github.com/ashmisinha/TerraWeek/assets/66667107/460e1c05-12c5-4d63-8bfb-7e50c6a3e3ce)


In this example, "aws_instance" is the first label that points to the **type of AWS resource** and "my_instance" is the second label that represents the **name of the resource**. 

![image](https://github.com/ashmisinha/TerraWeek/assets/66667107/524a1abd-53ab-47d6-a606-1f9a1b4b18f7)

### Parameters: Variables within Blocks

**Parameters** are **variables within blocks that allow you to define reusable values.** They enable you to create dynamic and flexible configurations. You can think of them as placeholders that can be assigned different values when using the block.

Continuing with our previous example, let’s see how parameters are written:

```hcl
resource "aws_instance" "my_instance" {
  ami           = var.ec2_ubuntu.ami_id
  instance_type = var.instance_type
}
```
![image](https://github.com/ashmisinha/TerraWeek/assets/66667107/80c5595b-4b11-4f45-8df8-ff0a99b701ff)

Here, **var.ami_id and var.instance_type are parameters.** Instead of hard-coding the values directly, we refer to the variables ami_id and instance_type. This way, we can **create a template** that are reusable.

### Arguments: Assigning Values to Parameters
We now have parameters, we need a way to assign values to them. This is where arguments come into picture. **Arguments** are **used to provide specific values to parameters within a block.** It's something that we give manually (main.tf).

Let’s see an example of assigning arguments to our previous block:

```hcl
variable "ami_id" {
default = "ami-053b0d53c279acc90"
}

variable "instance_type" {
default = "t2.micro"
}
```
![image](https://github.com/ashmisinha/TerraWeek/assets/66667107/359bc7b0-c2dc-4f38-9958-7d540d19b10b)

In this example, we are using the variable block to pick a value for AMI for an EC2 instance . We **assign values to the parameters ami_id and instance_type using the equals sign =.**

Notice how the parameter names (ami_id and instance_type) match the variable names used within the block definition. This is important for the proper assignment of values.

### Variables, Data Types, and Expressions
**Variables** are an important part of Terraform configurations. They **allow you to define values that can be reused throughout your configuration.** In HCL, variables are defined using the variable block.

**Variables:**

To define a variable in HCL, create a **variables.tf** file and add the following code:

```hcl
variable "instance_count" {
  type    = number
  default = 2
}
```
![image](https://github.com/ashmisinha/TerraWeek/assets/66667107/c7092ec9-1e06-47f3-95c8-3912610f12f9)

In this example, the instance_count variable is defined with a type of number and a default value of "2".

**Data Types**:
HCL supports several data types, including:
- String: Represents text values
```hcl
  variable "message" {
    type    = string
    default = "Hello, World!"
  }
```
- Number: Represents numeric values.
```hcl
  variable "count" {
    type    = number
    default = 42
  }
```
- Boolean: Represents true or false values
```hcl
  variable "enabled" {
    type    = bool
    default = true
  }
```
- List: Represents a sequence of values
```hcl
  variable "regions" {
    type    = list(string)
    default = ["us-east-1","us-east-2"]
  }
```  
- Map: Represents key-value pairs. Maps are defined using curly braces ({}) and can have string keys and values of any data type.
```hcl
variable “tags” {
  type = map(string)
  default = {
      name = “instance_dev”
      env_name = “dev”   
   }
}
```

## Writing Terraform Configurations using HCL syntax
Let’s write a **Terraform configuration for Docker and AWS using HCL syntax.**

### 1. Docker
The first step is to install docker on the instance as docker does not comes with terraform else, we will run into below issue.

![image](https://github.com/ashmisinha/TerraWeek/assets/66667107/de51a5e6-6590-4a38-b3d9-b95c20f3307f)

Also, add the required permission to docker else, you will receive "permission denied" error.

```
sudo chown $USER /var/run/docker.sock
```

Before, we begin writting the terraform configuration for Docker, remember that if we follow a standard structure (which is accoridng to the industry standards) and a neat coding style we can avoid confusion and errors. 

**Docker Provider**
The Docker provider allows you to manage Docker containers and related resources. **To add the Docker provider to your Terraform configuration, you need to include the required_providers block and specify the particular version.**
We will first add a terraform block and add the provider, usually we write providers in _**terraform.tf**_

```hcl
terraform {
  required_providers {
    docker = {
      source  = "kreuzwerker/docker"
      version = "3.0.2"
   }
  }
}
```
![image](https://github.com/ashmisinha/TerraWeek/assets/66667107/34829b21-2932-4672-8c45-1709a50cbb42)

Then, we configure the provider that we added in the terraform.tf. This is wriitern in _**providers.tf**_

```hcl
provider "docker" {
}
```
![image](https://github.com/ashmisinha/TerraWeek/assets/66667107/7ce11c1b-5511-459a-99ce-28808c7886eb)

Finally, we write the logic, like here, we want to create an image and create a container out of it **(creating resources)**. Written usually in the **_main.tf_**.

```hcl
resource "docker_image" "nginx-img" {
        name = "nginx:latest"
        keep_locally = false
}
resource "docker_container" "nginxc" {
        name = "my-nginxc"
        image = docker_image.nginx-img.name
        ports  {
        internal = 80
        external = 80
        }
}
```
![image](https://github.com/ashmisinha/TerraWeek/assets/66667107/45068d32-d13a-498e-bbce-42ee4d5c451d)

We created a Docker container called my-nginxc with the latest version of the nginx image and mapping port 80 to port 80.

![image](https://github.com/ashmisinha/TerraWeek/assets/66667107/8b50c194-81ee-448e-bfac-47739aeff1ad)
![image](https://github.com/ashmisinha/TerraWeek/assets/66667107/f7916cd2-2b91-430e-8abd-d2174e29f0a7)


We can also access the nginx using port 80
![image](https://github.com/ashmisinha/TerraWeek/assets/66667107/0cccab9a-7e5c-498e-9ea3-6ca9cae1d184)


### 2. AWS

The first thing we need to do is configure aws:
```
aws configure
```
![image](https://github.com/ashmisinha/TerraWeek/assets/66667107/e04ad06b-bfc1-4a84-b155-ea9a9752fa4e)

Once it is done, then we begin writing the configurations using HCL syntax.

_**terraform.tf**_

```hcl
terraform {
  required_providers {
    aws = {
      source = "hashicorp/aws"
      version = "5.16.1"
    }
  }
}
```
![image](https://github.com/ashmisinha/TerraWeek/assets/66667107/24d71a6e-7810-4917-bbcd-1e8c81ac5669)

_**providers.tf**_

```hcl
provider "aws" {
  region = "us-east-1"
}
```
![image](https://github.com/ashmisinha/TerraWeek/assets/66667107/0b0dfd65-e9ab-4dee-9558-a6e9f549574d)

We can create variables, if you do not want to hardcode things and create a template so that it can be resused.

```hcl
variable "ami_id" {
default = "ami-053b0d53c279acc90"
}

variable "instance_type" {
default = "t2.micro"
}

variable "instance_count" {
  type    = number
  default = 2
}
```
![image](https://github.com/ashmisinha/TerraWeek/assets/66667107/23ae723a-3da3-4912-ac68-c7dd1660267a)

Next, we can create resources for AWS:

```hcl
resource "aws_instance" "my_instance" {
  ami           = var.ami_id
  instance_type = var.instance_type
}
```
![image](https://github.com/ashmisinha/TerraWeek/assets/66667107/0c2bf207-38d6-457e-9315-347d80eaeaa6)

We can see the EC2 instance is providioned
![image](https://github.com/ashmisinha/TerraWeek/assets/66667107/b23611f7-1002-4a19-8f07-435b265e9b85)
![image](https://github.com/ashmisinha/TerraWeek/assets/66667107/36568f0b-4482-4eaf-a949-7e0a73cc80cf)

## Conclusion

In this blog post, we've discussed HCL blocks, parameters, and arguments, explored various resource types, and provided configuration examples for Docker and AWS providers.

Happy Terraforming!👾

