import json
import boto3

s3=boto3.client('s3')
ses=boto3.client('ses')

def lambda_handler(event, context):
    
    bucket_name="files-storages"
    file_key=event['Records'][0]['s3']['object']['key']
    
    file_obj=s3.get_object(Bucket=bucket_name,Key=file_key)
    file_data=file_obj['Body'].read().decode('utf-8')
    
    
    email_id=file_data.splitlines()
    
    for email in email_id:
        send_email(email)    
    
    return {
        'statusCode': 200,
        'body': json.dumps('Hello from Lambda!')
    }
def send_email(email):
    response=ses.send_email(
        Source='serviceprovider01010@gmail.com',
        Destination={
            'ToAddresses': [email]
        },
        Message={
            'Subject':{'Data':'Service from AWS'},
            'Body':{'Text':{'Data':"Thank you for used our service \n Please use it aga"}}
        }
        )