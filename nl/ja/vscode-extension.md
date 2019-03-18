---

copyright:
  years: 2017, 2019
lastupdated: "2019-03-05"

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

# スマート・コントラクトのツール
{: #develop-vscode}

{{site.data.keyword.blockchainfull}} Platform Visual Studio Code 拡張機能は、スマート・コントラクト・パッケージを開発、パッケージ化、およびデプロイするための、Visual Studio Code 内の環境を提供します。 この拡張機能には、簡素化されたローカル・スマート・コントラクト開発用の Hyperledger Fabric の事前構成済みローカル・インスタンスをセットアップするためのコマンドも含まれています。

**注:** {{site.data.keyword.blockchainfull_notm}} Platform 拡張機能は、{{site.data.keyword.blockchainfull_notm}} Platform スターター・プラン・ネットワークおよび Hyperledger Fabric バージョン 1.3.x 以降と互換性があります。

## 前提条件
{: #develop-vscode-prerequisites}

{{site.data.keyword.blockchainfull_notm}} Platform Visual Studio Code 拡張機能をインストールする前に、以下の前提条件を満たしてください。

- **Yeoman のインストール**

  Yeoman は、スケルトン・スマート・コントラクト・プロジェクトの作成に使用されるジェネレーター・ツールです。 Yeoman をインストールするには、`npm install -g yo` コマンドを使用します。

- **Yeoman 用の Fabric ジェネレーターのインストール**

  Yeoman のインストール後に、スケルトン・スマート・コントラクト・パッケージを作成するための Fabric ジェネレーターをインストールします。 Fabric ジェネレーターをインストールするには、`npm install -g generator-fabric` コマンドを使用します。

- **Docker のインストール**

  Hyperledger Fabric の事前構成インスタンスを実行するには、[Docker ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://www.docker.com/) がインストールされていることを確認します。

- **オペレーティング・システム要件**

  現在、この拡張機能は Mac、Windows、および Linux と互換性があります。

- **Hyperledger Fabric バージョン要件**

  この拡張機能は、Hyperledger Fabric バージョン 1.3.0 以降と互換性があります。

- **Hyperledger Fabric バージョン要件**

  この拡張機能は、Hyperledger Fabric バージョン 1.3.0 以降と互換性があります。

## 拡張機能のインストール
{: #develop-vscode-installing-the-extension}

1. [Visual Studio Code 拡張機能のマーケットプレイス・ページ ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://marketplace.visualstudio.com/items?itemName=IBMBlockchain.ibm-blockchain-platform)  にナビゲートするか、Visual Studio Code 内の「拡張機能」パネルで **{{site.data.keyword.blockchainfull_notm}} Platform** を検索します。
2. **「インストール」**をクリックします。
3. Visual Studio Code を再始動して、拡張機能のインストールを完了します。

## Visual Studio Code のコマンド・パレット
{: #develop-vscode-command-palette}

この拡張機能により、Visual Studio Code のコマンド・パレットに多くのコマンドが追加されます。 この資料で説明されている操作の多くは、Visual Studio Code のコマンド・パレットからコマンドを実行して開始することもできます。

## プロジェクトの作成
{: #develop-vscode-creating-a-project}

新規プロジェクトを作成するには、以下のステップを実行します。

1. コマンド・パレットを開き、**「IBM Blockchain Platform: Create Smart Contract Project」**コマンドを実行します。
2. スマート・コントラクトを作成する言語を選択します。現在、オプションは「JavaScript」と「TypeScript」です。
3. プロジェクトの名前のフォルダーを作成して開きます。
4. 新規プロジェクトを開く方法を選択します。 プロジェクト・フォルダーが開きます。

## ネットワークへの接続および接続の切断
{: #develop-vscode-connecting-and-disconnecting}

この拡張機能を使用する場合は、Hyperledger Fabric インスタンス内のピアおよびチャネルにスマート・コントラクト・パッケージをインストールしてインスタンス化します。 この拡張機能では、Docker を使用して Hyperledger Fabric の事前構成済みローカル・インスタンスを初期化できます。

### 事前構成済み Hyperledger Fabric インスタンスへの接続
{: #develop-vscode-connecting-to-preconfigured-Fabric-instance}

事前構成済み Hyperledger Fabric インスタンスに接続するには、まず、Docker がマシンで実行されていることを確認します。

1. Visual Studio Code で**「{{site.data.keyword.blockchainfull_notm}} Platform」**タブを開きます。
2. **「{{site.data.keyword.blockchainfull_notm}} Platform」**ペインで、**local_fabric** をクリックします。 Docker が実行中の場合は、ローカル Hyperledger Fabric インスタンスがダウンロードされ、開始されます。
3. ローカル Hyperledger Fabric インスタンスが開始されたら、**local_fabric** をダブルクリックして接続します。 これで、`mychannel` というチャネルが表示されます。

#### 事前構成済み Hyperledger Fabric ランタイムの再始動
{: #develop-vscode-restarting-Fabric-runtime}

`local_fabric` ランタイムを再始動するには、以下の手順を実行します。

1. `local_fabric` 接続が確立されたら右クリックします。
2. **「Restart Fabric Runtime」**を選択します。

Hyperledger Fabric コンテナーが停止し、再始動します。

#### Hyperledger Fabric ランタイムの終了
{: #develop-vscode-teardown-Fabric}

`local_fabric` ランタイムを終了するには、以下の手順を実行します。

1. `local_fabric` 接続が確立されたら右クリックします。
2. **「Teardown Fabric Runtime」**を選択します。

`local_fabric` ネットワークを終了すると、すべての Hyperledger Fabric コンテナーが閉じます。 **注**: これにより、台帳とワールド・ステート・データが失われます。

#### 事前構成済み Hyperledger Fabric ランタイムでの開発モードの有効化
{: #develop-vscode-enabling-development-mode-Fabric}

通常の運用では、ピアがチェーンコード・コンテナーを作成して管理し、インスタンス化されたスマート・コントラクトを実行します。 開発モードに切り替えると、ピアではチェーンコード・コンテナーを手動で実行できるようになります。 コマンド・ラインまたは端末でチェーンコード・コンテナーを手動で実行すると、スマート・コントラクトの反復的な開発およびデバッグに役立ちます。

事前構成済み Hyperledger Fabric ランタイムで開発モードの有効にするには、以下の手順を実行します。

1. `local_fabric` 接続が確立されたら右クリックします。
2. **「Toggle Development Mode」**を選択します。

拡張機能のデバッグ機能を使用するには、開発モードを有効にする必要があります。

### P{{site.data.keyword.blockchainfull_notm}} Platform スターター・プランへの接続
{: #develop-vscode-connecting-to-Starter-Plan}

{{site.data.keyword.blockchainfull_notm}} Platform Visual Studio Code 拡張機能を使用して、{{site.data.keyword.blockchainfull_notm}} Platform スターター・プランのインスタンスに接続できます。 接続後、この拡張機能を使用してスマート・コントラクトを開発およびデプロイできます。

**注:** 現在、この拡張機能は {{site.data.keyword.blockchainfull_notm}} Platform エンタープライズ・プランと互換性がありません。

1. {{site.data.keyword.blockchainfull_notm}} Platform スターター・プランのインスタンスがない場合は、[インスタンスを作成します ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://cloud.ibm.com/catalog/services/ibm-blockchain-5-prod)。
2. **「起動」**をクリックして {{site.data.keyword.blockchainfull_notm}} Platform UI を開きます。
3. **「概説」**>**「接続プロファイル」** > **「ダウンロード」**をクリックして、接続プロファイルを取得します。
4. **「認証局」** > **「証明書の生成」**をクリックし、証明書と秘密鍵をファイル・システムに保存して、必要な管理証明書を生成します。
5. **「メンバー」** > **「証明書」** > **「証明書の追加」**をクリックし、前の手順で作成した証明書を選択して、{{site.data.keyword.blockchainfull_notm}} Platform Starter Plan インスタンスに証明書を追加します。
6. Visual Studio Code で {{site.data.keyword.blockchainfull_notm}} Platform 拡張機能ビューを開き、**「Add new connection」**をクリックします。
7. 接続名と、接続プロファイルへのパスを入力し、ファイル・システム上の既存の [ウォレット ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/wallet.html "Wallet") を選択するか、証明書と秘密鍵を使用して新しいウォレットを作成します。

### 独自の Hyperledger Fabric インスタンスへの接続
{: #develop-vscode-connecting-to-own-Fabric-instance}

この拡張機能を使用すると、Hyperledger Fabric の事前構成済みローカル・インスタンスに接続したり、独自の Hyperledger Fabric インスタンスに接続したりできます。 独自の Fabric インスタンスに接続する場合は、Fabric 1.3.0 以降でなければなりません。

独自の Hyperledger Fabric インスタンスに接続するには、以下の手順を実行します。

1. Visual Studio Code で**「{{site.data.keyword.blockchainfull_notm}} Platform」**タブを開きます。
2. **「{{site.data.keyword.blockchainfull_notm}} Platform」**ペインで、**「Add new connection」**をクリックします。
3. 接続の名前を入力します。 この名前は、**「{{site.data.keyword.blockchainfull_notm}} Platform」**ペインに表示されます。
4. Hyperledger Fabric 接続プロファイルの完全修飾ファイル・パスを入力します。
5. 既存のファイル・システムの[ウォレット ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/wallet.html "Wallet") を選択するか、Fabric ID の証明書と秘密鍵のファイル・パスを入力して、新しいファイル・システム・ウォレットを作成します。
6. 接続リストの `local_fabric` の下に接続が表示されます。 接続する接続名をダブルクリックします。

バージョン 1.3.0 以降の既存の Hyperledger Fabric インスタンスがない場合は、事前構成済み Hyperledger Fabric インスタンスを使用して接続するか、以下のコマンドを実行してバージョン 1.3.0 イメージを取得し、タグ付けすることができます。

```
docker pull nexus3.hyperledger.org:10001/hyperledger/fabric-ca:amd64-1.3.0-stable
docker tag nexus3.hyperledger.org:10001/hyperledger/fabric-ca:amd64-1.3.0-stable hyperledger/fabric-ca
docker pull nexus3.hyperledger.org:10001/hyperledger/fabric-orderer:amd64-1.3.0-stable
docker tag nexus3.hyperledger.org:10001/hyperledger/fabric-orderer:amd64-1.3.0-stable hyperledger/fabric-orderer
docker pull nexus3.hyperledger.org:10001/hyperledger/fabric-peer:amd64-1.3.0-stable
docker tag nexus3.hyperledger.org:10001/hyperledger/fabric-peer:amd64-1.3.0-stable hyperledger/fabric-peer
docker pull nexus3.hyperledger.org:10001/hyperledger/fabric-tools:amd64-1.3.0-stable
docker tag nexus3.hyperledger.org:10001/hyperledger/fabric-tools:amd64-1.3.0-stable hyperledger/fabric-tools
docker pull nexus3.hyperledger.org:10001/hyperledger/fabric-ccenv:amd64-1.3.0-stable
docker tag nexus3.hyperledger.org:10001/hyperledger/fabric-ccenv:amd64-1.3.0-stable hyperledger/fabric-ccenv
```
{:codeblock}

### ネットワークからの切断
{: #develop-vscode-disconnecting}

ネットワークに接続したら、「Blockchain Connections」ペインの右上にある切断アイコンをクリックして、接続を閉じることができます。 コマンド・パレットから**「Disconnect from a blockchain」**コマンドを実行して切断することもできます。

## 接続の編集または削除
{: #develop-vscode-editing-or-deleting-connection}

接続は、接続ペインで編集または削除できます。

### 接続の編集
{: #develop-vscode-editing-connection}

接続を編集することにより、接続プロファイルのファイル・パス、接続に付けられた名前、および ID 証明書と秘密鍵のファイル・パスを変更できます。

接続を編集するには、以下の手順を実行します。

1. 拡張機能から、左下で編集する接続を右クリックすると、コンテキスト・メニューが開き、ID の追加、接続の編集、または接続の削除を行うためのオプションが表示されます。
2. **「Edit connection」**を選択します。
3. **「User Settings」**ページが開き、接続の詳細が強調表示されます。
4. 変更を行い、設定ページを保存します。

### 接続の削除
{: #develop-vscode-deleting-connection}

接続は、以下の方法で削除できます。

1. 拡張機能から、左下で編集する接続を右クリックすると、コンテキスト・メニューが開き、ID の追加、接続の編集、または接続の削除を行うためのオプションが表示されます。
2. **「Delete connection」**を選択します。
3. 接続の削除を確認するダイアログ・ボックスが表示されます。 **「はい」**をクリックします。

接続が削除されます。

## ID の追加
{: #develop-vscode-adding-identities}

接続を追加するときに、使用する ID が含まれた既存のウォレットを選択するか、ID の証明書と秘密鍵を使用して新規ウォレットを作成する必要があります。 ウォレットについて詳しくは、Fabric 資料の[ウォレットに関するトピック ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/wallet.html "Wallet") を参照してください。

既に確立されている接続に ID を追加するには、以下の手順を実行します。

1. 拡張機能から、左下で編集する接続を右クリックすると、コンテキスト・メニューが開き、ID の追加、接続の編集、または接続の削除を行うためのオプションが表示されます。
2. **「Add an identity」**を選択します。
3. 追加する ID が含まれた既存のウォレットを選択するか、証明書と秘密鍵のファイル・パスを指定して、新規ウォレットを作成します。

## 接続の探索
{: #develop-vscode-exploring-connections}

Hyperledger Fabric インスタンスに接続すると、使用可能なチャネルおよびそれらのチャネル内のピアのリストが**「{{site.data.keyword.blockchainfull_notm}} Platform」**ペインに表示されます。 Hyperledger Fabric インスタンスを少し探索して、構造を把握します。 最初にリストされているのは、接続内のチャネルです。チャネルの下に、そのチャネルのメンバーであるピアのリスト、およびそのチャネルでインスタンス化されたスマート・コントラクト・パッケージのリストがあります。 リスト内の各ピアの下には、そのピアにインストールされているすべてのスマート・コントラクト・パッケージのリストがあります。 インストールまたはインスタンス化されたスマート・コントラクト・パッケージのバージョンを表示することもできます。

### 事前構成済み Hyperledger Fabric ランタイムの接続詳細のエクスポート
{: #develop-vscode-exploring-connection-details}

`local_fabric` 接続に接続するために必要な接続詳細をエクスポートできます。 `local_fabric` 接続の接続詳細は、Hyperledger Fabric インスタンスへの接続や Hyperledger Fabric インスタンスとの対話を意図したクライアント・アプリケーションのテストに役立ちます。

`local_fabric` 接続の詳細をエクスポートするには、以下の手順を実行します。

1. 事前構成済み Hyperledger Fabric ランタイムを開始します。
2. `local_fabric` 接続を右クリックし、**「Export Connection Details」**を選択します。

接続の詳細は、現行プロジェクト・ディレクトリーに含まれる `local_fabric` というディレクトリーに保存されます。

## スマート・コントラクトのパッケージ化
{: #packaging-a-smart-contract}

スマート・コントラクトをデプロイする準備ができたら、まずそれをパッケージ化する必要があります。 スマート・コントラクトをパッケージ化するには、以下の手順を実行します。

1. Visual Studio Code で、**「{{site.data.keyword.blockchainfull_notm}} Platform」**パネルにナビゲートします。
2. **「Smart Contract Packages」**ペインで、「+」アイコンをクリックします。 ファイル・ビューアーでスマート・コントラクト・プロジェクトを開いている場合は、自動的にパッケージされ、**「Smart Contract Packages」**ペインに表示されます。 複数のスマート・コントラクト・フォルダーを開いている場合は、パッケージするスマート・コントラクト・フォルダーを尋ねられます。 スマート・コントラクト・フォルダーが開いていない場合は、エラー・メッセージが表示されます。

## スマート・コントラクト・パッケージのインストール
{: #develop-vscode-installing-smart-contract-packages}

Hyperledger Fabric のインスタンスに接続した後、ピアにスマート・コントラクト・パッケージをインストールしてインスタンス化できます。

1. 「{{site.data.keyword.blockchainfull_notm}} Platform」ペインで、スマート・コントラクト・パッケージをインストールするピアにナビゲートします。
2. ピアを右クリックしてスマート・コントラクト・パッケージをインストールし、**「Install Smart Contract」**を選択します。

## スマート・コントラクト・パッケージのインスタンス化
{: #develop-vscode-instantiating-smart-contract-packages}

チャネルでの実行を開始するには、まずインストール済みのスマート・コントラクト・パッケージをインスタンス化する必要があります。

1. スマート・コントラクト・パッケージがインストールされている Hyperledger Fabric インスタンスに接続します。
2. スマート・コントラクト・パッケージをインスタンス化するチャネルを右クリックし、**「Instantiate Smart Contract」**をクリックします。
3. インスタンス化するスマート・コントラクト・パッケージとバージョンを選択します。 スマート・コントラクト・パッケージは、このチャネルのメンバーであるピアにインストールする必要があります。
4. スマート・コントラクトのインスタンス化関数の名前を入力します。
5. インスタンス化関数に必要な引数を入力します。

## スマート・コントラクト・パッケージのエクスポートまたは削除
{: #develop-vscode-exporting-deleting-smart-contract-package}

スマート・コントラクトがパッケージ化された後、`.cds` ファイルとしてエクスポートするか、不要になった場合は削除できます。

スマート・コントラクト・パッケージを削除するには、以下の手順を実行します。

1. 「{{site.data.keyword.blockchainfull_notm}} Platform」拡張機能パネルで、削除するスマート・コントラクト・パッケージを右クリックします。
2. **「Delete Package」**を選択します。

これで、パッケージが削除され、スマート・コントラクト・パッケージのリストから消えます。

スマート・コントラクト・パッケージをエクスポートするには、以下の手順を実行します。

1. 「{{site.data.keyword.blockchainfull_notm}} Platform」拡張機能パネルで、エクスポートするスマート・コントラクト・パッケージを右クリックします。
2. **「Export Package」**を選択します。
3. スマート・コントラクト・パッケージ・ファイルを保存するディレクトリーを選択し、**「Export」**をクリックします。

## インスタンス化されたスマート・コントラクトのテスト
{: #develop-vscode-testing-instantiated-smart-contract}

スマート・コントラクトのテストは、スマート・コントラクトがインスタンス化された後に生成できます。 テストは、JavaScript または TypeScript のいずれかで生成され、実行またはデバッグできます。

スマート・コントラクト・テストを生成するには、以下の手順を実行します。

1. スマート・コントラクトがインスタンス化されていることを確認します。
2. **「Instantiated Smart Contracts」**で、テストを生成するスマート・コントラクトを右クリックします。
3. **「Generate Smart Contract Tests」**を選択します。
4. 次に、テスト・ファイルの言語 (**JavaScript** または **TypeScript**) を選択します。 これで、{{site.data.keyword.blockchainfull_notm}} Platform 拡張機能によって必要な npm モジュールがインストールされ、テスト・ファイルが作成されます。

テスト・ファイルが作成されたら、ファイルの**「Run Tests」**ボタンをクリックしてテストを実行できます。

## 事前構成済み Hyperledger Fabric ランタイムを使用したスマート・コントラクトのデバッグ
{: #develop-vscode-debugging}

スマート・コントラクトをローカルでデバッグできるので、スマート・コントラクトの開発者は、スマート・コントラクトの機能の修正を繰り返し、バグを修正してから、インスタンス化して機能を呼び出すことができます。 スマート・コントラクトをデバッグすると、ブレークポイントおよび出力を使用してスマート・コントラクト・トランザクションを実行できるため、トランザクションが意図したとおりに動作することを確認できます。 スマート・コントラクトをデバッグするには、以下の手順を実行します。

1. 開発モードの `local_fabric` 接続に接続していることを確認します。
2. スマート・コントラクト・プロジェクトを開きます。
3. 左側のナビゲーション・バーを使用して、Visual Studio Code のデバッグ・ビューを開きます。
4. 左上のドロップダウンを使用して、「Debug Smart Contract」構成を選択します。
5. **「play」**ボタンをクリックして、スマート・コントラクトをパッケージ化してインストールします。
6. スマート・コントラクト・ファイル内の該当する行番号をクリックして、スマート・コントラクトにブレークポイントを追加します。
7. インストール済みのスマート・コントラクトを右クリックし、**「Instantiate」**を選択します。 これで、右クリックしてトランザクションを送信できるようになり、定義されたブレークポイントで実行が一時停止します。

デバッグ中にスマート・コントラクトを変更するには、スマート・コントラクトに変更を加えた後に**「restart」**ボタンをクリックします。 デバッグを再開するということは、コントラクトを再度インスタンス化する必要がないことを意味します。

**注**: デバッグを再開すると、スマート・コントラクトがローカル・メモリーに保管されます。大規模なスマート・コントラクトに多数の変更を加えると、スマート・コントラクトの再インスタンス化が必要になる場合があります。

## インスタンス化されたスマート・コントラクトのアップグレード
{: #develop-vscode-upgrading-instantiated-smart-contract}

スマート・コントラクトがピアにインストールされ、チャネル上でインスタンス化された後、アップグレードしてスマート・コントラクトの新しいバージョンをデプロイできます。

1. アップグレードするスマート・コントラクトがインスタンス化されていることを確認します。
2. 新しいバージョンのスマート・コントラクトを同じネットワーク上のピアにインストールします。
3. インスタンス化されたスマート・コントラクトを右クリックし、**「Upgrade Smart Contract」**を選択します。
4. オプションで、新しいスマート・コントラクトがインスタンス化された後に実行するトランザクションを選択します。

## トランザクションの送信
{: #develop-vscode-submitting-transactions}

スマート・コントラクトがインストールされてインスタンス化された後、「{{site.data.keyword.blockchainfull_notm}} Platform」拡張機能パネルの接続ペインからトランザクションを送信できます。

トランザクションを送信するには、以下の手順を実行します。

1. スマート・コントラクトがインストールされ、インスタンス化されていること、およびネットワークに接続されていることを確認します。
2. 接続ペインで、**「Instantiated Smart Contracts」**を展開します。
3. 送信するトランザクションを含むスマート・コントラクトを展開します。
4. 送信するトランザクションを右クリックし、**「Submit Transaction」**を選択します。
5. トランザクションに必要な引数を入力し、**Enter** キーを押します。
