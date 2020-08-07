---
title: Amazon AWS使用方法
date: 2019-10-16 15:58:17
categories: [编程]
tags: [AWS,boto3]
---

Amazon AWS使用方法：

1.参照[官网](https://aws.amazon.com/cn/cli/)方法安装AWS-CLI

直接使用“pip install awscli”即可。

2.参照[boto3 Documentation](https://boto3.amazonaws.com/v1/documentation/api/latest/guide/quickstart.html)安装boto3

直接使用“pip install boto3”安装就行。

3.配置文件

先阅读官网，了解配置文件信息，进入终端：

使用"open -t ~/.aws/credentials"，先设置access token.

<!--more-->

```bash
[default]
aws_access_key_id = XXXXXXXX
aws_secret_access_key = XXXXXXXX
```

使用"open -t ~/.aws/config"，设置region

```bash
[default]
output = json
region = us-east-1
```

(注意：如果设置的region与access token属性不一致，程序执行时，会报错“<class 'botocore.exceptions.ClientError'>:An error occurred (IllegalLocationConstraintException) when calling the PutObject operation: The ap-east-1 location constraint is incompatible for the region specific endpoint this request was sent to.”)

4.AWS CLI S3命令使用

我主要使用S3，常用的命令如下所示：

1、创建bucket

```bash
aws s3 mb s3://bucket-name
```

2、列出所有bucket

```
aws s3 ls
```

3、列出指定bucket信息

```
aws s3 ls s3://bucket-name
```

3.1、如果看bucket里面的对象，后面加上recursive参数

```
aws s3 ls s3://bucket-name --recursive
```

4、删除空白bucket

```
aws s3 rb s3://bucket-name
```

5、删除非空bucket

```
aws s3 rb s3://bucket-name --force
```

上述命令查看[AWS CLI S3 command](https://docs.aws.amazon.com/zh_cn/cli/latest/userguide/cli-services-s3-commands.html)

6、删除bucket里的文件对象

```
aws s3 rm s3://bucket-name --recursive
```

其它命令参考[AWS CLI S3 rm command](https://docs.aws.amazon.com/cli/latest/reference/s3/rm.html)

