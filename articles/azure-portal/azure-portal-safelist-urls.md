---
title: ファイアウォールまたはプロキシ サーバーのセーフリストに Azure portal の URL を追加する
description: これらの URL をプロキシ サーバー バイパスに追加して、Azure portal やそのサービスと通信を行います
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 04/10/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 399c64c88e78079432fcf7c09dafd199da83358b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "81255051"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>ファイアウォールまたはプロキシ サーバーのセーフリストに Azure portal の URL を追加する

Azure portal の URLl のセキュリティ制限をバイパスするようにオンプレミスのセキュリティデバイスを構成することができます。 この構成により、ローカルまたはワイドエリアネットワークと Azure クラウドの間のパフォーマンスと接続性が向上します。

ネットワーク管理者は、多くの場合、プロキシサーバー、ファイアウォール、またはその他のデバイスを展開します。 これらのデバイスはセキュリティで保護されており、ユーザーがインターネットにアクセスする方法を制御できます。 ユーザーを保護するように設計されたルールは、ビジネスに関連する正当なインターネットトラフィックをブロックまたは低速化する場合があります。 このトラフィックには、お互いと Azure 間の通信が含まれます。 ご利用のネットワークと Azure portal およびそのサービスとの間の接続性を最適化するために、Azure portal の URL をセーフリストに追加することをお勧めします。

## <a name="azure-portal-urls-for-proxy-bypass"></a>プロキシのバイパス対象となる Azure portal の URL

Azure portal 用にセーフリストに追加する URL エンドポイントは、組織がデプロイされている Azure クラウドに固有のものです。 これらのエンドポイントへのネットワークトラフィックで制限をバイパスできるようにするには、クラウドを選択します。 次に、プロキシサーバーまたはファイアウォールに URL の一覧を追加します。

#### <a name="public-cloud"></a>[パブリック クラウド](#tab/public-cloud)

```
*.aadcdn.microsoftonline-p.com
*.aka.ms
*.applicationinsights.io
*.azure.com
*.azure.net
*.azureafd.net
*.azure-api.net
*.azuredatalakestore.net
*.azureedge.net
*.loganalytics.io
*.microsoft.com
*.microsoftonline.com
*.microsoftonline-p.com
*.msauth.net
*.msftauth.net
*.trafficmanager.net
*.visualstudio.com
*.windows.net
*.windows-int.net
```

#### <a name="us-government-cloud"></a>[米国政府のクラウド](#tab/us-government-cloud)

```
*.azure.us
*.loganalytics.us
*.microsoft.us
*.microsoftonline.us
*.msauth.net
*.usgovcloudapi.net
*.usgovtrafficmanager.net
*.windowsazure.us
```

#### <a name="china-government-cloud"></a>[中国政府のクラウド](#tab/china-government-cloud)

```
*.azure.cn
*.microsoft.cn
*.microsoftonline.cn
*.chinacloudapi.cn
*.trafficmanager.cn
*.chinacloudsites.cn
*.windowsazure.cn
```
---

> [!NOTE]
> これらのエンドポイントへのトラフィックには、標準的な TCP ポートが使用されます (HTTP では 80、HTTPS では 443)。
>
>
