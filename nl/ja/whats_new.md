---

copyright:
  years: 2017, 2019
lastupdated: "2019-04-23"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:note: .note}
{:important: .important}
{:tip: .tip}

# 新機能
{: #whats-new}

## 2019 年 4 月 23 日
{: #whats-new-4-17-2019}

{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private リリース v1.0.2 は、Hyperledger Fabric v1.4.0 コード・ベースを使用していて、{{site.data.keyword.cloud_notm}} Private v3.1.2 でのデプロイメントに対応しています。

既存の {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private を v1.0.2 にアップグレードする場合は、[{{site.data.keyword.cloud_notm}} Private での Helm チャートのアップグレード](/docs/services/blockchain/howto/helm_install_icp.html#helm-install-upgrading)を参照してください。

Hyperledger Fabric v1.4.0 の詳細については、[Hyperledger Fabric の資料 ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/ "Hyperledger Fabric v1.4 の資料") を参照してください。{{site.data.keyword.cloud_notm}} Private について詳しくは、[{{site.data.keyword.cloud_notm}} Private v3.1.2 の資料 ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン") を参照してください。](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.2/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private v3.1.2 の資料")

## 2019 年 2 月 8 日
{: #whats-new-2-08-2019}

{{site.data.keyword.blockchainfull}} Platform は無料の2.0 ベータ版をリリースしました。これは、ブロックチェーン・ネットワークのデプロイ、操作、モニターを可能にする次世代の {{site.data.keyword.blockchainfull_notm}} Platform ソリューションです。 {{site.data.keyword.blockchainfull_notm}} Platform 2.0 無料ベータ版には、{{site.data.keyword.cloud_notm}} 上に存在するユーザーの {{site.data.keyword.IBM_notm}} Kubernetes サービス・クラスターにブロックチェーン・コンポーネントをデプロイして管理できる新しいユーザー・インターフェース・コンソールが含まれています。 {{site.data.keyword.blockchainfull_notm}} Platform 2.0 無料ベータ版では、次のことが可能です。

**1 つのシームレスな操作で、自分のネットワークを素早く簡単に構築できる**

*	スマート・コントラクトの開発 (VS Code) とネットワークの管理をスムーズに一体化できる
*	シンプルになった DevOps により、単一のコンソールで開発、テスト、本番環境へと移行できる
*	Javascript、Java、および Go 言語によるスマート・コントラクトの作成がサポートされる

**ネットワークの運用と管理を完全にコントロールできる**

*	必要なブロックチェーン・コンポーネント (ピア、順序付けサービス、認証局) だけをデプロイできる
*	再設計されたコンソールにより、どこにデプロイされたネットワーク・コンポーネントであろうと一カ所から管理できる
*	ID、台帳、スマート・コントラクトの完全なコントロールを維持できる

**新たに導入された柔軟なマルチクラウド対応機能により、分散ネットワークを簡単に拡張できる**

*	あらゆる環境 (オンプレミス、パブリック、ハイブリッド・クラウド) で実行されているノードに接続できる
*	単一のピアを複数の業界ネットワークに簡単に接続できる
*	Kubernetes を使用することで、まずは小さい規模からスタートし、前払いなしで拡張した分だけ支払い、アップグレードすることができる

- {{site.data.keyword.blockchainfull_notm}} Platform 2.0 無料ベータ版については、[{{site.data.keyword.blockchainfull_notm}} Platform 2.0 について](/docs/services/blockchain/howto/ibp-console.html#ibp-console-overview)で詳しく説明しています。
- 無料の 2.0 ベータ版リリースを {{site.data.keyword.IBM_notm}} Kubernetes サービス・クラスターにデプロイする方法については、[{{site.data.keyword.blockchainfull_notm}} Platform 2.0 の概説](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks)で説明しています。
- {{site.data.keyword.blockchainfull_notm}} Platform 2.0 無料ベータ版の使用方法を学習するための新しいチュートリアルを、**「方法」**カテゴリーの「**{{site.data.keyword.blockchainfull_notm}} Platform 2.0**」サブセクションに用意しています。
  * [ネットワーク構築チュートリアル](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network)では、順序付けプログラムとピアを作成することで、ネットワークをホストするプロセスを学習します。
  * [ネットワーク参加チュートリアル](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network)では、ピアを作成してチャネルに参加させることで、既存のネットワークに参加する方法を学習します。
  * [ネットワークにスマート・コントラクトをデプロイする](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts)では、スマート・コントラクトを作成してネットワークにデプロイする方法を学習します。
- {{site.data.keyword.blockchainfull_notm}} Platform 2.0 無料ベータ版オファリングは、Hyperledger Fabric v1.4 をベースとしており、ピアツーピアのゴシップ、サービス・ディスカバリー、およびプライベート・データをサポートします。 ネットワーク上にプライベート・データを構成する方法については、この[トピック](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data)を参照してください。

- {{site.data.keyword.blockchainfull_notm}} Visual Studio Code 機能拡張は、Visual Studio Code Marketplace から入手可能です。 開発者は、この拡張機能を使用して、スマート・コントラクトを作成、テストし、Hyperledger Fabric のインスタンスにデプロイできます。 この拡張機能は Hyperledger Fabric 1.3 以降と互換性があります。 VS Code 拡張機能の使用方法について詳しくは、[スマート・コントラクトのツール](/docs/services/blockchain/vscode-extension.html#develop-vscode)を参照してください。  

目次の改善のために、概説のトピックがすべて**「概説チュートリアル」**というセクションにまとめられました。 また、**「{{site.data.keyword.blockchainfull_notm}} Platform について」**のサブセクションとして記載されている各 {{site.data.keyword.blockchainfull_notm}} Platform オファリングについての説明が、**「詳細」**セクションに入れられました。

**注:** スターター・プランのユーザーは、無料のクラウド・クレジットを利用できなくなりました。

## 2018 年 12 月 7 日
{: #whats-new-12-7-2018}

*スターター・プラン・ネットワークの運用* トピックと*エンタープライズ・プラン・ネットワークの運用* トピックがまとめられ、[ネットワーク・モニターの使用](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard)に関する 1 つのチュートリアルに置き換えられました。

## 2018 年 11 月 27 日
{: #whats-new-11-27-2018}

{{site.data.keyword.blockchainfull_notm}} Platform は、{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private をリリースします。 {{site.data.keyword.cloud_notm}} Private は、Kubernetes ベースのコンテナー化アプリケーションを開発および管理するためのアプリケーション・プラットフォームです。ユーザーは、認証局 (CA)、順序付けプログラム、およびピアを x86、LinuxONE、および IBM Z にデプロイできます。{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private は Hyperledger Fabric v1.2.1 に基づいており、Kubernetes Helm チャートを使用してデプロイされます。 Helm チャートをインストールした後、これを {{site.data.keyword.cloud_notm}} Private カタログでバンドル・サービスとして見つけることができます。 [このオファリング](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about)は、経験豊富な Fabric ユーザーに適しています。

{{site.data.keyword.cloud_notm}} Private について詳しくは、[{{site.data.keyword.cloud_notm}} Private v3.1.0 の資料 ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン") を参照してください。](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private v3.1.0 の資料")

{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private のリリースをもって、IBM Blockchain Platform リモート・ピア (ベータ版) プログラムが終了します。 引き続き {{site.data.keyword.cloud_notm}} Private または AWS にピアをデプロイし、それをスターター・プランまたはエンタープライズ・プラン・ネットワークに接続することは可能です。 詳しくは、[スターター・プランまたはエンタープライズ・プランに対するピアのデプロイ](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy)および [AWS でのピアのデプロイ](/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws)を参照してください。 これらのピアは、リモート・ピアではなく**分散**ピアであると見なされます。これは、デプロイメントがお客様によって管理され、その結果、リモートになる中央の場所がないためです。

このリリースでは、資料の目次も改善されています。 スターターまたはエンタープライズのユーザーの場合、内容は引き続き**「詳細」**、**「方法」**、**「リファレンス」**、および**「ヘルプ」**セクションにあり、リンクも同じです。 目次のサブセクションが少し異なる場合があります。

## 2018 年 11 月 13 日
{: #whats-new-11-13-2018}

{{site.data.keyword.blockchainfull_notm}} Platform は、{{site.data.keyword.blockchainfull_notm}} Platform for Amazon Web Services (AWS) をリリースします。

{{site.data.keyword.blockchainfull_notm}} Platform for AWS を使用すると、AWS Cloud でピアを実行し、ピアを {{site.data.keyword.cloud_notm}} 内の既存のブロックチェーン・ネットワークに接続できます。 AWS のピアは、既存のネットワーク内の接続プロファイルおよびその他のブロックチェーン・コンポーネントを活用できます。一方、ピアを {{site.data.keyword.cloud_notm}} の外部の他のアプリケーションとより柔軟に連携させることができます。 詳しくは、[{{site.data.keyword.blockchainfull_notm}} Platform for Amazon Web Services について](/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws)を参照しください。
/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws

## 2018 年 10 月 4 日
{: #whats-new-10-4-2018}

{{site.data.keyword.blockchainfull_notm}} Platform は、スターター・プランを Hyperledger Fabric v1.1.0 から v1.2.1 にアップグレードします。 詳しくは、[スターター・プランについて](/docs/services/blockchain/starter_plan.html#starter-plan-about)を参照してください。

**重要:** Fabric v1.2.1 は、現在は v1.1.0 ピアを使用するリモート・ピア・ベータ版と互換性がありません。 2018 年 10 月 4 日より前に作成され、Fabric v1.1.0 に基づくスターター・プラン・ネットワークは影響を受けず、引き続きリモート・ピア・ベータ版で使用できます。 {{site.data.keyword.blockchainfull_notm}} Platform は、v1.2.1 ピアを使用するようにリモート・ピア・ベータ版を更新します。これは、Fabric v1.2.1 レベルの新しいスターター・ネットワーク、および Fabric v1.1.0 レベルのエンタープライズ・ネットワークと互換性があります。 リモート・ピア・ベータ版が Fabric v1.2.1 に更新されるまでは、v1.1.0 リモート・ピアを新しい v1.2.1 スターター・ネットワークとともにデプロイしないでください。

## 2018 年 9 月 4 日
{: #whats-new-9-4-2018}

{{site.data.keyword.blockchainfull_notm}} Platform はリモート・ピア・オファリング・ベータ版をリリースします。 リモート・ピア・オファリングは Hyperledger Fabric v1.1.0 に基づいています。 リモート・ピアを使用すると、{{site.data.keyword.blockchainfull_notm}} Platform ピア・ノードを独自の {{site.data.keyword.cloud_notm}} Private または Amazon Web Services (AWS) クラウド環境で実行できます。 詳しくは、[リモート・ピアについて](/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws)を参照してください。

## 2018 年 6 月 15 日
{: #whats-new-6-15-2018}

{{site.data.keyword.blockchainfull_notm}} Platform は、スターター・プラン GA をリリースします。 詳しくは、[スターター・プランについて](/docs/services/blockchain/starter_plan.html#starter-plan-about)を参照してください。

## 2018 年 5 月 15 日
{: #whats-new-5-15-2018}

{{site.data.keyword.blockchainfull_notm}} Platform は、エンタープライズ・プランを Hyperledger Fabric v1.0.0 から v1.1.0 にアップグレードします。 詳しくは、[エンタープライズ・プランについて](/docs/services/blockchain/enterprise_plan.html#enterprise-plan-about)を参照してください。

## 2018 年 3 月 18 日
{: #whats-new-3-18-2018}

{{site.data.keyword.blockchainfull_notm}} Platform は、スターター・プラン・ベータ版をリリースします。 スターター・プランは、{{site.data.keyword.blockchainfull_notm}} Platform ネットワークで学習および実践するための開発およびテスト環境を提供します。 詳しくは、[スターター・プランについて](/docs/services/blockchain/starter_plan.html#starter-plan-about)を参照してください。

## 2017 年 8 月 11 日
{: #whats-new-8-11-2017}

{{site.data.keyword.blockchainfull_notm}} Platform は、クラウド上の
{{site.data.keyword.blockchainfull_notm}} の代わりとなるエンタープライズ・プランをリリースします。 詳しくは、[エンタープライズ・プランについて](/docs/services/blockchain/enterprise_plan.html#enterprise-plan-about)を参照してください。
