### Receive EDI file from a Partner and process via B2B Cloud

As of today, webMethods B2B Cloud doesnt have an inbound channel for accepting FTP files and process them. What do you do when you have partners ( brick and mortar stores, mom and pop shops , for example) can only do FTP . We have a solution !! This tutorial will walkyou through this solution. 

PS : Receiving a file via FTP in B2B Cloud  is in Software AG's roadmap. Please check with your account team or Product management for timelines

# Transaction Flow
1.  webMethods B2B Cloud comes along with Flow editor . We will create an Integration in Flow editor to receive the file and submit to B2B Cloud. This integration will get the file from an FTP server
1.  Once the file is received , the integration will map the sender id, receiver id , edi interchange and group envelopes
1.  The EDI document will be submitted to B2B Cloud using the B2B application
1.  B2B Cloud receives the file and identifies the sender and receiver 
1.  B2B Cloud executes the processing rule based on the senderid, receiverid, document type
1. B2B cloud executes the processing rule which is configured to call an Integration for further mapping. The integration does the following
	- Received and parse the EDI 850 file
	- Convert the edi file to XML
	- Create an EDI 855
	- send the EDI 855 back to the trading partner via B2B Cloud

# Tutorial Steps
1. Create an Integration to receive the file and submit to B2B Cloud. Follow the steps as shown in the below code snippet
![](https://github.com/krishnajc/webmethods-b2b-examples/blob/master/receiveEDI-FTP/images/receiveFTPFile.png)
	 Add EDI application and set the operation to addGroupEnvelope. Refer to the following code snippet for mappings
![](https://github.com/krishnajc/webmethods-b2b-examples/blob/master/receiveEDI-FTP/images/mapaddgroupenvelopeinput.png)
	Add EDI application and set the operation to addInterchangeEnvelope. Refer to the following code snippet for mappings
![](https://github.com/krishnajc/webmethods-b2b-examples/blob/master/receiveEDI-FTP/images/mapInterchangeenvelopeinput.png)
	Add B2B application and set the operation to submit. Refer to the following code snippet for mappings
![](https://github.com/krishnajc/webmethods-b2b-examples/blob/master/receiveEDI-FTP/images/mapB2BSubmitinput.png)
1. Create a partner profile. Before this the assumption is you have already created an Enterprise for your company to receive files. here is a screenshot for of how an Enterprise should look like. Setting an Enterprise is a one time setup
![](https://github.com/krishnajc/webmethods-b2b-examples/blob/master/receiveEDI-FTP/images/enterprise.png)
Partner setup 
![](https://github.com/krishnajc/webmethods-b2b-examples/blob/master/receiveEDI-FTP/images/addpartner.png)
1. Add a EDI 850 and 855 business document. This process will create a document type in webMethods Flow Editor , so it will be easy for mappings to be done when a processing rule executes an Integration as part of the action
![](https://github.com/krishnajc/webmethods-b2b-examples/blob/master/receiveEDI-FTP/images/addbusinessdocument.png)
Follow the same step to add EDI 855 as well
![](https://github.com/krishnajc/webmethods-b2b-examples/blob/master/receiveEDI-FTP/images/addEDI850.png)
1. Now, before adding the processing rule in B2B Cloud, lets create an Integration in Flow Editor which allows you to map and process the EDI. This is where all the mappings and transformations are done and if needed the PO is sent to an ERP system like SAP for example
	- Swicth to flow editor and click on recipes
	-Enter "edi" in the search box and press enter
	-You will see receiveEDI850Send855B2BTransactions recipe. This recipe does the following
*Receives an EDI 850 Purchase Order (PO) from a trading partner through the webMethods.io B2B product instance, performs service orchestrations to interpret the PO, and uses a back-end FTP server to generate the PO Acknowledgment, and routes it back to the trading partner through webMethods.io B2B product instance.*
	-Click on Use
	-Select a project and fill out the application details. If an application is not created , create a new application for each account. This flow assumes you have connectivity to an ftp server. if you dont have one , you can sign up for a free ftp server on https://hostedftp.com/ or https://DriveHQ.com
	-see the following code snippet
![](https://github.com/krishnajc/webmethods-b2b-examples/blob/master/receiveEDI-FTP/images/recipe.png)
1. Next, add a processing rule to identify the sender , receiver, and document type to take further action
![](https://github.com/krishnajc/webmethods-b2b-examples/blob/master/receiveEDI-FTP/images/processingRule.png)
![](https://github.com/krishnajc/webmethods-b2b-examples/blob/master/receiveEDI-FTP/images/addProcessingrule1.png)
![](https://github.com/krishnajc/webmethods-b2b-examples/blob/master/receiveEDI-FTP/images/addProcessingrule2.png)
![](https://github.com/krishnajc/webmethods-b2b-examples/blob/master/receiveEDI-FTP/images/addProcessingrule3.png)
![](https://github.com/krishnajc/webmethods-b2b-examples/blob/master/receiveEDI-FTP/images/addProcessingrule4.png)





