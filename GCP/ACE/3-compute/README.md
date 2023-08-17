# Google Compute Engine
When we need to deploy applications we need servers and when we need to deploy applications in the cloud we need virtual servers.

<li>In corporate data centers, applications are deployed to physical servers.
<li>Where do we deploy applications in the cloud?
<ul>
<li>Rent virtual servers
<li>Virtual Machines- Virtual servers in GCP
<li>Google Compute Engine(GCE) - Provision and manage virtual machines.
</ul>

## Google compute engine - Features
<table>
<tr>
<th>Compute Engine</th>
<th>Persistent Disk</th>
<th>Cloud Load Balancing</th>
</tr>
</table>


<li>Create and manage lifecycle of virtual machine instances.
<li>Load balancing and autoscaling for multiple VM instances.
<li>Attach storage (& network storage) to your VM instances.
<li>Manage network connectivity and configuration for your VM instances.
<li>TODO:
<ul><li>Setup VM instances as HTTP(Web) server.
<li>Distribute load with load balancer.
</ul>

## Creating our first VM in GCP
<li>Create a few VM instances and play with them.
<li>Checkout the lifecycle of VM instances.
<li>Use SSH to connect to VM instances.


## Create a virtual machine using the GCP Console

<ol>
<li>In the Navigation menu (Navigation menu), click Compute Engine > VM instances.
<li>Click Create.
<li>On the Create an Instance page, for Name, type your VM name my-vm-1.
<li>For Region and Zone, select the region and zone .
<li>For Machine type, accept the default.
<li>For Boot disk, if the Image shown is not Debian GNU/Linux 10 (Buster), click Change and select Debian GNU/Linux 10 (Buster).
<li>Leave the defaults for Identity and API access unmodified.
<li>For Firewall, click Allow HTTP traffic.
<li>Leave all other defaults unmodified.
<li>To create and launch the VM, click Create.
</ol>

### Create using gcloud

```sh
gcloud compute instances create instance-2 --project=kinetic-road-371315 --zone=us-west4-b --machine-type=e2-medium --network-interface=network-tier=PREMIUM,subnet=default --maintenance-policy=MIGRATE --service-account=886257991781-compute@developer.gserviceaccount.com --scopes=https://www.googleapis.com/auth/devstorage.read_only,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/servicecontrol,https://www.googleapis.com/auth/service.management.readonly,https://www.googleapis.com/auth/trace.append --create-disk=auto-delete=yes,boot=yes,device-name=instance-2,image=projects/debian-cloud/global/images/debian-11-bullseye-v20221206,mode=rw,size=10,type=projects/kinetic-road-371315/zones/us-west4-b/diskTypes/pd-balanced --no-shielded-secure-boot --no-shielded-vtpm --no-shielded-integrity-monitoring --reservation-affinity=any
```

## Compute Engine Machine types and images

### Compute engine machine family
#### <li>What type of hardware do you want to run your worklaods on?

### <li>Different machine families for different workloads:
<ul>

<li>General purpose(E2,N2,N2D,N1): Best price-performance ratio
<ul>
<li>Web and application servers, small-medium databases, dev environments.
</ul>

<li>Memory optimized(M2,M1): Ultra high memory workloads
<ul>
<li>Large in-memory databases and in-memory analytics.
</ul>

<li>Compute Optimized(C2): Compute intensive workloads
<ul>
<li>Gaming Applications
</ul>

</ul>

### Compute Engine Machine Types
<table>
<tr>
<th>Machine Name</th>
<th>vCPUs^1</th>
<th>Memory(GB)</th>
<th>Max number of persistent disks(PDs)^2</th>
<th>Max total PD size(TB)</th>
<th>Local SSD</th>
<th>Maximum egress bandwidth(Gpps)^3</th>
</tr>
<tr>
<td>e2-standard-2</td>
<td>2</td>
<td>8</td>
<td>128</td>
<td>257</td>
<td>no</td>
<td>4</td>
</tr>
<tr>
<td>e2-standard-4</td>
<td>4</td>
<td>16</td>
<td>128</td>
<td>257</td>
<td>no</td>
<td>8</td>
</tr>
<tr>
<td>e2-standard-8</td>
<td>8</td>
<td>32</td>
<td>128</td>
<td>257</td>
<td>no</td>
<td>16</td>
</tr>
<tr>
<td>e2-standard-16</td>
<td>16</td>
<td>64</td>
<td>128</td>
<td>257</td>
<td>no</td>
<td>16</td>
</tr>
<tr>
<td>e2-standard-32</td>
<td>32</td>
<td>128</td>
<td>128</td>
<td>257</td>
<td>no</td>
<td>16</td>
</tr>
</table>

