# Expose B2B Cloud for AS2 partners to exchange EDI messages

B2B Cloud can be exposed as an AS2 over HTTP channel and used to exchange EDI messages with Partners. This usecase is common for all Partners who has capability of exchanging EDI messages over AS2 which is a very popular protocol for EDI.

Contributors: Chaitanya Jadcherla, Mangat Rai , Shashank Patel


## Prerequisites
1. You need Software AG webmethods.io B2B cloud tenant and webmethods.io integration cloud tenant. If you don't have one; sign up for free 30 trial tenant at https://signup.softwareag.cloud/#/?product=b2b

![](https://github.com/patelshashank/webmethods-b2b-examples/blob/master/receive-edi-as2/images/B2BLandingPage.png)

2. Create your enterprise Partner profile on B2B Cloud. Provide identifiers to identify your Enterprise uniquely.

![](https://github.com/patelshashank/webmethods-b2b-examples/blob/master/receive-edi-as2/images/MyEnterprise.png)

3. You need an FTP server to connect. You can sign up for a free ftp server on https://hostedftp.com/ or https://DriveHQ.com

## Transaction Flow
1. Partner (Postman client) sends EDI 850 to B2B Cloud via AS2 over HTTP
1. B2B Cloud identifies the EDI document Type, Sender and Receiver 
1. B2B Cloud executes the processing rule based on a criteria for senderid, receiverid, document type
1. B2B cloud executes the action defined in processing rule which is configured to call webmethods.io Integration for further mapping. The integration does the following
	- Receive EDI 850 file and send FA EDI 997 back to the partner
	- Parse EDI 850 file 
	- Convert the EDI file to XML
	- Send the XML file to back-end Application via FTP
	- Receive a response from back-end Application via FTP
	- Transform the Application response to EDI 855
	- Submit the EDI 855 back to B2B Cloud
	- B2B Cloud delivers EDI 855 message back to the Partner

![](https://github.com/patelshashank/webmethods-b2b-examples/blob/master/receive-edi-as2/images/EDIFlow.png)


## Tutorial Steps
1. Create an inbound AS2 channel on Software AG B2B Cloud which is open for inbound AS2 communication from Partner ACME.

![](https://github.com/patelshashank/webmethods-b2b-examples/blob/master/receive-edi-as2/images/as2INChannel.png)

2. Create an outbound AS2 channel on Software AG B2B Cloud which is open for outbound AS2 communication to Partner ACME.

![](https://github.com/patelshashank/webmethods-b2b-examples/blob/master/receive-edi-as2/images/outChannel.png)

3. Create a partner profile. Before this the assumption is you have already created an Enterprise for your company to receive files. Setting an Enterprise is a one time setup

	ACME Partner setup 
	![](https://github.com/patelshashank/webmethods-b2b-examples/blob/master/receive-edi-as2/images/addpartner.png)

4. Add a EDI 850 and 855 business document. This process will create a document type in webMethods Flow Editor , so it will be easy for mappings to be done when a processing rule executes an Integration as part of the action
![](https://github.com/patelshashank/webmethods-b2b-examples/blob/master/receive-edi-as2/images/addbusinessdocument.png)

Follow the same step to add EDI 855 as well
![](https://github.com/patelshashank/webmethods-b2b-examples/blob/master/receive-edi-as2/images/addEDI850.png)


5. Create a processing rule to process inbound EDI 850 to identify the sender , receiver, document type and action

	Add Processing rule
![](https://github.com/patelshashank/webmethods-b2b-examples/blob/master/receive-edi-as2/images/processingRule.png)

	Give a name
![](https://github.com/patelshashank/webmethods-b2b-examples/blob/master/receive-edi-as2/images/addProcessingrule1.png)

	Associate sender/s
![](https://github.com/patelshashank/webmethods-b2b-examples/blob/master/receive-edi-as2/images/addProcessingrule2.png)

	Associate document/s
![](https://github.com/patelshashank/webmethods-b2b-examples/blob/master/receive-edi-as2/images/addProcessingrule3.png)

	Provide the name of Integration that you are going to create and expose on webmethods.io Integration in the next step
![](https://github.com/patelshashank/webmethods-b2b-examples/blob/master/receive-edi-as2/images/addProcessingrule4.png)


6. Create an Integration on webmethods.io Integration which parses EDI 850 and converts to XML and sends this XML document to backend Application. Then, receives a response from backend and submits EDI 855 back to B2B

	- Switch to flow editor
![](https://github.com/patelshashank/webmethods-b2b-examples/blob/master/receive-edi-as2/images/FlowEditor.png)

	- Click on Recipes and search for edi
	
	You will see receiveEDI850Send855B2BTransactions recipe. This recipe does the following
	*Receives an EDI 850 Purchase Order (PO) from a trading partner through the webMethods.io B2B product instance, performs service orchestrations to interpret the PO, and uses a back-end FTP server to generate the PO Acknowledgment, and routes it back to the trading partner through webMethods.io B2B product instance.*

![](https://github.com/patelshashank/webmethods-b2b-examples/blob/master/receive-edi-as2/images/recipe_edi.png)

	- Click on Use
	
	- Select a project and fill out the application details. If an application is not created , create a new application for each account. This flow assumes you have connectivity to an ftp server. if you dont have one , you can sign up for a free ftp server on https://hostedftp.com/ or https://DriveHQ.com
	
	- See the following code snippet
![](https://github.com/patelshashank/webmethods-b2b-examples/blob/master/receive-edi-as2/images/recipe.png)

	- After successful import
![](https://github.com/patelshashank/webmethods-b2b-examples/blob/master/receive-edi-as2/images/receiveEDI850Integration.png)

## Testing

1. Import the postname collection - <link here> and modify Message-Id in the Request header and submit it to the Inbound channel URL.
![](https://github.com/patelshashank/webmethods-b2b-examples/blob/master/receive-edi-as2/images/postman.png)

1. Log on to B2B Cloud and select the transactions to see the transaction entries as shown below
![](https://github.com/patelshashank/webmethods-b2b-examples/blob/master/receive-edi-as2/images/b2btransactions.png)