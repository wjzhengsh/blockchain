---

copyright:
  years: 2017, 2018
lastupdated: "2018-06-14"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# 블록체인 네트워크 모니터링

이 튜토리얼은 {{site.data.keyword.cloud_short}}에서 {{site.data.keyword.blockchain}} 네트워크의 상태 정보를 보고 모니터하는 방법을 보여줍니다.
{:shortdesc}


## 피어, 순서 지정 및 CA 모니터링
{: #monitor-nodes}

네트워크 노드 중 하나에 대해 HTTP **HEAD** 요청을 발행하여 노드 상태를 확인할 수 있습니다. 네트워크 노드는 블록체인 네트워크의 피어, 순서 지정자 또는 CA일 수 있습니다. **HEAD** 요청은 GET 요청과 유사하며 본문 없이 헤더만 전송합니다. 노드가 정상적으로 작동하는 경우 200 응답을 받을 수 있습니다.

1. 네트워크 모니터의 "개요" 화면에서 **연결 프로파일**을 클릭하십시오. 그런 다음 **원시 JSON**을 클릭하여 웹 브라우저에서 연결 프로파일을 보거나 **다운로드**를 클릭하여 연결 프로파일을 로컬로 저장할 수 있습니다.
2. 연결 프로파일에서 확인할 네트워크 노드의 URL 정보를 찾으십시오. 예를 들어, `fabric-orderer-20190b` 순서 지정자의 URL은 `grpcs://fft-zbc02b.4.secure.blockchain.ibm.com:20190`입니다.  
    ![순서 지정자 URL 예](../images/orderer_url.png "순서 지정자 URL 예")
3. URL에서 **grpcs**를 **https**로 대체하십시오. 위의 예에서 URL은 `https://fft-zbc02b.4.secure.blockchain.ibm.com:20190`이 됩니다.
4. curl 또는 Chrome Postman 앱과 같은 도구를 사용하여 URL에 대해 **HEAD** 요청을 발행하십시오.
    - 200 상태 응답을 받으면 네트워크 노드가 정상적으로 작동하는 것입니다.
    - **HEAD** 요청이 실패하며 연결 오류가 발생하면, 네트워크 노드가 실행 중이 아니거나 노드 URL이 잘못되었거나 방화벽이 노드에 대한 사용자 액세스를 차단한 것입니다. 이 오류를 해결해야 합니다. 그렇지 않으면 애플리케이션이 노드에 연결할 수 없습니다.

다음 예는 curl에서 200 응답이 있는 **HEAD** 요청을 보여줍니다. HTTP **HEAD** 요청은 노드가 액세스 가능한지 여부를 확인하므로 grpc 오류를 무시할 수 있습니다. 액세스 가능한 경우 노드에 대한 grpc 요청은 애플리케이션에서도 작동합니다.

```
C:\>curl -i --head https://fft-zbc02b.4.secure.blockchain.ibm.com:20190
HTTP/2 200
contnent-type: application/grpc
grpc-status: 8
grpc-message: malformed method name: "/"
```
{:codeblock}

다음 예는 curl에서 연결 오류가 있는 **HEAD** 요청을 보여줍니다.

```
C:\>curl -i --head https://fft-zbc02b.4.secure.blockchain.ibm.com:20190
curl: (7) Failed to connect to fft-zbc02b.4.secure.blockchain.ibm.com:20190: Connection refused
```
{:codeblock}

다음 그림은 Chrome Postman 앱에서 200 응답이 있는 **HEAD** 요청을 보여줍니다.  

![HEAD 요청 Postman 예](../images/orderer_head_postman.png "HEAD 요청 Postman 예")


## 채널 모니터링
{: #monitor-channnels}

네트워크 모니터를 입력하고 "채널" 화면에서 보고 모니터하려는 채널을 찾으십시오.  특정 채널 화면에서는 다음 세 개 탭에서 데이터 상태 정보, 구성원 및 이 채널의 인스턴스화된 체인코드를 볼 수 있습니다.

* **채널 개요**  
  "채널 개요" 탭에는 이 채널에 대한 블록 정보가 표시됩니다.
    * 작성된 총 블록 수를 포함하는 일련의 데이터 점, 마지막 트랜잭션 이후의 시간 간격, 체인코드 인스턴스화 수 및 체인코드 호출 수.
    * 이 채널의 모든 블록을 나열하는 테이블.  블록을 펼쳐서 블록에 대한 자세한 정보를 볼 수 있습니다.  

  ![채널 개요](../images/channel_overview_detail.png "채널 개요")  

* **구성원**  
  "구성원" 탭에 조직 운영자의 이메일 주소를 포함한 이 채널에 대한 구성원의 정보가 표시됩니다.
  ![채널 구성원](../images/channel_members.png "채널 구성원")  

* **체인코드**  
  "체인코드" 탭에는 체인코드 ID, 버전 및 체인코드를 실행 중인 피어의 수와 함께 이 채널에서 인스턴스화된 모든 체인코드가 나열됩니다.   

  체인코드 행을 펼쳐서 체인코드에 대한 세부사항 정보를 가져오십시오.  
    * **JSON**을 클릭하여 체인코드의 JSON 파일을 볼 수 있습니다.
    * **로그**를 클릭하여 체인코드의 로그를 볼 수 있습니다.
    * **삭제**를 클릭하여 실행 중인 체인코드 컨테이너를 제거할 수 있습니다.
    **참고**: 실행 중인 체인코드 컨테이너를 삭제하면 실제로 체인코드가 삭제되지 않습니다. 블록체인 네트워크에서 인스턴스화된 체인코드는 삭제할 수 없습니다.

  ![채널 체인코드](../images/channel_chaincode.png "채널 체인코드")


## 체인코드 모니터링
{: #monitor-chaincode}

네트워크 모니터에 들어가서 "코드 설치" 화면을 여십시오. 체인코드를 실행하고 있는 경우 드롭 다운 목록에서 피어를 선택하면 체인코드 ID와 버전이 포함된 테이블에서 이 피어에 대한 모든 체인코드를 볼 수 있습니다.  이 화면에서 체인코드의 설치 및 인스턴스화를 수행할 수 있습니다.  자세한 정보는 [체인코드 설치, 인스턴스화 및 업데이트](install_instantiate_chaincode.html)를 참조하십시오.

  ![체인코드](../images/chaincode_install_overview.png "체인코드")


## 샘플 애플리케이션 모니터링
{: #monitor-apps}

스타터 플랜 네트워크에서는 네트워크 모니터의 "샘플 시험 사용" 화면에서 샘플 애플리케이션을 보고 액세스할 수 있습니다.  샘플 애플리케이션을 배치한 후 **실행** 단추를 클릭하여 애플리케이션 인터페이스를 시작하거나 **GitHub에서 보기** 링크를 클릭하여 코드 저장소로 이동할 수 있습니다.  자세한 정보는 [샘플 애플리케이션 배치](prebuilt_samples.html)를 참조하십시오.

  ![샘플 애플리케이션](../images/sampleappflow0.png "샘플 애플리케이션")
