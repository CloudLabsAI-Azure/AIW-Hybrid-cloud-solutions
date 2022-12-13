# HOL-1: Exercise 1: Getting started with Azure Arc

#### Secure and govern across environments 

As noted before, Contoso has Windows, Linux, SQL Servers, and Kubernetes clusters across multiple locations, including on-premises datacenters, distribution centers and multiple public clouds. Imagine you are a member of Contoso’s Central IT team. You want to be able to have central visibility of all these resources and manage them in a consistent manner, so your business operations run smoothly.

In this exercise, you will learn how Arc can help Contoso onboard and organize servers and Kubernetes clusters in the Azure Portal, govern them using Azure Policy and enable central monitoring by integrating with Azure Monitor.

## Task 1: Getting started with Hyper-V Infrastructure

Hyper-V is Microsoft's hardware virtualization product. It lets you create and run a software version of a computer, called a virtual machine. Each virtual machine acts like a complete computer, running an operating system and programs. When you need computing resources, virtual machines give you more flexibility, help save time and money, and are a more efficient way to use hardware than just running one operating system on physical hardware. In this task, you will walk through an on-prem environment that is hosted on Hyper-V. You will find three virtual machines hosted on the Hyper-V server, which you will onboard to Azure Arc and play around with.

1. You can see a virtual machine desktop 💻 (LabVM/ARCHOST) is loaded on the left side in your browser. Use this virtual machine throughout the workshop to perform the lab. You can also connect to the virtual machine using any RDP client using the ARCHOST VM credentials provided in the **Environment Details** tab.

    ![](.././media/cloudlab-vm-guidev1.png "Lab Environment")

1. In the **LabVM/ARCHost VM**, double click on the Azure portal shortcut of Microsoft Edge browser which is provided on the desktop.
  
    ![](.././media/select-azureportal.png "Select Azure Portal")
    
1. On the **Sign into Microsoft Azure** tab, you will see the login prompt. Enter the following **Email/Username** and then click on **Next**. 
   * Email/Username: <inject key="AzureAdUserEmail"></inject>
   
1. Now, enter the **Password** which you have already received for the above account.
      * Password: <inject key="AzureAdUserPassword"></inject>
      
1. If you see the pop-up **Stay Signed in?**, click No

1. If you see the pop-up **You have free Azure Advisor recommendations!**, close the window to continue the lab.

1. If **Welcome to Microsoft Azure** popup window appears, click **Maybe Later** to skip the tour.

1. Navigate to the Resource Group in the Azure portal navigate section.

    ![](.././media/navigate-resource-group.png "Select Resource Group from Navigate Option")    
  
