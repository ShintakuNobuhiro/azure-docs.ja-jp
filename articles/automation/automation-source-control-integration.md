---
title: Azure Automation でのソース管理の統合
description: この記事では、Azure Automation での GitHub とのソース管理の統合について説明します。
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a75167b6973d5cce8497c07e21d17ad071290f1d
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/16/2018
---
# <a name="source-control-integration-in-azure-automation"></a>Azure Automation でのソース管理の統合
ソース管理の統合により、Automation アカウントの Runbook を GitHub のソース管理リポジトリに関連付けることができます。 ソース管理により、チームとの共同作業、変更の追跡、Runbook の以前のバージョンへのロールバックを簡単に実行できるようになります。 たとえば、開発、テスト、または運用の Automation アカウントに異なるブランチをソース管理で同期できるようになり、開発環境内でテストされたコードを運用の Automation アカウントに昇格することが容易になります。

ソース管理することで、Azure Automation からソース管理にコードをプッシュしたり、ソース管理から Azure Automation に Runbook をプルしたりできます。 この記事では、Azure Automation 環境内でソース管理を設定する方法について説明します。 まず、Azure Automation を GitHub リポジトリにアクセスするよう構成し、ソース管理の統合を使用することで実行できるさまざまな操作について、順を追って説明します。 

