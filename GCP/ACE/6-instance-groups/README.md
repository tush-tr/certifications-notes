- [Instance Groups](#instance-groups)
- [Managed Instance Groups(MIG)](#managed-instance-groupsmig)
    - [Important Features](#important-features)
- [Creating Managed Instance Groups](#creating-managed-instance-groups)
- [Updating a managed instance group (MIG)](#updating-a-managed-instance-group-mig)
  - [Rolling Update](#rolling-update)
  - [Rolling Restart/replace](#rolling-restartreplace)
  - [How to configure updates in MIGs?](#how-to-configure-updates-in-migs)
- [Scenarios: Instance Groups](#scenarios-instance-groups)
- [Using gcloud to configure MIGs](#using-gcloud-to-configure-migs)
    - [Creating, deleting, listing MIGs](#creating-deleting-listing-migs)
    - [Autoscaling](#autoscaling)
    - [Update existing MIG policies(ex: auto healing policies)](#update-existing-mig-policiesex-auto-healing-policies)
- [Making updates in MIG using gcloud](#making-updates-in-mig-using-gcloud)
    - [Resize the group](#resize-the-group)
    - [Recreate one or more instances(delete and recreate instances)](#recreate-one-or-more-instancesdelete-and-recreate-instances)
    - [Update specific instances](#update-specific-instances)
    - [Update instance template](#update-instance-template)
- [Making updates in MIG using gcloud (rolling out, canary)](#making-updates-in-mig-using-gcloud-rolling-out-canary)
  - [Update instance template](#update-instance-template-1)
  - [Rolling actions](#rolling-actions)
    - [Restart(stop \& start)](#restartstop--start)
    - [Replace(delete \& recreate)](#replacedelete--recreate)
    - [Updates instances to a new template(Basic/Canary)](#updates-instances-to-a-new-templatebasiccanary)


<hr>

# Instance Groups
- How do you create a group of VM instances?
  - Instance Group - Group of VM instances managed as a single entity.
    - Managed group of similar VMs having similar lifecycle as ONE UNIT.
- Two types of Instance groups
  - **Managed**(MIG): Identical VMs created using a template:
    - Features: Autoscaling, auto healing, and managed releases.
  - **Unmanaged**: Different configuration for VMs in same group:
    - Does not offer auto scaling, auto healing & other services.
    - NOT recommended unless you need different kinds of VMs.
- **Location** can be Zonal or regional
  - Regional gives you higher availability (RECOMMENDED)

<hr>

# Managed Instance Groups(MIG)
- Identical VMs created using an instance template
### Important Features
  - **Maintain** certain number of instances
    - If an instance crashes, MIG launches another instance.
  - **Detect application failures** using health checks(Self healing)
  - Increase and decrease instances based on load(**Auto scaling**)
  - Add **Load Balancer** to distribute load.
  - Create instances in multiple zones(regional MIGs)
    - Regional MIGs provide higher availability compared to zonal MIGs
  - **Release** new application versions without downtime
    - **Rolling updates**: Release new version step by step(gradually). Update a percentage of instances to the new version at a time.
    - **Canary Deployment**: Test new version with a group of instances before releasing it across all instances.


<hr>

# Creating Managed Instance Groups
- Instance template is mandatory.
- Configure auto-scaling to automatically adjust number of instances based on load
  - **Minimum** number of instances
  - **Maximum** number of instances
  - **Autoscaling metrics**: CPU utilization target or load balancer utilization target or Any other metric from stack driver.
    - **Cool down period**(Initialization period): How long to wait before looking at auto scaling metrics again?
    - **Scale in controls**: Prevent a sudden drop in no of VM instances
      - Example: Don't scale in by more than 10% or 3 instances in 5 minuts.
  - **Auto Healing**: Configure a health check with initial delay(How long should you wait for your app to initialize before running a health check?)

>Scale In: Reduce no of VM instances when load decreases
>Scale Out: Increase no of VM instances when load increases

<hr>

# Updating a managed instance group (MIG)
## Rolling Update
- Gradual update of instances in an instance group to the new instance template.
  - Specify new template:
    - (OPTIONAL) Specify a template for canary testing.
  - Specify how you want the update to be done:
    - When should the update happen?
      - Start the update immediately(Proactive) or when instance group is resized later(Opporturistic)
    - How should the update happen?
      - **Maximum surge**: How many instances are added at any point in time?
      - **Maximum unavailable**: How many instances can be offline during the update?
## Rolling Restart/replace
- Gradual restart or replace of all instances in the group
  - No change in template BUT replace/restart existing VMs.
  - Configure Maximum surge, Maximum unavailable and what you want to do?(Restart/replace)

## How to configure updates in MIGs?
- For rolling update: Click  on ```UPDATE VMS```
  - Change the instance template
  - For canary testing add one more template
    - Configure template, target size of instances(How many instance you want to test for new template)
    <img src="https://tusharrajpoot.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F74e8208e-b153-4494-bd89-f1e426773a05%2Fupdatemig.png?id=f1a8b0d6-9b64-47c8-adfa-4759f99fabb2&table=block&spaceId=a76e34cd-6f40-4ea9-b2ce-f4521ea829f1&width=2000&userId=&cache=v2">
    - Select Update type: Automatic or Selective
    <img src="https://tusharrajpoot.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fab08ac16-bb01-408d-a834-b1d2ac1ed6a0%2FScreenshot_2023-06-01_at_2.58.16_AM.png?id=fe7d4ef2-816c-40cb-861b-298d73b50b64&table=block&spaceId=a76e34cd-6f40-4ea9-b2ce-f4521ea829f1&width=1660&userId=&cache=v2">
  <details><summary>
  For Restart/replace VMS
  </summary>

  Click on ```RESTART/REPLACE VMS``` 
  <img src="https://tusharrajpoot.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F49409fee-c94c-46e3-b198-6d63465b60d7%2FScreenshot_2023-06-01_at_3.39.42_AM.png?id=599a6028-c9c5-442b-ade1-1b71d8f55f43&table=block&spaceId=a76e34cd-6f40-4ea9-b2ce-f4521ea829f1&width=1140&userId=&cache=v2">
  <img src="https://tusharrajpoot.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F99614ac9-ce5d-42b2-8ff7-fe243a844f75%2FScreenshot_2023-06-01_at_3.40.06_AM.png?id=2478a1a8-4695-44ed-a498-f1cdf2350e66&table=block&spaceId=a76e34cd-6f40-4ea9-b2ce-f4521ea829f1&width=1080&userId=&cache=v2"></details>
<hr>

# Scenarios: Instance Groups
- **You want MIG managed application to survive zonal failures**
  - Create multiple zone MIG(or regional MIG)
- **You want to create VMs of different configurations in the same group**
  - Create Un-managed instance group
- **You want to preserve VM state in an MIG**
  - Stateful MIG-
    - Preserve VM state(instanc name, attached Persistent disk and metadata).
    - Recommended for stateful workloads(database, data processing apps)
- **You want high availability in a MIG even when there are hardware/software updates**
  - Use an instance template with availability policy 
    - automatic restart: enabled
    - on-host maintenance: migrate 
    - Ensures live migration and automatic restarts
- **You want unhealthy instances to be automatically replaced**
  - Configure health check on the MIG (self healing)
- **Avoid frequent scale up & downs**
  - Cool-down periods/initial delay

---

# Using gcloud to configure MIGs

### Creating, deleting, listing MIGs
- ```gcloud compute instance-groups managed```
- List managed instance groups
  - ```gcloud compute instance-groups managed list```
- Create Instance group
  ```sh
  gcloud compute instance-groups managed create my-mig --zone us-central1-a --template my-instance-template --size 1
  ```
  - ```--health-check=HEALTH_CHECK```: How do you decide if an instance is healthy?
  - ```--initial-delay```: How much time should we give to an instance to start.
- Other Similar commands:
  - ```gcloud compute instance-groups managed delete/describe/list```
### Autoscaling
- Setup Autoscaling: ```set-autoscaling/stop-autoscaling```
  - ```sh
    gcloud compute instance-groups managed set-autoscaling my-mig --max-num-replicas=10
    ```
    - ```--cool-down-period```(default 60s): How much time should auto scaler wait after initiating an autoscaling aciton.
    - ```--scale-based-on-cpu --target-cpu-utilization --scale-based-on-load-balancing --target-load-balancing-utitlization```
    - ```--min-num-replicas --mode```(off/on(default)/only-scale-out) 
  - ```sh
    gcloud compute instance-groups managed stop-autoscaling my-mig
    ```
### Update existing MIG policies(ex: auto healing policies)
- ```gcloud compute instance-groups managed update my-mig```
  - ```--initial-delay```
  - ```--health-check```
---

# Making updates in MIG using gcloud
### Resize the group
- ```gcloud compute instance-groups managed resize my-mig --size=5```
### Recreate one or more instances(delete and recreate instances)
- ```gcloud compute instance-groups managed recreate-instances my-mig --instances=my-instance-1,my-instance-2```
### Update specific instances
- ```gcloud compute instance-groups managed update-instances my-mig --instances=my-instance-3,my-instance-4```(update specific instances from the group)
  - ```--minimal-action=none```(default)/refresh/replace/restart
  - ```--most-disruptive-allowed-action=none```(default)/refresh/replace/restart

### Update instance template
- ```gcloud compute instance-groups managed set-instance-template my-mig --template=v2-template```

# Making updates in MIG using gcloud (rolling out, canary)
## Update instance template 
- ```gcloud compute instance-groups managed set-instance-template my-mig --template=v2-template```
  - After updating instance template, you can trigger rollout of the new template using update-instances, recreate-instances or rolling-action start-update commands.
## Rolling actions
- Scenario: You want to manage your new release - v1 to v2 - without downtime
- ```gcloud compute instance-groups managed rolling-action```
### Restart(stop & start) 
  - ```gcloud compute instance-groups managed rolling-action restart my-mig```
    - ```--max-surge=5 or 10%```(Max no of instances updated at a time)
### Replace(delete & recreate)
  - ```gcloud compute instance-groups managed rolling-action replace my-mig```
    - ```--max-surge=5 or 10%``` (Max no of instances updated at a time)
    - ```--max-unavailable=5 or 10%```(max no of instances that can be down for the update)
    - ```--replacement-method=recreate/substitute```(Substitute(default) creates instances with new names. recreate reuses names)
### Updates instances to a new template(Basic/Canary)
- Basic Version(update all instances slowly step by step)
  - ```gcloud compute instance-groups managed rolling-action start-update my-mig --version=template=v1-template```
- Canary Version (update a subset of instances to v2)
  - ```gcloud compute instance-groups managed rolling-action start-update my-mig --version=template=v1-template --canary-version=template=v2-template,target-size=10%```
