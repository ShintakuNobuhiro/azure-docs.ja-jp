---
title: Azure Stack の VPN ゲートウェイについて | Microsoft Docs
description: Azure Stack で使用する VPN ゲートウェイと、その構成について説明します。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 0e30522f-20d6-4da7-87d3-28ca3567a890
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/01/2017
ms.author: brenduns
ms.openlocfilehash: 7e489db0d9a65b850df41360ce11616d518c5265
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2018
---
# <a name="about-vpn-gateway-for-azure-stack"></a>Azure Stack の VPN ゲートウェイについて
*適用先: Azure Stack 統合システムと Azure Stack 開発キット*


Azure 仮想ネットワークとオンプレミスのサイトとの間でネットワーク トラフィックを送信する前に、仮想ネットワーク用の仮想ネットワーク ゲートウェイを作成する必要があります。

VPN ゲートウェイは、パブリック接続で暗号化されたトラフィックを送信する仮想ネットワーク ゲートウェイの一種です。 VPN ゲートウェイを使用して、Azure Stack 内の仮想ネットワークと Azure 内の仮想ネットワーク間で安全にトラフィックを送信できます。 また、仮想ネットワークと、VPN デバイスに接続されている別のネットワーク間で安全にトラフィックを送信することもできます。

仮想ネットワーク ゲートウェイを作成する場合、作成するゲートウェイの種類を指定する必要があります。 Azure Stack では、仮想ネットワーク ゲートウェイの 1 つの種類である "Vpn" がサポートされています。

各仮想ネットワークに配置できる仮想ネットワーク ゲートウェイは 2 つですが、種類はいずれか 1 つのみになります。 選択する設定によっては、1 つの VPN ゲートウェイへの複数の接続を作成できます。 1 つの例は、マルチサイト接続構成です。

> [!NOTE]
> Azure では、選択する VPN ゲートウェイ SKU の帯域幅スループットが、それに接続されるすべての接続に分配される必要があります。  Azure Stack では、VPN ゲートウェイ SKU の帯域幅値が、それに接続される各接続リソースに適用されます。     

> たとえば、Azure では、Basic VPN ゲートウェイ SKU で、約 100 Mbps の総スループットに対応できます。  その VPN ゲートウェイへの接続を 2 つ作成し、1 つの接続で 50 Mbps の帯域幅を使用する場合、もう 1 つの接続では 50 Mbps を使用できます。   

> Azure Stack では、Basic VPN ゲートウェイ SKU への "*各*" 接続に 100 Mbps のスループットが割り当てられます。

## <a name="configuring-a-vpn-gateway"></a>VPN ゲートウェイの構成
VPN ゲートウェイ接続は、特定の設定で構成された複数のリソースに依存します。 ほとんどのリソースは個別に構成できますが、場合によってはある一定の順序で構成を行う必要があります。

### <a name="settings"></a>設定
リソースごとに選択した設定は、適切な接続を作成するうえで非常に重要です。 VPN Gateway の個々のリソースと設定については、[Azure Stack の VPN ゲートウェイ設定](azure-stack-vpn-gateway-settings.md)に関するページを参照してください。 ゲートウェイの種類、VPN の種類、接続の種類、ゲートウェイ サブネット、ローカル ネットワーク ゲートウェイ、検討が必要なその他のさまざまなリソース設定を把握するために役立つ情報が記載されています。

### <a name="deployment-tools"></a>デプロイ ツール
Azure Portal などの 1 つの構成ツールを使用して、リソースを作成し、構成できます。 後で、追加のリソースを構成したり、該当する場合に既存のリソースを変更したりするために、PowerShell などの別のツールに切り替えることができます。 現時点では、すべてのリソースとリソースの設定を Azure Portal で構成することはできません。 各接続トポロジの記事の手順では、特定の構成ツールが必要な場合が指定されています。

## <a name="connection-topology-diagrams"></a>接続トポロジの図
VPN ゲートウェイ接続ではさまざまな構成が利用できることを理解しておくことが重要です。 どの構成が自分のニーズに最適かを判断します。 以下のセクションでは、次の VPN ゲートウェイ接続に関する情報とトポロジの図を確認できます。また、これらのセクションには次の一覧が記載された表があります。

