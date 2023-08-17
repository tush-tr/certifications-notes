# Getting started with Gcloud

- <a href="#1">Getting Started with Gcloud</a>
- <a href="#2">Gcloud config</a>
- <a href="#3">Managing multiple configurations in gcloud</a>
- <a href="#4">Understanding command structure in Gcloud to play with services</a>
- <a href="#5">Gcloud compute instances create </a>
- <a href="#6">Setting default region and zone</a>
- <a href="#7">List and describe</a>
- <a href="#8">gcloud with compute instances</a>
- <a href="#9">Gcloud with instance templates</a>


<hr>

<h2 id="1">Getting Started with Gcloud</h2>

- Command line interface to interact with Google Cloud Resources.
- Most GCP services can be managed from CLI using GCloud.
  - Compute Engine Virtual Machines
  - Managed Instance Groups
  - Databases
  - and .... many more

- We can create/delete/update/read existing resources and perform actions like deployments as well.
- (REMEMBER) Some GCP services have specific CLI tools:
  - Cloud Storage- gsutil
  - Cloud BigQuery- bq
  - Cloud BigTable- cbt
  - Kubernetes- kubectl(in addition to GCloud which is used to manage clusters)

### Installation

- GCloud is a part of Google Cloud SDK.
  - Cloud SDK requires python
  - Instructions to install Cloud SDK(and GCloud): <a href="https://cloud.google.com/sdk/docs/install">cloud.google.com/sdk/docs/install</a>
- We can also use Gcloud on Cloud Shell

```sh
gcloud --version
```

- Initialize Gcloud
    - Initialize or reinitialize gcloud
    - Authorize gcloud to use your user account credentials
    - Setup configuration
```sh
gcloud init
```

- List all properties of the active configuration
    - region
    - zone
    - [core]
      - account
      - project


  ```sh
  gcloud config list
  ```

<hr>


<h2 id="2">Gcloud config set</h2>

- List account only ```gcloud config list account```
- List configuration for region ```gcloud config list region```
  - When we run gcloud config list with specific property like ```region``` it will look the property in [core]
- List region in compute component: ```gcloud config list compute/region```
- Set a specified property in your active configuration: 
  - SYNTAX: ```gcloud config set SECTION/PROPERTY VALUE```
  - ```gcloud config set core/project VALUE``` or ```gcloud config set project VALUE```
  - ```gcloud config set compute/region VALUE```
  - ```gcloud config set compute/zone VALUE```
- Opposite to set command : ```gcloud config unset```

>```core``` is default section in gcloud config command


<hr>
<h2 id="3">Managing multiple configurations in gcloud</h2>

- Scenario: you're working on multiple projects from the same machine. You would want to be able to execute commands using different configurations.
- Simplify this using:
  - ```gcloud config configurations create/delete/describe/activate/list```
  - create configurations also activate the configuration

<hr>

<h2 id="4"> Understanding command structure in Gcloud to play with services</h2>

```sh
gcloud GROUP SUBGROUP ACTION ...
```

- GROUP:
  - config or compute or container or dataflow or functions or iam or....
    - which service group are you playing with.
- SUBGROUP:
  - Instances or images or instance-templates or machine-types or regions or zones.
    - Which subgroup of the service do you want to play with?
- ACTION:
  - Create or list or start or stop or describe or...
    - What do you want to do?

### Example:

- List all GCP Compute engine instances ```gcloud compute instances list```
  - GROUP: compute
  - SUBGROUP: instances
  - ACTION: list

- Create a new instance 
  - ```gcloud compute instances create my-first-instance ```
- Describe a specific instance[all details around a compute instance]
  - ```gcloud compute instances describe my-first-instance```
- Delete a instance
  - ```gcloud compute instances delete my-first-instance```

<details>
<summary>Other Gcloud command examples</summary>

```sh
gcloud compute regions list
gcloud compute zones list
gcloud compute machine-types list
gcloud compute machine-types list --filter="zone:us-central1-b"
gcloud config list project
gcloud config configurations list
gcloud config configurations activate my-default-configuration
gcloud config list
gcloud config configurations describe my-second-configuration
gcloud compute instances list
gcloud compute instances create
gcloud compute instances create my-first-instance-from-gcloud
gcloud compute instances describe my-first-instance-from-gcloud
gcloud compute instances delete my-first-instance-from-gcloud
gcloud compute zones list
gcloud compute regions list
gcloud compute machine-types list
 
gcloud compute machine-types list --filter zone:asia-southeast2-b
gcloud compute machine-types list --filter "zone:(asia-southeast2-b asia-southeast2-c)"
gcloud compute zones list --filter=region:us-west2
gcloud compute zones list --sort-by=region
gcloud compute zones list --sort-by=~region
gcloud compute zones list --uri
gcloud compute regions describe us-west4
 
gcloud compute instance-templates list
gcloud compute instance-templates create instance-template-from-command-line
gcloud compute instance-templates delete instance-template-from-command-line
gcloud compute instance-templates describe my-instance-template-with-custom-image

```

