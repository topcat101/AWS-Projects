# Overview

# Setting up AWS Organizations

## What is AWS Organizations

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

## Demonstrate inheritance of SCPs across accounts (Diagram)

##### Deny Disabling CloudTrail or Config

<img width="602" height="378" alt="Picture1" src="https://github.com/user-attachments/assets/a9c71213-9862-4b21-b053-434cb0f6f94a" />

##### Deny creation of IAM users

<img width="602" height="329" alt="Picture2" src="https://github.com/user-attachments/assets/516bc327-0c10-4add-9ba1-108722b47295" />

##### Restrict regions (only allow EU-WEST-2 or EU-WEST-1)

<img width="602" height="478" alt="Picture3" src="https://github.com/user-attachments/assets/f6bd827c-4e22-44fb-bb8d-812e9fd28926" />

##### Deny EC2 instance type not on the approved list (security control)

<img width="602" height="531" alt="Picture4" src="https://github.com/user-attachments/assets/578d352a-0e2e-47e9-9e67-fd9877e0544f" />


# Adopting AWS Config & applying Conformance packs
#### What is AWS Config
AWS Config provides records of the configuration of your resources within your environment. This enables tracking of changes over time, allowing you to see who changed what and when within your accounts. Config also validates compliance against rules that you define—for example, ensuring that all S3 buckets have encryption enabled. If a bucket does not meet this requirement, AWS Config can use remediation actions to automatically check the encryption configuration and, if it is missing or incorrect, apply the required encryption (such as AES-256). Additionally, Config provides an audit trail to support governance, compliance, and security requirements.

#### Manual setup of Config
You can set up AWS Config in two ways: manually or with the one-click setup. For learning purposes, I chose the manual route. During the configuration process, you can select which resources you want to record. I chose specific resource types, which allowed me to define the following resources to record:

•	AWS S3 Bucket

•	AWS IAM User

•	AWS EC2 Instance

I set the recording frequency to daily (this can be changed later in the Config settings). For the delivery channel, I created an S3 bucket. You can also enable Amazon SNS to receive email notifications of changes detected by AWS Config.

Next, skip the managed rules (we will cover this later) and review your configuration to confirm everything is set up as intended.

<img width="602" height="257" alt="Picture1" src="https://github.com/user-attachments/assets/496867de-731c-40ca-b83d-7a3a57ec81c5" />

Within the AWS Config dashboard, you will see data such as compliance status, noncompliant rules, AWS Config usage metrics, and more. This is where you can find the latest information on what may need to be brought into compliance within your environment. Note that this view is region-specific. For example, my setup is currently configured only for eu-west-2, as I have not yet enabled AWS Config in other regions.

<img width="274" height="543" alt="Picture2" src="https://github.com/user-attachments/assets/7c19405d-bf5d-4c96-83c9-86c79f468334" />

On the right-hand side, you will see the Settings option. Click this section to view more details about the configurations you have set up for your records.

<img width="602" height="222" alt="Picture3" src="https://github.com/user-attachments/assets/af540b5a-74b5-453a-a686-8c133bce9bfc" />

From the Settings menu, locate the Recorder section and click Edit. Here, you can change the recording frequency of your resources from daily to continuous, allowing AWS Config to track changes to your services in real time.

#### Setting up rules

To set up rules within your environment, navigate to:

AWS Config > Rules > Add rule

<img width="602" height="244" alt="Picture4" src="https://github.com/user-attachments/assets/d3bfd2ea-87c3-424e-99ac-4be8e34ff436" />

From there, you will be presented with a variety of AWS Managed Rules. You can search for and select the rules you want to apply, such as:

•	IAM-user-MFA-enabled

•	S3-bucket-versioning-enabled

•	approved-amis-by-tag

These rules must be added individually, as the rule type does not allow multiple selections at once.

##### Approved-amis-by-tag

<img width="599" height="314" alt="Picture5" src="https://github.com/user-attachments/assets/60afaf21-8195-40cb-a853-2f4234f975fa" />

For the parameters, I set the value as follows:

•	Status: Approved

This means that for the selected AMI from the EC2 instance, the tag must be configured with:

•	Key: Status

•	Value: Approved

After setting up the rule, you may notice that your resource in the Resource Scope section shows as Noncompliant. This happens because the AMI does not yet have the required tags.

To fix this, I needed to set up the tags for the AMI ID. Here’s how I did it:

1.	Go to the EC2 console.

2.	Select your instance and gather its details, such as the AMI ID.

3.	Open AWS CloudShell and run the following command:

aws ec2 create-tags \
    --resources <AMI-ID> \  Place your AMI ID
   --tags Key=Status,Value=Approved

This command allowed me to tag my AMI with the required key and value, ensuring compliance.

After doing this, go back to the Rules Dashboard to check whether the resource is now Compliant.

•	If it still shows as Noncompliant, click Actions > Re-evaluate.

•	If the status remains Noncompliant, check your Settings to confirm that the recording frequency is set to Continuous rather than Daily.

Expected Output:

Once configured correctly, the rule status should change to Compliant.

You will see this reflected when you change the Resource In Scope filter to Compliant or All.

<img width="602" height="57" alt="Picture6" src="https://github.com/user-attachments/assets/e06ba6c3-7dd6-4f24-a20e-ff0f76d42db8" />

##### IAM-user-MFA-enabled

<img width="602" height="498" alt="Picture7" src="https://github.com/user-attachments/assets/250b1757-dc46-46b3-b0e8-fc4d23201e1f" />

For this rule, I set the frequency to 1 hour.

Once the rule is deployed, AWS Config will identify any IAM users that do not have MFA enabled and mark them as Noncompliant.

