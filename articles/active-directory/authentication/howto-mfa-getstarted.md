---
title: クラウドでの Azure MFA の概要 | Microsoft Docs
description: クラウドで Azure MFA を開始する方法について説明する Microsoft Azure Multi-Factor Authentication のページです。
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: get-started-article
ms.date: 06/24/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: 0a822d55e8d7bd0d503eb7d77f96dc9e60e1a4ba
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2018
---
# <a name="getting-started-with-azure-multi-factor-authentication-in-the-cloud"></a>クラウドでの Azure Multi-Factor Authentication Server の概要
この記事では、クラウドで Azure Multi-Factor Authentication の使用を開始する方法について説明します。

> [!NOTE]
> 次のドキュメントでは、**Azure Portal** を使用してユーザーを有効にする方法について説明しています。 Office 365 ユーザー向けに Azure Multi-Factor Authentication を設定する方法についての情報をお探しの場合は、「[Office 365 ユーザー用の多要素認証を設定する](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6?ui=en-US&rs=en-US&ad=US)」を参照してください。

![MFA in the Cloud](./media/howto-mfa-getstarted/mfa_in_cloud.png)

## <a name="prerequisite"></a>前提条件
[Azure サブスクリプションにサインアップする](https://azure.microsoft.com/pricing/free-trial/) - Azure サブスクリプションをまだ取得していない場合は、サインアップが必要です。 Azure MFA を開始して使ってみるだけの場合、試用版のサブスクリプションを使用できます。

## <a name="enable-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication の有効化
ユーザーが Azure Multi-Factor Authentication を利用できるライセンスをお持ちであれば、Azure MFA を有効にするうえで必要な作業は特にありません。 ユーザーごとに 2 段階認証の適用を開始できます。 Azure MFA を利用できるライセンスは、次の 3 種類です。
- Azure Multi-Factor Authentication
- Azure Active Directory Premium
- Enterprise Mobility + Security

この 3 種類のライセンスのいずれもお持ちでない場合や、全ユーザーをカバーできるだけのライセンスがない場合でも、心配いりません。 手順が 1 つ増え、ディレクトリで[多要素認証プロバイダーを作成する](concept-mfa-authprovider.md)作業が加わるだけです。

## <a name="turn-on-two-step-verification-for-users"></a>ユーザーに対する 2 段階認証をオンにする

[ユーザーまたはグループに 2 段階認証を要求する方法](howto-mfa-userstates.md)に関するページに記載されているいずれかの手順を使用して、実際に Azure MFA を使ってみましょう。 すべてのサインインに対して 2 段階認証を実施するか、条件付きアクセス ポリシーを作成して必要なときにのみ 2 段階認証を要求するかを選ぶことができます。

## <a name="next-steps"></a>次の手順
クラウド側の Azure Multi-Factor Authentication を設定したら、デプロイ側の構成とセットアップを行います。 詳細については、「[Azure Multi-Factor Authentication の構成](howto-mfa-mfasettings.md)」を参照してください。

