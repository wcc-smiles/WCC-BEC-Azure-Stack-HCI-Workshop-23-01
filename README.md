# WCC-BEC-Azure-Stack-HCI-Workshop-23-01
Workshop Guide for Azure Stack Eval Workshop 23-01

## About this documentation

The aim of this documentation is to walk you through creating a *sandboxed* instance of **Azure Stack HCI** running in **Nested Virtualization** on a **Single Azure IaaS VM** as the virtualization Host.

From this Azure hosted **IaaS nested virtualization instance** of Azure Stack HCI you will then be able to:

* Explore the management and operational aspects of Azure Stack HCI
* Evaluate running workloads on Azure Stack HCI

This is a useful approach for those who don't have multiple server-class pieces of hardware to test a full physical hyperconverged solution.

The important takeaway here is, by following this guide, you'll lay down a solid foundation on to which you can explore additional Azure Stack HCI scenarios in the future

*This documentation is based on the deperecated project:* https://github.com/Azure/AzureStackHCI-EvalGuide

A full self-paced deployment guide for evaluating Azure Stack HCI is now available from the Azure Docs on Microsoft Learn:
https://learn.microsoft.com/en-gb/azure-stack/hci/guided-quick-deploy-eval

----
**[Click Here to jump to the Deployment Workflow](#deployment-Workflow)**

-----------
## What is Azure Stack HCI?

> Azure Stack HCI is a hyperconverged cluster solution that runs virtualized Windows and Linux workloads in a hybrid on-premises environment. Azure hybrid services enhance the cluster with capabilities such as cloud-based monitoring, site recovery, and backup, as well as a central view of all of your Azure Stack HCI deployments in the Azure portal. You can manage the cluster with your existing tools including Windows Admin Center, System Center, and PowerShell.

> Initially based on Windows Server 2019, Azure Stack HCI is now a specialized OS, running on your hardware, delivered as an Azure service with a subscription-based licensing model and hybrid capabilities built-in. Although Azure Stack HCI is based on the same core operating system components as Windows Server, it's an entirely new product line focused on being the best virtualization host.

>For more info on Azure Stack HCI please refer to the [official documentation](https://docs.microsoft.com/en-us/azure-stack/hci/overview "What is Azure Stack HCI documentation") and <a href="https://sway.office.com/f4UzIZqrmGgMqTfZ?ref=Link.office.com/f4UzIZqrmGgMqTfZ?ref=Link" target="_blank">Deploying Azure Stack HCI</a>

-----------

## Evaluating in Azure

To remove the requirement for validated hardware *([Azure Stack HCI Catalog](https://aka.ms/azurestackhcicatalog "Azure Stack HCI Catalog"))* this workshop guide utilises **Azure** and **Nested Virtualization** that allows an evaluation environment implemented using a **Single Hyper-V host** inside an **Azure IaaS VM**.

**Note: This should be used ONLY for evaluation purposes and is NOT for any Production wokloads use. For Production use, Azure Stack HCI should be deployed on validated physical hardware found in the [Azure Stack HCI Catalog](https://aka.ms/azurestackhcicatalog "Azure Stack HCI Catalog")**.

### Nested Virtualization

*Nested Virtualization* allows an instance of Hyper-V be installed on a VM, that then runs virtual machines that are created within this second layer of virtualization; the *Host VM* runs as a virtualized Hyper-V Host.

The following illustration intends to visualise this concept.

![Nested virtualization architecture](https://github.com/wcc-smiles/WCC-BEC-Azure-Stack-HCI-Workshop-01/blob/main/Assets/wcc-smiles-ashci-nestedvirtualization-d00.svg)

The preceding illustration shows that *Level 0* is a physical layer, that contains the virtualization host(s) hardware, onto which a hypervisor is installed; this host level hypervisor creates the *level 1* Virtualization platform; this utilizes Azure's Hardware and Azure's Hyper-V platform to create VMs.

For our workshop we will take a single virtual machine created at the *level 1* Virtualization layer, deploy a Windows Server 2019 OS, with Hyper-V enabled, and install additional roles and features such as Domain Services, DNS, DHCP and Windows Admin Center,

This Virtualized Layer running inside a VM as opposed to running on hardware is what we term *Level 2* virtualization, this means it is running a **nested** virtualization layer.

The nested virtualization layer allows us to create VMs to be seen as Nodes for the Azure Stack HCI Cluster and run the Azure Stack HCI operating system; these are now Virtual Machine nodes used to create the cluster intead of needing Physical nodes to create the cluster; it is **important** to note and differenttiate the cluster type we will be creating here; we will be creating an **Azure Stack HCI** cluster and not a regular *Windows Server* cluster.

### Deployment of Azure Stack HCI nested in Azure

As outlined in previous sections this workshop guide will use **nested virtualization** in Azure to evaluate Azure Stack HCI and build the cluster; **No** on-prem hardware is required.

You will deploy a **single Azure IaaS VM** running Windows Server 2019 to act as your main Hyper-V host - and through PowerShell DSC, this will be automatically configured with the relevant roles and features needed for this workshop, as well as download all required binaries, and deploy 2 Azure Stack HCI nodes to be clustered.

To reiterate, the whole configuration will run **inside the single Azure IaaS VM**.

The following illustration intends to visualize what will be implemented:
![Architecture diagram for Azure Stack HCI nested in Azure](https://github.com/wcc-smiles/WCC-BEC-Azure-Stack-HCI-Workshop-01/blob/main/Assets/architecture-vms.png)

### Storage Spaces Direct

This is the storage-virtialization tecchnology built-in to Azure Stack HCI. Using Azure Stack HCI servers is the recommended approach for deploying Storage Spaces Direct on a cluster of physical servers; it is also currently still available for deployment on the Data Center editions of Windows Server OS (*...but for how long?*)

The following illustration intends to visualize the Storage Spaces Direct architecture:
![Storage Spaces Direct Deployment methods](https://github.com/Azure/AzureStackHCI-EvalGuide/blob/main/deployment/media/wac_cluster_type_ga.png)

Learn more here: https://learn.microsoft.com/en-us/azure-stack/hci/concepts/storage-spaces-direct-overview

### Managing Azure Stack HCI VMs

Windows Admin Center is the recommended tool for managing Windows Server and Azure Stack HCI deployments because of its versatility, extensibility, and security. It allows you to manage your computing environment through a web browser, in a consistent manner, regardless of whether that environment resides in an on-premises datacenter or in Azure.

-----------

## Approach

This workshop will be delivered based on the Microsoft GitHub [AzureStackHCI-EvalGuide
Guide](https://github.com/Azure/AzureStackHCI-EvalGuide) project; this is the basis of the workshop's hands-on walk-through steps, and provides the best learning opportunity to create the Azure Stack HCI Cluster, Networking, Storage Spaces Direct, and  Azure Services Integration in an Azure Hosted Nested Virtualization environment. 

The benefit of this GitHub Microsoft project is that it only automates the aspects that have no learning value, such as deploying the *IaaS VM host, creating Hyper-V instance, AD Domain Services, Certificate Services DNS, installing Windows Admin Center* on host etc. that are part of the nested virtualization stack rather than the actual Azure Stack HCI instance. 

This Nested Virtualization environment as outlined in previous architecture has already been created for each workshop participant in order to save time through minimizing tasks and aspects that have no learning value.

This aproach allows you to focus on the learning aspects that are of value and specific to Azure Stack HCI rather than spending time deploying underlying pre-req infrastructure that has little relative learning value.

---------
## Azure Resources Created & Costs

### Resources Created
For the purposes of this workshop, the workshop proctors have already deployed in advance for each particpant a **single** Azure Virtual Machine, disks and a number of asssociated resources; the following image shows purely for reference the *resources* that will be created by the Microsoft Project *Deploy to Azure* JSON template.

![Resources Created](https://github.com/wcc-smiles/Azure-Stack-HCI-Workshop-Draft02/blob/main/Assets/2022-03-17%2021_45_17-sm33-ashci-evalguide-rg1%20-%20Microsoft%20Azure.png "Created project resources in Azure")

The primary cost element is the creation of the *Virtual Machine* and the *Disks*; these costs will vary based on the type of VM created, the type, size and number of disks, and running hours. The public *Azure Calculator* can be used to estimate resource costs https://azure.microsoft.com/en-gb/pricing/calculator/

If the Microsoft provided project JSON templates defaults are created then the following resources can be expected to be consumed; note that only resources created that generate a consumption cost are listed, resources such as an *NSG*, *Virtual Network*, *Network Interface* have no associated costs. The costs estimated would vary with running hours.

* *Virtual Machine* - E16s_v4 *Qty - 1*
* *OS Disk* - 127GiB Standard HDD LRS *Qty - 1*
* *Data Disks* - 32 GiB Standard SSD LRS *Qty - 8*
* *Public IP* - Basic *Qty - 1* 

--------
## Deployment Workflow

This guide will walk you through deploying a sandboxed infrastructure - the general flow will be as follows:

* **Part 1 - Create and configure your Azure Stack HCI 21H2 Cluster**: Use Windows Admin Center to deploy an Azure Stack HCI, create a cluster, and register this cluster with Azure.

* **Part 2 - Explore the management and operation of your Azure Stack HCI 21H2 environment**: Explore the management and operational aspects within the Windows Admin Center.

* **Part 3 - Integrate Azure Stack HCI with Azure**: Use Windows Admin Center to register your Azure Stack HCI Cluster and manage in the Aure portal.

* **Part 4 - Explore the management and operation of your Azure Stack HCI environment**: Explore the management and operational aspects within the Windows Admin Center.

---

## Deployment Steps
The *next section* contains the steps you need to start creating an Azure Stack HCI environment.

### Prerequisites

*This section is for reference purposes, all following pre-reqs have been completed through automated provisioning.*

* For the purposes of this workshop, the workshop proctors have already deployed in advance for each particpant a single Azure Virtual Machine, disks and a number of asssociated resources to provde the IaaS nested virtualization platform.
* The VM in Azure has been deployed using an Azure Resource Manager template. This VM will run Windows Server 2019 Datacenter, with the full desktop experience. PowerShell DSC will automatically configure this VM with the appropriate roles and features, download the necessary binaries, and configure 2 Azure Stack HCI nodes, ready for clustering.
* This deployment creates a Standard_E16s_v4 VM type, which is a memory-optimized VM size, with 16 vCPUs, 128 GiB memory, and no temporary SSD storage. The OS drive will be 127 GiB, as well as an additional 8 data disks (32 GiB each), providing for 256 GiB to deploy Azure Stack HCI.

As part of the deployment, the following steps will be automated for you:

* A Windows Server 2019 Datacenter VM will be deployed in Azure; with a 127GB OS disk
* 8 x 32GiB (by default) Azure Managed disks will be attached and provisioned with a Simple Storage Space for optimal nested VM performance
* The Hyper-V role and management tools will be installed and configured
* An Internal vSwitch will be created and NAT configured to enable outbound networking
* The DNS role and accompanying management tools will be installed and DNS fully configured
* The DHCP role and accompanying management tools will be installed and DHCP fully configured. DHCP Scope will be enabled
* Windows Admin Center will be installed and pre-installed extensions updated
* The Microsoft Chrome browser will be installed
* The Azure Stack HCI binaries will be downloaded
* 2 x Azure Stack HCI nodes will be created and deployed, ready to start cluster creation

----------------

### Part 1 - Create and configure your Azure Stack HCI Cluster:
This section will use **Windows Admin Center** to deploy an *Azure Stack HCI cluster* and *register* this cluster with Azure.

A pre-created **nested virtualization** environment running on a **Single Azure IaaS VM** has already been created for you to use for this exercise.

*You're now ready to connect to the host IaaS VM to start the deployment of an Azure Stack HCI 2 node cluster.*

---------

#### Connect to your Azure VM ###
You need to connect into the host VM, using **Remote Desktop**. 

Browse to https://portal.azure.com/ and login with the allocated credentials below:

* user1[-10]@milesbettersolutions.onmicrosoft.com (*this user has the following roles: tenant global admin and subscription owner*)
* H#9%H3bDuy15

**NOTE**
Select **Skip for now** on the dialog box that appears when you sign into the Azure Portal.
![Connect to a virtual machine in Azure](https://github.com/wcc-smiles/Azure-Stack-HCI-Workshop-Draft02/blob/main/Assets/2022-07-23%2016_50_03-Sent%20Items%20-%20Steve.Miles%40westcoastcloud.co.uk%20-%20Outlook.png "Connect to a virtual machine in Azure")

1. From the portal navigate to **Resource Groups** and Click on your **allocated Resource Group** named: **user1[-10]-rg**. 

2. Click on your **allocated virtual machine** named: **AzSHCIHost001**. Once you're on the **Overview** blade for your **VM**, along the top of the blade, click on **Connect**.

![Connect to a virtual machine in Azure](https://github.com/Azure/AzureStackHCI-EvalGuide/blob/main/deployment/media//connect_to_vm.png "Connect to a virtual machine in Azure")

3. Select **RDP**. On the newly opened **Connect** blade, ensure the **DNS name** or **Public IP** is selected. Ensure the **RDP** port is set to **3389**. Then Click **Download RDP File**.

![Configure RDP settings for Azure VM](https://github.com/Azure/AzureStackHCI-EvalGuide/blob/main/deployment/media/connect_to_vm_properties.png "Configure RDP settings for Azure VM")

4. Once downloaded, locate the **.rdp** file and launch. Click **Connect** and when prompted use the following credentials: 

* **Username:** azureuser  
* **Password:** 3AUh2@R2sOs9

Accept any certificate prompts and you should be successfully logged into the Windows Server 2019 **AzSHCIHost001** VM.

*Close Server Manager as this is not required*.

**You're now ready to create an Azure Stack HCI cluster from the Windows Admin Center (WAC)**.

------

Creating a cluster
-----------

Here are the major steps in the **Create Cluster Wizard** in *Windows Admin Center*:

* **Get Started** - ensures that each server meets the prerequisites for and features needed for cluster join
* **Networking** - assigns and configures network adapters and creates the virtual switches for each server
* **Clustering** - validates the cluster is set up correctly. For stretched clusters, also sets up up the two sites
* **Storage** - Configures Storage Spaces Direct
* **SDN** - Configure Software Defined Networking

### Cluster type ###
Not only does Azure Stack HCI support a cluster in a single site (or a **Local Cluster** as we'll refer to it going forward) consisting of between 2 and 16 nodes, but, also supports a **Stretch Cluster**, where a single cluster can have nodes distrubuted across two sites.

* With 2 Azure Stack HCI nodes, you will be able to create a **local cluster**
* If you have 4 Azure Stack HCI nodes, you will have a choice of creating either a **local cluster** or a **stretch cluster**

In this workshop, we'll be focusing on deploying a 2-node **Local Cluster**.  You can [check out the official docs for Stretched Clusters](https://docs.microsoft.com/en-us/azure-stack/hci/concepts/stretched-clusters "Stretched clusters overview on Microsoft Docs")

### Cluster Wizard Steps ###

This section will walk through the key steps for you to set up the Azure Stack HCIcluster with the Windows Admin Center:

1. From the desktop of the **AdminCenter** VM, Click the **Google Chrome** browser desktop shortcut.

Set the **Privacy and security** settings to allow **Pop-ups and redirects** from the **Site settings**

Set the URL **https://admincenter.contoso.com** under the settings **Allowed to send pop-ups and use redirects**

**NOTE** if Chrome is not set as the default browser type change this in the *Settings Default Apps*

2. From **AzSHCIHost001**, connect to the **AdminCenter VM** via **RDP** by clicking on the **AdminCenter** RDP shortcut on your desktop. 

Login to **Windows Admin Center** using the domain account:

* **Username:** Contoso\Administrator  
* **Password:** Password01

![Creating a cluster](https://github.com/wcc-smiles/Azure-Stack-HCI-Workshop-Draft02/blob/main/Assets/2022-07-10%2014_19_01-AzSHCIHost001%20(1)%20-%20azshcihost001ukdftw.eastus.cloudapp.azure.com_3389%20-%20Remote%20.png)

3. Once logged into **Windows Admin Center WAC**, if you receive a notification that **extension catalogs are being updated** then wait for the message that the extensions were successfully updated and cick **OK** and WAC to relaunch; then, under **All connections**, Click **Add**.

4. On the **Add or create resources** screen, from **Server clusters**, Click **Create new** to open the **Cluster Creation wizard**.

![Creating a cluster](https://github.com/wcc-smiles/Azure-Stack-HCI-Workshop-Draft02/blob/main/Assets/2022-07-13%2020_20_27-AzSHCIHost001%20(1)%20-%20azshcihost001wcbdte.eastus.cloudapp.azure.com_3389%20-%20Remote%20.png)

5. From the **Choose the cluster type** screen ensure you select **Azure Stack HCI**, select **All servers in one site** and Click **Create**.

![Choose cluster type in the Create Cluster wizard](https://github.com/Azure/AzureStackHCI-EvalGuide/blob/main/deployment/media/wac_cluster_type_ga.png "Choose cluster type in the Create Cluster wizard")

6. On the **Check the prerequisites** page, review the requirements and Click **Next**.
7. On the **Add servers** page enter the credentials of:

* **username:** Contoso\Administrator
* **password:** Password01

8. Then one by one, enter the node names of your Azure Stack HCI nodes: **AZSHOST1** and **AZSHOST2**. Clicking **Add** after each one has been found. 

Each node will be validated, and given a **Ready** status when fully validated.  

This may take a few moments - once you've added all nodes, Click **Next**.

![Add servers in the Create Cluster wizard](https://github.com/wcc-smiles/Azure-Stack-HCI-Workshop-Draft02/blob/main/Assets/2022-07-10%2014_38_00-AzSHCIHost001%20(1)%20-%20azshcihost001ukdftw.eastus.cloudapp.azure.com_3389%20-%20Remote%20.png "Add servers in the Create Cluster wizard")

9. On the **Join a domain** page, details should already be in place, as these nodes have already been joined to the domain to save time. Click **Next**.

![Joined the domain in the Create Cluster wizard](https://github.com/Azure/AzureStackHCI-EvalGuide/blob/main/deployment/media/wac_domain_joined_ga.png "Joined the domain in the Create Cluster wizard")

10. On the **Install features** page, Windows Admin Center will query the nodes for currently installed features, and will request you install required features. To install the features, Click **Next**. 

**Note: This will take approx 1-2 minutes.**

![Installing required features in the Create Cluster wizard](https://github.com/Azure/AzureStackHCI-EvalGuide/blob/main/deployment/media/wac_installed_features_ga.png "Installing required features in the Create Cluster wizard")

12. On the **Install updates** page, Windows Admin Center will *take approx 30 seconds* to query the nodes for available updates, and will request you install any that are required. For the purpose of this guide and to save time, we'll **ignore this** and Click **Next**.
12. On the **Install hardware updates** page, in a nested environment this doesn't apply, so Click **Next**.
13. On the **Restart servers** page, click **Restart servers**,

**Note: This will take approx 1-2 minutes.**

![Restart nodes in the Create Cluster wizard](https://github.com/Azure/AzureStackHCI-EvalGuide/blob/main/deployment/media/wac_restart_ga.png "Restart nodes in the Create Cluster wizard")

14. Once servers have **restarted,** Click **Next**.

### Networking ###
With the servers configured with the appropriate features, and rebooted, you're ready to configure the network.

#### Network Setup Overview ####

1. On the **Choose how to configure host networking** for our learning scenario select the option **Manually configure host networking**, then Click **Next: Networking**.

2. When prompted **Do you want to remove existing virtual switches?** Click **No**.

3. Windows Admin Center will check the network adapters and verify your networking setup - it'll tell you how many NICs are in each node, along with relevant hardware information, MAC address and status information. 

**Note: This will take approx 1-2 minutes to check and validate the network adapters.**

 When the **Check the newtork adapters** screen has finished validating, you will be able to Click **Next**.

![Verify network in the Create Cluster wizard](https://github.com/wcc-smiles/Azure-Stack-HCI-Workshop-Draft02/blob/main/Assets/2022-07-10%2015_28_44-AzSHCIHost001%20(1)%20-%20azshcihost001ukdftw.eastus.cloudapp.azure.com_3389%20-%20Remote%20.png "Verify network in the Create Cluster wizard")

4. On the **Select the adapters to use for management** page, for our lab scenario we will use the option: **One physical network adapters for management**.

![Select management adapter in the Create Cluster wizard](https://github.com/Azure/AzureStackHCI-EvalGuide/blob/main/deployment/media/wac_management_nic_ga.png "Select management adapter in the Create Cluster wizard")

5. Then, for each node, **select the highlighted NIC** that will be dedicated for management.  This NIC will be named **vEthernet (sdnSwitch)**.
The reason only one NIC is highlighted, is because this is the only NICs that has an IP address on the same network as the WAC instance. Once you've finished your selections, scroll to the bottom, then click **Apply and test**. 

**Note: This will take approx 1-2 minutes to apply changes.**

![Select management adapters in the Create Cluster wizard](https://github.com/wcc-smiles/Azure-Stack-HCI-Workshop-Draft02/blob/main/Assets/2022-07-10%2015_30_45-AzSHCIHost001%20(1)%20-%20azshcihost001ukdftw.eastus.cloudapp.azure.com_3389%20-%20Remote%20.png "Select management adapters in the Create Cluster wizard")

6. Windows Admin Center will then apply the configuration changes to your NICs. When changes complete, Click **Next**.

7. On the **Virtual Switch** page, for our lab scenario we will be using the option: **Create one virtual switch for compute and storage together** then Click **Next**.

8. On the **RDMA** page in a nested environment, you'll be presented with an error if you tick the box, so Click **Next**.

9. On the **Define networks** page. One of the NICs have been claimed by the Management NIC. The remaining NICs will be shown in the table. 

![Define Networks in the Create Cluster wizard](https://github.com/wcc-smiles/WCC-BEC-Azure-Stack-HCI-Workshop-01/blob/main/Assets/2023-04-06%2011_58_58-AzSHCIHost001%20(1)%20-%20azshcihost001ry2eew.eastus.cloudapp.azure.com_3389%20-%20Remote%20.png "Define Networks in the Create Cluster wizard")

10. Click **Apply and test**, Windows Admin Center tests network connectivity.

**Note: This will take approx 1-2 minutes**

11. Once changes have been successfully applied, Click **Next: Clustering**.

### Clustering ###
In this section we create the local cluster.

1. At the start of the **Cluster** wizard, wait for it to **check a few things** and then on the **Validate the cluster** page, Click **Validate**.

2. Cluster validation will then start; once completed, you should see a successful message.

**Note: This will take approx 1-2 minutes**

![Validation complete in the Create Cluster wizard](https://github.com/Azure/AzureStackHCI-EvalGuide/blob/main/deployment/media/wac_validated_ga.png "Validation complete in the Create Cluster wizard")

3. On the **Validate the cluster** screen, Click **Next**

**Note** wait a moment while some checks are carried out; the wizard will then when ready move to the  **Create cluster** screen.

4. On the **Create the cluster** page, enter your **cluster name** as **AZSHCICLUS**.

**IMPORTANT** - make sure you use **AZSHCICLUS** as the name of the cluster as we pre-created the AD object in Active Directory to reflect this name

5. Under **IP address**, Click **Specify one or more static addresses**, and enter **192.168.1.4**.
6. Then Click **Create cluster**.

![Finalize cluster creation in the Create Cluster wizard](https://github.com/wcc-smiles/Azure-Stack-HCI-Workshop-Draft02/blob/main/Assets/wac_create_clus_static_ga.png "Finalize cluster creation in the Create Cluster wizard")

**Note: This will take approx 1-2 minutes**

When prompted to specify your credentials enter the following:
* **username:** Contoso\Administrator
* **password:** Password01 
* And set to use these for all connections, Click **Continue**.

**Note: This will take approx 1-2 minutes**

7. Once complete, Click **Next: Storage**.

![Cluster creation successful in the Create Cluster wizard](https://github.com/Azure/AzureStackHCI-EvalGuide/blob/main/deployment/media/wac_cluster_success_ga.png "Cluster creation successful in the Create Cluster wizard")

### Storage ###

In this section we create the storage.

1. On the storage landing page within the Create Cluster wizard, **Skip** the step to **Erase Drives**, then Click **Next**.

![Cleaning drives in the Create Cluster wizard](https://github.com/Azure/AzureStackHCI-EvalGuide/blob/main/deployment/media/wac_clean_drives_ga.png "Cleaning drives in the Create Cluster wizard")

2. On the **Check drives** page, validate that all your drives have been detected, and show correctly.  As these are virtual disks in a nested environment, they won't display as SSD or HDD etc. You should have **6 data drives** per node.  Once verified, Click **Next**.  

**Note: This wiill only take a moment while it makes some checks**

![Verified drives in the Create Cluster wizard](https://github.com/Azure/AzureStackHCI-EvalGuide/blob/main/deployment/media/wac_check_drives_ga.png "Verified drives in the Create Cluster wizard")

3. Storage Spaces Direct validation tests will then automatically run.

**Note: This will take approx 1-2 minutes**

![Verifying Storage Spaces Direct in the Create Cluster wizard](https://github.com/Azure/AzureStackHCI-EvalGuide/blob/main/deployment/media//wac_validate_storage_ga.png "Verifying Storage Spaces Direct in the Create Cluster wizard")

4. Once completed, you should see a successful confirmation. Then Click **Next**.

![Storage verified in the Create Cluster wizard](https://github.com/Azure/AzureStackHCI-EvalGuide/blob/main/deployment/media/wac_storage_validated_ga.png "Storage verified in the Create Cluster wizard")

5. The final step with storage, is to **Enable Storage Spaces Direct**, Click **Enable**.  

**Note: This will take approx 1-2 minutes**

![Storage Spaces Direct enabled in the Create Cluster wizard](https://github.com/Azure/AzureStackHCI-EvalGuide/blob/main/deployment/media/wac_s2d_enabled_ga.png "Storage Spaces Direct enabled in the Create Cluster wizard")

6. With Storage Spaces Direct enabled, Click **Next:SDN**.

### SDN ###

For the purpose of this workshop, we will **skip** the SDN configuration.

1. On the **Define the Network Controller cluster** page, Click **Skip**.
2. On the **confirmation page**, Click on **Go to connections list**.

-----------------
### Congratulations! ###
**You've now successfully deployed and configured your Azure Stack HCI 21H2 cluster!**

----------------

### Integrate Azure Stack HCI with Azure

**IMPORTANT NOTE** **You MUST sucessfully complete the registration with Azure to create VMs**.

Azure Stack HCI is delivered as an Azure service and needs to register within 30 days of installation per the Azure Online Services Terms. You will need to register your Azure Stack HCI cluster with for monitoring, support, billing, and hybrid services. 

**NOTE** - After registering your Azure Stack HCI cluster, the **first 60 days usage will be free**.

Upon registration, an Azure Resource Manager resource is created to represent each on-premises Azure Stack HCI cluster, effectively extending the Azure management plane to Azure Stack HCI. 

Information is periodically synced between the Azure resource and the on-premises cluster.  

**Complete the steps in the following Microsoft documentation**:
* https://learn.microsoft.com/en-gb/azure-stack/hci/guided-quick-deploy-eval?tabs=global-admin&tutorial-step=6

### Congratulations! ###
**You've now registered your Azure Stack HCI cluster**

Next Steps
-----------

You're now ready to move on to the next step of **[Explore the management and operation of your Azure Stack HCI environment]**

----------------

### Part 2 - Explore the management and operation of your Azure Stack HCI environment:
In this section we  can now begin to explore some of the additional capabilities within Azure Stack HCI and the Windows Admin Center.  

We'll cover a few recommended activities below, to expose you to some of the key elements of the Windows Admin Center, but for the rest, we'll [direct you over to the official documentation](https://docs.microsoft.com/en-us/azure-stack/hci/ "Azure Stack HCI documentation") and the Microsoft Learn content: https://docs.microsoft.com/en-gb/learn/modules/manage-azure-stack-hci-virtual-machine-workloads/2-virtual-machines


Create volumes for VMs
-----------
In this step, you'll explore **creating a volume** on an Azure Stack HCI cluster by using **Windows Admin Center**, and **Enable data deduplication and compression**. 

### Create a two-way mirror volume ###
These steps are performed in **Windows Admin Center**.

1. Once logged into **Windows Admin Center**, Click on your previously deployed cluster, **azshciclus.contoso.com**.

![Create a volume on Azure Stack HCI 21H2](https://github.com/wcc-smiles/Azure-Stack-HCI-Workshop-Draft02/blob/main/Assets/2022-07-14%2009_16_49-AzSHCIHost001%20(1)%20-%20azshcihost001wcbdte.eastus.cloudapp.azure.com_3389%20-%20Remote%20.png)

2. On the left hand navigation, under **Cluster Resources** select **Volumes**.  The central **Volumes** page shows you should have a single volume currently.
3. On the Volumes page, select the **Inventory** tab, and then select **Create**.
4. In the **Create volume** pane, Leave **Volume01** for the volume name, and leave **Resiliency** as **Two-way mirror**.
5. In Size on HDD, specify **20GB** for the size of the volume, then Click **Create**.

![Create a volume on Azure Stack HCI](https://github.com/Azure/AzureStackHCI-EvalGuide/blob/main/deployment/media//wac_vm_storage_ga.png "Create a volume on Azure Stack HCI")

6. Creating the volume can take a few minutes. Notifications in the upper-right will let you know when the volume is created. The new volume appears in the Inventory list.

![Volume created on Azure Stack HCI](https://github.com/Azure/AzureStackHCI-EvalGuide/blob/main/deployment/media/wac_vm_storage_deployed_ga.png "Volume created on Azure Stack HCI")

### Turn on deduplication and compression ###
During the **Create volume** wizard, there was the option to enable deduplication and compression at creation time; however you should be aware of how to enable it for existing volumes.

1. In **Windows Admin Center**, on the **Volumes** page, select the **Inventory** tab, and then select your **Volume01** volume.
2. On the **Volume Volume01** pane, you'll see a UI toggle control to enable **Deduplication**.  Click to enable it, and Click **Start**.
3. In the **Enable deduplication** pane, use the drop-down to select **Hyper-V** then click **Enable deduplication**. This should be enabled quickly, as there's no files on the volume.

**NOTE** - You'll notice there there are 3 options; **Default, Hyper-V and Backup**.  

If you're interested in learning more about Deduplication in Azure Stack HCI, you should [refer to the documentation on these usage types](https://docs.microsoft.com/en-us/windows-server/storage/data-deduplication/understand#usage-type "Deduplication usage types")

Whilst we deployed the volume using the Windows Admin Center, you can also do the same through PowerShell.
If you're interested in taking that approach, [check out the official docs that walk you through that process](https://docs.microsoft.com/en-us/azure-tack/hci/manage/create-volumes "Official documentation for creating volumes")

**You've now successfully created a volume**

----------------

Deploy a virtual machine
-----------
In this step, you'll deploy a VM using Windows Admin Center.

**IMPORTANT NOTE**  **You MUST have sucessfully completed the registration with Azure to create VMs**

### Create the virtual machine ###
1. You should still be in **Windows Admin Center** for the next steps.
2. On the left hand navigation, under **Cluster Resources** select **Virtual machines**. The **Virtual machines** page shows you have no virtual machines deployed currently.
3. On the **Virtual machines** page, from the **Inventory** tab, and then select **Add** -> **New**.
4. In the **New virtual machine** pane, enter the following pieces of information:

    * Name: **VM001**
    * Generation: **Generation 2** (*Recommended*)
    * Host: **Leave as recommended**
    * Path: **C:\ClusterStorage\Volume01** (*The volume you created in previous steps*)
    * Virtual processors: **1**
    * Startup memory (GB): **0.5**
    * Network: **ConvergedSwitch**
    * Storage: Click **Add** then **Create an empty virtual hard disk** and set size to **5GB**
    * Operating System: **Install an operating system later**
    * Click **Create**

5. The creation process will take a few moments, and once complete, **VM001** should show within the **Virtual machines view**.
6. Select the **VM** and then click **Power** -> **Start** - within moments, the VM should be running.

![VM001 up and running](https://github.com/Azure/AzureStackHCI-EvalGuide/blob/main/deployment/media/wac_vm001_ga.png "VM001 up and running")

7. Click on **VM001** to view the properties and status for this running VM.
8. Click on **Connect** and tick the box **Automatically connect with the certficate presented by this machine** and then click **Connect**.

You may get a **VM Connect** prompt:

![Connect to VM001](https://github.com/Azure/AzureStackHCI-EvalGuide/blob/main/deployment/media/vm_connect_ga.png "Connect to VM001")

9. From **Settings** and in the **Remote Desktop** pane, click on **Allow remote connections to this computer**, then **Save**.
10. Click the **Back** button in your browser to return to the **VM001** view, then Click **Connect**, and enter credentails; and when prompted with the certificate prompt, Click **Connect** and enter appropriate credentials.
11. There's **no operating system** installed here, so it should show a **UEFI boot summary**, but the VM is running successfully.
12. Click **Disconnect**.

**You've now successfully created a VM using the Windows Admin Center**

----------------

### Live migrate the virtual machine ###
The final step we'll cover is using Windows Admin Center to live migrate VM001 from it's current node, to an alternate node in the cluster.

1. Still within the **Windows Admin Center** , under **Compute**, Click on **Virtual machines**.
2. On the **Virtual machines** page, select the **Inventory** tab and select your **VM001**.
3. Make a note of which hist in the cluster the VM is currently running on.
4. Click **Manage**.  You'll notice you can Clone, Domain Join and also Move the VM. Click **Move**.

![Start Live Migration using Windows Admin Center](https://github.com/Azure/AzureStackHCI-EvalGuide/blob/main/deployment/media/wac_move_ga.png "Start Live Migration using Windows Admin Center")

5. In the **Move Virtual Machine** pane, ensure **Failover Cluster** is selected, and leave the default **Best available cluster node** to allow Windows Admin Center to pick where to migrate the VM to, then Click **Move**.
6. The live migration will then begin, and within a few seconds, the VM should be running on a different node.
7. Review the new **host** this is now running on.

**You've now successfully live migrated a VM between cluster nodes using the Windows Admin Center**

-----------
## Congratulations!

You've reached the end of the evaluation guide.  In this guide you have:

* Created an Azure Stack HCI cluster and registered with Azure for billing
* Used the Windows Admin Center to create and modify volumes, then deploy a virtual machine.

**Read the information referenced in the following Microsoft documentation**:
* https://learn.microsoft.com/en-gb/azure-stack/hci/guided-quick-deploy-eval?tabs=global-admin&tutorial-step=9

----------------
## Next steps
You can now take the opportunity to explore further the capabilities that Azure Stack HCI can provide; a handful of useful links have been provided below to get you started.

* [Azure Stack HCI Landing Page](https://azure.microsoft.com/en-us/products/azure-stack/hci "Azure Stack HCI Landing Page")
* [Compare Azure Stack HCI system requirements](https://learn.microsoft.com/en-us/azure-stack/hci/concepts/system-requirements "Azure Stack HCI system requirements")

* [Compare Azure Stack HCI to Windows Server](https://docs.microsoft.com/en-us/azure-stack/hci/concepts/compare-windows-server "Compare Azure Stack HCI to Windows")

* [Integrate Windows Admin Center with Azure](https://docs.microsoft.com/en-us/azure-stack/hci/manage/register-windows-admin-center "Integrate Windows Admin Center with Azure")
* [Explore Windows Admin Center](https://docs.microsoft.com/en-us/azure-stack/hci/get-started "Explore Windows Admin Center")
* [Manage clusters](https://docs.microsoft.com/en-us/azure-stack/hci/manage/cluster "Manage clusters")
* [Create and manage storage volumes](https://docs.microsoft.com/en-us/azure-stack/hci/manage/create-volumes "Create and manage storage volumes")
* [Manage virtual machines](https://docs.microsoft.com/en-us/azure-stack/hci/manage/vm "Manage virtual machines")
* [Monitor with with Azure Monitor](https://docs.microsoft.com/en-us/azure-stack/hci/manage/azure-monitor "Monitor with with Azure Monitor")
* [Integrate with Azure Site Recovery](https://docs.microsoft.com/en-us/azure-stack/hci/manage/azure-site-recovery "Integrate with Azure Site Recovery")
* [Integrate with Azure Virtual Desktop](https://docs.microsoft.com/en-us/azure/virtual-desktop/azure-stack-hci?WT.mc_id=AZ-MVP-5003408 "Integrate with Azure Virtual Desktop")
* [Azure Virtual Desktop for Azure Stack HCI FAQ](https://docs.microsoft.com/en-us/azure/virtual-desktop/azure-stack-hci-faq "Azure Virtual Desktop for Azure Stack HCI FAQ")

----------------

\
\
**[Click Here to go Back to Top](#Azure-Stack-HCI-Workshop-Draft01)**

## Questions / feedback

*Any questions can be addressed by the workshop proctors*


