HOL-4: Exercise 2: Deploy your AKS-HCI infrastructure
==============
Overview
-----------
It's now time to deploy AKS on Azure Stack HCI. You'll first deploy the AKS on Azure Stack HCI management cluster, and then deploy a target cluster, onto which you can test deployment of a workload.

Contents
-----------
- [Overview](#overview)
- [Contents](#contents)
- [Architecture](#architecture)
- [Next Steps](#next-steps)

Architecture
-----------

From an architecture perspective, as shown earlier, this graphic showcases the different layers and interconnections between the different components:

![Architecture diagram for AKS on Azure Stack HCI in Azure](/media/nested_virt_akshci_ga.png "Architecture diagram for AKS on Azure Stack HCI in Azure")

In this section, you'll use Windows Admin Center to deploy the AKS on Azure Stack HCI, including all the key components. First, the management cluster (kubernetes virtual appliance) provides the the core orchestration mechanism and interface for deploying and managing one or more target clusters. These target, or workload clusters contain worker nodes and are where application workloads run. If you're interested in learning more about the building blocks of the Kubernetes infrastructure, you can [read more here](https://docs.microsoft.com/en-us/azure-stack/aks-hci/kubernetes-concepts "Kubernetes core concepts for Azure Kubernetes Service on Azure Stack HCI").

As mentioned earlier, Azure Stack HCI and AKS-HCI will de deployed as 2 separate environments within the same Azure VM. In a production environment, you would run AKS-HCI **on top of** Azure Stack HCI, but in this nested environment, the performance of the multiple levels of nesting can have a negative impact, so in this case, they will be deployed side by side for evaluation.


## Task 1: Configure Windows Admin Center 
-----------
Your Azure VM deployment automatically installed Windows Admin Center 2103, however there are some additional configuration steps that must be performed before you can use it to deploy AKS on Azure Stack HCI.

1. In the virtual machine (VM) on the left, double-click on the **Windows Admin Center** .

   ![Allow popups in Edge](./media/wac.png "Allow popups in Edge")
    
  > **Note**: If you get a pop up saying your connection is not private, Click on **advanced settings** and select **Continue to hybridhost001(unsafe)** to open the Windows Admin Center.
   
   ![Admin center popups in Edge](./media/WAC-popup.png "Allow popups in Edge")
   
   > **Note**: Refresh the page few times if **Windows Admin Center** takes too long to open.
   
2. Once Windows Admin Center is open, you may receive notifications in the top-right corner, indicating that some extensions are updating automatically. **Let these finish updating before proceeding**. Windows Admin Center may refresh automatically during this process. If you didn't receive any notifications you can continue with the next step.

3. Once complete, click on the **Settings**(1) icon from top right corner, then select **Extensions**(2)

   ![Allow popups in Edge](./media/extension.png "Allow popups in Edge")
    
4. Click on **Installed extensions** and you will see **Azure Kubernetes Service** is installed.

   ![Installed extensions in Windows Admin Center](./media/installed.png "Installed extensions in Windows Admin Center")
    
5. Please note, if there are any updates available for the Azure Kubernetes Service, select **Azure Kubernetes Service** and click on **Update** to install the **latest** version.

   ![](./media/extensionv.png)

In order to deploy AKS-HCI with Windows Admin Center, you need to connect your Windows Admin Center instance to Azure.

6. Now, in **Settings**, under **Gateway** click on **Register**.

   ![Installed extensions in Windows Admin Center](./media/hol4-settings-register.png "Installed extensions in Windows Admin Center")
    
7. Click **Register**(1), and in the **Get started with Azure in Windows Admin Center** blade, follow the instructions to **Copy the code**(2) and then click on the link **Enter the Code**(3) to configure device login.

   ![Installed extensions in Windows Admin Center](./media/login.png "Installed extensions in Windows Admin Center")
    
8. Now, Paste the code you copied in previous step and click on **Next** button.

   ![Installed extensions in Windows Admin Center](./media/code.png "Installed extensions in Windows Admin Center")
     
9. When prompted for credentials, **enter your Azure credentials** for a tenant you'd like to use to register the Windows Admin Center and click on **Continue** button if you get any popup saying **Are you trying to sign in to Windows Admin Center?**.

10. Now, navigate back in **Windows Admin Center** tab, you'll notice your tenant information has been added.  You can click on **Connect** to connect Windows Admin Center to Azure.

    ![Connecting Windows Admin Center to Azure](./media/connect.png "Connecting Windows Admin Center to Azure")

11. Click on **Sign in** and when prompted for credentials, **enter your Azure credentials** and you will see a popup **Permissions requested**. Check the box next to the **Consent on behalf of your organization** then click **Accept**.

    ![Permissions for Windows Admin Center](./media/ex2-task1-step10.png)

*******************************************************************************************************

**NOTE** - If you receive an error when signing in, still in **Settings**, under **User**, click on **Account** and click **Sign-in**. You should then be prompted for Azure credentials and permissions, to which you can then click **Accept**. Sometimes it just takes a few moments from Windows Admin Center creating the Azure AD application and being able to sign in. Retry the sign-in until you've successfully signed in.
**NOTE** - Sometime even after cluster is regitered ity may show an error with Signin with following error, you can ignore that and close the popup:
   ```AADSTS700016: Application with identifier '******************' was not found in the directory 'Azure HOL ****'. This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant. You may have sent your authentication request to the wrong tenant.```

*******************************************************************************************************

## Task 2: Validate Azure integration
-----------
In order to successfully deploy AKS on Azure Stack HCI with Windows Admin Center, additional permissions were applied on the Windows Admin Center Azure AD application that was created when you connected Windows Admin Center to Azure, earlier. In this step, we'll quickly validate those permissions.

1. Still in Windows Admin Center, click on the **Settings** gear in the top-right corner
2. Under **Gateway**, click **Register**. You should see your previously registered Azure AD app:

    ![Your Azure AD app in Windows Admin Center](./media/hol4-settings-register1.png "Your Azure AD app in Windows Admin Center")

3. Click on **View in Azure** to be taken to the Azure AD app portal, where you should see information about this app, including permissions required. If you're prompted to log in, provide appropriate credentials.
4. Once logged in, under **Configured permissions**, you should see a few permissions listed with the status **Granted for...** and the name of your tenant. The **Microsoft Graph (5)** API permissions will show as **not granted** but this will be updated upon deployment

5. Under **Configured permissions**, click on **Grant Admin Consent for Azure HOL** button. select **Yes** on the **Grant admin consent confirmation** pop-up to give your app the permissions.

    ![Confirm Azure AD app permissions in Windows Admin Center](../media/Ex2-task2-01.1.png "Confirm Azure AD app permissions in Windows Admin Center")
    
6.  Now select **Yes** on the **Grant admin consent confirmation** pop-up to give your app the permissions.

    ![Confirm Azure AD app permissions in Windows Admin Center](../media/Ex2-task2-01.2.png "Confirm Azure AD app permissions in Windows Admin Center")

*******************************************************************************************************

**NOTE** - If you don't see Microsoft Graph listed in the API permissions, you can either [re-register Windows Admin Center using steps here](#configure-windows-admin-center "re-register Windows Admin Center using steps here") for the permissions to appear correctly, or manually add the **Microsoft Graph Appliation.ReadWrite.All** permission. To manually add the permission:

- Click **+ Add a permission**
- Select **Microsoft Graph**, then **Delegated permissions**
- Search for **Application.ReadWrite.All**, then if required, expand the **Application** dropdown
- Select the **checkbox** and click **Add permissions**

*******************************************************************************************************

6. Switch back to the **Windows Admin Center tab** and click on **Windows Admin Center** in the top-left corner to return to the home page. 

    ![Confirm Azure AD app permissions in Windows Admin Center](./media/admin.png "Confirm Azure AD app permissions in Windows Admin Center")
    
    
   You'll notice that your HybridHost001 is already under management, so at this stage, you're ready to proceed to deploy the AKS on Azure Stack HCI management cluster onto your Windows Server 2019 Hyper-V host.

    ![HybridHost001 under management in Windows Admin Center](./media/akshcihost_in_wac.png "HybridHost001 under management in Windows Admin Center")

## Task 3: Deploying the AKS on Azure Stack HCI management cluster
-----------
The next section will walk through configuring the AKS on Azure Stack HCI management cluster, on your single node Windows Server 2019 host.

1. From the Windows Admin Center homepage, click on your **HybridHost001.hybrid.local** cluster. 
 
    ![HybridHost001 under management in Windows Admin Center](./media/cluster.png "HybridHost001 under management in Windows Admin Center")

1. On the **HybridHost001** VM, click on the windows button and look for PowerShell ISE and right-click on it to Run as administrator.

   ![](../media/powershell-1.png)
   
1. Now, copy the below code and paste it in your PowerShell window to install and update the PowerShellGet module.

   ```
   Install-Module –Name PowerShellGet –Force
   Update-Module –Name PowerShellGet –Force
   ```
1. Close the PowerShell session and re-open it with "Run as administrator" to use the **PowerShellGet** module to configure the AKS in the next steps.

1. Next, copy the below code and paste it in your PowerShell window, replace **your-subscription-ID-here** with your **subscription ID**: <inject key="Subscription ID" /> . After updating the subscription ID, run the PowerShell commands to configure the AKS on Azure Stack HCI management cluster. Click on **Yes to all** to install the modules from PSGallery then select **Yes to all** again to accept the license terms if it popups for confirmation.

   ```
   Install-Module -Name AksHci -Repository PSGallery
   Get-Command -Module AksHci
   Initialize-AksHciNode
   Get-VMSwitch
   $vnet = New-AksHciNetworkSetting -name aks-default-network -vswitchName InternalNAT -vipPoolStart 192.168.0.150 -vipPoolEnd 192.168.0.250
   Set-AksHciConfig -imageDir v:\clusterstorage\volume1\Images -workingDir v:\ClusterStorage\Volume1\ImageStore -cloudConfigLocation v:\clusterstorage\volume1\Config -vnet        $vnet -cloudservicecidr "192.168.0.150/16"
   Set-AksHciRegistration -subscriptionId "your-subscription-ID-here" -resourceGroupName "hybridhost"
   Install-AksHci
   ```

1. Once dependencies have been installed, you'll receive a popup on **HybridHost001** to authenticate to Azure. Provide your **Azure credentials**.

   ![](../media/azure_login_reg.png)

1. Once successfully authenticated, the configuration process will begin and will take around 10-15 minutes to finish. if you see below warning please ignore that and proceed with next step.

      > WARNING: If you are running Windows PowerShell remotely, note that some failover clustering cmdlets do not work remotely. When possible, run the cmdlet locally and specify a remote computer a
s the target. To run the cmdlet remotely, try using the Credential Security Service Provider (CredSSP). All additional errors or warnings from this cmdlet might be caused by running it remote
ly.

1. Navigate back to Windows Admin Center homepage and click on your **HybridHost001.hybrid.local** cluster.

1. You'll be presented with a rich array of information about your HybridHost001 cluster, of which you can feel free to explore the different options and metrics. When you're ready, on the left-hand side, select **Azure Kubernetes Service** and review the cluster details such as Connection status, AKS host name, total disk space and memory.

   ![](../media/AksHci.png)
  
     
### Updates and Cleanup ###
To learn more about **updating**, **redeploying** or **uninstalling** AKS on Azure Stack HCI with Windows Admin Center, you can [read the official documentation here.](https://docs.microsoft.com/en-us/azure-stack/aks-hci/setup "Official documentation on updating, redeploying and uninstalling AKS on Azure Stack HCI")

Task 4: Create a Kubernetes cluster (Target cluster)
-----------
With the management cluster deployed successfully, you're ready to move on to deploying Kubernetes clusters that can host your workloads. We'll then briefly walk through how to scale your Kubernetes cluster and upgrade the Kubernetes version of your cluster.

1. From the same page click on **Add Cluster** under **Kubernetes clusters** section.
    
     ![AKS on Azure Stack HCI management cluster deployment started in Windows Admin Center](./media/addcluster.png "AKS on Azure Stack HCI management cluster deployment started in Windows Admin Center")
     
3. Firstly, review the prerequisites - your Azure VM environment will meet all the prerequisites, so you should be fine to click **Next: Basics**

    ![AKS on Azure Stack HCI management cluster deployment started in Windows Admin Center](./media/basic.png "AKS on Azure Stack HCI management cluster deployment started in Windows Admin Center")

5. On the **Basics** page, firstly, choose whether you wish to **optionally** integrate with Azure Arc enabled Kubernetes. You can click the link on the page to learn more about Azure Arc. If you do wish to integrate, select the **Enabled** radio button, then use the drop downs to select the **subscription**, select  **HybridHost** resource group and  same region where your RG is deployed.

    ![Enable Arc integration with Windows Admin Center](/media/basic1.png "Enable Arc integration with Windows Admin Center")

3. Still on the **Basics** page, under **Cluster details** enter the following details,
     
     *  **Password**: ```demo!pass123```
     *  **Kubernetes cluster name**: ```akshciclus001``` 
     *  **Kubernetes version**: ```v1.21.2```

    ![AKS cluster details in Windows Admin Center](https://raw.githubusercontent.com/CloudLabsAI-Azure/hybridworkshop/main/media/aks_basics_cluster_details_single1.png "AKS cluster details in Windows Admin Center")

4. Under **Primary node pool**, accept the defaults, and then click **Next: Node pools**

    ![AKS primary node pool in Windows Admin Center](./media/basic3.png "AKS primary node pool in Windows Admin Center")

5. On the **Node pools** page, click on **+Add node pool**
6. In the **Add a node pool** blade, enter the following, then click **Add**
   1. **Node pool name**: linuxpool1
   2. **OS type**: Linux
   3. **Node size**: Standard_K8S3_v1 (6 GB Memory, 4 CPU)
   4. **Node count**: 1
   5. **Max pods per node**: leave default

   ![AKS node pools in Windows Admin Center](./media/hybridhost-add-node-pool.png "AKS node pools in Windows Admin Center")

7. Once your **Node pools** have been defined, click **Next: Authentication**
8. For this evaluation, for **AD Authentication** click **Disabled** and then click **Next: Networking**

    ![AKS virtual networking in Windows Admin Center](./media/ad.png "AKS virtual networking in Windows Admin Center")

10. On the **Networking** page, review the **defaults**. For this deployment, you'll deploy this kubernetes cluster on the existing virtual network that was created when you installed AKS-HCI in the previous steps.

    ![AKS virtual networking in Windows Admin Center](./media/aks_virtual_networking.png "AKS virtual networking in Windows Admin Center")

10. Click on the **aks-default-network**, ensure **Flannel** network configuration is selected, and then click **Next: Review + Create**

     ![AKS virtual networking in Windows Admin Center](./media/network.png "AKS virtual networking in Windows Admin Center")

12. On the **Review + Create** page, review your chosen settings, then click **Create**

     ![Finalize creation of AKS cluster in Windows Admin Center](./media/cresate.png "Finalize creation of AKS cluster in Windows Admin Center")

12. The creation process will begin and can take upto 15 minutes to deploy.
    > **Note**: Make sure to not switch to another tab otherwise the creation can be failed due to inactivity.
    
     ![Start deployment of AKS cluster in Windows Admin Center](./media/loadaks-cluster.png "Start deployment of AKS cluster in Windows Admin Center")

13. Once completed, you should see a message for successful creation, then click **Finish**

     ![Completed deployment of AKS cluster in Windows Admin Center](./media/ex2-task4-step14.png)

14. Back in the **Azure Kubernetes Service on Azure Stack HCI landing page**, you should now see your cluster listed.

     ![AKS cluster in Windows Admin Center](./media/Aks-display1.png "AKS cluster in Windows Admin Center")

16. On the dashboard, if you chose to integrate with Azure Arc, you should be able to click the **Azure instance** link to be taken to the Azure Arc view in the Azure portal.

     ![AKS cluster in Azure Arc](./media/Aks-display2.png "AKS cluster in Azure Arc")

Task 5: Scale your Kubernetes cluster (Target cluster)
-----------
Next, you'll scale your Kubernetes cluster to add an additional Linux worker node. As it stands, this has to be performed with **PowerShell** but will be available in Windows Admin Center in the future.

1. Open **PowerShell as Administrator** if not already opened and run the following command to import the new modules, and list their functions.

     ```powershell
     Import-Module AksHci
     Get-Command -Module AksHci
     ```

2. Next, to check on the status of the existing clusters, run the following command, you will see node count 1. 

     ```powershell
     Get-AksHciNodePool -clusterName akshciclus001
     ```

     ![](./media/Azure-stack-PS01.png)

3. Next, you'll scale your Kubernetes cluster node pool using below command, it will scale the node count to 2. 

   > **Note**: This can take upto 10 minutes to scale up the cluster

    ```powershell
    Set-AksHciNodePool -clusterName akshciclus001 -name linuxpool1 -count 2
    ```
    
    ![Output of Set-AksHciNodePool](./media/Azure-stack-PS02.png)

4. Once these steps have been completed, you can verify the details by running the following command:

     ```powershell
     Get-AksHciNodePool -clusterName akshciclus001
      ```

    ![Output of Get-AksHciCluster](./media/Azure-stack-PS03.png)

To access this **akshciclus001** cluster using **kubectl** (which was installed on your host as part of the overall installation process), you'll first need the **kubeconfig file**.

5. To retrieve the kubeconfig file for the akshciclus001 cluster, you'll need to run the following command from your **administrative PowerShell**:

    ```powershell
    Get-AksHciCredential -Name akshciclus001
     dir $env:USERPROFILE\.kube  
    ```

Next Steps
-----------
In this step, you've successfully deployed the AKS on Azure Stack HCI management cluster using Windows Admin Center, optionally integrated with Azure Arc, and subsequently, deployed and scaled a Kubernetes cluster that you can move forward with to the next stage, in which you can deploy your applications.


