---

copyright:
  years: 2017, 2019
lastupdated: "2019-03-20"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# {{site.data.keyword.blockchainfull_notm}} Platform for AWS 시작하기
{: #remote-peer-aws}

다음 지시사항은 AWS(Amazon Web Services) 빠른 시작 템플리트를 사용하여 {{site.data.keyword.blockchainfull}}  Platform for AWS 피어를 작성한 후 {{site.data.keyword.blockchainfull_notm}} Platform의 네트워크에 연결하는 방법에 대해 설명합니다.
{:shortdesc}

AWS에 대한 자세한 정보는 [AWS 개요 문서 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://d1.awsstatic.com/whitepapers/aws-overview.pdf "AWS 개요 문서")를 참조하십시오.

{{site.data.keyword.blockchainfull_notm}} Platform for AWS를 사용하여 피어는 {{site.data.keyword.cloud_notm}}에서 기존 스타터 또는 엔터프라이즈 플랜 네트워크의 연결 프로파일, Hyperledger Fabric 인증 기관(CA) 및 순서 지정 서비스를 활용할 수 있습니다. 빠른 시작을 통해 AWS CloudFormation 템플리트를 사용하여 피어를 배치할 수 있습니다. 이 템플리트는 스타터 또는 엔터프라이즈 플랜 네트워크에 연결된 AWS 호스트 {{site.data.keyword.blockchainfull_notm}} Platform 피어를 빠르게 구성, 배치 및 실행하려는 IT 인프라 의사결정자 및 시스템 관리자를 대상으로 합니다. 템플리트를 사용하여 AWS에서 새 가상 프라이빗 클라우드(VPC)를 빌드하거나 피어를 기존 VPC에 배치할 수 있습니다.

빠른 시작으로 다음 구성이 완료됩니다.
 * 두 개의 가용성 구역을 포함하는 고가용성 아키텍처
 * AWS 우수 사례에 따라 공인 서브넷으로 구성된 VPC. 이는 AWS에서 공유한 가상 네트워크를 제공합니다.
 * 인터넷에 액세스를 허용하기 위한 인터넷 게이트웨이
 * 공인 서브넷에서 두 개의 가용성 구역(각 서브넷당 하나)에 걸쳐 있는 두 개의 피어
 * 각 공인 서브넷에서 임베디드 LevelDB 데이터베이스 또는 보조 CouchDB 컨테이너가 포함된 피어 컨테이너

{{site.data.keyword.blockchainfull_notm}} Platform for AWS 피어를 배치하기 전에 [피어 고려사항](/docs/services/blockchain/howto/remote_peer.html#remote-peer-aws-about-limitations)을 검토하십시오.

## 전제조건
{: #remote-peer-aws-prerequisites}

{{site.data.keyword.blockchainfull_notm}} Platform for AWS 피어(원격 피어)를 사용하려면 IBM Blockchain Platform에서 호스팅되는 블록체인 네트워크의 구성원인 조직이 있어야 합니다. 네트워크 인증 정보 및 네트워크의 API 엔드포인트에 액세스하려면 IBM Cloud에서 네트워크 모니터를 사용해야 합니다. 블록체인 네트워크의 구성원이 아니면 네트워크를 작성하거나 참여해야 합니다. 자세한 정보는 [네트워크 작성](/docs/services/blockchain/get_start.html#getting-started-with-enterprise-plan-create-network) 또는 [네트워크에 가입](/docs/services/blockchain/get_start.html#getting-started-with-enterprise-plan-join-nw)을 참조하십시오.

피어에 대한 기본 VPC 인스턴스 유형은 `m4.xlarge`입니다.  CPU, 메모리 및 스토리지 요구사항에 따라 선택한 인스턴스 유형을 최적화해야 합니다. 피어를 사용하려면 최소 다음과 같은 항목이 필요합니다.  
-	2x CPU
-	2GB RAM
-	체인코드용 4GB 공간
-	원장을 위한 10GB 공간(원장이 증가함에 따라 확장하는 기능 포함)

이 최소 리소스 레벨은 테스트 및 실험을 수행하는 데 충분합니다. 프로덕션 환경의 경우 충분히 큰 스토리지 용량(예: 100GB)을 할당하는 것이 중요합니다. 사용되는 스토리지 용량은 네트워크에 필요한 트랜잭션 수 및 시그니처 수에 따라 달라집니다. 피어의 스토리지가 고갈되는 경우 <!-- either expand the storage or --> 더 큰 파일 시스템을 사용하여 새 피어를 배치한 후 동일한 채널에 있는 다른 피어와 동기화해야 합니다.


## 배치 옵션
{: #remote-peer-aws-deploy-options}

빠른 시작의 경우 다음과 같은 두 가지 배치 옵션을 제공합니다.

* {{site.data.keyword.blockchainfull_notm}} Platform for AWS를 새 VPC에 배치합니다(엔드 투 엔드 배치). 이 옵션은 VPC, 서브넷, NAT 게이트웨이, 보안 그룹, 보안 호스트 및 기타 인프라 컴포넌트로 구성된 새 AWS 환경을 빌드한 후 이 새 VPC에 피어를 배치합니다.

* {{site.data.keyword.blockchainfull_notm}} Platform for AWS를 기존 VPC에 배치합니다. 이 옵션은 기존 AWS 인프라에 있는 {{site.data.keyword.blockchainfull_notm}} Platform for AWS 피어를 프로비저닝합니다. 이 옵션의 경우 이 안내서의 후반에 설명하는 내용에 따라 CIDR 블록, 인스턴스 유형 및 피어 설정을 구성할 수 있는 별도의 템플리트가 존재합니다.

## 1단계: AWS 계정 준비
{: #remote-peer-aws-account}

1. 아직 AWS 계정이 없는 경우 [여기 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://aws.amazon.com "https//aws/amazon.com")에서 화면의 지시사항에 따라 계정을 작성하십시오.

2. 탐색줄의 지역 선택기를 사용하여 AWS에 피어를 배치할 AWS 지역을 선택하십시오.

3. 선호하는 지역에서 키 쌍을 작성하십시오.

4. 필요한 경우 Amazon EC2 <유형> 인스턴스 유형에 대한 서비스 한계 증가를 요청하십시오. 이 인스턴스 유형을 사용하는 기존 배치가 이미 존재하는 상태에서 이 배치로 인해 기본 한계를 초과할 것으로 판단되는 경우 이 작업을 수행해야 합니다.

## 2단계: 원격 피어 구성 정보 검색
{: #remote-peer-aws-network-endpoints}

구성 중에 피어에 네트워크의 API 엔드포인트를 제공해야 합니다. 피어는 이러한 엔드포인트를 통해 {{site.data.keyword.blockchainfull_notm}} Platform의 네트워크를 찾아서 연결할 수 있습니다. 네트워크 모니터의 **개요** 화면에서 **원격 피어 구성** 단추를 클릭하십시오.

![원격 피어 구성](../images/myresources_starter.png "원격 피어 구성")
*그림 1. 원격 피어 구성 패널*

팝업 창이 열리고 다음 필드의 값이 표시됩니다. 다음 필드의 값을 저장하십시오. AWS 빠른 시작 템플리트를 사용하여 피어를 구성할 때 해당 값이 필요합니다.

- **조직 MSP**
- **인증 기관(CA) 이름**
- **인증 기관(CA) URL**
- **인증 기관(CA) TLS 인증서**

각각의 필드를 복사하여 직접 빠른 시작 템플리트에 붙여넣거나 **다운로드** 링크를 클릭하여 JSON 파일로 저장할 수 있습니다.

빠른 시작 템플리트를 사용하려면 TLS 인증서가 `\r\n` 행 바꾸기 형식이어야 합니다. `*NIX` OS에서 네트워크 모니터가
있는 브라우저를 사용하는 경우에는 UI에서 복사하는 인증서의 형식을 다시 지정해야 합니다. 모든 `\n`을 `\r\n`으로
바꾸고 결과 문자열을 `Certificate Authority (CA) TLS Certificate Chain`으로 레이블 지정된
필드로 붙여넣으십시오.
{:important}

**참고:** JSON 형식의 정보를 다운로드하는 경우 해당 정보를 피어에 제공하기 전에 TLS 인증서를 PEM 형식으로 변환해야 합니다. 다음 명령을 실행하여 다운로드한 JSON 파일의 **인증 기관(CA) TLS 인증서**를 PEM 형식으로 변환하십시오.
```
echo -e "<CERT>" > admin.pem
```
{:codeblock}

`<CERT>`를 **인증 기관(CA) TLS 인증서**의 값으로 대체하십시오. 그런 다음 빠른 시작 템플리트에서 **인증 기관(CA) TLS 인증서**를 요청하는 프롬프트가 표시되면 admin.pem 파일을 `cat`한 후 해당 컨텐츠를 복사하여 필드에 붙여넣으십시오.  

## 3단계: {{site.data.keyword.blockchainfull_notm}} Platform for AWS 피어 등록
{: #remote-peer-aws-register-peer}

{{site.data.keyword.blockchainfull_notm}} Platform for AWS 피어를 네트워크에 참여시키려면 먼저 {{site.data.keyword.blockchainfull_notm}} Platform의 네트워크에 새 피어 ID를 추가해야 합니다. 피어를 등록하려면 다음 단계를 완료하십시오.

**참고:** 고가용성의 경우 빠른 시작 템플리트에서 두 개의 가용성 구역에 두 개의 피어 노드를 작성합니다. 따라서 두 개의 등록 ID 및 시크릿이 필요합니다. **이 프로세스를 두 번 반복하여 두 개의 등록 ID 및 시크릿을 생성하십시오.**

1. {{site.data.keyword.blockchainfull_notm}} Platform의 네트워크에 대한 네트워크 모니터에 로그인하십시오. 네트워크 모니터의 "인증 기관" 화면에서 네트워크에 등록된 모든 ID(예: 관리자 또는 클라이언트 애플리케이션)를 확인할 수 있습니다.
  ![CA 화면](../images/CA_screen_starter.png "CA 화면")
  *그림 2. CA 화면*

2. 패널에서 **사용자 추가** 단추를 클릭하십시오. 팝업 화면이 열린 후 아래의 필드를 채우면 네트워크에 피어를 등록할 수 있습니다. **나중에 빠른 시작 템플리트에서 피어를 구성할 때를 대비하여 ID 및 시크릿의 값을 저장하십시오.**
  - **등록 ID:** 피어를 구성할 때 `enroll ID`로 참조되는 피어에 사용할 이름입니다. 나중에 사용하기 위해 **이 값을 저장**하십시오.
  - **등록 시크릿:** 피어를 구성할 때 `enroll Secret`으로 참조되는 피어에 사용할 비밀번호입니다. 나중에 사용하기 위해 **이 값을 저장**하십시오.
  - **유형:** 이 필드의 `peer`를 선택하십시오.
  - **소속:** 사용자 조직(예: `org1`)에서 피어가 속할 소속입니다. 새 소속을 지정하거나 기존 소속을 사용할 수 있습니다.
  - **최대 등록 수:** 이 ID를 사용하여 인증서를 등록하거나 생성할 수 있는 횟수를 제한하는 데 이 필드를 사용할 수 있습니다. 값을 지정하지 않으면 무제한 등록으로 기본값이 지정됩니다.

  필드를 채운 후 **제출**을 클릭하여 피어를 등록하십시오. 이제 등록된 피어가 네트워크 상의 ID로 테이블에 나열됩니다. 보안 조치로 각 ID 및 수반하는 enrollID 및 secret을 사용하여 하나의 피어만 배치하십시오. 피어 ID 및 비밀번호를 재사용하지 마십시오.

## 4단계: 빠른 시작 실행
{: #remote-peer-aws-launchqs}

이 빠른 시작 참조 배치를 실행하는 동안에 사용되는 AWS 서비스의 비용에 대해서는 사용자에게 책임이 있습니다. 이 빠른 시작을 사용하기 위한 추가 비용은 존재하지 않습니다. 자세한 정보는 이 빠른 시작에서 사용할 각각의 AWS 서비스에 대한 가격 페이지를 참조하십시오. 가격은 변경될 수 있습니다.

1. 다음 옵션 중 하나를 선택하여 AWS CloudFormation 템플리트를 AWS 계정으로 실행하십시오. 옵션을 선택하는 데 도움이 되는 정보는 이 안내서의 전반에 있는 배치 옵션을 참조하십시오. 각각의 배치를 완료하기 위해 약 10분 정도가 소요됩니다.  

  * [{{site.data.keyword.blockchainfull_notm}} Platform for AWS를 AWS의 새 VPC에 배치 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://fwd.aws/v43nk " {{site.data.keyword.blockchainfull_notm}} Platform for AWS를 AWS의 새 VPC에 배치")합니다.  

  * [{{site.data.keyword.blockchainfull_notm}} Platform for AWS를 AWS의 기존 VPC에 배치 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://fwd.aws/zrP4g " {{site.data.keyword.blockchainfull_notm}} Platform for AWS를 AWS의 기존 VPC에 배치")합니다.

  **중요:**     
  {{site.data.keyword.blockchainfull_notm}} Platform for AWS를 기존 VPC에 배치하는 경우 VPC에서 데이터베이스 인스턴스에 대한 서로 다른 가용성 구역에 두 개의 공용 서브넷이 있어야 합니다. 이러한 서브넷의 경우 인스턴스가 인터넷에 노출되지 않은 상태로 패키지 및 소프트웨어를 다운로드할 수 있도록 해당 라우트 테이블에 NAT 게이트웨이 또는 NAT 인스턴스가 있어야 합니다. Amazon VPC 문서에서 설명하는 것처럼 DHCP 옵션에 도메인 이름 옵션도 구성되어 있어야 합니다.  

  또한 기존 VPC에 연결된 보안 그룹을 작성한 후 이 보안 그룹에 포트 22 및 7051에 대한 인바운드 규칙을 추가해야 합니다. 포트 22의 TCP 연결은 SSH에서 생성된 인스턴스에 액세스할 수 있도록 해주며, 포트 7051의 TCP 연결은 외부 gRPC에서 피어 인스턴스에 액세스할 수 있도록 해줍니다(Fabric 도구 CLI 및 Fabric SDK를 사용하여 피어를 작동시키는 경우에 필요함). 빠른 시작을 실행할 때 이러한 VPC 설정을 요청하는 프롬프트가 표시됩니다.

2. 탐색줄의 오른쪽 상단 구성에 표시된 지역을 확인한 후 필요에 따라 변경하십시오. 이 지역은 피어를 위한 네트워크 인프라가 빌드될 위치입니다. 템플리트는 기본적으로 미국 동부(오하이오) 지역에서 실행됩니다.

3. 템플리트 선택 페이지에서 템플리트 URL에 대한 기본 설정을 그대로 유지한 후 `다음`을 선택하십시오.

4. 세부사항 지정 페이지에서 필요한 경우 스택 이름을 변경하십시오. 템플리트에 대한 매개변수를 검토하십시오. 입력이 필요한 매개변수의 값을 제공하십시오. 다른 모든 매개변수의 경우 기본 설정을 검토한 후 필요에 따라 사용자 정의하십시오. 매개변수 검토 및 사용자 정의가 완료되면 `다음`을 선택하십시오.

다음 표에서는 매개변수를 카테고리별로 나열한 후 두 가지 배치 옵션에 대해 별도로 설명합니다.

  * [{{site.data.keyword.blockchainfull_notm}} Platform for AWS를 새 VPC에 배치하기 위한 매개변수](/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws-parameters-newvpc)

  * [{{site.data.keyword.blockchainfull_notm}} Platform for AWS를 기존 VPC에 배치하기 위한 매개변수](/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws-parameters-existvpc)

### {{site.data.keyword.blockchainfull_notm}} Platform for AWS를 새 VPC에 배치하기 위한 매개변수
{: #remote-peer-aws-parameters-newvpc}

다음 표에는 AWS 차트의 구성 가능 매개변수 및 해당 기본값이 나열되어 있습니다. 모든 값이 필수입니다.

|매개변수    |설명 | 기본값 |
| --------------|-------------|---------|
| `Stack name` |스택 이름은 스택 목록에서 특정 스택을 찾을 수 있도록 해주는 ID입니다. 스택 이름에는 영숫자 문자(대소문자 구분) 및 하이픈만 포함시킬 수 있습니다. 스택 이름은 영문자로 시작되어야 하며 128자를 초과해서는 안됩니다.| |
| | | |
| **네트워크 구성** | |
| `Availability Zones` |VPC에서 서브넷에 사용할 두 개의 가용성 구역입니다. 참고: 논리 순서가 그대로 유지됩니다. | |
| `Allowed SSH access CIDR` | 외부 SSH에서 IBM Blockchain Peer 인스턴스에 액세스하기 위해 허용되는 [CIDR 블록 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Subnets.html#vpc-resize "VPC 및 서브넷")입니다. 모든 위치에서 액세스할 수 있도록 허용하는 경우 0.0.0.0/0으로 설정할 수 있습니다(권장하지 않음). | |
| `PeerEndpointAccessCIDR` | 외부 gRPC에서 IBM Blockchain Peer 인스턴스에 액세스하기 위해 허용되는 [CIDR ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Subnets.html#vpc-resize "VPC 및 서브넷") 블록입니다. 모든 위치에서 액세스할 수 있도록 허용하는 경우 일반적으로 0.0.0.0/0으로 설정합니다(권장하지 않음). | |
| | | |
| **Amazon EC2 구성** | | |
| `InstanceType` | 피어 인스턴스를 위한 EC2 인스턴스 유형입니다. | m4.xlarge |
| `KeyPairName` |AWS 지역 내에 있는 기존 EC2 키 쌍의 이름입니다. 이 항목은 반드시 생성해야 합니다. | |
| | | |
|** IBM Blockchian 구성** | |
| `IBMBlockchainVersion` | 배치할 IBM Blockchain 버전입니다. | 1.2.1 |
| `StateDatabase` | 블록체인 상태를 저장하기 위해 사용할 데이터베이스의 유형입니다. 이 선택사항은 나머지 네트워크에서 사용되는 상태 데이터베이스 유형과 일치해야 합니다. |CouchDB|
| `PeerVolumeSize` | 피어를 위한 지속적 데이터(원장, 상태 데이터베이스, MSP)를 저장하기 위해 사용되는 EBS 볼륨의 크기(GB)입니다. | 100 |
| `Peer 1 enroll ID`| IBM Blockchain Platform UI 인증 기관 패널에서 첫 번째 피어에 대해 입력한 등록 ID입니다. |  |
| `Peer 1 enroll secret` | IBM Blockchain Platform UI 인증 기관 패널에서 첫 번째 피어에 대해 입력한 등록 시크릿입니다. | |
| `Peer 2 enroll ID` | IBM Blockchain Platform UI 인증 기관 패널에서 두 번째 피어에 대해 입력한 등록 ID입니다. | |
| `Peer 2 enroll secret` | IBM Blockchain Platform UI 인증 기관 패널에서 두 번째 피어에 대해 입력한 등록 시크릿입니다. | |
| | | |
|**IBM Blockchain 서비스 인증 정보**| | |
| `Organization MSP` | 이 값은 IBM Blockchain Platform UI에서 찾을 수 있습니다. 개요 패널에서 원격 피어 구성 단추를 클릭한 후 해당 정보를 복사하여 여기에 붙여넣으십시오. | |
| `Certificate Authority (CA) Name` | 이 값은 IBM Blockchain Platform UI에서 찾을 수 있습니다. 개요 패널에서 원격 피어 구성 단추를 클릭한 후 해당 정보를 복사하여 여기에 붙여넣으십시오.| |
| `Certificate Authority (CA) URL` | 이 값은 IBM Blockchain Platform UI에서 찾을 수 있습니다. 개요 패널에서 원격 피어 구성 단추를 클릭한 후 해당 정보를 복사하여 여기에 붙여넣으십시오(포트 포함). 지정되지 않은 경우 기본 포트는 443입니다. | |
| `Certificate Authority (CA)  TLS Certificate`| 이 값은 IBM Blockchain Platform UI에서 찾을 수 있습니다. 개요 패널에서 원격 피어 구성 단추를 클릭한 후 해당 정보를 복사하여 여기에 붙여넣으십시오.| |
| | | |
|**기타 매개변수**| | |
| `QSS3BucketName` | 빠른 시작 자산을 위한 S3 버킷 이름입니다. 빠른 시작 버킷 이름에는 숫자, 소문자, 대문자 및 하이픈(-)을 포함시킬 수 있습니다. 하이픈(-)으로 시작되거나 끝나서는 안됩니다. | `aws-quickstart` |
| `QSS3KeyPrefix` | 빠른 시작 자산을 위한 S3 키 접두부입니다. 빠른 시작 키 접두부에는 숫자, 소문자, 대문자, 하이픈(-) 및 슬래시(/)를 포함시킬 수 있습니다. | `quickstart-ibm-fabric/` |

1. 옵션 페이지에서는 스택의 리소스에 대한 태그(키-값 쌍)를 지정하고 고급 옵션을 설정할 수 있습니다. 완료되면 다음을 선택하십시오.

2. 검토 페이지에서 템플리트 설정을 검토 및 확인하십시오. 기능에서 템플리트가 IAM 리소스를 작성하는 것을 수신확인하는 선택란을 선택하십시오.

3. 작성을 선택하여 스택을 배치하십시오.

4. 스택의 상태를 모니터링하십시오. 모든 스택의 상태가 `CREATE_COMPLETE`가 되면 피어 클러스터가 준비된 것입니다. 정상적으로 완료된 후 couchDB가 선택된 경우에는 네 개의 중첩 스택이 포함되고 levelDB가 선택된 경우에는 두 개의 중첩 스택이 포함된 루트 스택이 있어야 합니다.

5. 스택에 대한 출력 탭에 표시되는 정보를 사용하여 작성된 리소스를 확인하십시오.


### {{site.data.keyword.blockchainfull_notm}} Platform 피어를 기존 VPC에 배치하기 위한 매개변수
{: #remote-peer-aws-parameters-existvpc}

{{site.data.keyword.blockchainfull_notm}} Platform for AWS 피어를 기존 VPC에 배치하는 경우 다음과 같은 사항을 고려해야 합니다.

 - VPC에서 데이터베이스 인스턴스에 대한 서로 다른 가용성 구역에 두 개의 개인용 서브넷이 있어야 합니다. 이러한 서브넷의 경우 인스턴스가 인터넷에 노출되지 않은 상태로 패키지 및 소프트웨어를 다운로드할 수 있도록 해당 라우트 테이블에 NAT 게이트웨이 또는 NAT 인스턴스가 있어야 합니다.

 - [Amazon VPC 문서 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_DHCP_Options.html "DHCP 옵션 세트")의 설명에 따라 DHCP 옵션에 도메인 이름 옵션을 구성하십시오.  

- 기존 VPC에 연결된 보안 그룹을 작성한 후 이 보안 그룹에 포트 22 및 7051에 대한 인바운드 규칙을 추가하십시오. 포트 22의 TCP 연결은 SSH에서 생성된 인스턴스에 액세스할 수 있도록 해주며, 포트 7051의 TCP 연결은 외부 gRPC에서 피어 인스턴스에 액세스할 수 있도록 해줍니다(Fabric 도구 CLI 및 Fabric SDK를 사용하여 피어를 작동시키는 경우에 필요함). 빠른 시작을 실행할 때 이러한 VPC 설정을 요청하는 프롬프트가 표시됩니다.

 {{site.data.keyword.blockchainfull_notm}} Platform for AWS 피어를 기존 VPC에 배치하는 경우 다음 매개변수가 [상기](/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws-parameters-newvpc) 해당 섹션의 매개변수를 대체합니다.

|매개변수    |설명 | 기본값 |
| --------------|-------------|---------|
| **네트워크 구성** | | |
| `VPCID` |	배치할 기존 VPC의 ID입니다.| |
| `AvailabilityZone1` | 첫 번째 피어 노드를 배치할 가용성 구역입니다. | |
| `SubnetID1` |	첫 번째 피어 노드에 사용할 서브넷의 ID입니다. 서브넷은 선택한 VPC에 속해 있어야 합니다. VPC에 속한 서브넷을 찾으려면 AWS VPC 대시보드를 확인한 후 "서브넷" 메뉴를 선택하십시오.| |
| `AvailabilityZone2` | 두 번째 피어 노드를 배치할 가용성 구역입니다. | |
| `SubnetID2` |	두 번째 피어 노드에 사용할 서브넷의 ID입니다. 서브넷은 선택한 VPC에 속해 있어야 합니다. VPC에 속한 서브넷을 찾으려면 AWS VPC 대시보드를 확인한 후 "서브넷" 메뉴를 선택하십시오. | |
| | | |
| **Amazon EC2 구성**| | |
| `InstanceType` 	| 피어 인스턴스를 위한 EC2 인스턴스 유형입니다. 	| m4.xlarge |
| `KeyPairName` |	AWS 지역 내에 있는 기존 EC2 키 쌍의 이름입니다. 이 항목은 반드시 생성해야 합니다. | |
| `SecurityGroup` | AWS 지역 내에 있는 기존 EC2 보안 그룹의 ID입니다. 포트 22 및 7051에 대한 인바운드 TCP 연결을 허용해야 합니다. |	| |

## 5단계: 배치 테스트
{: #remote-peer-aws-test}

AWS CloudFormation 템플리트에서 정상적으로 스택을 작성하면 AWS 계정에서 두 개의 {{site.data.keyword.blockchainfull_notm}} Platform for AWS 피어 인스턴스가 실행됩니다. 이 인스턴스는 빠른 시작 템플리트에 지정된 `Organization MSP` 및 `Peer enroll id`를 조합하여 이름 지정됩니다. 예를 들면 `org1-remotepeer1`입니다.  

![AWS EC2 인스턴스의 피어](../images/remote_peer_AWS_EC2_instances.png "AWS EC2 인스턴스의 피어")  
*그림 3. AWS EC2 인스턴스의 피어*

피어가 실행 중인지 확인하려면 다음 작업을 수행하십시오.

  * AWS 콘솔에서 인스턴스를 선택(**서비스 > EC2 > 인스턴스**)한 후 **연결** 단추를 클릭하여 새로 작성된 VPC로 SSH 연결을 설정하십시오. AWS에서 `ssh` 명령을 실행하기 위한 지시사항을 수행하십시오.  
  * 명령행에서 `docker ps`를 실행하여 실행 중인 컨테이너를 확인하십시오. 각각의 가상 머신에는 피어 컨테이너 및 ecs-agent가 포함되어 있습니다. CouchDB를 원장 데이터베이스로 선택한 경우 CouchDB 컨테이너도 존재합니다.

  ```
  CONTAINER ID        IMAGE                                STATUS              PORTS                          NAMES
  fb3c49fe52fe        amazon/amazon-ecs-agent:latest       Created                                            ecs-agent
  667780cf3cd3        ibmblockchain/fabric-peer:1.2.1      Up                  0.0.0.0:7051->7051/tcp         peer
  2aa143c81027        ibmblockchain/fabric-couchdb:0.4.6   Up                  4369/tcp, 5984/tcp, 9100/tcp   couchdb
  ```

  * `docker exec -it peer sh`를 실행하여 피어 컨테이너 내에서 쉘 세션을 작성할 수 있습니다.

필요에 따라 {{site.data.keyword.blockchainfull_notm}} Platform 네트워크에
대한 피어 연결이 작동 중인지 확인하기 위해 피어 컨테이너 내에서 `peer channel fetch` CLI 명령을 실행할 수 있습니다. 또는
[피어 운영](/docs/services/blockchain/howto/remote_peer_operate_aws.html#remote-peer-aws-operate) 지시사항으로 건너뛸 수 있습니다.  

`peer channel fetch` CLI 명령을 실행하여 채널에서 최초 블록을 페치하십시오.

1. 네트워크 모니터의 개요 패널에서 사용 가능한 `연결 프로파일` 의 구성 정보를 검색하십시오. **연결 프로파일**을 클릭한 다음 **다운로드**를 클릭하십시오.

   - `orderers > url`에 있는 **순서 지정자**를 검색하여 순서 지정자 URL을 찾으십시오. 네트워크 이름으로 시작하는 URL을 기록해 두십시오. 이 URL은 다음 예와 비슷합니다.

   ```
    ash-zbc07b.4.secure.blockchain.ibm.com:21239
   ```

   - **조직**을 검색하여 조직의 이름을 찾으십시오. 이 조직은 피어를 등록하기 위해 사용한 것과 동일한 조직이어야 합니다. 연관된 `mspid`와 함께 조직의 이름을 찾을 수 있습니다. 이 값은 네트워크 모니터 개요 패널에서도 사용할 수 있습니다. **원격 피어 구성** 단추를 클릭하십시오. 이 값은 `Organization MSP` 아래에 표시됩니다. `mspid`의 값을 기록해 두십시오.

   - 아직 작성하지 않은 경우 `docker exec -it peer sh`를 실행하여 피어 컨테이너 내에서 쉘 세션을 작성할 수 있습니다.

   ```
   docker exec -it peer sh
   ```
   {:codeblock}

   - 연결 프로파일의 순서 지정자 TLS 인증서를 피어에 복사하십시오. **순서 지정자** 섹션으로 이동하십시오. -----BEGIN CERTIFICATE-----로 시작되어 -----END CERTIFICATE-----로 끝나는 "pem:" 다음에 오는 인증서를 복사하십시오. 따옴표를 포함하지 마십시오. `<orderer cert>`를 creds.json 파일에서 복사한 컨텐츠로 대체하고 명령행에서 다음 명령을 실행하십시오.

   ```
   echo -e "<orderer cert>" > /etc/hyperledger/<PEER_ENROLL_ID>/orderer_tlscacert.pem
   ```
   {:codeblock}

   `<PEER_ENROLL_ID>`를 빠른 시작 템플리트에 지정되어 있으며 이 인스턴스와 연관된 등록 ID로 대체하십시오.

2. 최초 블록을 페치하려면 먼저 네트워크의 채널에 조직을 추가해야 합니다.

  - 피어를 위한 새 채널을 시작할 수 있습니다. 채널 개시자로서 [채널 작성](/docs/services/blockchain/howto/create_channel.html#ibp-create-channel-creating-a-channel) 중에 조직을 자동으로 포함시킬 수 있습니다.

  - 블록체인 네트워크의 다른 멤버도 [채널 업데이트](/docs/services/blockchain/howto/create_channel.html#ibp-create-channel-updating-a-channel)를 사용하여 기존 채널에 조직을 추가할 수 있습니다.

  - 채널에 조직이 추가된 후에는 피어의 서명 인증서를 채널에 추가해야 합니다. 피어는 설치 중에 해당 서명 인증서를 업로드하므로 인증서를 채널과 동기화하기만 하면 됩니다. 네트워크 모니터의 "화면" 채널에서 조직이 가입하는 채널을 찾고 **조치** 헤더의 드롭 다운 목록에서 **인증서 동기화**를 선택하십시오. 이 조치는 채널에 있는 피어 전체에서 인증서를 동기화합니다.

3. 다음 명령을 실행하여 피어 컨테이너에서 환경 변수를 설정하십시오.

   ```
   export ORDERER_1=<ORDERER_URL>
   export CHANNEL=<CHANNEL_NAME>
   export ORGID=<ORGANIZATION_MSP_ID>
   export PEERADDR=<PEER_ADDR>
   ```
   {:codeblock}

   고유 정보로 필드를 바꾸십시오.
     - `<ORDERER_URL>`을 `creds.json` 파일의 순서 지정자 호스트 이름과 포트로 바꾸십시오.
     - `<CHANNEL_NAME>`을 피어가 가입할 채널의 이름으로 대체하십시오.
     - `<ORGANIZATION_MSP_ID>`를 `creds.json` 파일의 조직 이름으로 바꾸십시오.
     - `<PEER_ADDR>`을 `localhost:7051`로 대체하십시오.

   예를 들면 다음과 같습니다.

   ```
   export ORDERER_1=ash-zbc07b.4.secure.blockchain.ibm.com:21239
   export CHANNEL=defaultchannel
   export ORGID=PeerOrg1
   export PEERADDR=localhost:7051
   ```

4. 다음 피어 CLI 명령을 실행하여 채널의 최초 블록을 페치하십시오.

   **중요:** 다음 명령에서 표시되는 각각의 `<PEER_ENROLL_ID>`를 빠른 시작 템플리트에 지정되어 있는 해당 피어 인스턴스와 연관된 등록 ID로 대체하십시오. 이 값은 `ls /etc/hyperledger/` 명령을 실행하여 찾을 수 있습니다. 두 개의 폴더가 나열됩니다. 하나는 `fabric`이고, 다른 하나는 `<PEER_ENROLL_ID>`입니다.

   ```
   CORE_PEER_TLS_ROOTCERT_FILE=/etc/hyperledger/<PEER_ENROLL_ID>/tls/ca.crt CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/etc/hyperledger/<PEER_ENROLL_ID>/msp/ GOPATH=/ peer channel fetch 0 -o ${ORDERER_1} -c ${CHANNEL} --cafile /etc/hyperledger/<PEER_ENROLL_ID>/orderer_tlscacert.pem --tls
   ```
   {:codeblock}


   **참고:** 이러한 CLI 명령을 실행할 때 다음과 같이 무시해도 되는 경고가 발생할 수 있습니다.

   ```
   [msp] getPemMaterialFromDir -> WARN 001 Failed reading file
   /etc/hyperledger/<PEER_ENROLL_ID>/msp/intermediatecerts/<intermediate cert name>.pem: no pem content for file  /etc/hyperledger/<PEER_ENROLL_ID>/msp/intermediatecerts/<intermediate cert name>.pem
   ```

   다음 명령을 실행하여 명령이 올바르게 작동했으며 최초 블록이 피어 컨테이너에 추가되었는지 확인하십시오.

   ```
  ls *.block
   ```
   {:codeblock}

   다음 예와 비슷한 내용이 표시되면 최초 블록이 제대로 추가된 것입니다.

   ```
  defaultchannel_0.block
   ```

   축하합니다. {{site.data.keyword.blockchainfull_notm}} Platform for AWS 피어가 정상적으로 {{site.data.keyword.blockchainfull_notm}} Platform 네트워크에 연결되었습니다.

## FAQ
{: #remote-peer-aws-faq}

* **Q**. 빠른 시작을 실행할 때 CREATE_FAILED 오류가 발생합니다.
* **A**. AWS CloudFormation에서 스택을 작성하는 데 실패하는 경우 실패 시 롤백을 `No`로 설정한 상태로 템플리트를 재실행할 것을 권장합니다. (이 설정은 AWS CloudFormation 콘솔의 옵션 페이지에 있는 고급 아래에 있습니다.) 이 설정을 사용하는 경우 스택의 상태가 유지되고 인스턴스가 실행 중인 상태로 유지되므로 문제를 해결할 수 있습니다. (`%ProgramFiles%\Amazon\EC2ConfigService` 및 `C:\cfn\log`에 있는 로그 파일을 확인하십시오.)

  - 실패 시 롤백을 `No`로 설정하는 경우 이 스택에 대한
  AWS 비용이 계속 발생합니다. 문제점이 해결된 후에는 스택을 삭제해야 합니다. 추가 정보는 AWS 웹 사이트의 [AWS CloudFormation 문제점 해결 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/troubleshooting.html "AWS CloudFormation 문제점 해결")을 참조하십시오.

* **Q**. AWS Cloudformation 템플리트를 배치할 때 크기 제한 오류가 발생합니다.
* **A**. IBM에서 제공한 위치 또는 다른 S3 버킷에서 빠른 시작 템플리트를 실행할 것을 권장합니다. 컴퓨터의 로컬 사본에서 또는 비S3 위치에서 템플리트를 배치하는 경우 스택을 작성할 때 템플리트 크기 제한사항이 발생할 수 있습니다. AWS CloudFormation 제한사항에 대한 자세한 정보는 [AWS 문서 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cloudformation-limits.html "AWS CloudFormation 제한사항")을 참조하십시오.

## 다음에 수행할 작업
{: #remote-peer-aws-whats-next}

AWS에 피어가 설정된 후 트랜잭션을 제출하고 블록체인 네트워크의 분산 원장을 읽어들이기 전에 몇 가지 작업 단계를 완료할 수 있습니다. 자세한 정보는 [AWS에서 피어 운영](/docs/services/blockchain/howto/remote_peer_operate_aws.html#remote-peer-aws-operate)을 참조하십시오.

## 고가용성(HA)
{: #remote-peer-aws-high-availability}

기본적으로 빠른 시작 템플리트는 HA 지원을 위해 서로 다른 두 개의 가용성 구역에 두 개의 피어 인스턴스를 배치합니다.
이 HA 지원을 활용하려면 [고가용성을 위한 클라이언트 애플리케이션](/docs/services/blockchain/best_practices.html#best-practices-app-ha-app)도 구성해야 합니다.

## 보안 고려사항
{: #remote-peer-aws-security}

AWS Cloud는 고객이 애플리케이션 및 데이터를 신속하고 안전하게 배치할 수 있도록 확장 가능하고 신뢰성이 높은 플랫폼을 제공합니다. AWS 인프라에 시스템을 빌드하는 경우 보안 책임이 사용자와 AWS 사이에 공유됩니다. 이 공유 모델을 통해 AWS가 호스트 운영 체제 및 가상화 계층에서 컴포넌트를 작동, 관리 및 제어하기 위한 작동 부담을 서비스가 작동되는 기능의 실제 보안까지 감소시킬 수 있습니다. 따라서 사용자가 게스트 운영 체제(업데이트 및 보안 패치 포함), 연관된 기타 애플리케이션 및 AWS 제공 보안 그룹 방화벽의 구성에 대한 책임 및 관리를 담당합니다. AWS의 보안에 대한 자세한 정보를 확인하려면 [AWS Cloud 보안 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://aws.amazon.com/security/ "AWS Cloud 보안")을 방문하십시오.

### AWS IAM(Identity and Access Management)
{: #remote-peer-aws-iam}

이 솔루션은 가장 적은 액세스 권한이 포함된 IAM 역할을 활용합니다. SSH 키, 비밀 키 또는 액세스 키를 프로비저닝된 인스턴스에 저장하는 작업은 필요 없거나 권장하지 않습니다.

### OS 보안
{: #remote-peer-aws-ossecurity}

클러스터 노드의 루트 사용자는 배치 프로세스 중에 지정된 SSH 키를 사용해서만 액세스할 수 있습니다. AWS는 SSH 키를 저장하지 않으므로 해당 SSH 키를 잊어버리는 경우 이러한 인스턴스에 액세스하지 못할 수도 있습니다. 운영 체제 패치는 사용자의 책임이며 주기적으로 수행되어야 합니다.

### 보안 그룹
{: #remote-peer-aws-securitygroups}

보안 그룹은 하나 이상의 인스턴스에 대한 트래픽을 제어하는 방화벽으로 작동합니다. 인스턴스를 실행하면 하나 이상의 보안 그룹이 인스턴스와 연관됩니다. 각각의 보안 그룹에 연관된 인스턴스와 트래픽을 송수신할 수 있도록 허용하는 규칙을 추가할 수 있습니다. 언제든지 보안 그룹에 대한 규칙을 수정할 수 있습니다. 새 규칙은 해당 보안 그룹과 연관된 모든 인스턴스에 자동으로 적용됩니다. 작성한 후 이 솔루션에 속한 개별 인스턴스에 지정한 보안 그룹은 피어에 필요한 다양한 기능에 액세스할 수 있도록 허용되는 동안 최대한 제한됩니다. 클러스터가 작동 및 실행되면 보안 그룹을 검토하여 필요에 따라 액세스를 추가적으로 제한할 것을 권장합니다.

### 피어 보안
{: #remote-peer-aws-peer-security}

피어는 {{site.data.keyword.blockchainfull_notm}} Platform 외부에 배치되기 때문에 사용자가 피어의 보안을 관리해야 합니다. 여기에는 엔터프라이즈 플랜 네트워크에서 제공되는 중요한 보안 영역(예: 키 관리 및 데이터 암호화)이 포함되어 있습니다. 피어에 대한 보안을 고려할 때 다음 주제를 검토하십시오.

#### 데이터 보안
{: #remote-peer-aws-security-data}

{{site.data.keyword.blockchainfull_notm}} Platform 엔터프라이즈 플랜에서는 [대칭 키 암호화 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/support/knowledgecenter/en/SSB23S_1.1.0.14/gtps7/s7symm.html "대칭 키 암호화")를 기반으로 하는 전체 디스크 암호화를 사용하여 네트워크에서 사용하는 모든 데이터를 보호합니다. 피어 데이터를 보호하려면 자체 환경에서 유사한 단계를 수행해야 합니다.

levelDB를 사용하는지 또는 couchDB를 사용하는지 여부에 관계 없이 상태 데이터베이스의 데이터는 암호화되지 않습니다. 애플리케이션 레벨 암호화를 사용하여 상태 데이터베이스에 있는 저장 데이터를 보호할 수 있습니다.

#### 데이터 상주
{: #remote-peer-aws-data-residency}

데이터 상주를 사용하는 경우 모든 블록체인 원장 데이터의 처리 및 저장이 단일 국가의 경계 내에서 수행되어야 합니다.
수행 방법에 대한 자세한 정보는 [데이터 상주](/docs/services/blockchain/howto/remote_peer.html#remote-peer-aws-about-data-residency)를 참조하십시오.

#### 키 관리
{: #remote-peer-aws-security-key-management}

키 관리는 피어 보안 측면에서 매우 중요한 요소입니다. 개인 키가 손상되거나 유실되는 경우 적대적인 액터가 피어의 데이터 및 기능에 액세스할 수도 있습니다. {{site.data.keyword.blockchainfull_notm}} Platform 엔터프라이즈 플랜에서는 HSM([Hardware Security Modules](/docs/services/blockchain/glossary.html#glossary-hsm))을 사용하여 네트워크의 개인 키를 저장합니다. HSM은 다른 당사자가 개인 키에 액세스하지 못하게 하는 물리적 어플라이언스입니다.

AWS에 피어를 배치하는 경우 사용자가 개인 키를 관리해야 합니다. {{site.data.keyword.blockchainfull_notm}} Platform에서 개인 키를 생성해도 해당 키는 Platform에 저장되지 않습니다. 키가 손상되지 않도록 안전한 위치에 저장하는 것이 중요합니다. 피어의 개인 키는 피어 MSP의 키 저장소 폴더(피어 컨테이너 내에 있는 `/etc/hyperledger/<PEER_ENROLL_ID>/msp/keystore/` 디렉토리)에 있습니다. 피어 내에 있는 인증서에 대한 자세한 정보를 확인하려면 [{{site.data.keyword.blockchainfull_notm}} Platform에서 인증서 관리](/docs/services/blockchain/certificates.html#managing-certificates) 주제의 [MSP(Membership Services Provider)](/docs/services/blockchain/certificates.html#managing-certificates-msp) 절을 참조하십시오.

Key Escrow를 사용하여 유실된 개인 키를 복구할 수 있습니다. 이 작업은 키 유실 전에 수행해야 합니다. 개인 키를 복구할 수 없는 경우 인증 기관에서 새 signCert를 받아서 새 개인 키를 가져와야 합니다. 또한 가입한 모든 채널에서 관리자 인증서를 제거하고 대체해야 합니다.

<!---
In {{site.data.keyword.blockchainfull_notm}} Platform when a private key is created, two sets of independent key material is generated, in custody of two different entities. Those two sets of key materials are then combined to create the private key.
--->

#### TLS
{: #remote-peer-aws-security-tls}

[전송 계층 보안 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/support/knowledgecenter/en/SSFKSJ_7.1.0/com.ibm.mq.doc/sy10660_.htm "SSL 또는 TLS 핸드쉐이크 개요")(TLS)은 Hyperledger Fabric의 신뢰 모델에 임베드됩니다. {{site.data.keyword.blockchainfull_notm}} Platform의 모든 컴포넌트에서는 TLS를 사용하여 서로 인증하고 통신합니다. 따라서 {{site.data.keyword.blockchainfull_notm}} Platform의 네트워크 컴포넌트는 피어와의 TLS 핸드쉐이크를 완료할 수 있어야 합니다. 이로 인한 영향 중 하나는 예를 들면 클라이언트 앱에서 피어로의 방화벽에서 화이트리스트를 사용하여 패스스루를 사용으로 설정해야 한다는 것입니다.


#### Membership Services Provider 구성
{: #remote-peer-aws-security-MSP}

IBM Blockchain Platform의 컴포넌트는 MSP(Membership Service Provider)를 통해 ID를 이용합니다. MSP는 CA에서 발행하는 인증서를 네트워크 및 채널 역할과 연관시킵니다. 피어에서 MSP가 작동하는 방법에 대한 자세한 정보는 이 [주제](/docs/services/blockchain/certificates.html#managing-certificates-msp)를 참조하십시오.

#### 애플리케이션 보안
{: #remote-peer-aws-security-appl}

모든 체인코드 호출에 서명되었으므로 Fabric에서 애플리케이션 보안을 관리합니다. Fabric에는 ACL 기반 애플리케이션 레벨 검사도 포함됩니다.

## 라이센스 및 가격
{: #remote-peer-aws-license-pricing-aws}

빠른 시작을 통해 사용 가능해진 배치 솔루션을 사용하려면 {{site.data.keyword.blockchainfull_notm}} Platform for AWS의 커뮤니티 에디션 라이센스 버전에 동의해야 합니다. {{site.data.keyword.blockchainfull_notm}} Platform for AWS(빠른 시작 오퍼링을 통해 제공되는 모든 패키지 및 이로부터 파생되는 패키지 포함)의 사용은 프로덕션 사용을 위한 것이 아닙니다. IBM은 언제든지 코드에 대한 액세스 권한 및 해당 코드의 사용 권한을 취소하도록 결정할 수 있습니다.
{{site.data.keyword.blockchainfull_notm}} Platform for AWS 소프트웨어 라이센스 계약에는 라이센스 이용 약관에 대한 자세한 정보가 포함되어 있습니다. 빠른 시작을 시작하면 사용자에게 계약 조항을 읽고 동의하도록 요청합니다.
