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
1. Create an Integration to receive the file and submit to B2B Cloud
!https://github.com/krishnajc/webmethods-b2b-examples/blob/master/receiveEDI-FTP/images/receiveFTPFile.png
