# HOL-1: Exercise 3: Onboard SQL Server to Arc

In the last exercise, you have seen how to enable security measures and monitoring for Arc-enabled servers. In this exercise, you will onboard SQL Server to Azure Arc using Azure Portal and PowerShell commands.

## Task 1: Login To Azure Portal

1. Navigate back to Azure Portal which you have already opened in the previous exercises.
      
1. Click on the search blade at the top and search for ``SQL Server``, select **SQL Server - Azure Arc**.
 
   ![](.././media/hyd19.png "sqlsearch")
   
1. Click on the **Add** button to create the **SQL Server- Azure Arc**. 
 
   ![](.././media/ss2.png "sqlsearch")
   
1. In the Adding existing SQL Servers instances page, Click on **Connect Servers**.

   ![](.././media/ss3.png "sqlsearch")
   
1. You will now see the prerequisite page. You can explore the page and then click on the **Next: Server details** option.
    
   > **Note**: We have already completed the prerequisite part for you. 
    
   ![](.././media/presql.png "sqlsearch")
   
1. On the **Server Details** blade, enter the below details.
 
   - Subscription: Leave default
   - Resource group: Select **azure-arc** from dropdown list.
   - Region: Select the same region as the Resource group.
   - Operating Systems: Select **Windows**.
   - Server Name: Type **sqlvm**
   - License Type: Select **I have a production environment on this server with Enterprise or Standard edition covered by Software Assurance or SQL subscription ("Paid")**.

     Now, click on the **Next: Tags** button.
   
      ![](.././media/H1E3T1S6.png "sqlsearch")
   
1. Leave the default for tags blade and click on **Next: Run Script** button.
 
1. On the **Script** blade, explore the given script. We will be using this PowerShell script to **Register Azure Arc enabled SQL Server** later.
 
   > **Note**: Please **skip the script download** from here by clicking on ``X`` at the top right as we have **already downloaded** this script inside the Lab VM for you.
    
   ![](.././media/runsqlv2.png "sqlsearch")
     
## Task 2: Register Azure Arc-enabled SQL Server.

1. Minimize the Azure Portal Browser window. 

1. From the desktop of your **LabVM/ARCHost VM**, double click on **Windows PowerShell** icon to open it.
 
   ![](.././media/powershell.png "sqlsearch")
  
1. Then, run the below command to change the directory to where the script gets downloaded.
 
   ``` 
   cd C:\LabFiles
   ```

1. After changing the directory to **Lab files**, run the command given below:

   ```
   .\Execute-RegisterSqlServerArc.ps1
   ```
     
   > **Note** : This will initiate the execution of **RegisterSqlServerArc.ps1** script inside **sqlvm** that is deployed on Hyper-V.

1. You will be asked to authenticate the PowerShell. Open the browser and paste the URL **`https://microsoft.com/devicelogin`(1)**, paste the **code(2)** and then provide credentials as below.

      * Email/Username: <inject key="AzureAdUserEmail"></inject>
      * Password: <inject key="AzureAdUserPassword"></inject>

         ![](.././media/H1E3T2S5.png "sqlsearch")

1. After authentication, you will see some outputs which show that the script resumes.
   
   > **Note**: If the script fails to execute. Please execute/run the script again. 

   ![](.././media/H1E3T2S6.png "sqlsearch")
  
1. In 5-10 minutes, you will see that the script execution is completed. Make sure that you see the following output: ``SQL Server - Azure Arc extension is successfully installed``

   ![](.././media/H1E3T2S7.png "sqlsearch")
  
1. Bring back the browser window where you had opened Azure Portal and search for **SQL Server -Azure Arc**. If you are already on that page, you will need to click on the Refresh button. On that page, you will see one resource **Sqlvm** that we just created using the PowerShell script in the previous step.

   ![](.././media/hyd17.png "sqlsearch")
  
1. Select the **Sqlvm** resource and now you can see the dashboard of **sqlvm** SQL Server -Azure Arc from Azure Portal.

   ![](.././media/hyd18.png "H1E3T2S8")

## Task 3: Run on-demand SQL Assessment.

1. Click on the search blade at the top and search for ```Log Analytics workspace```, then select **LogAnalyticsWS-<inject key="DeploymentID/Suffix" />**.

