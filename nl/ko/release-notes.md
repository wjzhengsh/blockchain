---

copyright:
  years: 2019
lastupdated: "2019-05-16"

keywords: release note, latest changes, Hyperledger Fabric

subcollection: blockchain

---

{:new_window: target="_blank"}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:shortdesc: .shortdesc}
{:pre: .pre}

# 릴리스 정보
{: #release-notes-saas-20}

이 릴리스 정보를 사용하여 Hyperledger Fabric v1.4.1을 기반으로 빌드된 {{site.data.keyword.blockchainfull}} Platform on {{site.data.keyword.cloud_notm}}의 최신 변경사항에 대해 알아보십시오.
{:shortdesc}


## 2019년 5월 9일
{: #05-09-2019}

**{{site.data.keyword.blockchainfull_notm}} Platform 콘솔 API 소개**

이제 API를 사용하여 피어, 순서 지정자 및 CA 노드를 프로비저닝, 편집 및 삭제할 수 있으므로 블록체인 네트워크 빌드를 스크립팅할 수 있습니다. API에 대해 자세히 알아보고 사용해 보려면 [{{site.data.keyword.cloud_notm}} API 문서 저장소 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](/apidocs/blockchain#introduction "소개")의 문서를 사용하십시오. 또한 API를 사용하여 네트워크를 빌드하는 방법에 대한 지시사항은 [API를 사용하여 네트워크 빌드](/docs/services/blockchain?topic=blockchain-ibp-v2-apis) 주제를 참조하십시오.  

**채널 통제**  

채널 통제 업데이트를 통해 정책을 재구성할 수 있습니다. 채널 업데이트에 서명해야 하는 채널 구성원을 제어할 수 있고 서명 콜렉션을 조정할 수도 있습니다.

## 2019년 4월 17일
{: #04-17-2019}

**노드 리소스의 크기를 지정하고 스케일링하는 기능**  

노드를 배치하면 이제 컨테이너에 CPU, 메모리 및 스토리지를 지정할 수 있는 기능이 제공됩니다(해당하는 경우). 사용 패턴에 따라 나중에 리소스를 위 또는 아래로 스케일링할 수 있습니다. 자세한 정보는 [리소스 할당](/docs/services/blockchain?topic=blockchain-ibp-console-govern#ibp-console-govern-allocate-resources)을 참조하십시오.

**IAM IBM Cloud Identity and Access Management(IAM) 사용**  

IAM은 UI에서 수행할 수 있는 조치를 제한할 뿐만 아니라 콘솔 UI에 액세스할 수 있는 사용자 액세스 권한을 제어하는 데 사용됩니다.  [콘솔에서 사용자 추가 및 제거](/docs/services/blockchain?topic=blockchain-ibp-console-manage-console#ibp-console-manage-console-add-remove)하는 방법에 대한 정보는 이 주제를 참조하십시오.

## 2019년 4월 3일
{: #04-03-2019}

**외부 CA에 대한 지원**

피어 또는 순서 지정자 노드를 추가하는 경우 {{site.data.keyword.IBM_notm}}에서 호스팅되지 않는 외부 CA에서 인증서를 사용할 수 있는 옵션이 제공됩니다. 자세한 정보는 [피어 또는 순서 지정자로 외부 CA에서 인증서 사용](/docs/services/blockchain?topic=blockchain-ibp-console-build-network#ibp-console-build-network-third-party-ca)에 대한 주제를 참조하십시오.

**순서 지정자 성능 조정**

순서 지정자 처리량 및 성능에 대한 제어를 강화하기 위해 콘솔에서 매개변수를 조정하는 새 순서 지정자를 사용할 수 있습니다. 매개변수 구성에 대한 지시사항은 [순서 지정자 조정](/docs/services/blockchain?topic=blockchain-ibp-console-govern#ibp-console-govern-orderer-tuning)에 대한 주제를 참조하십시오.
