---

copyright:
  years: 2017, 2018
lastupdated: "2018-05-15"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Swagger API를 사용하여 네트워크 작성 또는 가입

{{site.data.keyword.blockchainfull}} Platform은 {{site.data.keyword.cloud_notm}}에서 블록체인 네트워크를 작성하거나 가입하는 데 사용할 수 있는 다수의 REST API를 노출합니다. 네트워크와 연관된 [Swagger UI](swagger_apis.html)를 사용하여 이러한 API를 사용해 볼 수 있습니다.
{:shortdesc}


## API에 대한 기본 인증 신임 정보 검색
{: #retrieve-id-token}

시작하기 전에 {{site.data.keyword.cloud_notm}}에서 스타터 플랜 또는 엔터프라이즈 플랜을 사용하여 [{{site.data.keyword.blockchainfull_notm}} Platform 서비스 인스턴스 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://console.bluemix.net/catalog/services/blockchain)를 작성해야 합니다.

Swagger API를 사용하여 네트워크를 작성하거나 가입하려면 {{site.data.keyword.cloud_notm}}의 서비스 인스턴스에 액세스할 수 있도록 하는 기본 인증 신임 정보가 필요합니다.

1. [{{site.data.keyword.cloud_notm}} 대시보드 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://console.bluemix.net/dashboard/apps/)에서 작성한 서비스 인스턴스를 여십시오.
2. 왼쪽 네비게이터에서 **서비스 신임 정보**를 클릭하십시오.
3. **서비스 신임 정보** 페이지에서 "새 신임 정보" 단추를 클릭하여 새 신임 정보를 작성하십시오. 신임 정보에 이름(예: *CreateJoin*)을 지정하고 **추가** 단추를 클릭하십시오. "인라인 구성 매개변수 추가" 필드에는 아무것도 입력할 필요가 없습니다. ![서비스 신임 정보 검색](../images/service_credentials.gif "서비스 신임 정보 검색")
4. 새 신임 정보가 작성된 후 이 신임 정보의 **조치** 헤더 아래에서 **신임 정보 보기**를 클릭하십시오. 신임 정보의 컨텐츠는 다음 예제와 유사합니다.

    ```
    {
      "service_instance_id": "60cc757d-1234-7866-9893-491cec2d0eaa",
      "service_instance_token": "T8-W0rHv_vEya63P8KcVUwxAXXbDmihGcDRD5AcHVXGn6S2jbxx2LikPz8w26c3k",
      "description": "This token can be used with the IBP APIs to create or join a network. It can only be used once."
    }
    ```
    {:codeblock}

    `service_instance_id`는 기본 인증 사용자 ID 역할을 하고 `service_instance_token`은 기본 인증 비밀번호 역할을 합니다. **네트워크 작성** 또는 **네트워크 가입** API를 호출할 때 이러한 값을 기본 인증 신임 정보로 사용하십시오.

    이전 단계에 따라 서비스 신임 정보를 작성하기 전에 블록체인 네트워크를 작성하는 경우 신임 정보의 컨텐츠에 네트워크 정보도 포함되며 다음 예제와 유사하게 보입니다.

    ```
    {
      "PeerOrg1": {
        "url": "https://ibmblockchain_xyz.ng.bluemix.net",
        "network_id": "92d511f7e587413c8a9848fdae595ef2",
        "key": "PeerOrg1",
        "secret": "T8eUA65l-qtznUHL10KzQ7IK-3BVWWfHu5-hpCiDdXCRQyNfeyIm1p5NT7g17l6U"
      }
    }
    ```
    {:codeblock}

    **참고**: **초대** API의 경우 `key`는 기본 인증 사용자 ID 역할을 하고 `secret`은 기본 인증 비밀번호 역할을 합니다.


## 사용 가능한 네트워크 위치 확인
{: #check-location}

사용 가능한 네트워크 위치에서만 API를 사용하여 블록체인 네트워크를 작성할 수 있습니다. 네트워크를 작성하기 전에 다음 API를 사용하여 사용 가능한 네트워크 위치를 확인하십시오.

```
/network-locations/available
```
{:codeblock}


## 네트워크 작성

**참고**: 스타터 플랜을 사용하는 경우 {{site.data.keyword.cloud_notm}}에서 블록체인 서비스 인스턴스를 작성할 때 기본 네트워크가 제공되므로 API를 사용하여 네트워크를 작성할 필요가 없습니다.

엔터프라이즈 플랜을 사용하는 경우 두 가지 단계를 완료하여 API로 네트워크를 작성해야 합니다.

1. 엔터프라이즈 플랜<!-- or Enterprise Plus Plan-->을 사용하여 {{site.data.keyword.cloud_notm}}에 블록체인 서비스 인스턴스를 작성하십시오. 기본 인증 사용자 이름 및 비밀번호인 서비스 인스턴스 ID 및 토큰을 검색하십시오. 자세한 정보는 [API에 대한 기본 인증 신임 정보 검색](#retrieve-id-token)을 참조하십시오.

2. 이러한 서비스 신임 정보를 사용하여 **네트워크 작성** API를 호출하십시오.

```
/networks
```
{:codeblock}

**매개변수**:
- `location_id`: 사용 가능한 네트워크 위치의 ID입니다. 자세한 정보는 [사용 가능한 네트워크 위치 확인](check-location)을 참조하십시오.
- `company_name`: 네트워크의 구성원으로서의 ID입니다.
- `email`: 알림을 받을 이메일 주소입니다.
- `peers`: 이 구성원에 대해 작성할 피어의 수입니다. 올바른 값은 0 - 6입니다. 나중에 네트워크 모니터 UI에서 구성원에 대한 피어를 작성할 수도 있습니다.
- `ledger_type`: 이 네트워크의 원장 유형입니다. 올바른 값은 levelDB 및 couchDB입니다. **levelDB**가 기본값입니다.


## 새 구성원을 네트워크에 초대

블록체인 네트워크를 작성한 후 네트워크에 가입하도록 다른 구성원을 초대할 수 있습니다. 가입하도록 새 구성원을 초대할 네트워크의 ID를 지정해야 합니다. 구성원을 초대하는 데 필요한 기본 인증 신임 정보는 **네트워크 작성** API에서 사용되는 신임 정보와 다릅니다. <!--In order to get the basic auth information you will need to follow the same steps in "Retrieving basic auth information for API". --> [Swagger UI](swagger_apis##retrieving-network-credentials)에서 **네트워크 신임 정보 검색** API를 사용하거나 {{site.data.keyword.cloud_notm}}의 서비스 인스턴스에서 [API에 대한 기본 인증 정보를 검색](#retrieve-id-token)하여 구성원을 초대하기 위한 신임 정보를 가져올 수 있습니다.

```
/networks/{networkID}/invite
```
{:codeblock}

**매개변수**:
- `email`: 네트워크에 초대할 구성원의 이메일 주소입니다.
- `company_name`: 네트워크에 가입할 구성원에 대해 정의하는 ID입니다. 초대된 구성원이 네트워크에 가입할 때 해당 ID를 변경할 수 있습니다.

초대된 구성원은 네트워크에 가입하는 방법에 대한 지시사항이 포함된 이메일 초대를 받습니다.


## 네트워크 가입

블록체인 네트워크에 가입하도록 초대되면 이메일 초대를 받습니다. 이메일의 지시사항에 따르거나 다음 API를 사용하여 네트워크에 가입할 수 있습니다.

**참고**: 네트워크에 가입하기 전에 {{site.data.keyword.blockchainfull_notm}} Platform 서비스 인스턴스를 작성하고 기본 인증 사용자 이름 및 비밀번호인 서비스 인스턴스 ID 및 토큰을 검색해야 합니다. 자세한 정보는 [API에 대한 기본 인증 정보 검색](#retrieve-id-token)을 참조하십시오. 가입할 네트워크의 ID를 지정해야 합니다.

```
/networks/{networkID}/join
```
{:codeblock}

**매개변수**:
- `company_name`: 네트워크의 구성원으로서의 ID입니다. 이 ID는 초대자가 지정한 이름을 대체합니다.
- `email`: 알림을 받을 이메일 주소입니다. 이는 초대 알림의 이메일 주소와 일치해야 합니다.
- `peers`: 이 구성원에 대해 작성할 피어의 수입니다. 올바른 값은 0 - 6입니다. 나중에 네트워크 모니터에서 구성원에 대한 피어를 작성할 수도 있습니다.

