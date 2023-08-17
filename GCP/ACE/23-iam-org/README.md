- [Organizing Resources in GCP](#organizing-resources-in-gcp)
  - [Resource Hierarchy in GCP](#resource-hierarchy-in-gcp)
    - [Resources](#resources)
    - [Folder](#folder)
    - [Organization](#organization)
  - [Recommended Resource Hierarchy for enterprises](#recommended-resource-hierarchy-for-enterprises)
- [Billing Accounts](#billing-accounts)
  - [Managing Billing - Budgets, Alerts and Exports](#managing-billing---budgets-alerts-and-exports)
- [IAM Best practices](#iam-best-practices)
  - [Principle of least privilege](#principle-of-least-privilege)
  - [Separation of Duties](#separation-of-duties)
  - [Constant Monitoring](#constant-monitoring)
  - [Other practices](#other-practices)
- [User Identity Management](#user-identity-management)
  - [Google Workspace](#google-workspace)
  - [Enterprise Own Identity Proivder](#enterprise-own-identity-proivder)
  - [Corporate Directory Federation](#corporate-directory-federation)
  - [Google Identity Platform](#google-identity-platform)
- [IAM Members and Identities](#iam-members-and-identities)
  - [Use Cases](#use-cases)
- [Organization Policy Service](#organization-policy-service)
- [IAM Policy at Multi Level](#iam-policy-at-multi-level)
- [IAM Predefined Roles-  Organization, Billing and Project Roles](#iam-predefined-roles---organization-billing-and-project-roles)
  - [Billing Roles](#billing-roles)
  - [Scenarios](#scenarios)
- [IAM Predefined roles - Compute Engine](#iam-predefined-roles---compute-engine)
  - [Compute Engine Admin](#compute-engine-admin)
  - [Compute Instance Admin](#compute-instance-admin)
  - [Compute Engine Network Admin](#compute-engine-network-admin)
  - [Compute Engine Security Admin](#compute-engine-security-admin)
  - [Compute Storage Admin](#compute-storage-admin)
  - [Compute Engine Viewer](#compute-engine-viewer)
  - [Compute OS Admin Login](#compute-os-admin-login)
  - [Compute OS Login](#compute-os-login)
- [IAM Predefined roles - App Engine](#iam-predefined-roles---app-engine)
  - [App Engine Creator](#app-engine-creator)
  - [App Engine Admin](#app-engine-admin)
  - [App Engine Viewer](#app-engine-viewer)
  - [App Engine Code Deployer](#app-engine-code-deployer)
  - [App Engine Service Admin](#app-engine-service-admin)
  - [App Engine Roles don't allow you to](#app-engine-roles-dont-allow-you-to)
- [IAM Predefined roles - Scenarios](#iam-predefined-roles---scenarios)
- [IAM Predefined Roles - GKE](#iam-predefined-roles---gke)
  - [Kubernetes Engine Admin (roles/container.admin)](#kubernetes-engine-admin-rolescontaineradmin)
  - [Kubernetes Engine Cluster Admin](#kubernetes-engine-cluster-admin)
  - [Kubernetes Engine Deployer](#kubernetes-engine-deployer)
  - [Kubernetes Engine Viewer](#kubernetes-engine-viewer)
- [IAM Predefined Roels - Cloud Storage](#iam-predefined-roels---cloud-storage)
  - [Storage Admin](#storage-admin)
  - [Storage Object Admin](#storage-object-admin)
  - [Storage Object Creator](#storage-object-creator)
  - [Storage Object Viewer](#storage-object-viewer)
  - [(REMEMBER)Storage Admin vs Storage Object Admin](#rememberstorage-admin-vs-storage-object-admin)
- [IAM Predefined Roles - BigQuery](#iam-predefined-roles---bigquery)
  - [BigQuery Admin](#bigquery-admin)
  - [BigQuery Data Owner](#bigquery-data-owner)
  - [BigQuery Data Editor](#bigquery-data-editor)
  - [BigQuery Data Viewer](#bigquery-data-viewer)
  - [BigQuery Job User](#bigquery-job-user)
  - [BigQuery User](#bigquery-user)
- [IAM Predefined Roles - Logging and Service Accounts](#iam-predefined-roles---logging-and-service-accounts)
  - [Logging and Audit Logging](#logging-and-audit-logging)
  - [Service Accounts](#service-accounts)
- [Other Important IAM Roles](#other-important-iam-roles)
- [SSH into Linux VMs](#ssh-into-linux-vms)
  - [Login Options](#login-options)
  - [Access Options](#access-options)
- [IAM Scenarios](#iam-scenarios)
    - [You want to give permanent access to a subset of objects in a Cloud Storage Bucket](#you-want-to-give-permanent-access-to-a-subset-of-objects-in-a-cloud-storage-bucket)
    - [You want to give permanent access to the entire bucket in a Cloud Storage Bucket](#you-want-to-give-permanent-access-to-the-entire-bucket-in-a-cloud-storage-bucket)
    - [You want to provide time limited access to a specific object in Cloud Storage bucket](#you-want-to-provide-time-limited-access-to-a-specific-object-in-cloud-storage-bucket)
    - [You want to give access to a set of resources to your development team](#you-want-to-give-access-to-a-set-of-resources-to-your-development-team)
    - [Which Role? Upload objects to Google Cloud Storage](#which-role-upload-objects-to-google-cloud-storage)
    - [Which Role? Manage Kubernetes API Objects.](#which-role-manage-kubernetes-api-objects)
    - [Which role? Manage service accounts](#which-role-manage-service-accounts)
    - [Which Role? View data in BigQuery?](#which-role-view-data-in-bigquery)

# Organizing Resources in GCP
## Resource Hierarchy in GCP
<img src="https://d33wubrfki0l68.cloudfront.net/eaddeba5e864fe63444fe247f7a7277b427e42c2/ed88b/gcpimages/02-architecture/resource-hierarchy-overview.png">

- Organization > Folder > Project > Resources
### Resources
- Are creaeted in projects
### Folder
- Can contain multiple projects
### Organization
- Can contain multiple folders

## Recommended Resource Hierarchy for enterprises
- Create separate projects for different environment:
  - Complete isolation between test and production environments
- Create separate folders for each department:
  - Isolate production applications of one department from another
  - We can create a shared folder for shared resources.
- One project per application per environment:
  - Let consider 2 apps: A1 and A2
  - Let's assume we need 2 environments: Dev and Prod
  - In the ideal world you will create 4 projects: A1-dev, A1-prod, A2-dev, A2-prod
    - Isolates environments from each other
    - DEV changes will not break PROD
    - Grant all developers complete access(create,delete, deploy) to DEV projects.
    - Provide production access to operations team only.

# Billing Accounts
- Billing Account is mandatory for creating resources in project.
  - Billing account contains the payment details
  - Every project with active resources should be associated with a Billing Account.
- Billing account can be associated with one or more projects.
- You can have multiple billing accounts in an Organization.
- (RECOMMENDATION) Create billing accounts representing your organization structure.
  - A Startup can have just one billing account.
  - A large enterprise can have separate billing account for each department
- Two types:
  - Self serve: Billed directly to Credit card or Bank Account.
  - Invoiced: Generate invoices(used by large enterprises)

## Managing Billing - Budgets, Alerts and Exports
- Setup cloud billing budget to avoid surprises:
  - (RECOMMENDED) Configure alerts.
  - Default alert thresholds set at 50%, 90% & 100%
    - Send alerts to Pub Sub (optional)
    - Billing admins and billing account users are alerted by e-mail.
- Billing data can be exported(on a schedule) to:
  - BigQuery
    - If you want to visualize it
  - Cloud Storage
    - For history/archiving

# IAM Best practices
## Principle of least privilege
- Give least possible privilege needed for a role!
  - Basic roles are NOT recommended.
    - Prefer predefined roles when possible
  - Use service accounts with minimum privileges
    - Use different service accounts for diffferent apps/purposes.
## Separation of Duties
  - Involve atleast 2 people in sensitive tasks:
    - Example: have separate deployer and traffic migrator roles
      - App Engine provides app engine deployer and App Engine service admin roles
        - App Engine Deployer can deploy new version but can't shift traffic
        - App Engine Service Admin can shift traffic but can't deploy new version.
## Constant Monitoring
- Review cloud audit logs to audit changes to IAM policies and access to Service account keys
  - Archive cloud audit logs in cloud storage buckets for long term retention.
## Other practices
- Use groups when possible

# User Identity Management
- Email used to create free trial account => "Super Admin"
  - Access everything in your GCP organization, folders and projects
  - Manage access to other users using their Gmail Accounts
- NOT Recommended for enterprises(using gmail accounts of users)
## Google Workspace
- Option 1. Your enterprise is using Google Workspace
  - Use Google workspace to manage users(groups etc)
  - Link google Cloud Organization with Google Workspace
## Enterprise Own Identity Proivder
- Option 2. Your enterprise uses an identity provider of its own
  - Federate Google Cloud with your identity provider
## Corporate Directory Federation
- Federate Cloud Identity or Google Workspace with your external identity provider(IdP) such as Active Directory or Azure active directory.
- Enable Single Sign on:
  - 1 Users are redirected to an external IdP to authenticate
  - 2 When users are authenticated, SAML assertion is sent to Google Signin
- Examples:
  - Federate Active directory with Cloud Identity by using Google Cloud Directory Sync(GCDS) and Active Directory Federation Services(AD FS)
  - Federating Azure AD with Cloud Identity.
## Google Identity Platform
- Add Providers
  - OpenID Connect
  - SAML
  - Google
  - Twitter
  - Facebook
  - Microsoft
  - Apple

# IAM Members and Identities
- Google Account
  - Represents a person(an email addres)
- Service Account
  - Represents an application account (NOT person)
- Google Group
  - Collection - Google & Service Accounts
    - Has an unique email address
    - Helps to apply policy to a group
- Google Workspace domain
  - Google workspace (formerly G Suite) provides collaboration services for enterprises:
    - Tools like: Gmail, Calender, Meet, Chat, Drive, Docs etc are included
    - If your enterprise is using Google Workspace, you can manage permissions using your Google Workspace Domain.
- Cloud Identity Domain
  - Cloud Identity is an identity as a Service(IDaaS) solution that centrally manages users and groups.
    - You can use IAM to manage access to resources for each cloud identity account.

## Use Cases
| Scenario                                                                                                                                           | Solution                                                                                                                                                                     |
|----------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| All members in your team have G Suite accounts. You are creating a new production project and would want to provide access to your operations team | Create a Group with all your operations team. Provide access to production project to the group.                                                                             |
| All members in your team have G Suite accounts. You are setting up a new project. You want to provide a one time quic access to a team member.     | Assign the necessary role directly to G Suite email addresss of your team member. If it is not a one time quick access, the recommended approach would be to create a Group. |
| You want to provide an external auditor access to view all resources in your project BUT he should not be able to make any changes.                | Give them roles/viewer role(Generally basic roles are NOT recommended BUT it is the simplest way to provide view only access to all resources)                               |
| Your application deployed on a GCE VM(Project A) needs to access cloud storage bucket from a different project (Project B)                         | In Project B, assign the right role to GCE VM service account from Project A.                                                                                                |

# Organization Policy Service
- How to enable centralized constraints on all resources created in an Organization?
  - Configure Organization Policy
  - Example: Disable creation of service accounts
  - Example: Allow/Deny creation of resources in specific regions
- Needs a Role - Organization Policy Administrator
- (REMEMBER) IAM Focuses on Who
  - Who can take specific actions on resources?
- (REMEMBER) Organization Policy focuses on What
  - What can be done on specific resources?

# IAM Policy at Multi Level
<img src="https://cloud.google.com/static/iam/img/iam-overview-policy.svg">

- IAM Policy can be set at any level of the hierarchy
- Resources inherit the policies of All Parents
- The effective policy for a resource is the union of the policy on that resource and it's parents
- Policy inheritance is transitive:
  - For example: Organization policies are applied at resource level
- You can't restrict policy at lower level if permission is given at an higher level.

# IAM Predefined Roles-  Organization, Billing and Project Roles
- Organization Administrator
  - Define resource hierarchy
  - Define access management policies
  - Manage other users and roles
- Billing Account Creator
  - Create billing accounts
- Billing Account Administrator
  - Manage Billing Accounts(payment instruments, billing exports, link and unlink projects, manage roles on billing account)
    - CANNOT create a Billing Account
- Billing Account User
  - Associate projects with Billing accounts
  - Typically used in combination with Project Creator
  - These 2 roles allow user to create new project and link it with a billing account.
- Billing account viewer
  - See all billing account details

## Billing Roles
| Roles                         | Description                                  | Use Case      |
|-------------------------------|----------------------------------------------|---------------|
| Billing Account Creator       | Permissions to create new billing accounts   | Finance Team  |
| Billing Account Administrator | Manage billing account but can't create them | Finance Team  |
| Billing Account User          | Assign projects to billing accounts          | Project Owner |
| Billing Account Viewer        | View only access to billing account          | Auditor       |

## Scenarios
- I'm creating a project and I want to associate an existing billing account with the project.
  - Roles needed: Project creator and Billing account user(link project to billing account)
- I'm a billing auditor
  - Billing Account viewer role.


# IAM Predefined roles - Compute Engine
## Compute Engine Admin
- Complete control of compute - Instances, images, load balancers, network, firewalls etc.
## Compute Instance Admin
- Create, modify, and delete virtual machine instances and disks
## Compute Engine Network Admin
- Complete access to networking resources(routes, networks, health checks, VPN, Gateways etc) and READ only access to (firewall rules and SSL certificates)
## Compute Engine Security Admin
- Complete access to firewall rules and SSL certificates
## Compute Storage Admin
- Complete access to disks, images, snapshots
## Compute Engine Viewer
- Read ONLY access to everything in compute.
## Compute OS Admin Login
- Login to a compute engine instance as an administrator user
## Compute OS Login
- Login to a compute engine instance as a standard user


# IAM Predefined roles - App Engine
- App Engine roles (CRUD- Create, Read(Get/List), Update, Delete)
## App Engine Creator
- Applications(CD)(responsible for creating an application)
## App Engine Admin
- applications(RU), services/instances/versions(CRUD),operations
## App Engine Viewer
- app engine.versions.getFileContents
- Only role that can view code
## App Engine Code Deployer
- versions(CRD), applications/services/versions(R)
## App Engine Service Admin
- Versions(RUD),Applications(R), services/instances(CRUD)
- operations: Split or migrate traffic, start and stop a version
## App Engine Roles don't allow you to
- View and download application logs
- View monitoring charts in the Cloud Console
- Enable and disable billing
- Access configuration or data stored in other services.

# IAM Predefined roles - Scenarios
- What is difference between Compute Engine Admin vs Compute Instance Admin
  - Compute engine admin can do everything with instances and disks ONLY.
  - Compute Engine Admin is admin for everything in compute - instances, disks, images, network, firewalls etc.
- What is secure way of setting up application deployment?
  - Application Deployer - Roles: App Engine Deployer + Service Account Uuser
    - Limited to deploying new versions and deleting old versions that are not serving traffic
    - Will NOT be able to configure traffic
  - Operations - Role: App Engine Service Admin
    - CAN'T deploy a new version of an app
    - Change traffic between versions


# IAM Predefined Roles - GKE
- Clusters and Applications deployed to the cluster
- Kubernetes API Objects: Deployment/service/replicaset/secrets etc. Deploy microservice, expose it by creating a service etc.
- Cluster Operations: Create cluster or a node pool, add nodes to node pool.
## Kubernetes Engine Admin (roles/container.admin)
- Complete access to clusters and Kubernetes API objects.
## Kubernetes Engine Cluster Admin
- Provides access to management of clusters(Can't access kubernetes API Objects - Deployments, PODs etc)
## Kubernetes Engine Deployer
- Manage Kubernetes API objects(and read cluster info)
## Kubernetes Engine Viewer
- Get/list cluster and API objects.

# IAM Predefined Roels - Cloud Storage
## Storage Admin
- ```storage.buckets.*```, ```storage.objects.*```
## Storage Object Admin
- ```storage.objects.*```(Doesn't have ```storage.buckets.*```)
## Storage Object Creator
- ```storage.object.create```
## Storage Object Viewer
- ```storage.objects.get```,```storage.objects.list```
>REMEMBER: Container registry stores container images in cloud storage buckets.
- Control access to images in container registry using cloud storage permissions.
## (REMEMBER)Storage Admin vs Storage Object Admin
- Storage Admin can create buckets and play with objects
- Storage object admin can't create buckets but can play with objects in a bucket.

# IAM Predefined Roles - BigQuery
- Data Operations: View tables and Data etc
- Query Operations: Run jobs(Run queries), view status of jobs etc.
## BigQuery Admin
- ```bigquery.*```
## BigQuery Data Owner
- ```bigquery.datasets.*```,```bigquery.models.*```,```bigquery.routines.*```,```bigquery.tables.*```(Doesn't have access to jobs)
## BigQuery Data Editor
- ```bigquery.tables.(create/delete/export/get/getData/getIamPolicy/list/update/updateData/updateTag)```
- ```bigquery.models.*```, ```bigquery.routines.*```,```bigquery.datasets.(create/get/getIamPolicy/updateTag)```
## BigQuery Data Viewer
- get/list bigquery.(datasets/models/routines/tables)
## BigQuery Job User
- ```bigquery.jobs.create```
## BigQuery User
- BigQuery Data viewer + get/list(jobs, capacityCommitments/reservations etc)
- To see data, you need either BigQuery User or BigQuery Data Viewer roles
  - You can't see data with BigQuery job user roles
- BigQuery data owner or data viewer roles don't have access to jobs.


# IAM Predefined Roles - Logging and Service Accounts
## Logging and Audit Logging
- ```roles/logging.viewer``` (logs viewer)
  - Read all logs except access transparency logs and data access audit logs
- ```roles/logging.privateLogViewer``` (Private Logs Viewer) 
  - Logs Viewer + Read access transparency logs and Data access audit logs
- ```roles/logging.admin```(Logging Admin)
  - All Permissions related to Logging
## Service Accounts
- ```roles/iam.serviceAccountAdmin```
  - Create and Manage service accounts
  - ```roles/iam.serviceAccountUser```
    - Run operations as the service account
      - ```roles/iam.serviceAccountUser``` => Create and manage instances that use a service account. This needs to be added to Admin roles if you want them to attach service accounts with instances
  - ```roles/iam.serviceAccountTokenCreator```
    - Impersonate service accounts (create OAuth2 access tokens, sign blobs or JWTs, etc)
  - ```roles/iam.serviceAccountKeyAdmin```
    - Create and manage (and rotate) service account keys.

# Other Important IAM Roles
- ```roles/iam.securityAdmin```
  - Get and set any IAM Policy
- ```roles/iam.securityReviewer```
  - List all resources & IAM Policies
- ```roles/iam.organizationRoleAdmin```
  - Administer all custom roles in the organization and the projects below it
- ```roles/iam.organizationRoleViewer ```
  - Read all custom roles in the organization and the projects below it.
- ```roles/iam.roleAdmin```
  - Provides access to all custom roles in the project
- ```roles/iam.roleViewer```
  - Provides read access to all custom roles in the project.
- ```roles/browser```
  - Read access to browse the hierarchy for a project, including the folder, organization and IAM Policy.

# SSH into Linux VMs
## Login Options
- Compute Engine Linux VMs uses Key-based SSH Authentication.
- Two options to manage keys
  - Metadata managed: 
    - Manually create and configure individual SSH Keys.
  - OS Login:
    - Manage SSH access without managing individual SSH Keys.
      - Recommended for managing multiple users across instances or projects.
      - Your linux user account is linked to your Google Identity
      - To enable: Set ```enable-oslogin``` to true in metadata.
        - ```gcloud compute project-info/instances add-metadata --metadata enable-oslogin=TRUE```
    - Advantage: Ability to import existing Linux accounts from onpromisse AD and LDAP.
    - Users need to have roles: roles/compute.osLOgin or roles/compute.osAdminLogin
- For Windows: windows instances use password authentication (username and password)
  - Generate using console or gcloud (gcloud compute reset-windows-password)

## Access Options
1. Console - SSH Button
   - Ephemeral SSH Key pair is created by Compute engine.
2. Gcloud - ```gcloud compute ssh```
   - A username and persistent SSH key pair are created by Compute Engine.
   - SSH Key pair reused for future interactions
3. Customized SSH Keys
   - Metadata managed: Upload the public key to project metadata
   - OS Login: Upload your publish SSH Key to your OS Login profile.
     - ```gcloud compute os-login ssh-keys add```
     - OS Login API: POST https://oslogin.googleapis.com/v1/users/ACCOUNT_EMAIL:importSshPublicKey
- You can disable Project Wide SSH Keys on a specific compute instance
  - ```gcloud compute instances add-metadata [Instance-name] --metadata block-project-ssh-keys=TRUE```

# IAM Scenarios
### You want to give permanent access to a subset of objects in a Cloud Storage Bucket
- Use ACLs
### You want to give permanent access to the entire bucket in a Cloud Storage Bucket
- Use IAM
### You want to provide time limited access to a specific object in Cloud Storage bucket
- Create a signed URL
### You want to give access to a set of resources to your development team
- Create a group with your development team as member. Bind the right predeifned roles to your Group.
### Which Role? Upload objects to Google Cloud Storage
- Storage Object Creator
### Which Role? Manage Kubernetes API Objects.
- Kubernetes Engine Developer
### Which role? Manage service accounts
- Service Account Admin
### Which Role? View data in BigQuery?
- BigQuery Data Viewer


