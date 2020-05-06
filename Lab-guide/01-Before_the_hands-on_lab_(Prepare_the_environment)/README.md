![](.//media/image1.png)

> Before the hands-on lab (Prepare the environment)

# Contents

[Before the hands-on lab (Prepare the environment)
1](#before-the-hands-on-lab-prepare-the-environment)

[Task 1: Install Azure Storage Explorer
1](#install-azure-storage-explorer)

[Task 2: Clone the GitHub repository 3](#clone-the-github-repository)

[Task 3: Deploy Azure Resource Group 4](#deploy-azure-resource-group)

[Task 4: Deploy Azure Data Factory 5](#deploy-azure-data-factory)

[Task 5: Deploy an Azure Storage Account as below
6](#deploy-an-azure-storage-account-as-below)

[Task 6: Deploy Azure Key Vault 7](#deploy-azure-key-vault)

[Task 7: Deploy Azure SQL Database 10](#deploy-azure-sql-database)

[Task 8: Create Blob Storage Containers for the Data Warehouse Output
and staging
14](#create-blob-storage-containers-for-the-data-warehouse-output-and-staging)

[Task 9: Create a container for WideWorldImporters Staging Input files
15](#create-a-container-for-wideworldimporters-staging-input-files)

[Task 10: Create a container for SmartFoods Staging Input files
15](#create-a-container-for-smartfoods-staging-input-files)

# Before the hands-on lab (Prepare the environment)

Duration: 60 minutes

In this exercise, you will set up your environment for use in the rest
of the hands-on lab. You should follow all the steps provided in the
Before the Hands-on Lab section to prepare your environment *before*
attempting the hands-on lab.

You will be provisioning the below resources with the following name
pattern:

1.  **Resource group:** adf-\<your name\>-dev-rg

2.  **Data Factory:** adf-\<your name\>-dev-datafactory

3.  **Storage Account:** adf\<your name\>devstorage

4.  **Azure Key Vault:** adf-\<your name\>-dev-kv

5.  **Azure SQL DB:** adf-\<your name\>-dev-sqldb

#### Install Azure Storage Explorer

Microsoft Azure Storage Explorer is a standalone app that makes it easy
to work with Azure Storage data on Windows, macOS, and Linux (Azure Docs
on Storage Explorer
[here](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows))

1)  Download Azure Storage Explorer from [this
    page](https://azure.microsoft.com/en-us/features/storage-explorer/)

2)  Install Azure Storage Explorer

3)  Click the Open Connect dialog

4)  Select “Add an Azure account” and continue logging in to your Azure
    account.

5)  Once you log in you can see a list of your storage accounts.

![](.//media/image2.png)

![](.//media/image3.png)

![](.//media/image4.png)

#### Clone the GitHub repository

Clone this GitHub repository to your machine or download the source Zip
file of this repo

![](.//media/image5.png)

#### Deploy Azure Resource Group

1)  Search for Resource groups from the main search bar in Azure portal

![](.//media/image6.png)

2)  Click Add

![](.//media/image7.png)

1)  **For Resource group name: adf-\<your name\>-dev-rg for example:
    adf-john-dev-rg**

![](.//media/image8.png)

#### Deploy Azure Data Factory

1)  Similar to Task 0 search for “Azure Data Factory”

2)  Click Add

3)  **For Storage account name: adf-\<your name\>-dev-datafactory for
    example: adf-john-dev-datafactory**

4)  Deploy the Azure Data Factory in the region of your choice and for
    Resource Group choose the RG created in Task 0.

5)  **Uncheck “Enable GIT”**

6)  Click Create

![](.//media/image9.png)

#### Deploy an Azure Storage Account as below

1)  Search for Storage Accounts

2)  Click Add

3)  Provide subscription, resource group

4)  **For Storage account name: adf\<your name\>devstorage for example:
    adfjohndevstorage**

5)  Select Location

6)  For Account Kind Storage V2

7)  **For Replication change to LRS**

