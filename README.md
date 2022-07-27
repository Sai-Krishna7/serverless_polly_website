# serverless_polly_website
Serverless Website that uses Amazon Polly to convert text to speech.

Overview:
Do 2 things:
1. Take the text and submit to S3, where there exists a static website.
This triggers a Lamda function through an api call and then store the data in DynamoDB.
Then, it triggers an SNS notification thus triggering a new lambda function that obtains the text file and passes it to Amazon Polly which wll return the voice as an mp3 file. Pass it back to lambda to be stored in S3 as a mp3 file.

2. Users want to hear the mp3 file. Go to website trigger a gateway call, trigger a get lambda request and restore saved data from DynamoDB.
Then use Alexa Skill to read out the mp3 file stored in DynamoDB.

Steps:
1. DynamoDB:
    Create a table called posts to act as a database and store the mp3 file info for example.

2. S3 buckets:
    Create 2 new S3 buckets. One for the website that hosts a static webpage and the other to store the text and mp3 files.
    Within that for the website S3 bucket adjust the bucket poilicy so that it allows getObject requests in read only.

    3. Create an SNS topic 

    4. Create IAM role to allow lambda funcitons to commmunicate with S3, SNS and DynamoDB. Lambda should now have permission to use and execute.
        * Faced issues with testing the Lambda function. Had to convert the boto3 code to support python 3.6. 
        * Then added AWSDynamoDBFullAccess and AWSSNSFullAccess Policies to the Post_Reader_NewPost iam role.
    - Post_Reader_NewPost takes this text store it in DynamoDB and pass it to SNS which in turn triggers another Lambda function. 
    - Tested PostReader_GetPost lambda function with custom testing template. Imported boto3 module and saved it in a venv and uploaded the python scipt file as a zip file with necessary dependencies.
    Passed it to the Lambda function using an predesigned S3 location url since file size was too large for inline editing.

    5. API Gateway - Using REST API's to create a POST http request to the PostReader_NewPost Lambda function.