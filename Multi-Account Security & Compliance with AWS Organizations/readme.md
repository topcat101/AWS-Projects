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

Service Control Policies (SCPs) allow your organization to enforce controlled measures within a hierarchy using Organizational Units (OUs). For example, if you apply a policy at the root level that restricts access to only the London and Ireland regions, then all ac-counts under that organization will only be able to access those regions. This provides greater control over what IAM users can and cannot access, thereby enhancing overall security.

To create an SCP, navigate to Organizations > Policies > Service Control Policies, and then click Create policy.

https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_scps_syntax.html

The way I’ve implemented inheritance of the SCPs across the accounts is by assigning policies to Organizational Units (OUs). This allows access to be controlled as it flows down the hierarchy. For example, I have:

-	Denied the ability to disable CloudTrail and Config, ensuring that no one can turn these off in the DEV OU.
  
-	Restricted regions at the Root OU level.
  
-	Denied the creation of IAM users in the Sandbox OU.
  
-	Denied certain EC2 instance types in the Production OU.
  
To assign a policy, select the SCP you created and then choose the target OU you want to apply it to.

-	Demonstrate inheritance of SCPs across accounts (Diagram)

##### Deny Disabling CloudTrail or Config

<img width="602" height="378" alt="Picture1" src="https://github.com/user-attachments/assets/a9c71213-9862-4b21-b053-434cb0f6f94a" />

##### Deny creation of IAM users

<img width="602" height="329" alt="Picture2" src="https://github.com/user-attachments/assets/516bc327-0c10-4add-9ba1-108722b47295" />

##### Restrict regions (only allow EU-WEST-2 or EU-WEST-1)

<img width="602" height="478" alt="Picture3" src="https://github.com/user-attachments/assets/f6bd827c-4e22-44fb-bb8d-812e9fd28926" />

##### Deny EC2 instance type not on the approved list (security control)

<img width="602" height="531" alt="Picture4" src="https://github.com/user-attachments/assets/578d352a-0e2e-47e9-9e67-fd9877e0544f" />

### AWS Config
#### What is AWS Config

AWS Config provides records of the configuration of your resources within your environment. This enables tracking of changes over time, allowing you to see who changed what and when within your accounts. Config also validates compliance against rules that you define, for example, ensuring that all S3 buckets have encryption enabled. If a bucket does not meet this requirement, AWS Config can invoke a Lambda function to check the encryption configuration and, if it is missing or incorrect, automatically apply the required encryption (such as AES-256). Nevertheless, Config allows for an audit trail for the governance, compliance and security requirements.

#### Manual setup of Config
https://docs.aws.amazon.com/config/latest/developerguide/manual-setup.title.html
-	Document the process I have taken
-	Share how you can change the settings to record what data. (AWS Config > Settings > Recorder)
-	Talk about the settings what you can change for the frequency Daily or Continuous.

#### Config dashboard
-	Talk about the regions

	
#### Setting up rules

-	Different rules sets (IAM-user-MFA-enabled & S3-Bucket-versioning enabled)
-	Talk about remediation actions
-	Compliant or Nocompliant
-	Re-evaluating
-	Talk about what I have done so far to remediate these actions, show examples to the viewer so we can assist them with this cruise.
-	
-	Talk how I have enabled MFA based on bucket deletions for S3-Bucket-versioning enabled, what administrative privileges I have had to give to a user.
-	Enable aws access keys for root user. (Not advisable)

#### Deploying a Conformance packs
-	Operational Best practices for aws identity and access management.
-	Templates & Parameters
-	Discuss the complaint score
-	Talk about Parameters
-	Taslk about the rules that have been set


#### Demonstrating auto-remediation (if versioning is disabled -> re-enable automatically)
-	Talk about the steps I have taken to set this up
-	What I have done to troubleshoot the issue
-	Talk about using the cmd line and using this cmd “aws configservice describe-remediation-execution-status --config-rule-name s3-bucket-versioning-enabled”
-	Talk about the time it has taken and show the role (Policy) that I have used to allow SSM to access those resources automatically.


#### Show how Config aggregates compliance across all accounts in the Organization.
#### Deploying to multi accounts within organizations

https://aws.amazon.com/blogs/mt/moving-from-a-single-account-aws-config-deployment-to-an-organization-wide-deployment/#:~:text=In%20order%20to%20use%20AWS,Solution%20overview
https://docs.aws.amazon.com/config/latest/developerguide/cpack-prerequisites.html
https://aws.amazon.com/blogs/devops/how-to-centrally-manage-aws-config-rules-across-multiple-aws-accounts/

#### Notes
- Write Custom Config rules with lambda (EC2 instance must have specific tags)
- Writing a custom config rule with Lambda (EC2 instances must have a specific tags) -- ADDING THIS AT A LATER DATE....


### IAM Identity Center

- Create a user
- Provided permissions sets
- Showcase how a single user can switch roles between accounts without separate IAM credentials.
- Testing if the SCP are working accordingly
- Then I can go into ABAC (Attribute-Based Access Control) with tags for dynamic permissions
- Assign permission sets at OU level, so new accounts inherit access automatically



### IAM Access Analyser

### Security & Compliance Reporting

### Automation & Alerts

### GuardDuty & Security Hub Integration

### Documentation & Governance
