# Exercise 2: Enforce GitOps using Azure Policy for Azure Arc Enabled Kubernetes
In this exercise, you will see how to use Azure Policy to enforce that each Microsoft.Kubernetes/connectedclusters resource or Git-Ops enabled Microsoft.ContainerService/managedClusters resource has specific Microsoft.KubernetesConfiguration/sourceControlConfigurations applied on it.

## Task 1: Create a Policy Assignment
In this task you will select an existing policy definition and create a policy assignment. When creating the policy assignment you set the scope for the assignment: this will be the Azure Arc enabled Kubernetes Cluster. You will also set the parameters for the sourceControlConfiguration that will be created. Once the assignment is created the Policy engine will identify all connectedCluster or managedCluster resources that are located within the scope and will apply the sourceControlConfiguration to each one.

1. From the Azure Portal ```https://portal.azure.com```, navigate to the resource group you have access to and click on microk8s-cluster resource. 

    ![](.././media/015.png)

1. From the **Kuberenetes Azure Arc** blade, click on **Go to Policies** under Configure Azure Policy.

    ![](.././media/15.png)

1. Click **Assign policy**.

    ![](.././media/16.png)

1. On the Basics Tab, click on the ellipses (…) to the right of **Policy definition**.

    ![](.././media/17.png)

1. In the **Search** window for available definitions, type ```GitOps``` and select the one called **Deploy GitOps to Kubernetes cluster**.  Click the blue **Select** button below.

    ![](.././media/18.png)

1. Click **Next** at the bottom of the window.

1. Provide the following details under **Parameters** tab and Click **Next**
    - Configuration resource name: **cluster-config**
    - Operator resource name: **cluster-config**
    - Operator namespace: **cluster-config**
    - Operator scope: **cluster**
    - Operator type: **Flux**
    - Operator Parameters: **--git-readonly**
    - Repository URL: The forked repo of **https://github.com/Azure/arc-k8s-demo** that you are using for performing the lab
    - Set the **Enable helm** option to **false**
    - **Leave the other options set to default**
     
    ![](.././media/19.png)
   
1. Select the **Create a remediation task** check box and then click **Review+create** 

    ![](.././media/20.png)
     
1. Click Create.

    ![](.././media/21.png)

1. Navigate to **Azure-Arc RG** -> **microk8s-cluster** -> **Policies**.

1. You can check if your cluster is **compliant** or **not** against **“[Preview]:Deploy GitOps to Kubernetes cluster”** policy you assigned in previous step by looking at the Compliance State Column. It will be **Compliant**. If it is shows **Not started** then you have to run the **remediation task** again by clicking on the policy -> Create Remediation Task -> Remediate.

     ![](.././media/25.png)
     
   > **Note**: The Compliant shows as **non-compliant**, you will need to create a remediation task in the next task and after sometime you will see the complaint state changed to **Compliant**
   
