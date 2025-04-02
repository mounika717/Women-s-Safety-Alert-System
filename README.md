Women’s Safety Alert System using AWS services

Overview
Implementing a *Women’s Safety Alert System* using AWS Free Tier services.

AWS Services Used:
- AWS Lambda (Backend logic, Python)
- Amazon SNS (Sending emergency SMS & emails)
- DynamoDB (Storing user & emergency contact details)
- API Gateway (Exposing API for alerts)
- S3 (Optional: Hosting a simple frontend UI)
- IAM (Setting up permissions)

---

1. Create a DynamoDB Table for Storing Emergency Contacts
   - Table Name: EmergencyContacts
   - Primary Key: user_id (String)
   - Add a sample entry:
   
```json
{
  "user_id": "user123",
  "name": "Mounika",
  "phone": "+916309176725",
  "email": "mounika@example.com"
}
## 2. Create an AWS Lambda Function to Send Emergency Alerts
Create a new role with DynamoDB Read Access and SNS Full Access.
Replace the default code with the following Python code:

import json
import boto3

dynamodb = boto3.resource('dynamodb')
table = dynamodb.Table('EmergencyContacts')
sns = boto3.client('sns')

def lambda_handler(event, context):
    user_id = event['queryStringParameters']['user_id']
    response = table.get_item(Key={'user_id': user_id})
    
    if 'Item' not in response:
        return {"statusCode": 404, "body": json.dumps({"error": "User not found"})}
    
    contact = response['Item']
    message = f"🚨 EMERGENCY ALERT! {contact['name']} needs help! Call them immediately at {contact['phone']}"
    
    sns.publish(PhoneNumber=contact['phone'], Message=message)
    sns.publish(TopicArn='arn:aws:sns:your-topic-arn', Message=message)
    
    return {"statusCode": 200, "body": json.dumps({"message": "Alert Sent!"})}

3. Create an SNS Topic for Emergency Alerts

Copy the Topic ARN and update it in your Lambda function.
Click Create Subscription → Choose Email → Enter emergency contacts.

4. Create an API Gateway for Triggering Alerts

API Gateway → Create API → Choose REST API.
Create a GET method, integrate it with the SendAlert Lambda.
Click Deploy API, create a new Stage.
Copy the Invoke URL.

5. Deploy a Frontend on S3
Create an S3 Bucket, enable Static Website Hosting.
Upload an index.html file with a button to trigger the API.

6.Testing Emergency Alert System

curl -X GET "https://your-api-url/send-alert?user_id=user123"

