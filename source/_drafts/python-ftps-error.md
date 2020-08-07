---
title: Python FTPS连接报错
date: 2019-10-11 11:08:16
categories: [编程]
tags: [FTPS,SFTP]
---

Python环境FTPS连接存在一个Bug。

耽误了很多时间，查到原因是ftplib包没有封装ssl协议。

很奇怪的是：在我的Mac机器上，连接同一台FTP服务器，使用FileZilla没有问题；使用Paramiko也没有问题；使用FTP_TLS连接就报错"EOF error"

解决办法在第三个帖子里面。

决定放弃使用ftplib.ftp_tls方法，转向paramiko。

**参考资料**

- [Login issue with ftplib but not with Paramiko?](https://stackoverflow.com/questions/51079806/login-issue-with-ftplib-but-not-with-paramiko)
- [EOF Error with ftplib only when connecting to GoDaddy hosted server](https://stackoverflow.com/questions/40112202/eof-error-with-ftplib-only-when-connecting-to-godaddy-hosted-server)
- [Python FTP implicit TLS connection issue](https://stackoverflow.com/questions/12164470/python-ftp-implicit-tls-connection-issue)
- [FTPES - FTP over explicit TLS/SSL in Python](https://stackoverflow.com/questions/5534830/ftpes-ftp-over-explicit-tls-ssl-in-python)

