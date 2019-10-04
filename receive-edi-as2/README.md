# Expose B2B Cloud for AS2 partners to exchange EDI messages

B2B Cloud can be exposed as an AS2 over HTTP channel and used to exchange EDI messages with Partners. This usecase is common for all Partners who has capability of exchanging EDI messages over AS2 which is a very popular protocol for EDI.

Contributors: Chaitanya Jadcherla, Mangat Rai , Shashank Patel


## Prerequisites
![](https://github.com/patelshashank/webmethods-b2b-examples/blob/master/receive-edi-as2/images/B2BLandingPage.png)

1. You need Software AG webmethods.io B2B cloud tenant and webmethods.io integration cloud tenant. If you don't have one; sign up for free 30 trial tenant at https://signup.softwareag.cloud/#/?product=b2b

1. Create your enterprise Partner profile on B2B Cloud. Provide identifiers to identify your Enterprise uniquely.

![](https://github.com/patelshashank/webmethods-b2b-examples/blob/master/receive-edi-as2/images/MyEnterprise.png)

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

3. Create a processing rule to process inbound EDI 850.

1. Create an Integration to receive the file and submit to B2B Cloud. Follow the steps as shown in the below code snippet
![](https://github.com/patelshashank/webmethods-b2b-examples/blob/master/receive-edi-as2/images/receiveFTPFile.png)
	 Add EDI application and set the operation to addGroupEnvelope. Refer to the following code snippet for mappings
![](https://github.com/patelshashank/webmethods-b2b-examples/blob/master/receive-edi-as2/images/mapaddgroupenvelopeinput.png)
	Add EDI application and set the operation to addInterchangeEnvelope. Refer to the following code snippet for mappings
![](https://github.com/patelshashank/webmethods-b2b-examples/blob/master/receive-edi-as2/images/mapInterchangeenvelopeinput.png)
	Add B2B application and set the operation to submit. Refer to the following code snippet for mappings
![](https://github.com/patelshashank/webmethods-b2b-examples/blob/master/receive-edi-as2/images/mapB2BSubmitinput.png)
1. Create a partner profile. Before this the assumption is you have already created an Enterprise for your company to receive files. here is a screenshot for of how an Enterprise should look like. Setting an Enterprise is a one time setup
![](https://github.com/patelshashank/webmethods-b2b-examples/blob/master/receive-edi-as2/images/enterprise.png)
Partner setup 
![](https://github.com/patelshashank/webmethods-b2b-examples/blob/master/receive-edi-as2/images/addpartner.png)
1. Add a EDI 850 and 855 business document. This process will create a document type in webMethods Flow Editor , so it will be easy for mappings to be done when a processing rule executes an Integration as part of the action
![](https://github.com/patelshashank/webmethods-b2b-examples/blob/master/receive-edi-as2/images/addbusinessdocument.png)
Follow the same step to add EDI 855 as well
![](https://github.com/patelshashank/webmethods-b2b-examples/blob/master/receive-edi-as2/images/addEDI850.png)
1. Now, before adding the processing rule in B2B Cloud, lets create an Integration in Flow Editor which allows you to map and process the EDI. This is where all the mappings and transformations are done and if needed the PO is sent to an ERP system like SAP for example
	- Switch to flow editor and click on recipes
	- Enter "edi" in the search box and press enter
	- You will see receiveEDI850Send855B2BTransactions recipe. This recipe does the following
*Receives an EDI 850 Purchase Order (PO) from a trading partner through the webMethods.io B2B product instance, performs service orchestrations to interpret the PO, and uses a back-end FTP server to generate the PO Acknowledgment, and routes it back to the trading partner through webMethods.io B2B product instance.*
	- Click on Use
	- Select a project and fill out the application details. If an application is not created , create a new application for each account. This flow assumes you have connectivity to an ftp server. if you dont have one , you can sign up for a free ftp server on https://hostedftp.com/ or https://DriveHQ.com
	- See the following code snippet
![](https://github.com/patelshashank/webmethods-b2b-examples/blob/master/receive-edi-as2/images/recipe.png)
1. Next, add a processing rule to identify the sender , receiver, and document type to take further action
![](https://github.com/patelshashank/webmethods-b2b-examples/blob/master/receive-edi-as2/images/processingRule.png)
![](https://github.com/patelshashank/webmethods-b2b-examples/blob/master/receive-edi-as2/images/addProcessingrule1.png)
![](https://github.com/patelshashank/webmethods-b2b-examples/blob/master/receive-edi-as2/images/addProcessingrule2.png)
![](https://github.com/patelshashank/webmethods-b2b-examples/blob/master/receive-edi-as2/images/addProcessingrule3.png)
![](https://github.com/patelshashank/webmethods-b2b-examples/blob/master/receive-edi-as2/images/addProcessingrule4.png)
## Testing

1. Now the setup is ready to be tested. Make sure you change the sender id and receiver id as you have configured in step 2.
Place an EDI file ( Sample attached here) in the ftp location configured and the integration created in step 1

<pre>ISA*00*          *00*          *01*987654321      *01*111111111      *161013*1141*U*00200*000000001*0*T*:! GS*PO*4405197800*999999999*20101127*1719*1421*X*004010! ST*850*00010! BEG*01*BK*08292233294**20101127*610385385! REF*DP*038! REF*PS*R! ITD*14*3*2**45**46! DTM*002*20101214! PKG*F*68***PALLETIZE SHIPMENT! PKG*F*66***REGULAR! TD5*A*92*P3**SEE XYZ RETAIL ROUTING GUIDE! N1*ST*XYZ RETAIL*9*0003947268292! N3*31875 SOLON RD! N4*SOLON*OH*44139! PO1*1*120*EA*9.25*TE*CB*065322-117*PR*RO*VN*AB3542! PID*F****SMALL WIDGET! PO4*4*4*EA*PLT94**3*LR*15*CT! PO1*2*220*EA*13.79*TE*CB*066850-116*PR*RO*VN*RD5322! PID*F****MEDIUM WIDGET! PO4*2*2*EA! PO1*3*126*EA*10.99*TE*CB*060733-110*PR*RO*VN*XY5266! PID*F****LARGE WIDGET! PO4*6*1*EA*PLT94**3*LR*12*CT! PO1*4*76*EA*4.35*TE*CB*065308-116*PR*RO*VN*VX2332! PID*F****NANO WIDGET! PO4*4*4*EA*PLT94**6*LR*19*CT! PO1*5*72*EA*7.5*TE*CB*065374-118*PR*RO*VN*RV0524! PID*F****BLUE WIDGET! PO4*4*4*EA! PO1*6*696*EA*9.55*TE*CB*067504-118*PR*RO*VN*DX1875! PID*F****ORANGE WIDGET! PO4*6*6*EA*PLT94**3*LR*10*CT! CTT*6! AMT*1*13045.94! SE*33*00010! GE*1*1421! IEA*1*000000001!</pre>
![](https://github.com/patelshashank/webmethods-b2b-examples/blob/master/receive-edi-as2/images/ftpplacefile.png)
![](https://github.com/patelshashank/webmethods-b2b-examples/blob/master/receive-edi-as2/images/ftp850855.png)
1.  Run the Integration created in step 1. Alternatively, you can schedule to integration run frequently to process the files in a near real time basis
1. Log on to B2B Cloud and select the transactions to see the transaction entries as shown below
![](https://github.com/patelshashank/webmethods-b2b-examples/blob/master/receive-edi-as2/images/b2btransactions.png)