1. Click on the azure-arc Resource group and confirm whether you have total 12 records to confirm all the below resources are deployed successfully.

   ![](https://raw.githubusercontent.com/CloudLabsAI-Azure/AIW-Hybrid-cloud-solutions/main/media/resources-azure-arc-rg.png)

   * In the Resource group we have one **Virtual Machine**, **Kubernetes Service**, **Storage account** and **Log Analytics workspace** deployed.

   * **Virtual Machine**: You will be using the Virtual Machine which is already open on the left side of the page to perform all the Lab exercises.

   * **Kubernetes Services**: We have already deployed the Azure Arc Data controller on to the Kubernetes Service and in later exercises we will be deploying Azure arc enabled data resources on to the Kubernetes cluster using Azure Arc data services.

   * **Storage Account**: You will use this storage account to backup and restore the database to SQL MI.
   
   * **Log Analytics workspace**: You will be using one of the Log Analytics workspace to upload and view the logs generated from both Postgres Hyperscale and SQL MI servers.

1. Now, double click on the **Hyper-V Manager** from the desktop of the provided Virtual Machine to start the Hyper-V Manager.

    ![](.././media/select-hyper-v.png "Select hyper-v from desktop")

1. Then, you need to Select **ARCHOST- <inject key="DeploymentID/Suffix" />** to connect with the Local Hyper-V server.

    ![](.././media/archost-localserver.png "ARCHOST Server")

1. You will find two guest virtual machines running on the Hyper-V manager. Find a list of guest virtual machines with private IP address.
     * **ubuntu-k8s** - ```192.168.0.8```
     * **sqlvm** - ```192.168.0.4```
     
    ![](.././media/guestvms1.png "Guest VMs")
    
    > **Note**: If you see VMs are in stopped state, and when you click on Start button, if VMs are not getting started or if it is throwing any error. Then, right click on Virtual Machine in stopped state and then click on **Delete saved state..**. After that, you can start the VMs and proceed to next task.

## Task 2: Onboard Linux Machine to Azure Arc

Now, let’s onboard the Linux Machine to Azure Arc as an Arc-enabled server. This VM also has the Kubernetes cluster that we will use in the subsequent labs. So, here we will onboard ubuntu-k8s VM to Azure Arc

1. From the start menu of the ARCHOST VM, search for **putty** and open it.

    ![](.././media/startputty.png "Search Putty")
     
1. In the Putty Configuration tool, enter the **ubuntu-k8s** VM private IP - ```192.168.0.8```, make sure the Port value is ```22```. Once you enter the private IP of the ubuntuk8s VM, click on the **Open** to lunch the terminal.

    ![](.././media/putty-enter-ip.png "Enter ubuntu-k8s VM private IP")
    
1. Enter the **ubuntu-k8s** VM username - ```demouser``` in **login as** and then hit **Enter**. Now, enter the password - ```demo@pass123``` and press **Enter**. Remember the password will be hidden and not be visible in the terminal.

    - **Username** : Enter demouser
      ```BASH
      demouser
      ```
   
    - **Password** : Enter demo@pass123
      ```BASH
      demo@pass123
      ```

    ![](.././media/enter-ubuntu-k8s-credentials.png "Enter ubuntu-k8s credentials")
    
    > **Note** : To paste any value in the Putty terminal, just copy the values from anywhere and then right-click on the terminal to paste the copied value.
    
1. Login to the **Root user account** using sudo command; enter the following command and then provide **password** - ```demo@pass123``` when prompted for the password.

    - Command:
      ```BASH
      sudo su
      ```

    - Password:
      ```BASH
      demo@pass123
      ```
    
    ![](.././media/root-login.png "Root Login")
    
 1. Run the below commands to upgrade the az packages and az module. 
   
     ```
      curl https://bootstrap.pypa.io/get-pip.py > get-pip.py
      python3 get-pip.py
      apt install pip
      python3 get-pip.py
      python3 -m pip install -U pip
      python3 -m pip install --upgrade pip --target /opt/az/lib/python3.6/site-packages/
      az upgrade -y
      init 6
    ```
    > **Note** : If in case, the above commands fail then please run the below mentioned command:
    
    ```
     sudo apt-get install python3-pip
    ```
 
1. Open a new Putty session, re-perform the steps from step-2 to step-4 of the same task to get the upgraded packages and then continue from  step-7.
    
1. Next, you have to navigate back to the Desktop of the provided virtual Machine ARCHOST VM 💻, and then click on `installArcAgentLinux.txt` file to open it.

   ![](.././media/variableazlogin.gif "Install Arc Agent")

1. Then, select the first 7 lines and, then right click and copy. 

1. Then, go back to putty session and paste it in ubuntu-k8s VM by doing right click and it will start getting executed. 

1. Once it is executed, you have declared the values of AppID, AppSecret, TenantID, SubscriptionID, ResourceGroup, and location, and then logged into azure using the 7th line. You can also find the values of these variables in the **Environment Details** tab. These variables are required for the next steps.

    ![](.././media/variableazlogin.png "azlogin")
    
1. Now, to download the Azure Arc installation package for Linux, run the below command:

   ```
   wget https://aka.ms/azcmagent -O ~/install_linux_azcmagent.sh
   ```
    
   ![](.././media/download-arc-agent.png "Download Arc Linux Agent")
    
1. Then, to install Azure Arc agent on the VM, run the below command :

   ```
   bash ~/install_linux_azcmagent.sh
   ```

   ![](.././media/run-installation.png "Install Arc Agent")
    
1. Once the installation is successful, you will see the following message in terminal **Latest version of azcmagent is installed**.

   ![](.././media/arcagent-installed.png "Arc Agent latest version installed")    
    
1. Finally, connect the ubuntu-k8s machine to Azure Arc by running the connect command given below.  Once you run the command given below, it will take 1-2 minutes to onboard the machine to Azure Arc. 
    
   ```
   azcmagent connect --resource-group $ResourceGroup --tenant-id $TenantID --location $location --subscription-id $SubscriptionId -i $AppID -p $AppSecret
   ```

   > Remember, we are using variables declared earlier in step 8. If you have connected with a new putty session, you may have to run steps 4 to 9 again.
     
   ![](.././media/connected-azure-arc.png "Connected to Arc")

1. Let's verify the onboarding of **ubuntu-k8s** server on Azure Arc from Azure portal. Switch to the browser tab where you have logged into Azure portal already in step 1 and browse TO **azure-arc** resource group

1. Now click on Refresh from the azure-arc overview page.

1. Then search and verify if **ubuntu-k8s** resource of resource type: **Server - Azure Arc** got created. Click on the resource to get more information.

   ![](.././media/varify-onboard-arc-ubuntuk8s.png "ubuntu k8s onboarded")

1. On **ubuntu-k8s** Server - Azure Arc **Overview** page, verify that the status is **Connected**. You can also check other details from this tab like Computer name, Operating system, Operating system version and Agent version of ubuntu machine. 
   > **Note**: Operating system and Agent version that you see may not match with the provided screenshot if there were any updaes to the Agent/ OS Version.

   ![](.././media/hol1ss1.png "ubuntu k8s onboard status check")

## Task 3: Onboard Kubernetes Cluster to Azure Arc

We have onboarded the Linux VM to Azure Arc and verified in task 2. Now, you will onboard the local Kubernetes cluster to Azure Arc. So, here we onboard **MicroK8s** Kubernetes cluster to Azure Arc which is hosted on **ubuntu-k8s** VM. We already have the Microk8s Kubernetes cluster ready and configured with the Arc enabled CLI extensions.

   > **Note** : If you have closed the putty after completing **task 2**, then perform the first 8 steps of task 2 again and then return to perform this task. Make sure that you perform all steps with root user in ubuntu-k8s vm.

1. To install helm, you need to run the following commands within the terminal of ubuntu-k8s VM that is opened in putty:
            
     > **Info** : Helm is a Kubernetes deployment tool for automating creation, packaging, configuration, and deployment of applications and services to Kubernetes clusters. The kubernetes app's manifests are stored in helm charts.
   ```
   curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3
   chmod 700 get_helm.sh
   ./get_helm.sh
   ```
   **Note**: Incase if you see `Could not find git. It is required  for plugin installation.` warning, please ignore it and continue with the lab.
    
   ![](.././media/installhelm.png "installhelm")

1. Next, you have to run the below command to ensure the Azure CLI version and customlocation extension for Az CLI are the latest.

    ```
    az upgrade -y
    az extension add --name customlocation
    ```
    
    >**Note**: If you face any exception while updating CLI version, please rerun the command again.

1. Then, you will update the Arc enabled Kubernetes CLI extension to ensure that we are always using the lastest k8s extension for Azure CLI.

   ```
   az extension update --name connectedk8s
   ```
    
   ![](.././media/update-k8s-extensions-new.png "Update Az k8s extensions")
    
1. Now, you can check the status of the Kubernetes cluster by running ```microk8s.status``` in **ubuntu-k8s** VM. To check the status once the command is executed, you have to scroll up to top of the output to view the status. If the status is **microk8s is running**, you can proceed to the next step. But, if it is in a stopped state, you have to run ```microk8s start``` command to restart the Kubernetes cluster.

   - Command to check the status of the Kubernetes cluster
     ```
     microk8s.status
     ```
     
   - Command to start the Kubernetes cluster
     ```
     microk8s start
     ```
     
   >**Note**: In case if you see any error,  Open a new Putty session, re-perform the steps from step-2 to step-4 of the same task 2, run the below command to refresh the certificates and retry the step.

   ```
   microk8s refresh-certs
   ```

   ![](.././media/k8s-status-running.png "check cluster cluster")

1. Next, you will write the config file to $HOME/.kube directory by executing the below command.

     > **Info** : A kubeconfig file is a file used to configure access to Kubernetes when used in conjunction with the kubectl commandline tool (or other clients).

   ```
   cd $HOME
   mkdir .kube
   cd .kube
   microk8s config > config
   cd ..
   ```

   ![](.././media/kube.png "kube") 

1. Now, you will Connect the Kubernetes cluster to Azure Arc by executing the below command. This command will take few minutes to onboard Kubernetes cluster to Azure Arc.

   ```
   az connectedk8s connect --name microk8s-cluster --resource-group $ResourceGroup -l $location
   ```
    
   ![](.././media/connect-k8sv2.png "Connect Kubernetes")
   
   > **Note**: While running the above command, if you face an error stating **Could not retrieve credential from local cache**, run the following command to login to azure portal again.
   ```
   az login -u $AppID --service-principal --tenant $TenantID -p $AppSecret
   ```
   
1. Once the previous command is executed successfully, the **provisioning state** in output will show as succeeded.

   ![](.././media/k8s-connectedv2.png "Kubernetes Cluster Connected")    

## Task 4: Verify if the Kubernetes cluster is connected to Azure Arc

Now let us verify if the Kubernetes cluster is connected to Azure Arc and is in a healthy state.

1. Verify whether the cluster is connected by running the following command:
   
   ```
   az connectedk8s list -g $ResourceGroup -o table
   ```
     
   ![](.././media/check-k8s-connectionv2.png "Varify Micro-k8s cluster is connected")
   
1. Navigate to the Resource Group from the Azure portal navigation pane and click on the Resource Group named **azure-arc**. 

1. Click on Refresh on the azure-arc overview page and then look for the resource named **microk8s-cluster** of resource type **Azure Arc enabled Kubernetes resource**.

   ![](.././media/hol1ss3.png "Varify in Azure")

1. Azure Arc enabled Kubernetes deploys a few operators into the azure-arc namespace. You can view these deployments and pods by running the command in the terminal of the ubunte-k8s VM:

   ```
   kubectl -n azure-arc get deployments,pods
   ```
   
   The output should be similar as shown below:
   
   ![](.././media/get-pods.png)
   
## Task 5: Create a policy assignment to identify compliant/non-compliant resources
Policies can be applied to Arc enabled servers the same way they are applied to Microsoft Azure virtual machines. Policies are applied to ensure that the Azure resources are compliant with established practices such as ensuring that all resources are tagged with an owner. Initiatives can be applied to ensure the server operating systems are compliant such as ensuring the time zone is set correctly on a Microsoft Windows server or a software package is installed on a Linux server. The initiatives use a publish policy to deploy a configuration requirement and an audit policy to check if the requirement has been met. In this task, let's deploy the **Log Analytics Workspace** using policy on ubuntu-k8s machine, which was onboarded earlier to Azure Arc.

1. From the Azure Portal, search for ```Arc``` from the search box and then click on it. 

    ![](.././media/searchAzureArc1v3.png)
    
1. Select **Servers** from the options on the left side of the Azure Arc blade.

    ![](.././media/aa-hol1-servers.png)
    
1. Click on the **ubuntu-k8s** server from connected machines. 

    ![](.././media/hol1-ex1-t5-s3.png)
    
1. From **ubuntu-k8s** server blade, select **Policies** under **Operations** section on the left side.

    ![](.././media/select-policies.png)
    
1. Click on **Assign policy** to assign a policy to the connected **ubuntu-k8s** machine.

    ![](.././media/assign-policies.png)
    
1. In **Assign policy** window, under **Basics** section, select **ellipse(...)**  from **Policy Definitions**.

    ![](.././media/select-ellipsev2.png)
    
1. Search for ```Deploy Log Analytics``` in **Available Definitions** and then click on **Deploy Log Analytics extension for Linux VMs** and then click on **Select** button at the bottom.

    ![](.././media/HOL1-Ex1-T5-P7.png)
    
    >**Note**: If you see multiple policies with the same name, select the Build-in policy when choosing Deploy Log Analytics extension for Linux VMs.
    
1. After selecting the policy definition, update the Assignment name to **Deploy Log Analytics extension for Linux VMs (1)**. Then move to the **Parameters** blade by clicking on the **Next (2)** button at the bottom.

    ![](.././media/basic-nextv2-updated.png)

> **Note:** Make sure to update the Assignment name same as mentioned above in step 8. Different name will result in failure in validation of this task.


1. Under the **Log Analytics Workspace**, select the existing workspace **LogAnalyticsWS-<inject key="DeploymentID/Suffix" />** from the available list and then click on **Next**.

    ![](.././media/select-existing-wsv3.png)

1. On the **Remediation** blade, enable the checkbox for **Create a remediation task** and then click on the **Next** button.

    ![](.././media/hol1ss2.png)
    
1. On **Non-compliance messages** blade, enter following message ```Log Analytics agent is not installed```. This message will be displayed when linux machine will be non compliant. Now, click on the **Review + create**.

    ![](.././media/non-com-message.png)
    
1. On **Review + create** blade, select **Create** to confirm.

    ![](.././media/createv2.png)
    
1. Now, once the policy assignment is created, you will see Deploy Log Analytics Workspace for Linux on the assigned policies list in **Not started** state. It will start to deploy the Log Analytics Agent in **ubuntu-k8s** Hyper-V guest VM. Once Log Analytics Agent is deployed in the ubuntu-k8s VM, compliance state will be updated to **Compliant**. It will take around 20-30 minutes for the process. You can move ahead to the next task and come back later to check the compliance state.

    ![](.././media/ws-compliant.png)    

## Task 6: Monitor Arc Enabled machines with Azure Monitor

Azure Monitor can collect data directly from your hybrid machines into a Log Analytics workspace for detailed analysis and correlation. Typically, this would entail installing the Log Analytics agent on the machine using a script, manually, or automatically following your configuration management standards. Arc enabled servers recently introduced support to install the Log Analytics and Dependency agent VM extensions for Windows and Linux, enabling Azure Monitor to collect data from your non-Azure VMs.

In this task, let's configure and collect data from your Linux machine by enabling Azure Monitor for VMs following a simplified set of steps, which streamlines the experience and takes a shorter amount of time.

1. In the Azure Portal, from **ubuntu-k8s** Server - Azure Arc blade, select **Insights** under **Monitoring** section on the left.

    ![](.././media/insights-option.png)
    
1. Click on the **Enable** on Insights blade. You may have to scroll down to see the **Enable** button.

    ![](.././media/enable-insights.png)

1. On the Azure Monitor **Insights Onboarding** page, choose the existing **Log Analytics Workspace**  LogAnalyticsWS-<inject key="DeploymentID/Suffix" /> and then click on **configure** button.

    ![](.././media/HOL1-EX1-T6-P3.png)

1. Once you click on the **Enable** button, you can see a notification on the bell icon(🔔) in the top right corner: which says **validating deployment** and then changes to **Submitting deployment** and finally **Deployment in progress**. Deployment will take approx 15-20 minutes to deploy the insights for Ubuntu-k8s VM as extensions are being installed on your connected machine (ubuntu-k8s).

    > Note: If you are still seeing Enable button even after clicking on Enable. Once the extensions are installed, it will automatically change. You can move on to the next task.

    ![](.././media/submitting-deployment.png)
    
1. On Azure Arc ubuntu-k8s Insights blade, you will see **Insights deployment is in progress... Please wait.** notification. Once the deployment is completed, you will see a notification on the upper right corner that says **Deployment succeeded**.

    ![](.././media/insights-dep-in-prog.png)

    ![](.././media/deployment-succeeded.png)

1. Once the deployment is succeeded, go back to the **Insights** blade for ubuntu-k8s VM and then refresh the page once, you may have to re-click on the **Enable** button and refresh the page again to see the Insights. Data will take around 10 minutes to be routed to the Insights from your Linux machine: ubuntu-k8s.

    ![](.././media/insight11.png)

    > Note: By this time, the Compliance state of the policy also might have changed. While you wait for the insights to come up, you can check the compliance state in Policies under **Operations** section on the left or you can move on to the next page and come back later to view the insights.

1. Once the Insights are ready, click on the **Performance** blade to review Logical Disk Operations, CPU Utilization, Available Memory, Logical Disk IOPS, Logical Disk MB/s, and much more. It is exciting to see the **graphical representation** on VM performance, whether the VM is deployed on-prem, on other cloud provider platforms, or any edge technologies.

    ![](.././media/HOL1-EX1-T6-P7.png)
    
1. Click on **Map** and review the **ubuntu-k8s** with few running **Processes**. Also, you can explore machine properties from the right. If there will be any **Alerts** you can check it by clicking on **Alerts** on the right side 👉.

    ![](.././media/HOL1-EX1-T6-P8.png)


## In this exercise, you have covered the following:
 
   - Getting started with Hyper-V Infrastructure.
   - Onboarding Linux Machine to Azure Arc.
   - Onboarding Kubernetes Cluster to Azure Arc and verification.
   - Created a policy assignment to identify compliant/non-compliant resources.
   - Monitor Arc Enabled machines with Azure Monitor.
