---
layout: post
title: AWS S3 파일 생성시 SNS, SQS를 통해 EC2로 전달하기 
description: AWS S3 파일 생성시 SNS, SQS를 통해 EC2로 전달하기 
modified: 2016-07-18
tags: [aws]
comments: true
image:
  feature: abstract-14.png
---
S3에 file이 upload가 되면 그 Event가 AWS SNS(Simple Notification Service)를 통해 AWS SQS(Simple Queue Service)에 전달되고, 이를 EC2에서 읽어서 파일을 가공하는 것을 살펴보자. 

### Architecture

중간에 SNS와 SQS를 쓰는 이유는 S3에서 바로 EC2로 event를 전달하면 EC2가 busy인 경우 그 event를 처리할 수 없을 수 있기 때문이다. 
SNS와 SQS를 쓰는 이유는 향후 scalable한 확장을 할 수 있고 event를 적재해 놓을 수 있기 때문이다. 
SNS를 통해 S3의 event를 다양한 종류의 SQS에 등록해 놓으면 EC2에서 동작하는 각기 다른 기능들이 맵핑된 SQS로부터 event를 읽고 처리할 수 있다. 


```
S3 Bucket --> SNS --> SQS A ==> EC2 A 기능 
                            ==> EC2 A 기능
                   -> SQS B ==> EC2 B 기능
                   .... 
                          
--> : Push
==> : Pull
```

위와 같은 구성을 위해 SQS부터 SNS, S3를 설정한 뒤 마지막으로 Ec2를 설정하도록 하자. 

### AWS SNS 설정하기

AWS SNS는 topic이라는 것에 message들이 등록되고, subscriber들에게 전달되는 것이다. 그러므로 topic을 만들고, subscriber를 설정하는 것이 필요하다. 
AWS SNS에서는 subscriber를 엔드포인트라고 칭한다. 

#### 필요한 S3 bucket 정보 확인하기

AWS SNS 설정을 위해서는 event를 만들어 내는 S3 bucket arn이 필요하다. 

1. AWS S3 Console로 이동한다. 
2. Buckets 중 event를 발생시킬 bucket을 선택하여 이름을 저장해 둔다. 아래의 SNS Topic 권한 설정시 사용된다.  


#### topic 만들기 

1. AWS SNS console로 이동한다. 
2. **Topics** > **Create Topic**을 선택하면 Create Topic 대화 상자가 나온다. 
3. **Topic name**과 **Display name**에 적당한 topic 이름을 넣는다. 예를 들어 둘 다 'imageAdded'로 한다. 
4. **Create Topic**을 클릭하면 생성된 topic이 추가된것을 확인할 수 있다. 
5. 새로운 topic을 눌러 **topic arn**을 클릭하면 topic details 페이지가 나타난다. 
6. 이제 topic의 ARN을 저장해 두자. 

##### topic 접근 권한 설정하기 

S3 bucket에서 topic 접근 권한을 가져야만 event를 전달할 수 있다. 그 접근 권한은 다음과 같이 설정가능하다. 

1. AWS SNS Console에서 생성된 topic을 선택한다. 
2. **Other topic actions**을 선택하고, **edit topic policy**를 선택한다. 
3. Edit topic policy 창에서 **Advanced View** 탭을 선택한다. 
4. 다음과 같은 접근 권한을 변경하고 **Update Policy**를 선택한다.  

예를 들면, 아래는 default 설정이다. 

```
{
  "Version": "2008-10-17",
  "Id": "__default_policy_ID",
  "Statement": [
    {
      "Sid": "__default_statement_ID",
      "Effect": "Allow",
      "Principal": {
        "AWS": "*"
      },
      "Action": [
        "SNS:GetTopicAttributes",
        "SNS:SetTopicAttributes",
        "SNS:AddPermission",
        "SNS:RemovePermission",
        "SNS:DeleteTopic",
        "SNS:Subscribe",
        "SNS:ListSubscriptionsByTopic",
        "SNS:Publish",
        "SNS:Receive"
      ],
      "Resource": "SNS-ARN",
      "Condition": {
        "StringEquals": {
          "AWS:SourceOwner": "xxxxxxxxxx"
        }
      }
    }
  ]
}
```

