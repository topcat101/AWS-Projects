### Introduction

### AWS Organizations

#### What is AWS Organizations

AWS Organizations is a service that allows you to centrally manage and govern multiple AWS accounts. It lets you group accounts into a hierarchy, apply policies to enforce compliance and security, consolidate billing, and share resources across accounts. This makes it easier to maintain control environments with multiple teams, workloads, or business units.

#### What are Organizations Accounts

An AWS Organizations account acts as a separate, isolated environment within the organization. Each account can contain its own resources, services, and permissions. By structuring workloads into different accounts, you can isolate environments (such as production, development, or testing) and control what users or services can and cannot access. This separation improves security, governance, and cost management across the organization.

#### Creating the Organization Account

Within AWS Organizations, start by selecting Create an organization. Once the organization is created, you will be taken to the AWS accounts page in the navigation pane.

This view displays your organization structure, showing your account within the root folder of the hierarchy. The account is associated with the email address you used when creating your AWS account.

To add accounts to your organization, you can create a member account by choosing Create an AWS account. This will prompt you to provide the required details, such as the account name and email address. You can even use the same email address as your current account by applying a unique email alias (for example: username@domain.com).

<img width="601" height="349" alt="Organizations Structure" src="https://github.com/user-attachments/assets/d0e77270-03d6-428f-8cbc-fb6213849dee" />


Select the Root folder by clicking its checkbox, then choose the Actions button on the right-hand side. From there, select Add organizational unit (OU) to create a new OU.

Once the OU is created, you can use the Actions menu again to move your account into the new OU group. For example, the image below shows an organization structure with an account moved into its designated OU.

<img width="443" height="328" alt="Organizations Diagram" src="https://github.com/user-attachments/assets/9af07970-724d-4467-ac12-25e7b0ce506e" />

Due to the limitations of this setup, I moved my management account into the MainApp OU. However, this is not a recommended practice. The management account should remain within the root folder to ensure proper governance, security, and account management across the organization.

#### Applying Service Control Policies (SCP)
-	Applying Service Control policies at OU level

 	o	Deny Disabling CloudTrail or Config

 	o	Deny creation of IAM users (Force use of identity Center)

 	o	Restrict regions (e.g., only allow EU-WEST-2 or us-east-1)

 	o	Deny S3 buckets with encryption enabled

 	o	Deny EC2 instance type not on the approved list (Cost/ security control)

-	Demonstrate inheritance of SCPs across accounts



### IAM Identity Center

### AWS Config

### IAM Access Analyser

### Security & Compliance Reporting

### Automation & Alerts

### GuardDuty & Security Hub Integration

### Documentation & Governance
