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
{:pre: .pre}

# ネットワークにスマート・コントラクトをデプロイするチュートリアル
{: #ibp-console-smart-contracts}

スマート・コントラクトとは、ブロックチェーン台帳のデータの読み取りと更新を可能にするコードであり、チェーンコードとも呼ばれます。 スマート・コントラクトは、ビジネス・ロジックを、ブロックチェーン・ネットワークのすべてのメンバーから承認および検証を受ける実行可能プログラムに変換できます。 このチュートリアルは、[サンプル・ネットワークのチュートリアル・シリーズ](/docs/services/blockchain/howto/ibp-console-smart-contracts.md.html#ibp-console-smart-contracts-structure)の第 3 部であり、ブロックチェーン・ネットワークでトランザクションを開始するためにスマート・コントラクトをデプロイする方法について説明します。
{:shortdesc}

**対象者:** このトピックは、ブロックチェーン・ネットワークの作成、モニター、管理を担当するネットワーク・オペレーターを対象に設計されています。また、スマート・コントラクトの作成方法を参照しているセクションに興味があるアプリケーション開発者も対象にしています。

## サンプル・ネットワークのチュートリアル・シリーズ
{: #ibp-console-smart-contracts-structure}

この 3 部構成のチュートリアル・シリーズでは、{{site.data.keyword.blockchainfull_notm}} Platform 2.0 コンソールを使用し Kubernetes クラスターへのネットワークのデプロイやスマート・コントラクトのインストールとインスタンス化を行うことで、比較的にシンプルなマルチノードの Hyperledger Fabric ネットワークを作成して相互接続するプロセスを体験していきます。

* [ネットワーク構築チュートリアル](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network)では、順序付けプログラムとピアを作成することで、ネットワークをホストするプロセスを学習します。
* [ネットワーク参加チュートリアル](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network)では、ピアを作成してチャネルに参加させることで、既存のネットワークに参加するプロセスを学習します。
* **ネットワークにスマート・コントラクトをデプロイする** (このチュートリアル) では、スマート・コントラクトを作成してネットワークにデプロイする方法を学習します。

これらのチュートリアルの手順を使用すれば、複数の組織が参加する開発テスト用のネットワークを 1 つのクラスター内に構築できます。 順序付けサービス・ノードを作成して組織を追加することで、
ブロックチェーン・コンソーシアムを形成する場合は、**ネットワーク構築**のチュートリアルを使用してください。ピアをネットワークに接続するには、**ネットワーク参加**のチュートリアルを使用してください。 さまざまなコンソーシアム・メンバーと一緒にこれらのチュートリアルを進めると、実際に**分散した**ブロックチェーン・ネットワークを作成できます。  

この最後のチュートリアルの目的は、スマート・コントラクトの作成とパッケージ化、ピア上でのスマート・コントラクトのインストール、チャネル上でのスマート・コントラクトのインスタンス化の方法を示すことです。  

スマート・コントラクトは、ピアにインストールしてから、チャネルでインスタンス化します。 **スマート・コントラクトを使用してトランザクションを送信したりデータを読み取ったりするメンバーはすべて、そのコントラクトをピアにインストールする必要があります。** スマート・コントラクトは、名前とバージョンによって定義されます。 そのため、スマート・コントラクトを実行するチャネルのすべてのピアで、インストールしたコントラクトの名前とバージョンの両方が一致していなければなりません。

スマート・コントラクトを各ピアにインストールしたら、単一のネットワーク・メンバーが、そのコントラクトをチャネルでインスタンス化します。 この操作を実行するには、そのネットワーク・メンバーがチャネルに参加していなければなりません。 インスタンス化すると、台帳がそのスマート・コントラクト用の初期データに更新され、コントラクトがインストールされているチャネルに参加しているピア上でスマート・コントラクト・コンテナーが開始されます。 これにより、ピアは、実行中のコンテナーを使用してトランザクションを実行できるようになります。  
- **スマート・コントラクトをインスタンス化する必要があるネットワーク・メンバーは 1 つだけです。**
- **スマート・コントラクトがインストールされたピアが、同じスマート・コントラクトのバージョンが既にインスタンス化されたチャネルに参加すると、スマート・コントラクト・コンテナーが自動的に開始されます。**  

このチュートリアルでは、{{site.data.keyword.blockchainfull_notm}} Platform コンソールを使用してネットワークへのスマート・コントラクトのデプロイメントを管理するための次の手順について説明します。

- [ピアにスマート・コントラクトをインストールする](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-install)。
- [チャネルでそれらをインスタンス化する](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-instantiate)。
- [エンドースメント・ポリシーを指定する](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-endorse)。
- [スマート・コントラクトのポリシーとコードをアップグレードする](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-upgrade)。


## 始めに

スマート・コントラクトをインストールする前に、次の準備を完了させてください。

- {{site.data.keyword.blockchainfull_notm}} Platform コンソールを使用して、[ネットワークを構築する](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network)か、または[ネットワークに参加する](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network)必要があります。
- スマート・コントラクトはピアにインストールするので、必ず、コンソールに[ピアを追加](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-create-peer-org1)してください。  
- スマート・コントラクトはチャネルでインスタンス化されます。 このため、コンソールを使用して、[チャネルを作成する](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-create-channel)か、または[チャネルに参加する](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-join-peer-org2)必要があります。

## 手順 1: スマート・コントラクトを作成する

{{site.data.keyword.blockchainfull_notm}} コンソールで管理できるのは、スマート・コントラクトの*デプロイメント* であり、開発ではありません。 スマート・コントラクトの開発に興味がある場合は、手始めに、Hyperledger Fabric コミュニティーで提供されているチュートリアルと、{{site.data.keyword.IBM_notm}} から提供されているツールを使用すると良いでしょう。

- スマート・コントラクトを使用して複数の団体の間でトランザクションを実行する方法については、Hyperledger Fabric の資料の[アプリケーションの開発に関するトピック ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/developing_applications.html "アプリケーションの開発に関するトピック") を参照してください。
- アプリケーションを使用してスマート・コントラクトと対話する方法について最初から最後まで説明したチュートリアルについては、[Hyperledger Fabric コマーシャル・ペーパー・チュートリアル ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/tutorial/commercial_paper.html "Hyperledger Fabric Commercial Paper tutorial") を参照してください。
- スマート・コントラクトにアクセス制御メカニズムを組み込む方法については、[開発者向けのチェーンコード ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/chaincode4ade.html#chaincode-access-control "Chaincode for Developers") を参照してください。
- スマート・コントラクトを作成する準備ができたら、[{{site.data.keyword.blockchainfull_notm}} Visual Studio Code 拡張機能 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://marketplace.visualstudio.com/items?itemName=IBMBlockchain.ibm-blockchain-platform "{{site.data.keyword.blockchainfull_notm}} Platform - Visual Studio Marketplace") を使用して、独自のスマート・コントラクト・プロジェクトの作成を開始できます。 また、この拡張機能を使用して、[Visual Studio Code からネットワークに直接接続する](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-vscode)こともできます。
- インストールする準備ができたら、スマート・コントラクトをピアにインストールできるように [.cds 形式 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/latest/chaincode4noah.html#packaging "packaging smart contracts") でパッケージ化する必要があります。 詳しくは、[スマート・コントラクトのパッケージ化](/docs/services/blockchain/vscode-extension.html#packaging-a-smart-contract)を参照してください。 あるいは、[ピア CLI コマンド ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/commands/peerchaincode.html#peer-chaincode-package "ピア・チェーンコード・パッケージ") を使用してパッケージを構築することもできます。
<!-- Update the tutorial link to release1-4 when it is published -->


## 手順 2: スマート・コントラクトをインストールする
{: #ibp-console-smart-contracts-install}

コンソールを使用して、以下の手順を実行します。

1. 1 つ以上のスマート・コントラクトをインストールするための**「スマート・コントラクト」**タブをクリックします。
2. **「スマート・コントラクトのインストール (Install smart contract)」**をクリックし、[.cds 形式 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/latest/chaincode4noah.html#packaging "packaging smart contracts") のスマート・コントラクト・パッケージ・ファイルをアップロードします。
{{site.data.keyword.blockchainfull_notm}} Visual Studio Code 拡張機能を使用して、[スマート・コントラクト・パッケージを作成](/docs/services/blockchain/vscode-extension.html#packaging-a-smart-contract)できます。 **「スマート・コントラクト」**タブでパッケージをインストールするときに、スマート・コントラクトをインストールするピア・ノードを 1 つ以上選択できます。

コンソールにピアが 1 つしかない場合は、そのピアにスマート・コントラクトがインストールされます。 スマート・コントラクトのインストール先のピアを選択するためのプロンプトは表示されません。 「ノード」タブにナビゲートし、コンソールで管理しているピアをクリックすると、ピアにインストールされているスマート・コントラクトのリストが表示されます。

スマート・コントラクトをチャネルでインスタンス化した後でも、この**「スマート・コントラクト」**タブに戻り、追加のピアにスマート・コントラクトをインストールすることができます。 インストールするスマート・コントラクトのバージョンが、インスタンス化されたバージョンと同じであれば、それらの追加のピアも既存のピアと同じようにトランザクションを処理できます。
{:tip}

**このコンソールでは、Hyperledger Composer の `.bna` ファイルはインストールできません。**

<!-- Instead, `.bna` files must be installed on a peer by using the [`Composer` CLI commands ![External link icon](../images/external_link.svg "External link icon")]("peer chaincode" "peer chaincode").  -->

## 手順 3: スマート・コントラクトをインスタンス化する
{: #ibp-console-smart-contracts-instantiate}

スマート・コントラクトはチャネルでインスタンス化されます。 チャネルにピアを参加させているコンソール・メンバーは、だれでもスマート・コントラクトをインスタンス化し、関連付けられた[エンドースメント・ポリシー](/docs/services/blockchain/glossary.html#glossary-endorsement-policy)を指定できます。

コンソールを使用して、以下の手順を実行します。

1. 「スマート・コントラクト」タブで、ピアにインストールされているスマート・コントラクトをリストから見つけて、その行の右側のオーバーフロー・メニューにある**「インスタンス化」**をクリックします。
2. 開かれたサイド・パネルで、スマート・コントラクトをインスタンス化するチャネルを選択し、そのチャネルが存在する順序付けプログラムを選択します。 `channel1` という名前のチャネルと、`Orderer` という名前の順序付けプログラム・ノード (作成済みのもの) を選択できます。次に、**「次へ」**をクリックします。
3. [スマート・コントラクトのエンドースメント・ポリシー](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-endorse)を指定します。これについては、後のセクションで説明します。
4. ポリシーに含める組織のメンバーも選択する必要があります。 このチュートリアルに沿って進める場合は、`org1msp` を選択します。**ネットワーク構築**チュートリアルと**ネットワーク参加**チュートリアルの両方を完了している場合は、`org2msp` も選択します。
5. 最後のパネルでは、スマート・コントラクトの開始時に実行するスマート・コントラクトの関数と、その関数に渡す関連引数を指定することが求められます。

あるチャネルでインスタンス化されているスマート・コントラクトをすべて表示するには、左側のナビゲーションでチャネルのアイコンをクリックし、テーブルからチャネルを選択し、**「チャネルの詳細 (Channel details)」**タブをクリックします。

無料の {{site.data.keyword.cloud_notm}} Kubernetes サービス・クラスターを使用する場合は、有料クラスターに比べてインスタンス化に非常に時間がかかる可能性があることに注意してください。 クラスターにデプロイしたピア数に応じて、数分かかる可能性があります。

スマート・コントラクトにプライベート・データ・コレクション定義が含まれている場合、スマート・コントラクトを {{site.data.keyword.blockchainfull_notm}} コンソールからインスタンス化することはできません。 プライベート・データが含まれたスマート・コントラクトをインスタンス化する方法についての[推奨事項](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data)を参照してください。
{:note}

**インストールとインスタンス化**の組み合わせは強力な機能です。これにより、ピアは単一のスマート・コントラクトを多くのチャネルで使用できるからです。 使用するスマート・コントラクトは同じでも、データにアクセスできるネットワーク・メンバーが異なる複数のチャネルにピアが参加する必要がある場合があります。 ピアは、スマート・コントラクトを 1 回インストールすれば、同じスマート・コントラクトがインスタンス化されているすべてのチャネルで、そのチェーンコード・コンテナーを使用できます。 この軽量なアプローチは、コンピュートおよびストレージ・スペースを節約できるので、ネットワーク拡張の役に立ちます。

## 手順 4: クライアント・アプリケーションを使用してトランザクションを送信する
{: #ibp-console-smart-contracts-connect-to-SDK}

スマート・コントラクトをチャネルでインスタンス化したら、クライアント・アプリケーションを使用してネットワークの他のメンバーと取引を実行できます。 アプリケーションから、スマート・コントラクトに含まれているビジネス・ロジックを呼び出して、ブロックチェーン台帳上の資産を作成、移転、更新できます。

### SDK を使用した接続
{: #ibp-console-smart-contracts-connect-to-SDK-panel}
**「スマート・コントラクト」**タブには、インスタンス化されたスマート・コントラクトにクライアント・アプリから接続するために必要な情報が含まれています。 インスタンス化された各スマート・コントラクトの隣にあるオーバーフロー・メニューにナビゲートします。 **「SDK を使用して接続する (Connect with your SDK)」**ボタンをクリックします。 サイド・パネルが開き、そのスマート・コントラクトに接続するために必要な情報 (コントラクト名、チャネル名、および接続プロファイル) が表示されます。 詳しくは、
[アプリケーションの作成](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app)を参照してください。

## エンドースメント・ポリシーの指定
{: #ibp-console-smart-contracts-endorse}

どのスマート・コントラクトにもエンドースメント・ポリシーが必要です。エンドースメント・ポリシーは、インスタンス化するときに指定します。 エンドースメント・ポリシーには、チャネルでスマート・コントラクトを実行して、トランザクションの出力を独立して検証できる一連の組織とピアを指定します。 例えば、エンドースメント・ポリシーで、チャネルのメンバーの大多数がトランザクションを承認する場合にのみそのトランザクションが台帳に追加されることを指定できます。 スマート・コントラクトをインスタンス化した組織が、そのスマート・コントラクトをインストール済みのメンバーの中から、バリデーターにするメンバーを選択できます。また、この組織がすべてのチャネル・メンバーに対するエンドースメント・ポリシーを設定します。 エンドースメント・ポリシーは、[スマート・コントラクトの更新](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-upgrade)の手順に従って更新できます。

[スマート・コントラクトをインスタンス化する](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-instantiate)手順の中で、サイド・パネルでチャネルを選択した後にコントラクトのエンドースメント・ポリシーを設定できます。 このパネルでは、チャネルにスマート・コントラクトをインストール済みのピアのリストから、トランザクションを承認する必要があるピアを選択して、シンプルなポリシーを指定できます。 この方法を使用して、すべてのチャネル・メンバー、大多数のチャネル・メンバー、または 1 メンバーによるエンドースメント・ポリシーを指定できます。また、メンバーの自己署名を禁止にするシンプルな「+ 1」のエンドースメント・ポリシーを指定することもできます。 デフォルトのエンドースメント・ポリシーは、`1 of x` に設定されます。つまり、スマート・コントラクト・トランザクションを承認するために必要なメンバーは 1 つだけです。

ポリシーを JSON 形式で指定する場合は、**「拡張 (Advanced)」**ボタンをクリックします。 この方法では、より複雑なエンドースメント・ポリシーを指定できます。例えば、チャネルのある特定のメンバーが、他の大多数のメンバーと共に、トランザクションを検証しなければならないことを指定したりできます。 他にも[高度なエンドースメント・ポリシーの例 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/arch-deep-dive.html#example-endorsement-policies "Example endorsement policies") が Hyperledger Fabric の資料に記載されています。 JSON 形式でエンドースメント・ポリシーを記述する方法について詳しくは、[Hyperledger Fabric Node SDK の資料 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://fabric-sdk-node.github.io/global.html#ChaincodeInstantiateUpgradeRequest "Hyperledger Fabric Node SDK の資料") を参照してください。

## スマート・コントラクトのアップグレード
{: #ibp-console-smart-contracts-upgrade}

スマート・コントラクトと台帳上の資産の関係を維持したまま、スマート・コントラクトをアップグレードしてコードまたはエンドースメント・ポリシーを変更することができます。 インスタンス化されたスマート・コントラクトのアップグレードが必要になる理由には、さまざまな理由があります。
1. スマート・コントラクトをアップグレードすれば、機能を追加/削除したり、ビジネス・ネットワークのロジックを修正したりできる。
2. 新規メンバーをチャネルに追加したら、インスタンス化されたスマート・コントラクトのエンドースメント・ポリシーを、その新規チャネル・メンバーを含むように更新することが*必須である* 。 スマート・コントラクト自体には変更がなくても、新規チャネル・メンバーと連携するために、新しいバージョン番号でスマート・コントラクトをもう一度パッケージ化し、チャネルでインスタンス化する必要があります。 そうしないと、トランザクション・エンドースメントは成功しません。
3. スマート・コントラクトの初期設定の引数が変更された。

**インスタンス化されたスマート・コントラクトをアップグレードする前に、新規バージョンのスマート・コントラクトを、前のレベルのスマート・コントラクトを実行しているチャネル内のすべてのピアにインストールする必要があります。**

### スマート・コントラクトのアップグレード方法
{: #ibp-console-smart-contracts-upgrade-howto}

スマート・コントラクトをアップグレードするときには、同じスマート・コントラクト名を指定しますが、新規バージョン番号を使用して、更新したコードをインストールします。 新規バージョンのスマート・コントラクトがチャネルのいずれかのピアにインストールされていると、**「インスタンス化されたスマート・コントラクト (Instantiated smart contracts)」**テーブルの元のバージョンの横に、`「アップグレードが利用可能 (Upgrade Available)」`ボタンが表示されるようになるので注意してください。

{:important}
スマート・コントラクトを実行する新規メンバーがチャネルに参加する場合は、スマート・コントラクトの更新が必須です。そのためには、すべてのピアに新規バージョンをインストールし、新規メンバーを含むように変更したエンドースメント・ポリシーを使用して、チャネルでスマート・コントラクトをインスタンス化します。

- **「インスタンス化されたスマート・コントラクト (Instantiated smart contracts)」**テーブルまでスクロールダウンします。
- **「インスタンス化されたスマート・コントラクト (Instantiated smart contracts)」**テーブルで、アップグレードするスマート・コントラクトのバージョンとチャネルを見つけます。 そのバージョンの横に、**「アップグレードが利用可能 (Upgrade Available)」**ラベルが付いているはずです。
- スマート・コントラクトの行の右側にある**オーバーフロー・メニュー**をクリックし、**「アップグレード」**をクリックして、以下の手順を実行します。  

 1. チャネルでアップグレードするスマート・コントラクトのバージョンをドロップダウン・リストから選択します。
 2. チャネル・メンバーを追加または削除して、エンドースメント・ポリシーを更新します。 **「拡張 (Advanced)」**をクリックし、JSON 形式の新しい文字列を貼り付けて、既存のポリシーを変更することもできます。
 3. (オプション) パラメーターを変更した場合は、スマート・コントラクトの初期設定の引数値を変更します。 値がわからない場合は、スマート・コントラクトの開発者に確認してください。 パラメーターが変更されていない場合は、このフィールドはブランクのままでかまいません。

スマート・コントラクトをアップグレードしたら、チャネルでインスタンス化されているコントラクトのバージョンを変更し、新規バージョンをインストールしたすべてのピアのスマート・コントラクト・コンテナーを変更します。

### スマート・コントラクトのアップグレード時の考慮事項
{: #ibp-console-smart-contracts-upgrade-considerations}

1. すべてのピアに新規バージョンのスマート・コントラクトをインストールしなければなりませんか?  

  ピアで呼び出されたスマート・コントラクトは、そのチャネルでインスタンス化されているバージョンを実行しようとします。 前のバージョンがピアで実行されていると、エラーになります。 このため、チャネルでスマート・コントラクトをアップグレードする前に、*前のバージョンを実行しているすべてのピアに最新のバージョンのスマート・コントラクトをインストールすることがベスト・プラクティスです。*  

2. 後のバージョンのスマート・コントラクトでも、前のバージョンのスマート・コントラクトの台帳のデータを処理できますか?  

  はい。 新しいスマート・コントラクトのコードが、互換性のある方法で (新規 JSON フィールドを追加し、既存フィールドのセマンティクスやタイプは変更しない) データを処理している限り、後のバージョンのスマート・コントラクトは前のバージョンのデータに対して機能します。

3. チャネルでスマート・コントラクトを更新する前に、ピアを最新バージョンにアップグレードすることを忘れていた場合、ピアはどうなりますか?  

  新規バージョンのスマート・コントラクトを使用するようにチャネルを更新した後に、まだ前のバージョンを実行するピアがチャネルに存在する場合、それらのピアは、スマート・コントラクトのトランザクションを承認できなくなります。 また、スマート・コントラクトのエンドースメント・ポリシーの定義によっては、トランザクションを台帳にコミットできるだけの十分な承認が得られないというリスクもあります。 しかし、後からでもそれらのピアに新規バージョンのスマート・コントラクトをインストールできます。そうすれば、再びトランザクションを承認できるようになり、事実上、後れを取り戻せます。

## プライベート・データ
{: #ibp-console-smart-contracts-private-data}

プライベート・データは、バージョン 1.2 以上の Hyperledger Fabric ネットワークの機能であり、この機能を使用すると、**チャネル上の**他の組織のメンバーから機密情報を隠すことができます。 データ・プライバシーは、[プライベート・データ・コレクション ![外部リンク・アイコン")](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/private-data/private-data.html#what-is-a-private-data-collection "What is a private data collection?") を使用することで実現しています。 例えば、数人の卸売業者と農業者の集団が 1 つのチャネルに参加しているとします。 内密に取引をしたい農業者と卸売業者は、その目的のためのチャネルを作成することができます。 しかし、販売に関する機密の側面 (価格など) のプライバシーを維持するために、ビジネス上の対話を管理するプライベート・データ・コレクションをスマート・コントラクトに作成することもできます。そうすれば別のチャネルを作成する必要はありません。 ブロックチェーンでプライベート・データを使用するケースについて詳しくは、Fabric 資料の[プライベート・データ ![外部リンク・アイコン")](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/private-data/private-data.html#private-data "Private data") の概念に関する記事を参照してください。

{{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta でプライベート・データを使用するためには、次の 3 つの条件を満たす必要があります。  
1. **プライベート・データ・コレクションを定義する。** プライベート・データ・コレクションのファイルをスマート・コントラクトに追加できます。 そして、実行時に、クライアント・アプリケーションでプライベート・データ固有のチェーンコード API を使用して、コレクションのデータを入力および取得できます。 スマート・コントラクトでプライベート・データ・コレクションを使用する方法について詳しくは、Fabric 資料の[プライベート・データの使用 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/latest/private_data_tutorial.html "Using private data") に関するチュートリアルを参照してください。  

2. **スマート・コントラクトをインストールしてインスタンス化する。** スマート・コントラクトのプライベート・データ・コレクションを定義したら、スマート・コントラクトをチャネルのメンバーであるピアにインストールする必要があります。チャネルでスマート・コントラクトをインスタンス化するときに、コレクションの構成を指定する必要があります。 {{site.data.keyword.blockchainfull_notm}} コンソールは、現在、スマート・コントラクトをインスタンス化する**手順の中で**コレクション定義を指定できるようになっていません。 ただし、Fabric SDK を使用すれば、プライベート・データを使用するスマート・コントラクトをインストール、インスタンス化、更新できます。 詳しくは、Node SDK 資料の [How to use private data ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://fabric-sdk-node.github.io/release-1.4/tutorial-private-data.html "how to use private data") を参照してください。  

 **注:** スマート・コントラクトをインストールまたはインスタンス化するために、クライアントはピアの管理者である必要があります。 そのため、アプリケーション ID を作成するのではなく、コンソール・ウォレットからピア管理者 ID の証明書をダウンロードし、ピア管理者の公開鍵と秘密鍵を SDK に直接渡す必要があります。 鍵ペアを SDK に渡す方法の例については、[低水準の Fabric SDK API を使用したネットワークへの接続](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-low-level)を参照してください。  

3. **アンカー・ピアを構成する。** プライベート・データを使用するには組織間[ゴシップ ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/gossip.html "Gossip data dissemination protocol") を有効にする必要があるため、コレクション定義に含まれている組織ごとにアンカー・ピアが存在しなければなりません。 このアンカー・ピアは特殊な**タイプ**のピアではありません。組織が他の組織に周知するピアに過ぎず、周知する際に、組織間ゴシップをブートストラップします。 このため、コレクション定義に含まれている組織ごとに、少なくとも 1 つの[アンカー・ピア ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/gossip.html#anchor-peers "Anchor peers") を定義する必要があります。
 - アンカー・ピアにするためにピアを構成するには、**「チャネル」**タブをクリックして、スマート・コントラクトがインスタンス化されたチャネルを開きます。  
 - **「チャネルの詳細 (Channel details)」**タブをクリックします。
 - 「アンカー・ピア (Anchor peers)」テーブルにスクロールダウンし、**「アンカー・ピアの追加 (Add anchor peer)」**をクリックします。
 - 組織のアンカー・ピアとして機能させるピアを、コレクション定義内の各組織から少なくとも 1 つ選択します。 冗長性のために、コレクション内の各組織から複数のピアを選択することを検討しても良いでしょう。

これで、チャネルがプライベート・データを使用するように構成されました。

**問題:** コンソールでスマート・コントラクトのインストール、インスタンス化、またはアップグレードを行うとエラーで失敗します。  
**解決方法:** スマート・コントラクトに対する上記の操作のいずれかが失敗する場合は、[ノードのログで](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-node-logs)エラーを確認してください。