You should see a list of IAM users flagged as requiring MFA in order to become compliant

<img width="602" height="113" alt="Picture8" src="https://github.com/user-attachments/assets/4177b590-a4bc-4373-9e36-b77c6abbeb7d" />

To make a user compliant with the IAM-user-MFA-enabled rule:

1.	Open the IAM Console and navigate to Users.

2.	Select a user from the Noncompliant list.

3.	At the top of the user details page, locate the notification indicating that the user does not have MFA enabled.

4.	Click the highlighted prompt to begin enabling MFA.

5.	Follow the setup wizard to configure MFA for the user (for example, using a virtual MFA device or hardware token).

The IAM user will be marked as Compliant in AWS Config once MFA has been success-fully enabled

<img width="602" height="60" alt="Picture9" src="https://github.com/user-attachments/assets/70f3cdd4-67e4-423e-a4d2-4fde9b489ffa" />

##### S3-bucket-versioning-enabled

When creating this rule, you will see the parameter isMfaEnabled. This option ensures that MFA Delete is required when deleting an S3 bucket. In that case, the root account MFA device must be used as confirmation.

Checking Noncompliant Buckets

After creating the rule, you may notice that some S3 buckets are marked as Noncompliant.

1.	Go to the S3 Console.

2.	Select a bucket from the noncompliant list.

3.	Open the Properties tab.

4.	Locate Bucket Versioning.

5.	If versioning is disabled, click Edit and enable it.

Enabling MFA Delete

To enable MFA Delete on an S3 bucket, follow these steps:

1.	Create access keys for the root account (temporary use only).

2.	Open AWS CloudShell (or a local terminal configured with the AWS CLI).

3.	Configure the root profile with your keys

aws configure --profile root-mfa

4.	Enable versioning with MFA Delete on the target bucket:

aws s3api put-bucket-versioning --bucket bucket-name --versioning-configuration Status=Enabled,MFADelete=Enabled --mfa "arn:aws:iam::account-id:mfa/root-account-mfa-device mfa-token" --profile root-mfa

This command attaches the root MFA device to the bucket, enabling MFA Delete.


Security Best Practice

Once the configuration is complete, delete the temporary root access keys. Leaving root keys active is considered a security risk.

Expected Result:

•	The S3 bucket shows Versioning: Enabled.

•	MFA Delete is enforced for bucket deletion.


<img width="602" height="89" alt="Picture10" src="https://github.com/user-attachments/assets/c259b49b-0b8b-4785-b7b2-8ce48997994b" />

<img width="602" height="27" alt="Picture11" src="https://github.com/user-attachments/assets/1b4f3e51-7a67-4fc5-bf5a-d843c52dbb72" />


#### Demonstrating auto-remediation
If Versioning is disabled -> re-enable automatically

Auto-remediation for Re-enabling Versioning

Auto-remediation for re-enabling versioning (if it is disabled) allows for a smoother process and helps reduce human error compared to doing it manually.
To begin, navigate to:

AWS Config > Rules > s3-bucket-versioning-enabled > Manage remediation

Once you are in the remediation actions editor, you will be prompted to select a remediation method:

•	Automatic remediation

•	Manual remediation

I chose automatic remediation and provided the following details:

•	Maximum retry attempts: 3

•	Retry time window (in seconds): 60

<img width="602" height="254" alt="Picture12" src="https://github.com/user-attachments/assets/fb9fa018-1e13-4359-9e39-b0ab2df8ccae" />

From there, proceed to the Remediation action details section, where you will select “AWS-ConfigureS3BucketVersioning” as the action to be performed. This will display the details of the action along with the parameters that need to be filled in.

<img width="602" height="346" alt="Picture13" src="https://github.com/user-attachments/assets/b6d7d17d-7a8a-459e-a624-ee3015a33e9f" />

For the Rate limit, I left the field empty.

For the Resource ID parameters, I provided BucketName, which ensures the remediation loops through each bucket to enable versioning.

Within the Parameters section, I configured the following:

•	VersioningState → Enabled

•	AutomationAssumeRole → ARN (to run the SSM actions)

<img width="602" height="410" alt="Picture14" src="https://github.com/user-attachments/assets/1afc11ad-cd26-46fb-87d4-5bfa31749c74" />

If you don’t already have a role for SSM to use, I recommend creating one via the IAM console. When setting up the role, be sure to define SSM as a trusted entity, so that it can assume the role and obtain temporary permissions to access the required resources.

<img width="602" height="97" alt="Picture15" src="https://github.com/user-attachments/assets/59dc8a7c-5e96-40c9-9a2a-91533a5a1275" />


Once you have created the role, your AWS Systems Manager (SSM) service will need permissions to access the relevant resources. At minimum, the role should include the following permissions:

•	S3:GetBucketVersioning

•	S3:PutBucketVersioning

<img width="604" height="97" alt="Picture16" src="https://github.com/user-attachments/assets/824c0ebe-0db4-40da-83f8-eb88cdde00f2" />

After attaching the policy, retrieve the ARN of the IAM role and enter it into the Automa-tionAssumeRole parameter.

If your SSM automation is not running as expected, I recommend using the following command:

aws configservice describe-remediation-execution-status --config-rule-name s3-bucket-versioning-enabled

This command provides more context on why the remediation may not be running as intended. It allows you to identify any failed states and view the associated error mes-sages for troubleshooting.



#### Deploying a Conformance packs

##### What are conformance packs

Conformance packs offer an abundance of predefined rules that you can deploy across your AWS services. These rules may help you align with specific standards, such as PCI-DSS, or implement best practices for your resources, such as the Operational Best Practices for AWS IAM pack.


_________________________________________________________________________________________________________________________________________________________________________________________
# Notes

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