#### <li>How much CPU, memory or disk do you want?
<ul>
<li>variety of machine types are available for each machine family.
<li>Let's take an example: e2-standard-2:
<ul>
<li>e2- machine type family
<li>standard- Type of workload
<li>2- number of CPUs
</ul>
</ul>

#### <li>Memory, disk and networing capabilities increase along with vCPUs

### Image
#### <li> What operating system and what software do you want on the instance?
#### <li>Types of Images:
<ol>
<li>Public images: Provided & maintained by google or open source communities or third party vendors.
<li>Custom Images: Created by you for your projects.
</ol>

### Some commands we can use inside our debian VM

```sh
sudo su
apt update 
apt install apache2
ls /var/www/html
echo "Hello World!"
echo "Hello World!" > /var/www/html/index.html
echo $(hostname)
echo $(hostname -i)
echo "Hello World from $(hostname)"
echo "Hello World from $(hostname) $(hostname -i)"
echo "Hello world from $(hostname) $(hostname -i)" > /var/www/html/index.html
sudo service apache2 start
```

## Installing HTTP webserver on GCE VM

<li>Install apache web server

```sh
sudo su
apt install apache2
```

Now if I click on external IP of the virual machine I can see the apache debian webpage

Now lets go to apache html dir

```sh
ls /var/www/html
# output- index.html
```

we can put our content inside our index.html file

```sh
echo "Hi I'm Tushar" > /var/www/html/index.html
```

## Internal and External IPs

<li>External (Public) IP addresses are internet addressable.
<li>Internal(Private) IP addresses are internal to a corporate network.
<li>You can't have two resource with same public(external) IP address
<ul>
<li>However, two different corporate networks can have resources with same internal (private) IP address.
</ul>

<li>Al VM instances are assigned to at least one internal IP address.
<li>Creation of External IP addresses ca be enables for VM instances.
<ul>
<li>(Remember) When you stop an VM instance, External IP address is lost.
</ul>


## Static IP addresses
<li>Scenario: How do you get a constant external IP address for a VM instance?

<ol>
<li>Go to Search bar and search External IP addresses
<li>Click on external ip address(Part of VPC)
<li>We can see our External IP address which is assigned to our VM.
<li>Click on reserve Static address
<li>Add a name for your static IP address.
<li>click on reserve.
<li>We can see- one static address with type- static, and in VM was ephameral.
<li>Check a button- change in VM row click on that, and attach IP address to VM.
</ol>


<li>Static IP can be switched to another VM instance in same project.
<li>Static IP remains attached even if you stop the instance. You have to manually detach it.
<li>REMEMBER: You are billed for an static IP when you are not using it as well!
<ul>
<li>Make sure that you explicitly release an static IP when you're not using it.
</ul>

## Startup Script
### Simplify VM HTTP server setup
<li>How do you reduce the number of steps in creating an VM instance and setting up a HTTP server?

<li>Options:
<ul>
<li>Startup script
<li>instance template
<li>custom image
</ul>

### Bootstrapping with startup script

```sh
#!/bin/bash
apt update 
apt -y install apache2
echo "Hello world from $(hostname) $(hostname -I)" > /var/www/html/index.html
```

<li>Bootstrapping: Install OS patches or software when an VM instance is launched.
<li>In VM, you can configure startup script to bootstrap.

### How to use startup script
<li>While creating a VM
<li>Click on automation step
<li>Add you startup script

## Simplify VM creation with Instance templates
<li>Why do you need to specify all the VM instance details(Image, instance type etc) every time you launch an instance?
<ul>
<li>How about creating an instance template?
<li>Define machine type, image, labels, startup script and other properties.
</ul>

<li>Used to create VM instances and manage instance groups
<ul>
<li>Provides a convenient way to create similar instances.
</ul>
<li>CANNOT be updated.
<ul>
<li>To make a change, copy an existing template and modify it.
</ul>

<li>(Optional)Image family can be specified(Example- debian-9)
<ul>
<li>Latest non-depprecated version of the family is used.
</ul>

