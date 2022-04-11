# AWS-Boto-in-Python
**Practicing on Integrate to AWS with data workflow, upload data, use triggers and some analysis**
_________________________________
### S3

> Create first boto3 client and check out what buckets already exist in S3.

> Credentials:
  - Region Name = `us-east-1`
  - AWS Access Key = `IAmAFakeKey`
  - AWS Secret Access Key = `IAmAFakeSecretBecauseWeAreUsingMoto`
```
import boto3

# Generate the boto3 client for interacting with S3
# Set up AWS credentials 
s3 = boto3.client('s3', region_name='us-east-1', aws_access_key_id='IAmAFakeKey', 
                                        aws_secret_access_key='IAmAFakeSecretBecauseWeAreUsingMoto')
                
# List the buckets
buckets = s3.list_buckets()

# Print the buckets
print(buckets)
```
_________________________________

> Initialize a boto3 client for S3, and another client for SNS.
```
import boto3

# Generate the boto3 client for interacting with S3 and SNS
s3 = boto3.client('s3', region_name='us-east-1', 
                         aws_access_key_id='IAmAFakeKey', 
                         aws_secret_access_key='IAmAFakeSecretBecauseWeAreUsingMoto')

sns = boto3.client('sns', region_name='us-east-1', 
                         aws_access_key_id='IAmAFakeKey', 
                         aws_secret_access_key='IAmAFakeSecretBecauseWeAreUsingMoto')

# List S3 buckets and SNS topics
buckets = s3.list_buckets()
topics = sns.list_topics()

# Print out the list of SNS topics
print(topics)
```
_________________________________

- Create a boto3 client to S3.
- Create 'gim-staging', 'gim-processed' and 'gim-test' buckets.
- Print the response from creating the 'gim-staging' bucket.
- Get the buckets from S3.
- Iterate over the bucket key from response to access the list of buckets.
- Print the name of each bucket.
```
import boto3

# Create boto3 client to S3
s3 = boto3.client('s3', region_name='us-east-1', 
                         aws_access_key_id='IAmAFakeKey', 
                         aws_secret_access_key='IAmAFakeSecretBecauseWeAreUsingMoto')

# Create the buckets
response_staging = s3.create_bucket(Bucket='gim-staging')
response_processed = s3.create_bucket(Bucket='gim-processed')
response_test = s3.create_bucket(Bucket='gim-test')

# Print out the response
print(response_staging)

# Get the list_buckets response
response = s3.list_buckets()

# Iterate over Buckets from .list_buckets() response
for bucket in response['Buckets']:
  
  	# Print the Name for each bucket
    print(bucket['Name'])
```
_________________________________

- Delete the 'gim-test' bucket.
- Get the list of buckets from S3.
- Print the name of each bucket.
```
import boto3

# Create boto3 client to S3
s3 = boto3.client('s3', region_name='us-east-1', 
                         aws_access_key_id='IAmAFakeKey', 
                         aws_secret_access_key='IAmAFakeSecretBecauseWeAreUsingMoto')

# Delete the gim-test bucket
s3.delete_bucket(Bucket='gim-test')

# Get the list_buckets response
response = s3.list_buckets()

# Print each Buckets Name
for bucket in response['Buckets']:
    print(bucket['Name'])

```
_________________________________

