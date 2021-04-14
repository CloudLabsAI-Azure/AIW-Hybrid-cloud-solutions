![](media/Arc-logo.png)
# Azure Arc Hands-on Labs
## What is Azure Arc?
For customers who want to simplify complex and distributed environments across on-premises, edge, and multi-cloud, [Azure Arc](https://azure.microsoft.com/services/azure-arc/) enables deployment of Azure services anywhere and extends Azure management to any infrastructure. 
Azure Arc helps you accelerate innovation across hybrid and multi-cloud environments and provides the following benefits to your organization:
   * **Gain central visibility, operations, and compliance** – Standardize visibility, operations, and compliance across a wide range of resources and locations by extending the Azure control plane. Right from Azure, you can easily organize, govern, and secure Windows, Linux, SQL Servers and Kubernetes clusters across datacenters, edge, and multi-cloud.
   * **Build Cloud native apps anywhere, at scale** – Centrally code and deploy applications confidently to any Kubernetes distribution in any location. Accelerate development by using best in class applications services with standardized deployment, configuration, security, and observability.
   * **Run Azure data services anywhere** – Flexibly use cloud innovation where you need it by deploying Azure services anywhere. Implement cloud practices and automation to deploy faster, consistently, and at scale with always-up-to-date Azure Arc enabled services.
   
![](media/Arc-Chart.png)

## Azure Arc Hands-on Labs
The following labs provide you a quick and easy way to get started with Azure Arc through virtual environments that do not require any complex set-up or installations. For the purposes of these exercises, let’s consider Contoso, a large manufacturing organization. Their IT systems run Windows, Linux, SQL Servers, and Kubernetes clusters across multiple locations, including on-premises datacenters, distribution centers and multiple public clouds. This poses operational challenges for Contoso. They’d like a consistent way to govern and operate across these disparate environments, ensure security across the entire organization, and enable innovation and developer agility (especially with their investments in cloud native practices), all while meeting regulatory and compliance requirements.

With Azure Arc, Contoso can unify inventory, governance, and compliance ensuring consistency for their entire IT landscape. They already take advantage of the core management capabilities such as tagging, update management, governance with Azure Policy, monitoring with Azure Monitor, security with Azure Defender, and more for their Azure workloads but would like to extend these same capabilities to their resources outside Azure. By onboarding their servers and Kubernetes clusters running outside Azure to Azure Arc, Contoso can take advantage of all the Azure Resource Manager (ARM) capabilities mentioned above. In addition, with Azure Arc enabled Kubernetes, Contoso can guarantee Kubernetes deployments and app consistency through GitOps-based configuration management for their Kubernetes clusters in Azure, on-premises and in other clouds.
Leveraging Azure Arc enabled data services, Contoso is interested in implementing cloud-native, evergreen versions of SQL and PostgreSQL Hyperscale to reduce the management overhead and deploy their applications and databases anywhere with elastic scale.
Let’s take the journey together with Contoso and see how easy it is to accomplish all the above with Azure Arc.

## In the next pages, you will be covering each of the scenarios below with specific instructions to perform the lab:

  * [Hands-on Lab 1](./HOL-1): First, let's start by learning how to onboard a virtual machine and Kubernetes cluster, both running on-premises, to Azure Arc, then apply a few Azure Policies, enable monitoring, and alerts as well as Integrate Azure Security Center and Azure Sentinel to your on-premises resources. You’ll also be able to deploy an SQL Server on the VM, connect it to Arc and explore SQL Assessments for this resource.
  * [Hands-on Lab 2](./HOL-2): In this lab, you’ll learn to deploy GitOps configurations to the Kubernetes cluster that you onboarded to Arc earlier and enable Azure Policy add-on for Kubernetes to the same cluster.
  * [Hands-on Lab 3](): In this lab, you will deploy the Azure Arc data controller, Azure SQL Managed Instance & Azure PostgreSQL Hyperscale to an existing Kubernetes cluster.
