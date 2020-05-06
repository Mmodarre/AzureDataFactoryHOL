![](.//media/image1.png)

Self-hosted Integration Runtime, decompress files and Delete activity

# Contents

[Self-hosted Integration Runtime, decompress files and Delete activity
1](#self-hosted-integration-runtime-decompress-files-and-delete-activity)

[Task 1: Create a self-hosted IR to access SmartFoods reference data
from local file system.
1](#create-a-self-hosted-ir-to-access-smartfoods-reference-data-from-local-file-system.)

[Task 2: Prepare the local machine 5](#prepare-the-local-machine)

[Task 3: Create local file system Linked Service
5](#create-local-file-system-linked-service)

[Task 4: Create Dataset for file system
8](#create-dataset-for-file-system)

[Task 5: (Challenge Task) Parametrize the dataset with
8](#challenge-task-parametrize-the-dataset-with)

[Task 6: Copy the data from on-prem to cloud
10](#copy-the-data-from-on-prem-to-cloud)

[Task 7: Delete the zip file from local file system
15](#delete-the-zip-file-from-local-file-system)

[Part 1 Learning Summary: 16](#part-1-learning-summary)

# Self-hosted Integration Runtime, decompress files and Delete activity

Duration: 60 minutes

> **Note:** This exercise requires a MS Windows workstation. You can use
> your own machine if you are working from a PC, alternatively you can
> deploy a small VM in Azure.

The integration runtime (IR) is the compute infrastructure that Azure
Data Factory uses to provide data-integration capabilities across
different network environments.

A self-hosted integration runtime can run copy activities between a
cloud data store and a data store in a private network. It also can
dispatch transform activities against compute resources in an
on-premises network or an Azure virtual network. The installation of a
self-hosted integration runtime needs an on-premises machine or a
virtual machine inside a private network.

If you want to learn more on how to create and configure a self-hosted
IR check [This
article](https://docs.microsoft.com/en-us/azure/data-factory/create-self-hosted-integration-runtime).

#### Create a self-hosted IR to access SmartFoods reference data from local file system.

1.  Go to connections tab of ADF

![](.//media/image2.png)

2.  Under ‘Integration runtimes’ click ‘New’

![](.//media/image3.png)

3.  Select ‘Azure, Self-Hosted’

![](.//media/image4.png)

4.  Choose Self-Hosted

![](.//media/image5.png)

5.  For name enter ‘*OnPremisesIR’*

6.  click ‘Create’

Now to install the IR engine you have two options

1.  Express Setup: With this option you can download a pre-configured
    executable file (exe) and after finishing installation the IR engine
    is configured and connected to your ADF instance.

2.  Manual Setup: You can download the generic version of Azure IR and
    install on a MS Windows machine and then you need to register it to
    your instance of ADF using the on screen provided keys.

<!-- end list -->

7.  Choose your preferred installation method and install the IR on your
    Windows machine.

> **Note1**: Azure data factory allows clustering self-hosted (SH) IR
> for availability and DR. Refer to the linked document above for
> further details on clustering and nodes.
> 
> **Note2:** You can enable/disable auto update of the SHIR (default
> enabled) or set time window for it from them ‘Auto Update’ tab
> 
> **Note3**: A single installation of SHIR can be shared with multiple
> ADF instances using ‘Linked Self-Hosted IR’ option.
> 
> **Note4**: One of the main capabilities of SHIR is to access on-prem
> systems behind firewall and as such the IR needs to be installed on a
> machine that is located within the firewall of the data system you
> intend to access. This could be an on-prem SQL Server, on-prem SFTP or
> on-prem local file system (or NAS storage).

8.  Once installation completes and the IR starts successfully the
    ‘*OnPremisesIR*’ status on the list changes to ‘*Running’*

![](.//media/image6.png)

Now that we have a running On-prem IR we can go ahead and create
LinkedServices and datasets to access on-prem files.

#### Prepare the local machine

For the SH IR to access the local machine securely we need to create a
local user on the machine and copy the data from the GitHub repo to a
directory this user can access:

1.  On the Windows machine go to Settings -\> Accounts -\> Other users
    -\> ‘Add someone else to this PC’

2.  Click ‘I don’t have this person’s sing-in information’

3.  Click ‘Add a user without a Microsoft account’

4.  For username enter ‘adf\_ir’

5.  Enter a password

6.  Answer the security questions

7.  Go to C:\\Users and create a directory called ‘adf\_ir’

8.  Browse to the location that you clone the GitHub repo on your
    machine and under \\Data\\SmartFoods copy the file
    SmartFoodsRefData.zip to C:\\Users\\adf\_ir

#### Create local file system Linked Service

1.  In ADF start creating a new Linked Service

2.  For Data Store select “File System”

![](.//media/image7.png)

3.  Now configure the new Linked service:
    
    1.  Name: *SmartFoodsLocalFileSystem*
    
    2.  Connect via: **<span class="underline">OnPremisesIR</span>**
    
    3.  Host: C:\\Users\\adf\_ir
    
    4.  Username: adf\_ir
    
    5.  Password: \<The password you created the adf\_ir account with\>
    
    6.  Click “Test connection”
    
    7.  If successful click “Create”

![](.//media/image8.png)

#### Create Dataset for file system

1.  Create a new dataset

2.  For data store choose : *File System*

3.  For Format choose: *Delimited Text*

4.  name: *SmartFoodsRefDataLocalFileSystem*

5.  Linked Service: *SmartFoodsLocalFileSystem*

6.  “First Row as header” ticked

7.  Leave directory and file blank

8.  Import Schema: None

![](.//media/image9.png)

#### (Challenge Task) Parametrize the dataset with 

> Configure the dataset (Use below figure as reference and if you need a
> refresher on how to create datasets go back to first part of the lab)

![](.//media/image10.png)

Click “Preview data” to make sure the dataset is working correctly.

File: SmartFoodsRefData

Filetype: zip

![](.//media/image11.png)

![](.//media/image12.png)

> Note: The zip file contains 3 CSV files. The preview randomly shows
> part of one of the files within the compressed file.

Don’t forget to **<span class="underline">Publish</span>** your
changes\!

#### Copy the data from on-prem to cloud

Now that we have a working linked service and dataset for SmartFoods
reference data we need to create a pipeline to copy the data from the
local file system to the cloud.

The pipeline will like SFTP pipeline with two modifications. First there
is no need for a filter activity and second since the files are not
dated, we will need to delete the file from source once copy completes
successfully.

*Get metadata* (list files) -\> *Copy activity* (from local FS to Blob
Storage) -\> *Delete activity* (Remove copied files from local file
system)

1.  Create a pipeline and rename it to
    *“SmartFoodsRefDataLocalFStoBlob”*

2.  Add a “Get Metadata” activity and rename it to
    “*GetSmartFoodsRefDataFileListLocalFS*”

3.  Configure the Get Metadata using the below figure as reference:

![](.//media/image13.png)

4.  Add a “ForEach” activity and rename it to
    “*LoopSmartFoodsLocalFileSystemRefDataFiles*”

5.  Configure the “ForEach” activity with items:

<!-- end list -->

    @activity('GetSmartFoodsRefDataFileListLocalFS').output.childItems

![](.//media/image14.png)

6.  Go inside “ForEach” activity canvas and add a “Copy” activity and
    rename it to “*CopySmartFoodsRefDataLocalFStoBlob*”

7.  Setup source
    
    1.  Source dataset: *SmartFoodsRefDataLocalFileSystem*
    
    2.  File*: @first(split(item().name,'.'))*
    
    3.  Filetype: *@last(split(item().name,'.'))*

![](.//media/image15.png)

Now we need to setup ‘sink’ for this copy activity and since data
belongs to SmartFoods source system we could use the
‘SmartFoodsDelimitedTextBlob’ dataset (If you recall this dataset
writes to ‘*smartfoodsstaging*’ container and is parametrized with
‘*folder*’, ‘*file*’ and ‘*filetype*’. Except since the Zip file
contains multiple files if set the parameters, once unzipped they will
overwrite each other\! This problem arises since ADF does NOT support
optional parameters.

> Azure Data Factory Does NOT support OPTIONAL parameters\!

The only solution left is to create a copy of this dataset and remove
the *‘file’* and *‘filetype’* parameters.

8.  Clone the ‘*SmartFoodsDelimitedTextBlob*’ dataset and rename it to
    ‘*SmartFoodsDelimitedTextBlobOnlyFolderPram*’. Then remove
    ‘*file’* and ‘*filetype’* parameters from it. (Use below figure
    as reference)

![](.//media/image16.png)

9.  Go back to copy activity and setup sink
    
    1.  Sink dataset: *SmartFoodsDelimitedTextBlobOnlyFolderPram*
    
    2.  Folder: Ref\_data
    
    3.  Copy behavior: Preserve Hierarchy

![](.//media/image17.png)

> **Note:** We are changing ‘Copy behavior’ to ‘Preserve Hierarchy’ as
> we want to make sure the copy process will retain the original file
> names within the compressed file. But this will result in a creating a
> sub directory with in ‘ref\_data and name it as
> ‘SmartFoodsRefData.zip’

![](.//media/image18.png)

#### Delete the zip file from local file system

Azure Data Factory also offers a ‘delete’ activity to remove the files
from any file system.

1.  After the ‘Copy’ activity add a ‘Delete’ activity (in activities
    list under General) and connect to success of the Copy activity.

2.  Rename it to ‘DeleteSmartFoodsRefDataLocalFS’

3.  For source set it up exactly same as the source of ‘Copy’ activity

> ![](.//media/image19.png)

4.  Debug your pipeline to make sure it is working correctly.

> **Note:** Delete activity runs successfully but if you check the
> directory the file may still be in there. The primary reason for this
> would access/security settings applied to the file. (if adf\_ir user
> has the right access to perform deleting the file or not)

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

17. Setting up a self-hosted integration runtime

18. Accessing local file system using SHIR

19. Copying activity with compression/decompression

20. Delete activity