위의 것을 다음과 같이 변경할 수 있다. SNS-ARN과 BUCKET-NAME은 실제 것으로 변경한다. 

```
{
 "Version": "2008-10-17",
 "Id": "example-ID",
 "Statement": [
  {
   "Sid": "example-statement-ID",
   "Effect": "Allow",
   "Principal": {
     "Service": "s3.amazonaws.com"  
   },
   "Action": [
    "SNS:Publish"
   ],
   "Resource": "SNS-ARN",
   "Condition": {
      "ArnLike": {          
      "aws:SourceArn": "arn:aws:s3:*:*:BUCKET-NAME"    
    }
   }
  }
 ]
}
```

### AWS S3 설정하기

1. AWS S3 Console로 이동한다. 
2. Buckets 중 event를 발생시킬 bucket을 선택하여 **Properties**를 선택한 후 **Events**를 클릭한다. 
3. **Name**상자에 이벤트를 구성하는 이름을 입력한다. 예를 들어 'ImageAddedEvent'
4. **Events** 상자를 클릭하고 이벤트 발생 시 전달할 이벤트 유형을 선택한다. 여기서는 ObjectCreated(All)에서 Put과 CompleteMultiPartUpload를 선택하면 새로운 파일이 추가되면 이벤트가 뜬다. 
5. **Prefix**와 **Suffix**를 정할 수 있다. 예를 들어 여기서는 **Suffix**에 'jpeg jpg png gif'로 여백을 띄운 텍스트로 설정해서 확장자가 이미지 인 파일이 업로드 되었을 때만 이벤트가 발생하도록 한다. 
6. **SendTo** 항목에는 **SNS topic**을 설정한다. 
7. 해당 topic을 선택하거나, **Add SNS topic ARN**을 선택하여 **SNS Topic ARN** 상자에 앞에서 만들어 둔 SNS topic arn을 붙여 넣기 한다. 
8. **Save**를 한다. 만약 위에서 Topic Policy를 제대로 설정하지 않았다면 에러가 뜨고 저장에 실패할 것이다. 
9. 이제 Events Section 아래에 새로운 이름의 Event가 추가된 것을 확인 할 수 있다. 

### AWS SQS 설정하기 

1. AWS SQS Console로 이동한다. 
2. **Create new Queue**를 선택하고 설정 페이지에서 **Queue Name**을 입력하고(예를 들어 'ImageQueue') 나머지는 default 설정으로 **Queue Settings**을 설정한 뒤 **Create Queue**를 클릭한다. 
3. Queues Page에서 생성된 Queue를 선택한 뒤 **Queue Actions**에서 **Subscribe Queues to SNS Topic**을 선택한다. 
4. **Choose a Topic**에서 앞에서 생성된 SNS topic을 선택한 후 **Subscribe**를 선택한다. 
5. 이제 AWS SNS Console로 가서 앞에서 만든 SNS topic을 보면 **Endpoint**가 이번에 만든 Queue로 설정이 되어 있다.

### EC2에서 SQS 읽기

#### 테스트를 위해 Ubuntu AMI로 Instance 만들어 실행시키기

1. AWS EC2 Console로 이동한다. 
2. **Launch Instance**를 선택한다. 
3. Step 1: Choose an Amazon Machine Image (AMI)에서 **Ubuntu**를 선택한다. 
4. Step 2: Choose an Instance Type에서 적당한 것을 고른다. 테스트 용이면 t2.micro로 충분하다. 그 뒤 **Review and Launch**를 선택해서 바로 구동한다. 
5. Step 7: Review Instance Launch에서 테스트 용이므로 모든 설정을 default로 해서 **Launch**를 누른다. 
6. 'Select an existing key pair or create a new key pair'에서 IAM user을 위해 미리 만들어든 keypair를 사용하거나 새로 만들 수 있다. 여기서는 테스트 용이므로 EC2 Instance에 접근하기 위한 keypair를 새로 만들어보자.**Create a new key pair**를 선택하고, **key pair name**을 적당히 적고 **Download Key Pair**를 선택해서 다운로드 받자. 그리고, 'pem' 키는 분실하면 안된다.
7. **Launch Instances**를 눌러서 실행한다. 