- 利用可能なデプロイメント モデル
- 利用可能な構成ツール
- 記事に直接移動するリンク (利用可能な場合)

以降のセクションの図と説明は、要件を満たす接続トポロジを選択するために役立ちます。 図は主要なベースライン トポロジを示していますが、図をガイドラインとして使用して、より複雑な構成を構築することもできます。

## <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a>サイト間とマルチサイト (IPsec/IKE VPN トンネル)
### <a name="site-to-site"></a>サイト間
サイト間 (S2S) VPN ゲートウェイ接続とは、IPsec/IKE (IKEv1 または IKEv2) VPN トンネルを介した接続です。 この種類の接続では、オンプレミスの VPN デバイスが必要です。そのデバイスは、パブリック IP アドレスを割り当てられていて、NAT の内側に配置されていない必要があります。 S2S 接続は、クロスプレミスおよびハイブリッド構成に使用できます。    
![サイト間 VPN 接続の構成の例](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-site-to-site-connection-diagram.png)

### <a name="multi-site"></a>マルチサイト
この種類の接続は、サイト間接続の一種です。 仮想ネットワーク ゲートウェイから複数の VPN 接続を作成し、通常は複数のオンプレミスのサイトに接続します。 複数の接続を使用する場合は、(クラシック VNet を使用する際に動的ゲートウェイと呼ばれる) RouteBased という VPN の種類を使用する必要があります。 各仮想ネットワークに配置できる VPN ゲートウェイは 1 つのみであるため、ゲートウェイを経由するすべての接続は、使用可能な帯域幅を共有します。 これは一般に "マルチサイト" 接続と呼ばれます。   
![Azure VPN Gateway マルチサイト接続の例](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-multisite-connection-diagram.png)



## <a name="gateway-skus"></a>ゲートウェイの SKU
Azure Stack の仮想ネットワーク ゲートウェイを作成する場合、使用するゲートウェイ SKU を指定する必要があります。 以下の VPN ゲートウェイ SKU がサポートされています。
- Basic
- 標準
- HighPerformance

Basic より Standard、Standard または Basic より HighPerformance というように、より上位のゲートウェイ SKU を選択すると、より多くの CPU とネットワーク帯域幅がゲートウェイに割り当てられます。 その結果、ゲートウェイは、仮想ネットワークに対してより高いネットワーク スループットをサポートできます。

Azure Stack では、UltraPerformance ゲートウェイ SKU はサポートされていません。この SKU は、ExpressRoute 専用です。

SKU を選択する場合、次を考慮してください。
- Azure Stack では、ポリシー ベースのゲートウェイはサポートされていません。
- Basic SKU では、ボーダー ゲートウェイ プロトコル (BGP) はサポートされていません。
- ExpressRoute と VPN Gateway が共存する構成は、Azure Stack ではサポートされていません。
- アクティブ/アクティブ S2S VPN ゲートウェイ接続は、HighPerformance SKU のみで構成できます。

## <a name="estimated-aggregate-throughput-by-sku"></a>SKU の予測される合計スループット
次の表は、ゲートウェイの種類と、ゲートウェイ SKU によって予測される合計スループットを示したものです。

|   | VPN Gateway のスループット *(1)* | VPN Gateway の IPsec トンネルの最大数 *(2)* |
|-------|-------|-------|
|**Basic SKU** ***(3)***    | 100 Mbps  | 10    |
|**Standard SKU**       | 100 Mbps  | 10    |
|**高性能 SKU** | 200 Mbps    | 5 |
***(1)*** インターネット経由でのクロスプレミス接続では、VPN スループットが保証されるわけではありません。 この値は、達成可能な最大スループットです。  
***(2)*** トンネルの最大数は、すべてのサブスクリプションの Azure Stack デプロイの合計です。  
***(3)*** BGP は、Basic SKU ではサポートされていません。

## <a name="next-steps"></a>次の手順
Azure Stack 用の [VPN ゲートウェイの設定](azure-stack-vpn-gateway-settings.md)について学習します。
