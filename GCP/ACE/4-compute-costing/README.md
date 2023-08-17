# Google Compute - Optimizing costs and performance in GCP

- <a href="#1">Sustained use discounts in GCP</a>
- <a href="#2">Committed use discounts in GCP</a>
- <a href="#3">Saving costs with preemptible VMs</a>
- <a href="#4">Spot VMs</a>
- <a href="#5">Understanding Billing for GCE - GCE VMs</a>
- <a href="#6">Achieve high availability with live migration and Automatic Restart</a>
- <a href="#7">Understanding custom machine types</a>
- <a href="#8">Exploring GPU in GCP</a>
- <a href="#9">VMs in GCP: Important points to remember</a>
- <a href="#10">Best practices for VMs in GCP</a>
- <a href="#11">Compute Engine scenarios</a>
- <a href="#12">Summary in Brief</a>


<hr>
<h2 id="1"> Sustained use discounts in GCP</h2>

- **Automatic discounts** for running VM instances for significant portion of the billing month.
  - Example: If you use N1, N2 machine types for more than 25% to 50% discount on every incremental minute.
  - Discount increases with usage(graph)
  - <img src="https://cloud.google.com/static/compute/images/sustained-use-example.png" height="200">
- **Applicable** for instances created by Google Kubernetes Engine and Compute Engine.
- **RESTRICTION**: Doesn't apply on certain machine types(example: E2 and A2)
- **RESTRICTION**: Doesn't apply to VMs created by app engine flexible and Dataflow.

<hr>
<h2 id="2"> Committed use discounts</h2>

- For workloads with **predictable resource** needs.
- **Commit** for 1 year or 3 years.
- **Up to 70% discount** based on machine type and GPUs.
- **Applicable** for instances created by **Google kubernetes engine** and **Compute Engine**.
- **RESTRICTION**: Doesn't apply to VMs created by app engine flexible and Dataflow.
- How to use committed use discounts?
  - Go to Compute Engine page
  - Choose Committed use discounts.
  - We can purchase a committment.

<hr>
<h2 id="3">Preemptible VMs</h2>

- **Short-lived Cheaper**(upto 80%) compute instances
  - Can be stopped by GCP any time(preemptible) within 24 hours.
  - Instances get 30 second warning (to save anything they want to save)
- **Use Preemptible VM's** if:
  - Your applications are fault tolerant.
  - You are very cost sensitive.
  - Your workload is **NOT immediate**
  - Example: Non immediate batch processing jobs

- **RESTRICTIONS**:
  - Not always available
  - No SLA and can't be migrated to regular VMs.
  - No Automatic Restarts
  - Free tier credits not applicable.

- How to create?
  - While creating a virtual machine
  - In Management, security, disks, networking tab.
  - Availability policy > Choose preemptibility.



<h2 id="4">Spot VMs</h2>

- Latest version of preemptible VMs
- Difference: Doesn't have a maximum runtime
  - Compared to traditional preemptible VMs which have a maximum runtime of 24 hours
- Other features similar to traditional preemptible VMs
  - May be reclaimed at any time with 30-second notice
  - NOT always available
  - Dynamic pricing: 60-91% discount compared to on-demand VMs.
  - Free tier credits not applicable.

<hr>

<h2 id="5">Google Compute Engine - Billing </h2>

- You're billed by the second(After a minimum of 1 minute)
- You are NOT billed for compute when a compute instance is stopped.
  - However, you will be billed for any storage attached with it.
- (RECOMMENDATION): Always create budget alerts and make use of budget exports to stay on top of billing!

### How to create budgets?
- Go to billing> Budgets & alerts
- Create a budget
- Budget type: Specified amount
- Target budget
- Alert threshold tools

### Billing export
- We can send our billing data to bigquery or file

## What are the ways you can save money?
- Choose the right machine type and image for your workload.
- Be aware of the discounts available:
  - Sustained use discounts
  - committed use discounts
  - discounts for preemptible VM instances.

<hr>

<h2 id="6">Compute Engine: Live migration & availability Policy</h2>

- How do you keep your VM instances running when a host system needs to be updated(a software or a hardware update needs to be performed)
- ### Live Migration
  - Your running instance is migrated to another host in the same zone.
  - Does not change any attributes or properties of the VM.
  - SUPPORTED for instances with local SSDs.
  - NOT SUPPORTED for GPUs and preemptible instances.
- ### Important configuration- **Availability Policy**
  - Where we configure the live migration configuration.
  - **On Host maintenance**: What should happen during periodic infrastructure maintenance?
    - Migrate(default): Migrate VM instance to other hardware
    - Terminate: Stop the VM Instance
  - **Automatic Restart**: Restart VM instances if they are terminated due to non-user-initiated reasons(maintenance event, hardware failure etc.)
