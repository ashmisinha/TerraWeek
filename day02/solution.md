# Terraform Configuration Language (HCL)

Welcome to the 2nd day of #Terraweek challenge, where we will work with the Terraform Hashicorp Configuration Language (HCL). In this blog, we will understand and work with following concepts:

-  HCL blocks, parameters, and arguments
-  Variables, Data Types, and Expressions
-  Writing Terraform Configurations
-  Testing and Debugging the configuration

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
Let’s write a **Terraform configuration using HCL syntax.**

### Adding Required Providers - Docker and AWS (in providers.tf)

The Docker provider allows you to manage Docker containers and related resources. **To add the Docker provider to your Terraform configuration, you need to include the required_providers block and specify the particular version.**

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
![image](https://github.com/ashmisinha/TerraWeek/assets/66667107/51019971-7c60-4890-89e9-b71a19aa25de)


**AWS Provider:**

The **AWS provide**r is one of the most commonly used providers in Terraform. It allows you to **provision and manage resources in Amazon Web Services (AWS).**

```hcl
provider "aws" {
  region = "us-east-1"
}
```

**Creating Resources**

Next, we can create resources using HCL syntax. Here is an example of how to create a Docker container:

```hcl
provider "docker" {
}
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
![image](https://github.com/ashmisinha/TerraWeek/assets/66667107/b4f51259-73fe-431f-a84f-d5aee9860c34)

We created a Docker container called my-nginxc with the latest version of the nginx image and mapping port 80 to port 80.


# Conclusion

In this blog, we’ve covered HCL blocks, parameters, and arguments, different resource types, and written configurations for providers like Docker & AWS. 

Happy Terraforming!

