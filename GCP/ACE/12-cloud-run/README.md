- [Cloud Run and Cloud Run for Anthos](#cloud-run-and-cloud-run-for-anthos)
  - [Cloud Run](#cloud-run)
  - [Anthos](#anthos)
  - [Cloud Run for Anthos](#cloud-run-for-anthos)
  - [Deploying Containerized application in Cloud Run](#deploying-containerized-application-in-cloud-run)
  - [Cloud run with gcloud cli](#cloud-run-with-gcloud-cli)

# Cloud Run and Cloud Run for Anthos
## Cloud Run
- Container to Production in Seconds.
  - Built on top of an open standard - KNative
  - Fully managed serverless platform for containerized applications
    - ZERO infrastructure management
    - Pay-per-use (For used CPU, Memory, Requests, and Networking)
- Fully integrated end-to-end developer experience:
  - No limitations in languages, binaries and dependencies
  - Easily portable because of container based architecture
  - Cloud Code, Cloud Build, Cloud Monitoring, Cloud Logging integrations.

## Anthos
- Run kubernetes clusters anywhere
  - Cloud, Multi-cloud and On-Premise
## Cloud Run for Anthos
- Deploy your workloads to Anthos clusters running on-premises or on Google Cloud.


## Deploying Containerized application in Cloud Run
- In Cloud run: click on create service
- Add container image to deploy
- add service name
- Add region
- We can't change service name and region after one time.
- Charged per invocation
- Or CPU is always allocated(Instance will be running always)
- AutoscalingL min instances, max instances
- Ingress
  - Allow all traffic
  - Allow internal traffic and traffic from cloud load balancing
  - Allow internal traffic only
- Configure authentication
  - unauthenticated invocations
  - require authentication
- Create the service

## Cloud run with gcloud cli
- Deploy a new container
  ```sh
  gcloud run deploy SERVICE_NAME --image IMAGE_URL --revision-suffix v1
  ```
  - First deployment creates a service and first revision
  - Next deployments for the same service creates new revisions
- List available revisions
  - ```gcloud run revisions list```
- Adjust traffic assignments
  - ```gcloud run services update-traffic myservice --to-revisions=v2=10,v1=90```