#### EC2 인스턴스에 연결하기

1. AWS EC2 Console로 이동한다. 
2. 해당 instance의 state가 running인지 확인한다. 
3. 해당 instance를 선택하면 아래 Description에 Public IP를 저장해 둔다. 
4. 터미널에서 pem 파일이 있는 폴더로 이동한다. 
5. 'chmod 400 pem파일명'으로 pem 파일의 permission을 변경한다. 
6. [이전 글](http://hochulshin.com/dev-aws-ec2-connection-basic/)을 참조해서 그 instance로 연결한다. (ssh -i [pem파일경로] ubuntu@[ec2 instance의 publicIP] )

#### EC2에 필요한 package 설정하기  

1. 앞에서 열어둔 EC2 instance와 연결된 terminal로 이동한다. 
2. 실행에 필요한 package를 설치해야 한다. 
3. sudo apt-get update
4. sudo apt-get -y install python-pip 
5. sudo pip install boto3

#### Credentials 설정하기 

이 단계를 위해 AWS IAM console > Users > User 선택 > Security Credentials > Access Keys에서 생성한 후 id와 access key 정보를 복사한다. 

1. 앞에서 열어둔 EC2 instance와 연결된 terminal로 이동한다. 
2. ~/.aws 폴더가 있는지 확인해서 없으면 폴더를 만든다. 
3. ~/.aws/crendentials 파일을 만들고 아래와 같이 key id와 access key를 추가한다. 

```
[default]
aws_access_key_id = YOUR_KEY
aws_secret_access_key = YOUR_SECRET
```

#### Region 설정하기

1. 앞에서 열어둔 EC2 instance와 연결된 terminal로 이동한다. 
2. ~/.aws/config 파일을 만들고 아래와 같이 region을 추가한다. 서울의 경우는 다음과 같다. 

```
[default]
region=ap-northeast-2
```

#### boto3 테스트하기 

1. 앞에서 열어둔 EC2 instance와 연결된 terminal로 이동한다. 
2. python을 타이핑하여 python interpreter를 실행시킨다. 
3. 아래 명령을 python interpreter에서 실행하여 s3 bucket 목록이 잘 출력되는지 확인하자. 

```
>>> import boto3
>>> s3 = boto3.resource('s3')
>>> for bucket in s3.buckets.all():
        print(bucket.name)
```

#### AWS SQS로부터 메시지 읽기위한 Python code 

앞에서 만든 SQS 이름을 기록해 두자. 

- utils.py 파일을 만들고 다음 내용을 추가한다. 

```python
# Copyright 2015 Amazon Web Services, Inc. or its affiliates. All rights reserved.

import boto3, botocore
from botocore.exceptions import NoCredentialsError

NUM_MESSAGES = 10

def connect2Service(service):
	#Returning the connection
	try:
		return boto3.resource(service)
	except botocore.exceptions.BotoCoreError as e:                                         
		if isinstance(e, botocore.exceptions.NoCredentialsError):
			print("No AWS Credentials file found or credentials are invalid")
	return None

```

- sqs_consumer.py를 만들고 다음 내용을 추가한다. 
- 앞에서 생성한 AWS SQS이름을 QUEUE_NAME 변수의 값으로 입력한다. SLEEP, maxRetry, maxMsgs는 설정 변경 가능하다. 아래 예제는 10000번 재시도를 하는 것으로 되어 있다. 

```python
# Copyright 2015 Amazon Web Services, Inc. or its affiliates. All rights reserved.

import threading
import time
import utils

QUEUE_NAME = "QUEUE-NAME"
QUEUE_ATTR_NAME = "ApproximateNumberOfMessages"
SLEEP = 10

def Connect2sqs():
	#Connect to SQS service
	return utils.connect2Service('sqs')

#The SQSConsumer class retrieves messages from an SQS queue.
class SQSConsumer (threading.Thread):
	sqs = Connect2sqs()

	def __init__(self, threadID, name, counter):
		threading.Thread.__init__(self)
		self.threadID = threadID
		self.name = name
		self.counter = counter

	def run(self):
		print("SQSConsumer Thread running!")
		maxRetry = 10000 # MAXIMUM 10000 tries
		numMsgs = 0
		maxMsgs = self.getNumberOfMessages()
		count = 0
		print("No. of Messages to consume:", maxMsgs)
		while numMsgs < maxMsgs or count < maxRetry:
			time.sleep(SLEEP)
			numMsgs += self.consumeMessages()
			count += 1
			print("Iteration No.:", count, numMsgs)
		print("SQSConsumer Thread Stopped")
		
	def getQueue(self, sqsQueueName=QUEUE_NAME):
  #Get the SQS queue using the SQS resource object and QUEUE_NAME
		queue = None
		try:
			queue = self.sqs.get_queue_by_name(QueueName=sqsQueueName)
		except Exception as err:
			print("Error Message {0}".format(err))
		return queue

	def getNumberOfMessages(self):
		numMessages = 0
		try:
			queue = self.getQueue()
			if queue:				
			  # Receive messages from the SQS queue by using the receive_messages API method.
				# Enable long polling and set maximum number of messages to 10.
				attribs = queue.attributes
				numMessages = int(attribs.get(QUEUE_ATTR_NAME))
		except Exception as err:
			print("Error Message {0}".format(err))
		return numMessages

	def consumeMessages(self, sqsQueueName=QUEUE_NAME):
		numMsgs = 0
		try:
			queue = self.getQueue()
			if queue:
				mesgs =  queue.receive_messages(													
										AttributeNames=['All'], MaxNumberOfMessages=10, WaitTimeSeconds=20)
				if not len(mesgs):
					print("There are no messages in Queue to display")
					return numMsgs
				for mesg in mesgs:		
					# Retrieve the Attributes of a message.
					attributes = mesg.attributes		
					senderId = attributes.get('SenderId')
					sentTimestamp = attributes.get('SentTimestamp')
					
					# Retrieve the body of a message.
					bd = mesg.body
					messagebody = eval(bd)
					print(messagebody)

        	# Delete Message from the SQS queue
					self.deleteMessage(queue, mesg)
					time.sleep(1)
				numMsgs = len(mesgs)
		except Exception as err:
			print("Error Message {0}".format(err))
		return numMsgs

	def deleteMessage(self, queue, mesg):
		try:
			#Delete Message from the SQS queue
			mesg.delete() 									
			print("Message deleted from Queue")
			return True
		except Exception as err:
			print("Error Message {0}".format(err))
		return False
	
def main():
	try:
		thread1 = SQSConsumer(1, "Thread-1", 1)
		thread1.start()
	except Exception as err:
		print("Error Message {0}".format(err))
	thread1.join()
	return thread1

if __name__ == '__main__':
	main()

```

#### EC2 인스턴스에 연결해서 파일 업로드 하기

1. instace에 연결된 터미널로 instance상에 폴더를 하나 만든다. 이름은 'test'라고 임의로 정한다.  
2. Local의 terminal을 하나 더 열어 앞의 python 파일이 있는 폴더로 이동한다. 
3. [이전 글](http://hochulshin.com/dev-aws-ec2-connection-basic/)을 참조해서 앞에서 준비해 둔 python 파일들을 upload한다. 그 뒤 앞에서 instance에 연결된 터미널을 이용해 python 파일들이 모두 잘 추가되었는지 확인한다. 

```
scp -i [pem 파일 경로] utils.py ubuntu@[public 주소]:~/test/utils.py
scp -i [pem 파일 경로] sqs_consumer.py ubuntu@[public 주소]:~/test/sqs_consumer.py
```

### 실행하기 

#### EC2에서 Consumer 실행하기 

instace에 연결된 터미널에서 test 폴더에서 다음 명령으로 consumer를 실행시킨다. 

- python sqs_consumer.py

다음과 같은 메시지를 볼 수 있을 것이다. 

```
SQSConsumer Thread running!
('No. of Messages to consume:', 0)
....
```

#### S3에 파일 upload하기

AWS S3 console에서 해당 bucket으로 이동하여 upload를 선택하고, file을 upload한다. 







