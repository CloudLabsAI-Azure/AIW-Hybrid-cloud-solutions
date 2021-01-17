## Exercise 3: Onboard SQL Server to Arc
In this Exercise you will onboard SQL Server to Azure Arc using Azure Portal and Powershell.

## Task 1: Login To Azure Portal

1. In the **LabVM/ARCHost VM**, click on Azure portal shortcut of Microsoft Edge browser which is created on desktop.
  
    ![](.././media/0.png "edge")
   
1. On **Sign in to Micsoft Azure** tab you will see login screen, in that enter following **Email/Username** and then click on **Next**. 
   * Email/Username: <inject key="AzureAdUserEmail"></inject>
   
1. Now enter the following **Password** and click on **Sign in**.
   * Password: <inject key="AzureAdUserPassword"></inject>
   
1. If you see the pop-up **Stay Signed in?**, click No

1. If you see the pop-up **You have free Azure Advisor recommendations!**, close the window to continue the lab.

1. If **Welcome to Microsoft Azure** popup window appears, click **Maybe Later** to skip the tour.
      
1. Click in search blade and search for ```SQL Server```, select **SQL Server - Azure Arc**.
 
    ![](.././media/sqlserver.png "sqlsearch")
   
1. Click on the **Create SQL Server - Azure Arc** button to create the **SQL Server- Azure Arc**. 
 
    ![](.././media/createsql.png "sqlsearch")
   
1. You will see the prerequistes page, you can explore the page and just click on the **Next: Server details** button.
    
    - **Note**: We have already completed the prerequistes part for you. 
    
    ![](.././media/presql.png "sqlsearch")
   
1. On the **Server Details** blade enter the below details.
 
    - Subscription: Leave default
    - Resource group: Select **Azure-arc-SUFFIX** from dropdown list.
    - Region: Select same region as Resource group.
    - Operating Systems: Select **windows**.
      Now click on next button.
   
    ![](.././media/detailssql.png "sqlsearch")
   
1. Leave default for tags blade and click on **Next: Run Script** button.
 
1. On the **Script** blade explore the script, we will be using this Powershell script to **Register Azure Arc enabled SQL Server**.
 
     > **Note:** You dont have to do anything here, we have already downloaded this script inside you VM.
    
    ![](.././media/runsql.png "sqlsearch")
     
## Task 2: Register Azure Arc enabled SQL Server.

1. From your **LabVM/ARCHost VM**, Open **Windows Powershell** icon from the desktop.
 
    ![](.././media/powershell.png "sqlsearch")
  
1. Run the below command to change the directory to where the Script is download.
 
     ``` cd C:\LabFiles ```
     
1. After changing the directory to **Lab files**, Run the below command:

     ``` .\Execute-RegisterSqlServerArc.ps1 ```
     
    > Note: This will automatically run the **RegisterSqlServerArc.ps1** script inside **sqlvm** deployed on Hyper-V.

1. After running the command you will see the script started running.

    ![](.././media/run.png "sqlsearch")
  
1. After some time you will see that the script execution is completed. Make sure that you see the below output.

    ![](.././media/completed.png "sqlsearch")
  
1. Go back to azure portal and search for the **SQL Server -Azure Arc**, You will be able to see 1 one resource that we just created using powershell script in previous step.

    ![](.././media/sqlvm.png "sqlsearch")
  
 1. Select the **SQLVM** resource and now you can see the dashboard of **SQLVM** SQL Server -Azure Arc.

    ![](.././media/dashsql.png "sqlsearch")
 
