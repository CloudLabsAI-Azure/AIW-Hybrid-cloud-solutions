# HOL-1: Exercise 1: Getting started with Azure Arc

#### Gain central visibility, operations, and compliance. 

As noted before, Contoso have Windows, Linux, SQL Servers, and Kubernetes clusters across multiple locations, including on-premises datacenters, distribution centers and multiple public clouds. Imagine you are a member of Contoso’s Central IT team. You want to be able to have central visibility of all these resources and manage them in a consistent manner so your business operations run smoothly.

Let's learn how Arc can help Contoso onboard and organize servers and Kubernetes clusters in the Azure Portal, govern them using Azure Policy and enable central monitoring by integrating with Azure Monitor.

## Task 1: Getting started with Hyper-V Infrastructure

Hyper-V is Microsoft's hardware virtualization product. It lets you create and run a software version of a computer, called a virtual machine. Each virtual machine acts like a complete computer, running an operating system and programs. When you need computing resources, virtual machines give you more flexibility, help save time and money, and are a more efficient way to use hardware than just running one operating system on physical hardware. In this task, you will walk through an on-prem environment that is hosted on Hyper-V. You will find three virtual machines hosted on the Hyper-V server, which you will onboard to Azure Arc and play around with.

1. You can see a virtual machine desktop 💻 (LabVM/ARCHOST) is loaded on the left side in your browser. Use this virtual machine throughout the workshop to perform the lab. You can also connect to the virtual machine using RDP using the ARCHOST VM credentials provided in the **Environment Details** tab.

    ![](.././media/cloudlab-vm-guidev1.png "Lab Environment")

1. In the **LabVM/ARCHost VM**, click on the Azure portal shortcut of Microsoft Edge browser which is created on the desktop.
  
    ![](.././media/select-azureportal.png "Select Azure Portal")
    
1. On the **Sign into Microsoft Azure** tab you will see the login screen, in that enter following **Email/Username** and then click on **Next**. 
   * Email/Username: <inject key="AzureAdUserEmail"></inject>
   
1. Now, enter the **Password** which you have already received for the above account.
      
1. If you see the pop-up **Stay Signed in?**, click No

1. If you see the pop-up **You have free Azure Advisor recommendations!**, close the window to continue the lab.

1. If **Welcome to Microsoft Azure** popup window appears, click **Maybe Later** to skip the tour.

1. Navigate to the Resource Group in the Azure portal navigate section.

    ![](.././media/navigate-resource-group.png "Select Resource Group from Navigate Option")    

1. Click on the Resource Group named **azure-arc**. You will be using this resource group through out the lab.

    ![](.././media/azure-arc-rg.png "Select azure-arc Resource Group")

1. Double click on the **Hyper-V Manager** from VM desktop to start the Hyper-V Manager.

    ![](.././media/select-hyper-v.png "Select hyper-v from desktop")

1. Select **ARCHOST** to connect with the Local Hyper-V server. In your machine, there will be a unique suffix added at end of **ARCHOST**, something like **ARCHOST-XXXXXX**.

    ![](.././media/archost-localserver.png "ARCHOST Server")

1. You will find two guest virtual machines running on the Hyper-V manager. Find a list of guest virtual machines with private IP address.
     * **ubuntu-k8s** - ```192.168.0.8```
     * **sqlvm** - ```192.168.0.4```
     
    ![](.././media/guestvms1.png "Guest VMs")
    
## Task 2: Onboard Linux Machine to Azure Arc

Now let's onboard the Linux VMs and local Kubernetes cluster to Azure Arc. So, here we will onboard **ubuntu-k8s** VM to Azure Arc.

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
    
1. There is file `installArcAgentLinux.txt` on ARCHOST VM desktop 💻. Open the file and copy the first 7 lines and paste in putty to declare the values of AppID, AppSecret, TenantID, SubscriptionID, ResourceGroup, and location, and then log into azure using the 7th line. You can also find the values of these variables in the **Environment Details** tab and then use them in the next steps.

    ![](.././media/variableazlogin.png "azlogin")
    
