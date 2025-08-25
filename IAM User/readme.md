### Introduction

Work in progress!

### Creating a user

Provide details such as the username and password and specify whether the user should have access to the AWS Management Console. 
Additionally, ensure that the user is required to create a new password at their next sign-in for security purposes.

<img width="602" height="608" alt="Img1(Creating user)" src="https://github.com/user-attachments/assets/d75be18e-7857-4ff9-b867-4d9eed8cc573" />



 
Select “Add user to group”, then proceed to Review and create, where you can confirm the user details you have configured. 
Once in that section, click Create, and you will be provided with the console sign-in details.

<img width="602" height="404" alt="Img2(Creating user)" src="https://github.com/user-attachments/assets/70095554-5419-4238-9f83-a57013848c1f" />

Once you have created the user, go to the Users section within the IAM service. 
There, you will find the services and details related to that user. 
It is recommended that you save the user’s ARN (12 digits), as it will be required for future reference.

<img width="515" height="260" alt="Img3(Creating user)" src="https://github.com/user-attachments/assets/1cee71e9-3679-4baf-ad43-72328a6026d9" />
 
You can also enable MFA (Multi-Factor Authentication) as a best practice to enhance the security of user account access.

### Creating the policy within IAM
To create a policy, navigate to: IAM > Policies > Create Policy.
  
<img width="602" height="140" alt="img1(Creating the policy)" src="https://github.com/user-attachments/assets/38212bdd-458f-43ab-aec5-f016e2f6dfb3" />

Within the Create Policy section, you can choose either the JSON editor or the Visual editor. 
In this example, we’ll use JSON because of its readability and structured formatting.
When using the JSON editor, you will encounter the following key elements in the policy:
•	Version – Defines the version of the policy language.
•	Statement – Contains the main policy details.
•	Sid – Serves as an optional identifier or description.
•	Effect – Determines whether the statement allows or denies access to resources.
•	Action – Specifies the action(s) allowed or denied for services.
•	Resource – Defines the object(s) to which the statement applies.
Note: 
-	IAM JSON Policy Elements Reference https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html
-	Creating IAM Policies
https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create-console.html

 <img width="602" height="630" alt="img2(Creating the policy)" src="https://github.com/user-attachments/assets/cc146d6d-1cd3-4f83-ab7e-07451e0406c7" />

The policy being created for the user account grants access to EC2 resources, S3 buckets, Organizations, and IAM.
After defining the policy, select Next. This step will display the permissions included in the policy and allow you to specify a name for it. Once you have reviewed and confirmed the details, select Create policy.
The newly created policy will then be available in the Policies section of the IAM service.
 
### Attaching policy to a user
To attach a policy to a created user, navigate to: IAM > Users > Username > Add permissions.
Within the Add permissions section, you will be presented with the following options:
•	Add user to group
•	Copy permissions
•	Attach policies directly

<img width="602" height="357" alt="img1(Attaching Policy to user)" src="https://github.com/user-attachments/assets/5f83cb2f-8475-4650-8d5f-ddb7ab1f356e" />

You should select Attach policies directly, which allows you to add the policy you created. From there, search for the policy by name and select the checkbox to attach it. Then, choose Next and select Add permissions. The user will now have the specified permissions assigned to their account.

<img width="601" height="229" alt="img2(Attaching Policy to user)" src="https://github.com/user-attachments/assets/49fdf70c-a07f-4b9e-addf-9ada3b05d2d6" />
  
 
### Sign in
Once you have completed the above sections, sign in to the AWS Management Console. It is recommended to open a new browser tab or use an incognito/private browsing window. On the AWS sign-in page, enter the user credentials along with the 12-digit AWS account ID.


### Enabling CloudTrail for user activity

### Next steps/ Future improvements
