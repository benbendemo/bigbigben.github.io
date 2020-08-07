---
title: Airflow使用的一些心得
date: 2019-10-17 17:41:47
categories: [编程]
tags: [Python,Airflow]
---

最近一直在做Python Airflow的开发项目，从看着中文文档起步到在StackOverflow里翻箱倒柜，一路踩坑无数，分享一点自己的心得吧。

1、Backfill的理解

Backfill要这样理解，比如，你设置DAG从1月1日开始每天执行一次，假设你的Airflow流程在1月1日执行完一次后，你把它关掉了。今天是1月5日，如果开启了Backfill（默认是开启的），1月5日任务执行前，会自动把跳过去的1月2日、1月3日、1月4日这三次任务补上。这就叫做Backfill。

有两个地方可以设置Backfill参数：

第一个是airflow.cfg文件里的“catchup_by_default = True”参数，默认True表示开启了Backfill；

第二种方法是在DAG里设置catchup参数，设置为False表示关掉Backfill功能。

```python
dag = DAG(
    dag_id='worldcheck_file_download',
    default_args=args,
    dagrun_timeout=timedelta(minutes=20),
    catchup=False,
    schedule_interval="*/10 06,07,08,09,10,11,12,13,14,15,16,17 * * *"
)
```

Backfill的设置，参考《[How to prevent airflow from backfilling dag runs?](https://stackoverflow.com/questions/38751872/how-to-prevent-airflow-from-backfilling-dag-runs)》一文。



2、关于DAG里参数设置

```python
args = {
    'owner': 'airflow',
    'start_date': airflow.utils.dates.days_ago(2),
    'email': ['test@example.com'],
    'email_on_failure': True,
    'email_on_retry': False,
    'retries': 1,
    'retry_delay': timedelta(seconds=60)
}

dag = DAG(
    dag_id='worldcheck_file_download',
    default_args=args,
    dagrun_timeout=timedelta(minutes=20),
    catchup=False,
    schedule_interval="*/10 06,07,08,09,10,11,12,13,14,15,16,17 * * *"
)
```

start_date参数一般习惯设置为当前日期的两天之前；这是因为如果你将start_date设置为今天的日期，第一次执行时间并不是今天，而是明天；

dagrun_timeout参数设置了整个DAG执行的总时间，不管这个DAG里包含多少个Task，只要整个执行时间超过20分钟，DAG自动会被更新为failed；

3、PythonOperator执行状态为success，但作业里面有报错Error，使用“raise AirflowException”

查看[AirflowException事例参考](https://programtalk.com/python-examples/airflow.exceptions.AirflowException/)。



参考资料：

https://stackoverflow.com/questions/38751872/how-to-prevent-airflow-from-backfilling-dag-runs