1. Now, download the Azure Arc installation package for Linux, run the below command:

   ```
   wget https://aka.ms/azcmagent -O ~/install_linux_azcmagent.sh
   ```
    
   ![](.././media/download-arc-agent.png "Download Arc Linux Agent")
    
1. Install Azure Arc agent by running :

   ```
   bash ~/install_linux_azcmagent.sh
   ```

   ![](.././media/run-installation.png "Install Arc Agent")
    
1. Once the installation is successful, you will see the following message in terminal **Latest version of azcmagent is installed**.

   ![](.././media/arcagent-installed.png "Arc Agent latest version installed")    
    
1. Finally, connect the ubuntu-k8s machine to Azure Arc. Run following connect command.  Once you run the below command, it will take few minutes to onboard the machine to Azure Arc. 
    
   ```
   azcmagent connect --resource-group $ResourceGroup --tenant-id $TenantID --location $location --subscription-id $SubscriptionId -i $AppID -p $AppSecret
   ```

   > Remember, we are using variables declared earlier in step 5. If you have connected with a new putty session, you may have to run steps 4,5 and 6 again.
     
   ![](.././media/connected-azure-arc.png "Connected to Arc")

1. Let's verify the onboarding of **ubuntu-k8s** server on Azure Arc from Azure portal. Switch to the browser tab where you have logged into Azure portal already in step 1 and browse **azure-arc** resource group to verify whether the **ubuntu-k8s** resource of resource type: **Server - Azure Arc** got created. Click on the resource to get more information.

   ![](.././media/varify-onboard-arc-ubuntuk8s.png "ubuntu k8s onboarded")

1. On **ubuntu-k8s** Server - Azure Arc **Overview** page, verify that the status is **Connected**. Let's also check other details from this tab like Computer name, Operating system, Operating system version and Agent version of ubuntu machine. 

   ![](.././media/ubuntu-k8s-overview-status.png "ubuntu k8s onboard status check")

## Task 3: Onboard Kubernetes Cluster to Azure Arc

We have onboarded the Linux VM to Azure Arc and verified in task 2. Now, you will onboard the local Kubernetes cluster to Azure Arc. So, here we onboard **MicroK8s** Kubernetes cluster to Azure Arc which is hosted on **ubuntu-k8s** VM. We alreaddy have the Microk8s Kubernetes cluster ready and configured, and also Arc enabled CLI extensions are installed.

   > **Note** : If you have closed the putty after completing **task 2**, then perform the first 6 steps of task 2 again and then return to perform this task. Make sure that you perform all steps with root user in ubuntu-k8s vm.

1. Install helm using following commands:

   ```
   curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3
   chmod 700 get_helm.sh
   ./get_helm.sh
   ```
    
   ![](.././media/installhelm.png "installhelm")

1. Update the Arc enabled Kubernetes CLI extensions, so that we use the lastest k8s extensions for the lab.

   ```
   az extension update --name connectedk8s
   ```

   ```
   az extension update --name k8sconfiguration
   ```
    
   ![](.././media/update-k8s-extensions.png "Update Az k8s extensions")
    
1. Now, check the status of the Kubernetes cluster by running ```microk8s.status``` as demouser in **ubuntu-k8s** VM. You can proceed further if it is running. If it is in a stopped state, you may have to run ```microk8s start``` command to run the Kubernetes cluster.

   - Command to check the status of the Kubernetes cluster
     ```
     microk8s.status
     ```

   - Command to start the Kubernetes cluster
     ```
     microk8s start
     ```

   ![](.././media/k8s-status-running.png "check cluster cluster")

1. now update kube config file.

   ```
   cd $HOME
   mkdir .kube
   cd .kube
   microk8s config > config
   cd ..
   ```

   ![](.././media/kube.png "kube") 

