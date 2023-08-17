- [File and Block storage in GCP](#file-and-block-storage-in-gcp)
  - [Storage types - Block Storage and File Storage](#storage-types---block-storage-and-file-storage)
  - [Block Storage](#block-storage)
  - [File Storage](#file-storage)
  - [GCP - Block storage and File storage](#gcp---block-storage-and-file-storage)
    - [Block Storage](#block-storage-1)
    - [File Storage](#file-storage-1)
- [GCP - Block Storage : Local SSDs](#gcp---block-storage--local-ssds)
  - [Local SSDs](#local-ssds)
    - [Advantages:](#advantages)
    - [Disadvantages:](#disadvantages)
- [Persistent Disks(PD)](#persistent-diskspd)
- [Local SSDs vs Persistent Disks](#local-ssds-vs-persistent-disks)
- [Persistent Disk Types: Standard, Balanced, SSD](#persistent-disk-types-standard-balanced-ssd)
- [Persistent Disks - Snapshots](#persistent-disks---snapshots)
  - [Recommendations for snapshots](#recommendations-for-snapshots)
- [Machine Images](#machine-images)
- [Snapshots vs Machine Images vs Images vs Instance template](#snapshots-vs-machine-images-vs-images-vs-instance-template)
- [Disks with gcloud](#disks-with-gcloud)
    - [Creating disks](#creating-disks)
    - [Resize disks](#resize-disks)
    - [Taking snapshots](#taking-snapshots)
- [Images with gcloud](#images-with-gcloud)
    - [Creating images](#creating-images)
    - [Deprecate image](#deprecate-image)
    - [Exports virtual disk images](#exports-virtual-disk-images)
    - [Delete image](#delete-image)
- [Persistent Disk - Scenarios](#persistent-disk---scenarios)
    - [You want to improve performance of Persistent Disks(PD)](#you-want-to-improve-performance-of-persistent-diskspd)
    - [You want to increase durability of persistent disks(PD)](#you-want-to-increase-durability-of-persistent-diskspd)
    - [You want to take hourly backup of Persistent disks](#you-want-to-take-hourly-backup-of-persistent-disks)
    - [You want to delete old snapshots created by scheduled snapshots](#you-want-to-delete-old-snapshots-created-by-scheduled-snapshots)
- [Cloud FileStore](#cloud-filestore)
- [Global, Regional and Zonal Resources](#global-regional-and-zonal-resources)
  - [Global](#global)
  - [Regional](#regional)
  - [Zonal](#zonal)
- [File and Block Storage Scenarios](#file-and-block-storage-scenarios)
    - [You want high IOPS but your data can be lost without problem.](#you-want-high-iops-but-your-data-can-be-lost-without-problem)
    - [You want to create a high performance file sharing system in GCP which can be attached with multiple VMs](#you-want-to-create-a-high-performance-file-sharing-system-in-gcp-which-can-be-attached-with-multiple-vms)
    - [You want to backup your VM configuration along with all its attached Persistent Disks](#you-want-to-backup-your-vm-configuration-along-with-all-its-attached-persistent-disks)
    - [You want to make it easy to launch VMs with hardened OS and customized software](#you-want-to-make-it-easy-to-launch-vms-with-hardened-os-and-customized-software)
# File and Block storage in GCP
## Storage types - Block Storage and File Storage
<img src="https://tusharrajpoot.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F2d70be60-82ac-41c3-a80d-46e2964b4fd4%2FUntitled.png?id=755ad652-9a91-4e9e-accb-2a2c2c4d08ac&table=block&spaceId=a76e34cd-6f40-4ea9-b2ce-f4521ea829f1&width=2000&userId=&cache=v2">

- What is Type of storage of your hard disk?
  - Block Storage
- You've created a file share to share a set of files with your colleagues in a enterprise. What type of storage you're using?
  - File Storage

## Block Storage
- Use case: Hard disks attached to your computers.
- Typically, ONE block storage device can be connected to ONE virtual server.
  - (EXCEPTIONS): You can attach read only block devices with multiple virtual servers and certain cloud providers are exploring multi-writer disks as well.
- HOWEVER, you can connect multiple different block storage devices to one VM.
- Used as.
  - ```Direct-attached storage(DAS)``` - Similar to hard disk
  - ```Storage Area Network(SAN)``` - High-speed network connecting a pool of storage devices.
    - Used by databases: Oracle and Microsoft SQL server
## File Storage
- Media workflows need huge shared storage for supporting proceesses like video editing.
- Enterprise users need a quick way to share files in a secure and organized way.
- These file shares are shared by several virtual servers.

## GCP - Block storage and File storage
### Block Storage
- ```Persistent Disks```: Network block storage
  - zonal: Data replicated in one zone
  - Regional: Data replicated in multiple zone.
- ```Local SSDs```: Local Block storage.
- While creating VMs in compute engine, we can attach persistent disks or Local SSDs.(Boot disk)(Default: Persistent disk-boot disk)
### File Storage
- ```Filestore```: High performance file storage.
- File store page
- Enable the Cloud Filestore.
- It's not a part of compute engine.
- Create a new instance of file store.
- Connect your filestore from compute engine or kubernetes engine.


# GCP - Block Storage : Local SSDs
- Two popular types of block storage can be attached to VM instances:
  - Local SSDs
  - Persistent Disks
- Local SSDs are physically attached to the host of the VM instance
  - Temporary data
  - Lifecycle tied to VM instance
- Persistent Disks are network storage
  - More durable
  - Lifecycle NOT tied to VM instance

## Local SSDs
- Physically attached to the host of VM instance:
  - Provide very high (IOPS) and very low latency.
  - (BUT)Ephemeral Storage: temporary data(Data persists only until instance is running)
    - Enable live migration for data to survive maintenance events.
  - Data automatically encrypted
    - HOWEVER, you can't configure encryption keys.
  - Lifecycle tied to VM instance.
  - ONLY some machine types support local SSDs.
  - Supports SCSI and NVMe interfaces.
- REMEMBER:
  - Choose NVMe-enabled and multi-queue SCSI images for best performance.
  - Larger Local SSDs(more storage), More vCPUs(attached to VM) => Even better performance.

### Advantages:
- Very fast I/O (~10-100X compared to PDs)
  - Higher throughput and lower latency
- Ideal for use cases needing high IOPs while storing temporary information.
  - Examples: Caches, temporary data, scratch files etc.
### Disadvantages:
- Ephemeral Storage:
  - Lower durability, lower availability, lower flexibility compared to PDs.
  - You can't detach and attach it to another VM instance.

# Persistent Disks(PD)
- Network block storage attached to your VM instance.
- Provisioned capacity.
- Very flexible:
  - Increase size when you need it - when attached to VM instance
  - Performance scales with size
    - For higher performance, resize or add more PDs.
- Independent lifecycle from VM instance
  - Attach/detach from one VM instance to another
- Options: Regional and Zonal
  - Zonal PDs replicated in single zone. Regional PDs replicated in 2 zones in same region.
  - Typically regional PDs are 2X the cost of Zonal PDs.
- Use case: Run your custom database.

# Local SSDs vs Persistent Disks
| Feature                   | Persistent disks          | Local SSDs            |
|---------------------------|---------------------------|-----------------------|
| Attachment to VM instance | As a network drive        | Physically attached   |
| Lifecycle                 | Separate from VM instance | Tied with VM instance |
| I/O Speed                 | Lower(Network latency)    | 10-100X of PDs        |
| Snapshots                 | Supported                 | Not supported         |
| Use Case                  | Permanent storage         | Ephemeral Storage     |

---

# Persistent Disk Types: Standard, Balanced, SSD
| Feature                                       | Standard                 | Balanced                             | SSD               |
|-----------------------------------------------|--------------------------|--------------------------------------|-------------------|
| Underlying storage                            | Hard disk drive          | Solid state drive                    | Solid state drive |
| Referred to as                                | pd-standard              | pd-balanced                          | pd-ssd            |
| Performance - Sequential IOPs(Big Data/Batch) | Good                     | Good                                 | Very Good         |
| Performance - Random IOPs(Transactional Apps) | Bad                      | Good                                 | Very Good         |
| Cost                                          | Cheapest                 | In between                           | Expensive         |
| Use Cases                                     | Big Data(cost efficient) | Balance between cost and performance | High performance  |

# Persistent Disks - Snapshots
- Take point-in-time snapshots of your persistent disks
- You can also schedule snapshots (configure a schedule):
  - you can also auto-delete snapshots after X days.
- Snapshots can be multi-regional and regional.
- You can share snapshots across projects.
- You can create new disks and instances from snapshots.
- Snapshots are incremental:
  - Deleting a snapshot only deletes data which is NOT needed by other snapshots.
- Keep similar data together on a Persistent Disk.
  - Separate your operating system, volatile data and permanent data.
  - Attach multiple disks if needed.
  - This helps to better organize your snapshots and images.
## Recommendations for snapshots
- Avoid taking snapshots more often than once an hour.
- Disk volume is available for use but snapshots reduce performance.
  - (RECOMMENDED) Schedule snapshots during off-peak hours.
- Creating snapshots from disk is faster than creating from images.
  - But creating disks from image is faster than creating from snapshots.
  - (RECOMMENDED) If you are repeatedly creating disks from a snapshot:
    - Create an image from snapshot and use the image to create disks.
- Snapshots are incremental:
  - But you don't lose data by deleting older snapshots.
  - Deleting a snapshot only deletes data which is not needed by other snapshots.
  - RECOMMENDED: Don't hesitate to delete unnecessary snapshots.

# Machine Images
- Machine image is different from Image.
- Multiple disks can be attached with a VM:
  - One boot disk(Your OS runs from boot disk)
  - Multiple Data disks
- An image is created from the boot persistent disk.
- HOWEVER, a machine image is created from a VM instance:
  - Machine image contains everything you need to create a VM instance:
    - configuration
    - Metadata
    - Permissions
    - Data from one or more disks
- Recommended for disk backups, instance cloning and replication.
- We can create a machine image from an existing VM instance.
- We can create instances from machine images.



# Snapshots vs Machine Images vs Images vs Instance template
| Scenarios                        | Machine Image | Persistent disk snapshot | Custom Image | Instance template |
|----------------------------------|---------------|--------------------------|--------------|-------------------|
| Single disk backup               | Yes           | Yes                      | Yes          | No                |
| Multiple disk backup             | Yes           | No                       | No           | No                |
| Differential backup              | Yes           | Yes                      | No           | No                |
| Instance cloning and replication | Yes           | No                       | Yes          | Yes               |
| VM Instance configuration        | Yes           | No                       | No           | Yes               |

---

# Disks with gcloud
- ```gcloud compute disks list/create/delete/resize/snapshot```
### Creating disks
- ```gcloud compute disks create my-disk-1 --zone=us-east1-a```
  - What should be size and type?
    - ```--size=SIZE(1GB or 2TB)```
    - ```--type=TYPE(default -pd-standard)```(```gcloud compute disk-types list```)
  - What should be on the disk?
    - ```--image --image-family --source-disk --source-snapshot```
  - How should data on disk be encrypted?
    - ```--kms-key --kms-project```
### Resize disks
- ```gcloud compute disks resize DISK_NAME --size=10GB --zone/region=us-central1-c```
- Only increasing size is supported.
### Taking snapshots
- ```gcloud compute disks snapshot test --zone=us-central1-a --snapshot-names=snapshot-test```
- You can also play with snapshots which are created:
  - ```gcloud compute snapshots list/describe/delete```

# Images with gcloud
- ```gcloud compute images```
- Actions: ```create/delete/deprecate/describe/export/import/list/update```
### Creating images
- ```gcloud compute images create my-image```
  - From a disk ```--source-disk=my-disk```, ```--source-disk-zone=us-east1-a```
  - From a snapshot ```--source-snapshot=source-snapshot```
  - From another image ```--source-image=source-image --source-image-project=source-image-project```
  - From latest non deprecated image from a family ```--source-image-family=source-image-family```, ```--source-image-project=source-image-project```
### Deprecate image
- ```gcloud compute images deprecate IMAGE --state=DEPRECATED```
### Exports virtual disk images
- ```gcloud compute images export --image=my-image --destination-uri=gs://my-bucket/my-image.vmdk --export-format=vmdk --project=my-project```

### Delete image
- ```gcloud compute images delete my-image1 myimage2```

# Persistent Disk - Scenarios
### You want to improve performance of Persistent Disks(PD)
- Increase size of PD or add more PDs. Increase vCPUs in your VM.
### You want to increase durability of persistent disks(PD)
- Go for regional PDs(2X cost but replicated in 2 zones)
### You want to take hourly backup of Persistent disks
- Schedule hourly snapshots
### You want to delete old snapshots created by scheduled snapshots
- Configure it as part of snapshot scheduling



# Cloud FileStore
- Shared cloud file storage.
  - Supports NFSv3 protocol.
  - Provisioned Capacity
- Suitable for High-performance workloads:
  - Up to 320 TB with throughput of 16GB and 480K IOPs
- Supports HDD(General purpose) and SSD(performance-critical workloads)
- Use cases: file share, media workflows and content management.

# Global, Regional and Zonal Resources
## Global
- Images
- Snapshots
- Instance templates(unless you use zonal resources in your templates)
## Regional
- Regional managed instance groups
- Regional Persistent disks
## Zonal
- Zonal managed instance groups
- Instances.
- Persistent disks
  - You can only attach a disk to instances in the same zone as the disk.

# File and Block Storage Scenarios
### You want high IOPS but your data can be lost without problem.
- Local SSDs
### You want to create a high performance file sharing system in GCP which can be attached with multiple VMs
- Filestore
### You want to backup your VM configuration along with all its attached Persistent Disks
- Create a machine image
### You want to make it easy to launch VMs with hardened OS and customized software
- Create a custom image

---