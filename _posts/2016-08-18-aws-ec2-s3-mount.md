---
layout: post
title: AWS - EC2에서 S3 마운트하기
description: AWS - EC2에서 S3 마운트하기
modified: 2016-08-18
tags: [aws, s3, ec2]
comments: true
image:
  feature: abstract-10.png
---

<section id="table-of-contents" class="toc">
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section><!-- /#table-of-contents -->

EC2에서 S3를 마운트해서 파일을 이동시키면 permission이 이상해 지는 문제가 발생하곤 한다.
그런 문제 없이 마운트하는 방법은 다음과 같다. 

### 마운트 명령

다음 명령을 terminal에서 실행시키자. 즉시 적용이 된다. 

```
sudo s3fs {s3-bucket-name} -o allow_other -o uid=1000 -o gid=1000 -o umask=0022 /mnt/{mount-target-folder}
```

- {s3-bucket-name}: 마운트할 s3 bucket이름
- {mount-target-folder}: 마운트 될 folder이름. 미리 만들어져 있어야 한다. 

### 부팅 시 적용

ubuntu 재부팅 시에도 적용될 수 있도록 하자.
위 command를 /etc/rc.local 에 복사해 넣으면 EC2 instance시 부팅시 자동으로 적용된다.  
