![](.//media/image1.png)

Linked Services, Datasets and Integration Runtimes

# Contents

[Linked Services, Datasets and Integration Runtimes:
1](#linked-services-datasets-and-integration-runtimes)

[Task 1: Create Azure Data Factory Integration Runtime
1](#create-azure-data-factory-integration-runtime)

[Task 2: Create a new ADF Key Vault Linked Service
5](#create-a-new-adf-key-vault-linked-service)

[Task 3: Add blob Storage credentials to AKV
11](#add-blob-storage-credentials-to-akv)

[Task 4: Add other credentials to Azure Key Vault
13](#add-other-credentials-to-azure-key-vault)

[Task 5: Create SFTP Linked Service 13](#create-sftp-linked-service)

[Task 6: Create datasets for WWI SFTP data
14](#create-datasets-for-wwi-sftp-data)

[Task 7: Create Azure Blob Storage Linked Service
19](#create-azure-blob-storage-linked-service)

[Task 8: Create Blob Storage Datasets for WWI Input data
22](#create-blob-storage-datasets-for-wwi-input-data)

[Task 9: Create Blob Storage Datasets for SmartFoods Input data
22](#create-blob-storage-datasets-for-smartfoods-input-data)

[Task 10: Create Blob Storage Datasets for WWI Data Warehouse output
23](#create-blob-storage-datasets-for-wwi-data-warehouse-output)

[Task 11: Create a SQL Database Linked Service and Dataset
24](#create-a-sql-database-linked-service-and-dataset)

[Task 12: Create an HTTP Linked Service:
28](#create-an-http-linked-service)

[Task 13: Create a CSV Dataset on the HTTP Linked Service
29](#create-a-csv-dataset-on-the-http-linked-service)

[Task 14: Create a JSON Dataset on the HTTP Linked Service
31](#create-a-json-dataset-on-the-http-linked-service)

# Linked Services, Datasets and Integration Runtimes: 

Duration: 90 minutes

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

![](.//media/image2.png)

Click on Author and Monitor and it should be taking you to ADF UI like
below. This is the main ADF UI. On the left-hand side there is Author
and Monitor tabs. click on author tab to start building the first
pipeline.

> ![](.//media/image3.png)

2.  Create a new Integration run time in a specific Azure Region: go to
    Author tab Connections (at the bottom of the screen) click New

![](.//media/image4.png)

Select “Perform data movement and dispatch activities to external
computes”

![](.//media/image5.png)

Choose “Azure Public, Self-Hosted”

![](.//media/image6.png)

Give your new IR a name and Choose the preferred region

![](.//media/image7.png)

#### Create a new ADF Key Vault Linked Service

Process summary: For ADF to be able to access different services it will
require credentials and the recommended approach is to store the secrets
in Azure Key Vault and give ADF permissions to retrieve these
credentials at runtime.

To add an Azure Key Vault Linked Service to ADF apart from adding it in
ADF you also need to authorize ADF to access the secrets in KV as well.
So, after creating the linked service there is some further steps(Step E
and further) to be completed on Azure portal.

![](.//media/image8.png)

1)  Create a new Linked Service: go to Author tab Connections (at the
    bottom of the screen) click New

2)  Search for Key Vault and select Azure Key Vault

3)  Give you linked service a name, select subscription and the Key
    Vault created in previous steps

4)  Copy the managed identity object id and click on “**Edit Key
    Vault**” link

![](.//media/image9.png)

![](.//media/image10.png)

5)  On the key vault page (In Azure Portal) select access policies

![](.//media/image11.png)

![](.//media/image12.png)

![](.//media/image13.png)

6)  from secrets permissions drop down select “Get” and “List”

![](.//media/image14.png)

7)  Click on Select principle

![](.//media/image15.png)

8)  paste the “managed identity object id” which will then show your ADF
    and select it

9)  Finally click Add and you should see something like below

10) If all looks good click “Save”

> **Note:** Leave the browser tab of Azure Key Vault open as we will
> need it soon.

![](.//media/image16.png)

11) Go back to ADF where we left of and click on “Test Connection” if
    connection was successful click “Create” to create the linked
    Service.

![](.//media/image17.png)

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

![](.//media/image18.png)

2)  Copy the Connection string

3)  Go back to the browser tab you had AKV open

4)  Click on Secrets

5)  Generate/Import

> ![](.//media/image19.png)

6)  For name provide \<storage account name\>connectionstring

7)  For value paste the Storage account connection string

8)  Click “Create”

![](.//media/image20.png)

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

![](.//media/image21.png)

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

![](.//media/image22.png)

2)  Select SFTP

3)  Select Parquet for format

4)  From Drop down select the SFTP linked service created in previous
    task

5)  For name provide “WWISftpParquet”

6)  Leave “Directory” and “File” Blank (We are going to parametrize
    these)

7)  Change “Import Schema to None”

8)  Click OK

![](.//media/image23.png)

![](.//media/image24.png)

9)  Go to parameters tab in your dataset and create three parameters as
    in the screenshot above

10) Go to connection tab and select the directory box once selected
    click on “Add dynamic Contents” or hit Alt+P

11) In Expression editor your list of parameters is shown at the bottom
    select folder and inspect the contents in the expression editor box
    and then click “Finish”

![](.//media/image25.png)

12) Repeat the same steps for file name but instead in the Expression
    Editor enter.

<!-- end list -->

    @{dataset().filename}.@{dataset().filetype}

> Hint: The above expression concatenates the two parameters with a ‘.’
> in between to make a full file name.
> 
> Hint2: Also, we could write the same expression as:
> @concat(dataset().filename,’.’,dataset().filetype)

![](.//media/image26.png)

1)  Click on preview data and fill in the parameters as:
    
    1.  **Folder**: WorldWideImporters/orderlines
    
    2.  **Filename**: orderlines\_2019-09-02
    
    3.  **Filetype**: parquet

![](.//media/image27.png)

If the data set and parameters are created correctly you should see
something like below:

![](.//media/image28.png)

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

![](.//media/image29.png)

3)  Select Azure Blob Storage

4)  For name call it \<your storage account name\>john

5)  Select your IR

6)  Change Authentication Mechanism to Key Vault

7)  Select the AKV linked Service you created in the previous task

8)  For Secret name provide \<storage account name\>connectionstring

9)  Test connection and click “create”.

![](.//media/image30.png)

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

![](.//media/image22.png)

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

#### Create Blob Storage Datasets for SmartFoods Input data

SmartFoods data is available to an API. Customer data is in JSON format
and Transactions are in CSV format. But we are using Azure Data
Factory’s Copy activity to convert customer data to CSV so we have the
same file format for both feeds from SmartFoods API source.

1)  Click the plus sign on the left top hand of ADF and select Dataset.

![](.//media/image22.png)

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

![](.//media/image22.png)

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

![](.//media/image31.png)

![](.//media/image32.png)

Create a Dataset similar to Blob storage – User Screenshots as a guide

![](.//media/image33.png)

![](.//media/image34.png)

![](.//media/image35.png)

#### Create an HTTP Linked Service:

Name: SmartFoodsApiLinkedService

Base URL: <https://smartfoods.azurewebsites.net/api/>

> Note: Use Screenshot as a guide for other options.

![](.//media/image36.png)

#### Create a CSV Dataset on the HTTP Linked Service

ADF allows us to create datasets of various format on top of an HTTP
Linked service to receive data over an HTTP connection from another
source in various formats such as delimited text.

1)  Start creating a dataset

2)  Select HTTP as type

3)  Select Delimited Text as format

4)  Name: SmartFoodsTransactionApiCsv

5)  Leave other options **BLANK** as it is and click “Ok”

![](.//media/image37.png)![](.//media/image38.png)

![](.//media/image39.png)

6)  Create a parameter as “authCode” under dataset

> “Note: We are going to use this parameter to pass the Authentication
> token to the API Service on calling the service.”

![](.//media/image40.png)

7)  Under Connection tab and for “Relative URL” click go to “dynamic
    content editor and provide:

Here we are creating an HTTP dataset which the query parameters for it
is parametrized so we can change them at runtime and retrieve different
data portions from the API

    smartfoods?code=@{dataset().authCode}

![](.//media/image41.png)

#### Create a JSON Dataset on the HTTP Linked Service

SmartFoods API provides the customer data in JSON format as such we need
to create a second dataset on top of the same HTTP Linked service but in
JSON Format

Follow the same steps as in previous task, except for data format select
**JSON**. For name provide “**SmartFoodsCustomerApiJson”**.

Screenshots below for guidance:

![](.//media/image42.png)

![](.//media/image43.png)