8)  Click “Review + Create” and then “Create”

> ![](.//media/image10.png)

#### Deploy Azure Key Vault

**Azure Key Vault:** Centralizing storage of application secrets in
Azure Key Vault allows you to control their distribution. Key Vault
greatly reduces the chances that secrets may be accidentally leaked.
When using Key Vault, application developers no longer need to store
security information in their application. Not having to store security
information in applications eliminates the need to make this information
part of the code. For example, an application may need to connect to a
database. Instead of storing the connection string in the app's code,
you can store it securely in Key Vault

1)  Search for Azure Key Vault

2)  Click Add

3)  Provide subscription, resource group

4)  **For Key Vault Name use: adf-\<your name\>-dev-kv for example:
    adf-john-dev-kv**

5)  Select Location

6)  Leave the rest as it is

7)  Click “Review + Create” and then “Create”

![](.//media/image11.png)

![](.//media/image12.png)

#### Deploy Azure SQL Database

Azure SQL Database is a general-purpose relational database, provided as
a managed service. With it, you can create a highly available and
high-performance data storage layer for the applications and solutions
in Azure. SQL Database can be the right choice for a variety of modern
cloud applications because it enables you to process both relational
data and [non-relational
structures](https://docs.microsoft.com/en-au/azure/sql-database/sql-database-multi-model-features),
such as graphs, JSON, spatial, and XML.

It's based on the latest stable version of the [Microsoft SQL Server
database
engine](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json).
You can use advanced query processing features, such
as [high-performance in-memory
technologies](https://docs.microsoft.com/en-au/azure/sql-database/sql-database-in-memory) and [intelligent
query
processing](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing?toc=/azure/sql-database/toc.json).
In fact, the newest capabilities of SQL Server are released first to SQL
Database, and then to SQL Server itself. You get the newest SQL Server
capabilities with no overhead for patching or upgrading, tested across
millions of databases.

![](.//media/image13.png)

1)  Search for SQL Database

2)  Click Add

3)  Provide subscription, resource group

4)  **For Database Name use: adf-\<your name\>-dev-sqldb for example:
    adf-john-dev-sqldb**

> ![](.//media/image14.png)
> 
> ![](.//media/image15.png)

5)  Under Server click “Create New”

6)  **For Server Name use: adf-\<your name\>-dev-sqlserver for example:
    adf-john-dev-sqlserver**

7)  Provide admin user and password of your choice

8)  Location

9)  Click OK

10) For SQL elastic pools select “No”

11) Click configure database
    
    1.  Choose the options as per the screenshot and click “Apply”
        (General Purpose Serverless Max VCores: 4 Min VCores: 0.5 Apply

12) Go to “Networking” tab
    
    1.  Change “Connectivity method” to “Public endpoint”
    
    2.  Change “Allow Azure services and resources to access this
        server” to “YES” and “Add current client IP address” to “Yes”
        (See Screenshot below)

13) Click “Review + create” “Create”

![](.//media/image16.png)

![](.//media/image17.png)

![](.//media/image18.png)

**Then:**

![](.//media/image19.png)

#### Create Blob Storage Containers for the Data Warehouse Output and staging

1)  From Azure Storage Explorer right click on “Blob Containers” under
    your Storage Account

2)  Click “Create Blob Container”

![](.//media/image20.png)

3)  Provide “wwidatawarhouse” as container name

![](.//media/image21.png)

#### Create a container for WideWorldImporters Staging Input files

Repeat the steps above and create a container called “***wwistaging***”

#### Create a container for SmartFoods Staging Input files

Repeat the steps above and create a container called
“**smartfoodsstaging**”

> **<span class="underline">Note:</span>** If you follow all the steps
> you should now have a total of **3** Blob containers within your
> Storage Account as:
> 
> **wwidatawarehouse**
> 
> **wwistaging**
> 
> **smartfoodsstaging**
