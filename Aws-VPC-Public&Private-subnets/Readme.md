# Introduction

This AWS project demonstrates that I am currently practising building secure and scalable infrastructure within the cloud, 
by setting up a Virtual Private Cloud (VPC) with both public and private subnets. 
The goal of this was to build hands-on experience with AWS networking while following best practices for security and access management.

The services that I have used are VPCs, Internet Gateway, Route Tables, EC2 Instances, and security groups to design a secure environment. 
Public instances are accessible through the internet gateway (Internet), whilst the private instances, 
can only be reached through the bastion host within the public subnet.

The main purpose of this project is to build my knowledge in the cloud infrastructure architecture, security and networking while creating a real-world environment to showcase in my portfolio. Below is a diagram of the architecture of the system.

<img width="1218" height="700" alt="AWS-Private Public-Subnets-Diagram" src="https://github.com/user-attachments/assets/74a04f22-f85f-41d6-813c-e69bb4c0e678" />


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

Set the destination to 0.0.0.0/0 and the target to the Internet Gateway. The destination 0.0.0.0/0 represents all IPv4 addresses, meaning any traffic that can access your VPC’s CIDR block will be directed through the Internet Gateway. This allows instances in the public subnet to access the internet.

<img width="602" height="365" alt="RouteTable(2)" src="https://github.com/user-attachments/assets/4f556653-ff98-455e-86d5-e0fc5cce00ee" />

For the private route table, you only need to associate it with the private subnet. This ensures that the private subnet follows the rules defined in the private route table. By default, it will only have a local route, meaning traffic is restricted to communication within the VPC.

# Create a Bastion Host
A Bastion Host, also known as a Jump Server, acts as a secure gateway to access resources in the private subnet from the public subnet. Instead of exposing private instances directly to the internet, you connect to the Bastion Host (which resides in the public subnet) and then use it to securely access instances within the private subnet.

To create a Bastion Host, you must first launch an EC2 instance. You can choose any operating system (e.g., Amazon Linux, Ubuntu, or Windows Server) and select an instance type such as t3.micro for testing or small-scale environments.
During setup, you will need to create or select an existing key pair (generating a .pem file), which allows you to securely connect to the instance via SSH (Linux) or RDP (Windows).

In the network settings, assign the instance to the VPC you created earlier, and place it inside the public subnet. Be sure to enable Auto-assign Public IP, so the instance can be accessed from the internet.

This instance will serve as your Bastion Host, enabling secure access to resources within your private subnet.

<img width="597" height="278" alt="Bastion Host(1)" src="https://github.com/user-attachments/assets/07ffad86-d77f-406e-a981-223ce10acfb4" />

Next, assign a Security Group to the instance and provide it with a descriptive name. Ensure that the security group allows SSH (port 22) access to the Bastion Host from your local machine (preferably restricted to your IP address for security).

Once the security group is configured, you can proceed to launch the instance. At this point, your Bastion Host will be ready to use for secure access to your private subnet resources.


# Create a private instance
The private host should be created within the private subnet of your VPC. The process is similar to creating the Bastion Host. When selecting a key pair, use the same key pair that was created for your previous EC2 instance.

In the Network Settings, assign the instance to your VPC and select the private subnet. Disable Auto-assign Public IP, since this instance will not require direct access to the public internet.

Next, configure a new Security Group. You can give it any descriptive name. For inbound rules, choose Custom TCP Rule (SSH, port 22) and set the Source to the IP range of the public subnet (e.g., 10.0.20.0/24). This ensures that only the Bastion Host (in the public subnet) can access the private instance via SSH. Add a short description such as “SSH access via Bastion.”

Once complete, launch the instance. The private host will now be securely deployed within the private subnet and accessible only through the Bastion Host.

<img width="602" height="428" alt="Private(1)" src="https://github.com/user-attachments/assets/7048cb2f-96be-4773-b605-b681d8feb38a" />
 
Note: The private server does not have a public IPv4 address assigned, which means it cannot be accessed directly from the internet. It can only be reached through the Basti-on Host in the public subnet.

# Connecting via SSH steps
To connect to your public host (Bastion Host), you will need to use the key pair you created earlier and establish a secure SSH connection from your local machine. This provides secure access to the Bastion Host, which will then allow you to connect to resources within the private subnet.

