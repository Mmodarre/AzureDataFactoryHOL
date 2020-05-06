![](.//media/image1.png)

Copy Activity, Parameters, Debug and Publishing

# Contents

[Copy Activity, Parameters, Debug and Publishing:
1](#copy-activity-parameters-debug-and-publishing)

[Task 1: Create a pipeline and setup authentication to SmartFoods OAuth2
(Token based) API
1](#create-a-pipeline-and-setup-authentication-to-smartfoods-oauth2-token-based-api)

[Task 2: Store the access token in a variable
9](#store-the-access-token-in-a-variable)

[Task 3: Retrieve data from API and store in Blob Storage
11](#retrieve-data-from-api-and-store-in-blob-storage)

[Task 4: Test your pipeline 15](#test-your-pipeline)

# Copy Activity, Parameters, Debug and Publishing:

Duration: 30 minutes

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

![](.//media/image2.png)

3.  From the parameters tab create a pipeline parameter and call it
    “date”

![](.//media/image3.png)

![](.//media/image4.png)

4.  From the activities bar under “General” drag a “Web” activity to the
    canvas

![](.//media/image5.png)

5.  Rename the activity to AKVUsername

6.  Go to your Azure Key Vault in **Azure Portal** and from secrets
    select “SmartFoodsApiUsername” (You have created this secret
    previously)

![](.//media/image6.png)

7.  Select the current version

![](.//media/image7.png)

8.  Copy the “Secret Identifier”

![](.//media/image8.png)

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

![](.//media/image9.png)

13. Go back to General tab and tick “Secure Output”

14. Now click “Debug” to test the activity

![](.//media/image10.png)

15. Under output tab click the output button to see the activity output

![](.//media/image11.png)

The output should be in form of:

    { "SecureOutput": "**********" }

This is the effect of setting secure output setting

> **Try it:** Try removing the “secure output” tick and re-run debug and
> see how the output will differ.

16. **Repeat the same steps and add “AKVPassword” Web activity.**

![](.//media/image12.png)

17. Add another “Web activity” to the canvas. Rename it to
    “SmartFoodsLogin” and attach the success connectors from the
    AKVUsername and AKVPassword to it as below:

![](.//media/image13.png)

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

![](.//media/image14.png)

19. Under general tab tick the “Secure Output” to make sure the tokens
    are not getting revealed in ADF logs.

20. Debug you pipeline to make sure all activities are successful.

#### Store the access token in a variable

Now we have the token to access the API we need to store the token in a
variable to be passed to our copy activity in order to access the API
securely.

1.  Under variables in the pipeline create a new variable “token”

> ![](.//media/image15.png)

2.  Add a “Set Variable” activity to canvas

![](.//media/image16.png)

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

  - First create a pipeline parameter called “date”

![](.//media/image17.png)

  - Drag a copy activity to canvas and connect to “SetAccessToken”
    activity

  - Rename it to “SmartFoodsCustomersToBlob”

![](.//media/image18.png)

  - Under source
    
    1.  Source dataset: “**SmartFoodsCustomerApiJson**”
    
    2.  For “authCode” parameter go to Expression editor and select the
        “token” variable

![](.//media/image18.png)

3.  Request method: Post

4.  Request body: click on expression editor and enter

<!-- end list -->

    @{json(concat('{"trans_date": "',pipeline().parameters.date,'","dataDomain" : "customers"}'))}

> **Expression explanation**: We are composing a JSON for REST request
> body with two attributes trans\_date which we use the pipeline
> parameter as value and dataDomain hard coded to “customers”

![](.//media/image19.png)

  - For Sink
    
    1.  Sink dataset: “SmartFoodsDelimitedTextBlob”
    
    2.  Dataset parameters:
        
          - Folder: “customers”
        
          - File: Enter Expression editor and enter

<!-- end list -->

    smartfoods_customers_@{replace(pipeline().parameters.date,'-','')}

  - Filetype: “csv”

![](.//media/image20.png)

#### Test your pipeline

1.  Click “Debug”

2.  Provide 2020-02-10 as the value for “date” parameter

3.  Go to Azure Storage Explorer -\> find the respective storage
    account, container and directory -\> Locate the
    “smartfoods\_customers\_20200201.csv” file and open it to make
    sure the data is copied correctly.

![](.//media/image21.png)