1. Connect the Kubernetes cluster to Azure Arc by executing the following command. This command will take few minutes to onboard Kubernetes cluster to Azure Arc.

   ```
   az connectedk8s connect --name microk8s-cluster --resource-group $ResourceGroup -l $location
   ```
    
   ![](.././media/connect-k8sv2.png "Connect Kubernetes")
    
1. Once the previous command is executed successfully, the **provisioning state** in output will show as succeeded.

   ![](.././media/k8s-connectedv2.png "Kubernetes Cluster Connected")    

## Task 4: Verify if the Kubernetes cluster is connected to Azure Arc

Now let us verify if the Kubernetes cluster is connected to Azure Arc and is in a healthy state.

1. Verify whether the cluster is connected by running the following command:
   
   ```
   az connectedk8s list -g $ResourceGroup -o table
   ```
     
   ![](.././media/check-k8s-connectionv2.png "Varify Micro-k8s cluster is connected")
   
1. Navigate to the Resource Group from the Azure portal navigation pane and click on the Resource Group named **azure-arc**. Look for the resource named **microk8s-cluster** of resource type **Azure Arc enabled Kubernetes resource**.

   ![](.././media/varify-in-azure1.png "Varify in Azure")

1. Azure Arc enabled Kubernetes deploys a few operators into the azure-arc namespace. You can view these deployments and pods by running the command in the command prompt:

   ```
   kubectl -n azure-arc get deployments,pods
   ```
   
   The output should be similar as shown below:
   
   ![](.././media/get-pods.png)
   
## Task 5: Create a policy assignment to identify compliant/non-compliant resources
Policies can be applied to Arc enabled servers the same way they are applied to Microsoft Azure virtual machines. Policies are applied to ensure that the Azure resources are compliant with established practices such as ensuring that all resources are tagged with an owner. Initiatives can be applied to ensure the server operating systems are compliant such as ensuring the time zone is set correctly on a Microsoft Windows server or a software package is installed on a Linux server. The initiatives use a publish policy to deploy a configuration requirement and an audit policy to check if the requirement has been met. In this task, let's deploy the **Log Analytics Workspace** using policy on ubuntu-k8s machine, which was onboarded earlier to Azure Arc.

1. From the Azure Portal, search for ```Azure Arc``` from the search box and then click on it. 

    ![](.././media/searchAzureArc1v2.png)
    
1. Select **Servers** from the options on the Azure Arc blade.

    ![](.././media/select-servers.png)
    
1. Explore the **ubuntu-k8s** server from connected machines. 

    ![](.././media/ubuntu-k8s-server1.png)
    
1. From **ubuntu-k8s** server blade, select **Policies** under **Operations** section.

    ![](.././media/select-policies.png)
    
1. Click on **Assign policy** to assign a policy to the connected **ubuntu-k8s** machine.

    ![](.././media/assign-policies.png)
    
1. In **Assign policy** window **Basics** blade, select **ellipse(...)**  from **Policy Definitions**.

    ![](.././media/select-ellipsev2.png)
    
1. Search for ```Deploy Log Analytics``` in **Available Definitions** and then select **Deploy Log Analytics agent for Linux VMs**.

    ![](.././media/deployloganalytics.png)
    
1. After selecting the policy definition, move to the **Parameters** blade by clicking on the **Next** button at the bottom.

    ![](.././media/basic-nextv2.png)
    
1. Under the **Log Analytics Workspace**, select the existing workspace with prefix **LogAnalyticsWS-** from the available list and then click on **Next**.

    ![](.././media/select-existing-wsv2.png)

1. On the **Remediation** blade, enable the checkbox for **Create a remediation task** and then click on the **Next** button.

    ![](.././media/remediation-next.png)
    
1. On **Non-compliance messages** blade, enter following message ```Log Analytics agent is not installed```. This message will be displayed when linux machine will be non compliant. Now, click on teh **Review + create**.

    ![](.././media/non-com-message.png)
    
1. On **Review + create** blade, select **Create**.

    ![](.././media/createv2.png)
    
