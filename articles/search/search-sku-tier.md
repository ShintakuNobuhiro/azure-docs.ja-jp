---
title: Azure Search サービスの価格レベルまたは SKU の選択 | Microsoft Docs
description: Azure Search は無料、Basic、Standard の各 SKU にプロビジョニングできます。Standard は、複数のリソース構成および容量レベルで使用できます。
services: search
author: HeidiSteen
manager: cgronlun
tags: azure-portal
ms.service: search
ms.topic: conceptual
ms.date: 05/12/2018
ms.author: heidist
ms.openlocfilehash: bbf535c5b446fd654331374d29c106b6e43d55f5
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/17/2018
ms.locfileid: "34266882"
---
# <a name="choose-a-pricing-tier-for-azure-search"></a>Azure Search の価格レベルの選択

Azure Search では、特定の価格レベルまたは SKU で[サービスがプロビジョニングされます](search-create-service-portal.md)。 指定できる選択肢には、**Free**、**Basic**、**Standard** があります。このうち **Standard** は、複数の構成および容量で指定できます。 

この記事では、こうしたレベルを選択するための情報を提供します。 これは、[価格ページ](https://azure.microsoft.com/pricing/details/search/)と[サービスの制限](search-limits-quotas-capacity.md)ページを補完するもので、さまざまな価格レベルに関連のある請求概念や消費パターンをまとめたものです。 また、お客様のニーズにどの階層が最適かを特定するため、反復的なアプローチをお勧めしています。 

価格レベルは、機能ではなく、容量を規定します。 ある価格レベルの容量が少なすぎることがわかった場合は、それより上の価格レベルで新しいサービスをプロビジョニングしたうえで、[インデックスを再読み込み](search-howto-reindex.md)する必要があります。 同じサービスで、特定の SKU から別の SKU へのインプレース アップグレードを実行することはできません。

使用可能な機能は、価格レベルの主な検討事項ではありません。 **Free** レベルを含む、すべての価格レベルで、S3HD のインデクサー サポートを除く、機能パリティが提供されています。 ただし、インデックス作成とリソースに対する制約により、実質的には機能の使用範囲が制限されることがあります。 たとえば、[コグニティブ検索](cognitive-search-concept-intro.md)インデックス作成には、データ セットのサイズがごく小さい場合を除いて Free サービスではタイムアウトになってしまう、実行時間の長いスキルがあります。

> [!TIP]
> ほとんどのお客様は、まず評価のために **Free** レベルから始め、その後 **Standard** レベルに移行します。 価格レベルを選択し、[検索サービスをプロビジョニング](search-create-service-portal.md)したら、[レプリカ数とパーティション数を増やし](search-capacity-planning.md)てパフォーマンスをチューニングできます。 容量を調整する理由とタイミングの詳細については、「[Azure Search のパフォーマンスと最適化に関する考慮事項](search-performance-optimization.md)」を参照してください。
>

## <a name="billing-concepts"></a>請求概念

容量の定義、サービスの制限、およびサービス ユニットなどの価格レベルの選択に必要な概念です。 

### <a name="capacity"></a>容量

容量は*レプリカ*と*パーティション*で構成されています。 レプリカは、検索サービスのインスタンスで、各レプリカは、インデックスの負荷分散されたコピーを 1 つ ホストします。 たとえば、6 つのレプリカを持つサービスには、サービスに読み込まれているすべてのインデックスのコピーが 6 つあります。 パーティションはインデックスを格納し、自動的に検索可能なデータを分割します。たとえば、2 つのパーティションによりインデックスが半分に、3 つのパーティションでは 3 分の 1 に分割されます。 容量の意味では、*パーティション サイズ*が、複数の価格レベルの中で大きな違いのある機能です。

> [!NOTE]
> すべての **Standard** 価格レベルでは、[レプリカとパーティションを柔軟に組み合わせる](search-capacity-planning.md#chart)ことができます。これにより、バランスを変更することにより、[速度とストレージ量のどちらを重視するか](search-performance-optimization.md)を決定できます。 **Basic** レベルでは、3 つのレプリカが使用できるため可用性が高くなりますが、パーティションは 1 つのみです。 **Free** レベルでは、専用のリソースが提供されません。コンピューティング リソースは複数の無料のサービス間で共有されます。

### <a name="limits"></a>制限

インデックス、インデクサーなどのホスト リソースを提供します。 各価格レベルには、作成できるリソース量に対する[サービス制限](search-limits-quotas-capacity.md)があります。 そのため、インデックス (およびその他のオブジェクト) の上限数は、複数の価格レベルで第二の差別化要素になります。 ポータルで各オプションを確認し、インデックスの数の制限に注意してください。 インデクサー、データ ソース、およびスキルセットなどのその他のリソースにはインデックスに対する制限が設定されます。

### <a name="search-units"></a>検索ユニット

理解する必要がある最も重要な請求概念は、Azure Search の課金単位となる*検索ユニット* (SU) です。 Azure Search が機能するには、レプリカとパーティションの両方が必要なため、この片方を基準に課金しても意味がありません。 このため、この両方を合わせた単位に基づいて課金されます。 数式的には、 SU は、サービスによって使用されるレプリカとパーティションの積 (R X P = SU) です。 すべてのサービスは少なくとも 1 SU (レプリカ 1 つと 1 つのパーティションを掛けた値) で始まりますが、より現実的なモデルは 3 レプリカ x 3 パーティションのサービスで、9 SU が課金されます。 

各価格レベルでは、段階的に容量が増量できますが、合計容量の一部をオンラインで使用し、残りを予備用に取っておくことができます。 課金の観点からいうと、オンラインで使用するパーティションとレプリカの数を SU 式を使用して計算すれば、実際に支払う金額が割り出せます。

課金レートは SU ごとの時間単位で、各価格レベルでレートが異なります。 各レベルの価格は、「 [Search の価格](https://azure.microsoft.com/pricing/details/search/)」を参照してください。

## <a name="consumption-patterns"></a>消費パターン

ほとんどのお客様が、**Free** サービスから始め、これを無期限に維持しながら、重要な開発または運用ワークロードのために **Standard** レベルを選択します。 

![Azure search の価格レベル](./media/search-sku-tier/tiers.png "Azure search の価格レベル")

これに対し、重要で特殊な消費パターンのために **Basic** と **S3 HD** が存在します。 **Basic** は、小規模な運用ワークロード用で、SLA、専用のリソース、高可用性を提供しますが、ストレージは 合計 2 GB を上限としており大きくはありません。 この価格レベルは、使用する容量が利用可能な容量を一貫して下回るお客様のために作られています。 反対に、**S3 HD** は、 ISV、パートナー、[マルチテナント ソリューション](search-modeling-multitenant-saas-applications.md)、または小さなインデックスを大量に必要とする構成に典型的なワークロードのために作られています。 多くの場合、**Basic** と **S3 HD** 価格レベルのどちらが適切かは明白ですが、さらにガイダンスが必要な場合は、[StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) に投稿して確認するか、または [Azure サポートに問い合わせてください](https://azure.microsoft.com/support/options/)。

より一般的に使用される Standard レベルの **S1-S3** 価格レベルでは、パーティションのサイズ、インデックス、インデクサー、推論リソースの最大数が変化した時点で、容量を段階的に増量できます。

|  | S1 | S2 | S3 |  |  |  |  |
|--|----|----|----|--|--|--|--|
| パーティション サイズ|  25 GB | 100 GB | 250 GB |  |  |  |  |
| インデックスとインデクサーの制限| 50 | 200 | 200 |  |  |  |  |

**S1** は専用のリソースと複数のパーティションが必要な場合に、一般的な選択肢です。 最大 12 の 25 GB のパーティションを使用して、レプリカに対しパーティションを最大化した場合、**S1** のサービス当たりの限界は合計 300 GB です (よりバランスの取れた構成については [ パーティションとレプリカの割り当て](search-capacity-planning.md#chart)を参照)。

ポータルと料金のページでは、パーティションのサイズとストレージが強調されていますが、各価格レベルのすべてのコンピューティング機能 (ディスク容量、速度、CPU) は価格と共に線形に増加します。 **S2** レプリカは **S1** より高速で、**S3** は **S2** より高速です。 **S3** レベルでは、格段に I/O が速いため、一般に線形パターンであるコンピューティング機能と価格の関係から逸脱します。 I/O のボトルネックを予測している場合、**S3** はこれより低い価格レベルよりはるかに高い IOPS を提供します。

**S3** と **S3 HD** は同一の大容量インフラストラクチャを使用していますが、上限に達する方法が異なります。 **S3** は、少数の非常に大きいインデックスのために作られています。 したがって、上限はリソースに左右されます (サービスあたり 2.4 TB)。 **S3 HD** は、大量の非常に小さいインデックスのために作られています。 インデックスが 1,000 個になった時点で、**S3 HD** は、インデックス制約という形の制限に達します。 **S3 HD** を使用しているお客様で、1,000 を超えるインデックスが必要な場合は、Microsoft サポートにお問い合わせください。

> [!NOTE]
> 以前は、ドキュメントの制限が考慮事項でしたが、2018年 1 月以降は、ほとんどの Azure Search サービスに適用されなくなりました。 ドキュメントの制限が引き続き適用される条件の詳細については、[サービスの制限: ドキュメントの制限](search-limits-quotas-capacity.md#document-limits)に関する記事を参照してください。
>

## <a name="evaluate-capacity"></a>容量の評価

容量が大きいほど、サービスの実行にかかるコストも高くなります。 価格レベルでは、ストレージとリソースの 2 つのレベルで制限が課せられます。どちらか先に上限に達した方が実質的な制限になりますので、この両方を考慮する必要があります。 

通常は、ビジネス要件によって必要なインデックスの数が規定されます。 たとえば、ドキュメントの大規模なリポジトリのグローバル インデックス、または、リージョン、アプリケーション、またはビジネス分野などによって複数のインデックスが必要になります。

インデックスのサイズを特定するには、インデックスを 1 つ[構築](search-create-index-portal.md)する必要があります。 Azure Search のデータ構造は、主に、[転置インデックス](https://en.wikipedia.org/wiki/Inverted_index)でソース データとは異なる特性があります。 転置インデックスのサイズと複雑性は、コンテンツによって決まり、必ずしも、フィードするデータ量によっては決まりません。 冗長性の高い大規模なデータ ソースの場合、変動の多いコンテンツを含む小さいデータセットの場合よりも、インデックスのサイズが小さくなることがあります。  そのため、元のデータ セットのサイズに基づいてインデックスのサイズを推測できることはほとんどありません。

### <a name="preliminary-estimates-using-the-free-tier"></a>Free レベルを使用した事前見積もり

容量を見積もる方法の一つは、まず **Free** レベルを使用することです。 **Free** サービスでは、50 MB のストレージ、最大 3 つのインデックス、2 分間のインデックス作成時間が提供されます。 これらの制約の中で予想インデックス サイズを見積もることは簡単ではありませんが、次の例で一つの方法を示します。

+ [Free サービスを作成する](search-create-service-portal.md)
+ 少量の代表的なデータ セットを準備します (たとえば、5,000 のドキュメントと 10% のサンプル サイズとします)。
+ [最初のインデックスを構築](search-create-index-portal.md)し、ポータルに表示されるサイズを確認します (たとえば、30 MB とします)。

サンプルが代表的でデータ ソース全体の 10% だったとすると、すべてのドキュメントにインデックスが付けられた場合、30 MB のインデックスは約 300 MB になります。 2 つのインデックス (開発用と運用用) に割り当てるためにこの予測値を 2 倍にし、合計 600 MB のストレージ要件を割り出せます。 この要件は **Basic** レベルで簡単に満たせるため、このレベルから利用を開始します。

### <a name="advanced-estimates-using-a-billable-tier"></a>課金対象のレベルを使用した高度な見積もり

お客様によっては、大量のサンプリングと処理時間に対応できる専用リソースからまず始め、開発段階でインデックスの量、サイズ、クエリ量の現実的な予想を立てることを選択することもできます。 当初は、サービスは最善の推測による見積もりを基にプロビジョニングされ、その後、開発プロジェクトが成熟するに伴って、通常は、既存のサービスの容量が予想される運用ワークロードを上回るか下回るかを予想することができます。 

1. [各価格レベルのサービス制限を確認](https://docs.microsoft.com/en-us/azure/search/search-limits-quotas-capacity#index-limits)して、より低い価格レベルで、必要なインデックス数に対応できるかどうかを判断してください。 間で、**Basic**-**S1**- **S2** レベルでのインデックス制限は、それぞれ 15、50、200 です。

1. [課金対象レベルでサービスを作成します](search-create-service-portal.md)。

    + 学習曲線の初めの段階では、**Basic** または **S1** の低レベルから始めます。
    + サイズの大きなインデックスの作成とクエリの読み込みが明らかに必要な場合は、**S2** または **S3** から始めます。

1. [最初のインデックスを構築](search-create-index-portal.md)して、ソース データがどのようにインデックスに変換されるかを特定します。 これは、インデックスのサイズを推測する唯一の方法です。

1. ポータルで、[ストレージ、サービス制限、クエリ量、および待機時間を監視](search-monitor-usage.md)します。 秒当たりのクエリ数、調整されたクエリ数、および検索の待機時間がポータルに表示されます。これらはすべてから、適切な価格レベルかどうかを決定できます。 ポータルのメトリックに加え、[検索トラフィック分析](search-traffic-analytics.md)を有効にすることで、クリックスルー分析などの詳細な監視を構成できます。 

ストレージ (パーティション) を使い果たした、またはリソース (インデックス、インデクサーなど) の最大限界に達した時点で最大制限に達するため、インデックス数とサイズのいずれも同様に重要です。 ポータルの [概要] ページでは、現在の使用状況と最大制限が並んで表示されるため、両方を追跡できます。

> [!NOTE]
> ドキュメントに余分なデータが含まれている場合は、ストレージの要件が過剰になる可能性があります。 ドキュメントには、検索機能に必要なデータのみが含まれていることが理想的な状態です。 検索不可能なバイナリ データは別の場所 (たとえば Azure テーブルまたは Blob Storage) に保存し、外部データへの URL 参照を保持するフィールドをインデックスに追加します。 個々のドキュメントの最大サイズは 16 MB です (1 回の要求で複数のドキュメントを一括アップロードする場合は、それより小さくなります)。 詳細については、「[Azure Search サービスの制限](search-limits-quotas-capacity.md)」をご覧ください。
>

**クエリ数に関する考慮事項**

クエリ/秒 (QPS) は、パフォーマンスのチューニング中に注目されるメトリックですが、一般に、最初から非常にクエリ数が多いことが予想される場合を除いては、価格レベルの考慮事項にはなりません。

すべての Standard レベルでは、レプリカとパーティションのバランスが取られているため、負荷分散のための追加のレプリカを使用することによってクエリの応答時間を短縮したり、並列処理のためにパーティションが追加されたりします。 サービスがプロビジョニングされた後に、パフォーマンスをチューニングできます。

最初から大量のクエリ数が継続することが予想される場合は、より強力なハードウェアを使用する、より高い価格レベルを検討してください。 その後、現実にはこれらのクエリ数に達しなかった場合、パーティションとレプリカをオフラインにするか、またはより低い価格レベルのサービスに切り替えることができます。 クエリ スループットの計算方法の詳細については、「[Azure Search のパフォーマンスと最適化に関する考慮事項](search-performance-optimization.md)」を参照してください。


**サービス レベル アグリーメント**

**Free** レベルおよびプレビュー機能には、[サービス レベル アグリーメント (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/) がありません。 課金対象のすべてのレベルで、SLA が有効になるのは、サービスにとって十分な冗長性がプロビジョニングされるときです。 クエリ (読み取り) の SLA には複数のレプリカが必要です。 クエリとインデックス作成 (読み取り/書き込み) の SLA には 3 つ以上のレプリカが必要です。 パーティション数は SLA には関係ありません。 

## <a name="tips-for-tier-evaluation"></a>レベルの評価に関するヒント

+ 効率的なインデックスを構築する方法と、どの更新方法が最も影響が少ないかについて説明します。 [検索トラフィック分析](search-traffic-analytics.md)を実行して、クエリ アクティビティに関する洞察を得ることをお勧めします。

+ クエリに関するメトリックを構築し、利用パターン (業務時間中のクエリ数、オフピーク時のインデックス作成) に関するデータを収集し、このデータを使用して、今後のサービス プロビジョニングに関する決定に役立つ情報を取得します。 時間ごと、または毎日のレベルでは実用的ではない場合、パーティションやリソースを動的に調節して、クエリ数の計画された変化に対応したり、措置を取る間、そのレベルで対応できる場合は、計画外の持続した変化に対応できます。

+ 過小なプロビジョニングの唯一の欠点は、実際の要件が見積もりより大きかった場合に、サービスを中断する必要があることに注意してください。 サービスの中断を回避するには、同じサブスクリプション内のより高いレベルで新しいサービスを作成し、すべてのアプリと要求が新しいエンドポイントを指すようになるまで並行して実行します。

## <a name="next-steps"></a>次の手順

**Free** レベルから始め、データのサブセットを使用し最初のインデックスを構築することで、その特性を理解します。 Azure Search のデータ構造は転置インデックスで、転置インデックスのサイズと複雑性はコンテンツによって決まります。 冗長性の高いコンテンツでは、不規則なコンテンツよりもインデックスのサイズが小さくなる傾向があります。 そのため、インデックス ストレージの要件を決定するのは、データ セットのサイズではなく、コンテンツの特性です。

インデックスのサイズが見積もれたら、この記事で説明されている **Basic** または **Standard** レベルのいずれかの[課金対象のサービスをプロビジョニング](search-create-service-portal.md)します。 データ のサブセットに対する人為的な制約を緩和し、実際に検索可能にする必要があるすべてのデータが含まれるよう、[インデックスを再構築](search-howto-reindex.md)します。

必要なパフォーマンスとスケールに合わせて[パーティションとレプリカを割り当て](search-capacity-planning.md)ます。

パフォーマンスと容量に問題なければ完了です。 問題がある場合は、より密接にニーズに適合するよう、別のレベルで検索サービスを再作成します。

> [!NOTE]
> ご不明な点がある場合は、[StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) に投稿するか、[Azure サポートにお問い合わせ](https://azure.microsoft.com/support/options/)ください。