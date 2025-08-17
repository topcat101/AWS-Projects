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

# Subnet for both public and private

Now that the Internet Gateway is attached to the VPC, the next step is to create and connect subnets within that VPC. It is important to ensure that the subnets are created in the correct Availability Zone (for example, eu-west-2 in this case). You will typically create both public and private subnets.

To do this, navigate to VPC > Subnets in the AWS Management Console and click Create Subnet.

<img width="601" height="214" alt="SUBNET(1)" src="https://github.com/user-attachments/assets/d3e30af3-2ae2-4f9b-b3f5-25e26db5fcc7" />


To create a subnet, you must first select the VPC you created earlier. After that, provide a subnet name and specify an IPv4 CIDR block. The size of the CIDR block determines how many IP addresses will be available within the subnet. For example, using a /24 CIDR block provides 251 usable IPv4 addresses.


# Route tables
A Route Table allows you to define rules that determine how network traffic is directed within your VPC. Each subnet in a VPC must be associated with a route table, which controls the flow of traffic both inside the VPC and to external networks such as the internet.

To create a route table, navigate to VPC > Route Tables in the AWS Management Console and click Create Route Table. Provide details such as the name and the VPC to which it should be associated. Typically, you will create two route tables:

•	A Public Route Table, associated with the public subnet and configured to allow internet access via the Internet Gateway.

•	A Private Route Table, associated with the private subnet and configured for internal traffic only (or optionally routed through a NAT Gateway for internet access).


 <img width="602" height="213" alt="RouteTable(1)" src="https://github.com/user-attachments/assets/32daa007-e6e9-41d3-bfb1-9f3c808aea4f" />


Once both the public and private route tables have been created, the next step is to edit the routes. Start with the public route table. By default, a local route is already present, which enables communication within the VPC. Keep this route in place and then add a new route that enables internet access.

Set the destination to 0.0.0.0/0 and the target to the Internet Gateway. The destination 0.0.0.0/0 represents all IPv4 addresses, meaning any traffic can access your VPC’s CIDR block will be directed through the Internet Gateway. This allows instances in the public subnet to access the internet.

<img width="602" height="365" alt="RouteTable(2)" src="https://github.com/user-attachments/assets/4f556653-ff98-455e-86d5-e0fc5cce00ee" />

For the private route table, you only need to associate it with the private subnet. This ensures that the private subnet follows the rules defined in the private route table. By default, it will only have a local route, meaning traffic is restricted to communication within the VPC.