- Upload 'final_report.csv' to the 'gid-staging' bucket with the key '2019/final_report_01_01.csv'.
- Get the object metadata and store it in response.
- Print the object size in bytes.
- List only objects that start with '2018/final_' in 'gid-staging' bucket.
- Iterate over the objects, deleting each one.
- Print the keys of remaining objects in the bucket.
```
import boto3

s3 = boto3.client('s3', region_name='us-east-1', 
                         aws_access_key_id='IAmAFakeKey', 
                         aws_secret_access_key='IAmAFakeSecretBecauseWeAreUsingMoto')

# Upload final_report.csv to gid-staging
# Set filename and key
s3.upload_file(Bucket='gid-staging',Filename='final_report.csv', Key='2019/final_report_01_01.csv')

# Get object metadata and print it
response = s3.head_object(Bucket='gid-staging', Key='2019/final_report_01_01.csv')
                       
# Print the size of the uploaded object
print(response['ContentLength'])


# List only objects that start with '2018/final_'
response = s3.list_objects(Bucket='gid-staging', 
                           Prefix='2018/final_')

# Iterate over the objects
if 'Contents' in response:
  for obj in response['Contents']:
      # Delete the object
      s3.delete_object(Bucket='gid-staging', Key=obj['Key'])

# Print the keys of remaining objects in the bucket
response = s3.list_objects(Bucket='gid-staging')

for obj in response['Contents']:
  	print(obj['Key'])

```
_________________________________

- List the objects in 'gid-staging' bucket starting with '2019/final_'.
- For each file in the response, give it an ACL of 'public-read'.
- Print the Public Object URL of each object.
```
import boto3

# Create boto3 client to S3
s3 = boto3.client('s3', region_name='us-east-1', 
                         aws_access_key_id='IAmAFakeKey', 
                         aws_secret_access_key='IAmAFakeSecretBecauseWeAreUsingMoto')

# List only objects that start with '2019/final_'
response = s3.list_objects(
    Bucket='gid-staging', Prefix='2019/final_')

# Iterate over the objects
for obj in response['Contents']:

    # Give each object ACL of public-read
    s3.put_object_acl(Bucket='gid-staging', 
                      Key=obj['Key'], 
                      ACL='public-read')
    
    # Print the Public Object URL for each object
    print("https://{}.s3.amazonaws.com/{}".format( 'gid-staging', obj['Key']))
```
_________________________________

- Generate a presigned URL for final_report.csv that lasts 1 hour and allows the user to get the object.
- Print out the generated presigned URL.
```
import boto3

# Create boto3 client to S3
s3 = boto3.client('s3', region_name='us-east-1', 
                         aws_access_key_id='IAmAFakeKey', 
                         aws_secret_access_key='IAmAFakeSecretBecauseWeAreUsingMoto')
                           
# Generate presigned_url for the uploaded object
share_url = s3.generate_presigned_url(
  # Specify allowable operations
  ClientMethod='get_object',
  # Set the expiration time
  ExpiresIn=3600,
  # Set bucket and shareable object's name
  Params={'Bucket': 'gid-staging','Key': 'final_report.csv'}
)

# Print out the presigned URL
print(share_url)
```
_________________________________
- List only objects that start with '2018/final_' in 'gid-staging' bucket.
- For each file in response, load the object from S3.
- Load the object's StreamingBody into pandas, and append to df_list.
- Concatenate all the DataFrames with pandas.
- Preview the resulting DataFrame.
```
import boto3
import pandas as pd

# Create boto3 client to S3
s3 = boto3.client('s3', region_name='us-east-1', 
                         aws_access_key_id='IAmAFakeKey', 
                         aws_secret_access_key='IAmAFakeSecretBecauseWeAreUsingMoto')
                         
                         
# List only objects that start with '2018/final_'                         
response = s3.list_objects(Bucket='gid-staging', Prefix='2018/final_')

df_list =  [ ] 

for file in response['Contents']:
    # For each file in response load the object from S3
    obj = s3.get_object(Bucket='gid-requests', Key=file['Key'])
    # Load the object's StreamingBody with pandas
    obj_df = pd.read_csv(obj['Body'])
    # Append the resulting DataFrame to list
    df_list.append(obj_df)

# Concat all the DataFrames with pandas
df = pd.concat(df_list)

# Preview the resulting DataFrame
df.head()
```
_________________________________