<ol>
<li>Go to instance template (under Compute engine)
<li>Click on create instance template
<li>Name it
<li>Add all configurations(region, zone, machine family, machine type etc.)
<li>Fire wall - allow http traffic
<li>Add startup script
<li>Create it
<li>No cost associated with instance template.
</ol>

<li>Create instance from instance template

## Reducing Launch time with a custom image
<li>Installing OS patches and software at launch of VM instances increases bootup time.
<li>How about creating custom image with OS patches and software pre-installed.
<ul>
<li>Can be created from an instance, a persistent disk, a snapshot, another image, or a file in cloud storage.
<li>Can be shared across projects.
<li>(Recommendation) Deprecate old images($ specify replacement image)
<li>(Recomendation) Hardering an image- customize images to your corporate security standards.
</ul>

<li>Prefer using custom image to startup script

> always stop the instance and create image from it.


## Troubleshooting launch of Apache web server on VM

<li>Make sure we're using the external IP of the running VM instance.
<li>SSH into it, make sure that apache web server is perfectly installed.

```sh
ls /var/www/html
cat /var/www/html/index.html
```

<li>Make sure that apache service is startedup fine.

```sh
sudo su
service apache2 start
```


<li>check url is working or not

<li>if not click on VM instance, check firewalls allow http traffic

## Reducing costs - Compute engine Virtual Machines

<table>
<tr>
<td>Feature</td>
<td>GCE</td>
</tr>

<tr>
<td>Sustained use discounts</td>
<td>Automatic discounts for using resources for long periods of time.<br>Ex: if you use N1, N2, machine types for more than 25% of a month, you get a 20% to 50% discount on every incremental minute.</td>
</tr>

<tr>
<td>Committed use discounts</td>
<td>Reserve compute instances ahead of time, commit for 1 year or 3 years, Up to 70% discount based on machine types and base images.</td>
</tr>
<tr>
<td>Preemptible VMs</td>
<td>Cheaper, temporary instances for non critical workloads(Fixed pricing, Max 24 hrs, cheapest)</td>
</tr>
</table>


## Achieving High availability with live migration and automatic restart

<li>How do you keep your VM instances running when a host system needs to be updated( a software or a hardware update needs to be performed?)
<li>Live migration
<ul>
<li>Your running instance is migrated to another host in the same zone.
<li>does not change any attributes or properties of the VM.
<li>Supported for instances with local SSDs.
<li>Not supported for GPUs and preemptible instances.
</ul>

<li>Important configuration- Availabality policy:
<ul>
<li>On Host maintainance: What should happen during periodic infrastructure maintenance?
<ul>
<li>Migrate(default): Migrate VM instance to other hardware/
<li>Terminate: Stop the VM instance
</ul>
<li>Automatic restart- Restart VM Instance if they are terminated due to non-user-initiated reasons(maintenance event, hardware failure etc)
</ul>

## VMs - Best practices
<li>Choose Zone and region based on:

<ul>
<li>Cost, regulations, availability needs, latency and specific hardware needs.
<li>distribute instances in multiple zones and regions for high availability.
</ul>

<li>Choose right machine type for you needs:

<ul>
<li>Play with them to find out the right machine type.
<li>Use GPUs for Math and graphical intensive applications.
</ul>

<li>Reserve for "committed use discounts" for constant workloads.
<li>Use preemptible instances for fault-tolerant, NON time critial workloads.
<li>Use labels to indicate enviroment, team, business unit etc.

## Compute Engine Scenarios

<table>
<tr>
<th>Scenario</th>
<th>Solution</th>
</tr>

<tr>
<td>What are the pre-requisite to be able to create a VM instance?</td>
<td>
<li>Project
<li>Billing account
<li>Compute engine APIs should be enabled.
</td>
</tr>

<tr>
<td>You want dedicated hardware for your compliance, licensing, and management needs</td>
<td>Sole-tenant nodes</td>
</tr>

<tr>
<td>I've 1000s of VM and I want to automate OS patch management, OS inventory management and OS configuration management(manage software installed)</td>
<td>Use VM Manager</td>
</tr>

<tr>
<td>You want to login to your VM instance to install software</td>
<td>SSH into it</td>
</tr>

<tr>
<td>Don't want to expose a VM to internet</td>
<td>Don't assign an external IP address</td>
</tr>
<tr>
<td>You want to allow http traffic to your VM</td>
<td>Configure firewall rules</td>
</tr>

</table>


