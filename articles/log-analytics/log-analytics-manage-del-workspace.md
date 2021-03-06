---
title: Azure Log Analytics ワークスペースを削除する | Microsoft Docs
description: 個人用の月額プランで Log Analytics ワークスペースを作成した場合や、ワークスペース モデルを再構築する場合に、Log Analytics ワークスペースを削除する方法について説明します。
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2018
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 084b8fa0addbe2ddeee699b58c9b0de1a6f27d99
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2018
---
# <a name="delete-an-azure-log-analytics-workspace-with-the-azure-portal"></a>Azure Portal で Azure Log Analytics ワークスペースを削除する
この記事では、不要になった Azure Log Analytics ワークスペースを Azure Portal を使用して削除する方法を示します。 

## <a name="to-delete-a-workspace"></a>ワークスペースを削除するには 
Log Analytics ワークスペースを削除すると、そのワークスペースに関連するすべてのデータが 30 日以内にサービスから削除されます。  サービス操作に悪影響を及ぼす重要なデータや構成が含まれていることがあるため、ワークスペースを削除する場合は注意が必要です。 Log Analytics にデータを格納する次のようなその他の Azure サービスとソースを考慮してください。

* Application Insights
* Azure Security Center
* Azure Automation
* Windows と Linux の仮想マシンで実行されているエージェント
* 環境内の Windows および Linux のコンピューターで実行されているエージェント
* System Center Operations Manager
* 管理ソリューション 

ワークスペースにレポートするように構成されたすべてのエージェントと System Center Operations Manager 管理グループは、引き続き孤立した状態になります。  続行する前に、ワークスペースに統合するエージェント、ソリューション、および Azure サービスの一覧を作成します。   
 
ユーザーが管理者で、ワークスペースに関連付けられている複数のユーザーが存在する場合は、それらのユーザーとワークスペースの間の関連付けがなくなります。 ユーザーが他のワークスペースに関連付けられている場合は、その他のワークスペースを使用して Log Analytics を継続して使用できます。 ただし、他のワークスペースに関連付けられていない場合、Log Analytics を使用するには、ワークスペースを作成する必要があります。 

1. [Azure Portal](http://portal.azure.com) にサインインします。 
2. Azure ポータルで、左下隅にある **[その他のサービス]** をクリックします。 リソースの一覧で、「**Log Analytics**」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[Log Analytics]** を選択します。
3. Log Analytics サブスクリプション ウィンドウでワークスペースを選択して、中央のウィンドウの上部にある **[削除]** をクリックします。<br><br> ![ワークスペースのプロパティ ウィンドウの削除オプション](media/log-analytics-manage-del-workspace/log-analytics-delete-workspace.png)<br>  
4. ワークスペースの削除を確定するよう求める確認メッセージ ウィンドウが表示されたら、**[はい]** をクリックします。<br><br> ![ワークスペースの削除の確定](media/log-analytics-manage-del-workspace/log-analytics-delete-workspace-confirm.png)

