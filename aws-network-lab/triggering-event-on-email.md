# Create a policy on AWS to trigger alerts in an email on the  resource events.
## **Goal**
## Receive email alerts whenever:
✔ A new AWS resource is created 
✔ An AWS resource is deleted
## Step 1 — Enable CloudTrail (captures create/delete actions)

1.Go to AWS Console → CloudTrail

2.Create a Trail (if not already created)

# Select:
- Management events → Read/Write: Write-only

3.Save the trail.

4.CloudTrail logs all API actions like:
* RunInstances

* CreateBucket

* TerminateInstances

* DeleteBucket

* CreateUser

* DeleteUser, etc.
![My Screenshot](https://github.com/jasimn/aws-lab/blob/main/aws-network-lab/Screenshot%202025-12-03%20143947.png)
## Step 2 — Create SNS Topic for Email Alerts

 1. Go to Amazon SNS

2. Click Topics
3. Create Topics
* Type: Standard

* Topic name: aws-resource-change-alerts


4.Click Create subscription

* Protocol: Email

* Enter your email address

5.Confirm email (check inbox → Click "Confirm subscription")
![My Screenshot](https://github.com/jasimn/aws-lab/blob/main/aws-network-lab/Screenshot%202025-03-10%20233201.png)
------------------------------
## Step 3 — Create EventBridge Rule (Triggers on CloudTrail Create/Delete)

1.Go to Amazon EventBridge

2.Click Rules 
3.Create rule
* Rule name: resource-create-delete-alerts

## Event Pattern

Choose:
* AWS events → CloudTrail → AWS API Call via CloudTrail
Now select operations:
* Event type: AWS API Call via CloudTrail

* API call category: Write-only
*
 Or use Custom Pattern (Better)
```bash
{
  "source": ["aws.*"],
  "detail-type": ["AWS API Call via CloudTrail"],
  "detail": {
    "eventName": [
      {"prefix": "Create"},
      {"prefix": "Run"},
      {"prefix": "Put"},
      {"prefix": "Delete"},
      {"prefix": "Terminate"},
      {"prefix": "Remove"}
    ]
  }
}
```
## Target
Choose:
* SNS topic
* Select: aws-resource-change-alerts
* Create rule.
![My Screenshot](https://github.com/jasimn/aws-lab/blob/main/aws-network-lab/new.png)  
## now when we  create/delete/stop an instance we will get an alert in email (jasimakhtar302@gmail.com)  

