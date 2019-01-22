---

copyright:
  years: 2018
lastupdated: "2018-11-27"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# ブロックチェーン・コンポーネントの概要

{{site.data.keyword.blockchainfull}} Platform (IBP) のコンポーネントと構造は、{{site.data.keyword.IBM_notm}} が大きく貢献しているオープン・ソースの許可制ブロックチェーン・ソリューションである [Hyperledger Fabric ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/) の基礎となるインフラストラクチャーおよびツールに基づいています。Fabric に基づくネットワークには、いくつかの標準コンポーネントが含まれており、それらはさまざまなユース・ケースをサポートする多数の構成にデプロイできます。

Fabric ネットワークのより包括的な説明、およびその構成コンポーネントの相互関係については、Fabric コミュニティー資料の[ブロックチェーン・ネットワークの構造についてのこの資料](https://hyperledger-fabric.readthedocs.io/en/release-1.2/network/network.html)を確認してください。ここでは、ネットワークをどのように開始して拡張できるかが説明されています。

Fabric に基づくネットワークのコンポーネントの概要については、以下のビデオを確認してください。

<iframe class="embed-responsive-item" id="youtubeplayer" title="スターター・プランのビデオ" type="text/html" width="640" height="390" src="https://www.youtube.com/embed/sJaT2L99BUo" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen> </iframe>

*このビデオでは、スターター・ネットワークおよびエンタープライズ・ネットワークの観点からコンポーネントに焦点を当てていますが、情報は ICP (IBM Cloud プラットフォーム) 上の IBP のカスタマー管理ソリューションに大いに関連しています。*

この概要では、認証局 (CA)、順序付けプログラム、ピア、スマート・コントラクト、およびアプリケーションにのみ焦点を当てます。[{{site.data.keyword.blockchainfull_notm}} Platform for ICP デプロイメント・ガイド](ibp_for_icp_deployment_guide.html)のトピックに示されているように、この順序は任意ではなく、Fabric に基づくネットワークのコンポーネントがデプロイされる順序を反映しています。

## 認証局
{: #ca}

Fabric に基づくブロックチェーン・ネットワークの基礎となるのは、ID と許可です。ID は、CA が発行する x.509 証明書の形式を取り、誰かを*識別する* という点でクレジット・カードと類似しています。ID には属性を含めることができます。これらの証明書は、コンポーネント・レベルまたはチャネル・レベルで MSP フォルダーに含められることによって、許可にリンクされます。したがって、例えば、ピア MSP に **admins** という MSP サブフォルダーがあるとします。その管理フォルダー内に証明書があるユーザーは、ピアの管理者である、つまり、そのピアの管理者が実行を許可されているアクションを実行できることを意味します。ピア内部の検証システムでは、署名証明書によって識別されるユーザーが管理アクションを実行しようとするたびに検査が実行されます。証明書が「admin」フォルダー内の証明書と一致するかどうか検査されます。一致する場合は、アクションを実行できます。そうでない場合は、アクションの実行要求は拒否されます。

{{site.data.keyword.blockchainfull_notm}} CA は [Hyperledger Fabric-CA](https://hyperledger-fabric-ca.readthedocs.io/en/latest/) に基づいていますが、x.509 証明書に基づく PKI を使用している場合は、別の CA を使用できます。複数レベルの CA が存在する場合があり、通常は、そうあるべきです。ネットワークの「ルート CA」は通常、「中間 CA」に証明書を提供する場合以外は公開されません。中間 CA は、証明書をユーザーおよびコンポーネントに直接発行したり、さらに多くの中間 CA 層に発行したりします。認証局を使用して ID およびメンバーシップを確立する方法について詳しくは、[ID についての Hyperledger Fabric の資料 ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/latest/identity/identity.html) および[メンバーシップについての資料 ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/latest/membership/membership.html) を参照してください。

## 順序付けプログラム
{: #orderer}

順序付けサービスはしばしばネットワークの「心臓」と呼ばれますが、その機能は、実際には非常に単純であり、トランザクションをブロックに順序付けして、台帳に書き込まれるようにそれらをピアに戻すことです。前のバージョンの Fabric では、この機能はピア内部に組み込まれていましたが、Fabric v1.0 からは、別個のコンポーネントに分離され、ピアのパフォーマンスが向上し、フォーク状態になる異常が避けられるようになりました。

物理レベルでは、通常、この順序付け機能には一連の順序付けプログラム (まとめて「順序付けサービス」と呼ばれる) が必要ですが、テスト環境または POC 環境では単一ノード (SOLO 順序付けプログラムと呼ばれる) を使用できます。

## ピア
{: #peer}

物理レベルでは、ブロックチェーン・ネットワークは主にピア・ノード (または単にピア) で構成されます。ピアは、台帳およびスマート・コントラクト (「チェーンコード」に含まれている) をホストするので、ネットワークの基本的な要素です。より正確に表現すると、ピアは台帳の**インスタンス**およびスマート・コントラクトの**インスタンス**をホストします。スマート・コントラクトおよび台帳は、ネットワーク内でそれぞれ、共有プロセスおよび共有情報をカプセル化するために使用されるので、ピアのこのような側面は、Fabric ネットワークが実際にどのように機能しているかを理解する良い開始点となります。

ピアについて詳しくは、Fabric コミュニティー資料の[ピアにのみ焦点を当てたこの資料](https://hyperledger-fabric.readthedocs.io/en/release-1.2/peers/peers.html)を確認してください。

## スマート・コントラクト

企業が相互に取引を行う前には、ルールやプロセスについての共通理解に到達して、何らかの契約で規定される必要があります。つまり、これらの契約によって、ビジネス・パートナー間のすべての相互作用を管理する「ビジネス・モデル」が示されます。

同様のニーズがブロックチェーン・ネットワークに存在します。これらのビジネス・モデルは業界用語で「スマート・コントラクト」と呼ばれ、Fabric (および IBP) では、「チェーンコード」と呼ばれるより大規模な構造にこれらの契約が含まれます。これには、ビジネス・ロジックだけでなく、スマート・コントラクトの呼び出しを試行しているユーザーの ID を検証する基礎的なインフラストラクチャーも含まれています。

ビジネスの世界では、契約は署名されて法律事務所で保管されますが、スマート・コントラクトはピアにインストールされて、チャネルに「インスタンス化」されます。

## アプリケーション

IBP などの Fabric ベースのネットワーク内のクライアント・アプリケーションは、API、SDK、およびスマート・コントラクトなどの基礎となるインフラストラクチャーを活用して、高レベルの抽象化でクライアントの対話 (呼び出しおよび照会) を可能にします。

Fabric に基づくネットワークとアプリケーションの対話方法については、[最初のアプリケーション作成についての資料 ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/master/write_first_app.html "Writing Your First Application") および[アプリケーションの開発についてのセクション ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/master/developapps/developing_applications.html "Developing Applications") を確認してください。

後者のリンクは、`Contract` クラスなどの Fabric 機能に言及していますが、それらは Fabric v1.2 (スターター・プランおよび ICP 上の IBP リリースのベースになっているもの) との互換性はないので注意してください。ただし、このセクションには、Fabric v1.2 コンポーネントとのインターフェースを持つアプリケーションを開発する場合に留意する価値のある概念的な情報やその他の役立つヒントが含まれています。
