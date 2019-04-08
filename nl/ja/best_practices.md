---

copyright:
  years: 2019
lastupdated: "2019-03-20"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# アプリケーション開発のベスト・プラクティス
{: #best-practices-app}

このガイドの対象読者は、アプリケーション開発の基本を既に習得していて、ソリューションを拡大する準備が整っているユーザーです。以下のベスト・プラクティスに参考にしながら、ネットワークのパフォーマンスを最大化して、アプリケーションのダウン時間を回避してください。
{:shortdesc}

## アプリケーションの接続と可用性
{: #best-practices-app-connectivity-availability}

Hyperledger Fabric の[トランザクション・フロー ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/txflow.html "トランザクション・フロー"){:new_window} には複数のコンポーネントが関わり、その中でクライアント・アプリケーションはユニークな役割を果たします。 SDK は、承認を受けるためにトランザクション提案をピアに送信します。 その後、SDK は、承認された提案を収集して順序付けサービスに送信します。すると、順序付けサービスが、トランザクションのブロックをチャネル台帳に追加するためにピアに送信します。 実動アプリケーションの開発者は、SDK とネットワークの間の対話を管理して効率性と可用性を確保しておく必要があります。

### トランザクションの管理
{: #best-practices-app-managing-transactions}

アプリケーション・クライアントは、トランザクション提案が検証されたこと、および提案が正常に完了したことを確認する必要があります。 提案は、ネットワーク障害やコンポーネント障害などのさまざまな原因によって遅延したり消失したりする可能性があります。 [高可用性](/docs/services/blockchain/best_practices.html#best-practices-app-ha-app)を確保するようにアプリケーションをコーディングすることによって、コンポーネントの障害に対応することが必要です。また、ネットワークが応答する前に提案がタイムアウトにならないように、アプリケーションの[タイムアウト値を大きくする](/docs/services/blockchain/best_practices.html#best-practices-app-set-timeout-in-sdk)こともできます。

チェーンコードが実行されていない場合は、そのチェーンコードに送信された最初のトランザクション提案によって、チェーンコードが開始されます。 チェーンコードの開始中は、他のすべての提案は、チェーンコードが現在開始中であることを示すエラーを受け取り、拒否されます。 これはトランザクションの無効化とは異なります。 チェーンコードの開始中に提案が拒否された場合、アプリケーション・クライアントは、チェーンコードが開始された後に、拒否された提案を再送信する必要があります。 アプリケーション・クライアントは、メッセージ・キューを使用してトランザクション提案の消失を防止できます。

チャネル・ベースのイベント・サービスを使用して、トランザクションのモニターやメッセージ・キューの作成を行うことができます。 [channelEventHub ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://fabric-sdk-node.github.io/ChannelEventHub.html "channelEventHub"){:new_window} クラスは、トランザクション、ブロック、およびチェーンコードのイベントに基づいてリスナーを登録できます。 チャネル eventhub のチャネル・ベースのリスナーは、複数のチャネルに拡張したり、チャネルが異なるトラフィックを区別したりできます。

古い EventHub クラスではなく channelEventHub を使用することをお勧めします。 eventHub は単一スレッドであり、チャネル全体のリスナーをスローダウンまたはハングさせる可能性があるすべてのチャネルからのイベントが含まれます。 eventHub クラスは、イベントが配信されることを保証しません。また、欠落したイベントを追跡するために、特定のポイント (ブロック番号など) からイベントを取得する方法も提供しません。

**注:** ピア EventHub クラスは、Fabric SDK の今後のリリースで非推奨になります。ピア EventHub クラスを使用する既存のアプリケーションがある場合は、代わりにチャネル EventHub クラスを使用するようにアプリケーションを更新してください。詳しくは、Node SDK の資料の [How to use the channel-based event service ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://fabric-sdk-node.github.io/tutorial-channel-events.html "How to use the channel-based event service"){:new_window} を参照してください。

### ネットワーク接続のオープンとクローズ
{: #best-practices-app-connections}

トランザクション・プロポーザルを送信する前に SDK を使用してピア・オブジェクトと順序付けプログラム・オブジェクトを作成すると、アプリケーションとネットワーク・コンポーネントの間に gRPC 接続がオープンします。 例えば、以下のコマンドは `org1-peer1` への接続をオープンします。 この接続は、アプリケーションの実行中はアクティブなままです。

```
var peer = fabric_client.newPeer(creds.peers["org1-peer1"].url, { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null});
```
{:codeblock}

アプリケーションとネットワークの間の接続を管理する際には、以下の推奨事項を考慮してください。

- 新規接続をオープンしてトランザクションを送信する代わりに、ネットワークと対話するときにピア・オブジェクトと順序付けプログラム・オブジェクトを再利用します。 ピア・オブジェクトと順序付けプログラム・オブジェクトを再利用すると、リソースを節約でき、パフォーマンスが向上します。  
- ネットワーク・コンポーネントへの永続的な接続を維持するには、[gRPC キープアライブ ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://github.com/grpc/grpc/blob/master/doc/keepalive.md "gRPC キープアライブ") を使用します。 キープアライブは gRPC 接続をアクティブのままにし、「未使用」接続がクローズされないようにします。 以下のピア接続の例では、gRPC オプションを [ConnectionOpts ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://fabric-sdk-node.github.io/global.html#ConnectionOpts "接続") オブジェクトに追加します。 gRPC オプションは、{{site.data.keyword.blockchainfull_notm}} Platform が推奨する値に設定されます。  
  ```
  var peer = fabric_client.newPeer(creds.peers["org1-peer1"].url, { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null},
  "grpcOptions": {
    "grpc.keepalive_time_ms": 120000,
    "grpc.http2.min_time_between_pings_ms": 120000,
    "grpc.keepalive_timeout_ms": 20000,
    "grpc.http2.max_pings_without_data": 0,
    "grpc.keepalive_permit_without_calls": 1
    }
  );
  ```
  {:codeblock}

  推奨値が設定されたこれらの変数は、ネットワーク接続プロファイルの `"peers"` セクションにもあります。 [SDK で接続プロファイル](/docs/services/blockchain/v10_application.html#best-practices-app-connection-profile)を使用してネットワーク・エンドポイントに接続すると、推奨オプションがアプリケーションに自動的にインポートされます。

- 接続が不要になった場合は、`peer.close()` コマンドと `orderer.close()` コマンドを使用してリソースを解放し、パフォーマンスの低下を防ぎます。 詳しくは、Node SDK の資料の[ピアの close ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://fabric-sdk-node.github.io/Peer.html#close__anchor "ピアの close") クラスと[順序付けプログラムの close![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://fabric-sdk-node.github.io/Orderer.html#close__anchor "順序付けプログラムの close") クラスを参照してください。 接続プロファイルを使用してピアおよび順序付けプログラムをチャネル・オブジェクトに追加した場合は、`channel.close()` コマンドを使用して、そのチャネルに割り当てられているすべての接続をクローズできます。

### 可用性の高いアプリケーション
{: #best-practices-app-ha-app}

高可用性を実現するためのベスト・プラクティスとして、フェイルオーバーできるように組織ごとに少なくとも 2 つのピアをデプロイすることを強くお勧めします。 また、アプリケーションも高可用性のために調整する必要があります。 両方のピアにチェーンコードをインストールして、それらをチャネルに追加します。 そして、ネットワークをセットアップしてピア・ターゲット・リストを作成するときに、両方のピア・エンドポイントにトランザクション提案を送信するようにします。 エンタープライズ・プラン・ネットワークにはフェイルオーバーのために複数の順序付けプログラムがあるため、ある順序付けプログラムが使用不可になっても、クライアント・アプリケーションは承認されたトランザクションを別の順序付けプログラムに送信できます。 ネットワーク・エンドポイントを手動で追加するのではなく[接続プロファイル](/docs/services/blockchain/v10_application.html#dev-app-connection-profile)を使用する場合は、プロファイルが最新のものであること、また、追加のピアと順序付けプログラムがプロファイルの `channels` セクションの該当するチャネルに追加されていることを確認してください。 これにより、SDK は、接続プロファイルを使用して、チャネルに参加するコンポーネントを追加できます。

## 相互 TLS を有効にする
{: #best-practices-app-mutual-tls}

Fabric V1.1 レベルのエンタープライズ・プラン・ネットワークを実行する場合は、アプリケーションで[相互 TLS を有効にする](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-network-preferences)ことができます。 相互 TLS を有効にする場合は、この機能をサポートするようにアプリケーションを更新する必要があります。 そうしないと、アプリケーションはネットワークと通信できません。

接続プロファイルで `certificateAuthorities` セクションを見つけます。このセクションに、相互 TLS を使用してネットワークと通信するための証明書をエンロールして取得するのに必要な以下の属性があります。

- `url`: 相互 TLS 証明書を提供できる CA に接続するための URL
- `enrollId`: 証明書を取得するために使用する登録 ID
- `enrollSecret`: 証明書を取得するために使用する登録シークレット
- `x-tlsCAName`: アプリケーションでの相互 TLS を使用した通信を可能にするための証明書を取得する際に使用する CA 名。

相互 TLS をサポートするようにアプリケーションを更新する方法について詳しくは、[How to configure mutual TLS ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://fabric-sdk-node.github.io/tutorial-mutual-tls.html "相互 tls"){:new_window} を参照してください。


## (オプション) Fabric SDK でのタイムアウト値の設定
{: #best-practices-app-set-timeout-in-sdk}

Fabric SDK は、クライアント・アプリケーションにブロックチェーン・ネットワーク内のイベントについてのデフォルトのタイムアウト値を設定します。 Fabric Java SDK でのデフォルトのタイムアウト設定については、次の例を参照してください。 ファイル・パスは `src&#xa5;main&#xa5;java&#xa5;org&#xa5;hyperledger&#xa5;fabric&#xa5;sdk&#xa5;helper&#xa5;Config.java` です。

```
    /**
     * Timeout settings
     **/
    public static final String PROPOSAL_WAIT_TIME = "org.hyperledger.fabric.sdk.proposal.wait.time";
    public static final String CHANNEL_CONFIG_WAIT_TIME = "org.hyperledger.fabric.sdk.channelconfig.wait_time";
    public static final String TRANSACTION_CLEANUP_UP_TIMEOUT_WAIT_TIME = "org.hyperledger.fabric.sdk.client.transaction_cleanup_up_timeout_wait_time";
    public static final String ORDERER_RETRY_WAIT_TIME = "org.hyperledger.fabric.sdk.orderer_retry.wait_time";
    public static final String ORDERER_WAIT_TIME = "org.hyperledger.fabric.sdk.orderer.ordererWaitTimeMilliSecs";
    public static final String PEER_EVENT_REGISTRATION_WAIT_TIME = "org.hyperledger.fabric.sdk.peer.eventRegistration.wait_time";
    public static final String PEER_EVENT_RETRY_WAIT_TIME = "org.hyperledger.fabric.sdk.peer.retry_wait_time";
    public static final String EVENTHUB_CONNECTION_WAIT_TIME = "org.hyperledger.fabric.sdk.eventhub_connection.wait_time";
    public static final String EVENTHUB_RECONNECTION_WARNING_RATE = "org.hyperledger.fabric.sdk.eventhub.reconnection_warning_rate";
    public static final String PEER_EVENT_RECONNECTION_WARNING_RATE = "org.hyperledger.fabric.sdk.peer.reconnection_warning_rate";
    public static final String GENESISBLOCK_WAIT_TIME = "org.hyperledger.fabric.sdk.channel.genesisblock_wait_time";

    ...

    // Default values
    /**
     * Timeout settings
     **/
    defaultProperty(PROPOSAL_WAIT_TIME, "20000");
    defaultProperty(CHANNEL_CONFIG_WAIT_TIME, "15000");
    defaultProperty(ORDERER_RETRY_WAIT_TIME, "200");
    defaultProperty(ORDERER_WAIT_TIME, "10000");
    defaultProperty(PEER_EVENT_REGISTRATION_WAIT_TIME, "5000");
    defaultProperty(PEER_EVENT_RETRY_WAIT_TIME, "500");
    defaultProperty(EVENTHUB_CONNECTION_WAIT_TIME, "5000");
    defaultProperty(GENESISBLOCK_WAIT_TIME, "5000");
    /**
     * This will NOT complete any transaction futures time out and must be kept WELL above any expected future timeout
     * for transactions sent to the Orderer. For internal cleanup only.
     */
    defaultProperty(TRANSACTION_CLEANUP_UP_TIMEOUT_WAIT_TIME, "600000"); //10 min.
```
{:codeblock}

しかし、独自のアプリケーションでデフォルトのタイムアウト値を変更しなければならない場合があります。 例えば、応答に要する時間が 5000 ミリ秒 (イベント・ハブ接続のデフォルトのタイムアウト値) を超えるトランザクションをアプリケーションが呼び出すと、トランザクションが完了する前に 5000 ミリ秒で呼び出しイベントが終了するため、失敗エラーを受け取ります。システム・プロパティーを設定して、クライアント・アプリケーションのデフォルト値を上書きできます。 システム・プロパティーを設定する前にデフォルト値が初期設定されるため、システム・プロパティーが有効にならない場合があります。 したがって、クライアント・アプリケーション内の静的構造でタイムアウトのシステム・プロパティーを設定する必要があります。 Fabric Java SDK でイベント・ハブ接続のタイムアウト値を 15000 ミリ秒に変更する場合の以下の例を参照してください。 ファイル・パスは `src&#xa5;main&#xa5;java&#xa5;org&#xa5;hyperledger&#xa5;fabric&#xa5;sdk&#xa5;helper&#xa5;Config.java` です。

```
 public static final String EVENTHUB_CONNECTION_WAIT_TIME = "org.hyperledger.fabric.sdk.eventhub_connection.wait_time";
 private static final long EVENTHUB_CONNECTION_WAIT_TIME_VALUE = 15000;

 static {
     System.setProperty(EVENTHUB_CONNECTION_WAIT_TIME, EVENTHUB_CONNECTION_WAIT_TIME_VALUE);
 }
```
{:codeblock}

Node SDK を使用する場合は、呼び出されるメソッド内にタイムアウト値を直接指定できます。 例えば、以下の行を使用して、[チェーンコードのインスタンス化 ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://fabric-sdk-node.github.io/Channel.html#sendInstantiateProposal "sendInstantiateProposal") のタイムアウト値を 5 分に拡大できます。
```
channel.sendInstantiateProposal(request, 300000);
```
{:codeblock}

## CouchDB を使用する場合のベスト・プラクティス
{: #best-practices-app-couchdb-indices}

状態データベースに CouchDB を使用している場合、チェーンコードからチャネルの状態データに対する JSON データ照会を実行できます。 JSON 照会用に索引を作成してチェーンコードで使用することを強くお勧めします。 索引があれば、ネットワークがトランザクションとエントリーの追加ブロックをワールド・ステートに追加するときに、アプリケーションで効率的にデータを取得できます。

CouchDB について、および索引をセットアップする方法について詳しくは、Hyperledger Fabric の資料で [CouchDB as the State Database ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](http://hyperledger-fabric.readthedocs.io/en/release-1.1/couchdb_as_state_database.html "CouchDB as the State Database"){:new_window} を参照してください。 [Fabric CouchDB チュートリアル ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/couchdb_tutorial.html) にも、索引をチェーンコードで使用する例があります。

CouchDB データベース全体をスキャンする照会にはチェーンコードを使用しないでください。 完全なデータベース・スキャンでは、応答時間が長くなり、ネットワークのパフォーマンスが低下します。 次のような手順により、長時間かかる照会を防止および対処できます。
- チェーンコードを使用して索引をセットアップします。
- 索引を使用するには、索引のすべてのフィールドを照会のセレクターまたはソートのセクションに入れることも必要です。
- 照会が複雑になると、パフォーマンスが低下し、索引を使用する可能性も低くなります。
- テーブルや索引の完全スキャンが発生する演算子 (`$or`、`$in`、`$regex` など) の使用は避けてください。

照会で索引を使用する例や、最適なパフォーマンスが得られる照会のタイプについては、Fabric CouchDB チュートリアル ![外部リンク・アイコン](https://hyperledger-fabric.readthedocs.io/en/release-1.2/couchdb_tutorial.html#use-best-practices-for-queries-and-indexes) を参照してください。

{{site.data.keyword.blockchainfull_notm}} Platform 上のピアには queryLimit が設定されているため、状態データベースから返されるエントリー数は 10,000 個に制限されます。 1 つの照会で queryLimit に達する場合は、複数の照会を使用することで残りの結果を取得できます。 範囲照会の結果を追加で取得する必要がある場合には、前の照会で返された最後のキーを使用して次の照会を開始します。 JSON 照会の結果がさらに必要な場合は、データ内のいずれかの変数で照会をソートしてから、前の照会で返された最後の値を次の照会の「より大」フィルターで使用します。

集計またはレポート作成のためにデータベース全体を照会しないでください。 アプリケーションの中でダッシュボードを作成したり大量のデータを収集したりする場合は、ブロックチェーン・ネットワークのデータを複製したオフチェーン・データベースを照会します。 これにより、ネットワークのパフォーマンスを低下させたり、トランザクションを中断したりすることなく、ブロックチェーン上のデータを把握できます。

オフチェーン・データ・ストアは、Fabric SDK に用意されているチャネル・ベースのイベント・サービス・クライアントを使用して作成できます。 例えば、ブロック・リスナーを使用して、チャネル台帳に追加される最新のトランザクションを取得できます。 有効なトランザクションからのトランザクションの読み取り/書き込みセットを使用して、別のデータベースに格納されているワールド・ステートのコピーを更新できます。 詳しくは、Node SDK の資料の [How to use the channel-based event service ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://fabric-sdk-node.github.io/tutorial-channel-events.html "How to use the channel-based event service"){:new_window} を参照してください。
