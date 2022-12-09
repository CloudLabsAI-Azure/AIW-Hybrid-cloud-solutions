# HOL-2: Exercise 1: Enable GitOps Configuration on connected K8s Cluster

#### Develop cloud-native apps, operate anywhere

In addition to managing and monitoring their Kubernetes clusters, Contoso’s central development teams are building applications for internal inventory management at their distribution sites. They need these applications to be containerized and run-on Kubernetes clusters. The locations are spread across the country and Contoso is faced with the challenge of how to uniformly deploy, configure and manage their containerized applications across all these locations. By leveraging GitOps on Azure Arc enabled Kubernetes, Contoso can centrally declare their Kubernetes configurations and applications in a Git repository and deploy them to all clusters simultaneously. Developers are more empowered because they can commit changes directly in the Git repo and these updates are also automatically rolled out to all the clusters.

GitOps, as it relates to Kubernetes, is the practice of declaring the desired state of Kubernetes configuration (deployments, namespaces, etc.) in a Git repository followed by a polling and pull-based deployment of these configurations to the cluster using an operator. In this exercise, you will deploy a sample Kubernetes app using az k8sconfiguration command and gitops and also update the configuration in the repository which you have linked to connected cluster and verify if cluster is getting updated based on the changes made. You will be using the Kubernetes cluster with which you connected in the earlier exercise.

## Task 1: Fork the GitHub Arc K8s demo repository

1. Launch the following GitHub repository url ```https://github.com/Azure/arc-k8s-demo```. On upper right corner you will see **Sign in** and **Sign up** options, if you already have a github account then click on **Sign in**, otherwise **Sign up**.

   ![](.././media/01.png) 

2. Now, from the upper right cornor, click on the **Fork** to fork the repository to your GitHub account.

   ![](.././media/02.png)

## Task 2: Deploy App using az k8sconfiguration

1. Using the Azure CLI extension for **k8sconfiguration**, link connected cluster to personal git repository. Provide this configuration a name **cluster-config**, instruct the agent to deploy the operator in the **cluster-config** namespace, and give the operator **cluster-admin** permissions. 

1. From the start menu of the **ARCHOST** VM, search for **putty** and open it with double click or other way.

    ![](.././media/startputty.png "Search Putty")
     
1. In Putty Configuration tool, enter the **ubuntu-k8s** VM private IP - ```192.168.0.8```, make sure the Port value is ```22```. Once you entered the private IP of the **ubuntuk8s** vm, click on the Open to lunch the terminal.

    ![](.././media/putty-enter-ip.png "Enter ubuntu-k8s VM private IP")
    
1. Enter the **ubuntu-k8s** vm username - ```demouser``` in **login as** and then hit **Enter**. 

   ```
   demouser
   ```

1. Now, enter the password - ```demo@pass123``` and press **Enter**. Remember password will be hidden and will not be visible in terminal.

   ```
   demo@pass123
   ```

    ![](.././media/enter-ubuntu-k8s-credentials.png "Enter ubuntu-k8s credentials")
    
    > Note: To paste any value in Putty terminal, just copy the values from anywhere and then right click on the terminal to paste the copied value.

1. Login with Sudo. Run following command and provide the Password `demo@pass123`.

   ```
   sudo su
   ```
   
   ```
   demo@pass123
   ```
    
 1. Run the below commands to upgrade the az packages and az module. 
   
     ```
      curl https://bootstrap.pypa.io/get-pip.py > get-pip.py
      apt install pip
      python3 get-pip.py
      python3 -m pip install -U pip
      python3 -m pip install --upgrade pip --target /opt/az/lib/python3.6/site-packages/
      az upgrade -y
      init 6 #TO restart
    ```

1. Open a new Putty session, re-perform the steps from step-2 to step-6 of the same task to get the upgraded packages and then continue from  step-9.

1. Next, you have to navigate back to the Desktop of the provided virtual Machine ARCHOST VM 💻, and then click on `installArcAgentLinux.txt` file to open it.

   ![](.././media/variableazlogin.gif "Install Arc Agent")

1. Then, select the first 7 lines and, then right click and copy. 

1. Then, go back to putty session and paste it in ubuntu-k8s VM by doing right click and it will start getting executed. 

1. Once it is executed, you have declared the values of AppID, AppSecret, TenantID, SubscriptionID, ResourceGroup, and location, and then logged into azure using the 7th line. You can also find the values of these variables in the **Environment Details** tab. These variables are required for the next steps.

    ![](.././media/variableazlogin.png "azlogin")

1. Copy the below command to any text editor

   ```
   az k8sconfiguration create --name cluster-config --cluster-name microk8s-cluster --resource-group $ResourceGroup --operator-instance-name cluster-config --operator-namespace cluster-config --repository-url https://github.com/<githubusername>/arc-k8s-demo --scope cluster --cluster-type connectedClusters
   ```

