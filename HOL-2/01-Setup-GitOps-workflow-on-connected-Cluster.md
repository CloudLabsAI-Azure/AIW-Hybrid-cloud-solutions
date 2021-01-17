# Exercise 1: Setup GitOps workflow on connected Cluster

GitOps, as it relates to Kubernetes, is the practice of declaring the desired state of Kubernetes configuration (deployments, namespaces, etc.) in a Git repository followed by a polling and pull-based deployment of these configurations to the cluster using an operator. In this exercise, you will deploy a sample kubernetes app using az k8sconfiguration command and gitops and also update the configuration in the repository which you have linked to connected cluster and verify if cluster is getting updated based on the changes made.

## Task 1: Create a Configuration

1. Launch the following GitHub repository url ```https://github.com/Azure/arc-k8s-demo```. On upper right cornor you will see **Sign in** and **Sign up** options, if you already have a github account then click on **Sign in**, otherwise **Sign up**.

   ![](.././media/01.png) 

2. Now, from the upper right cornor, click on the **Fork** to fork the repository to your GitHub account.

   ![](.././media/02.png)

## Task 2: Deploy App using az k8sconfiguration

1. Using the Azure CLI extension for **k8sconfiguration**, link connected cluster to personal git repository. Provide this configuration a name **cluster-config**, instruct the agent to deploy the operator in the **cluster-config** namespace, and give the operator **cluster-admin** permissions. 

1. From the start menu of the **ARCHOST** VM, search for **putty** and open it with double click or other way.

    ![](.././media/startputty.png "Search Putty")
     
1. In Putty Configuration tool, enter the **ubuntu-k8s** VM private IP - ```192.168.0.8```, make sure the Port value is ```22```. Once you entered the private IP of the **ubuntuk8s** vm, click on the Open to lunch the terminal.

    ![](.././media/putty-enter-ip.png "Enter ubuntu-k8s VM private IP")
    
1. Enter the **ubuntu-k8s** vm username - ```demouser``` in **login as** and then hit **Enter**. Now, enter the password - ```demo@pass123``` and press **Enter**. Remember password will be hidden and not be visible in terminal, don't worry about that.

    ![](.././media/enter-ubuntu-k8s-credentials.png "Enter ubuntu-k8s credentials")
    
    > Note: To paste any value in Putty terminal, just copy the values from anywhere and then right click on the terminal to paste the copied value.
    
1. Login to **Azure** using follwing command:
   
     ```./azlogin.sh```
   
   ![](.././media/02.png)

1. Run the following command:

   - Replace **your personal github account name** with your personal github account that you are using to perform the lab and Signed in above.

   ```
   az k8sconfiguration create --name cluster-config --cluster-name microk8s-cluster --resource-group $ResourseGroup --operator-instance-name cluster-config --operator-namespace cluster-config --repository-url https://github.com/<your personal github account name>/arc-k8s-demo --scope cluster --cluster-type connectedClusters
   ```
   
   The output should be as shown:

   ![](.././media/04.png) 
   
     > Note: Wait for 5 mins before performing the next step

## Task 3: Validate the sourceControlConfiguration

1. Validate whether the **sourceControlConfiguration** was successfully created and the **compliance** state is Installed. If it is pending, retry the same command again after sometime.

   ```
   az k8sconfiguration show --resource-group Azure-Arc-XXXXXX --name cluster-config --cluster-name AzureArcAKSCluster1 --cluster-type connectedClusters
   ```
     > Note: that the sourceControlConfiguration resource is updated with compliance status, messages, and debugging information in the output.

   The output should be as shown:

   ![](.././media/arc-0024.png) 
  
2. Navigate to **Azure-Arc RG->AzureArcAKSCluster1->Configurations**. Ensure that the operator state status should show as **Installed**.

   ![](.././media/azure-arc-10.png) 
  
## Task 4:  Validate the Kubernetes configuration

1. After config-agent has installed the flux instance, resources held in the git repository should begin to flow to the cluster. Check to see that the namespaces, deployments, and resources are created by **Running the following command:**

   ```
   kubectl get ns --show-labels
   ```
 
   The output shows that team-a, team-b, itops, and cluster-config namespaces have been created as shown:
  
   ![](.././media/azure-arc-11.png) 
   
2. The **flux operator** will be deployed to **cluster-config** namespace, as directed by our **sourceControlConfig**:
      
    ```
    kubectl -n cluster-config get deploy  -o wide
    ```
   
    The output should be as shown:
   
    ![](.././media/azure-arc-12.png) 
  
3. You can explore the other resources deployed as part of the configuration repository by running the following commands:

   ```
   kubectl -n team-a get cm -o yaml
   ```
   
   ```
   kubectl -n itops get all
   ```
## Task 5: Make changes to cluster declarations in the Git repo.

1.  Run the following command in Powershell window and confirm that the Age is the same for both **azure-vote-back** and **azure-vote-front** apps. It will be same since the deployment was done through **az k8sconfiguration** command.

    ```
    kubectl get pods 
    ```
    ![](.././media/arc-0032.png)   

2. Browse to the **forked** repo of ```https://github.com/Azure/arc-k8s-demo```

3. Navigate to **cluster-apps->azure-vote.yaml** and edit the yaml file

   ![](.././media/azure-arc-15.png)   

4. Change the cpu request from 250 to **280** in line 62 

   ![](.././media/azure-arc-16.png)   

## Task 6: Verify changes are deployed to the cluster.

1.  Run the following command and copy the pod name starting with **azure-vote-front-**

    ```
    kubectl get pods 
    ```
    ![](.././media/arc-0033.png) 
    
    Observe in the above image that the previous pod is terminated and a new pod is created based on the updated configuration

2.  Replace the pod name that you copied in the previous step and run the command
 
    ```
    kubectl get pod <podname> -o yaml
    ```
    ![](.././media/arc-0034.png)   
    
    Observe the CPU request value that you updated in the previous steps in the output as shown:
    
    ![](.././media/arc-0035.png)   


