### Overview
The baseline of this project is to create an IAM user, assign a custom policy, and monitor their activity within AWS. This involves setting up CloudTrail to capture events, integrating it with CloudWatch Logs, and creating metrics and alarms to track IAM user actions such as failed logins or S3 bucket changes.

The overall goal of this mini project is not only to avoid giving users unnecessary levels of access, but also to track the actions they perform within the environment for better security and visibility.

<img width="1157" height="434" alt="Sending notification via AWS SNS" src="https://github.com/user-attachments/assets/a7b643a5-3ca1-44e7-8053-4f5ae5d5093d" />


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

Enabling CloudTrail for monitoring user activity
To begin, you need to create a trail in AWS CloudTrail. Navigate to the CloudTrail service and select Create trail.

You will then be prompted to provide details such as:

•	Trail name – The identifier for your trail.

•	S3 bucket and folder – The storage location where the CloudTrail logs will be delivered.

Notes: https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-create-and-update-a-trail.html

<img width="602" height="250" alt="cloudtrail(1)" src="https://github.com/user-attachments/assets/3519bb74-3a8c-4604-9650-637cc30924c8" />

Once you have created the trail, you will be presented with a detailed dashboard containing the following key sections: Event history, Trails, Query results history, and CloudTrail Insights. For now, proceed directly to Event history by selecting View full event history.

<img width="602" height="259" alt="cloudtrail(2)" src="https://github.com/user-attachments/assets/49e25875-5199-4b30-96c9-a663da3f9432" />

  
This will present the logs of user activity within the selected region from the past 90 days. These logs allow you to review actions that have been taken, such as the deletion of an S3 bucket or the creation of a new S3 bucket.

### Enabling & Integrating CloudWatch with CloudTrail

Go to the trails you have created in CloudTrail and open the service. Once there, navigate to CloudWatch Logs. Enable CloudWatch Logs and create a new log group with a new IAM role.

<img width="602" height="50" alt="Picture1" src="https://github.com/user-attachments/assets/84c42a4d-3d8d-4285-b93b-5a0edbc1069a" />

To find the new CloudWatch logs you created, follow this path: CloudWatch > Log Groups > [Log Group Name].

<img width="602" height="201" alt="Picture2" src="https://github.com/user-attachments/assets/f81ea686-1888-4858-9165-cb59911b3462" />

Once inside your logs, you will see log stream files. Select the latest file to view it. The log is displayed in JSON format and contains detailed information about the user’s recent actions. However, it typically takes approximately five minutes after an API call for the logs to appear in CloudWatch, so keep this delay in mind.

<img width="602" height="74" alt="Picture3" src="https://github.com/user-attachments/assets/6b42cc88-b41a-487b-80a6-326df1549cc8" />

Clicking into the logs will display more detailed information, which can help identify issues that have occurred or highlight potential problems in certain situations.


<img width="602" height="190" alt="Picture4" src="https://github.com/user-attachments/assets/38c82c3d-df0e-4976-87a1-fa24697c1171" />

If you would like to query the data, you can move to Logs Insights, which provides various techniques for querying log data. In this case, I chose to use OpenSearch SQL and the built-in query generator to create a query that filters by a specific username and S3 bucket. Once the query is executed, it retrieves and displays all matching log entries, including the details you requested.

### Setting Up Metrics & Alerts: Tracking IAM User Activity

First, you need to set up a metric filter in CloudWatch. Navigate to: CloudWatch > Log groups. Select the log group you want to monitor by checking its box, then choose Actions > Create metric filter.

<img width="602" height="68" alt="Picture1" src="https://github.com/user-attachments/assets/1309f7fc-34dc-4307-bcc7-79be8c4dc27d" />
 
Now you can define the details for your metric by creating a filter pattern.

-	Failed sign-in attempts to the Management Console

{ ($.eventName = ConsoleLogin) `&&`  ($.responseElements.ConsoleLogin = "Failure") }

-	S3 bucket creation or deletion by an IAM user

{ (`$.eventName = CreateBucket`) || (`$.eventName = DeleteBucket`) }

#### Assigning the Metric

