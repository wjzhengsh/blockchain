---

copyright:
  years: 2019
lastupdated: "2019-05-16"

keywords: logging levels, metrics, health check, peer, orderer

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

# 操作サービスを使用したノードの操作
{: #operations_service}

{{site.data.keyword.blockchainfull}} Platform v2.0 は Hyperledger Fabric v1.4.1 に基づきます。プラットフォームでは、オペレーターがノードのヘルス・チェックを実行し、ピアと順序付けプログラムのノードから操作のメトリックをプルし、ロギング・レベルを管理するための「操作」の RESTful API を提供する、操作サービス機能がサポートされています。ピアと順序付けプログラムが、「操作」の RESTful API を提供する HTTP サーバーをホストします。操作サービスについて詳しくは、[The Operations Service ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/operations_service.html "The Operations Service"){:new_window} を参照してください。
{:shortdesc}


## 考慮事項と制限
{: #operations_service_consideration_limitation}

- ヘルス・チェッカーにアクセスできるように、すべてのピアと順序付けプログラムのノードは `clientAuthRequired: false` で構成されます。`clientAuthRequired` は `false` に設定され、相互 TLS も有効化されるため、REST サーバーにアクセスするときに、認証できるように TLS ID を渡す必要があります。この設定により、適切な鍵を持つユーザーのみが、対応するログを表示できます。
- 現在、[Prometheus ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/operations_service.html#prometheus) に基づくメトリック・プル・モデルのみがサポートされます。

## 始めに
{: #operations_service_before_you_begin}

ピアと順序付けプログラムから次の情報を収集する必要があります。

- **`peer-endpoint`** または **`orderer-endpoint`**

  コンソールからエクスポートしたピアまたは順序付けプログラムの JSON ファイルで、ピアまたは順序付けプログラムの操作のエンドポイント URL を確認できます。

    1. **「ノード」**タブで、ピアまたは順序付けプログラムをクリックします。
    2. ピアまたは順序付けプログラムのページで、ピアまたは順序付けプログラムの名前の横にある**「設定」**アイコンをクリックします。
    3. サイド・パネルで、**「エクスポート」**をクリックして、ピアまたは順序付けプログラムの JSON ファイルを保存します。
    4. JSON ファイルの `operations_url` パラメーターの値である、操作のエンドポイント URL を見つけます。この値は、このトピックで後述するコマンドで `peer-endpoint` または `orderer-endpoint` とも呼ばれます。例えば、次のようにします。

      ```
      {
      "short_name": "Peer1Org1_0",
      "name": "Peer1 Org1",
      "url": "https://169.46.208.93:32739",
      "type": "fabric-peer",
      "msp_id": "org1msp",
      "operations_url": "https://169.46.208.93:32101"
      }
      ```

      ```
      {
      "short_name": "Orderer_0",
      "name": "Orderer",
      "url": "https://169.46.208.93:31612",
      "type": "fabric-orderer",
      "msp_id": "orderermsp",
      "operations_url": "https://169.46.208.93:30115"
      }
      ```

- **`client-tls-cert`** および **`client-tls-key`**

  コンソールで TLS CA の証明書と秘密鍵を確認できます。

  1. **「ノード」**タブで、ピアまたは順序付けプログラムの CA ノードをクリックします。
  2. CA のページで、**「TLS 認証局 (TLS Certificate Authority)」**をクリックします。
  3. TLS CA の表で、`admin` ユーザーを見つけて、**「アクション」**の垂直に並んだドットをクリックします。次に、**「アイデンティティーのエンロール (Enroll Identity)」**をクリックします。
  4. サイド・パネルで、**「次へ」**をクリックして、TLS CA の証明書と秘密鍵を表示できます。
  5. アイデンティティーに表示名を付けて、**「アイデンティティーのエクスポート (Export identity)」**をクリックして、**「アイデンティティーのウォレットへの追加 (Add identity to wallet)」**をクリックし、TLS CA の証明書と秘密鍵を JSON ファイルに保存します。
  6. エクスポートした JSON ファイルを開きます。
  7. JSON ファイルの `private_key` パラメーターの値である、秘密鍵を見つけます。これは、後述のコマンドで使用される場合は `client-tls-key` です。
  8. JSON ファイルの `cert` パラメーターの値である、TLS CA 証明書を見つけます。これは、後述のコマンドで使用される場合は `client-tls-cert` です。

- **`peer tls-ca cert`** または **`orderer tls-ca cert`**

  コンソールでピアまたは順序付けプログラムの TLS CA 証明書を確認できます。

  1. **「ノード」**タブで、ピアまたは順序付けプログラムのノードをクリックします。
  2. ノード名の横にある**「設定」**アイコンをクリックします。
  3. ノードの TLS CA 証明書のストリングをコピーします。これは、後述のコマンドで使用できる `peer tls-ca cert` または `orderer tls-ca cert` です。

**注:** すべての証明書と鍵について、次のコマンドを使用して、`base64` 形式の証明書のストリングを、個別の `.pem` ファイルに変換する必要があります。
  ```
  export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
  echo <base64_string> | base64 --decode $FLAG > <key_name>.pem
  ```
  {:codeblock}
{:important}


## ノードの正常性のチェック
{: #operations_service_health_check}

`curl -k <peer-endpoint>/healthz` または `curl -k <orderer-endpoint>/healthz` コマンドを実行して、ピアまたは順序付けプログラムのノードの正常性をチェックします。例えば、次のようにします。

```
curl -k https://169.46.208.93:3210/healthz
```
{:codeblock}

ヘルス・チェックについて詳しくは、[Health checks ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/operations_service.html#health-checks "Health checks") を参照してください。


## メトリックの表示
{: #operations_service_view_metrics}

以下のコマンドを実行して、ピアのメトリックを表示します。順序付けプログラムのメトリックを表示するには、同じコマンドで、`<peer-endpoint>` を `<orderer-endpoint>` に置き換えて実行します。

```
curl -k <peer-endpoint>/metrics --cert <client-tls-cert> --key <client-tls-key> --cacert <peer tls ca-cert>
```
{:codeblock}

例えば、次のようにします。

```
curl -k https://169.55.231.152:30766/metrics --cert msp/org1/ca/tls/msp/signcerts/cert.pem --key msp/org1/ca/tls/msp/key.pem  --cacert msp/org1/ca/tls/msp/cacerts/tlsca.pem
```
{:codeblock}


メトリックについて詳しくは、[Metrics ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/operations_service.html#metrics "Metrics") を参照してください。


## ロギング・レベルの表示
{: #operations_service_log_level_view}

以下のコマンドを実行して、ロギング・レベルを表示します。コマンドが完了したら、端末にログ・レベルが表示されます。

```
curl -k <peer-endpoint>/logspec --cert <client-tls-cert> --key <client-tls-key> --cacert <peer tls ca-cert>
```
{:codeblock}

例えば、次のようにします。
```
curl -k https://169.46.208.93:3210/logspec --cert msp/org1/ca/tls/msp/signcerts/cert.pem --key msp/org1/ca/tls/msp/keystore/key.pem  --cacert msp/org1/ca/tls/msp/cacerts/tlsca.pem
```

<!--
```
curl https://169.46.208.93:3210/logspec --cert temp/1mycluster-test-32240/msp/org1/ca/tls/msp/signcerts/cert.pem --key temp/1mycluster-test-32240/msp/org1/ca/tls/msp/keystore/3fb20abb935f88b83a8da68317a44a4fa0953d7ec6d06bb19a6fc3979a603095_sk  --cacert temp/1mycluster-test-32240/msp/org1/ca/tls/msp/cacerts/169-55-231-152-30021-tlsca.pem
```
-->

以下の例のような結果を表示できます。

```
{"spec":"info"}
```

## ロギング・レベルの設定
{: #operations_service_log_level_set}

既存のロギング・レベルの設定を変更するには、`spec` という単一の属性で構成された JSON 本文を含む `PUT` メソッドを使用する、以下のコマンドを実行します。`<log-level>` を予期されるロギング・レベルに置き換えます。設定できるロギング・レベルについて詳しくは、Hyperledger Fabric の資料で[ロギングの仕様 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/logging-control.html#logging-specification "ロギングの仕様") を参照してください。

```
curl -X PUT  <peer-endpoint>/logspec -d '{"spec":"<log-level>"}' --cert <client-tls-cert> --key <client-tls-key> --cacert <peer tls ca-cert>
```
{:codeblock}

例えば、次のようにします。
```
curl -X PUT  https://169.46.208.93:3210/logspec -d '{"spec":"chaincode=debug:info"}' --cert msp/org1/ca/tls/msp/signcerts/cert.pem --key msp/org1/ca/tls/msp/keystore/key.pem  --cacert msp/org1/ca/tls/msp/cacerts/tlsca.pem
```

新しいロギング・レベルを設定したら、[ロギング・レベルの表示コマンド](#operations_service_log_level_view)を使用して、設定を確認できます。

<!--
```
curl -X PUT  https://169.46.208.93:3210/logspec -d '{"spec":"chaincode=debug:info"}' --cert temp/1mycluster-test-32240/msp/org1/ca/tls/msp/signcerts/cert.pem --key temp/1mycluster-test-32240/msp/org1/ca/tls/msp/keystore/3fb20abb935f88b83a8da68317a44a4fa0953d7ec6d06bb19a6fc3979a603095_sk  --cacert temp/1mycluster-test-32240/msp/org1/ca/tls/msp/cacerts/169-55-231-152-30021-tlsca.pem
```
-->

ログ・レベルの構成について詳しくは、Hyperledger Fabric の資料で [Log level management ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/operations_service.html#log-level-management "Log level management") を参照してください。
