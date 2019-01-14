---

copyright:
  years: 2018
lastupdated: "2018-08-31"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# 샘플 애플리케이션 배치
{: #overview}


***[이 페이지가 도움이 되었습니까? 알려주십시오.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


사용자가 블록체인 네트워크 및 애플리케이션 개발을 이해할 수 있도록 {{site.data.keyword.blockchainfull}} Platform은 사용자가 배치하고 테스트할 수 있는 샘플 애플리케이션을 제공합니다.
{: shortdesc}

| 샘플     | 유스 케이스       | 샘플 유형  | 작성자 및 지원  |
| --------------|---------------------|----|-------|
| [Marbles ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://github.com/IBM-Blockchain/marbles "Marbles")| 여러 사용자가 서로 다른 특성으로 Marbles를 작성하고 다른 사용자에게 전송할 수 있습니다. <br> <br> 지시사항에 따라 {{site.data.keyword.cloud_notm}}의 {{site.data.keyword.blockchainfull_notm}} 서비스에 Marbles를 배치하십시오. 화면 캡처는 스타터 플랜이 아닌 엔터프라이즈 플랜 UI를 보여줍니다. 기능은 유사합니다. | Javascript 애플리케이션의 GO 체인코드|IBM<br> [도움말 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://github.com/IBM-Blockchain/marbles/issues "도움말") |
|[자동차 제조업체 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://github.com/IBM-Blockchain/vehicle-manufacture "자동차 제조업체") | 사용자는 가상 차량의 라이프사이클을 살펴볼 수 있습니다.  <br> <br> 네트워크에 샘플 배치에 대한 단계별 안내서의 지시사항에 따라 **{{site.data.keyword.cloud_notm}}에 이 데모를 수동으로 배치**하십시오.|Javascript 애플리케이션의 [Hyperledger Composer](../references/hyperledger_composer.html) 비즈니스 네트워크 정의 |IBM<br> [도움말 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://github.com/IBM-Blockchain/vehicle-manufacture/issues "도움말") |
|[부패 상품![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://github.com/clauseHQ/demo-clause-ibm-perishable-goods "부패 상품")| 사용자는 배송과 온도를 기준으로 공급 체인에 상품을 구매하는 합법적인 계약을 배치할 수 있습니다. <br>  <br> <!-- This sample leverages the [Toolchain tool service![External link icon](../images/external_link.svg "External link icon")](../images/external_link.svg "External link icon")](https://console.bluemix.net/docs/services/ContinuousDelivery/index.html) to deploy the sample on your network automatically.--> |Javascript 애플리케이션의 [Hyperledger Composer](../references/hyperledger_composer.html) 비즈니스 네트워크 정의 | [절 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](http://clause.io/ "절")<br> [도움말 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://github.com/clauseHQ/demo-clause-ibm-perishable-goods/issues "도움말") |

IBM 및 커뮤니티 파트너가 샘플 애플리케이션을 작성합니다. 샘플에 대한 지원은 해당 Github 저장소를 통해 제공됩니다. 문제점이 발생하면 공개자 이름 옆에 있는 **도움 받기** 링크를 클릭하고 Github 저장소에서 문제를 열어 관련 팀에게 알리십시오.

샘플을 배치하려면 스타터 플랜 또는 엔터프라이즈 플랜에서 {{site.data.keyword.cloud_notm}}에 블록체인 네트워크가 필요하며 채널 및 해당 피어를 구성합니다. 자세한 정보는 [스타터 플랜 네트워크 통제](../get_start_starter_plan.html) 및 [엔터프라이즈 플랜 네트워크 통제](../get_start.html)를 참조하십시오.


<!--

After you provision a Starter Plan network, you can deploy the sample applications in the Network Monitor, which automates the steps to enable sample applications to run on your network. You can also enable the samples step-by-step to learn the entire process of application deployment, which you need to follow when you deploy your own applications.

-->

<!--
## Deploying sample applications in Starter Plan

Starter Plan provides a simple approach to deploy sample applications by leveraging the Toolchain service on {{site.data.keyword.cloud_notm}} with just a few clicks. After you deploy and launch a sample application, it will run on your blockchain network automatically.

Starter Plan provides two sample applications for you to start with.

* **Marbles**

  The Marbles sample enables users to create virtual marbles with different properties and to transfer them with other users. For more information about Marbles, see [Marbles Demo ![External link icon](../images/external_link.svg "External link icon")](https://github.com/IBM-Blockchain/marbles).


* **Vehicle Manufacture**

  The Vehicle Manufacture sample enables users to go through the lifecycle of a virtual vehicle. For more information about this sample, see [Vehicle Manufacture ![External link icon](../images/external_link.svg "External link icon")](https://github.com/IBM-Blockchain/vehicle-manufacture).

Complete the following steps to deploy a sample application:

1. Enter the **Network Monitor** of your Starter Plan network. If you don't have one, see [Creating a network](../get_start_starter_plan.html#creating-a-network).

2. Open the "Try samples" screen in your Network Monitor. Choose the sample application that you want to deploy and click the **Deploy via Toolchain** button.

3. A Toolchain service configuration window opens. Ensure that all required tools are correctly integrated. Note that if you have more than one organizations, make sure that you enter the correct organization name. The organization name should be the email address that you use to sign up for the network.
    **Tip**: You must disable pop-up blockers so that the Toolchain service configuration page can open.


  If this is the first application that you deploy via Toolchain, you need to authorize Toolchain to access the GitHub repository.

  ![sampleappflow2](../images/sampleappflow2.png)

  After you click the "Authorize" button, you are taken to GitHub. If you don't have a GitHub account, you need to create one. Give Toolchain access to your repositories by entering your account information. If you don't want to give Toolchain this access, you can deploy the sample applications manually. For more information, see [Deploying sample applications manually](#deploy_sample_applications_manually).

5. Click the **Create** button at the bottom of the Toolchain page. This should take you back to the Network Monitor, where the deployment of Marbles should be in process. This process should take five to 10 minutes.

After the deployment completes, you can start to use the Marbles sample on your Starter Plan network.

Because this process creates a forked GitHub repository that you have access to and control over, you can make changes to Marbles in the forked repository and commit them. These commits will trigger an automatic build of your Marbles application and allow you to demo it in {{site.data.keyword.cloud_notm}}.
-->

<!--
## Deploying sample applications manually
{: #deploy_sample_applications_manually}

If you want to deploy sample applications without using the Network Monitor, ensure that you install all software prerequisites on your local file system. For more information, see [Setting up application development environment](../v10_application.html#setting-up-application-development-environment).

You also need a blockchain network on {{site.data.keyword.cloud_notm}} with either Starter Plan or Enterprise Plan, and configure a channel and its peers. For more information, see [Govern Starter Plan network](../get_start_starter_plan.html) and [Govern Enterprise Plan network](../get_start.html). After you provision a network and can deploy applications on it, retrieve the API endpoints of your network resources that your application will access. For more information, see [Adding network API endpoints to your application](../v10_application.html#adding-network-api-endpoints-to-your-application).

You can deploy one of the following sample applications to your network:

- **Marbles**

  In the Marbles application, multiple users can create marbles with different properties and transfer them to others. The Marbles application is written in JavaScript and the chaincode is written in Go.

  You can find the sample code and instructions in [Marbles in GitHub ![External link icon](../images/external_link.svg "External link icon")](https://github.com/IBM-Blockchain/marbles).

  Use the Bluemix instructions rather than the instructions for hosting Marbles locally and input the relevant information from your network. Note that the screen captures in the Marbles GitHub show the Enterprise Plan UI (since the Enterprise Plan supports only the manual path for deploying Marbles), which is a little different from the Starter Plan UI. Nevertheless both UIs have the same basic parts, and you can find the names of your peers, channels, and other service credential information in the appropriate screens.

- **Fabcar**

  In Fabcar, you can perform **queries** and **ledger updates** on car records in the ledger. Fabcar is written in JavaScript and the chaincode is written in Go.

  You can find sample code in [Fabric car in GitHub ![External link icon](../images/external_link.svg "External link icon")](https://github.com/hyperledger/fabric-samples/tree/release/fabcar), and instruction in [Writing Your First Application ![External link icon](../images/external_link.svg "External link icon")](http://hyperledger-fabric.readthedocs.io/en/latest/write_first_app.html).

- **Other applications**

  For more information about how to host your own applications inside {{site.data.keyword.cloud_notm}}, see [Hosting applications](../v10_application.html#hosting-applications).

-->

<!--
## Deleting a sample application

To delete a sample application that was acquired through the Toolchain process, navigate to where the sample lives in the UI. Because sample applications are instantiated on a channel, you can find the sample in channels. Click **Channels** on the left navigation to open the "Channels" screen. Click the relevant channel on which the sample is instantiated, and then click **Chaincode**. This displays chaincode that are instantiated on this channel.

If you click the chaincode of your application, you can see a **Delete** tab. However, clicking **Delete** alone does not delete the sample application, but delete only the chaincode container.  You also need to navigate to the {{site.data.keyword.cloud_notm}} dashboard and the Toolchain dashboard to delete the sample there.

-->
