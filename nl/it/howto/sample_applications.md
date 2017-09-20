---

copyright:
  years: 2017
lastupdated: "2017-08-14"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Sample applications

You can deploy and test sample applications to get a better understanding on {{site.data.keyword.blockchainfull}} network and application development.
{:shortdesc}

## Before you begin

Ensure that you install all software prerequisites on your local file system.  For more information, see [Setting up application development environment](../v10_application.html#setting-up-application-development-environment).

You also need to have a {{site.data.keyword.blockchain}} network on {{site.data.keyword.Bluemix_short}} with a channel and its peers configured.  For more information, see [Govern the Network](../get_start.html).  When your network is ready to use, retrieve API endpoints of your network resources that your application will access.  For more information, see [Adding network service credentials to your application](../v10_application.html#adding-network-service-credentials-to-your-application).


## Marbles application

In the Marbles application, multiple users can create marbles with different properties and transfer them with others.  The Marbles application is writen in Javascript and the chaincode is writen in Go.

You can find the sample code and instructions in [Marbles in GitHub ![External link icon](../images/external_link.svg "External link icon")](https://github.com/IBM-Blockchain/marbles).


## Fabric car application

In the Fabric car application, you can perform **query** and **update** on car records in the ledger.  The Fabric car application is writen in Javascript and the chaincode is writen in Go.

You can find sample code in [Fabric car in GitHub ![External link icon](../images/external_link.svg "External link icon")](https://github.com/hyperledger/fabric-samples/tree/release/fabcar), and instruction in [Writing Your First Application ![External link icon](../images/external_link.svg "External link icon")](http://hyperledger-fabric.readthedocs.io/en/latest/write_first_app.html).

<!-- 
## High available application
-->
<!--
The high available application demonstrate how to enable the following features to ensure the high availability of a {{site.data.keyword.blockchain}} network.
1. Have 2 peers and have your application smart enough to talk to one and if it is getting errors or no response switch over to the other.
2. Same for orderers, 2 or 3 and have your application smart enough to fail over if needed.
OR put orderers/peers behind a load balancer.
-->