After entering the filter pattern, select Next to assign the metric:

1.	Filter name – Provide a name that clearly describes the filter.

2.	Namespace – A container for CloudWatch metrics, used to isolate metrics from different sources. You can assign any meaningful name here.

3.	Metric name – Provide a descriptive name for the metric.

4.	Metric value – Set the value to 1 (this indicates each matching event is counted).

Once complete, select Next to review the configuration.

<img width="602" height="233" alt="Picture2" src="https://github.com/user-attachments/assets/013d5ae7-e46e-4ff5-98e6-25ccafa7c14e" />
 
At the Review and create step, verify that all the required details for the metric filter are correct. If changes are needed, select Edit; otherwise, choose Create metric filter.

To access the metric filters you have created, go to the CloudWatch log group where the filter was defined. Within the log group, select the Metric filters tab to view your filters.
 
You are now provided with the containers of your metric data. To view your metrics, select Query metrics from the right-hand side.
Within the metrics tabs, go to Custom namespaces and select the namespace you created (for example, Create&DeleteBucket). Then check the box for the corresponding metric name. This will display the data showing when specific events have occurred.

It is recommended to adjust the time zone to your local setting and set the view to a 3-hour window for clearer visibility of recent activity.

<img width="602" height="208" alt="Picture4" src="https://github.com/user-attachments/assets/742ab56f-460c-4725-8a6b-ced05f215288" />
 
To begin setting up alarms, return to the log group in CloudWatch where you created the metric filter, and open the Metric filters tab.

<img width="480" height="771" alt="Picture5" src="https://github.com/user-attachments/assets/8ca1cb0b-b877-4348-8258-09b44c5e7de5" />

Once you are in the Metric filters area, select the namespace you want to create an alarm for, then choose Create alarm.

#### Create alarm

<img width="464" height="343" alt="Picture7" src="https://github.com/user-attachments/assets/f6c39238-13a3-4394-b589-6d7a3a9c2682" />

In the Create alarm section, configure the alarm details:

 •	Metric name – Select the metric you defined earlier.

 •	Statistic – Leave as Sum (default).

 •	Period – For example, set to 30 seconds to evaluate how many times the event occurs within that time frame.

 •	Threshold type – Leave as Static.

 •	Condition – Set to Greater/Equal than 1, meaning the alarm will trigger if the event occurs at least once in the defined period.

Statistics Reference: https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Statistics-definitions.html

#### Configure Actions

When configuring the actions for your alarm:

•	Alarm state trigger – Leave this set to In Alarm.

•	Send notification to SNS topic – Select Create new topic and give it a meaningful name relevant to the alarm.

•	Notification endpoint – Enter the email address you want to use for receiving alerts.

•	Create topic – After creating the topic, confirm the subscription by clicking the link sent to your email inbox (check your Junk/Spam folder if you don’t see it).

•	Once confirmed, return to the configuration and select Next.

•	Verify the SNS topic for any additional details or troubleshooting if issues occur.

⚠️ Note: It is not recommended to use Gmail addresses for SNS notifications. 

<img width="602" height="191" alt="Picture8" src="https://github.com/user-attachments/assets/ea1abed2-2151-411f-b6c6-2ff64a98c87f" />

#### Alarm Details and Testing

In the Alarm details section, provide the relevant information such as a name and description for the alarm. Once complete, select Next and then Create alarm.
To test the alarm, create a new S3 bucket (or perform the event you configured the filter for). Allow a few minutes for processing, and a notification should be sent to the email address you specified.

#### ⚠️ Troubleshooting:

•	If no email arrives, confirm that you subscribed to the SNS topic (check your inbox and spam/junk folders).

•	Verify the filter pattern is correct and matches the test event.

•	Ensure the alarm threshold (e.g., ≥ 1) is appropriate for the test event.

•	Check CloudWatch > Alarms to confirm the alarm state changed to In Alarm.



### Next steps/ Future improvements
-	Refining IAM Policies
-	Integrating IAM with AWS Organizations
-	Adding IAM Identity Centre (AWS SSO)
-	IAM Access Analyzer
-	Applying IAM Best Practices
-	Setting Up Reports