1. Then select **Agents** from the left side menu. Click on **Log Analytics agent instructions** and copy the value of **Workspace ID** and **Primary Key** and save it into a Notepad or Notepad++ for later use.
 
   ![](.././media/H1E3T3S2.png "sqlsearch")

1. Now, search for **Machines - Azure Arc** from search box and click on **Machines - Azure Arc**.
 
   ![](.././media/hyd20.png "server-azure-arc-search") 
   
1. Select **sqlvm** from the list of Azure Arc servers.

   ![](.././media/hyd21.png "select-sql-vm")
    
1. Click on the **Extension** button from the left side menu and click on the **+ Add** button to add a new extension.
 
   ![](.././media/hyd22.png "sqlsearch")
    
1. Click on the **+ Add** button to add a new extension.

   ![](.././media/hyd23.png "sqlsearch")

1. Search and select the **Log Analytics Agent - Azure Arc** extension and click on the **Next** button to continue.
 
   ![](.././media/hyd24.png "sqlsearch")    
   
1. At this step, you must enter the Log Analytics workspace ID and a key to install the MMA ( Microsoft Monitoring Agent ) in the **sqlvm**.
  
1. Now, enter the Workspace ID and Key that you copied from the previous step, click on **Review + Create** button.
 
   ![](.././media/hyd25.png "sqlsearch")

1. Review the configuration, and click on **Create**.

   ![](.././media/hyd26.png "sqlsearch")

   > **Note**: The deployment will take around 5 to 10 minutes to complete. You have to wait for this deployment to be successful to proceed to the next step.
   
1. Open **sqlvm** from the Hyper-V Manager by double clicking on **sqlvm**.

   ![](.././media/opensqlvm.png "opensqlvm")

1. On Connect to sqlvm box, scroll the bar towards Small to open the VM in the smallest window and then click on the **Connect** button.

   ![](.././media/scalsqlvm.png "scalsqlvm")

1. Type password **demo@pass123** and press **Enter** button to login. Then, you can resize the sqlvm window size at your convenience.
   
   ![](.././media/entervmpassword.png "entervmpassword")

1. Click on Start Menu and search for **Microsoft SQL Server Management Studio 18** and open it.
   
   ![](.././media/H1E3T3S13.png "H1E3T3S13")
  
1. On **Connect to server** pop-up select SQLVM.

   ![](.././media/H1E3T3S14.png "H1E3T3S14")
   
1. In the left pane, expand **Security** then **Logins**. In Logins, right-click on **NT AUTHORITY\SYSTEM** and click on **Properties**.

   ![](.././media/H1E3T3S15.png "H1E3T3S15")
  
1. In Login Properties pane, click on **Server Roles** then enable the **sysadmin** role and click on **Ok**.

   ![](.././media/H1E3T3S16.png "H1E3T3S16")
 
1. Then, Go to **SQLVM** Azure Arc - SQL Server resource select the **Best practices assessment** under settings from the left pane and click on **Change license type**.
   
   > **Note**: Skip and continue from **Step 19** if **change license type** is not available 
   
   ![](.././media/H1E3T3S17.png "H1E3T3S17")

1. Under **SQL Server management details**, select license type as **Paid** and click on **Save**.

   ![](.././media/H1E3T3S18.png "H1E3T3S18")

1. Select the log Analytics Workspace as **LogAnalyticsWS-<inject key="DeploymentID/Suffix" />** from the drop-down and click on **Enable assessment**.

   ![](.././media/H1E3T3S19.png "H1E3T3S19")
   
   > **Note**: After enabling the assessment, wait for a few minutes to get it complete. 
 
1. Once the assessment is **completed**, click on it to see the results. 

   ![](.././media/H1E3T3S20.png "H1E3T3S20")
   
1. The **Assessment results** will look like below:

    ![](.././media/H1E3T3S21.png "H1E3T3S21")
      
   > **Note**: Now you can move to the next Exercise, you don't have to wait here for the Result to appear.   

## In this exercise, you have covered the following:
 
   - Register Azure Arc-enabled SQL Server.
   - Run on-demand SQL Assessment.
