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
