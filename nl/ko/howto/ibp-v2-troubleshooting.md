---

copyright:
  years: 2019
lastupdated: "2019-03-05"

subcollection: blockchain

---


{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:tsSymptoms: .tsSymptoms}
{:tsCauses: .tsCauses}
{:tsResolve: .tsResolve}
{:pre: .pre}

# 문제점 해결
{: #ibp-v2-troubleshooting}

콘솔을 사용하여 노드, 채널 또는 스마트 계약을 관리할 때 일반 문제가 발생할 수 있습니다. 대부분의 경우 몇 개의 간단한 단계를 수행하면 이러한 문제점으로부터 복구할 수 있습니다. 

## 피어 또는 순서 지정자를 작성한 후 내 노드 작동이 실패하는 이유는 무엇입니까? 
{: #ibp-console-build-network-troubleshoot-entry1}

기존 노드를 관리할 때 오류가 발생할 수 있습니다. 이런 경우에는 대개 노드 로그를 참조하는 것이 유용합니다.   

예를 들어 노드를 작동하려고 할 때 조치가 실패할 수 있습니다.
{: tsSymptoms}

피어 또는 순서 지정자를 새로 작성한 후에는 클러스터 스토리지 구성에 따라 노드 작동 준비가 완료될 때까지 몇 분 정도 소요될 수 있습니다.
{: tsCauses}

Kubernetes 대시보드를 확인하고 피어 또는 노드 상태가 `Running`인지 확인하십시오. 그런 다음 조치를 다시 시도하십시오. 노드가 작동된 후에도 여전히 문제가 발생하는 경우에는 [노드 로그](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-node-logs)에서 오류가 있는지 확인하십시오.   
{: tsResolve}

## 피어가 시작하지 못하는 이유는 무엇입니까? 
{: #ibp-console-build-network-troubleshoot-entry2}

이 오류는 다양한 상황에서 발생할 수 있습니다. 

피어 로그에는 `2019-02-06 19:43:24.159 UTC [main] InitCmd -> ERRO 001 Cannot run peer because cannot init crypto, folder “/certs/msp” does not exist`가 포함되어 있습니다.
{: tsSymptoms}

- 이 오류는 다음 상황에서 발생할 수 있습니다. 
  - 피어 또는 순서 지정자의 조직 MSP 정의를 작성할 때 `client`가 아닌 `peer` 유형의 ID에 해당하는 등록 ID 및 시크릿을 지정했습니다. `client` 유형이어야 합니다. 
  - 피어 또는 순서 지정자의 조직 MSP 정의를 작성할 때 해당 조직 관리자 ID의 등록 ID 또는 시크릿과 일치하지 않는 등록 ID 및 시크릿을 지정했습니다. 
  - 피어 또는 순서 지정자를 작성할 때 'peer' 유형이 아닌 ID의 등록 ID 및 시크릿을 지정했습니다. 

- 피어 또는 순서 지정자 CA 노드를 열고 **등록된 사용자** 테이블에 나열된 등록된 ID를 확인하십시오. 
- 피어 또는 순서 지정자를 삭제하고 올바른 등록 ID 및 시크릿을 신중히 지정하여 다시 작성하십시오. 
- 피어 또는 순서 지정자를 작성하기 전에 'client' 유형의 조직 관리자 ID를 작성해야 합니다. 조직 MSP 정의를 작성할 때 등록 ID와 동일한 ID를 지정해야 합니다. [피어 ID 등록](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-use-CA-org1) 및 [순서 지정자 ID 등록](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-use-CA-orderer)에 대해서는 이러한 지침을 참조하십시오.
{: tsResolve}

## 스마트 계약 설치, 인스턴스화 또는 업그레이드에 실패하는 이유는 무엇입니까? 
{: #ibp-console-smart-contracts-troubleshoot-entry1}

스마트 계약을 설치, 인스턴스화 또는 업그레이드할 때 오류가 발생할 수 있습니다. 예를 들어 스마트 계약을 피어에 설치하려고 할 때 `피어에 스마트 계약을 설치할 때 오류가 발생했습니다.`라는 오류가 발생하면서 실패합니다.
{: tsSymptoms}

이 버전의 스마트 계약이 이미 피어에 있거나 피어의 리소스가 부족한 경우에 이 오류를 수신할 수 있습니다.
{: tsCauses}

- Kubernetes 대시보드를 열고 피어 상태가 `Running`인지 확인하십시오.   
- 피어 노드를 열고 스마트 계약 버전이 이미 피어에 존재하지 않는지 확인하고 올바른 버전으로 다시 시도하십시오. 
- 노드가 작동된 후에도 여전히 문제가 발생하는 경우에는 [노드 로그](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-node-logs)에서 오류가 있는지 확인하십시오.   
{: tsResolve}

## 스마트 계약 컨테이너 로그를 보려면 어떻게 해야 합니까? 
{: #ibp-console-smart-contracts-troubleshoot-entry2}

스마트 계약 문제를 디버깅하기 위해 스마트 계약 또는 체인코드, 컨테이너 로그를 확인해야 할 수도 있습니다.
{: tsSymptoms}

이 지침에 따라 [컨테이너 로그를 확인](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-container-logs)하십시오.
{: tsResolve}

## 채널, 스마트 계약 및 ID가 콘솔에서 사라졌습니다. 어떻게 하면 되돌릴 수 있습니까? 
{: #ibp-v2-troubleshooting-browser-storage}

콘솔 지갑 ID는 블록체인 컴포넌트를 관리할 수 있도록 공개 키 및 개인 키 쌍으로 구성되지만, 브라우저 로컬 스토리지에만 저장됩니다. 이러한 ID를 보호하고 관리할 책임은 사용자에게 있습니다. 해당 ID를 작성한 후 파일 시스템으로 내보내는 것이 좋습니다. 노드를 새로 작성할 때 콘솔 지갑의 ID를 노드와 연관시키십시오. 이 관리자 ID를 사용하여 노드를 관리할 수 있습니다. 브라우저를 전환하거나 다른 시스템의 브라우저로 변경할 경우 해당 ID는 더 이상 지갑에 없습니다. 따라서 컴포넌트를 관리할 수 없습니다.
{: tsSymptoms}

{{site.data.keyword.blockchainfull_notm}} Platform 2.0의 새로운 기능 중 하나는 이제 인증서를 보호하고 관리하는 책임이 사용자에게 있다는 점입니다. 따라서 사용자가 컴포넌트를 관리할 수 있도록 인증서가 브라우저 로컬 스토리지에만 저장됩니다.
{: tsCauses}

- 조직 MSP 정의를 새로 작성할 때 언제든지 조직을 관리할 수 있는 ID에 대한 키를 생성할 수 있습니다. 따라서 해당 프로세스 중에는 **생성** 및 **내보내기** 단추를 차례로 클릭하여 생성된 ID를 콘솔 지갑에 저장한 후 파일 시스템에 JSON 파일로 저장해야 합니다. 
- 브라우저에서 이 문제점을 해결하려면 해당 ID를 가져와서 해당 노드와 연관시켜야 합니다. 
  - 문제점이 발생한 브라우저에서 **지갑** 탭을 클릭한 후 **ID 추가**를 클릭하여 JSON 파일을 지갑으로 가져오십시오. 
  - **JSON 업로드**를 클릭한 후 **파일 추가** 단추를 사용하여 내보낸 JSON 파일을 찾아보십시오. 
  - **제출**을 클릭하십시오.
  - 이제 이 ID와 원래 연관된 피어 또는 순서 지정자 노드를 열고 **설정** 아이콘을 클릭하십시오. 
  - **ID 연관** 단추를 클릭하십시오. 
  - 콘솔 지갑으로 방금 가져온 ID를 드롭 다운 목록에서 선택하십시오. 
  - **연관**을 클릭하십시오. 
- 원래 브라우저의 지갑에 있었던 각 ID에 대해 이 프로세스를 반복하십시오.
{: tsResolve}

## 내 채널에 조직을 추가하려고 할 때 `채널 업데이트 중 오류 발생` 오류가 발생하는 이유는 무엇입니까? 
{: #ibp-v2-troubleshooting-update-channel}

채널에 다른 조직을 추가하려고 하면 `채널 업데이트 중 오류 발생` 메시지와 함께 업데이트가 실패합니다.
{: tsSymptoms}

이 오류는 **채널 업데이트** 패널에서 선택한 **채널 업데이트 MSP ID**가 채널 관리자가 아닌 경우에 발생합니다.
{: tsCauses}

**채널 업데이트** 패널에서 아래로 스크롤하여 **채널 업데이트 MSP ID**로 이동한 후 채널 작성 시 지정한 MSP ID를 선택하거나 채널 관리자의 MSP ID를 지정하십시오.
{: tsResolve}
