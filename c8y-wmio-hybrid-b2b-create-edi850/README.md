# Send a Purchase Order EDI 850 based on Alarm from Cumulocity

This examples highlights that how an Alarm generated using cumulocity platform can be easily converted to an EDI document and send to Enterprise partner for further processing. In this example cumulocity is monitoring a Tank and reporting content level back. There is a Smart Rule which is constantly looking at Tank Level and raises an alarm as soon as content goes below critical level. Once the alarm is raised, a workflow is triggered on wm.io platform which intern create an EDI 850 document and route document to Partner via B2B cloud. Lot of manufacturer are moving into continue manufacturing model, using a setup like this can help them achieve continue manufacturing very well.

Contributors: Shashank Patel, Mangat Rai


## Prerequisites
1. You need Software AG webmethods.io B2B cloud tenant, webmethods.io integration cloud tenant and cumulocity cloud tenant. If you don't have one; sign up for free 30 trial tenant at [Software AG B2B](https://signup.softwareag.cloud/#/?product=b2b)

This tutorial assume that you already have a device connected with cumulocity platform and have smart rule created to raise alarm on Tank Level.

![](images/B2BLandingPage.png)

2. Create your enterprise Partner profile on B2B Cloud. Provide identifiers to identify your Enterprise uniquely.

![](images/MyEnterprise.png)

## Transaction Flow
1. Cumulocity raises an alarm once Quantity in Tank goes below a certain threshold.
2. A workflow in wm.io gets triggered based on the alarm generated in previous step.
3. Workflow invokes a hybrid service to query on prem SAP ECC system to get details about partner, material, quantity etc needed to create EDI 850 document.
4. Workflow invokes Salesforce create Case Action and create a case in Salesforce for tracking of Purchase Order.
5. Workflow executes a flow editor service
	- Maps 850 EDI business document
	- convert business document to EDI data.
	- Submit EDI 850 Purchase Order to B2B using submit inbuilt service. 
6. B2B cloud will route document to partner using sender\receiver id, Partner profile and outbound channel defined in B2B Cloud.

![](images/FlowDiagram.png)


## Tutorial Steps
1. Login to your cumulocity instance. Find you device and copy device Id.

![](images/cumulocity_device.png)

2. Create a workflow in wm.io. Check [tutorial](https://github.com/SoftwareAG/webmethodsio-examples) on how to create workflow in wm.io

3. Create a Cumulocity Trigger which listens to Alarm as starting point for this newly created workflow.

	Cumulocity Alarm Trigger
	
![](images/cumulocity_alarm.png)

4. Next step is invoke a hybrid Service to look up tank details from on premise SAP ECC. To setup hybrid service login to your on prem Integration Server and follow next steps
	
	a. Setup SAP Connection to connect with your ECC system.
	![](images/onPrem_SAPConnection.png)
	
	b. Setup Cloud connection with your wm.io instance. provide your wm.io cloud tenant url, username and password.
	![](images/onPrem_hybrid.png)
	
	c. Create Hybrid cloud application to publish to your wm.io cloud tenant.
	![](images/onPrem_hybridApps.png)
	
	d. Once app is published, it will start showing up in Action menu on right hand side of workflow canvas.

4. Add a EDI 850 and 855 business document. This process will create a document type in webMethods Flow Editor , so it will be easy for mappings to be done when a processing rule executes an Integration as part of the action

![](images/addbusinessdocument.png)

Follow the same step to add EDI 855 as well

![](images/addEDI850.png)


5. Create a processing rule to process inbound EDI 850 to identify the sender , receiver, document type and action

Add Processing rule

![](images/processingRule.png)

Give a name

![](images/addProcessingrule1.png)

Associate sender/s

![](images/addProcessingrule2.png)

Associate document/s

![](images/addProcessingrule3.png)

Provide the name of Integration that you are going to create and expose on webmethods.io Integration in the next step

![](images/addProcessingrule4.png)


6. Create an Integration on webmethods.io Integration which parses EDI 850 and converts to XML and sends this XML document to backend Application. Then, receives a response from backend and submits EDI 855 back to B2B

- Switch to flow editor

![](images/FlowEditor.png)

- Click on Recipes and search for edi. You will see receiveEDI850Send855B2BTransactions recipe. Use this recipe.

![](images/recipe_edi.png)

- Select a project and fill out the application details. If an application is not created , create a new application for each account. This flow assumes you have connectivity to an ftp server. if you dont have one , you can sign up for a free ftp server on https://hostedftp.com/ or https://DriveHQ.com

- See the following code snippet

![](images/recipe.png)

- After successful import

![](images/receiveEDI850Integration.png)

## Testing

1. Download postman collection - [postman](B2B%20wm.io.postman_collection.json) and import it on Postman. Modify Message-Id field in the Request header and submit it to the Inbound channel URL.

![](images/postman.png)

2. On B2B Cloud, click on Transactions to monitor documents as shown below

![](images/b2btransactions.png)