---

copyright:
  years: 2019
lastupdated: "2019-05-16"

keywords: logging levels, metrics, health check, peer, orderer

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

# 오퍼레이션 서비스를 사용하여 노드 운영
{: #operations_service}

{{site.data.keyword.blockchainfull}} Platform v2.0은 Hyperledger Fabric v1.4.1을 기반으로 합니다. 이 플랫폼은 운영자가 노드 상태 검사를 수행하고, 피어 및 순서 지정자 노드에서 운영 메트릭을 가져오고, 로깅 레벨을 관리하기 위한 RESTful "오퍼레이션" API를 제공하는 오퍼레이션 서비스 기능을 지원합니다. 피어 및 순서 지정자 호스트는 RESTful "오퍼레이션" API를 제공하는 HTTP 서버를 호스팅합니다. 오퍼레이션 서비스에 대한 자세한 정보는 [오퍼레이션 서비스 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/operations_service.html "오퍼레이션 서비스"){:new_window}를 참조하십시오.
{:shortdesc}


## 고려사항 및 제한사항
{: #operations_service_consideration_limitation}

- 모든 피어 및 순서 지정자 노드는 상태 검사기에 액세스할 수 있도록 `clientAuthRequired: false`로 구성됩니다. `clientAuthRequired`가 `false`로 설정되고 상호 TLS가 사용으로 설정되었으므로 인증할 수 있으려면 REST 서버에 액세스할 때 TLS ID를 전달해야 합니다. 이 설정은 적절한 키가 있는 사용자만 해당 로그를 볼 수 있도록 합니다.
- 현재 [Prometheus ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/operations_service.html#prometheus)를 기반으로 모델을 가져오는 메트릭만 지원됩니다.

## 시작하기 전에
{: #operations_service_before_you_begin}

피어 및 순서 지정자로부터 다음 정보를 수집해야 합니다.

- **`peer-endpoint`** 또는 **`orderer-endpoint`**

  콘솔에서 내보내는 피어 또는 순서 지정자 JSON 파일에서 피어 또는 순서 지정자의 오퍼레이션 엔드포인트 URL을 찾을 수 있습니다.

    1. **노드** 탭에서 피어 또는 순서 지정자를 클릭하십시오.
    2. 피어 또는 순서 지정자 페이지에서 피어 또는 순서 지정자 이름 옆에 있는 **설정** 아이콘을 클릭하십시오.
    3. 사이드 패널에서 **내보내기**를 클릭하여 피어 또는 순서 지정자 JSON 파일을 저장하십시오.
    4. JSON 파일에서 `operations_url` 매개변수의 값인 오퍼레이션 엔드포인트 URL을 찾으십시오. 이 주제의 뒷부분에 있는 명령에서는 이 값을 `peer-endpoint` 또는 `orderer-endpoint`라고 합니다. 예를 들어, 다음과 같습니다.

      ```
      {
      "short_name": "Peer1Org1_0",
      "name": "Peer1 Org1",
      "url": "https://169.46.208.93:32739",
      "type": "fabric-peer",
      "msp_id": "org1msp",
      "operations_url": "https://169.46.208.93:32101"
      }
      ```

      ```
      {
      "short_name": "Orderer_0",
      "name": "Orderer",
      "url": "https://169.46.208.93:31612",
      "type": "fabric-orderer",
      "msp_id": "orderermsp",
      "operations_url": "https://169.46.208.93:30115"
      }
      ```

- **`client-tls-cert`** 및 **`client-tls-key`**

  콘솔에서 TLS CA의 인증서 및 개인 키를 찾을 수 있습니다.

  1. **노드** 탭에서 피어 또는 순서 지정자의 CA 노드를 클릭하십시오.
  2. CA 페이지에서 **TLS 인증 기관**을 클릭하십시오.
  3. TLS CA 테이블에서 `admin` 사용자를 찾고 **조치** 아래의 세로 점을 클릭하십시오. 그런 다음 **ID 등록**을 클릭하십시오.
  4. 사이드 패널에서 **다음**을 클릭하면 TLS CA의 인증서 및 개인 키를 볼 수 있습니다.
  5. ID에 표시 이름을 지정하고 **ID 내보내기**를 클릭한 후 **지갑에 ID 추가**를 클릭하여 TLS CA의 인증서 및 개인 키를 JSON 파일에 저장하십시오.
  6. 내보낸 JSON 파일을 여십시오.
  7. JSON 파일에서 `private_key` 매개변수의 값인 개인 키를 찾으십시오. 이는 아래 명령에서 사용할 수 있는 `client-tls-key`입니다.
  8. JSON 파일에서 `cert` 매개변수의 값인 TLS CA 인증서를 찾으십시오. 이는 아래 명령에서 사용할 수 있는 `client-tls-cert`입니다.

- **`peer tls-ca cert`** 또는 **`orderer tls-ca cert`**

  콘솔에서 피어 또는 순서 지정자의 TLS CA 인증서를 찾을 수 있습니다.

  1. **노드** 탭에서 피어 또는 순서 지정자의 노드를 클릭하십시오.
  2. 노드 이름 옆에 있는 **설정** 아이콘을 클릭하십시오.
  3. 노드의 TLS CA 인증서 문자열을 복사하십시오. 이는 아래 명령에서 사용할 수 있는 `peer tls-ca cert` 또는 `orderer tls-ca cert`입니다.

**참고:** 모든 인증서 및 키에 대해 다음 명령을 사용하여 `base64` 형식인 인증서 문자열을 개별 `.pem` 파일로 변환해야 합니다.
  ```
  export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
  echo <base64_string> | base64 --decode $FLAG > <key_name>.pem
  ```
  {:codeblock}
{:important}


## 노드 상태 검사
{: #operations_service_health_check}

피어 또는 순서 지정자 노드의 상태를 검사하려면 `curl -k <peer-endpoint>/healthz` 또는 `curl -k <orderer-endpoint>/healthz` 명령을 실행하십시오. 예를 들어, 다음과 같습니다.

```
curl -k https://169.46.208.93:3210/healthz
```
{:codeblock}

상태 검사에 대한 자세한 정보는 [상태 검사 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/operations_service.html#health-checks "상태 검사")를 참조하십시오.


## 메트릭 보기
{: #operations_service_view_metrics}

피어 메트릭을 보려면 다음 명령을 실행하십시오. 동일한 명령일 실행하여 순서 지정자 메트릭을 볼 수 있지만 `<peer-endpoint>`를 `<orderer-endpoint>`로 바꾸십시오.

```
curl -k <peer-endpoint>/metrics --cert <client-tls-cert> --key <client-tls-key> --cacert <peer tls ca-cert>
```
{:codeblock}

예를 들어, 다음과 같습니다.

```
curl -k https://169.55.231.152:30766/metrics --cert msp/org1/ca/tls/msp/signcerts/cert.pem --key msp/org1/ca/tls/msp/key.pem  --cacert msp/org1/ca/tls/msp/cacerts/tlsca.pem
```
{:codeblock}


자세한 정보는 [메트릭 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/operations_service.html#metrics "메트릭")을 참조하십시오.


## 로깅 레벨 보기
{: #operations_service_log_level_view}

로깅 레벨을 보려면 다음 명령을 실행하십시오. 명령이 완료된 후 터미널에 로그 레벨이 표시됩니다.

```
curl -k <peer-endpoint>/logspec --cert <client-tls-cert> --key <client-tls-key> --cacert <peer tls ca-cert>
```
{:codeblock}

예를 들어, 다음과 같습니다.
```
curl -k https://169.46.208.93:3210/logspec --cert msp/org1/ca/tls/msp/signcerts/cert.pem --key msp/org1/ca/tls/msp/keystore/key.pem  --cacert msp/org1/ca/tls/msp/cacerts/tlsca.pem
```

<!--
```
curl https://169.46.208.93:3210/logspec --cert temp/1mycluster-test-32240/msp/org1/ca/tls/msp/signcerts/cert.pem --key temp/1mycluster-test-32240/msp/org1/ca/tls/msp/keystore/3fb20abb935f88b83a8da68317a44a4fa0953d7ec6d06bb19a6fc3979a603095_sk  --cacert temp/1mycluster-test-32240/msp/org1/ca/tls/msp/cacerts/169-55-231-152-30021-tlsca.pem
```
-->

다음 예제와 유사한 결과를 볼 수 있습니다.

```
{"spec":"info"}
```

## 로깅 레벨 설정
{: #operations_service_log_level_set}

기존 로깅 레벨 설정을 변경하려면 다음 명령을 실행하십시오. 이 명령은 `spec`이라는 단일 속성으로 구성된 JSON 본문에 `PUT` 메소드를 사용합니다. `<log-level>`을 예상 로깅 레벨로 바꾸십시오. 설정할 수 있는 로깅 레벨에 대한 자세한 정보는 Hyperledger Fabric 문서에서 [로깅 스펙 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/logging-control.html#logging-specification "로깅 스펙")을 참조하십시오.

```
curl -X PUT  <peer-endpoint>/logspec -d '{"spec":"<log-level>"}' --cert <client-tls-cert> --key <client-tls-key> --cacert <peer tls ca-cert>
```
{:codeblock}

예를 들어, 다음과 같습니다.
```
curl -X PUT  https://169.46.208.93:3210/logspec -d '{"spec":"chaincode=debug:info"}' --cert msp/org1/ca/tls/msp/signcerts/cert.pem --key msp/org1/ca/tls/msp/keystore/key.pem  --cacert msp/org1/ca/tls/msp/cacerts/tlsca.pem
```

새 로깅 레벨을 설정한 후 [로깅 레벨 보기 명령](#operations_service_log_level_view)을 사용하여 설정을 확인할 수 있습니다.

<!--
```
curl -X PUT  https://169.46.208.93:3210/logspec -d '{"spec":"chaincode=debug:info"}' --cert temp/1mycluster-test-32240/msp/org1/ca/tls/msp/signcerts/cert.pem --key temp/1mycluster-test-32240/msp/org1/ca/tls/msp/keystore/3fb20abb935f88b83a8da68317a44a4fa0953d7ec6d06bb19a6fc3979a603095_sk  --cacert temp/1mycluster-test-32240/msp/org1/ca/tls/msp/cacerts/169-55-231-152-30021-tlsca.pem
```
-->

로그 레벨 구성에 대한 자세한 정보는 Hyperledger Fabric 문서에서 [로그 레벨 관리 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/operations_service.html#log-level-management "로그 레벨 관리")를 참조하십시오.
