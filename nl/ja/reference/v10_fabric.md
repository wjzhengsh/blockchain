---

copyright:
  years: 2017, 2019
lastupdated: "2019-03-05"

keywords: Hyperledger Fabric, confidential channels, Membership Service Provider, Linux Foundation, SDKs, modular architecture, permissioned network

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}


# Hyperledger Fabric
{: #hyperledger-fabric}

{{site.data.keyword.blockchainfull}} ネットワークは、Linux Foundation の Hyperledger Project 内のブロックチェーン・プロジェクトの 1 つである Hyperledger Fabric スタック上に構築されています。 これは「許可制」のネットワークで、すべてのユーザーとコンポーネントには既知のアイデンティティーがあります。 すべてのコミュニケーション・タッチポイントに署名/検証ロジックが実装されており、一連のエンドースメントと検証の検査によってトランザクションが承認されます。 この点は匿名性を重視する従来型のブロックチェーンとは大きく異なります。従来型の実装は暗号通貨に依存し、トランザクションの検証を行うために高度な計算負荷がかかります。
{:shortdesc}

Hyperledger Fabric は、スケーラビリティーとパフォーマンスを拡張するためのモジュラー・アーキテクチャーを提供します。 このトピックでは、Hyperledger Fabric のいくつかのキー・コンポーネントを説明します。 Hyperledger Fabric の概要全体については、[Hyperledger Fabric の資料 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/){:new_window} を参照してください。

## ピア
{: #hyperledger-fabric-peer}

物理レベルでは、ブロックチェーン・ネットワークは主にピア・ノード (または単にピア) で構成されます。 ピアは、台帳およびスマート・コントラクト ([「チェーンコード」![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/chaincodenamespace.html "Chaincode namespace") に含まれています) をホストするので、ネットワークの基本的な要素です。 より正確に表現すると、ピアは台帳の**インスタンス**およびスマート・コントラクトの**インスタンス**をホストします。 スマート・コントラクトおよび台帳は、ネットワーク内でそれぞれ、共有プロセスおよび共有情報をカプセル化するために使用されるので、ピアのこのような側面は、Fabric ネットワークが実際にどのように機能しているかを理解する良い開始点となります。

ピアについて詳しくは、Fabric コミュニティー資料の[ピアにのみ焦点を当てたこの資料 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/peers/peers.html) を確認してください。

## 認証局
{: #hyperledger-fabric-certificate-authority}

Hyperledger Fabric には、**許可制**ブロックチェーン・ネットワークのプラットフォームとして、すべてのメンバー組織とそのユーザーのネットワーク・アイデンティティーを管理するためのモジュラー**認証局 (CA)** コンポーネントが組み込まれています。 すべてのユーザーに対して許可アイデンティティーが求められているため、ネットワーク・アクティビティーに対する ACL ベースの制御が可能になり、すべてのトランザクションに関して、それが最終的に帰属する登録済みユーザーを明確にすることができます。
* CA は、ネットワークに参加する権限がある各**メンバー** (組織または個人) に、ルート証明書 (**rootCert**) を発行します。
* さらに CA は、各メンバー・コンポーネント、サーバー・サイド・アプリケーション、そして時にはユーザーに、エンロールメント証明書 (**eCert**) を発行します。
* エンロール済みの各ユーザーは、トランザクション証明書 (**tCert**) の割り振りも付与されます。 それぞれの **tCert** は、1 つのネットワーク・トランザクションを許可します。

このような、ネットワーク・メンバーシップとアクションに対する証明書ベースの制御を使用すると、メンバーは、特定のユーザー・アイデンティティーによって、専用かつ機密のチャネル、アプリケーション、データへのアクセスを制限できます。

Hyperledger Fabric の認証局コンポーネントについて詳しくは、[Fabric CA User’s Guide![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/){:new_window} を参照してください。

## メンバーシップ・サービス・プロバイダー
{: #hyperledger-fabric-membership-service-provider}

Hyperledger Fabric には**メンバーシップ・サービス・プロバイダー (MSP)** コンポーネントが含まれています。このコンポーネントは、証明書の発行と検証に使用されるすべての暗号メカニズムとプロトコルの抽象化に加え、ユーザー認証を提供します。 MSP は各チャネル・ピアにインストールされ、ピアに対して発行されるトランザクション要求が、必ず認証済みの許可ユーザー・アイデンティティーから発信されるようにします。

Hyperledger Fabric メンバーシップ・サービス・プロバイダー・コンポーネントについて詳しくは、[Hyperledger Fabric 資料 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/){:new_window} の [Membership ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/membership/membership.html){:new_window} を参照してください。

## 順序付けサービス
{: #hyperledger-fabric-ordering-service}

Ethereum や Bitcoin などの他の分散ブロックチェーンでは、トランザクションを順序付けしてピアに送り出す中央の機関は存在しません。 {{site.data.keyword.blockchainfull_notm}} Platform がベースとするブロックチェーン、Hyperledger Fabric は、他とは異なる動作をします。 その特徴的なものが、**順序付けプログラム**と呼ばれるノードです。

順序付けプログラムは、次のように必要不可欠な機能をいくつか実行するので、ネットワークの重要なコンポーネントです。

- 順序付けプログラムは、台帳に書き込むためにピアに送られるトランザクションのブロックを、文字どおり**順序付け**します。このプロセスを「順序付け」と呼びます。 順序付けプログラムではなくピア自体がこれらのトランザクションを集めて順序付けした場合、あるピアがトランザクションを台帳に書き込んでいるのに別のピアは書き込んでいないというフォーク状態が生じる可能性が高くなります。
- 順序付けプログラムは、**順序付けシステム・チャネル**を保守します。これは、チャネルの作成を許可されたピア組織のリスト、つまり、**コンソーシアム**が存在する場所です。
- 順序付けプログラムは、重要な ID 有効性検査を行います。 例えば、順序付けプログラムのコンソーシアムのメンバーでない組織がチャネルを作成しようとした場合、その要求は拒否されます。 また、順序付けプログラムは、トランザクション・チャネルに対する動作も検証します (チャネル構成を変更するための許可など)。

Hyperledger Fabric は現在、SOLO (順序付けプログラム・ノード 1 つ) と Kafka ベースの順序付けサービスの両方の実装をサポートしています。 Hyperledger Fabric の順序付けサービスについて詳しくは、[Hyperledger Fabric 資料 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/){:new_window} の [Bringing up a Kafka-based Ordering Service ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/kafka.html){:new_window} を参照してください。

## Fabric SDK
{: #hyperledger-fabric-fabric-sdks}

Fabric SDK を使用することにより、アプリケーション開発者は、ブロックチェーン・ネットワークとやり取りするアプリケーションを作成することができます。 SDK は、アプリケーションでチャネルとチェーンコードのライフサイクルを管理する上で役立ちます。

Hyperledger Fabric には Node.js SDK と Java SDK の両方が用意されており、ブロックチェーン・ネットワークと対話するための次の機能を利用できます。

* ユーザーを登録してエンロールさせる
* チャネルを作成する
* ピアをチャネルに参加させる
* システム・チャネルまたはアプリケーション・チャネルの構成を更新する
* チェーンコードをピアにインストールする
* チャネル上でチェーンコードをインスタンス化する
* チャネル上でチェーンコードをアップグレードする
* 台帳を更新するためにチェーンコード関数を呼び出す
* 台帳にある特定のトランザクション、ブロック、またはキーを照会する
* チャネル上のイベントをモニターする (例えば、トランザクションのコミットメントの成功)

Fabric SDK について詳しくは、[Hyperledger Fabric 資料 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/){:new_window} の [Hyperledger Fabric SDKs ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/fabric-sdks.html){:new_window} を参照してください。

## トランザクション・フロー
{: #hyperledger-fabric-transaction-flow}

データの一貫性と整合性を確保するため、Hyperledger Fabric は、クライアント認証、エンドースメント、順序付け、台帳へのコミットメントなどのトランザクション・フロー全体にわたり、複数のチェックポイントを実装しています。

次の**図 1** は、Hyperledger Fabric ブロックチェーン・ネットワーク上のトランザクション・フローを表しています。
![トランザクション・フロー](../images/v10_txflow.png "Hyperledger Fabric ネットワーク上のトランザクション・フロー")
*図 1. Hyperledger Fabric ネットワーク上のトランザクション・フロー*

Hyperledger Fabric ネットワークでは、照会とトランザクションにおけるデータ・フローは、クライアント・サイド・アプリケーションがチャネル上のピアにトランザクション要求を送信することによって開始します。 ネットワークにおける初期のデータ・フローは、照会とトランザクションの両方で共通しています。

1. クライアント・アプリケーションは、SDK で使用可能な API を使用して、トランザクション・プロポーザルに署名し、それを指定されたチャネル上の適切なエンドース・ピアに送信します。 この初期トランザクション・プロポーザルは、エンドースメントのための**要求**です。
2. チャネル上の各ピアは、送信元クライアントのアイデンティティーと権限を検査し、有効であれば、指定された入力に対して指定されたチェーンコードを実行します。 各ピアは、呼び出されたチェーンコードのトランザクション結果とエンドースメント・ポリシーに基づき、署名付きの YES または NO 応答をアプリケーションに返します。 それぞれの署名付き YES 応答が、トランザクションの**エンドースメント**です。

	トランザクション・フローがここまで来ると、プロセスは照会とトランザクションで枝分かれします。 プロポーザルがチェーンコードで照会関数を呼び出した場合、アプリケーションはデータをクライアントに返します。 プロポーザルがチェーンコードで台帳を更新する関数を呼び出した場合、アプリケーションは引き続き以下のステップを実行します。
3. アプリケーションは、読み取り/書き込みセットとエンドースメントを含むトランザクションを**順序付けサービス**に転送します。
4. そのトランザクションが順序付けサービスに中継されます。 すべてのチャネル・ピアは、チェーンコード固有の検証ポリシーを適用して並行性制御バージョン検査を実行することによって、ブロック内の各トランザクションを検証します。
	* 検証プロセスで不合格になったトランザクションにはブロック内で無効のマークが付けられます。そして、そのブロックはチャネルの台帳に追加されます。
	* すべての有効なトランザクションは、変更されたキー/値のペアに従って状態データベースを更新します。

**gossip データ配布プロトコル**は、チャネル全体に台帳データを継続的にブロードキャストして、ピア間で台帳が同期しているようにします。 詳しくは、[Hyperledger Fabric 資料 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/){:new_window} の [Gossip data dissemination protocol ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/gossip.html){:new_window} を参照してください。

トランザクション・フローの詳しい紹介については、[Hyperledger Fabric 資料 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/){:new_window} の [Transaction Flow![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/txflow.html){:new_window} を参照してください。
