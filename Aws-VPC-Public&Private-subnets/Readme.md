# Introduction

This AWS project demonstrates that I am currently practising building secure and scalable infrastructure within the cloud, 
by setting up a Virtual Private Cloud (VPC) with both public and private subnets. 
The goal of this was to build hands-on experience with AWS networking while following best practices for security and access management.

The services that I have used are VPCs, Internet Gateway, Route Tables, EC2 Instances, and security groups to design a secure environment. 
Public instances are accessible through the internet gateway (Internet), whilst the private instances, 
can only be reached through the bastion host within the public subnet.

# Create a Virtual Private Cloud

Having a VPC within your infrastructure is important because it allows you to control network configurations, such as IP address ranges, subnets, and security settings

To get started, go to the AWS Management Console and search for VPC. Once you are in the VPC service, navigate to Your VPCs > Create VPC. This will initiate the process of creating a VPC.

<img width="602" height="196" alt="AWS VPC - (1)" src="https://github.com/user-attachments/assets/c5f9d044-bae2-4162-9fb0-29af043ead52" />

After entering the Create VPC section, enter the required details such as the Name and IPv4 CIDR block. After completing the configuration, click Create VPC. Your VPC will then be successfully created. Following the rfc1918 standards I used 10.0.0.0/16

<img width="508" height="265" alt="AWS VPC - (2)" src="https://github.com/user-attachments/assets/f4a1ec61-0221-4de5-a593-0495378b7632" />


# Create an internet gateway

An Internet Gateway acts as a bridge between your private network and the public internet, enabling communication between the two.

To access the Internet Gateway, navigate to VPC > Internet Gateways. After entering in this section, you can create a new Internet Gateway. The only required detail is the name.

<img width="602" height="160" alt="IGW(1)" src="https://github.com/user-attachments/assets/af742bf6-87b8-4121-8e58-6f93d38e0099" />

Once the Internet Gateway has been created, you need to associate it with a VPC by attaching it to that VPC.

<img width="602" height="102" alt="IGW(2)" src="https://github.com/user-attachments/assets/043f830b-78ac-47aa-9802-4d49120dedf9" />
