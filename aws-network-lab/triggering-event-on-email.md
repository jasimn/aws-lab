# Create a policy on AWS to trigger alerts in an email on the  resource events.
## **Goal**
## Receive email alerts whenever:
✔ A new AWS resource is created 
✔ An AWS resource is deleted
## Step 1 — Enable CloudTrail (captures create/delete actions)

1.Go to AWS Console → CloudTrail

2. Create a Trail (if not already created)

Select:
- Management events → Read/Write: Write-only

3.Save the trail.

4.CloudTrail logs all API actions like:
 * RunInstances

* CreateBucket

* TerminateInstances

* DeleteBucket

* CreateUser

DeleteUser, etc.

Step 2 — Create SNS Topic for Email Alerts

Go to Amazon SNS

Click Topics → Create Topic

Type: Standard

Topic name: aws-resource-change-alerts

Create topic

Click Create subscription

Protocol: Email

Enter your email address

Confirm email (check inbox → Click "Confirm subscription")
