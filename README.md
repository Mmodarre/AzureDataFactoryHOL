![](.//media/image1.png)

**ELT with Azure Data Factory**

**And**

**Mapping Data Flows**

Hands-on lab step-by-step

Feb 2020

Information in this document, including URL and other Internet Web site
references, is subject to change without notice. Unless otherwise noted,
the example companies, organizations, products, domain names, e-mail
addresses, logos, people, places, and events depicted herein are
fictitious, and no association with any real company, organization,
product, domain name, e-mail address, logo, person, place or event is
intended or should be inferred. Complying with all applicable copyright
laws is the responsibility of the user. Without limiting the rights
under copyright, no part of this document may be reproduced, stored in
or introduced into a retrieval system, or transmitted in any form or by
any means (electronic, mechanical, photocopying, recording, or
otherwise), or for any purpose, without the express written permission
of Microsoft Corporation.

Microsoft may have patents, patent applications, trademarks, copyrights,
or other intellectual property rights covering subject matter in this
document. Except as expressly provided in any written license agreement
from Microsoft, the furnishing of this document does not give you any
license to these patents, trademarks, copyrights, or other intellectual
property.

The names of manufacturers, products, or URLs are provided for
informational purposes only and Microsoft makes no representations and
warranties, either expressed, implied, or statutory, regarding these
manufacturers or the use of the products with any Microsoft
technologies. The inclusion of a manufacturer or product does not imply
endorsement of Microsoft of the manufacturer or product. Links may be
provided to third party sites. Such sites are not under the control of
Microsoft and Microsoft is not responsible for the contents of any
linked site or any link contained in a linked site, or any changes or
updates to such sites. Microsoft is not responsible for webcasting or
any other form of transmission received from any linked site. Microsoft
is providing these links to you only as a convenience, and the inclusion
of any link does not imply endorsement of Microsoft of the site or the
products contained therein.

© 2018 Microsoft Corporation. All rights reserved.

Microsoft and the trademarks listed at
<https://www.microsoft.com/en-us/legal/intellectualproperty/Trademarks/Usage/General.aspx>
are trademarks of the Microsoft group of companies. All other trademarks
are property of their respective owners.

# Contents

[Azure Data Factory hands-on lab 1](#azure-data-factory-hands-on-lab)

[Abstract and learning objectives 1](#abstract-and-learning-objectives)

[Overview 2](#overview)

[Solution architecture 2](#solution-architecture)

[Requirements 3](#requirements)

[Before the hands-on lab Exercise 4](#before-the-hands-on-lab-exercise)

[Task 1: Install Azure Storage Explorer
4](#install-azure-storage-explorer)

[Task 2: Clone the GitHub repository 6](#clone-the-github-repository)

[Task 3: Deploy Azure Resource Group 7](#deploy-azure-resource-group)

[Task 4: Deploy Azure Data Factory 8](#deploy-azure-data-factory)

[Task 5: Deploy an Azure Storage Account as below
9](#deploy-an-azure-storage-account-as-below)

[Task 6: Deploy Azure Key Vault 10](#deploy-azure-key-vault)

[Task 7: Deploy Azure SQL Database 13](#deploy-azure-sql-database)

[Task 8: Create Blob Storage Containers for the Data Warehouse Output
and staging
17](#create-blob-storage-containers-for-the-data-warehouse-output-and-staging)

[Task 9: Create a container for WideWorldImporters Staging Input files
18](#create-a-container-for-wideworldimporters-staging-input-files)

[Task 10: Create a container for SmartFoods Staging Input files
18](#create-a-container-for-smartfoods-staging-input-files)

[Linked Services, Datasets and Integration Runtimes:
19](#linked-services-datasets-and-integration-runtimes)

[Task 1: Create Azure Data Factory Integration Runtime
19](#create-azure-data-factory-integration-runtime)

[Task 2: Create a new ADF Key Vault Linked Service
22](#create-a-new-adf-key-vault-linked-service)

[Task 3: Add blob Storage credentials to AKV
29](#add-blob-storage-credentials-to-akv)

[Task 4: Add other credentials to Azure Key Vault
31](#add-other-credentials-to-azure-key-vault)

[Task 5: Create SFTP Linked Service 31](#create-sftp-linked-service)

[Task 6: Create datasets for WWI SFTP data
32](#create-datasets-for-wwi-sftp-data)

[Task 7: Create Azure Blob Storage Linked Service
37](#create-azure-blob-storage-linked-service)

[Task 8: Create Blob Storage Datasets for WWI Input data
40](#create-blob-storage-datasets-for-wwi-input-data)

[Task 9: Create Blob Storage Datasets for SmartFoods customer(JSON)
Input data
40](#create-blob-storage-datasets-for-smartfoods-customerjson-input-data)

[Task 10: Create Blob Storage Datasets for WWI Data Warehouse output
42](#create-blob-storage-datasets-for-wwi-data-warehouse-output)

[Task 11: Create a SQL Database Linked Service and Dataset
43](#create-a-sql-database-linked-service-and-dataset)

[Task 12: Create an HTTP Linked Service:
46](#create-an-http-linked-service)

[Task 13: Create a CSV Dataset on the HTTP Linked Service
47](#create-a-csv-dataset-on-the-http-linked-service)

[Task 14: Create a JSON Dataset on the HTTP Linked Service
49](#create-a-json-dataset-on-the-http-linked-service)

[Copy Activity, Parameters, Debug and Publishing:
51](#copy-activity-parameters-debug-and-publishing)

[Task 1: Create a pipeline and setup authentication to SmartFoods OAuth2
(Token based) API
51](#create-a-pipeline-and-setup-authentication-to-smartfoods-oauth2-token-based-api)

[Task 2: Store the access token in a variable
60](#store-the-access-token-in-a-variable)

[Task 3: Retrieve data from API and store in Blob Storage
62](#retrieve-data-from-api-and-store-in-blob-storage)

[Task 4: Test your pipeline 66](#test-your-pipeline)

[Task 5: Extend the pipeline with Lookup activity and ForEach Loops
67](#extend-the-pipeline-with-lookup-activity-and-foreach-loops)

[Task 6: Expand the pipeline using Lookup activity
72](#expand-the-pipeline-using-lookup-activity)

[Task 7: Test your pipeline 76](#test-your-pipeline-1)

[Task 8: How much did this cost? 77](#how-much-did-this-cost)

[Task 9: Clone and modify SmartFoods customer pipeline for
***transactions***
78](#clone-and-modify-smartfoods-customer-pipeline-for-transactions)

[ELT with Mapping Dataflows, SmartFood’s “Items(foods)” and Customer
dimensions
81](#elt-with-mapping-dataflows-smartfoods-itemsfoods-and-customer-dimensions)

[Task 1: Create a Parquet dataset to write SmartFoods DW Blob container
81](#create-a-parquet-dataset-to-write-smartfoods-dw-blob-container)

[Task 2: Create SQL Database Dataset 82](#create-sql-database-dataset)

[Task 3: Create Foods Dimension 83](#create-foods-dimension)

[Task 4: (Challenge Task) Create customer dimension
86](#challenge-task-create-customer-dimension)

[Task 5: Create SmartFoods Invoice fact tables
89](#create-smartfoods-invoice-fact-tables)

# Azure Data Factory hands-on lab

## Abstract and learning objectives 

In this workshop, you will deploy an End to End Azure ELT solution. This
workshop uses Azure Data Factory (and Mapping Dataflows) to perform
Extract Load Transformation (ELT) using Azure Blob storage, Azure SQL
DB. Azure DevOps repositories to perform source control over ADF
pipelines and Azure DevOps pipelines to deploy across multiple
environments including Dev, Test and Production.

By attending this workshop, you will better able to build a complete
Azure data factory ELT pipeline. In addition, you will learn to:

  - Deploy Azure Data Factory including an Integration Runtime.

  - Build Mapping Dataflows in ADF.

  - Create Blob Storage and Azure SQLDB Linked Services.

  - Create Azure Key Vault and Linked Services in ADF

  - Create ADF parameterized pipeline.

  - Perform code-free Spark ELT using Azure Data Factory Mapping
    Dataflows.

  - Source control ADF pipelines.

  - CI/CD ADF pipelines and your ELT code.

This hands-on lab is designed to provide exposure to many of Microsoft’s
transformative line of business applications built using Microsoft data
and advanced analytics. The goal is to show an end-to-end solution,
leveraging many of these technologies, but not necessarily doing work in
every component possible. The lab architecture is below and includes:

  - Azure Data Factory (ADF)

  - Azure Storage

  - Azure Data Factory Mapping Dataflows

  - Azure SQL Database

  - Azure Key vault

  - (optional) Azure DevOps

## Overview

WideWorldImporters (WWI) imports a wide range of products which then
resells to retailers and public directly. In an increasingly crowded
market, they are always looking for ways to differentiate themselves,
and provide added value to their customers.

They are looking to pilot a data warehouse to provide additional
information useful to their internal sales and marketing agents. They
want to enable their agents to perform AS-IS and AS-WAS analysis in
order to price the items more accurately and predict the product demand
at different times during the year.

Also to extend their physical presence WWI is extending their business
by and recently acquired a medium supermarket business called SmartFoods
which their differentiating factor is their emphasis on providing very
comprehensive information on food nutrients to customer in order for
them to make health wise decisions. SmartFoods run their own loyalty
program which customer can accumulate points on their purchases. WWI CIO
is hopping to use the loyalty program information and the food nutrients
database of SmartFoods to provide customers with a HealthSmart portal.
The portal will be showing aggregated information on customers important
food nutrients (Carbs, Saturated fats and etc.) to promote healthy and
SmartFood shopping.

In this hands-on lab, attendees will build an end-to-end solution to
build a data warehouse using data lake methodology.

## Solution architecture

Below is a diagram of the solution architecture you will build in this
lab. Please study this carefully so you understand the whole of the
solution as you are working on the various components.

![](.//media/image2.png)

**  
**

**Data sources:**

1.  SmartFoods Rest API:

<table>
<thead>
<tr class="header">
<th><strong>Type</strong></th>
<th>Rest API</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><strong>Authentication</strong></td>
<td>Oauth2</td>
</tr>
<tr class="even">
<td><strong>Data Endpoints</strong></td>
<td><ol type="1">
<li><p>Order line Transactions (CSV)</p></li>
<li><p>Customers (JSON)</p></li>
<li><p>Auth Token (JSON)</p></li>
</ol></td>
</tr>
<tr class="odd">
<td><strong>Frequency</strong></td>
<td>Daily</td>
</tr>
<tr class="even">
<td><strong>Documentation</strong></td>
<td><a href="https://github.com/Mmodarre/retailDataGeneratorAzureFunction">https://github.com/Mmodarre/retailDataGeneratorAzureFunction</a></td>
</tr>
</tbody>
</table>

2.  SmartFoods Items

<table>
<thead>
<tr class="header">
<th><strong>Type</strong></th>
<th>On premises Local file system</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><strong>Authentication</strong></td>
<td>NA</td>
</tr>
<tr class="even">
<td><strong>Data Endpoints</strong></td>
<td><ol type="1">
<li><p>Food (CSV)</p></li>
<li><p>Food-Nutrition (CSV)</p></li>
<li><p>Nutrition (CSV)</p></li>
</ol></td>
</tr>
<tr class="odd">
<td><strong>Frequency</strong></td>
<td>NA – One Off</td>
</tr>
<tr class="even">
<td><strong>Documentation</strong></td>
<td></td>
</tr>
</tbody>
</table>

3.  WWI OLTP

<table>
<thead>
<tr class="header">
<th><strong>Type</strong></th>
<th>SFTP</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><strong>Authentication</strong></td>
<td>Username/Password</td>
</tr>
<tr class="even">
<td><strong>Data Endpoints</strong></td>
<td><ol type="1">
<li><p>Orderline Transactions (Parquet)</p></li>
<li><p>Orders Transactions (Parquet)</p></li>
<li><p>Customers (Parquet)</p></li>
</ol></td>
</tr>
<tr class="odd">
<td><strong>Frequency</strong></td>
<td>Daily</td>
</tr>
<tr class="even">
<td><strong>Documentation</strong></td>
<td></td>
</tr>
</tbody>
</table>

## Requirements

1.  Microsoft Azure subscription Free Trial or pay-as-you-go (Credit
    Card) or MSDN subscription.

2.  MS Windows development Environment (Only a requirement for Azure
    Self Hosted IR – If you are using a Linux or Mac OS workstation you
    can achieve the same by running a Windows VM locally or in Azure)

3.  Azure Storage Explorer

## Before the hands-on lab Exercise

Duration: 20 minutes

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

![](.//media/image3.png)

![](.//media/image4.png)

![](.//media/image5.png)

#### Clone the GitHub repository

Clone this GitHub repository to your machine or download the source Zip
file of this repo

![](.//media/image6.png)

#### Deploy Azure Resource Group

1)  Search for Resource groups from the main search bar in Azure portal

![](.//media/image7.png)

2)  Click Add

![](.//media/image8.png)

1)  **For Resource group name: adf-\<your name\>-dev-rg for example:
    adf-john-dev-rg**

![](.//media/image9.png)

#### Deploy Azure Data Factory

1)  Similar to Task 0 search for “Azure Data Factory”

2)  Click Add

3)  **For Storage account name: adf-\<your name\>-dev-datafactory for
    example: adf-john-dev-datafactory**

4)  Deploy the Azure Data Factory in the region of your choice and for
    Resource Group choose the RG created in Task 0.

5)  **Uncheck “Enable GIT”**

6)  Click Create

![](.//media/image10.png)

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

> ![](.//media/image11.png)

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

![](.//media/image12.png)

![](.//media/image13.png)

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

![](.//media/image14.png)

1)  Search for SQL Database

2)  Click Add

3)  Provide subscription, resource group

4)  **For Database Name use: adf-\<your name\>-dev-sqldb for example:
    adf-john-dev-sqldb**

> ![](.//media/image15.png)
> 
> ![](.//media/image16.png)

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

![](.//media/image17.png)

![](.//media/image18.png)

![](.//media/image19.png)

**Then:**

![](.//media/image20.png)

#### Create Blob Storage Containers for the Data Warehouse Output and staging

1)  From Azure Storage Explorer right click on “Blob Containers” under
    your Storage Account

2)  Click “Create Blob Container”

![](.//media/image21.png)

3)  Provide “wwidatawarhouse” as container name

![](.//media/image22.png)

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

## Linked Services, Datasets and Integration Runtimes: 

Duration: 40 minutes

In this exercise, attendees will create multiple Azure data factory
pipelines and related component including ADF IR, LinkedServices and
Datasets.

#### Create Azure Data Factory Integration Runtime

**Azure Integration Runtime**

The Integration Runtime (IR) is the compute infrastructure used by Azure
Data Factory. Data Factory offers three types of Integration Runtime,
and you should choose the type that best serve the data integration
capabilities and network environment needs you are looking for. These
three types are:

**Azure**: In a serverless fashion runs in the cloud.

  - **Auto Resolve:** Azure finds the closest region depending on
    Source/Target Dataset to run this activity

  - **Fixed Region:** Developer setup the runtime in one of Azure
    regions and the IR always gets deployed in that region.

<!-- end list -->

  - **Self-hosted:** Developer installs on On-premises infrastructure
    (MS Windows only) or an Azure VM. SHIR is most useful for accessing
    resources within private networks (either in the Cloud Vnet or
    behind on-prem firewall) or for accessing “File” on file system.

  - **Azure-SSIS:** Special IR used for running SSIS packages in the
    cloud within ADF.

In this task we create a “Fixed Region Azure IR”.

1.  Open Azure Data Factory resource in Azure Portal

![](.//media/image23.png)

Click on Author and Monitor and it should be taking you to ADF UI like
below. This is the main ADF UI. On the left-hand side there is Author
and Monitor tabs. click on author tab to start building the first
pipeline.

> ![](.//media/image24.png)

2.  Create a new Integration run time in a specific Azure Region: go to
    Author tab Connections (at the bottom of the screen) click New

![](.//media/image25.png)

Select “Perform data movement and dispatch activities to external
computes”

![](.//media/image26.png)

Choose “Azure Public, Self-Hosted”

![](.//media/image27.png)

Give your new IR a name and Choose the preferred region

![](.//media/image28.png)

#### Create a new ADF Key Vault Linked Service

Process summary: For ADF to be able to access different services it will
require credentials and the recommended approach is to store the secrets
in Azure Key Vault and give ADF permissions to retrieve these
credentials at runtime.

To add an Azure Key Vault Linked Service to ADF apart from adding it in
ADF you also need to authorize ADF to access the secrets in KV as well.
So, after creating the linked service there is some further steps(Step E
and further) to be completed on Azure portal.

![](.//media/image29.png)

1)  Create a new Linked Service: go to Author tab Connections (at the
    bottom of the screen) click New

2)  Search for Key Vault and select Azure Key Vault

3)  Give you linked service a name, select subscription and the Key
    Vault created in previous steps

4)  Copy the managed identity object id and click on “**Edit Key
    Vault**” link

![](.//media/image30.png)

![](.//media/image31.png)

5)  On the key vault page (In Azure Portal) select access policies

![](.//media/image32.png)

![](.//media/image33.png)

![](.//media/image34.png)

6)  from secrets permissions drop down select “Get” and “List”

![](.//media/image35.png)

7)  Click on Select principle

![](.//media/image36.png)

8)  paste the “managed identity object id” which will then show your ADF
    and select it

9)  Finally click Add and you should see something like below

10) If all looks good click “Save”

> **Note:** Leave the browser tab of Azure Key Vault open as we will
> need it soon.

![](.//media/image37.png)

11) Go back to ADF where we left of and click on “Test Connection” if
    connection was successful click “Create” to create the linked
    Service.

![](.//media/image38.png)

#### Add blob Storage credentials to AKV

As mentioned before Azure Key Vault (AKV) is used to store all
credentials for services that ADF will connect to. This has multiple
advantages

1.  Security of storing sensitive information in credentials store which
    only the ADF service or Administrators can read from

2.  If Credentials need to be rotated ADF Linked Service will not need
    to be modified

3.  When we migrate the ADF pipeline from Dev to Test to production no
    change is necessary

<!-- end list -->

1)  From Azure portal go to your Storage account

![](.//media/image39.png)

2)  Copy the Connection string

3)  Go back to the browser tab you had AKV open

4)  Click on Secrets

5)  Generate/Import

> ![](.//media/image40.png)

6)  For name provide \<storage account name\>connectionstring

7)  For value paste the Storage account connection string

8)  Click “Create”

![](.//media/image41.png)

#### Add other credentials to Azure Key Vault

Follow the same steps in previous task and add the following credentials
to your Azure Key Vault:

| **Name**                  | **Value**                    |
| ------------------------- | ---------------------------- |
| SmartFoodsRestAPIUsername | adflab                       |
| SmartFoodsRestAPIPassword | Password.1\!                 |
| WWISftpPassword           | PROVIDED TO YOU IN CLASSROOM |

#### Create SFTP Linked Service

As mentioned in the solution architecture section the WWI input data is
extracted in Parquet format from the OLTP RDBMS and stored in an SFTP
server. So, in this step we create a LS to the SFTP server.

1.  Click new linked services

2.  Select SFTP

![](.//media/image42.png)

3.  Connect Via: \<NAME OF YOUR IR\>

4.  For name enter WWISftp

5.  Host: adflabsftp.westus2.cloudapp.azure.com

6.  Port: 22

7.  Disable SSH host key Validation

8.  Authentication type: Basic

9.  User name: sftpuser

10. Azure Key Vault
    
    1.  Secret name: WWISftpPassword

#### Create datasets for WWI SFTP data

In this step we create a parametrized dataset on SFTP Linked Service to
access the Parquet files on SFTP server.

1)  Click the plus sign on the left top hand of ADF and select Dataset.

![](.//media/image43.png)

2)  Select SFTP

3)  Select Parquet for format

4)  From Drop down select the SFTP linked service created in previous
    task

5)  For name provide “WWISftpParquet”

6)  Leave “Directory” and “File” Blank (We are going to parametrize
    these)

7)  Change “Import Schema to None”

8)  Click OK

![](.//media/image44.png)

![](.//media/image45.png)

9)  Go to parameters tab in your dataset and create three parameters as
    in the screenshot above

10) Go to connection tab and select the directory box once selected
    click on “Add dynamic Contents” or hit Alt+P

11) In Expression editor your list of parameters is shown at the bottom
    select folder and inspect the contents in the expression editor box
    and then click “Finish”

![](.//media/image46.png)

12) Repeat the same steps for file name but instead in the Expression
    Editor enter.

<!-- end list -->

    @{dataset().filename}.@{dataset().filetype}

> Hint: The above expression concatenates the two parameters with a ‘.’
> in between to make a full file name.
> 
> Hint2: Also, we could write the same expression as:
> @concat(dataset().filename,’.’,dataset().filetype)

![](.//media/image47.png)

1)  Click on preview data and fill in the parameters as:
    
    1.  **Folder**: WorldWideImporters/orderlines
    
    2.  **Filename**: orderlines\_2019-09-02
    
    3.  **Filetype**: parquet

![](.//media/image48.png)

If the data set and parameters are created correctly you should see
something like below:

![](.//media/image49.png)

This way our dataset can be re-used in different pipelines or the same
pipeline to access different files.

#### Create Azure Blob Storage Linked Service

A data factory can have one or more pipelines. A **pipeline** is a
logical grouping of **activities** that together perform a task.
a **dataset** is a named view of data that simply points or references
the data you want to use in your **activities** as inputs and outputs.

Before you create a dataset, you must create a **linked service** to
link your data store to the data factory. Linked services are much like
connection strings, which define the connection information needed for
Data Factory to connect to external resources.

Now in this task you will create a Linked Service to Azure Blob Storage.

2)  Click new in Linked Service window again

![](.//media/image50.png)

3)  Select Azure Blob Storage

4)  For name call it \<your storage account name\>john

5)  Select your IR

6)  Change Authentication Mechanism to Key Vault

7)  Select the AKV linked Service you created in the previous task

8)  For Secret name provide \<storage account name\>connectionstring

9)  Test connection and click “create”.

![](.//media/image51.png)

#### Create Blob Storage Datasets for WWI Input data

All the input data for WWI is sent in Parquet format from the source, so
this makes our life easier as we only need a single parametrized parquet
dataset for all different data domains. The data set will be created on
the Azure Blob linked service created in previous task.

The datasets are parametrized so the same Dataset can be used for
writing and reading different files and data domains (i.e. customers or
orders on any date). The only part of the dataset that gets hardcoded is
the container; the “folder path” and “file name” remains blank to be
parametrized.

1)  Click the plus sign on the left top hand of ADF and select Dataset.

![](.//media/image43.png)

2)  Select Blob

3)  Select Parquet for format

4)  From Drop down select the Azure Blob Storage linked service created
    in previous task

5)  For name provide “WWIStagingBlobParquet”

6)  Click “Browse” and select ***wwistaging*** container (created in
    previous steps)

7)  Leave “Directory” and “File” Blank (We are going to parametrize
    these)

8)  Change “Import Schema to None”

9)  Click OK

10) Parametrize the Dataset like the SFTP data set with following
    parameters:
    
    1.  folder
    
    2.  filename
    
    3.  filetype

#### Create Blob Storage Datasets for SmartFoods customer(JSON) Input data

SmartFoods data is available to an API. Customer data is in JSON format
and Transactions are in CSV format. As such our solution requires to
have a separate “dataset” for each data domain.

**- For Transactions CSV**

1)  Click the plus sign on the left top hand of ADF and select Dataset.

![](.//media/image43.png)

2)  Select Blob

3)  Select “Delimited Text” for format

4)  From Drop down select the Azure Blob Storage linked service created
    in previous task

5)  For name provide “**SmartFoodsDelimitedTextBlob**”

6)  Click “Browse” and select ***smartfoodsstaging*** container (created
    in previous steps)

7)  Leave “Directory” and “File” Blank (We are going to parametrize
    these)

8)  Change “Import Schema to None”

9)  Click OK

10) Parametrize the Dataset like the SFTP data set with following
    parameters:
    
    1.  folder
    
    2.  filename
    
    3.  filetype

<!-- end list -->

  - **For customer JSON**

<!-- end list -->

1)  Click the plus sign on the left top hand of ADF and select Dataset.

![](.//media/image43.png)

2)  Select Blob

3)  Select “JSON” for format

4)  From Drop down select the Azure Blob Storage linked service created
    in previous task

5)  For name provide “**SmartFoodsJsonBlob**”

6)  Click “Browse” and select ***smartfoodsstaging*** container (created
    in previous steps)

7)  Leave “Directory” and “File” Blank (We are going to parametrize
    these)

8)  Change “Import Schema to None”

9)  Click OK

10) Parametrize the Dataset like the SFTP data set with following
    parameters:
    
    1.  folder
    
    2.  filename
    
    3.  filetype

#### Create Blob Storage Datasets for WWI Data Warehouse output

We are planning to use Parquet file type for storing all output DW files
and as such for output only a single Parquet file format is enough.

> Note: A question here would be, why can’t we use the Parquet dataset
> created in previous step for WWI input data?\! The answer is:
> Technically we can but reusing the same objects in a solution comes
> with a comes with the extra complexity cost.
> 
> **Better Practices Note: How dynamic should the solution
> be<sup>1</sup>?**  
> It can be oh-so-tempting to want to build one solution to rule them
> all. (Especially if you love tech and problem-solving, like me.
> It’s fun figuring things out\!) But be mindful of how much time you
> spend on the solution itself. If you start spending more time figuring
> out how to make your solution work for all sources and all edge-cases,
> or if you start getting lost in your own framework… stop.
> 
> Your solution should be dynamic enough that you save time on
> development and maintenance, but not so dynamic that it becomes
> difficult to understand.
> 
> …don’t try to make a solution that is generic enough to
> solve everything :)
> 
> Your goal is to **deliver business value**. If you end up looking like
> this cat, spinning your wheels and working hard (and maybe having lots
> of fun) but without getting anywhere, you are probably
> over-engineering your solution.
> 
> Alright, now that we’ve got the warnings out the way… Let’s start by
> looking at parameters :)  
> 1: Reference:
> <https://www.cathrinewilhelmsen.net/2019/12/20/parameters-azure-data-factory/>

1)  Click the plus sign on the left top hand of ADF and select Dataset.

![](.//media/image43.png)

2)  Select Blob

3)  Select Parquet for format

4)  From Drop down select the Azure Blob Storage linked service created
    in previous task

5)  For name provide “WWIDataWarehouseBlobParquet”

6)  Click “Browse” and select **wwidatawarehouse** container (created in
    previous steps)

7)  Leave “Directory” and “File” Blank (We are going to parametrize
    these)

8)  Change “Import Schema to None”

9)  Click OK

10) Parametrize the Dataset like the SFTP data set with following
    parameters:
    
    1.  folder
    
    2.  filename
    
    3.  filetype

#### Create a SQL Database Linked Service and Dataset

1)  Create a Linked service for SQL Database by following the similar
    procedure as the one you did in task 5 except for Linked Service
    Type select “Azure SQL Database”

> **Note1:** Like Blob Storage, use Azure Key Vault
> 
> **Note2:** From Azure portal/SQL Database Get the connection string
> and store in AKV (Screenshots below)
> 
> **Note3:** In the connection string make sure you replace the
> “{your\_password”} with your SQL Database password you chosen in
> Pre-lab setup.

![](.//media/image52.png)

![](.//media/image53.png)

Create a Dataset similar to Blob storage – User Screenshots as a guide

![](.//media/image54.png)

![](.//media/image55.png)

![](.//media/image56.png)

#### Create an HTTP Linked Service:

Name: SmartFoodsApiLinkedService

Base URL: <https://smartfoods.azurewebsites.net/api/>

> Note: Use Screenshot as a guide for other options.

![](.//media/image57.png)

#### Create a CSV Dataset on the HTTP Linked Service

ADF allows us to create datasets of various format on top of an HTTP
Linked service to receive data over an HTTP connection from another
source in various formats such as delimited text.

1)  Start creating a dataset

2)  Select HTTP as type

3)  Select Delimited Text as format

4)  Name: SmartFoodsTransactionApiCsv

5)  Leave other options **BLANK** as it is and click “Ok”

![](.//media/image58.png)![](.//media/image59.png)

![](.//media/image60.png)

6)  Create a parameter as “authCode” under dataset

> “Note: We are going to use this parameter to pass the Authentication
> token to the API Service on calling the service.”

![](.//media/image61.png)

7)  Under Connection tab and for “Relative URL” click go to “dynamic
    content editor and provide:

Here we are creating an HTTP dataset which the query parameters for it
is parametrized so we can change them at runtime and retrieve different
data portions from the API

    smartfoods?code=@{dataset().authCode}

![](.//media/image62.png)

#### Create a JSON Dataset on the HTTP Linked Service

SmartFoods API provides the customer data in JSON format as such we need
to create a second dataset on top of the same HTTP Linked service but in
JSON Format

Follow the same steps as in previous task, except for data format select
**JSON**. For name provide “**SmartFoodsCustomerApiJson”**.

Screenshots below for guidance:

![](.//media/image63.png)

![](.//media/image64.png)

## Copy Activity, Parameters, Debug and Publishing:

In this exercise you create a pipeline to ingest data from SmartFoods
web services. Primary learning objectives:

1.  Accessing an OAuth2 API

2.  Setting up copy activity

3.  Setting up parametrized pipelines

4.  Using parametrized datasets in activities

5.  Creating, setting and reading variables

6.  Using Web Activity

#### Create a pipeline and setup authentication to SmartFoods OAuth2 (Token based) API

In order to access the SmartFoods API we need to first call the endpoint
with username and password as the HTTP POST body to get a time-based
OAuth token. This token then can be used in further activities to
authenticate to the API and retrieve data. The best way to perform this
is to use a “Web” activity within data factory.

The easy way to do this is by adding a web activity to the canvas and
hardcoding the credentials in it. but this is a serious security breach
and as explained before every secret used in ADF should be stored in
AKV.

As a result, we will require to add two more “Web” activities before
this web activity to first retrieve the credentials from AKV at runtime
and pass to the third (main) Web Activity to authenticate to the API and
retrieve the token.

1.  Click on the plus sing and click on Pipeline to add a new pipeline

2.  Rename the pipeline under “general” tab to
    **SmartFoodCustomerApiToBlob**

![](.//media/image65.png)

3.  From the parameters tab create a pipeline parameter and call it
    “date”

![](.//media/image66.png)

![](.//media/image67.png)

4.  From the activities bar under “General” drag a “Web” activity to the
    canvas

![](.//media/image68.png)

5.  Rename the activity to AKVUsername

6.  Go to your Azure Key Vault in **Azure Portal** and from secrets
    select “SmartFoodsApiUsername” (You have created this secret
    previously)

![](.//media/image69.png)

7.  Select the current version

![](.//media/image70.png)

8.  Copy the “Secret Identifier”

![](.//media/image71.png)

9.  Paste it in a code editor or Notepad

10. At the end of the URI add

<!-- end list -->

    ?api-version=7.0

So, it will look something like this:

    https://adf-mehdi-dev-kv.vault.azure.net/secrets/SmartFoodsApiUsername/a35670dbbf19471eac4f8390e3c31882?api-version=7.0

11. Repeat the same steps for “SmartFoodsApiPassword” secret

12. Now back in ADF under the web activity and
    
    1.  paste the URI with api version added to it in the URL box
    
    2.  Change Authentication to MSI (This indicates we have give this
        instance of ADF access to out AKV so no other auth is necessary)
    
    3.  For resource enter:

<!-- end list -->

    https://vault.azure.net

![](.//media/image72.png)

13. Go back to General tab and tick “Secure Output”

14. Now click “Debug” to test the activity

![](.//media/image73.png)

15. Under output tab click the output button to see the activity output

![](.//media/image74.png)

The output should be in form of:

    { "SecureOutput": "**********" }

This is the effect of setting secure output setting

> **Try it:** Try removing the “secure output” tick and re-run debug and
> see how the output will differ.

16. **Repeat the same steps and add “AKVPassword” Web activity.**

![](.//media/image75.png)

17. Add another “Web activity” to the canvas. Rename it to
    “SmartFoodsLogin” and attach the success connectors from the
    AKVUsername and AKVPassword to it as below:

![](.//media/image76.png)

18. Setup the web activity as below

URL:

    https://smartfoods.azurewebsites.net/api/SmartFoodsOauth

Method: POST

Body:

    @json(concat('{"username":"',activity('AKVUsername').output.value,'","password":"',activity('AKVPassword').output.value,'"'))

> Note1: you need to click on “Add Dynamic Content” to enter the
> “Expression Editor” before pasting the value
> 
> Note2: Expression explanation: For HTTP POST body we need to compose a
> JSON document and pass the username and password attribute to it. So
> we use the “Concat” function to add the static and dynamic parts
> together to compose a JSON document and then pass it to the “JSON”
> function to format it correctly as a JSON. The resulting JSON will
> look like below.

    {
    	“Username”: “<value coming from AKVUsername web activity>”,
    	“password”: “<value coming from AKVPassword web activity>”
    }

![](.//media/image77.png)

19. Under general tab tick the “Secure Output” to make sure the tokens
    are not getting revealed in ADF logs.

20. Debug you pipeline to make sure all activities are successful.

#### Store the access token in a variable

Now we have the token to access the API we need to store the token in a
variable to be passed to our copy activity in order to access the API
securely.

1.  Under variables in the pipeline create a new variable “token”

> ![](.//media/image78.png)

2.  Add a “Set Variable” activity to canvas

![](.//media/image79.png)

1.  add “Set variable” activity.

2.  connect to “SmartFoodsLogin” using “Success”.

3.  click on variables.

4.  For “Name” select “token”.

5.  go to Expression Editor for “Value” and set it to:

<!-- end list -->

    @activity('SmartFoodsLogin').output.token

6.  Rename the activity to “SetAccessToken”.

7.  (Optional) Debug to test your pipeline.

#### Retrieve data from API and store in Blob Storage

In Azure Data Factory, you can use the **“Copy”** activity to copy data
among data stores located on-premises and in the cloud. Also Copy
activity allows us to change the data format through the copy process.
For example, here we receive the data in JSON format but store as CSV.
After you copy the data, you can use other activities to further
transform and analyze it.

1.  First create a pipeline parameter called “date”

![](.//media/image80.png)

2.  Drag a copy activity to canvas and connect to “SetAccessToken”
    activity

3.  Rename it to “SmartFoodsCustomersToBlob”

![](.//media/image81.png)

4.  Under source
    
    1.  Source dataset: “**SmartFoodsCustomerApiJson**”
    
    2.  For “authCode” parameter go to Expression editor and select the
        “token” variable

![](.//media/image81.png)

3.  Request method: Post

4.  Request body: click on expression editor and enter

<!-- end list -->

    @{json(concat('{"trans_date": "',pipeline().parameters.date,'","dataDomain" : "customers"}'))}

> **Expression explanation**: We are composing a JSON for REST request
> body with two attributes trans\_date which we use the pipeline
> parameter as value and dataDomain hard coded to “customers”

![](.//media/image82.png)

5.  For Sink
    
    1.  Sink dataset: “SmartFoodsDelimitedTextBlob”
    
    2.  Dataset parameters:
        
          - Folder: “customers”
        
          - File: Enter Expression editor and enter

<!-- end list -->

    smartfoods_customers_@{replace(pipeline().parameters.date,'-','')}

  - Filetype: “csv”

![](.//media/image83.png)

#### Test your pipeline

1.  Click “Debug”

2.  Provide 2020-02-10 as the value for “date” parameter

3.  Go to Azure Storage Explorer -\> find the respective storage
    account, container and directory -\> Locate the
    “smartfoods\_customers\_20200201.csv” file and open it to make
    sure the data is copied correctly.

![](.//media/image84.png)

#### Extend the pipeline with Lookup activity and ForEach Loops

The current pipeline works fine for daily loading of the data (single
day per run) but for initial loading of the data we need to load
multiple dates automatically.

**Solution Summary:** Azure Data Factory provides multiple ways for
achieving this goal. One way is to modify the existing pipeline and add
the ForEach loop to the same pipeline. The downside of this approach
though is we will then have separate pipelines for daily and initial
load and that is against the reusability better practice. Instead we
keep the existing pipeline and create a second pipeline which executes
this pipeline within a loop.

A *ForEach* loop in Azure Data Factory (like any programming language)
requires an *array* of items to iterate over. In our case we need an
array of all the dates that needs to be loaded from the API. To better
understand the mechanics of loops and arrays in ADF, initially we
manually create an array and pass it to *ForEach* loop. In the next task
we modify the pipeline and load the array from a text file using
*Lookup* activity.

1.  Create a new pipeline and rename it to
    **InitialLoadSmartFoodCustomerApiToBlob**

2.  Create a variable “dates” of type “Array” and provide the below as
    “DEFAULT VALUE”

<!-- end list -->

    ["2020-01-01","2020-01-02","2020-01-03","2020-01-04"]

> Note: This is the hardcoded list of dates that the ForEach loop will
> iterate through and pass our ‘SmartFoodCustomerApiToBlob’ pipeline

![](.//media/image85.png)

3.  Drag a “*ForEach”* loop activity to the canvas and rename it to
    ***“LoopSmartFoodsDates”***

4.  Under activities click the pencil icon to enter the “ForEach” loop
    canvas.

![](.//media/image86.png)

5.  Drag a ***“Execute Pipeline”*** activity on the ForEach loop canvas.

![](.//media/image87.png)

6.  Rename it to ***RunSmartFoodCustomerApiToBlob***

7.  Under “Settings”
    
    1.  “Invoked Pipeline” select “SmartFoodCustomerApiToBlob” pipeline
        that you created previously.
    
    2.  Expand the “Advanced” part and click on “Auto-fill parameters.

![](.//media/image88.png)

3.  For ***“date”*** parameter, from the expression editor select the
    “LoopSmartFoodsDates” under ForEach iterator

![](.//media/image89.png)

> Note: To go back to the main canvas (from the Execute pipeline canvas)
> use the hyperlink on top of the canvas.

![](.//media/image90.png)

#### Expand the pipeline using Lookup activity

Now that we have a pipeline that can iterate over an array of dates and
invoke the main pipeline we want to expand it further with a
***Lookup*** activity to read the list of dates from a text file (it can
be a SQL database as well) and perform the loading automatically.

This is how our pipeline looks like right now:

![](.//media/image91.png)

First, we need to place the file with list of dates on Azure Blob
Storage so ADF can access it.

1.  On your computer browse to the location that you cloned the GitHub
    repo. Under Data/SmartFoods/ there is a file ***dates.csv***. Open
    this file and inspect the contents.

2.  Open Azure Storage Explorer and go to *smartfoodsstaging* container.
    Create a new folder called *ref\_data* and upload the *dates.csv*
    file there.

![](.//media/image92.png)

Now lets start developing the pipeline

1.  Drag a ***Lookup*** activity on the canvas and rename it to
    ***LookupDates***

2.  Connect from **Success** of the Lookup activity to *ForEach*
    activity.

3.  Under Settings
    
    1.  Source dataset: SmartFoodsDelimitedTextBlob
    
    2.  Folder: *ref\_data*
    
    3.  File: *dates*
    
    4.  Filetype: *csv*
    
    5.  ***<span class="underline">Untick</span>** First row only*
    
    6.  Click Preview data to make sure it is working as expected.

![](.//media/image93.png)

![](.//media/image94.png)

4.  Click on the ForEach under *Settings*
    
    1.  For *Items* go to Expression Editor and change it to the below
        to receive it from the *Lookup* activity instead of the
        hard-coded variable:

<!-- end list -->

    @activity('LookupDates').output.value

![](.//media/image95.png)

5.  Double click on the ForEach loop to open its canvas and then click
    on *Execute Pipeline* activity. Under *Settings:*
    
    1.  For *date* change the value to below:

<!-- end list -->

    @item().date

![](.//media/image96.png)

#### Test your pipeline

1.  Click *Debug* to start the pipeline

2.  Inspect the output

![](.//media/image97.png)

3.  Once finished successfully go to *Azure Storage Explorer* to make
    sure all files in the dates.csv are created.

![](.//media/image98.png)

#### How much did this cost?

Once a debug run is finished, you can examine the “debug run
consumption” to measure how much resource the pipeline you designed is
consuming.

> **Note:** The important parts are number of “activity runs” and
> consumption of “DIU-hour” of “Data movement activities”
> 
> **Important Note:** Since we used the **Execute Pipeline,** there is a
> cost for both the outer pipeline and inner pipeline. The cost showing
> here is only for the outer pipeline. If you’d like to see the resource
> consumption of inner pipelines click on output and go to the actual
> pipeline run of the inner pipeline.

![](.//media/image99.png)

#### Clone and modify SmartFoods customer pipeline for ***<span class="underline">transactions</span>***

A great feature of ADF is the ability to clone different objects, and if
the underlying objects are sufficiently with minimal modification the
new pipeline can perform a similar task on a different set of data.

In this task we take advantage of this feature and replicate the
*SmartFoodCustomerApiToBlob* pipeline and modify it to load SmartFoods
transactions.

1.  From the left hand list of pipelines click on the three dots next to
    *SmartFoodCustomerApiToBlob* pipeline and select ***Clone***.

![](.//media/image100.png)

2.  Click on the newly created pipeline (clone creates a pipeline with
    the same name and \_copy1 extension) and rename it to
    ***SmartFoodTransactionApiToBlob***

3.  Click on the *copy* activity and rename it to
    ***SmartFoodsTransactionsToBlob***

4.  Under Source:
    
    1.  Source dataset: SmartFoodsTransactionsToBlob
    
    2.  authCode:

<!-- end list -->

    @variables('token')

3.  Request Method: Post

4.  Request Body:

<!-- end list -->

    @{json(concat('{"trans_date": "',pipeline().parameters.date,'","dataDomain" : "transactions"}'))}

5.  Under Sink:
    
    1.  Folder: transaction
    
    2.  File:

<!-- end list -->

    smartfoods_transactions_@{replace(pipeline().parameters.date,'-','')}

3.  File Type: csv

<!-- end list -->

6.  Now Debug to test your pipeline.

#### Add ‘Transaction’ pipeline to the ‘InitialLoad’ pipeline

Now that we have a working single day ‘transactions’ pipeline we need to
add it to initial load pipeline so both feeds from SmartFoods source
systems can be loaded from a single pipeline.

1.  Open the *‘**InitialLoadSmartFoodCustomerApiToBlob’*** pipeline and
    rename it to ‘***InitialLoadSmartFoodAllfeedsApiToBlob***’.

2.  Double click on the ‘ForEach’ loop activity to enter its canvas.

3.  Right click on ‘Execute pipeline’ activity and click copy

4.  Right click anywhere on the canvas and paste it.

![](.//media/image101.png)

![](.//media/image102.png)

5.  Rename the new activity to ***‘RunSmartFoodTransactionApiToBlob’***

![](.//media/image103.png)

6.  Under settings:
    
    1.  Invoked pipeline: *SmartFoodTransactionApiToBlob*
    
    2.  Date:

<!-- end list -->

    @item().date

7.  Debug to test your pipeline and once completed successfully inspect
    Azure Blob Storage to make sure all files are copies correctly.

#### Publishing 

Azure data factory provides two ways of saving changes: ADF mode and
Source Control mode. Since we have not yet setup source control our ADF
instance is saving in ADF mode. In Data Factory mode changes are not
permanently save until you click the publish button. So, if we close the
browser window right now all the progress we made will be discarded.
Although ADF mode is not recommended for real world environments, if you
are using Data Factory mode it is better practice to frequently
‘Validate’ and ‘Publish’ your changes save your work.

![](.//media/image104.png)

#### Load WWI Data

Now that we have setup pipelines to load SmartFoods data from API we
need to repeat the process and create pipelines to load *customers,
Orders* and *Orderlines* data from WWI STFP.

**Customers:**

1.  Create a new pipeline
    
    1.  Name: ‘*WWICustomersSftpToBlob’*

2.  Create a new parameter called *‘date’*

3.  Drag a ‘Copy’ activity on the canvas
    
    1.  Name: *‘CopyWWICustomerSFTPtoBlob’*

4.  Setup ‘Copy’ activity source
    
    1.  **source dataset**: *WWISftpParquet*
    
    2.  **folder**: *WorldWideImporters/customers*
    
    3.  **Filename**: *customers\_@{pipeline().parameters.date}*
    
    4.  **Filetype**: *parquet*

5.  Setup ‘Copy’ activity source
    
    1.  **source dataset**: *WWISftpParquet*
    
    2.  **folder**: *customer*
    
    3.  **Filename**: *customers\_@{pipeline().parameters.date}*
    
    4.  **Filetype**: *parquet*

6.  Debug the pipeline with ‘date’ parameter as
    *<span class="underline">‘2019-02-01’</span>*

7.  Inspect Blob Storage to make sure the file has been created.

> **Note1**: With Parquet files unlike delimited text files, you cannot
> open in with a text editor to inspect the content\!
> 
> **Note2**: Take advantage of pipeline cloning feature to save time and
> reduce error.

**Orders:**

1.  Create a new pipeline
    
    1.  Name: ‘*WWICustomersSftpToBlob’*

2.  Create a new parameter called *‘date’*

3.  Drag a ‘Copy’ activity on the canvas
    
    1.  Name: *‘CopyWWIOrderSFTPtoBlob’*

4.  Setup ‘Copy’ activity source
    
    1.  **source dataset**: *WWISftpParquet*
    
    2.  **folder**: *WorldWideImporters/orders*
    
    3.  **Filename**: *orders\_@{pipeline().parameters.date}*
    
    4.  **Filetype**: *parquet*

5.  Setup ‘Copy’ activity source
    
    1.  **source dataset**: *WWISftpParquet*
    
    2.  **folder**: *order*
    
    3.  **Filename**: *orders\_@{pipeline().parameters.date}*
    
    4.  **Filetype**: *parquet*

6.  Debug the pipeline with ‘date’ parameter as
    *<span class="underline">‘2019-02-01’</span>*

7.  Inspect Blob Storage to make sure the file has been created.

**OrderLines:**

1.  Create a new pipeline
    
    1.  Name: ‘*WWIOrderlinesSftpToBlob’*

2.  Create a new parameter called *‘date’*

3.  Drag a ‘Copy’ activity on the canvas
    
    1.  Name: *‘CopyWWIOrderlineSFTPtoBlob’*

4.  Setup ‘Copy’ activity source
    
    1.  **source dataset**: *WWISftpParquet*
    
    2.  **folder**: *WorldWideImporters/orderlines*
    
    3.  **Filename**: *orderlines\_@{pipeline().parameters.date}*
    
    4.  **Filetype**: *parquet*

5.  Setup ‘Copy’ activity source
    
    1.  **source dataset**: *WWISftpParquet*
    
    2.  **folder**: *orderline*
    
    3.  **Filename**: *orderlines\_@{pipeline().parameters.date}*
    
    4.  **Filetype**: *parquet*

6.  Debug the pipeline with ‘date’ parameter as
    *<span class="underline">‘2019-02-01’</span>*

7.  Inspect Blob Storage to make sure the file has been created.

> **Note3:** Another way of achieving the same result was to use 3 copy
> activities within a single pipeline. They are both valid methods. The
> only important thing is consistency. You created two separate
> pipelines for SmartFoods, so it is consistent to create three separate
> pipelines for WWI.

#### Setup Initial Load for WWI data

Now that we have individual pipelines for WWI source systems, we need to
setup an Initial Load pipeline similar to the one you created for
SmartFoods to iterate over an array and automatically load the data. For
SmartFoods, since we were accessing an API we needed to provide the list
of dates but when access a file system like SFTP (or Azure Blob storage,
local file system or etc.) we can use another type of activity called
‘***Get Metadata***’. ‘Get Metadata’ activity can perform a lookup
(list) of a file system and retrieve the list of files available on it.
In summary the pipeline will look like this:

*Get Metadata(List of files) -\> ForEach loop \[ Execute pipeline \[
Copy pipeline\] \]*

But if you inspect the files available on the SFTP server you’ll notice
not all files on the server are correct files to be loaded. Hence our
pipeline needs to have another type of activity, called ‘*Filter’*
activity, to filter out incorrect files. So, the pipeline will look like
this:

*Get Metadata(List of files) -\> Filter(filter files not matching the
expected patter) -\> ForEach loop \[ Execute pipeline \[ Copy pipeline\]
\]*

![](.//media/image105.png)

1.  Create a new pipeline and rename it to
    ‘*InitialLoadWWICustomerSftpToBlob’*

2.  Drag a ‘Get Metadata’ activity to the canvas and rename it to
    *‘GetWWICustomerFileListSFTP’*

3.  Under Dataset
    
    1.  Dateset: WWISftpParquet
    
    2.  Folder: WorldWideImporters/customers
    
    3.  File: \*
    
    4.  Filetype: parquet
    
    5.  Field list -\> New -\> Child Items

![](.//media/image106.png)

4.  Click Debug to inspect the output of the activity

> Output format of the Get Metadata activity: Output of Get Metadata
> activity is a **JSON** document. At the root of the document is
> **‘childitems’** and for <span class="underline">each file</span>
> there is a sub-document with **‘name’** and **‘type’** attributes.

![](.//media/image107.png)

![](.//media/image108.png)

5.  Drag a filter activity and rename it to
    *‘FilterWWISFTPCustomerFileNames’* and connect it to get metadata
    with success connector.

![](.//media/image109.png)

6.  Under Settings of ‘Filter’ activity

**Items**

> items: Accept an array of items that we want the filter activity to
> apply to. So, in this case it is the root document ‘childitems’

    @activity('GetWWICustomerFileListSFTP').output.childitems

**Condition**

> Condition: Requires a logical expression to filter the input on and
> since the array has already been passed in ‘Items’ field we need to
> use the @item in our expression

    @not(or(contains(item().name,'testing'),contains(item().name,'old')))

This expression is checking if the ‘name’ attribute with in the item has
“*old*” **<span class="underline">or</span>** “*testing*” words in it
and then uses ‘or’ function to reverse it.

![](.//media/image110.png)

7.  Debug and inspect the ‘filter’ activity output.

> Output of ‘filter’ activity: The output of filter activity is again a
> JSON document with ‘Output’ document being the most important part.
> Within that there is an ‘ItemsCount’ and ‘FilterItemsCount’ and an
> array called ‘Value’

![](.//media/image111.png)

There were 5 items with ‘testing’ and ‘old’ words in the file name that
the filter activity removed from the results.

8.  drag ‘ForEach’ loop activity and connect to success of ‘filter’
    activity and rename it to *‘LoopWWISftpCustomerFiles’*
    
    1.  Under ‘settings’ -\> Items:

<!-- end list -->

    @activity('FilterWWISFTPCustomerFileNames').output.Value

![](.//media/image112.png)

![](.//media/image113.png)

9.  Rename ‘Execute Pipeline’ to *‘ExecuteWWICustomersSftpToBlob’*

10. Add and ‘Execute Pipeline’ activity to ‘ForEach’ loop
    
    1.  Invoked pipeline: *WWICustomersSftpToBlob*
    
    2.  Parameters -\> date :

<!-- end list -->

    @substring(item().name,add(indexof(item().name,'_'),1),10)

> **Expression explanation:** Remember that the pipeline we are invoking
> is expecting a ‘date’ parameter but the ‘Get Metadata’ and ‘Filter’
> activities are passing on file names in the form of
> ‘Customer\_\<date\>.parquet’. In order to retrieve the date part
> from the file name we need to use a ‘Substring’ function and within
> that we pass the original string, the starting position and then
> number of characters. To find the starting position we use the
> ‘indexof’ function and add 1 to it. Here is the same expression in
> better formatting:

    @substring(
    		item().name,
    		add(
    			indexof(item().name,'_'),
    			1
    		),
    		10)

11. Finally debug your pipeline and confirm that it copies all 54 files
    successfully.

12. Publish your changes.

#### Create Initial load pipelines for WWI ‘Orders’ and ‘Orderlines’

Now that we created the pipelines for loading all customer files from
SFTP, we need to create similar pipelines for WWI Orders and Orderlines
feeds.

Option 1: Clone the WWI Customer Initial load pipeline and modify it for
orders and orderlines.

Option 2(Further practice): Create pipelines from scratch following the
same instructions as above.

> Once you finish this task this is the pipelines you should have
> created in your ADF

![](.//media/image114.png)

## Part 1 Learning Summary:

**Congratulations\!** You have reached the end of first part of Azure
Data Factory Hands-On Lab. Here is a summary of all learning objectives
that was covered so far.

1.  Creating and renaming pipelines.

2.  Adding activities and chaining them.

3.  Look up activity using a Text file.

4.  Web activity.

5.  Accessing Oauth2 APIs using HTTP activity (composing request body
    JSON).

6.  Linked Services and setting them up using Azure Key Vault.

7.  Datasets and parameterization.

8.  Variables and using Set Variable activity.

9.  Pipeline and Dataset parameters.

10. Copy activity (Source and Sink Dataset).

11. ForEach loop activity.

12. Execute pipeline activity.

13. Get Metadata activity for listing files.

14. Filter activity.

15. Cloning pipelines.

16. ADF publishing (saving changes).

## ELT with Mapping Dataflows, SmartFood’s “Items(foods)” and Customer dimensions

Data Flow is a new feature of Azure Data Factory that allows you to
build data transformations in a visual user interface.

In the Blob container we copied for SmartFoods there are multiple CSV
files which represents SmartFood’s reference data for the transactions
that comes through the HTTP API.

#### Create a Parquet dataset to write SmartFoods DW Blob container

Similar to the task 6 in Exercise 2 create a **Parquet** Dataset on
“wwidatawarhouse” container (we created previously) and make sure you
parametrized the “file” and “directory” fields as before.

![](.//media/image115.png)

![](.//media/image116.png)

#### Create SQL Database Dataset

Create a SQL Database Dataset using the Linked Service created
previously and parametrize the schema name and table name as below:

![](.//media/image117.png)Pre-Task C: Create and Schema in your SQL DB

Either using Query Editor in Azure Portal or using SSMS connect to your
Azure SQL DB and create and schema for SmartFoods and a table for items

> **Note:** You may need to add your Client IP Address to your SQL DB
> through “Set Firewall” page.

    CREATE SCHEMA smartfoods;
    GO
    
    CREATE TABLE [smartfoods].[item](
    	[ItemKey] [bigint] NULL,
    	[SourceSKUCode] [int] NULL,
    	[ItemDescription] [nvarchar](max) NULL,
    	[ItemFoodGroup] [nvarchar](max) NULL,
    	[RecInsertDt] [date] NULL
    );
    GO

#### Create Foods Dimension

In the SmartFoods Blob container that we copied previously there was a
file named “food.csv” which contains the foods (transaction items)
reference data.

We would like to create a dimension table for this data source as below:

|         |                 |                   |                 |             |
| ------- | --------------- | ----------------- | --------------- | ----------- |
| ItemKey | SourceSKUCode\* | ItemDescription\* | ItemFoodGroup\* | RecInsertDt |

> Note: The columns marked with \* exists within the data source but the
> rest needs to be generated by the ELT process.

1.  Create a mapping Dataflow by clicking on new Data flow button

![](.//media/image118.png)

2.  At the top of the page turn on the “data flow debug”

![](.//media/image119.png)

3.  Click “Add Source” on canvas

4.  Select the SmartFood Blob storage CSV dataset (This dataset was
    previously used as a sink for HTTP copy activity)

5.  Click “Debug Settings”

6.  Under parameters provide
    
      - “file” = food
    
      - “folder” = /
    
      - “fileType” = csv

7.  Go to “projection tab”
    
    1.  click “import Schema
    
    2.  **Change the data type for “sku” column to “integer”**

8.  Now go to “Data Preview” tab and refresh it to get a preview of the
    dataset

9.  Add a derived column transformation by clicking the plus sing on the
    bottom right hand of the source transformation

![](.//media/image120.png)

![](.//media/image121.png)

10. For Column name use “RecInsertDt” and go into expression editor and
    find “currentDate()

![](.//media/image122.png)

> *Note: Inside the expression editor click the “Refresh” button to get
> the result of the expression instantly*

11. Next add a “surrogate key” transformation and configure it as below:

![](.//media/image123.png)

12. Add a “Select” transformation and configure it as below. (Pay
    attention that we are renaming and re-ordering columns\!)

![](.//media/image124.png)

13. Add a “Sink” transformation and select the SQL DB Dataset you
    created in the pre-tasks as the sink dataset.

14. Set the settings for the sink transformation as:

![](.//media/image125.png)

> Note: For brevity in this exercise we are setting up our pipeline to
> truncate the table on every load but in real world scenarios we
> commonly do not do this\!

The finale Data flow:

![](.//media/image126.png)

15. Create a pipeline place
    
    1.  place a mapping dataflow activity on canvas
    
    2.  select your DF

16. Debug the pipeline (You will need to provide the parameters)

#### (Challenge Task) Create customer dimension

In the SmartFoods Blob container that we copied previously there was a
file named “customer.csv” which contains the customers’ reference data.

We would like to create a dimension table for this data source as below:

|             |            |           |          |      |       |              |             |     |             |
| ----------- | ---------- | --------- | -------- | ---- | ----- | ------------ | ----------- | --- | ----------- |
| CustomerKey | LoyaltyNum | Firstname | Lastname | City | State | EmailAddress | MemberSince | Dob | RecInsertDt |

> Note 1: The source is providing “name” field, which is full name, but
> we need to separate first name and last name
> 
> Note 2: We know some of the email addresses of customers are NOT the
> right format (<abc@xyz.com>) and we need to replace these with NULL
> instead

**Optional Extra challenge:** WWI also likes to calculate the age of the
customer as well and store in “Age” column can you used Mapping Data
flows Expression Language to calculate it?

**<span class="underline">Table DDL:</span>**

    CREATE TABLE [smartfoods].[customer](
    	[CustomerKey] [bigint] NULL,
    	[LoyaltyNum] [nvarchar](max) NULL,
    	[FirstName] [nvarchar](max) NULL,
    	[LastName] [nvarchar](max) NULL,
    	[City] [nvarchar](max) NULL,
    	[State] [nvarchar](max) NULL,
    	[Email] [nvarchar](max) NULL,
    	[MemberSince] [date] NULL,
    	[Dob] [date] NULL,
    	[Age] [int] NULL,
    	[RecInsertDt] [date] NULL
    ) ;
    GO

**<span class="underline">Final Data Flow:</span>**

![](.//media/image127.png)

**If you are stuck or want to double check your answer the solution for
Expression Language and Select transformation is in the next page.  
**

**<span class="underline">Derived column expressions solution:</span>**

![](.//media/image128.png)

**<span class="underline">Select transformation:</span>**

![](.//media/image129.png)

#### Create SmartFoods Invoice fact tables

The Data that we retrieved in the previous exercise from SmartFoods
Transaction API seems to be in an uncommon format for invoices. Usually
invoice data has an invoice header and an invoice item lines but for the
case of SmartFoods the API is only capable of providing the data in form
of line items with repeated invoice header information.

![](.//media/image130.png)

The requirement is to create two separate tables in following form:

Invoice

|               |             |       |             |              |     |              |             |
| ------------- | ----------- | ----- | ----------- | ------------ | --- | ------------ | ----------- |
| InvoiceNumber | CustomerKey | Store | InvoiceDtts | InvoiceTotal | Gst | NumLineItems | RecInsertDt |

InvoiceLine

|               |         |                 |           |     |     |             |
| ------------- | ------- | --------------- | --------- | --- | --- | ----------- |
| InvoiceNumber | ItemKey | ItemDescription | UnitPrice | Qty | Gst | RecInsertDt |

1.  **For Invoice Table Overall Data flow looks:**

![](.//media/image131.png)

Aggregate transformation:

![](.//media/image132.png)

Join transformation:

![](.//media/image133.png)

Select Transformation:

![](.//media/image134.png)

2.  **For Invoice Lines:**

In the **same** data flow after your source CSV add a new branch
transformation. This will branch the same data source to two different
pathes

![](.//media/image135.png)

**Final Data flow for invoice and invoice line:**

![](.//media/image136.png)

**Derived Column Transformation:**

![](.//media/image137.png)

**Join transformation:**

![](.//media/image138.png)

**Select Transformation:**

![](.//media/image139.png)

**DDLS for InvoiceLine table:**

    CREATE TABLE [smartfoods].[invoiceline](
    	[invoiceNumber] [nvarchar](max) NULL,
    	[ItemKey] [bigint] NULL,
    	[ItemDescription] [nvarchar](max) NULL,
    	[UnitPrice] [float] NULL,
    	[qty] [int] NULL,
    	[Gst] [float] NULL,
    	[RecInsertDt] [date] NULL
    );
    GO
