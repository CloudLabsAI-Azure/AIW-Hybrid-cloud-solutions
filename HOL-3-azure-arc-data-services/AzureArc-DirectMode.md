# Exercise 1: Deploy Azure Arc Data Controller 
  Duration: 30 Minutes
  
 In this exercise you will connect an existing Kubernetes cluster to Azure using Azure Arc-enabled Kubernetes. You will also deploy an Azure data controller in direct connectivity mode to a customer location using Azure portal and Azure CLI. 
 
 
# Connect an existing Kubernetes cluster to Azure using Azure Arc-enabled Kubernetes
 
## Task 1: Login to Azure and install Azure CLI extensions
 
1. Open **Windows PowerShell** from the desktop of your ARCHOST VM and run the below command to login to Azure.
    ```
    az login
    ```
1. After running the above command a browser tab will open to login to azure portal.
 
1. On the **Sign into Microsoft Azure** tab you will see the login screen, in that enter following **Email/Username** and then click on **Next**. 
   * Email/Username: <inject key="AzureAdUserEmail"></inject>

1. Now enter the following **Password** and click on **Sign in**.
   * Password: <inject key="AzureAdUserPassword"></inject>

1. After adding the creds you will see that you have logged into the Microsoft Azure.

    ![](media/login-success.png "Lab Environment")

1. Now switch back to Windows PowerShell and you will be able to see that you have logged in to Azure.

1. Run the below commands to install required Azure CLI extensions.
   
   ```
   az extension add --name k8s-extension
   az extension add --name connectedk8s
   az extension add --name k8s-configuration
   az extension add --name customlocation   
   ```
   
   ![](media/install-extensions.png "Lab Environment")
   
1. You can validate you have all the required extensions with the latest versions by running the below command: 
   
   ```
   az version
   ```   
   
   ![](media/version-check.png "Lab Environment")
   
1. After confirming the required tools are installed, next step is to register your subscription with Arc for Kubernetes.

1. Run the below commands to register the required Resource providers if not already registered. 

   ```
   az provider register --namespace Microsoft.Kubernetes
   az provider register --namespace Microsoft.KubernetesConfiguration
   az provider register --namespace Microsoft.ExtendedLocation
   ```
   
   ![](media/register-provider.png "Lab Environment")
   
## Task 2: Onboard an existing Kubernetes cluster to Azure using Azure Arc-enabled Kubernetes

In this Task you will be connecting an existing Kubernetes cluster to Azure using Azure Arc-enabled Kubernetes and will be enabling custom features by adding an Azure Arc data services extension and a custom location on the Azure Arc-enabled Kubernetes cluster. 

1. Also, you should reword this to: Run the below command to connect the existing Kubernetes to your Azure subscription using Azure Arc-enabled Kubernetes. Once you run the below command, it will take a few minutes to onboard the cluster to Azure Arc. 

   ```
   az connectedk8s connect --name Arc-Data-Demo-DirectMode --resource-group azure-arc
   ```
  
   > **Note:** We have already defined your Cluster name and Azure resource group name in the above commands, If you are trying this in your own subscription, please make sure that you have entered correct details.
 
1. Once the previous command is executed successfully, the provisioning state in output will show as succeeded.

    ![](media/provisionstate.png "Lab Environment") 
 
1. Verify whether the Azure Arc-enabled Kubernetes cluster is onboarded and connected to the resource group in the Azure subscription by running the following command:

   ```
   az connectedk8s list -g azure-arc -o table 
   ``` 
   
   ![](media/list-table.png "Lab Environment")
   
1. Azure Arc-enabled Kubernetes deploys a few operators into the azure-arc namespace. You can view these deployments and pods by running the command in the command prompt:  

   ```
   kubectl -n azure-arc get deployments,pods
   ```
   
   The output should be similar as shown below:
    
   ![](media/deploy-pods.png "Lab Environment")

1. Navigate to the Resource Group from the Azure portal navigation pane and click on the Resource Group named azure-arc. Look for the resource named **Arc-Data-Demo-DirectMode** of resource type Azure Arc-enabled Kubernetes resource.

   ![](media/azurearc-connected.png "Lab Environment")

