# Exercise 4: Setup GitOps workflow on connected Cluster

In this exercise, you will deploy a sample kubernetes app using az k8sconfiguration command and gitops and also update the configuration in the repository which you have linked to connected cluster and verify if cluster is getting updated based on the changes made.

## Task 1: Create a Configuration

1. Fork the repository https://github.com/Azure/arc-k8s-demo to your personal github account. As you will be making changes to the files in the repository.

## Task 2: Deploy App using az k8sconfiguration

1. Using the Azure CLI extension for k8sconfiguration, link connected cluster to personal git repository. Provide this configuration a name cluster-config, instruct the agent to deploy the operator in the cluster-config namespace, and give the operator cluster-admin permissions. 

2. Run the following command:
   - Replace the XXXXXX with the deploymentID provided in the environment details page
   - Replace **your personal github account name** with your personal github account that you are using to perform the lab.

   ```
   az k8sconfiguration create --name cluster-config --cluster-name AzureArcAKSCluster1 --resource-group Azure-Arc-XXXXXX --operator-instance-name cluster-config --operator-namespace cluster-config --repository-url https://github.com/<your personal github account name>/arc-k8s-demo --scope cluster --cluster-type connectedClusters
   ```
   
   The output should be as shown:

   ![](./images/arc-0023.png) 
   
     > Note: Wait for 5 mins before performing the next step

## Task 3: Validate the sourceControlConfiguration

1. Validate whether the sourceControlConfiguration was successfully created and the compliance state is Installed. If it is pending, retry the same command again after sometime.

   ```
   az k8sconfiguration show --resource-group Azure-Arc-XXXXXX --name cluster-config --cluster-name AzureArcAKSCluster1 --cluster-type connectedClusters
   ```
     > Note: that the sourceControlConfiguration resource is updated with compliance status, messages, and debugging information in the output.

   The output should be as shown:

   ![](./images/arc-0024.png) 
  
2. Navigate to **Azure-Arc RG->AzureArcAKSCluster1->Configurations**. Ensure that the operator state status should show as **Installed**.

   ![](./images/azure-arc-10.png) 
  
## Task 4:  Validate the Kubernetes configuration

1. After config-agent has installed the flux instance, resources held in the git repository should begin to flow to the cluster. Check to see that the namespaces, deployments, and resources are created by **Running the following command:**

   ```
   kubectl get ns --show-labels
   ```
 
   The output shows that team-a, team-b, itops, and cluster-config namespaces have been created as shown:
  
   ![](./images/azure-arc-11.png) 
   
2. The flux operator will be deployed to cluster-config namespace, as directed by our sourceControlConfig:
      
    ```
    kubectl -n cluster-config get deploy  -o wide
    ```
   
    The output should be as shown:
   
    ![](./images/azure-arc-12.png) 
  
3. You can explore the other resources deployed as part of the configuration repository by running the following commands:

   ```
   kubectl -n team-a get cm -o yaml
   ```
   
   ```
   kubectl -n itops get all
   ```
## Task 5: Make changes to cluster declarations in the Git repo.

1.  Run the following command in Powershell window and confirm that the Age is the same for both azure-vote-back and azure-vote-front apps. It will be same since the deployment was done through az k8sconfiguration command.

    ```
    kubectl get pods 
    ```
    ![](./images/arc-0032.png)   

2. Browse to the forked repo of https://github.com/Azure/arc-k8s-demo

3. Navigate to **cluster-apps->azure-vote.yaml** and edit the yaml file

   ![](./images/azure-arc-15.png)   

4. Change the cpu request from 250 to **280** in line 62 

   ![](./images/azure-arc-16.png)   

## Task 6: Verify changes are deployed to the cluster.

1.  Run the following command and copy the pod name starting with **azure-vote-front-**

    ```
    kubectl get pods 
    ```
    ![](./images/arc-0033.png) 
    
    Observe in the above image that the previous pod is terminated and a new pod is created based on the updated configuration

2.  Replace the pod name that you copied in the previous step and run the command
 
    ```
    kubectl get pod <podname> -o yaml
    ```
    ![](./images/arc-0034.png)   
    
    Observe the CPU request value that you updated in the previous steps in the output as shown:
    
    ![](./images/arc-0035.png)   


