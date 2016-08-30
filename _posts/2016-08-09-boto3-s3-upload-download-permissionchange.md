---
layout: post
title: AWS - Boto3를 이용한 S3 다루기 
description: AWS - Boto3를 이용한 S3 다루기 
modified: 2016-08-09
tags: [aws, boto3, s3]
comments: true
image:
  feature: abstract-11.png
---

<section id="table-of-contents" class="toc">
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section><!-- /#table-of-contents -->

Boto3를 이용해 S3에 접근해서 업로드, 다운로그, 그리고 permission을 변경하는 것을 알아보자. 

## Reference

[Boto3의 client](http://boto3.readthedocs.io/en/latest/reference/services/s3.html#client) API

## code

Sample라는 bucket에 download.jpg라는 파일이 있을 때 이를 다운로드하고, 같은 파일을 upload.jpg로 바꾸고 퍼미션을 public-read로 바꾸는 예제이다. 


```python
import boto3

def download(s3, bucket, obj, local_file_path):
    s3.download_file(bucket, obj, local_file_path)

def upload(s3, local_file_path, bucket, obj):
    s3.upload_file(local_file_path, bucket, obj)

def make_public_read(s3, bucket, key):
    s3.put_object_acl(ACL='public-read', Bucket=bucket, Key=key)

if __name__ == "__main__":
    bucket = "Sample"
    obj = "download.jpg"
    local_file_path = "download.jpg"
    s3 = boto3.client('s3')
    download(s3, bucket, obj, local_file_path)
    upload(s3, local_file_path, bucket, "upload.jpg")
    make_public_read(s3, bucket, "upload.jpg")

```