### Step 1 - Connecting to the Bastion Host via PowerShell

1.	Open PowerShell on your local machine.
2.	Navigate to the directory where your .pem key file is stored.
3.	Enter the following command:
ssh -i .\Key_Name.pem username@Public-IP

<img width="441" height="27" alt="Picture11" src="https://github.com/user-attachments/assets/00781167-9e36-4788-939e-4b75db18e0be" />


extra context:
-	Replace Key_Name.pem with the name of your key file.
-	Replace username with the correct user for your AMI (e.g., ubuntu for Ubuntu, ec2-user for Amazon Linux).
-	Replace Public-IP with the public IP address of your Bastion Host

Once you have successfully connected to your instance, you will be presented with system details. For example, if you are using an Ubuntu server, you will see the default Ubuntu welcome message along with basic system information.

### Step 2 – Copying the .pem File to the Server

There are multiple ways to transfer your key file to the Bastion Host. One option is to copy and paste the contents of the .pem file into a new file on the server. However, a more common method is to use SFTP (or similar tools to transfer the file directly).

<img width="602" height="385" alt="Picture12" src="https://github.com/user-attachments/assets/0555e43c-a283-48fc-9096-e1ec085ecf8d" />

To create a new file inside your instance, you can use the touch command. This command creates an empty file if it does not already exist or updates the timestamp if the file is already present.

<img width="602" height="73" alt="Picture13" src="https://github.com/user-attachments/assets/fd1a1ad0-736c-4766-95c4-156cf2b53df8" />

After creating the file, open it using a text editor such as nano. Paste the contents of your .pem file into it and then save the changes. This ensures your private key is available inside the instance for SSH access to private servers.

Alternatively, instead of manually copying or pasting the contents of the .pem file, you can use a tool such as FileZilla to securely transfer the file over port 22 (SFTP). FileZilla provides a graphical interface for file transfers, which can be easier for users unfamiliar with command-line tools. Detailed instructions are available in the official FileZilla documentation: Howto: FileZilla Project Wiki.

#### Alternative Method – Using FileZilla (SFTP)
 1.	Download and install FileZilla Client.
 2.	Open FileZilla and go to File > Site Manager.
 3.	Add a new connection with the following details:
  o	Host: Public IP of your Bastion Host
  o	Port: 22
  o	Protocol: SFTP – SSH File Transfer Protocol
  o	Logon Type: Key file authentication
  o	User: ubuntu (for Ubuntu) or ec2-user (for Amazon Linux)
  o	Key file: Select your .pem file
 4.	Connect, and then simply drag and drop your .pem file from your local machine into the Bastion Host’s directory.

### Step 3 – Connecting to the Private Instance via the Public Server

After transferring the .pem file to your Bastion Host (public server), you must set the correct file permissions. Private key files require read-only permissions for security; otherwise, SSH will refuse to use them.

Run the following command to grant read-only access to the file:

chmod 400 PrivateKey.pem

This ensures that only the file owner can read the key, which is required for SSH authentication.

<img width="602" height="236" alt="Picture14" src="https://github.com/user-attachments/assets/22bc617e-9519-4c3e-9f8c-3f50141c6e72" />

Once the correct permissions have been applied to the .pem file, you can connect to the private server from the Bastion Host using the following SSH command:

ssh -i PrivateKey.pem username@<Private-Server-IP>

Replace PrivateKey.pem with your key file, username with the appropriate user (e.g., ubuntu for Ubuntu, ec2-user for Amazon Linux), and <Private-Server-IP> with the private instance’s IP address.
 
After running the SSH command, you are now logged into the private server. From here, you have full access to manage the system, install software, configure services, and perform administrative tasks securely through the Bastion Host.

# Future improvements

-	High availability: Deploy subnets across multiple Availability zones (AZs) for redundancy
  
-	Infrastructure as Code (IaC): Rebuild this environment using Terraform or AWS CloudFormation for repeatability, error handling & version control
  
-	Monitoring & Logging: Integrate AWS CloudWatch and VPC Flow Logs for monitoring, alerting and auditing network activity.