</details>

<hr>

<h2 id="5">Gcloud compute instances create</h2>

- Creating compute instances:
  - ```gcloud compute instances create [NAME]```
    - Options:
      - ```--machine-type``` : default type is n1-standard-1
      - ```--custom-cpu``` : --custom-cpu 6
      - ```--custom-memory``` : --custom-memory 3072MB
      - ```--custom-vm-type``` : --custom-vm-type n2
      - ```--image``` or ```--image-family``` or ```--source-snapshot``` or ```--source-instance-template``` or ```--source-machine-image```(beta)
      - ```--service-account``` or ```--no-service-account```
      - ```--tags``` (list of tags: allow network firewall rules and routes)
      - ```--preemptible```
      - ```--restart-on-faliure```(default)
      - ```--no-restart-on-faliure```
      - ```--maintenance-policy``` (MIGRATE(default/TERMINATE))
      - ```--boot-disk-size```, ```--boot-disk-type```
      - ```--boot-disk-auto-delete```(default), ```--no-boot-disk-auto-delete```
      - ```deletion-protection```, ```--no-deletion-protection```(Default)
      - ```--metadata/metadata-from-file startup-script/startup-script-url```
        - ```--metadata-from-file startup-script=/local/path/to/script/startup```
        - ```--metadata startup-script="echo 'hello-world'"```
        - ```shutdown-script```
      - ```--network --subnet --network-tier``` (PREMIUM(default), STANDARD)
      - ```--accelerator="type=nvidia-tesla-v100,count=8" --metadata="install-nvidia-driver=True"``` (GPU)

<hr>

<h2 id="6">Setting default region and zone</h2>

We've 3 options:
1. Centralized Configuration: ```gcloud compute project-info add-metadata```
   - ```--metadata=[google-compute-default-region=REGION | google-compute-defaut-zone=ZONE]```
2. Local gcloud configuration: ```gcloud config set compute/region REGION```
3. Command Specific : ```--zone``` or ```--region``` in the command

> Priority: Option 3 (if exists) overrides option 2 (if exists) overrides option 1

<hr>
<h2 id="7">List and Describe</h2>

- List commands are used to list a set of resources
  - gcloud compute RESOURCES list
    - ```gcloud compute images/regions/zones list```
  - Most list commands support a few common options
    - ```--filter="zone:VALUE"```
    - ```--sort-by``` (NAME,-NAME) 
    - ```--uri``` 
- Describe commands are used to describe a specific resource.
  - ```gcloud compute images describe ubuntu-1604-xenial-v20210203 --project my-new-project```

<hr>

<h2 id="8">gcloud with compute instances</h2>

- ```gcloud compute instances list/start/stop/delete/reset/describe/move```
  - ```gcloud compute instances delete example-instance```
    - ```--delete-disks=VALUE```(all or data or boot)
    - ```--keep-disks=VALUE```(all or data or boot)
  - ```gcloud compute instances move example-instance-1 --zone us-central1-b --destination-zone us-central1-f```
    - move a VM from one zone to another

<hr>

<h2 id="9">Gcloud with instance templates</h2>

- ```gcloud compute instance-templates create/delete/describe/list```
  - ```--source-instance=SOURCE_INSTANCE --source-instance-zone``` (which instance to create a template from)
  - Supports almost all options supported by ```gcloud compute instances create [NAME]```
  - 

<hr>

## Important things to remember

- Cloud Shell is backed by a VM instance(automatically provisioned by Google Cloud when you launch Cloud Shell)
  - 5 GB of free persistent disk storage is provided as your $HOME directory
  - Prepackaged with latest version of Cloud SDK, docker etc.
  - (Remember) Files in your home directory persist betwee sessions(scripts, user configuration files like .bashrc and .vimrc etc)
  - Instance is terminated if you are intactive for more than 20 minutes.
    - Any modifications that you made to it outside your $HOME will be lost.
  - (REMEMBER) After 120 days of inactivity, even your $HOME directory is deleted.

- Cloud Shell can be used to SSH into virtual machines using their private IP addresses.

<hr>