# View Application History<a name="emr-cluster-application-history"></a>

You can view Spark History Server and YARN timeline service application details using the **Application user interfaces** tab of a cluster's detail page in the console\. Amazon EMR application history makes it easier for you to troubleshoot and analyze active jobs and job history\. 

The **Application user interfaces** tab provides several viewing options:
+ [Off\-cluster access to persistent application user interfaces](https://docs.aws.amazon.com/emr/latest/ManagementGuide/app-history-spark-UI.html) – Starting with Amazon EMR version 5\.25\.0, persistent application user interface links are available for Spark\. With Amazon EMR version 6\.0\.0 and later, Tez UI and the YARN timeline server also have persistent application user interfaces\. The YARN timeline server and Tez UI are open\-source applications that provide metrics for active and terminated clusters\. The Spark user interface provides details about scheduler stages and tasks, RDD sizes and memory usage, environmental information, and information about the running executors\. Persistent application UIs are run off\-cluster, so cluster information and logs are available for 30 days after an application terminates\. Unlike on\-cluster application user interfaces, persistent application UIs don't require you to set up a web proxy through a SSH connection\.
+ [On\-cluster application user interfaces](https://docs.aws.amazon.com/emr/latest/ManagementGuide/on-cluster-app-UI.html) – There are a variety of application history user interfaces that can be run on a cluster\. On\-cluster user interfaces are hosted on the master node and require you to set up a SSH connection to the web server\. On\-cluster application user interfaces keep application history for one week after an application terminates\.
+ [High\-level application history](https://docs.aws.amazon.com/emr/latest/ManagementGuide/app-history-summary.html) – With Amazon EMR version 5\.8\.0 or later, you can view a summary of application history in the EMR console, including key metrics for stage tasks and executors\. The summary of the application history is available for all YARN applications\. Additional details are provided for Spark applications, but these details are only a subset of the information available through the Spark application UI\. 