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

# トラブルシューティング
{: #ibp-v2-troubleshooting}

コンソールを使用してノード、チャネル、またはスマート・コントラクトを管理する際に、一般的な問題が発生する場合があります。多くの場合、いくつかの簡単なステップを実行することで、これらの問題から復旧することが可能です。

## ピアまたは順序付けプログラムの作成後にノード操作が失敗するのはなぜですか?
{: #ibp-console-build-network-troubleshoot-entry1}

既存のノードを管理する際に、エラーが発生することがあります。その場合、ノード・ログを参照すると役立つことが多くあります。  

例えば、ノードを操作しようとしたときに、そのアクションが失敗する場合があります。
{: tsSymptoms}

新しいピアまたは順序付けプログラムを作成した後、クラスター・ストレージの構成によっては、ノードの操作ができるようになるまでに 2、3 分かかることがあります。
{: tsCauses}

Kubernetes ダッシュボードを調べて、ピアまたはノードの状況が `Running` であることを確認してください。その後、アクションを再試行してください。ノードの起動後も問題が発生する場合は、エラーについて[ノード・ログを確認](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-node-logs)してください。  
{: tsResolve}

## ピアを開始できないのはなぜですか?
{: #ibp-console-build-network-troubleshoot-entry2}

このエラーは、さまざまな状況の下で発生する可能性があります。

ピアのログに `2019-02-06 19:43:24.159 UTC [main] InitCmd -> ERRO 001 Cannot run peer because cannot init crypto, folder “/certs/msp” does not exist` と記録される
{: tsSymptoms}

- このエラーは、次の状況下で発生する可能性があります。
  - ピアまたは順序付けプログラムの組織 MSP 定義を作成したときに、タイプ `client` ではなくタイプ `peer` の ID に対応している登録 ID と機密事項を指定した。タイプは `client` でなければなりません。
  - ピアまたは順序付けプログラムの組織 MSP 定義を作成したときに指定した登録 ID と機密事項が、対応している組織の管理者 ID の登録 ID と機密事項に一致していない。
  - ピアまたは順序付けプログラムを作成したときに、ID のタイプが「peer」でない登録 ID と機密事項を指定した。

- ピアまたは順序付けプログラムの CA ノードを開き、**「登録済みユーザー (Registered Users)」**表にリストされている登録済みの ID を表示します。
- ピアまたは順序付けプログラムを削除し、正しい登録 ID と機密事項を指定するよう注意して再作成します。
- ピアまたは順序付けプログラムを作成する前に、タイプ「client」の組織の管理者 ID を作成する必要があるので注意してください。組織の MSP 定義の作成時の登録 ID と同じ ID を指定していることを確認してください。[ピア ID を登録する](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-use-CA-org1)ことに関する指示と、[順序付けプログラム ID を登録する](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-use-CA-orderer)ことに関する指示を参照してください。
{: tsResolve}

## スマート・コントラクトのインストール、インスタンス化、またはアップグレードが失敗するのはなぜですか?
{: #ibp-console-smart-contracts-troubleshoot-entry1}

スマート・コントラクトのインストール、インスタンス化、またはアップグレードの際に、エラーが発生することがあります。例えば、ピア上でスマート・コントラクトをインストールしようとすると、エラー `An error occurred when installing smart contract on peer` で失敗します。
{: tsSymptoms}

ピア上にこのバージョンのスマート・コントラクトが既に存在しているか、ピアのリソースが不足している場合に、このエラーを受け取ることがあります。
{: tsCauses}

- Kubernetes ダッシュボードを開いて、ピアの状況が `Running` であることを確認してください。  
- ピア・ノードを開いて、対象のスマート・コントラクトのバージョンがピア上にないことを確認し、適切なバージョンでやり直してください。
- ノードの起動後も問題が発生する場合は、エラーについて[ノード・ログを確認](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-node-logs)してください。  
{: tsResolve}

## スマート・コントラクトのコンテナー・ログを表示するにはどうしたらよいですか?
{: #ibp-console-smart-contracts-troubleshoot-entry2}

スマート・コントラクト、またはチェーンコードのコンテナー・ログを表示して、スマート・コントラクトの問題をデバッグする必要が生じることがあります。
{: tsSymptoms}

指示に従って[コンテナー・ログを表示](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-container-logs)します。
{: tsResolve}

## コンソールにチャネル、スマート・コントラクト、ID が表示されなくなりました。どうすれば元に戻せますか?
{: #ibp-v2-troubleshooting-browser-storage}

コンソール・ウォレットの ID は、ブロックチェーン・コンポーネントの管理に使用できる公開鍵と秘密鍵のペアで構成されますが、これらの ID はブラウザーのローカル・ストレージのみに保管されます。これらの ID の保護と管理は、お客様の責任となります。これらの ID の作成後に、ファイル・システムにエクスポートすることをお勧めします。新しいノードを作成する際には、必ずコンソール・ウォレットの ID をそのノードと関連付けます。この管理者 ID を使用して、ノードを管理します。ブラウザーを切り替えるか、別のマシン上のブラウザーに変更すると、これらの ID はウォレット内からなくなります。そのため、コンポーネントを管理できなくなります。
{: tsSymptoms}

{{site.data.keyword.blockchainfull_notm}} Platform 2.0 の新機能の 1 つとして、お客様自身が証明書の保護と管理を行うようになったということがあります。したがって、コンポーネントを管理できるように、証明書がブラウザーのローカル・ストレージ内のみに保持されます。
{: tsCauses}

- 新しく組織の MSP 定義を作成する際には、必ず組織の管理に使用できる ID の鍵を生成します。したがって、このプロセス中に、**「生成」**をクリックしてから**「エクスポート」**ボタンをクリックして、生成した ID をコンソール・ウォレット内に保管してから、JSON ファイルとしてファイル・システムに保存しなければなりません。
- ブラウザー内でこの問題を解決するには、次のようにこれらの ID をインポートし、対応するノードと関連付ける必要があります。
  - 問題が発生したブラウザーで、**「ウォレット (Wallet)」**タブをクリックした後に**「アイデンティティーの追加 (Add identity)」**をクリックして、JSON ファイルをウォレット内にインポートします。
  - **「JSON のアップロード (Upload JSON)」**をクリックし、**「ファイルの追加」**ボタンを使用してエクスポートした JSON ファイルを参照します。
  - **「送信」**をクリックします。
  - 次に、元々この ID が関連付けられていたピアまたは順序付けプログラムのノードを開き、**「設定」**アイコンをクリックします。
  - **「ID の関連付け (Associate identity)」**ボタンをクリックします。
  - ドロップダウン・リストから、コンソール・ウォレットにインポートしたばかりの ID を選択します。
  - **「関連付け」**をクリックします。
- 元のブラウザーのウォレット内あった ID ごとに、このプロセスを繰り返します。
{: tsResolve}

## 組織をチャネルに追加しようとするとエラー `An error occurred when updating channel` が発生するのはなぜですか?
{: #ibp-v2-troubleshooting-update-channel}

別の組織をチャネルに追加しようとすると、メッセージ `An error occurred when updating channel` で更新が失敗します。
{: tsSymptoms}

**「チャネルの更新 (Update Channel)」**パネルで選択した**チャネル・アップデーター MSP ID** がチャネルの管理者でないと、このエラーが発生します。
{: tsCauses}

**「チャネルの更新 (Update channel)」**パネルで、**「チャネル・アップデーター MSP ID (Channel Updater MSP ID)」**にスクロールダウンし、チャネルの作成時に指定した MSP ID を選択するか、チャネルの管理者である MSP ID を指定します。
{: tsResolve}
