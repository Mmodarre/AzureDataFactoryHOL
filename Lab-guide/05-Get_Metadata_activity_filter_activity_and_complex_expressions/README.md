![](.//media/image1.png)

Get Metadata activity, filter activity and complex expressions

# Contents

[Get Metadata activity, filter activity and complex expressions
1](#get-metadata-activity-filter-activity-and-complex-expressions)

[Task 1: Load WWI Data 1](#load-wwi-data)

[Task 2: Setup Initial Load for WWI data
2](#setup-initial-load-for-wwi-data)

[Task 3: Create Initial load pipelines for WWI ‘Orders’ and ‘Orderlines’
11](#create-initial-load-pipelines-for-wwi-orders-and-orderlines)

# Get Metadata activity, filter activity and complex expressions

Duration: 60 minutes

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

![](.//media/image2.png)

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

![](.//media/image3.png)

4.  Click Debug to inspect the output of the activity

> Output format of the Get Metadata activity: Output of Get Metadata
> activity is a **JSON** document. At the root of the document is
> **‘childitems’** and for <span class="underline">each file</span>
> there is a sub-document with **‘name’** and **‘type’** attributes.

![](.//media/image4.png)

![](.//media/image5.png)

5.  Drag a filter activity and rename it to
    *‘FilterWWISFTPCustomerFileNames’* and connect it to get metadata
    with success connector.

![](.//media/image6.png)

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

![](.//media/image7.png)

7.  Debug and inspect the ‘filter’ activity output.

> Output of ‘filter’ activity: The output of filter activity is again a
> JSON document with ‘Output’ document being the most important part.
> Within that there is an ‘ItemsCount’ and ‘FilterItemsCount’ and an
> array called ‘Value’

![](.//media/image8.png)

There were 5 items with ‘testing’ and ‘old’ words in the file name that
the filter activity removed from the results.

8.  drag ‘ForEach’ loop activity and connect to success of ‘filter’
    activity and rename it to *‘LoopWWISftpCustomerFiles’*
    
    1.  Under ‘settings’ -\> Items:

<!-- end list -->

    @activity('FilterWWISFTPCustomerFileNames').output.Value

![](.//media/image9.png)

![](.//media/image10.png)

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

![](.//media/image11.png)
