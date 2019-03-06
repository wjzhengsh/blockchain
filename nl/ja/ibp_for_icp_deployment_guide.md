---

copyright:
  years: 2018, 2019
lastupdated: "2019-02-08"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private の概説
{: #get-started-icp}

***[このページは参考になりましたか。 ご意見をお聞かせください。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

{{site.data.keyword.blockchainfull}} Platform for {{site.data.keyword.cloud_notm}} Private では、{{site.data.keyword.cloud_notm}} Private などのオンプレミス環境の x86、LinuxONE、および IBM Z に、Kubernetes Helm チャートを使用して認証局 (CA)、順序付けプログラム、およびピアをデプロイし、複数のクラウド環境にホストされているコンポーネントにそれらを接続することができます。{{site.data.keyword.cloud_notm}} Private は、オンプレミスのコンテナー化されたアプリケーションを開発して管理するためのアプリケーション・プラットフォームです。 コンテナーを管理するための統合環境を備えており、コンテナー・オーケストレーター Kubernetes、プライベート・イメージ・レジストリー、管理コンソール、およびモニタリング・フレームワークも含まれています。
{:shortdesc}

[Hyperledger Fabric](https://hyperledger-fabric.readthedocs.io/en/release-1.2/) に基づくブロックチェーン・ネットワークは、ほぼ無数の構成にデプロイして、多くのユース・ケースをサポートできます。 このように順応性があるにもかかわらず、特にネットワーク・コンポーネントをセットアップしてデプロイするための適切な**シーケンス**については、多くのベスト・プラクティスがあります。

このデプロイメント・ガイドでは、{{site.data.keyword.cloud_notm}} Private 上で機能する {{site.data.keyword.blockchainfull}} Platform ネットワークをセットアップするための適切な手順と、デプロイ時に考慮すべきベスト・プラクティスなどについて説明します。 ただし、機能する CA、順序付けプログラム、またはピアのみをセットアップする場合は、基本的なルールが適用されます。 単一の順序付けサービス・ノードのみをデプロイする SOLO 順序付けサービスは、実稼働環境用ではないことに注意してください。 SOLO を実行するネットワークまたはチャネルは、「実稼働」環境と見なすことはできません。 ただし、ピアと CA を実稼働環境にデプロイすることはできます (特に、可用性が高い場合)。

{{site.data.keyword.cloud_notm}} Private に {{site.data.keyword.blockchainfull_notm}} Platform をデプロイするプロセスは難しく、Fabric に関する高度な専門知識があることを前提としています。 Fabric、{{site.data.keyword.blockchainfull_notm}} Platform、または {{site.data.keyword.cloud_notm}} Private の知識がなく、開発環境または PoC (概念検証) をセットアップすることが目標である場合は、代わりに[スターター・プラン](/docs/services/blockchain/starter_plan.html#starter-plan-about)を確認することを検討してください。 また、あらゆるデプロイメント構成がサポートされるわけではないことにも注意してください。これは難しいため、Fabric の専門家が管理職務を他の当事者に引き継ぐ前に、デプロイメントとコンポーネント接続のプロセスを行うことを前提としています。 これらの専門家は、このガイドおよび Helm チャート・コンポーネント全般の資料の対象読者です。
{:important}

## ステップ 1: ネットワーク構成の決定

ブロックチェーン・ネットワークの構造は、ユース・ケースで決定する必要があります。 これらの基本的なビジネス上の決定は、状況によって異なりますが、以下の点を考慮してください。

* **開発環境**のセットアップ

  このために[スターター・プラン](/docs/services/blockchain/starter_plan.html#starter-plan-about)が構築されていますが、開発環境で独自のコンポーネントを管理する場合は、順序付けプログラムとピア (さらに組織ごとに 1 つの CA) で構成される基本構成が必要になります。 組織ごとに、[fabcar ネットワーク](https://hyperledger-fabric.readthedocs.io/en/release-1.2/understand_fabcar_network.html)の構成と同様の 1 つの順序付けプログラムと 1 つのピアのみ含めることを決定できます。 同様に、コンポーネントごとに別々のユーザーを作成するのではなく、これらのすべてのコンポーネントに対して 1 つの管理ユーザーのみ必要とするように決定できます。

* 新規ネットワークまたは既存のネットワークへの**実動コンポーネント**のデプロイ

  実動コンポーネントと実動ネットワークのニーズは、開発環境や PoC (概念検証) とは異なります。 例えば、高可用性が優先されます。 単一の順序付けプログラムのみが含まれる (したがって単一障害点となる) SOLO 順序付けサービスは、定義上は実稼働環境用ではありません。

**注:** このデプロイメント・ガイドでは、すべての反復および可能性のあるネットワーク構成については説明しませんが、考慮すべき一般的なガイドラインとルールを示します。

## ステップ 2: {{site.data.keyword.cloud_notm}} Private での Kubernetes クラスターのセットアップ

ネットワーク構造を決定したら、ユース・ケースに対応するように {{site.data.keyword.cloud_notm}} Private 上に Kubernetes クラスターをセットアップします。 詳しくは、[{{site.data.keyword.cloud_notm}} Private のセットアップ](/docs/services/blockchain/ICP_setup.html#icp-setup)を参照してください。

## ステップ 3: CA のセットアップ

Fabric ベースのブロックチェーン・ネットワークでは、デプロイする必要がある最初のコンポーネントは CA です。 これは、コンポーネントのデプロイ前にその操作を許可されたユーザー ID がコンポーネントの構成に少なくとも 1 つ含まれている必要があるためです。

これは、概念的に理解するのが難しい場合があります。 例えば、ラップトップなどの電子デバイスには、新しい場合は誰でもアクセスできます。 デバイスをオンにした最初のユーザーは「所有者」と見なされるため、パスワードを登録および設定でき、後続のすべてのユーザーはそのパスワードを知っている必要があります。 ただし、Fabric ベースのブロックチェーン・ネットワークのコンポーネントのセットアップ方法はこれとは異なります。 ピアおよび順序付けプログラムは、最初にデプロイされるときに管理情報が組み込まれている必要があります。

**組織ごとに 1 つの CA をデプロイすることをお勧めします。** 例えば、ある組織に関連付けられている 3 つのピアと別の組織に関連付けられている順序付けプログラムをデプロイする場合、2 つの CA をデプロイする必要があります。 一方の CA はピア組織用、もう一方は順序付け組織用です。

CA の無限回帰 (すべての CA を別の CA に無限にリンクする必要がある) を防止するために、CA にはデフォルトのユーザー名とパスワードのペアが関連付けられています。 このユーザー名とパスワードは、CA のデプロイ時に指定します。 この CA は、コンポーネントのトラスト・ルートである「ルート CA」です。 実動ネットワークでは、このルート CA から証明書を取得する他の CA を少なくとも 1 つデプロイし、「中間 CA」とも呼ばれるその CA を使用して証明書をユーザーとコンポーネントに発行することをお勧めします。 ただし、このリリースでは、中間 CA の使用はサポートされていません。

すべての組織に、エンロール用の CA と TLS CA が必要です。  {{site.data.keyword.cloud_notm}} Private に CA をデプロイすると、TLS CA もデフォルトで同じコンテナーにデプロイされます。 この TLS CA によって、TLS 証明書が生成され、管理されます。 スターターまたはエンタープライズ・プラン・ネットワーク上の CA には TLS CA は含まれていませんが、エンロールに使用される CA は含まれています。 したがって、ピアをスターターまたはエンタープライズ・プラン・ネットワークに接続する場合は、ピアをデプロイする前に、そのピアの TLS CA として機能する [{{site.data.keyword.cloud_notm}} Private に CA をデプロイ](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy)する必要もあります。 [{{site.data.keyword.cloud_notm}} Private ピアを {{site.data.keyword.blockchainfull_notm}} Platform に接続するための前提条件](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-prerequisites)も参照してください。 TLS CA は証明書の発行にのみ使用され、そのアクティビティーの完了時にシャットダウンできます。

TLS について詳しくは、Hyperledger Fabric の資料で [Securing Communication With Transport Layer Security (TLS) ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.3/enable_tls.html "Securing Communication With Transport Layer Security (TLS)") を参照してください。

### 順序付けプログラムおよびピア用の MSPS の準備

{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private プロセスは非常に複雑であるため、初期セットアップ時にすべてのネットワーク・コンポーネント・ノードの管理者として単一の管理ユーザー ID を使用することをお勧めします。 これにより、1 人のユーザーがさまざまなコンポーネント間の構成および接続をセットアップして、正しく機能することを確認することで、デプロイメントおよび接続エラーを削減できます。 ただし、各コンポーネントに異なる証明書があることが非常に重要です。 ここでの違いは、見落としやすい場合があります。 トランザクション提案に署名するエンティティーはピアの管理者ではなく、**ピア自体**です。 したがって、ピアはエンロールされている必要があり、その操作に付加する証明書と特定の種類の署名を生成するために使用できる秘密鍵を持っている必要があります。 Fabric ベースのブロックチェーン・ネットワークにおける ID および許可について詳しくは、Fabric の資料で [Identity ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.3/identity/identity.html "Identity") および [Membership ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.3/membership/membership.html "Membership") を参照してください。

[CA の操作に関する文書](/docs/services/blockchain/howto/CA_operate.html#ca-operate-fabric-ca-client)の指示に従ってインストールする必要がある Fabric CA クライアントを使用して、ID を登録およびエンロールします。 この ID は、デプロイするネットワーク・コンポーネントの管理者であり、構成の変更およびコンポーネントの接続を可能にします。 これらのコンポーネントの管理を後で他のユーザーに転送する場合は、これらのコンポーネントの新規管理者を登録してエンロールし、必要に応じて管理者として自分自身を削除できます。

順序付けプログラムまたはピアがデプロイされると、順序付けプログラムまたはピアに関連付けられた `init` コンテナーは、Kubernetes 秘密オブジェクトを使用してコンポーネントの MSP を作成します。 秘密オブジェクトの作成方法については、[CA の操作](/docs/services/blockchain/howto/CA_operate.html#ca-operate)を参照してください。 前述したように、組織ごとに CA をセットアップし、このフローを繰り返す必要があります。

## ステップ 4: 順序付けプログラムおよびピアのデプロイ

Kubernetes 秘密が作成されると、コンポーネントをデプロイする準備が整います。 チャネルを確立する場合は、どのピアよりも前に順序付けプログラムをデプロイすることをお勧めします。 すべてのコンポーネントに異なる組織名を使用してください。

- **[順序付けプログラムをデプロイします](/docs/services/blockchain/howto/orderer_deploy_icp.html#icp-orderer-deploy)**。 現時点では、SOLO 順序付けサービスのみがサポートされていることに注意してください。 計算に関連するデプロイメント・プロセスにはオプションがあります。

- **[{{site.data.keyword.cloud_notm}} Private 順序付けプログラムに接続するピアをデプロイします](/docs/services/blockchain/howto/peer_deploy_icp.html#icp-peer-deploy)**。 Helm チャートでは、データベース・タイプなどのピアをデプロイするための多数のオプションを指定できます。 ピアの組織 MSP ID が順序付けプログラムの組織 MSP ID と異なることを確認してください。

- **[{{site.data.keyword.blockchainfull_notm}} Platform ネットワークに接続するピアをデプロイします](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy)**。 ピアをデプロイするプロセスと、それを {{site.data.keyword.cloud_notm}} の[スターター・プラン](/docs/services/blockchain/starter_plan.html#starter-plan-about)または[エンタープライズ・プラン](/docs/services/blockchain/enterprise_plan.html#enterprise_plan-about)・ネットワークに接続するプロセスは、接続プロファイルとネットワーク・モニター UI を使用するため異なります。 ピアが属する組織は、ネットワーク内のチャネルに既に参加している必要があります。 この場合も、ピアの組織 MSP ID が順序付けプログラムの組織 MSP ID と異なることを確認してください。

## 次のステップ

すべてのノードをデプロイしたら、ノードの操作を開始して、トランザクションを送信できます。詳細については、以下のリンクを参照してください。

- [{{site.data.keyword.cloud_notm}} Private での認証局の操作](/docs/services/blockchain/howto/CA_operate.html#ca-operate)
- [{{site.data.keyword.cloud_notm}} Private での順序付けプログラムの操作](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate)
- [マルチクラウド・ネットワークを使用した {{site.data.keyword.cloud_notm}} Private でのピアの操作](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate)
- [スターター・プランまたはエンタープライズ・プランを使用した {{site.data.keyword.cloud_notm}} Private でのピアの操作](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate)

## ネットワークの拡大

開発環境または PoC (概念検証) をセットアップすることが目標である場合は、順序付けプログラムのデプロイメント時に作成される順序付けシステム・チャネルにピア組織を追加する必要があります。 これは、すべてのコンポーネントを使用するマルチステップ・プロセスであり、関連する操作トピックで説明されています。

1. 組織を追加する前に、組織を作成して MSP ID を設定する必要があります。 [CA の操作](/docs/services/blockchain/howto/CA_operate.html#ca-operate-deploy-orderer-peer)を参照してください。

2. 組織は、作成後に順序付けシステム・チャネルに追加できます。 詳しくは、[順序付けプログラムの操作](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-add-organizations-to-consortium)を参照してください。

3. 順序付けシステム・チャネルに組織がリストされる場合、その組織は「共同事業体」のメンバーであり、アプリケーション・チャネル (トランザクションが発生するチャネルの種類) を作成できるようになります。 チャネルの作成方法について詳しくは、[チャネルの作成](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-create-channel)を参照してください。
