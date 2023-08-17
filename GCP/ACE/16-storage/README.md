- [Object Storage - Cloud Storage](#object-storage---cloud-storage)
- [Objects and Buckets](#objects-and-buckets)
- [Storage Classes](#storage-classes)
  - [Standard Storage Class](#standard-storage-class)
  - [Nearline Storage Class](#nearline-storage-class)
  - [Coldline Storage](#coldline-storage)
  - [Archive Storage](#archive-storage)
  - [Features across storage classes](#features-across-storage-classes)
- [Uploading and Downloading options](#uploading-and-downloading-options)
  - [Simple uploads](#simple-uploads)
  - [Multipart uploads](#multipart-uploads)
  - [Resumable upload](#resumable-upload)
  - [Streaming transfers](#streaming-transfers)
  - [Parallel composite uploads](#parallel-composite-uploads)
  - [Simple Download](#simple-download)
  - [Streaming Download](#streaming-download)
  - [Slices object download](#slices-object-download)
- [Versioning in GCP Cloud Storage](#versioning-in-gcp-cloud-storage)
- [Object Lifecycle Management in GCP Cloud Storage](#object-lifecycle-management-in-gcp-cloud-storage)
  - [Example lifecycle rule](#example-lifecycle-rule)
- [Cloud Storage - Encryption with KMS](#cloud-storage---encryption-with-kms)
- [Cloud Storage Scenarios](#cloud-storage-scenarios)
    - [How do you speed up large uploads(example: 100GB) to cloud storage](#how-do-you-speed-up-large-uploadsexample-100gb-to-cloud-storage)
    - [You want to permanently store application logs for regulatory reasons. You don't expect to access them all.](#you-want-to-permanently-store-application-logs-for-regulatory-reasons-you-dont-expect-to-access-them-all)
    - [Logs files stored in cloud storage. You expect to access them once in quarter.](#logs-files-stored-in-cloud-storage-you-expect-to-access-them-once-in-quarter)
    - [How do you change storage of an existing bucket in cloud Storage?](#how-do-you-change-storage-of-an-existing-bucket-in-cloud-storage)
- [Gsutil CLI for Cloud storage](#gsutil-cli-for-cloud-storage)
    - [Creating bucket](#creating-bucket)
    - [List current and non-current object versions](#list-current-and-non-current-object-versions)
    - [Copy objects](#copy-objects)
    - [Rename/Move objects](#renamemove-objects)
    - [Change storage class for Objects](#change-storage-class-for-objects)
    - [Upload and download objects](#upload-and-download-objects)
    - [Enable/Disable versioning](#enabledisable-versioning)
    - [Set access permissions for specific objects](#set-access-permissions-for-specific-objects)
    - [Setup IAM Role](#setup-iam-role)
    - [Signed URL For temporary access](#signed-url-for-temporary-access)
# Object Storage - Cloud Storage
- Most popular, very flexible & inexpensive storage service
  - Serverless: Autoscaling and infinite scale
- Store large objects using a key-value approach:
  - Treats entire object as a unit(Partial updates not allowed)
    - Recommended when you operate on entire object most of the time.
    - Access control at object level
  - Also called object storage
- Provides REST API to access and modify objects.
  - Also provides a CLI(gsutil) & client libraries (C++,C#, Java, Node.js, PHP, Python, Ruby)
- Store all file types - text, binary, backup & archives.
  - Media files and archives, application packages and logs
  - Backups of your databases or storage devices
  - Staging data during on-premise to cloud database migration.

# Objects and Buckets
- Objects are stored in buckets
  - Bucket names are globally unique.
  - Bucket names are used as part of object URLs => can only contain lower case letters, numbers, hyphens, underscores and periods.
  - 3-63 characters max. Can't start with goog prefix or should not contain google(even misspelled)
  - Unlimited objects in a bucket.
- Each object is identified by a unique key.
  - Key is unique in a bucket
- Max object size is 5TB.
  - But you can store unlimited number of such objects.

# Storage Classes
- Different kinds of data can be stored in cloud storage
  - Media files and archives
  - Application packages and logs
  - Backups of your databases or storage devices
  - Long term archives
- Huge variations in access patterns
- Can I pay a cheaper price for objects I access infrequently?
- Storage Classes help to optimize your costs based on your access needs
  - Designed for durability of 99.999999999%(11 9's)
## Standard Storage Class
- name: STANDARD
- Minimum storage duration: None
- Typically monthly availability:
  - ```>99.99%``` in multi-region and dual region
  - 99.99% in regions
- Use Case:
  - Frequently used data/short period of time

## Nearline Storage Class
- name: NEARLINE
- Minimum storage duration: 30 days
- Typically monthly availability:
  - ```>99.95%``` in multi-region and dual region
  - 99.9% in regions
- Use Case:
  - Read or modify once a month on average
## Coldline Storage
- name: COLDLINE
- Minimum storage duration: 90 days
- Typically monthly availability:
  - ```>99.95%``` in multi-region and dual region
  - 99.9% in regions
- Use Case:
  - Read or modify at most once a quarter.

## Archive Storage
- name: ARCHIVE
- Minimum storage duration: 365 days
- Typically monthly availability:
  - >99.95% in multi-region and dual region
  - 99.9% in regions
- Use Case:
  - Less than once a year

## Features across storage classes
- High durability(99.999999999% annual durability)
- Low Latency (first byte typically in tens of milliseconds)
- Unlimited storage
  - Autoscaling(no configuration needed)
  - No minimum object size
- Same APIs across storage classes
- Committed SLA is 99.95% for multi-region and 99.9% for single region for standard, nearline, and coldline storage classes.
  - No committed SLA for archive storage.
<img src="https://img-c.udemycdn.com/redactor/raw/article_lecture/2022-09-21_04-22-53-1080136dbc5dcdf8ee06d98a299007aa.png">

# Uploading and Downloading options
## Simple uploads
- Small files(that can be re uploaded in case of failures) + No object metadata
## Multipart uploads
- Small files(that can be re uploaded in case of failures) + Object metadata
## Resumable upload
- Larger files. RECOMMENDED for most usecases(even for small files - costs one additional HTTP request)
## Streaming transfers
- Upload an object of unknown size
## Parallel composite uploads
- File divided up to 32 chunks and uploaded in parallel.
- Significantly faster if network and disk speed are not limiting factors.
## Simple Download
- Downloading objects to a destination
## Streaming Download
- Downloading data to a process
## Slices object download
- Slice and download large objects


# Versioning in GCP Cloud Storage
- Prevents accidental deletion & provides history.
  - Enabled at bucket level
    - Can be turned on/off at any time.
  - Live version is the latest version
    - If you delete live object, it becomes noncurrent object version.
    - If you delete noncurrent object version, it is deleted.
  - Older versions are uniquely identified by (Object key + a generation number)
  - Reduce costs by deleting older(noncurrent) versions.

# Object Lifecycle Management in GCP Cloud Storage
- Files are frequently accessed when they're created
  - Generally usage reduces with time
  - How do you save costs by moving files automatically between storage classes?
    - Solution: Object Lifecycle Management
- Identify objects using conditions based on:
  - Age, CreatedBefore, IsLive, MatchesStorageClass, NumberOfNewerVersions etc.
  - Set multiple confitions: all conditions must be satisfied for action to happen.
- Two kinds of actions:
  - SetStorageClass actions(change from one storage class to another)
  - Deletion actions(delete objects)
- Allowed Transitions:
  - (Standard or Multi-Regional or Regional) to (Nearline or Coldline or Archive)
  - Nearline to (Coldline or Archive)
  - Coldline to Archive
## Example lifecycle rule
```json
{
    "lifecycle":{
        "rule": [
            {
                "action":{"type": "Delete"},
                "condition":{
                    "age": 30,
                    "isLive": true
                }
            },
            {
                "action":{
                    "type": "SetStorageClass",
                    "storageClass": "NEARLINE"
                },
                "condition":{
                    "age": 365,
                    "matchesStorageClass":["MULTI_REGIONAL","STANDARD","DURABLE_REDUCED_AVAILABILITY"]
                }
            }
        ]
    }
}
```

```json
{
    "lifecycle":{
        "rule":[
            {
                "action":{"type": "Delete"},
                "condition":{
                    "age":30,
                    "isLive": true
                }
            },
            {
                "action": {
                    "type": "SetStorageClass",
                    "storageClass": "NEARLINE"
                },
                "condition":{
                    "age": 365,
                    "matchesStorageClass": ["STANDARD"]
                }
            }
        ]
    }
}
```

# Cloud Storage - Encryption with KMS
- Cloud storage always encrypts data on the server side!
- Configure server-side encryption: Encryption performed by Cloud Storage
  - Google-managed encryption key - Default (No configuration required)
  - Customer-managed encryption keys - Created using Cloud Key Management Service(KMS). Managed by customer in KMS.
    - Cloud storage service account should have access to Keys in KMS for encrypting and decrypting using the customer-managed encryption key.
- (OPTIONAL) Client-side encryption - Encryption performed by customer before upload.
  - GCP Does not know about the keys used.
- Go to configuration in GCS bucket.


# Cloud Storage Scenarios
### How do you speed up large uploads(example: 100GB) to cloud storage
- Use ```Parallel composite uploads``` File is broken in to small chunks and uploaded.
### You want to permanently store application logs for regulatory reasons. You don't expect to access them all.
- Cloud Storage - Archive
### Logs files stored in cloud storage. You expect to access them once in quarter.
- Cold Line
### How do you change storage of an existing bucket in cloud Storage?
- Step 1. Change default storage class of the bucket.
- Step 2. Update the storage class of the objects in the bucket.

# Gsutil CLI for Cloud storage
- gsutil is CLI for Cloud storage.
- Already installed in CloudShell as Gcloud component
### Creating bucket
- ```gsutil mb gs://my_bucket_tushar_kube```
### List current and non-current object versions
- ```gsutil ls -a gs://BKT_NAE```
### Copy objects
- ```gsutil cp gs://SRC_BKT/SRC_OBJ gs://DESTN_BKT/NAME_COPY```
    - ```-o 'GSUtil:encryption_key=ENCRYPTION_KEY``` - encrypt object
### Rename/Move objects
- ```gsutil mv```
### Change storage class for Objects
- ```gsutil rewrite -s STORAGE_CLASS gs://BKT_NAME/OBJ_PATH``` 
### Upload and download objects
- ```gsutil cp```
### Enable/Disable versioning
- Cloud Storage(gsutil)
  - ```gsutil versioning set on/off gs://BKT_NAME```
  - ```gsutil uniformbu cketlevelaccess set on/off gs://BKT_NAME```
### Set access permissions for specific objects
- ```gsutil acl ch```
  - ```gsutil acl ch -u AllUsers:R gs://BKT_NAME/OBJ_PATH```(make specific object public)
    - R means read
  - Permissions: READ(R),WRITE(W),OWNER(O)
  - Scope: User, allAuthenticatedUsers, AllUsers(-u), Group(-g), Project(-p) etc.
- ```gsutil acl set JSONFILE gs://BKT_NAME```

### Setup IAM Role
- ```gsutil iam ch MBR_TYPE:MBR_NAME:IAM_ROLE gs://BKT_NAME```

### Signed URL For temporary access
- ```gsutil signurl -d 10m YOUR_KEY gs://BUCKET_NAME/OBJECT_PATH```



---