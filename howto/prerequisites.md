---

copyright:
  years: 2017
lastupdated: "2017-07-11"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Setting up application development environment
{: #prerequisites}

You need to install software prerequisites to develop and then deploy your applications to the blockchain network on {{site.data.keyword.Bluemix}}.
{:shortdesc}

*	Git ([Git download page ![External link icon](../images/external_link.svg "External link icon")](https://git-scm.com/downloads){:new_window})
	
	Git is a version control tool to familiarize yourself with, both for chaincode development and software development in general.  Also, git bash, which is installed with git on Windows, is an excellent alternative to the the Windows command prompt.
 
	Use the following command to verify your Git installation.  You should see an output similar to the below:  
	```
	$ git --version
	git version 2.11.1.windows.1
	```
	{:screen}

*	GoLang ([GoLang download page ![External link icon](../images/external_link.svg "External link icon")](https://golang.org/dl){:new_window})

	The GoLang installation installs a set of Go CLI tools, which are very useful to write chaincode.  For example, the `go build` command allows you to check that your chaincode actually compiles before you attempt to deploy it to a network.  At time of writing, this chaincode is known to build successfully with version `1.7.5`.

	Use the following command to verify your GoLang version.  You should see an output similar to the below:
	```
	$ go version
	go version go1.7.5 windows/amd64
	```
	{:screen}
	
	Follow the [Installation instructions ![External link icon](../images/external_link.svg "External link icon")](https://golang.org/doc/install){:new_window} to properly set the environment variables.  Check your `GOPATH` using the below command.  Note that your `GOPATH` does not need to match the example, it only matters that you have this variable set to a valid directory on your filesystem.   
	```
	$ echo $GOPATH
	C:\gopath
	```
	{:screen}

	You can then verify your GoLang installation by building GoLang code with the [hello world ![External link icon](../images/external_link.svg "External link icon")](https://golang.org/doc/install#testing){:new_window} example.

*	Node.js ([Node.js download page ![External link icon](../images/external_link.svg "External link icon")](https://nodejs.org/en/download/){:new_window}).  Choose a version between 6.9.5 < 7.  Node versions greater than 7 will cause errors when downloading the SDK modules.  

	Use the following commands to verify your Node.js installation.  You should see an output similar to the below:  
	```
	$ node -v
	v6.10.1
	
	$ npm -v
	3.10.10
	```
	{:screen}