- Upload the 'lines.html' file to 'datacamp-public' bucket.
- Specify the proper content type for the uploaded file.
- Specify that the file should be public.
- Print the Public Object URL for the new file.
```
import boto3
 
# Generate the boto3 client for interacting with S3
# Set up AWS credentials 
s3 = boto3.client('s3', region_name='us-east-1', aws_access_key_id='IAmAFakeKey', aws_secret_access_key='IAmAFakeSecretBecauseWeAreUsingMoto')
                
# Upload the lines.html file to S3
s3.upload_file(Filename='lines.html', 
               # Set the bucket name
               Bucket='datacamp-public', Key='index.html',
               # Configure uploaded file
               ExtraArgs = {
                 # Set proper content type
                 'ContentType':'text/html',
                 # Set proper ACL
                 'ACL': 'public-read'})

# Print the S3 Public Object URL for the new file.
print("http://{}.s3.amazonaws.com/{}".format('datacamp-public', 'index.html'))
```
_________________________________
### SNS

- Initialize the boto3 client for SNS.
- Create the 'city_alerts' topic and extract its topic ARN.
- Re-create the 'city_alerts' topic and extract its topic ARN with a one-liner.
- Verify the two topic ARNs match.
```
import boto3

# Initialize boto3 client for SNS
# Set up AWS credentials 
s3 = boto3.client('sns', region_name='us-east-1', aws_access_key_id='IAmAFakeKey', 
                   aws_secret_access_key='IAmAFakeSecretBecauseWeAreUsingMoto')
                
# Create the city_alerts topic
response = sns.create_topic(Name="city_alerts")
c_alerts_arn = response['TopicArn']

# Re-create the city_alerts topic using a oneliner
c_alerts_arn_1 = sns.create_topic(Name='city_alerts')['TopicArn']

# Compare the two to make sure they match
print(c_alerts_arn == c_alerts_arn_1)
```
_________________________________
- Get the current list of topics.
- For every topic ARN, if it doesn't have the word 'critical' in it, delete it.
- Print the list of remaining critical topics.
```
import boto3

# Initialize boto3 client for SNS
# Set up AWS credentials 
s3 = boto3.client('sns', region_name='us-east-1', aws_access_key_id='IAmAFakeKey', 
                   aws_secret_access_key='IAmAFakeSecretBecauseWeAreUsingMoto')
                
# Get the current list of topics
topics = sns.list_topics()['Topics']

for topic in topics:
  # For each topic, if it is not marked critical, delete it
  if "critical" not in topic['TopicArn']:
    sns.delete_topic(TopicArn=topic['TopicArn'])
    
# Print the list of remaining critical topics
print(sns.list_topics()['Topics'])
```
_________________________________

- Use Topic_Arn = `arn:aws:sns:us-east-1:123456789012:streets_critical`
- Use Phone Number = `+16196777733`
- Use Email = `eblock@sandiegocity.gov`
```
import boto3

# Initialize boto3 client for SNS
# Set up AWS credentials 
s3 = boto3.client('sns', region_name='us-east-1', aws_access_key_id='IAmAFakeKey', 
                   aws_secret_access_key='IAmAFakeSecretBecauseWeAreUsingMoto')
                
# Subscribe Elena's phone number to streets_critical topic
resp_sms = sns.subscribe(
  TopicArn = 'arn:aws:sns:us-east-1:123456789012:streets_critical', 
  Protocol='sms', Endpoint="+16196777733")

# Print the SubscriptionArn
print(resp_sms['SubscriptionArn'])

# Subscribe Elena's email to streets_critical topic.
resp_email = sns.subscribe(
  TopicArn = 'arn:aws:sns:us-east-1:123456789012:streets_critical', 
  Protocol='email', Endpoint="eblock@sandiegocity.gov")

# Print the SubscriptionArn
print(resp_email['SubscriptionArn'])
```
_________________________________