1. Then, replace as mentioned below and run the command in ubuntu-k8s VM SSH session that is opened in putty:

   - You have to replace **<githubusername>** in the previous command with your username of the GitHub account to which you had forked the repository. 
   
   If you get a prompt asking **Do you want to install the extension k8sconfiguration**, type **Y** and press **Enter**.

   ![](.././media/04.png) 
   
     > **Note**: Wait for 5 mins before performing the next step

     > ```Info``` - Once you execute the above command, the manifests in your forked repository provision a few namespaces, deploy workloads, and provide some team-specific configuration. Using this repository with GitOps creates the following resources on your kubernetes cluster:

     > *Namespaces*: cluster-config, team-a, team-b
     
     > *Deployment*: cluster-config/arc-k8s

     > *ConfigMap*: team-a/endpoints
     
     > The config-agent polls Azure for new or updated configurations.

## Task 3: Validate the sourceControlConfiguration

1. Now, to validate whether the **sourceControlConfiguration** was successfully created and the **compliance** state is Installed, you have to run the command given below. 
   
   > Note: If the state is pending, retry the same command again after every 1 minute.

   ```
   az k8sconfiguration show --resource-group $ResourceGroup --name cluster-config --cluster-name microk8s-cluster --cluster-type connectedClusters
   ```
     > **Note**: that the sourceControlConfiguration resource is updated with compliance status, messages, and debugging information in the output.

   The output should include the following value as given here: ```"complianceState": "Installed"```

   ![](.././media/05.png) 
  
2. In the Azure Portal which you have opened in the browser window, navigate to Resource group **azure-arc** RG-> Resource **microk8s-cluster** -> **GitOps**. Ensure that the operator state status is **Succeeded**.

   ![](.././media/hol2tsk3stp2.png) 
  
## Task 4:  Validate the Kubernetes configuration

After config-agent has installed the flux instance, resources held in the git repository should begin to flow to the cluster. 

   > ```Info```: Flux is the operator that makes GitOps happen in your cluster. It ensures that the cluster config matches the one in git and automates your deployments.

1. To verify that the namespaces, deployments, and resources are created, **run the following command** in the SSH Session opened to the ubuntu-k8s VM from putty:

   ```
   kubectl get ns --show-labels
   ```
 
   The output shows that team-a, team-b, Gitops, and cluster-config namespaces have been created as shown:
  
   ![](.././media/07.png) 
   
2. The **flux operator** will be deployed to **cluster-config** namespace, as directed by our **sourceControlConfig**:
      
    ```
    kubectl -n cluster-config get deploy  -o wide
    ```
   
    The output should be as shown:
   
    ![](.././media/08.png) 
  
3. You can explore the other resources deployed as part of the configuration repository by running the following commands:

   ```
   kubectl -n team-a get cm -o yaml
   ```

## Task 5: Make changes to cluster declarations in the Git repo.

1.  Run the following command in SSH session that is already opened to the ubuntu-k8s from putty and confirm that you are able to see **arc-k8s-demo-** pod.

    ```
    kubectl get pods 
    ```
    ![](.././media/pods1.png)

2. Browse to the **forked** repo of ```https://github.com/Azure/arc-k8s-demo```, which will be in the following format: ```https://github.com/<yourGitHubaccountusername>/arc-k8s-demo```

3. Navigate to **cluster-apps->arc-k8s-demo.yaml** and edit the yaml file.

   ![](.././media/pods2.png)   

4. Change the cpu request to **120** in line 32 and then scroll down to the buttom and click on **Commit changes** to confirm the changes to cpu request.

   ![](.././media/pods3.png)
   

## Task 6: Verify changes are deployed to the cluster.

1.  Run the following command in the SSH Session that you have opened to ubuntu-k8s VM from Putty and copy the pod name starting with **arc-k8s-demo-**

    ```
    kubectl get pods 
    ```
    ![](.././media/pods4.png) 
    
    Observe in the above image that the previous pod is terminated and a new pod is created based on the updated configuration.

   > ```Note```: If you don't see any change, retry running the command after a couple of minutes

2.  Replace the pod name that you copied in the previous step and run the command
 
    ```
    kubectl get pod <podname> -o yaml
    ```
    Example: ```kubectl get pod arc-k8s-demo-5779f4d696-fm22j -o yaml```
   
    ![](.././media/pods5.png)   
    
    Observe the CPU request value that you updated in the previous steps in the output as shown:
    
    ![](.././media/pods6.png)   

In this exercise, you have seen how to enable GitOps Configuration on connected K8s Cluster and how it works.

Now, you can move on to the next exercise.

