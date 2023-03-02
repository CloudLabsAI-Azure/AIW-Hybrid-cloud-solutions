HOL-4: Exercise 1: Integrate Azure Stack HCI 22H2 with Azure
==============
Overview
-----------

   As part of the lab environment, we have already deployed the Azure Stack HCI 22H2 Cluster, so you don't have to deploy it and you can continue adding it to Windows Admin Center and verify the registration of the cluster within Azure to unlock the full functionality.

   Azure Stack HCI 22H2 is delivered as an Azure service and needs to register within 30 days of installation per the Azure Online Services Terms.  With our cluster configured, we'll now register your Azure Stack HCI 22H2 cluster with **Azure Arc** for monitoring, support, billing, and hybrid services. Upon registration, an Azure Resource Manager resource is created to represent each on-premises Azure Stack HCI 22H2 cluster, effectively extending the Azure management plane to Azure Stack HCI 22H2. Information is periodically synced between the Azure resource and the on-premises cluster.  One great aspect of Azure Stack HCI 22H2, is that the Azure Arc registration is a native capability of Azure Stack HCI 22H2, so there is no agent required.

Contents
-----------
- [Overview](#overview)
- [Contents](#contents)
- [Add the existing Cluster to Windows Admin Center](#task-1-add-the-existing-cluster-to-windows-admin-center)
- [Verify Cluster Registration](#task-2-verify-your-azure-stack-hci-22h2-cluster-is-registered-with-azure)

## Task 1: Add the existing Cluster to Windows Admin Center

This Lab also includes a dedicated Windows Admin Center (WAC) gateway server. The WAC gateway server can be accessed by connecting to it from RDP. A shortcut is available on the HCIBox-Client desktop.

1. Open this shortcut and use the domain credential (arcedemo@jumpstart.local) to start an RDP session to the Windows Admin Center VM.

![-](./media/wac_gateway_shortcut.png "Screenshot showing WAC desktop shortcut")

2. Log in to the WAC gateway server using the domain credential.

![-](./media/wac_gateway_login.png "Screenshot showing logging into the WAC server")

3. Now you can open the Windows Admin Center shortcut on the WAC server desktop. Once again you will use your domain account to access WAC.

![-](./media/wac_gateway_desktop.png "Screenshot showing logging into WAC")
![-](./media/wac_login.png "Screenshot showing WAC login prompt")

4. Now that we are logged in, our first step is to add a connection to our HCI cluster. Click on the “Add” button, and then click on “Add” under “Server clusters”.

![-](./media/wac_empty.png "Screenshot showing WAC first login")
![-](./media/wac_add_cluster.png "Screenshot showing WAC adding cluster")

5. Enter “hciboxcluster” for the cluster name, and use the domain account credential to connect to the cluster.

![-](./media/wac_add_cluster_detail.png "Screenshot showing WAC connection details")
![-](./media/wac_add_cluster_detail_2.png "Screenshot showing WAC connection details")

6. Now that the cluster is added, we can explore management capabilities for the cluster inside of WAC. Click on the cluster to drill into its details.

![-](./media/wac_cluster_added.png "Screenshot showing cluster list")
![-](./media/wac_cluster_added_detail.png "Screenshot showing cluster detail")

You just successfully added the existing Azure Stack HCI 22H2 Cluster "hciboxcluster" to you Windows Admin Center. We will explore the Cluster capabilities leveraging WAC in upcoming tasks.

## Task 2: Verify your Azure Stack HCI 22H2 Cluster is registered with Azure

**Within this lab the Azure Stack HCI 22H2 cluster is already registered within Azure.**

To verify the registration let's now use **PowerShell**.

We're going to perform the registration verification from the **AdminCenter** machine, which we've been using to access Windows Admin Center.

1. After logging in to the **AdminCenter** VM, click on the windows button and search for **PowerShell ISE** then right-click on it to select **Run as administrator**.

![-](./media/2023-03-01_17h07_52.png "Open PowerShell ISE as an Administrator")
    
2. Copy and paste the below PowerShell commands and execute them, you will now see 

     ```powershell
     Invoke-Command -ComputerName AzSHOST1 -ScriptBlock {
     Get-AzureStackHCI
     } 
     ```
    
    ![Check updated registration status with PowerShell](./media/Verify-registered-cluster.png "Check updated registration status with PowerShell")

You can see the **RegistrationStatus**, the **ConnectionStatus** and **LastConnected** time, which is usually within the last day unless the cluster is temporarily disconnected from the Internet. An Azure Stack HCI 22H2 cluster can operate fully offline for up to 30 consecutive days.

Summary
-----------
In this exercise, you've successfully added the existing Azure Stack HCI cluster to your Windows Admin Center and checked the registerion status of the Cluster via PowerShell.. With this completed, you can now move on to the next exercise.

Product improvements
-----------
If, while you work through this guide, you have an idea to make the product better, whether it's something in Azure Stack HCI, AKS on Azure Stack HCI, Windows Admin Center, or the Azure Arc integration and experience, let us know! We want to hear from you!

For **Azure Stack HCI**, [Head on over to the Azure Stack HCI Q&A forum](https://learn.microsoft.com/en-us/answers/tags/6/azure-stack-hci "Azure Stack HCI Q&A"), where you can share your thoughts and ideas about making the technologies better and raise an issue if you're having trouble with the technology.

For **AKS on Azure Stack HCI**, [Head on over to our AKS on Azure Stack HCI GitHub page](https://github.com/Azure/aks-hci/issues "AKS on Azure Stack HCI GitHub"), where you can share your thoughts and ideas about making the technologies better. If however, you have an issue that you'd like some help with, read on... 
