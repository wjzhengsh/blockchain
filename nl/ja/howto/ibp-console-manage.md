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


# コンソールの管理
{: #ibp-console-manage-console}

コンソールの動作を管理できるさまざまなアクションがあります。 このトピックでは、日常のコンソール使用時に役立つ、ブロックチェーン・ノード操作以外のアクションについて説明します。
{:shortdesc}

**対象者:** このトピックは、ブロックチェーン・ネットワークの作成、モニター、管理を担当するネットワーク・オペレーターを対象に設計されています。

## コンソールでのユーザーの追加と削除

この {{site.data.keyword.blockchainfull}} Platform コンソールは、コンソールの管理者として {{site.data.keyword.IBM_notm}} 所有者の E メール・アドレスを使用してプロビジョンされます。 管理者は他のユーザーにコンソールへのアクセス権限を付与できます。

左側のナビゲーションの**「ユーザー」**をクリックして、E メール・アドレスを介したコンソールへのアクセス権限を新規ユーザーに付与します。 **「新規ユーザーの追加」**をクリックして、権限を与える E メール・アドレスのリストをユーザー・タイプ `Admin` または `General` とともに指定します。 `Admin` タイプのユーザーは、新規ユーザーを追加したり、既存のユーザーを削除したり、コンソール・ログを表示したりすることができます。 `General` ユーザーは、コンソールにログインできますが、ユーザーの追加や削除、ログの表示は実行できません。


このパネルにリストされるユーザーは、単に、コンソールにログイン可能なユーザーの E メール・アドレスです。 これらは、「組織」タブの**「使用可能な組織 (Available Organizations)」**やコンソール・ウォレットで保管されているアイデンティティーとは関係がありません。
{:note}

## 管理者の E メール・アドレスの更新

コンソールが複数の個人や組織によって使用される場合、ネットワークにアクセスするための機能 E メール・アドレスを作成することをお勧めします。 機能 E メールを使用すると、元の管理者が組織を離脱した場合や、その E メール・アドレスが使用停止された場合でも、コンソールにアクセスできます。 機能 E メールを作成できない場合は、複数のユーザーに `Admin` アクセス権限を指定することによって、単一の個人への依存を避けることができます。

コンソールのデプロイ時に構成されたコンソール管理者の E メール・アドレスを更新するには、コンソール管理者としてログインしている必要があります。 **「ユーザー」**タブにナビゲートし、**「IBM ID」**タイルで**「構成」**をクリックします。 開いたパネルで、コンソール管理者の新規 E メール・アドレスを指定します。

## ログの表示
{: #ibp-console-manage-logs}

{{site.data.keyword.blockchainfull_notm}} Platform コンソールの使用中は、問題のデバッグの目的でログを表示することが必要な場合があります。

### コンソール・ログの表示
{: #ibp-console-manage-console-logs}

コンソールの使用時やノードの操作時に発生した問題のデバッグが必要な場合、コンソール・ログに簡単にアクセスできます。 ロギング・レベルを設定することによって、コンソールで収集されるログの量を増やしたり減らしたりすることもできます。 コンソールのログは、[ノードのログ](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-node-logs) ({{site.data.keyword.IBM_notm}} Kubernetes サービスによって収集される) とは別個に収集されます。

コンソール・ブラウザーの**「設定」**タブにナビゲートして、ロギング設定を変更します。 コンソール・ログは、2 つの別個のソースから収集されます。

  * **クライアント・ロギング:** これらのログは、ブラウザーからコンソールにコマンドが送信されたときに収集されます。
  * **サーバー・ロギング:** これらのログは、コンソールからノードにコマンドが送信されたとき、およびコンソールのデプロイメント時に収集されます。 これらのログには、Hyperledger Fabric のロギング出力が含まれます。

各ログ・タイプの下のドロップダウン・リストを使用して、収集されるログの量を設定します。 例えば、**「エラー」**および**「警告」**では最少量のログが収集され、**「デバッグ」**および**「すべて」**では最大量が収集されます。

コンソール管理者としてログインしている場合にのみ、コンソール・ログを表示できます。 **「設定」**タブからログを表示するには、ブラウザー URL の語 `settings` を `api/v1/logs` に置き換えます。 例えば、コンソール URL が `localhost:3001/settings` の場合、`localhost:3001/api/v1/logs` にナビゲートすることによってログを表示できます。 クライアント・ログとサーバー・ログは別個のファイルに収集されます。 最新のログがページの先頭に表示されます。

### ノード・ログの表示
{: #ibp-console-manage-console-node-logs}

ピア、順序付けプログラム、および認証局のログは、{{site.data.keyword.IBM_notm}} Kubernetes サービスによって収集されます。 以下のステップに従って、{{site.data.keyword.blockchainfull_notm}} Platform 2.0 ネットワークをデプロイしたクラスターのノードのログを表示します。

1. [{{site.data.keyword.cloud_notm}} ダッシュボード ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://cloud.ibm.com/resources) を開き、{{site.data.keyword.IBM_notm}} Kubernetes サービス・クラスターの概要画面にナビゲートします。
2. クラスターの概要画面上の**「Kubernetes ダッシュボード」**をクリックします。
3. Kubernetes ダッシュボードで、左側のナビゲーションの「名前空間」ドロップダウン・リストを使用して、名前空間を {{site.data.keyword.blockchainfull_notm}} Platform のサービス・インスタンスに変更します。 サービス・インスタンス名は、文字と数値の長ストリングです。 サービス・インスタンス名は、{{site.data.keyword.blockchainfull_notm}} Platform コンソールの URL の最初の部分に表示されています。
4. 左側のナビゲーションで、**「ポッド」**をクリックしてデプロイ済みのノード・ポッドのリストを表示します。
5. ポッドをクリックします。 次に、上部メニューで**「ログの表示」**をクリックして、ノードのログを開きます。 ログ上部の**「ログの取得元 (Logs from)」**の後にあるドロップダウン・メニューを使用して、ポッド内の異なるコンテナーのログを表示できます。 例えば、ピアと状態データベース (CouchDB など) は、異なるコンテナーで実行され、異なるログが生成されます。

デフォルトでは、ノードのログはクラスター内でローカルに収集されます。 {{site.data.keyword.cloud_notm}} Log Analysis サービスやサード・パーティーのサービスを使用して、ネットワークのログを収集、保管、および分析することもできます。 詳しくは、[{{site.data.keyword.IBM_notm}} Kubernetes サービスのロギングとモニタリング ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://console.cloud.ibm.com/docs/containers?topic=containers-health#health "{{site.data.keyword.IBM_notm}} Kubernetes サービスのロギングとモニタリング") を参照してください。 ログをリアルタイムで簡単に構文解析できる、[{{site.data.keyword.cloud_notm}} LogDNA ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://cloud.ibm.com/catalog/services/logdna "{{site.data.keyword.IBM_notm}} Log Analysis with LogDNA") サービスの活用をお勧めします。

### スマート・コントラクトのコンテナー・ログの表示
{: #ibp-console-manage-console-container-logs}

スマート・コントラクトに関する問題が発生した場合は、以下のようにスマート・コントラクト、またはチェーンコードのコンテナー・ログを表示して問題をデバッグできます。

- Kubernetes ダッシュボードを開いて、スマート・コントラクトが稼働しているピアのポッドをクリックします。
- ダッシュボードから `exec` リンクをクリックして、ポッドの中にアクセスします。デフォルトでは、ピア・コンテナーを指しています。
- ドロップダウン・リストから `dind` コンテナーを選択して、このコンテナーに切り替えます。
- コマンド `docker ps -a` を実行して、チェーンコード・コンテナーのリストを表示します。
- `docker logs <chaincode-container-ID>` を実行します。その際、 <chaincode-container-ID> をチェーンコード・コンテナーの ID に置き換えます。


## リソース使用量のモニター

{{site.data.keyword.cloud_notm}} Platform ノードがデプロイされる場合、ノードにはデフォルトの CPU、メモリー、およびストレージ設定が事前構成されていて、それらの値は変更できません。 {{site.data.keyword.IBM_notm}} Kubernetes Service ダッシュボードを使用して、リソース使用量をモニターできます。 {{site.data.keyword.cloud_notm}} Platform コンソールで新規ノードを作成しようとして、Kubernetes クラスターを拡張しなければならないというエラーが発生した場合、Kubernetes クラスターにストレージを追加することができます。 ストレージ容量を増加したり既存ボリュームのパフォーマンスを向上したりする方法については、この[情報 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://cloud.ibm.com/docs/containers/cs_storage_file.html#change_storage_configuration "既存のストレージ・デバイスのサイズと IOPS の変更") を参照してください。
