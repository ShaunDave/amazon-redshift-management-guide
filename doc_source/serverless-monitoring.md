# Monitoring queries and workloads with Amazon Redshift Serverless<a name="serverless-monitoring"></a>

## Monitoring queries and workload with Amazon Redshift Serverless<a name="serverless-monitoring-overview"></a>

You can monitor your Amazon Redshift Serverless queries and workload with the provided system views\. 

### Granting access to monitor queries<a name="serverless-monitor-access"></a>

A superuser can provide access to users who aren't superusers so that they can perform query monitoring for all users\. First, you add a policy for a user or a role to provide query monitoring access\. Then, you grant query monitoring permission to the user or role\. 

**To add the query monitoring policy**

1. Choose [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. Under **Access management**, choose **Policies**\.

1. Choose **Create Policy**\.

1. Choose **JSON** and paste the following policy definition\.

   ```
   {
   "Version": "2012-10-17",
   "Statement": [
   {
   "Effect": "Allow",
   "Action": [
       "redshift-data:ExecuteStatement",
       "redshift-data:DescribeStatement",
       "redshift-data:GetStatementResult",
       "redshift-data:ListDatabases"
   ],
   "Resource": "*"
   },
   {
   "Effect": "Allow",
   "Action": "redshift-serverless:GetCredentials",
   "Resource": "*"
   }
   ]
   }
   ```

1. Choose **Review policy**\.

1. For **Name**, enter a name for the policy, such as `query-monitoring`\.

1. Choose **Create policy**\.

1. Under **Policies**, choose the policy you created\.

1. Under **Actions**, choose **Attach**\.

1. Choose the users or roles to which you want to attach the policy and choose **Attach policy**\.

**To grant query monitoring permission for a user**

Users with `sys:monitor` permission can view all queries\. In addition, users with `sys:operator` permission can cancel queries, analyze query history, and perform vacuum operations\.

1. Enter the following command to provide system monitor access, where *user\-name* is the name of the user for whom you want to provide access\.

   ```
   grant "sys:monitor" to "IAM:user-name";
   ```

1. \(Optional\) Enter the following command to provide system operator access, where *user\-name* is the name of the user for whom you want to provide access\.

   ```
   grant "sys:operator" to "IAM:user-name";
   ```

**To grant query monitoring permission for a role**

Users with a role that has `sys:monitor` permission can view all queries\. In addition, users with a role that has `sys:operator` permission can cancel queries, analyze query history, and perform vacuum operations\.

1. Enter the following command to provide system monitor access, where *role\-name* is the name of the role for which you want to provide access\.

   ```
   grant "sys:monitor" to "IAMR:role-name";
   ```

1. \(Optional\) Enter the following command to provide system operator access, where *role\-name* is the name of the role for which you want to provide access\.

   ```
   grant "sys:operator" to "IAMR:role-name";
   ```

### Monitoring views<a name="serverless_views-monitoring"></a>

*Monitoring views* are system views in Amazon Redshift Serverless that are used to monitor query and workload usage\. These views are located in the `pg_catalog` schema\. The system views available have been designed to give you the information needed to monitor Amazon Redshift Serverless, which is much simpler than that needed for provisioned clusters\. The SYS system views have been designed to work with Amazon Redshift Serverless\. To display the information provided by these views, run SQL SELECT statements\.

System views are defined to support the following monitoring objectives\.

**Workload monitoring**  
You can monitor your query activities over time to:  
+ Understand workload patterns, so you know what is normal \(baseline\) and what is within business service level agreements \(SLAs\)\.
+ Rapidly identify deviation from normal, which might be a transient issue or something that warrants further action\.

**Data load and unload monitoring**  
Data movement in and out of Amazon Redshift Serverless is a critical function\. You use COPY and UNLOAD to load or unload data, and you must monitor progress closely in terms of bytes/rows transferred and files completed to track adherence to business SLAs\. This is normally done by running system table queries frequently \(that is, every minute\) to track progress and raise alerts for investigation/corrective action if significant deviations are detected\.

**Failure and problem diagnostics**  
There are cases where you must take action for query or runtime failures\. Developers rely on system tables to self\-diagnose issues and determine correct remedies\.

**Performance tuning**  
You might need to tune queries that are not meeting SLA requirements either from the start, or have degraded over time\. To tune, you must have runtime details including run plan, statistics, duration, and resource consumption\. You need baseline data for offending queries to determine the cause for deviation and to guide you how to improve performance\.

**User objects event monitoring**  
You need to monitor actions and activities on user objects, such as refreshing materialized views, vacuum, and analyze\. This includes system\-managed events like auto\-refresh for materialized views\. You want to monitor when an event ends if it is user initiated, or the last successful run if system initiated\.

**Usage tracking for billing**  
You can monitor your usage trends over time to:  
+ Inform budget planning and business expansion estimates\.
+ Identify potential cost\-saving opportunities like removing cold data\.

You can't query STL, STV, SVCS, SVL, and some SVV system tables and views with Amazon Redshift Serverless, except the following: 
+ [SVV\_ALL\_COLUMNS](https://docs.aws.amazon.com/redshift/latest/dg/r_SVV_ALL_COLUMNS.html) 
+ [SVV\_ALL\_SCHEMAS](https://docs.aws.amazon.com/redshift/latest/dg/r_SVV_ALL_SCHEMAS.html) 
+ [SVV\_ALL\_TABLES](https://docs.aws.amazon.com/redshift/latest/dg/r_SVV_ALL_TABLES.html) 
+ [SVV\_COLUMNS](https://docs.aws.amazon.com/redshift/latest/dg/r_SVV_COLUMNS.html) 
+ [SVV\_DATASHARES](https://docs.aws.amazon.com/redshift/latest/dg/r_SVV_DATASHARES.html) 
+ [SVV\_DATASHARE\_CONSUMERS](https://docs.aws.amazon.com/redshift/latest/dg/r_SVV_DATASHARE_CONSUMERS.html) 
+ [SVV\_DATASHARE\_OBJECTS](https://docs.aws.amazon.com/redshift/latest/dg/r_SVV_DATASHARE_OBJECTS.html) 
+ [SVV\_EXTERNAL\_COLUMNS](https://docs.aws.amazon.com/redshift/latest/dg/r_SVV_EXTERNAL_COLUMNS.html) 
+ [SVV\_EXTERNAL\_DATABASES](https://docs.aws.amazon.com/redshift/latest/dg/r_SVV_EXTERNAL_DATABASES.html) 
+ [SVV\_EXTERNAL\_PARTITIONS](https://docs.aws.amazon.com/redshift/latest/dg/r_SVV_EXTERNAL_PARTITIONS.html) 
+ [SVV\_EXTERNAL\_SCHEMAS](https://docs.aws.amazon.com/redshift/latest/dg/r_SVV_EXTERNAL_SCHEMAS.html) 
+ [SVV\_EXTERNAL\_TABLES](https://docs.aws.amazon.com/redshift/latest/dg/r_SVV_EXTERNAL_TABLES.html) 
+ [SVV\_REDSHIFT\_COLUMNS](https://docs.aws.amazon.com/redshift/latest/dg/r_SVV_REDSHIFT_COLUMNS.html) 
+ [SVV\_REDSHIFT\_DATABASES](https://docs.aws.amazon.com/redshift/latest/dg/r_SVV_REDSHIFT_DATABASES.html) 
+ [SVV\_REDSHIFT\_FUNCTIONS](https://docs.aws.amazon.com/redshift/latest/dg/r_SVV_REDSHIFT_FUNCTIONS.html) 
+ [SVV\_REDSHIFT\_SCHEMAS](https://docs.aws.amazon.com/redshift/latest/dg/r_SVV_REDSHIFT_SCHEMAS.html) 
+ [SVV\_REDSHIFT\_TABLES](https://docs.aws.amazon.com/redshift/latest/dg/r_SVV_REDSHIFT_TABLES.html) 
+ [SVV\_TABLES](https://docs.aws.amazon.com/redshift/latest/dg/r_SVV_TABLES.html) 
+ [SVV\_TABLE\_INFO](https://docs.aws.amazon.com/redshift/latest/dg/r_SVV_TABLE_INFO.html) 
+ [SVV\_TRANSACTIONS](https://docs.aws.amazon.com/redshift/latest/dg/r_SVV_TRANSACTIONS.html) 

You can query the following SYS system views to monitor Amazon Redshift Serverless\. 
+ [SYS\_QUERY\_HISTORY](https://docs.aws.amazon.com/redshift/latest/dg/SYS_QUERY_HISTORY.html)
+ [SYS\_QUERY\_DETAIL](https://docs.aws.amazon.com/redshift/latest/dg/SYS_QUERY_DETAIL.html)
+ [SYS\_EXTERNAL\_QUERY\_DETAIL](https://docs.aws.amazon.com/redshift/latest/dg/SYS_EXTERNAL_QUERY_DETAIL.html)
+ [SYS\_LOAD\_HISTORY](https://docs.aws.amazon.com/redshift/latest/dg/SYS_LOAD_HISTORY.html)
+ [SYS\_LOAD\_ERROR\_DETAIL](https://docs.aws.amazon.com/redshift/latest/dg/SYS_LOAD_ERROR_DETAIL.html)
+ [SYS\_UNLOAD\_HISTORY](https://docs.aws.amazon.com/redshift/latest/dg/SYS_UNLOAD_HISTORY.html)
+ [SYS\_SERVERLESS\_USAGE](https://docs.aws.amazon.com/redshift/latest/dg/SYS_SERVERLESS_USAGE.html)