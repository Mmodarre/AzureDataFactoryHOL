![](.//media/image1.png)
### Note: This is a work in progress and any feedback and collaboration is really appreciated. New excercises will be added soon.
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

  - Create Azure Key Vault and Linked Services in ADF.

  - Create ADF parameterized pipeline.

  - Install Azure Data Factory self-hosted integration runtime to ingest
    from on-premises data systems.

  - (In progress) Perform code-free Spark ELT using Azure Data Factory
    Mapping Dataflows.

  - (To do) Source control ADF pipelines.

  - (To do) CI/CD ADF pipelines and your ELT code.

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
food nutrients (Carbs, Saturated fats etc.) to promote healthy and
SmartFood shopping.

In this hands-on lab, attendees will build an end-to-end solution to
build a data warehouse using data lake methodology.

## Solution architecture

Below is a diagram of the solution architecture you will build in this
lab. Please study this carefully so you understand the solution as
whole, before building various components.

![](.//media/image2.png)

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

## Getting Started

Hands on lab documents are located under Lab-guide directory. Here is
the list labs available:

**Azure Data Factory:**

1.  [Before\_the\_hands-on\_lab\_(Prepare\_the\_environment)](Lab-guide/01-Before_the_hands-on_lab_\(Prepare_the_environment\)/README.md)

2.  [Linked\_Services\_Datasets\_and\_Integration\_Runtimes](Lab-guide/02-Linked_Services_Datasets_and_Integration_Runtimes/README.md)

3.  [Copy\_Activity\_Parameters\_Debug\_and\_Publishing](Lab-guide/03-Copy_Activity_Parameters_Debug_and_Publishing/README.md)

4.  [Lookup\_activity\_ForEach\_loop\_and\_Execute\_Pipeline\_activity](Lab-guide/04-Lookup_activity__ForEach_loop_and_Execute_Pipeline_activity/README.md)

5.  [Get\_Metadata\_activity\_filter\_activity\_and\_complex\_expressions](Lab-guide/05-Get_Metadata_activity_filter_activity_and_complex_expressions/README.md)

6.  [Self-hosted\_Integration\_Runtime\_\_decompress\_files\_and\_Delete\_activity](Lab-guide/06-Self-hosted_Integration_Runtime__decompress_files_and_Delete_activity/README.md)

**Azure Data Factory Mapping Data Flows:**

7.  [SmartFoodsCustomerELT](Lab-guide/07-SmartFoodsCustomerELT/README.md)

8.  [ELT\_with\_Mapping\_Dataflows–Practice\_excercises](Lab-guide/08-ELT_with_Mapping_Dataflows–Practice_excercises/README.md)