- Use `contacts` DataFrame to get the Emails
- For each element in the `Email` column of `contacts`, create a subscription to the `streets_critical` Topic.
- List subscriptions for the `streets_critical` Topic and convert them to a DataFrame.
- Preview the DataFrame.
```
import boto3
import pandas as pd

# Initialize boto3 client for SNS
# Set up AWS credentials 
s3 = boto3.client('sns', region_name='us-east-1', aws_access_key_id='IAmAFakeKey', 
                   aws_secret_access_key='IAmAFakeSecretBecauseWeAreUsingMoto')
  
#print(contacts)  
# For each email in contacts, create subscription to street_critical
for email in contacts['Email']:
  sns.subscribe(TopicArn = 'arn:aws:sns:us-east-1:123456789012:streets_critical',
                # Set channel and recipient
                Protocol = 'email',
                Endpoint = email)

# List subscriptions for streets_critical topic, convert to DataFrame
response = sns.list_subscriptions_by_topic(
  TopicArn = 'arn:aws:sns:us-east-1:123456789012:streets_critical')
subs = pd.DataFrame(response['Subscriptions'])

# Preview the DataFrame
subs.head()
```
_________________________________

- List subscriptions for 'streets_critical' topic.
- For each subscription, if the protocol is 'sms', unsubscribe.
- List subscriptions for 'streets_critical' topic in one line.
- Print the subscriptions
```
import boto3

# Initialize boto3 client for SNS
# Set up AWS credentials 
s3 = boto3.client('sns', region_name='us-east-1', aws_access_key_id='IAmAFakeKey', 
                   aws_secret_access_key='IAmAFakeSecretBecauseWeAreUsingMoto')
                
# List subscriptions for streets_critical topic.
response = sns.list_subscriptions_by_topic(
  TopicArn = 'arn:aws:sns:us-east-1:123456789012:streets_critical')

# For each subscription, if the protocol is SMS, unsubscribe
for sub in response['Subscriptions']:
  if sub['Protocol'] == 'sms':
	  sns.unsubscribe(SubscriptionArn=sub['SubscriptionArn'])

# List subscriptions for streets_critical topic in one line
subs = sns.list_subscriptions_by_topic(
  TopicArn='arn:aws:sns:us-east-1:123456789012:streets_critical')['Subscriptions']

# Print the subscriptions
print(subs)

```
_________________________________

### Sending Messages

- potholes=150
- If there are over 100 potholes, send a message with the current backlog count.
- Create the email subject to also include the current backlog count.
- Publish message to the streets_critical Topic ARN.
```
import boto3

# Initialize boto3 client for SNS
# Set up AWS credentials 
s3 = boto3.client('sns', region_name='us-east-1', aws_access_key_id='IAmAFakeKey', 
                   aws_secret_access_key='IAmAFakeSecretBecauseWeAreUsingMoto')
                
potholes=150
# If there are over 100 potholes, create a message
if potholes > 100:
  # The message should contain the number of potholes.
  message = "There are {} potholes!".format(potholes)
  # The email subject should also contain number of potholes
  subject = "Latest pothole count is {}".format(potholes)

  # Publish the email to the streets_critical topic
  sns.publish(
    TopicArn = 'arn:aws:sns:us-east-1:123456789012:streets_critical',
    # Set subject and message
    Message = message,
    Subject = subject
  )

```
_________________________________
### AWS Rekognition (Recognition)
- Create a boto3 client to S3.
- Upload the image `report.jpg` to the `datacamp-img` bucket
- Use the Rekognition client to detect the labels for `report.jpg`. Return a maximum of 1 label.
```
# Generate the boto3 client for interacting with S3
# Set up AWS credentials 
import boto3

s3 = boto3.client('s3', region_name='us-east-1', aws_access_key_id='IAmAFakeKey', aws_secret_access_key='IAmAFakeSecretBecauseWeAreUsingMoto')
                
s3.upload_file(Filename='report.jpg', Key='report.jpg',   Bucket='datacamp-img')

rekog = boto3.client('rekognition',region_name='us-east-1', aws_access_key_id='IAmAFakeKey', aws_secret_access_key='IAmAFakeSecretBecauseWeAreUsingMoto')

# Use Rekognition client to detect labels
image1_response = rekog.detect_labels(
    # Specify the image as an S3Object; Return one label
    Image='report.jpg', MaxLabels=1)

# Print the labels
print(image1_response['Labels'])

```
_________________________________