- How to configure?
  - While creating new VM instance
  - In management tab> Availability policy

<hr>
<h2 id="7">Compute Engine features: Custom Machine types</h2>

- What do you do **when predefined VM options are NOT appropriate** for your workload?
  - Create a machine type customized to your needs(a **Custom Machine Type**)
- **Custom machine type**: Adjust vCPUs, memory and GPUs
  - Choose between E2,N2 or N1 machine types
  - Supports a wide variety of operating systems: CentOS, CoreOS, Debian, Red Hat, Ubuntu, Windows etc.
  - **Billed per vCPUs, memory provisioned** to each instance.
    - Example hourly price: $0.033174/vCPU + $0.004446/GB
  

<hr>

<h2 id="8">Compute Engine Features: GPUs</h2>

- How do you accelerate math intensive and graphics-intensive workloads for AI/ML etc?
  - Add a GPU(graphic processing unit) to your VM:
    - High performance for math intensive and graphics-intensive workloads
    - Higher costs
    - (REMEMBER) Use **images with GPU libraries**(deep learning) installed
      - Otherwise, GPU will not be used
    - **GPU Restrictions**:
      - **NOT SUPPORTED on all machine types**(For example, not supported on shared-core or memory-optimizied machine types) 
      - **On host maintenance** can only have value "Terminate VM Instance".
- Recommended Availability policy for GPUs
  - Automatic restart - On

### How to configure a GPU in VM?
1. Choose GPU machine families
  - A2, N1
  - GPU Specific machine families
2. Choose any machine family(can be used with GPU) and add GPUs to it


<hr>

<h2 id="9">Virtual Machines - Remember</h2>

- Associated with a project.
- Machine type availability can vary from region to regions.
- You can only change the machine type(adjust the number of vCPUs and memory) of a stopped instance
- VM's can be filtered by various properties.
  - Name, zone, machine type, internal/external IP, network, labels etc.
- Instances are zonal(Run in a specific zone(in a specific zone))
  - Images are global(You can provide access to other projects - if needed)
  - Instance templates are global(unless you use zonal resources in your templates)
- Automatic basic monitoring is enabled.
  - Default metrics: CPU utilization, network bytes(in/out), Disk throughput/IOPS
  - For memory utilization & Disk space utilization - Cloud monitoring agent is needed.



<hr>
<h2 id="10">Virtual Machine - Best practices</h2>

- Choose zone and region based on:
  - Cost, regulations, availability needs, latency and specific hardware needs.
  - Distribute instances in multiple zones and regions for high availability.
- Choose right machine type for you needs:
  - Play with them to find out the right machine type.
  - Use GPUs for math and graphic intensive applications
- Reserve for "committed use discounts" for constant workloads.
- Use preemptible instances for fault-tolerant, NON time critical workloads
- Use labels to indicate environment, team, business unit etc.





<hr>
<h2 id="11">Compute engine scenarios</h2>

- What are the pre-requisites to be able to create a VM instance?
  1. Project
  2. Billing account
  3. Compute Engine APIs should be enabled.
- You want dedicated hardware for your compliance licensing and management needs.
  - Sole tenant nodes
    - Create a sole tenant node group.
    - Add a node template(create one)
    - Affinity label(for assigning VM to this node group)
- I have 1000s of VM and I want to automate OS patch management, OS inventory management and OS configuration management (manage software installed)
  - VM Manager(OS Patch management)
- You want to login to your VM instance to install software
  - You can SSH into it.
- You don't want to expose VM to internet
  - Don't assign an external IP address
- You want to allow HTTP traffic to your VM.
  - Configure Firewall Rules

<hr>
<h2 id="12">Quick summary</h2>

### Image
- What OS and what software do you want on the VM instance?
- Reduce boot time and improve security by creating custom hardened images.
- You can share an image with other projects.

### Machine Types
- Optimized combination of compute(CPU, GPU), memory, disk(storage) and networking for specific workloads.
- you can create your own custom machine types when existing ones don't fit your needs.

## Static IP Addresses
- Get a constant IP addresses for VM instances
  
## Instance templates:
- Pre-configured templates simplifying the creation of VM instances

## Sustained use discounts: 
- Automatic discounts for running VM instances for significant portion of the billing month

## Committed use discounts:
- 1 year or 3 year reservations for workloads with predictable resource needs.

## Preemptible VM:
- Short-lived cheaper(upto 80%) compute instancs for non-time-critical, fault tolerance machines.
