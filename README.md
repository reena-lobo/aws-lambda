# aws-lambda
s3-lambda-dynamodb
AWS LAMBDA
AWS Lambda is a serverless compute service that permits you to run code without controlling or provisioning servers. Lambda uses highly available, elastic infrastructure to run your code. It carries out compute resources-administration tasks, such as automatic scaling and capacity provisioning, operating system and server maintenance. Lambda can run code for almost any form of backend or application service.
In a serverless model, code needs to be organized into functions. Lambda only runs your functions when necessary. It scales automatically, varying from several requests a day through to thousands a second. You only pay for the compute time you use—you won’t be charged when code is not run.Lambda can run your functions when triggered by events from different AWS services.
 AWS Lambda Application with Python Using Boto3
The use case is when a file gets uploaded to S3 Bucket, a Lambda Function is to be triggered to read this file and store it in DynamoDB table. The architecture will look like below:
 






So, let’s see the steps for doing this:
Step 1: Create a JSON File
Step 2: Create S3 Bucket
Step 3:  Create DynamoDB Table
Step 4: Create Lambda Function
              create an IAM role
Step 5: create an S3 trigger.

Step 1: Create a JSON File
Create a small json file with some sample STUDENT data. This is the file we would upload to the S3 bucket. Let’s name it student.json.
-Go to vs code-create filestudent.JSON
{
    "customerId": "1234",
    "firstName": "reena",
    "lastName": "lobo",
    "status": "active",
    "isPremium": true
    }

Step 2: Create S3 Bucket
Lets create a s3 file where customer.JSON file will be uploaded.
Goto aws management Console
s3
create bucket
Bucket name: customer-data-upload
select AWS Region
Create bucket
Step 3: Create DynamoDB Table
Goto dynamodb
Create table
Table name: customer
Partition key: customerId
Create table


Step 4: Create Lambda Function

i).	First create an IAM role:
•	Login to AWS console -> IAM -> Roles -> Create role
•	Select AWS service
•	From the Use case, select ‘Lambda’ (allows lambda functions to call AWS services on your behalf
•	Click Next
•	From the Permissions policies filter,search
1.Cloudwatchfull access
2.S3full access
3.dynamodbfullaccess
•	Click Next
•	Give a name for the Role, say: LambdaRolefors3todynamodb
•	Scroll down and in the ‘Permissions policy summary’, you can see these 3 policies we selected
 
•	Click Create role
ii).	Create Lambda function
•	Login to AWS console -> Lambda -> Create function
•	Select ‘Author from scratch’
•	Give a name for function say, LamdaforS3UPLOAD
•	Runtime: Python 3.7
•	Change default execution role: Select ‘Use and existing role’
•	Select -> LambdaRolefors3todynamodb (The role we created in the first step)
•	Click Create function
•	Select the ‘Code’ from the Tab
•	Use the code given below:
import json
import boto3
dynamodb =  boto3.resource('dynamodb')
s3_client = boto3.client('s3')

table = dynamodb.Table('customer')

def lambda_handler(event, context):
   # Retrieve File Information
   bucket_name =   event['Records'][0]['s3']['bucket']['name']
   s3_file_name =  event['Records'][0]['s3']['object']['key']

   # Load Data in object
   json_object =   s3_client.get_object(Bucket=bucket_name, Key= s3_file_name)
   jsonFileReader  =   json_object['Body'].read()
   jsonDict	=   json.loads(jsonFileReader)

   # Save date in dynamodb table
   table.put_item( Item=jsonDict)
Click on deploy
Step 5: Create s3 trigger:
Click on add trigger
select a sources3
Select bucket
event typeselectall object events
Add suffix as .JSON
Click on create trigger
Now go to s3 bucket and upload the .JSON File.As soon as the file gets uploaded in S3 bucket, it triggers the customer update,It will execute the code that receives the metadata of the file through the event object and loads this file content using boto3 APIs.Then, it saves the content to the customer table in DynamoDB as below.
 





