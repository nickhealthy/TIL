# Today I Learned

* IoT 온라인 컨퍼런스
* Practice AWS SDK with boto3(python) -S3
* Practice AWS SDK with boto3(python) - DynamoDB

---



## IoT 온라인 컨퍼런스

#### 토픽

* 데이터 토픽과 커맨드 컨트롤 토픽을 분리시켜야 함
  * 컨맨드 컨트롤 토픽은 중요한 데이터라 토픽을 분리시켜야 함

#### 커멘드 토픽의 구현

* 클라우드 IoT구현은 인터넷을 이용하므로
  * 메시지의 순서의 변화
  * Thing의 커넥션 상태 또는 On/Off 여부

#### 쉐도우를 통해서 Thing에 반영

#### 데이터의 처리 큐 (Kinesis, SQS)의 활용

1:1 형태의 메세지 처리 : SQS

N:1 형태의 메시지 처리 : Kinesis



## Practice AWS SDK with boto3(python) - S3

```python
import logging
import boto3
from botocore.exceptions import ClientError

# 버킷 생성
def create_bucket(bucket_name, region=None):
    # Create bucket
    try:
        if region is None:
            s3_client = boto3.client('s3')
            s3_client.create_bucket(Bucket=bucket_name)
        else:
            s3_client = boto3.client('s3', region_name=region)
            location = {'LocationConstraint': region}
            s3_client.create_bucket(Bucket=bucket_name,
                                    CreateBucketConfiguration=location)
    except ClientError as e:
        logging.error(e)
        return False
    return True

# 버킷 조회
# Retrieve the list of existing buckets
s3 = boto3.client('s3')
response = s3.list_buckets()

# Output the bucket names
print('Existing buckets:')
for bucket in response['Buckets']:
    print(f'  {bucket["Name"]}')

# 버킷 파일 업로드
def upload_file(file_name, bucket, object_name=None):
    """Upload a file to an S3 bucket

    :param file_name: File to upload
    :param bucket: Bucket to upload to
    :param object_name: S3 object name. If not specified then file_name is used
    :return: True if file was uploaded, else False
    """

    # If S3 object_name was not specified, use file_name
    if object_name is None:
        object_name = file_name

    # Upload the file
    s3_client = boto3.client('s3')
    try:
        response = s3_client.upload_file(file_name, bucket, object_name)
    except ClientError as e:
        logging.error(e)
        return False
    return True

# upload_file('s30_transter.txt', 'helloworld-newbuckettest')

s3 = boto3.client('s3')
s3.upload_file(
    's30_transter.txt', 'helloworld-newbuckettest', 's303_transter.txt',
    ExtraArgs={'ACL': 'public-read'}
)

# with open("s30_transter.txt", "rb") as f:
#     s3.upload_fileobj(f, "helloworld-newbuckettest", "s301_transter.txt")	
```



## Practice AWS SDK with boto3(python) - DynamoDB

```python
import boto3

# Get the service resource.
dynamodb = boto3.resource('dynamodb')

# Create the DynamoDB table.
table = dynamodb.create_table(
    TableName='usersz',
    KeySchema=[
        {
            'AttributeName': 'username',
            'KeyType': 'HASH'
        },
        {
            'AttributeName': 'last_name',
            'KeyType': 'RANGE'
        }
    ],
    AttributeDefinitions=[
        {
            'AttributeName': 'username',
            'AttributeType': 'S'
        },
        {
            'AttributeName': 'last_name',
            'AttributeType': 'S'
        },
    ],
    ProvisionedThroughput={
        'ReadCapacityUnits': 5,
        'WriteCapacityUnits': 5
    }
)

# Wait until the table exists.
table.meta.client.get_waiter('table_exists').wait(TableName='users')

# Print out some data about the table.
print(table.item_count)
print(table.creation_date_time)
```