1. Now, once the policy assignment is created, you will see Deploy Log Analytics Workspace for Linux on the assigned policies list in Compliant state. It will deploy the Log Analytics Workspace in **ubuntu-k8s** Hyper-V guest VM. Once Log Analytics Workspace is deployed in the ubuntu-k8s VM, compliance state will be updated to **Compliant**. It will take 20-30 minutes.

    ![](.././media/ws-compliant.png)    

## Task 6: Monitor Arc Enabled machines with Azure Monitor

Azure Monitor can collect data directly from your hybrid machines into a Log Analytics workspace for detailed analysis and correlation. Typically, this would entail installing the Log Analytics agent on the machine using a script, manually, or automatically following your configuration management standards. Arc enabled servers recently introduced support to install the Log Analytics and Dependency agent VM extensions for Windows and Linux, enabling Azure Monitor to collect data from your non-Azure VMs.

In this task, let's configure and collect data from your Linux machine by enabling Azure Monitor for VMs following a simplified set of steps, which streamlines the experience and takes a shorter amount of time.

1. From the Azure Portal, search for ```Azure Arc``` from the search box and then click on it. 

    ![](.././media/searchAzureArc1v2.png)
    
1. Select **Servers** from the options on the Azure Arc blade.

    ![](.././media/select-servers.png)
    
1. Explore the **ubuntu-k8s** server from connected machines. 

    ![](.././media/ubuntu-k8s-server1.png)

1. From **ubuntu-k8s** server blade, select **Insights** under **Monitoring** section.

    ![](.././media/insights-option.png)
    
1. Click on the **Enable** on Insights blade. You may have to scroll down to see teh **Enable** button.

    ![](.././media/enable-insights.png)

1. On the Azure Monitor **Insights Onboarding** page, choose the existing **Log Analytics Workspace** named like ```LogAnalyticsWS-{Suffix}``` and then click on **Enable**.

    ![](.././media/enable-insights2.png)

1. Once you click on the **Enable** button, you can see a notification on the bell icon(🔔) in the top right corner: which says **validating deployment** and then changes to **Submitting deployment** and finally **Deployment in progress**. Deployment will take approx 15-20 minutes to deploy the insights for Ubuntu-k8s VM as extensions are being installed on your connected machine (ubuntu-k8s).

    ![](.././media/submitting-deployment.png)
    
1. On Azure Arc ubuntu-k8s Insights blade, you will see **Insights deployment in progress... Please wait.**. Once the deployment is completed, you will see a notification on the upper right corner that says **Deployment succeeded**.

    ![](.././media/insights-dep-in-prog.png)

    ![](.././media/deployment-succeeded.png)

1. Once the deployment is succeeded, go back to the **Insights** blade for ubuntu-k8s VM and then refresh the page once, you may have to re-click on the **Enable** button and refresh the page again to see the Insights. Data will take around 10 minutes to be routed to the Insights from your Linux machine: ubuntu-k8s.

    ![](.././media/insight11.png)

1. Once the Insights are ready, click on the **Performance** blade to review Logical Disk Operations, CPU Utilization, Available Memory, Logical Disk IOPS, Logical Disk MB/s, and much more. It is exciting to see the **graphical representation** on VM performance, whether the VM is deployed on-prem, on other cloud provider platforms, or any edge technologies.

    ![](.././media/performance-matrix.png)
    
1. Click on **Map** and review the **ubuntu-k8s** with few running **Processes**. Also, you can explore machine properties from the right. If there will be any **Alerts** you can check it by clicking on **Alerts** on the right side 👉.

    ![](.././media/map.png)


## In this exercise, you have covered the following:
 
   - Getting started with Hyper-V Infrastructure.
   - Onboarding Linux Machine to Azure Arc.
   - Onboarding Kubernetes Cluster to Azure Arc and verification.
   - Created a policy assignment to identify compliant/non-compliant resources.
   - Monitor Arc Enabled machines with Azure Monitor.
