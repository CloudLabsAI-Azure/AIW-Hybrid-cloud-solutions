# Exercise 3: Onboard Azure Monitor for containers with Azure Arc-enabled Kubernetes cluster

Contoso's Central IT team is enhancing their monitoring capabilities by configuring Azure Monitor for their Kubernetes clusters. This involves setting up Container insights with a Log Analytics workspace to collect and analyze telemetry data from the Kubernetes cluster. Once configured, they will be able to monitor the cluster's performance and health, enabling proactive management and troubleshooting.

In this exercise, you will see how to configure Azure Monitor for containers and view insights for Kubernetes - Azure Arc resource.

## Task 1: Configuring Azure Monitor

In this task, you'll configure Azure Monitor for the Kubernetes cluster connected to Azure Arc. By navigating to the Azure Arc resource group and selecting the specific Kubernetes cluster, you'll access the monitoring settings. From there, you'll proceed to configure Container insights by selecting the appropriate Log Analytics workspace. This configuration enables the collection and visualization of insights data from the Kubernetes cluster, facilitating monitoring and analysis of its performance and health status

1. Navigate to **azure-arc** resource group and select **microk8s-cluster** Kubernetes - Azure Arc resource from the resources listed.

   ![](.././media/hol2-ex3-1.png "azuremonitor")

2. On the **microk8s-cluster** Kubernetes - Azure Arc pane, select **Insights (1)** under Monitoring from left-hand side menu and click on **Configure monitoring (2)**.

   ![](.././media/hyd30.png "azuremonitor")

3. On the Configure Container insights blade, for the Log Analytics workspace select the **loganalyticsws- <inject key="DeploymentID/Suffix" />** from the dropdown and click on **Configure**.

   ![](.././media/hyd31.png "azuremonitor")

4. You will be able to see the insights data after 30-60 minutes.

5. In the Insights pane, refresh the page and filter the **Time range = Last 6 Hours (1)**. Click on **Cluster (2)** and review the insights. Now that your cluster is being monitored, you can watch the monitoring telemetry for the cluster, nodes and pods.

   ![](.././media/hol2-ex3-4.png "azuremonitor")

6. In the same pane, filter the **Time range = Last 6 Hours (1)** and click on **Nodes (2)** and select **ubuntu-k8s**. Here you can observe that the ubuntu-k8s server azure-arc node is listed below, which defines the integration of Azure Arc connected cluster with Azure Monitor for Containers.

   ![](.././media/hol2-ex3-5.png "azuremonitor")

   ![](.././media/hol2-ex3-6.png "azuremonitor")

7. In the same pane, filter the **Time range = Last 6 Hours (1)** and click on **Containers (2)**. You will be able to see the list of Containers that are linked to the pod and node which you have monitored in the previous steps.

   ![](.././media/hol2-ex3-7.png "azuremonitor")