> [!NOTE]
> ソース管理では、[PowerShell ワークフロー Runbook](automation-runbook-types.md#powershell-workflow-runbooks) および [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks) のプル操作とプッシュ操作がサポートされていますが、 [グラフィカル Runbook](automation-runbook-types.md#graphical-runbooks) はサポートされません。<br><br>
> 
> 

Automation アカウントのソース管理を構成するには、2 つの簡単な手順が必要です。ただし、既に GitHub アカウントを持っている場合、必要な手順は 1 つだけです。 次に例を示します。

## <a name="step-1--create-a-github-repository"></a>手順 1. GitHub リポジトリを作成する
GitHub アカウントと、Azure Automation にリンクするリポジトリが既にある場合は、既存のアカウントにサインインし、次の手順 2. から開始してください。 それ以外の場合は、[GitHub](https://github.com/) に移動し、新しいアカウントにサインアップして、[新しいリポジトリを作成](https://help.github.com/articles/create-a-repo/)します。

## <a name="step-2--set-up-source-control-in-azure-automation"></a>手順 2. Azure Automation でソース管理を設定する
1. Azure Portal の [Automation アカウント] ページの **[アカウント設定]** で、**[ソース管理]** をクリックします。 
   
1. **[ソース管理]** ページが開きます。ここで、GitHub アカウントの詳細を構成できます。 構成するパラメーターを次に示します。  
   
   | **パラメーター** | **説明** |
   |:--- |:--- |
   | ソースの選択 |ソースを選択します。 現時点では、 **GitHub** のみがサポートされています。 |
   | 承認 |**[承認する]** ボタンをクリックすると、GitHub リポジトリへのアクセスが Azure Automation に許可されます。 既に別のウィンドウで GitHub アカウントにログインしている場合は、そのアカウントの資格情報が使用されます。 承認が成功すると、ページの **[承認プロパティ]** の下に GitHub のユーザー名が表示されます。 |
   | リポジトリの選択 |使用可能なリポジトリの一覧から GitHub リポジトリを選択します。 |
   | ブランチの選択 |使用可能なブランチの一覧からブランチを選択します。 ブランチを作成したことがない場合は、 **master** ブランチのみが表示されます。 |
   | Runbook フォルダーのパス |Runbook フォルダーのパスは、コードをプッシュまたはプルする GitHub リポジトリのパスを指定します。 これは、 **/foldername/subfoldername**形式で指定する必要があります。 Runbook フォルダーのパスにある Runbook のみが Automation アカウントと同期されます。 Runbook フォルダーのパスのサブフォルダーにある Runbook は同期 **されません** 。 リポジトリですべての Runbook を同期するには、 **/** を使います。 |
3. たとえば、**PowerShellScripts** という名前のリポジトリに **RootFolder** という名前のフォルダーがあり、このフォルダー内に **SubFolder** という名前のフォルダーがあるとします。 次の文字列を使用して、各フォルダー レベルで同期できます。
   
   1. **リポジトリ**から Runbook を同期するには、Runbook フォルダーのパスは */*
   2. **RootFolder**から Runbook を同期するには、Runbook フォルダーのパスは */RootFolder*
   3. **SubFolder**から Runbook を同期するには、Runbook フォルダーのパスは */RootFolder/SubFolder*となります。
4. 構成したパラメーターは、 **[ソース管理の設定]** ページに表示されます。  
   
    ![ソース管理ページを構成する](media/automation-source-control-integration/automation_02_SourceControlConfigure.png)
5. **[OK]** をクリックすると、ソース管理の統合は、お使いの Automation アカウント用に構成されるため、GitHub 情報を使用して更新する必要があります。 この部分をクリックすると、ソース管理の同期ジョブの履歴がすべて表示されます。  
   
    ![リポジトリの値](media/automation-source-control-integration/automation_03_RepoValues.png)
6. ソース管理の設定後、次の Automation リソースが Automation アカウントに作成されます。  
   2 つの[変数アセット](automation-variables.md)が作成されます。  
   
   * 次に示すように、 **Microsoft.Azure.Automation.SourceControl.Connection** 変数には、接続文字列の値が格納されます。  
     
     | **パラメーター** | **値** |
     |:--- |:--- |
     | Name |Microsoft.Azure.Automation.SourceControl.Connection |
     | type |String |
     | 値 |{"Branch":\<*ブランチ名*>,"RunbookFolderPath":\<*Runbookフォルダー パス*>,"ProviderType":\<*GitHub の場合は値 1*>,"Repository":\<*リポジトリ名*>,"Username":\<*Your GitHub ユーザー名*>} |

    * **Microsoft.Azure.Automation.SourceControl.OauthToken**変数には、OAuthToken のセキュリティで保護され暗号化された値が格納されます。  

    |**パラメーター**            |**値** |
    |:---|:---|
    | Name  | Microsoft.Azure.Automation.SourceControl.OauthToken |
    | type | Unknown(Encrypted) |
    | 値 | <*暗号化された OAuthToken*> |  

    ![variables](media/automation-source-control-integration/automation_04_Variables.png)  

    * **Automation ソース管理** は、承認済みのアプリケーションとして GitHub アカウントに追加されます。 アプリケーションを表示するには、GitHub のホーム ページから **[プロファイル]** > 、**[設定]** > 、**[アプリケーション]** の順に移動します。 このアプリケーションにより、Azure Automation は GitHub リポジトリを Automation アカウントに同期できます。  

    ![Git アプリケーション](media/automation-source-control-integration/automation_05_GitApplication.png)


## <a name="using-source-control-in-automation"></a>Automation でのソース管理の使用
### <a name="check-in-a-runbook-from-azure-automation-to-source-control"></a>Azure Automation からソース管理に Runbook をチェックインする
Runbook をチェックインすると、Azure Automation で Runbook に対して行った変更をソース管理リポジトリにプッシュできます。 Runbook のチェックイン手順は次のとおりです。

1. Automation アカウントから、[テキスト形式の Runbook を新しく作成](automation-first-runbook-textual.md)するか、[テキスト形式の既存の Runbook を編集](automation-edit-textual-runbook.md)します。 この Runbook は、PowerShell ワークフローまたは PowerShell スクリプト Runbook のどちらでもかまいません。  
2. Runbook を編集して保存したら、**[編集]** ページの **[チェックイン]** をクリックします。  
   
    ![[チェックイン] ボタン](media/automation-source-control-integration/automation_06_CheckinButton.png)

     > [!NOTE] 
     > Azure Automation からチェックインすると、ソース管理に現在存在しているコードが上書きされます。 チェックインするための対応する Git コマンド ライン命令は、**git add + git commit + git push** です。  

1. **[チェックイン]** をクリックすると、確認メッセージが表示されるので、**[はい]** をクリックして続行します。  
   
    ![チェックイン メッセージ](media/automation-source-control-integration/automation_07_CheckinMessage.png)
2. チェックインにより、ソース管理 Runbook の **Sync-MicrosoftAzureAutomationAccountToGitHubV1**が開始されます。 この Runbook は、GitHub に接続し、変更を Azure Automation からリポジトリにプッシュします。 チェックインしたジョブの履歴を表示するには、**[ソース管理の統合]** タブに戻り、[リポジトリの同期] ページをクリックして開きます。 このページには、すべてのソース管理ジョブが表示されます。  表示するジョブを選択し、クリックして詳細を表示します。  
   
    ![チェックイン Runbook](media/automation-source-control-integration/automation_08_CheckinRunbook.png)
   
   > [!NOTE]
   > ソース管理 Runbook は、特別な Automation Runbook であり、表示または編集することはできません。 これらは Runbook の一覧に表示されませんが、ジョブの一覧には同期ジョブが表示されます。
   > 
   > 
3. 変更された Runbook の名前は、入力パラメーターとしてチェックイン Runbook に送信されます。 **[リポジトリの同期]** ページで Runbook を展開すると、[ジョブの詳細を表示](automation-runbook-execution.md#viewing-job-status-from-the-azure-portal)できます。  
   
    ![チェックイン入力](media/automation-source-control-integration/automation_09_CheckinInput.png)
4. ジョブが完了したら GitHub リポジトリを更新して、変更を表示します。  リポジトリにコミットすると、「**Azure Automation で "*Runbook 名*" が更新されました**」というコミット メッセージが表示されます。  

### <a name="sync-runbooks-from-source-control-to-azure-automation"></a>ソース管理から Azure Automation に Runbook を同期する
[リポジトリの同期] ページの [同期] ボタンを使用すると、リポジトリの Runbook フォルダーのパスにあるすべての Runbook を Automation アカウントにプルできます。 同一リポジトリを複数の Automation アカウントに同期できます。 Runbook の同期手順は次のとおりです。

1. ソース管理を設定した Automation アカウントで、**[ソース管理の統合] の [リポジトリの同期]** ページを開き、**[同期]** をクリックします。確認メッセージが表示されるので、**[はい]** をクリックして続行します。  
   
    ![同期ボタン](media/automation-source-control-integration/automation_10_SyncButtonwithMessage.png)
2. 同期により、 **Sync-MicrosoftAzureAutomationAccountToGitHubV1**Runbook が開始されます。 この Runbook は、GitHub に接続し、変更をリポジトリから Azure Automation にプルします。 **[リポジトリ同期]** ページにこのアクション向けの新しいジョブが表示されます。 同期ジョブの詳細を表示するには、[ジョブの詳細] ページをクリックして開きます。  
   
    ![Runbook の同期](media/automation-source-control-integration/automation_11_SyncRunbook.png)

    > [!NOTE] 
    > ソース管理から同期すると、ソース管理に現在含まれている **すべての** Runbook のうち、Automation アカウントに現在存在するドラフト バージョンの Runbook が上書きされます。 同期するための対応する Git コマンド ライン命令は、**git pull** です。


## <a name="troubleshooting-source-control-problems"></a>ソース管理の問題のトラブルシューティング
チェックイン ジョブまたは同期ジョブにエラーがある場合は、ジョブが中断状態になるため、[ジョブ] ページでエラーに関する詳細情報を表示できます。  **[すべてのログ]** 部分には、該当するジョブに関連付けられた PowerShell ストリームがすべて表示されます。 これにより、チェックインまたは同期の問題を解決するために必要な詳細情報が提供されます。Runbook での同期またはチェックイン中に発生した一連のアクションも表示されます。  

![AllLogs イメージ](media/automation-source-control-integration/automation_13_AllLogs.png)

## <a name="disconnecting-source-control"></a>ソース管理の切断
GitHub アカウントから切断するには、[リポジトリの同期] ページを開き、**[切断]** をクリックします。 ソース管理を切断すると、それまでに同期された Runbook はそのまま Automation アカウントに保持されますが、[リポジトリの同期] ページは有効になりません。  

  ![[切断] ボタン](media/automation-source-control-integration/automation_12_Disconnect.png)

## <a name="next-steps"></a>次の手順
ソース管理の統合の詳細については、次のリソースをご覧ください。  

* [Azure Automation: Source Control Integration in Azure Automation](https://azure.microsoft.com/blog/azure-automation-source-control-13/)  
* [好みのソース管理システムの投票](https://www.surveymonkey.com/r/?sm=2dVjdcrCPFdT0dFFI8nUdQ%3d%3d)  
* [Azure Automation: Integrating Runbook Source Control using Visual Studio Online](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/)  

