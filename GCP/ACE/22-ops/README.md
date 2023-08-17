- [Cloud Monitoring](#cloud-monitoring)
  - [Cloud Monitoring Workspace](#cloud-monitoring-workspace)
  - [Cloud Monitoring for Virtual machines](#cloud-monitoring-for-virtual-machines)
- [Cloud Logging](#cloud-logging)
  - [Features](#features)
  - [Log collection](#log-collection)
- [Cloud Logging - Audit and Security Logs](#cloud-logging---audit-and-security-logs)
  - [Access Transparency Log](#access-transparency-log)
  - [Cloud Audit Logs](#cloud-audit-logs)
    - [What audit logs contain?](#what-audit-logs-contain)
    - [Cloud Audit logs comparision](#cloud-audit-logs-comparision)
- [Routing logs and exports](#routing-logs-and-exports)
  - [Controlling and Routing](#controlling-and-routing)
  - [Cloud Logging Export](#cloud-logging-export)
  - [Cloud Logging export - use cases](#cloud-logging-export---use-cases)
- [Cloud Logging Service in GCP](#cloud-logging-service-in-gcp)
- [Cloud Monitoring Service in GCP](#cloud-monitoring-service-in-gcp)
- [Google Cloud Trace](#google-cloud-trace)
- [Google Cloud Debugger](#google-cloud-debugger)
- [Google Cloud Profiler](#google-cloud-profiler)
- [Error Reporting](#error-reporting)
- [Scenarios : Operations in Cloud](#scenarios--operations-in-cloud)
# Cloud Monitoring
- To operate cloud applications effectively, you should know:
  -  Is my application healthy?
  - Are the users experiencing any issues?
  - Does my database has enough space?
  - Are my servers running in an optimum capacity?
- Tools to monitor your infrastructure
  - Measures key aspects of services(metrics)
  - Create visualizations(Graphs and Dashboard)
  - Configure Alerts (When metrics are NOT healthy)
    - Define alerting policies:
      - Condition
      - Notifications: Multiple channels
      - Documentation
## Cloud Monitoring Workspace
- You can use cloud monitoring to monitor one or more GCP projects and one or more AWS accounts.
- How do you group all the information from multiple GCP Projects or AWS Accounts?
- Create a workspace
- Workspaces are needed to organize monitoring information.
  - A workspace allows you to see monitoring information from multiple projects.
  - Step 1. Create a workspace in a specific project(Host project)
  - Step 2. Add other GCP Projects(or AWS accounts) to the workspace.

## Cloud Monitoring for Virtual machines
- Default metrics monitored include:
  - CPU Utilization
  - Some disk traffic metrics
  - Network traffic 
  - Uptime information
- Install cloud monitoring agent on the VM to get more disk, CPU, network, and process metrics:
  - collectd-based daemon
  - Gathers metrics from VM and sends them to Cloud Monitoring

# Cloud Logging
- Real time log management and analysis tool.
- Allows to store, search, analyze and alert on massive volume of data.
- Exabyte scale, fully managed service
  - No server provisioning, patching etc.
- Ingest log data from any source.
## Features
- Logs explorer
  - Search, sort and analyze using flexible queries
- Logs dashboard
  - Rich visualization
- Logs Metrics
  - Capture metrics from logs(using queries/matching strings)
- Logs Router
  - Route different log entries to different destinations.

## Log collection
- Most GCP managed services automatically send logs to cloud logging:
  - GKE
  - App Engine
  - Cloud Run
- Ingest logs from GCE VMs:
  - Install logging agent(based on fluentd)
  - (Recommended): Run logging agent on all VM instances
- Ingest logs from on-premise
  - Recommended: Use the BingPlane tool from Blue medora
  - Use the cloud logging API
  
# Cloud Logging - Audit and Security Logs
## Access Transparency Log
- Captures actions performed by GCP team on your content(NOT supported by all services)
  - Only for organizations with Gold Support level & above.
## Cloud Audit Logs
- Answers who did what, when and where:
  - Admin activity logs
  - Data access logs
  - System event audit logs
### What audit logs contain?
- Which service?
  - protoPayload.serviceName
- Which operation?
  - protoPayload.methodName
- Which resource is audited?
  - resource.Type
- Who is making the call?
  - authenticationInfo.principalEmail

### Cloud Audit logs comparision
| Feature         | Admin Activity logs                                                   | data access logs                             | System Event logs                                           | Policy denied logs                            |
|-----------------|-----------------------------------------------------------------------|----------------------------------------------|-------------------------------------------------------------|-----------------------------------------------|
| Logs for        | API calls or other actions that modify the configuration of resources | Reading configuration of resources           | Google Cloud Administrative actions                         | When user or service account is denied access |
| Default Enabled | ✔                                                                     | X                                            | ✔                                                           | ✔                                             |
| VM Examples     | VM Creation, patching resources, change in IAM permissions            | Listing resources(VMs, images etc)           | On Host maintenance, instance preemption, automatic restart | security policy violation logs                |
| Cloud Storage   | modify bucket or object                                               | Modify/read bucket or object                 |                                                             |                                               |
| Access Needed   | Logging/Logs viewer or project/viewer                                 | Logging/private logs viewer or project/owner | logging/logs viewer or project/viewer                       | logging/logs viewer or project viewer         |


# Routing logs and exports
## Controlling and Routing

<img src="https://theacetechnologist.com/gcp-logs.png">

- How do you manage your logs?
  - Logs from various sources reaches log router.
  - Log Router checks against configured rules
    - What to ingest? what to discard?
    - Where to route?
- Two types of logs buckets:
  - ```_Required```: Holds admin activity, System events & access transparency logs(retained for 400 days).
    - ZERO charge
    - you can't delete the bucket
    - You can't change retention period.
  - ```_Default```: All other logs(retained for 30 days)
    - You're billed based on cloud logging pricing.
    - you can't delete bucket:
      - But you can disable the ```_Default``` log sink route to disable ingestion!
    - You can edit retention settings(1 to 3650 days (10 years))
## Cloud Logging Export
- Logs are ideally stored in Cloud Logging for limited period.
  - For long term retention(Compliance, audit) logs can be exported to:
    - Cloud Storage Bucket( ex: bucket/syslog/2025/05/05)
    - BigQuery dataset(ex: tables syslog_20250505 > columns timestamp, log)
    - Cloud Pub/Sub topic(base64 encoded log entries )
- How do you export logs?
  - Create sinks to these destinations using Log Router:
    - You can create include or exclude filters to limit the logs.

## Cloud Logging export - use cases
- Use case 1: Troubleshoot using VM Logs:
  - Install Cloud logging agent in all VM's and send logs to cloud logging
- Usd Case 2: Export VM Logs to BigQuery for querying using SQL like queries:
  - Install cloud logging agent in all VM's and send logs to cloud logging
  - Create a BigQuery dataset for storing the logs
  - Create an export sink in Cloud logging with BigQuery dataset as sink destination.
- Use Case 3: You want to retain audit logs for external auditors at minimum cost
  - Create an export sink in cloud logging with Cloud storage bucket as sink destination.
  - Provide auditors with storage object viewer role on the bucket.

# Cloud Logging Service in GCP
- Log Explorer
  - Query your logs
- Logs Dashboard
- Logs-based metrics
- Log Router
  - Create sinks
  - Add conditions include/exclude
  - Sink services
    - Cloud Storage bucket
    - splunk
    - BigQuery dataset
    - Pub/sub topic
    - Logging bucket
  - Define filters
- Logs Storage
  - Where logs are stored
  - By Default 2 log buckets:
    - ```_Default```
      - retention period: 30 days
      - Most of the application logs will be stored.
    - ```_Required```
      - retention period: 400 days
      - Audit bucket
      - Can't be deleted or retention period change


# Cloud Monitoring Service in GCP
- Go to monitoring in GCP
- Overview
- Dashboard
- Services
- Metrics explorer
  - Use queries to explore metrics for services.
- Alerting
- Uptime checks
  - applications should run all the time.
- Groups
  - Group the services to monitor

# Google Cloud Trace
- Distributed tracing system for GCP: Collect latency data from:
  - Supported Google Cloud Services
  - Instrumented applications(using tracing libraries) using Cloud Trace API
- Find out
  - How long does a service take to handle requests?
  - What is the average latency of requests?
  - How are we doing over time?(increasing/decreasing trend)
- Supported for:
  - Compute Engine, GKE, App Engine(flexible, standard) etc.
- Trace client libraries available:
  - C#, Go, Java, Node.js, PHP, Python and Ruby.

# Google Cloud Debugger
- How to debug issues that are happening only in test or production environment
- Cloud Debugger: Capture state of a running application
  - Inspect the state of the application directly in the GCP environment
  - Take snapshots of variables and call stack
  - No need to add logging statements
  - No need to redeploy
  - Very lightweight => Very little impact to users
    - Can be used in any environment: Test, acceptance and production.

# Google Cloud Profiler
- How do you identify performance bottlenecks in production?
- Cloud profiler - Statistical, low-overhead profiler
  - continuously gathers CPU and Memory usage from production systems.
  - Connect profiling data with application source code.
    - Easily identify performance bottlenecks
  - Two major components:
    - Profiling agent (Collects profiling information)
    - Profile interface(Visualization)
# Error Reporting
- How do you idenitfy production problems in real life?
- Real-time exception monitoring:
  - Aggregates and displays errors reported from cloud services(using stack traces)
  - Centralized Error Management console:
    - Identify & manage top errors or recent errors
  - Use firebase crash reporting for errors from android & ios clients
  - Supported for Go, Java, .NET, Node.js, PHP, Python and Ruby.
- Errors can be reported by:
  - Sending them to Cloud Logging or
  - By calling Error reporting API
- Error reporting can be accessed from Desktop
  - Also available in the cloud console mobile app for IOS and Android.

# Scenarios : Operations in Cloud
- You would like to record all operations/requests on all objects in a bucket(for auditing)
  - Turn on data access audit logging for the bucket
- You want to trace a request across multiple microservices
  - Cloud Trace
- You want to identify prominent exceptions(or errors) for a specific microservice
  - Error reporting
- You want to debug a problem in production by executing step by step
  - Cloud Debugger
- You want to look at the logs for a specific request
  - Cloud Logging

