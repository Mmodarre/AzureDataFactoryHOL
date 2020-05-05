![](.//media/image1.png)

Linked Services, Datasets and Integration Runtimes

# Contents

[Lookup activity, ForEach loop and Execute Pipeline activity
1](#lookup-activity-foreach-loop-and-execute-pipeline-activity)

[Task 1: Extend the pipeline with Lookup activity and ForEach Loops
1](#extend-the-pipeline-with-lookup-activity-and-foreach-loops)

[Task 2: Expand the pipeline using Lookup activity
6](#expand-the-pipeline-using-lookup-activity)

[Task 3: Test your pipeline 10](#test-your-pipeline)

[Task 4: How much did this cost? 11](#how-much-did-this-cost)

[Task 5: Clone and modify SmartFoods customer pipeline for transactions
12](#clone-and-modify-smartfoods-customer-pipeline-for-transactions)

[Task 6: Add ‘Transaction’ pipeline to the ‘InitialLoad’ pipeline
14](#add-transaction-pipeline-to-the-initialload-pipeline)

[Task 7: Publishing 16](#publishing)

# Lookup activity, ForEach loop and Execute Pipeline activity

Duration: 60 minutes

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

![](.//media/image2.png)

3.  Drag a “*ForEach”* loop activity to the canvas and rename it to
    ***“LoopSmartFoodsDates”***

4.  Under activities click the pencil icon to enter the “ForEach” loop
    canvas.

![](.//media/image3.png)

5.  Drag a ***“Execute Pipeline”*** activity on the ForEach loop canvas.

![](.//media/image4.png)

6.  Rename it to ***RunSmartFoodCustomerApiToBlob***

7.  Under “Settings”
    
    1.  “Invoked Pipeline” select “SmartFoodCustomerApiToBlob” pipeline
        that you created previously.
    
    2.  Expand the “Advanced” part and click on “Auto-fill parameters.

![](.//media/image5.png)

3.  For ***“date”*** parameter, from the expression editor select the
    “LoopSmartFoodsDates” under ForEach iterator

![](.//media/image6.png)

> Note: To go back to the main canvas (from the Execute pipeline canvas)
> use the hyperlink on top of the canvas.

![](.//media/image7.png)

#### Expand the pipeline using Lookup activity

Now that we have a pipeline that can iterate over an array of dates and
invoke the main pipeline we want to expand it further with a
***Lookup*** activity to read the list of dates from a text file (it can
be a SQL database as well) and perform the loading automatically.

This is how our pipeline looks like right now:

![](.//media/image8.png)

First, we need to place the file with list of dates on Azure Blob
Storage so ADF can access it.

1.  On your computer browse to the location that you cloned the GitHub
    repo. Under Data/SmartFoods/ there is a file ***dates.csv***. Open
    this file and inspect the contents.

2.  Open Azure Storage Explorer and go to *smartfoodsstaging* container.
    Create a new folder called *ref\_data* and upload the *dates.csv*
    file there.

![](.//media/image9.png)

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

![](.//media/image10.png)

![](.//media/image11.png)

4.  Click on the ForEach under *Settings*
    
    1.  For *Items* go to Expression Editor and change it to the below
        to receive it from the *Lookup* activity instead of the
        hard-coded variable:

<!-- end list -->

    @activity('LookupDates').output.value

![](.//media/image12.png)

5.  Double click on the ForEach loop to open its canvas and then click
    on *Execute Pipeline* activity. Under *Settings:*
    
    1.  For *date* change the value to below:

<!-- end list -->

    @item().date

![](.//media/image13.png)

#### Test your pipeline

1.  Click *Debug* to start the pipeline

2.  Inspect the output

![](.//media/image14.png)

3.  Once finished successfully go to *Azure Storage Explorer* to make
    sure all files in the dates.csv are created.

![](.//media/image15.png)

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

![](.//media/image16.png)

#### Clone and modify SmartFoods customer pipeline for transactions

A great feature of ADF is the ability to clone different objects, and if
the underlying objects are sufficiently with minimal modification the
new pipeline can perform a similar task on a different set of data.

In this task we take advantage of this feature and replicate the
*SmartFoodCustomerApiToBlob* pipeline and modify it to load SmartFoods
transactions.

1.  From the left hand list of pipelines click on the three dots next to
    *SmartFoodCustomerApiToBlob* pipeline and select ***Clone***.

![](.//media/image17.png)

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

![](.//media/image18.png)

![](.//media/image19.png)

5.  Rename the new activity to ***‘RunSmartFoodTransactionApiToBlob’***

![](.//media/image20.png)

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

![](.//media/image21.png)
