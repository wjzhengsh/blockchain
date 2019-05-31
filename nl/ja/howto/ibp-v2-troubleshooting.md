---

copyright:
  years: 2019
lastupdated: "2019-05-16"

keywords: troubleshooting, debug, why, what does this mean, how can I, when I 

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

# トラブルシューティング
{: #ibp-v2-troubleshooting}

コンソールを使用してノード、チャネル、またはスマート・コントラクトを管理する際に、一般的な問題が発生する場合があります。 多くの場合、いくつかの簡単なステップを実行することで、これらの問題から復旧することが可能です。

- [ノードにマウスオーバーすると、状況が「`状況を表示できません (Status unavailable)`」になります。これはどういう意味ですか?](#ibp-v2-troubleshooting-status-unavailable)
- [ノードにマウスオーバーすると、状況が「`状況を検出できません (Status undetectable)`」になります。これはどういう意味ですか?](#ibp-v2-troubleshooting-status-undetectable)
- [ピアまたは順序付けプログラムの作成後にノード操作が失敗するのはなぜですか?](/docs/services/blockchain/howto/ibp-v2-troubleshooting.html#ibp-console-build-network-troubleshoot-entry1)
- [ピアを開始できないのはなぜですか?](/docs/services/blockchain/howto/ibp-v2-troubleshooting.html#ibp-console-build-network-troubleshoot-entry2)
- [スマート・コントラクトのインストール、インスタンス化、またはアップグレードが失敗するのはなぜですか?](/docs/services/blockchain/howto/ibp-v2-troubleshooting.html#ibp-console-smart-contracts-troubleshoot-entry1)
- [スマート・コントラクトのコンテナー・ログを表示するにはどうしたらよいですか?](/docs/services/blockchain/howto/ibp-v2-troubleshooting.html#ibp-console-smart-contracts-troubleshoot-entry2)
- [コンソールにチャネル、スマート・コントラクト、ID が表示されなくなりました。 どうすれば元に戻せますか?](/docs/services/blockchain/howto/ibp-v2-troubleshooting.html#ibp-v2-troubleshooting-browser-storage)
- [組織をチャネルに追加しようとするとエラー `An error occurred when updating channel` が発生するのはなぜですか?](/docs/services/blockchain/howto/ibp-v2-troubleshooting.html#ibp-v2-troubleshooting-update-channel)
- [Kubernetes クラスターの有効期限が切れました。 これはどういう意味ですか?](/docs/services/blockchain/howto/ibp-v2-troubleshooting.html#ibp-v2-troubleshooting-cluster-expired)
- [VS Code から実行するトランザクションが失敗するのはなぜですか?](/docs/services/blockchain/howto/ibp-v2-troubleshooting.html#ibp-v2-troubleshooting-anchor-peer)

## ノードにマウスオーバーすると、状況が「`状況が不明です (Status unavailable)`」になります。これはどういう意味ですか?
{: #ibp-v2-troubleshooting-status-unavailable}

CA、ピア、または順序付けプログラムのノードのタイルに表示されるノード状況がグレーになります。ノードの状況が得られないという意味です。 理想的には、ノードにマウスオーバーすると、ノードの状況が「`実行中`」と表示されます。
{: tsSymptoms}

この問題は、ノードが作成されたばかりで、デプロイメント・プロセスがまだ完了していない場合に起こることがあります。 ノードが CA の場合は、そのノードが稼働していない可能性があります。
ノードがピアまたは順序付けプログラムの場合は、ノードに対して実行されるヘルス・チェッカーがノードと通信できない場合にこの状態になります。  ノードが指定の時間内に応答しなかったり、ノードやネットワーク接続がダウンしていたりして、状況を取得するための要求がタイムアウト・エラーで失敗することがあります。
{: tsCauses}

新しいノードの場合は、デプロイメントが完了するまで数分待ってください。 新しいノードでない場合は、[関連するノードのログを調べて](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-node-logs)、エラーの原因を突き止めてください。
{: tsResolve}

## ノードにマウスオーバーすると、状況が「`状況を検出できません (Status undetectable)`」になります。これはどういう意味ですか?
{: #ibp-v2-troubleshooting-status-undetectable}

ピアまたは順序付けプログラムのノードのタイルに表示されるノード状況が黄色になります。黄色は、ノードの状況を検出できないという意味です。 理想的には、ノードにマウスオーバーすると、ノードの状況が「`実行中`」と表示されます。
{: tsSymptoms}

この状態になるのは、コンソールに*インポートされた*ピア・ノードや順序付けプログラム・ノードに対してヘルス・チェッカーを実行できない、という場合に限られます。 ノードのインポート時に `operations_url` を指定していないと、この状況になります。 ノード用のヘルス・チェッカーを実行するには、操作 url が必要です。 ノード自体はおそらく`実行中`ですが、操作 url が指定されていないので、その状況を確認できません。
{: tsCauses}

この問題は以下の手順で解決できます。
 1. ノードのタイルをクリックして開きます。
 2. **「設定」**アイコンをクリックします。
 3. **「ID の関連付け (Associate identity)」**をクリックし、このノードに関連付けられている ID を表示してメモします。 **「キャンセル」**をクリックして、このパネルを閉じます。
 4. **「エクスポート」**をクリックして、ノードの `JSON` ファイルを生成します。
 5. 生成した `JSON` ファイルを編集して、ノードの `operations_url` を入力します。 `operations_url` の値は、ノードの構成やネットワークのさまざまな設定によって異なります。 この値は、ノードをデプロイしたネットワーク管理者に確認してください。
 6. **「削除」**をクリックします。 この手順では、インポートしたノードがコンソールから削除されますが、実際のノードは削除されません。
 7. **「ノード」**タブで**「ピアの追加」**または**「順序付けプログラムの追加 (Add orderer)」**をクリックし、**「既存のピアのインポート (Import an existing peer)」**または**「既存の順序付けプログラムのインポート (Import an existing orderer)」**をクリックします。
 8. **「JSON のアップロード (Upload JSON)」**をクリックし、編集した JSON ファイルを参照します。 **「次へ」**をクリックします。
 9. 手順 3 でメモした ID を関連付けます。
 10. **「ピアの追加」**または**「順序付けプログラムの追加 (Add orderer)」**をクリックします。

ノードに対してヘルス・チェッカーを実行し、ノードの状況を報告できるようになりました。
{: tsResolve}

## ピアまたは順序付けプログラムの作成後にノード操作が失敗するのはなぜですか?
{: #ibp-console-build-network-troubleshoot-entry1}

既存のノードを管理する際に、エラーが発生することがあります。 その場合、ノード・ログを参照すると役立つことが多くあります。  

例えば、ノードを操作しようとしたときに、そのアクションが失敗する場合があります。
{: tsSymptoms}

新しいピアまたは順序付けプログラムを作成した後、クラスター・ストレージの構成によっては、ノードの操作ができるようになるまでに 2、3 分かかることがあります。
{: tsCauses}

Kubernetes ダッシュボードを調べて、ピアまたはノードの状況が `Running` であることを確認してください。 その後、アクションを再試行してください。 ノードの起動後も問題が発生する場合は、エラーについて[ノード・ログを確認](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-node-logs)してください。  
{: tsResolve}

## ピアを開始できないのはなぜですか?
{: #ibp-console-build-network-troubleshoot-entry2}

このエラーは、さまざまな状況の下で発生する可能性があります。

ピアのログに `2019-02-06 19:43:24.159 UTC [main] InitCmd -> ERRO 001 Cannot run peer because cannot init crypto, folder “/certs/msp” does not exist` と記録される
{: tsSymptoms}

- このエラーは、次の状況下で発生する可能性があります。
  - ピアまたは順序付けプログラムの組織 MSP 定義を作成したときに、タイプ `client` ではなくタイプ `peer` の ID に対応している登録 ID と機密事項を指定した。 タイプは `client` でなければなりません。
  - ピアまたは順序付けプログラムの組織 MSP 定義を作成したときに指定した登録 ID と機密事項が、対応している組織の管理者 ID の登録 ID と機密事項に一致していない。
  - ピアまたは順序付けプログラムを作成したときに、ID のタイプが「peer」でない登録 ID と機密事項を指定した。

- ピアまたは順序付けプログラムの CA ノードを開き、**「登録済みユーザー (Registered Users)」**表にリストされている登録済みの ID を表示します。
- ピアまたは順序付けプログラムを削除し、正しい登録 ID と機密事項を指定するよう注意して再作成します。
- ピアまたは順序付けプログラムを作成する前に、タイプ「client」の組織の管理者 ID を作成する必要があるので注意してください。 組織の MSP 定義の作成時の登録 ID と同じ ID を指定していることを確認してください。 [ピア ID を登録する](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-use-CA-org1)ことに関する指示と、[順序付けプログラム ID を登録する](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-use-CA-orderer)ことに関する指示を参照してください。
{: tsResolve}

## スマート・コントラクトのインストール、インスタンス化、またはアップグレードが失敗するのはなぜですか?
{: #ibp-console-smart-contracts-troubleshoot-entry1}

スマート・コントラクトのインストール、インスタンス化、またはアップグレードの際に、エラーが発生することがあります。  例えば、ピア上でスマート・コントラクトをインストールしようとすると、エラー `An error occurred when installing smart contract on peer` で失敗します。
{: tsSymptoms}

ピア上にこのバージョンのスマート・コントラクトが既に存在しているか、ピアのリソースが不足している場合に、このエラーを受け取ることがあります。
{: tsCauses}

- Kubernetes ダッシュボードを開いて、ピアの状況が `Running` であることを確認してください。  
- ピア・ノードを開いて、対象のスマート・コントラクトのバージョンがピア上にないことを確認し、適切なバージョンでやり直してください。
- ノードの起動後も問題が発生する場合は、エラーについて[ノード・ログを確認](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-node-logs)してください。  
{: tsResolve}

## スマート・コントラクトのコンテナー・ログを表示するにはどうしたらよいですか?
{: #ibp-console-smart-contracts-troubleshoot-entry2}

スマート・コントラクト (チェーンコード) のコンテナー・ログを表示して、スマート・コントラクトの問題をデバッグする必要が生じることがあります。
{: tsSymptoms}

指示に従って[コンテナー・ログを表示](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-container-logs)します。
{: tsResolve}

## コンソールにチャネル、スマート・コントラクト、ID が表示されなくなりました。 どうすれば元に戻せますか?
{: #ibp-v2-troubleshooting-browser-storage}

コンソール・ウォレットの ID は、ブロックチェーン・コンポーネントの管理に使用できる署名証明書と秘密鍵で構成されますが、これらの ID はブラウザーのローカル・ストレージのみに保管されます。これらの ID の保護と管理は、お客様の責任となります。 これらの ID の作成後に、ファイル・システムにエクスポートすることをお勧めします。 新しいノードを作成する際には、必ずコンソール・ウォレットの ID をそのノードと関連付けます。 この管理者 ID を使用して、ノードを管理します。 ブラウザーを切り替えるか、別のマシン上のブラウザーに変更すると、これらの ID はウォレット内からなくなります。 そのため、コンポーネントを管理できなくなります。
{: tsSymptoms}

{{site.data.keyword.blockchainfull_notm}} Platform 2.0 の新機能の 1 つとして、お客様自身が証明書の保護と管理を行うようになったということがあります。 したがって、コンポーネントを管理できるように、証明書がブラウザーのローカル・ストレージ内のみに保持されます。 プライベート・ブラウザー・ウィンドウを使用している時に、別のブラウザー・ウィンドウやプライベートではないブラウザー・ウィンドウに切り替えると、新しいブラウザー・セッションでは、作成した ID がコンソール・ウォレットから失われます。 したがって、プライベート・ブラウザー・セッションのコンソール・ウォレットからファイル・システムに ID をエクスポートしておく必要があります。 そうすれば、必要に応じて、プライベートではないブラウザー・セッションにインポートできます。 そうしない場合、リカバリーの方法はありません。
{: tsCauses}

- 新しく組織の MSP 定義を作成する際には、必ず組織の管理に使用できる ID の鍵を生成します。 したがって、このプロセス中に、**「生成」**ボタンをクリックしてから**「エクスポート」**ボタンをクリックして、生成した ID をコンソール・ウォレット内に保管してから、JSON ファイルとしてファイル・システムに保存しなければなりません。
- ブラウザー内でこの問題を解決するには、次のようにこれらの ID をインポートし、対応するノードと関連付ける必要があります。
  - 問題が発生したブラウザーで、**「ウォレット (Wallet)」**タブをクリックした後に**「アイデンティティーの追加 (Add identity)」**をクリックして、JSON ファイルをウォレット内にインポートします。
  - **「JSON のアップロード (Upload JSON)」**をクリックし、**「ファイルの追加」**ボタンを使用してエクスポートした JSON ファイルを参照します。
  - **「送信」**をクリックします。
  - 次に、元々この ID が関連付けられていたピアまたは順序付けプログラムのノードを開き、**「設定」**アイコンをクリックします。
  - **「ID の関連付け (Associate identity)」**ボタンをクリックします。
  - ドロップダウン・リストから、コンソール・ウォレットにインポートしたばかりの ID を選択します。
  - **「関連付け」**をクリックします。
- 元のブラウザーのウォレット内にあった ID ごとに、このプロセスを繰り返します。
{: tsResolve}

## 組織をチャネルに追加しようとするとエラー `An error occurred when updating channel` が発生するのはなぜですか?
{: #ibp-v2-troubleshooting-update-channel}

別の組織をチャネルに追加しようとすると、メッセージ `An error occurred when updating channel` で更新が失敗します。
{: tsSymptoms}

**「チャネルの更新 (Update Channel)」**パネルで選択した**チャネル・アップデーター MSP ID** がチャネルの管理者でないと、このエラーが発生します。
{: tsCauses}

**「チャネルの更新 (Update channel)」**パネルで、**「チャネル・アップデーター MSP ID (Channel Updater MSP ID)」**にスクロールダウンし、チャネルの作成時に指定した MSP ID を選択するか、チャネルの管理者である MSP ID を指定します。
{: tsResolve}

## Kubernetes クラスターの有効危険が切れました。 これはどういう意味ですか?
{: #ibp-v2-troubleshooting-cluster-expired}

{{site.data.keyword.IBM_notm}} Kubernetes Service クラスターの有効期限がもうすぐ切れるという E メールを受け取りました。または、クラスターの状況が「`有効期限切れ (Expired)`」と表示されます。 または、30 日後にコンソールにアクセスできなくなりました。
{: tsSymptoms}

無料の Kubernetes クラスターの有効期間は 30 日間のみです。
{: tsCauses}

無料のクラスターから有料のクラスターには移行できません。 30 日後にはコンソールにアクセスできなくなり、すべてのノードと証明書が削除されます。 行われる処理やお客様が取れる対処については、[Kubernetes クラスターの有効期限](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-cluster-expiration)のトピックを参照してください。
{: tsResolve}

## VS Code から実行するトランザクションが失敗するのはなぜですか?
{: #ibp-v2-troubleshooting-anchor-peer}

VS Code からトランザクションを実行すると、以下のようなエラーが出て失敗します。
```
Error submitting transaction: No endorsement plan available for {"chaincodes":[{"name":"hello-world"}]}
```
{: tsSymptoms}

このエラーが発生するのは、チャネルでアンカー・ピアを構成していない状態でファブリック・サービス・ディスカバリー・フィーチャーを使用した場合です。
{: tsCauses}

スマート・コントラクトのデプロイに関するチュートリアルの[プライベート・データのトピック](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data)にあるステップ 3 を実行して、アンカー・ピアを構成してください。