- Create a boto3 client to S3.
- Upload the image `report.jpg` to the `datacamp-img` bucket
- Use the Rekognition client to detect the labels for `report.jpg`. Return a maximum of 100 labels
- Iterate over each element of the 'Labels' key in response.
- Once you encounter a label with the name 'Cat', iterate over the label's instance.
- If an instance's confidence level exceeds 85, increment cats count by 1.
- Print the final cat count.
```
# Generate the boto3 client for interacting with S3
# Set up AWS credentials 
import boto3

s3 = boto3.client('s3', region_name='us-east-1', aws_access_key_id='IAmAFakeKey', aws_secret_access_key='IAmAFakeSecretBecauseWeAreUsingMoto')
                
s3.upload_file(Filename='report.jpg', Key='report.jpg',   Bucket='datacamp-img')

rekog = boto3.client('rekognition',region_name='us-east-1', aws_access_key_id='IAmAFakeKey', aws_secret_access_key='IAmAFakeSecretBecauseWeAreUsingMoto')

# Use Rekognition client to detect labels
response = rekog.detect_labels(
    # Specify the image as an S3Object; Return one label
    Image='report.jpg', MaxLabels=100)

# Create an empty counter variable
cats_count = 0
# Iterate over the labels in the response
for label in response['Labels']:
    # Find the cat label, look over the detected instances
    if label['Name'] == 'Cat':
        for instance in label['Instances']:
            # Only count instances with confidence > 85
            if (instance['Confidence'] > 85):
                cats_count += 1
# Print count of cats
print(cats_count)
```
_________________________________
- Create a boto3 client to S3.
- Upload the image `txt_test.jpg` to the `datacamp-img` bucket
- Iterate over each detected text in response, and append each detected text to words if the text's type is 'WORD'.
```
# Generate the boto3 client for interacting with S3
# Set up AWS credentials 
import boto3

s3 = boto3.client('s3', region_name='us-east-1', aws_access_key_id='IAmAFakeKey', aws_secret_access_key='IAmAFakeSecretBecauseWeAreUsingMoto')
                
s3.upload_file(Filename='txt_test.jpg', Key='txt_test.jpg',   Bucket='datacamp-img')

rekog = boto3.client('rekognition',region_name='us-east-1', aws_access_key_id='IAmAFakeKey', aws_secret_access_key='IAmAFakeSecretBecauseWeAreUsingMoto')

# Use Rekognition client to detect labels
response =rekog.detect_text(Image={'S3Object':{'Bucket': 'datacamp-img', 'Name': 'txt_test.jpg'}})

words = []
# Iterate over the TextDetections in the response dictionary
for text_detection in response['TextDetections']:
  	# If TextDetection type is WORD, append it to words list
    if text_detection['Type'] == 'WORD':
        # Append the detected text
        words.append(text_detection['DetectedText'])
# Print out the words list
print(words)

```
_________________________________
### Comprehend

> In this exercise, you will help Sam understand sentiment across many different languages. This will help the City understand how different communities are relating to scooters, something that will affect the votes of City Council members; She has already loaded the CSV into the scooter_df variable.

> Instructions:
- For every DataFrame row, detect the dominant language.
- Use the detected language to determine the sentiment of the description.
- Group the DataFrame by the 'sentiment' and 'lang' columns in that order.

```
for index, row in scooter_requests.iterrows():
  	# For every DataFrame row
    desc = scooter_requests.loc[index, 'public_description']
    if desc != '':
      	# Detect the dominant language
        resp = comprehend.detect_dominant_language(Text=desc)
        lang_code = resp['Languages'][0]['LanguageCode']
        scooter_requests.loc[index, 'lang'] = lang_code
        # Use the detected language to determine sentiment
        scooter_requests.loc[index, 'sentiment'] = comprehend.detect_sentiment(
          Text=desc, 
          LanguageCode=lang_code)['Sentiment']
# Perform a count of sentiment by group.
counts = scooter_requests.groupby(['sentiment', 'lang']).count()
counts.head()
```