## Create a custom location on the Azure Arc-enabled Kubernetes cluster

   Custom Locations provides administrators a way to deploy Azure Arc data services and other Azure Arc-enabled services to their own locations, similar to Azure locations. 
 
1. Now run the below command to enable features to create the custom location:

     ```
     az connectedk8s enable-features -n Arc-Data-Demo-DirectMode -g azure-arc --features cluster-connect custom-locations
    
     ```
    The output should be similar as shown below:
    
    **"Successsfully enabled features: ['cluster-connect', 'custom-locations'] for the Connected Cluster Arc-Data-Demo-DirectMode"**
        
      > **Note:** Custom Locations feature is dependent on the Cluster Connect feature. So both features have to be enabled for custom locations to work. Also, az connectedk8s enable-features needs to be run on a machine where the kubeconfig file is pointing to the cluster on which the features are to be enabled.
    
1. Run the below command to deploy the extension of Azure Arc-enabled Data Services on Azure Arc Kubernetes cluster.
  
     ```
    az k8s-extension create --name azdata --extension-type microsoft.arcdataservices --cluster-type connectedClusters -c Arc-Data-Demo-DirectMode -g azure-arc --scope cluster --release-namespace azure-arc --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper
     ```

1. After running the above command you will notice that the **ProvisioningState** is **Succeeded**. If this is pending, because the extension will take a few minutes to complete the installation.

    ![](media/extension-output.png "Lab Environment")

1. To verify the extension installation, switch back to Azure Portal in the browser and search for **Kubernetes - Azure Arc** and select your cluster.

1. Now select **Extension** from left side menu and check if the Install status in **Installed** or not, if not please refresh after some time and then check.

    ![](media/extension-installed.png "Lab Environment")

1. Now run the below command to get the Azure Resource Manager identifier of the Azure Arc-enabled Kubernetes cluster, you will be using the cluster id in later steps while creating the custom location.

    ```  
    $clusterID = az connectedk8s show -n Arc-Data-Demo-DirectMode -g azure-arc  --query id -o tsv
    $clusterID
    ```
     > **Note:** The clusterID is stored in $clusterID parameter and you will be using this parameter only in later steps. 
    
    ![](media/clusterid.png "Lab Environment")
    
1. Now run the below command to get the Azure Resource Manager identifier of the cluster extension deployed on top of Azure Arc-enabled Kubernetes cluster, referenced in later steps as extensionId:

    ```
    $extensionID = az k8s-extension show --name azdata --cluster-type connectedClusters -c Arc-Data-Demo-DirectMode -g azure-arc  --query id -o tsv
    $extensionID
    ```
      > **Note:** The extension resource ID is stored in $extensionID parameter and you will be using this parameter only in later steps.
    
    ![](media/extensionid.png "Lab Environment")
    
1. Now run the below command to create custom location by referencing the Azure Arc-enabled Kubernetes cluster ID and the extension ID.

    ```  
    az customlocation create -n azurearc-customlocation -g azure-arc --namespace azure-arc --host-resource-id $clusterID --cluster-extension-ids $extensionID
    ```
    
    The output should be similar as shown below:
    
     ![](media/custom-location.png "Lab Environment")
     
1. To verify the custom location deployment, switch back to the browser and login to [Azure Portal](https://portal.azure.com) if not already done.

1. Search for custom location in search bar and select custom locations. 

    ![](./media/search-cl.png "Lab Environment")
      
1. After selecting the custom locations from search bar, Select your **azurearc-customlocation**.

    ![](./media/cl-1.png "Lab Environment")
     
1. Explore the overview section. You can see the namespace and Kubernetes cluster details on overview page.

   ![](./media/cl-2.png "Lab Environment")

1. Now search for the log analytics workspace in azure portal.
   
   ![](./media/search-law.png "Lab Environment")

1. Navigate to LoganalyticsWS-Direct workspace. 

   ![](./media/select-law.png "Lab Environment")

1. Select **Agent management** from the left side menu. Copy the value of **Workspace ID** and **Primary key** and save the values in a notepad for later use while creating the Azure arc data controller.

    ![](./media/agents-management.png "Lab Environment")
    

     
  

