- [Cloud Functions Second Generation - What's new?](#cloud-functions-second-generation---whats-new)
  - [2 Product versions](#2-product-versions)
  - [Key ehancements in 2nd gen](#key-ehancements-in-2nd-gen)
  - [Creating cloud function](#creating-cloud-function)
  - [Cloud run behind Cloud Function gen2](#cloud-run-behind-cloud-function-gen2)
- [Scaling and Concurrency](#scaling-and-concurrency)
  - [How to configure concurrency](#how-to-configure-concurrency)
- [Gcloud with cloud functions](#gcloud-with-cloud-functions)
# Cloud Functions Second Generation - What's new?
## 2 Product versions
1. Cloud Functions (1st gen): First version
2. Cloud Functions (2nd gen): New version built on top of cloud run and eventarc

Recommended: Use Cloud Functions (2nd gen)
## Key ehancements in 2nd gen
- Longer Request timeout: Up to 60 minutes for HTTP -triggered functions
- Large instance size: Up to 16GiB Ram with 4 vCPU(v1: upto 8GB RAM with 2 vCPUs)
- Concurrency: Upto 1000 concurrent requests per function instance (v1: 1 concurrent request per function instance)
- Multiple function revisions and traffic splitting supported(v1: Not supported)
- Support for 90+ event types - enabled by Eventarc(v1: Only 7)

## Creating cloud function
- Same as v1 but add eventarc triggers also.

## Cloud run behind Cloud Function gen2
- After deploying 2nd gen cloud function
- You can see cloud run instance is also created for the cloud function.
- And in the cloud run instance 
  - we can manage revisions
  - traffic splitting etc.

# Scaling and Concurrency
- Typical serverless functions architecture:
  - Autoscaling - As new invocations come in, new function instances are created
  - One function instance handles ONLY ONE request AT A TIME.
  - 3 Concurrent function invocations => 3 function instances
    - If a 4th function invocation occurs while existing invocations are in progress, a new function instance will be created.
    - HOWEVER, a function instance that completed execution may be reused for future requests.
  - Typical PROBLEM: Cold Start:
    - New function instance initialization from scratch can take time.
    - (SOLUTION): Configure min number of instances(instances cost)
- 1st gen uses the typical serverless functions architecture
- 2nd gen adds a very important new feature
  - One function instance can handle multiple requests at the same time
    - Concurrency: How many concurrent invocations can one function instance handle?(Max 1000)
    - (IMPORTANT) Your function code should be safe to execute concurrently.

## How to configure concurrency
- Go to cloud run instance of cloud function
- Edit a new revision for instance

# Gcloud with cloud functions
- ```gcloud functions deploy [NAME]```
  - ```docker-registry``` registry to store the function's docker images
    - Default ```container-registry```
    - Alternative ````artifact-registry```
  - ```--docker-repository``` Repository to store function's docker images
    - ```projects/${PROJECT}/locations/${LOCATION}/repositories/${REPOSITORY}```
  - ```--gen2``` use 2nd gen. if this option is not present, 1st gen will be used
  - ```--runtime``` nodejs, python, java...
  - ```--service-account``` service account to use
    - 1st GEN: default - app engine default service account
    - 2nd GEN: default - compute default service account
  - ```--timeout``` function execution timeout
  - ```--source```
    - Zip file from GCS
    - Source Repo
    - Local file system
  - ```--trigger-bucket```,```--trigger-http```,```--trigger-topic```,```--trigger-event-filters```(ONLY in gen2 - Eventarc matching criteria for the trigger)
