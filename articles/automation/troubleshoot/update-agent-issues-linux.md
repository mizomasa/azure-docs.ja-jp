---
title: Azure Automation Update Management での Linux Update エージェントに関する問題のトラブルシューティング
description: Update Management ソリューションを使用して Linux Update エージェントの問題をトラブルシューティングして解決する方法について説明します。
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 12/03/2019
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: bba1c7e89a9c3bb1c9aa1567e36dd71a40f14636
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679073"
---
# <a name="troubleshoot-linux-update-agent-issues"></a>Linux Update エージェントに関する問題のトラブルシューティング

Update Management にマシンが準備完了 (正常) と表示されない理由は多数存在する可能性があります。 Update Management では、Hybrid Runbook Worker エージェントの正常性を検査して、背後にある問題を特定できます。 この記事では、Azure portal から Azure マシンを対象として、また、[オフラインのシナリオ](#troubleshoot-offline)で Azure 以外のマシンを対象としてトラブルシューティング ツールを実行する方法について説明します。 

次の一覧は、マシンが取り得る 3 つの準備状態です。

* 準備完了 - Hybrid Runbook Worker がデプロイされ、最後に表示されてからの経過時間が 1 時間未満である。
* 切断 -  Hybrid Runbook Worker がデプロイされ、最後に表示されてからの経過時間が 1 時間以上である。
* 未構成 - Hybrid Runbook Worker が見つからないか、オンボードが終了していない。

> [!NOTE]
> Azure portal に表示される内容とマシンの現在の状態の間で、わずかに遅延が発生する可能性があります。

## <a name="start-the-troubleshooter"></a>トラブルシューティングの開始

Azure マシンの場合は、ポータルの **[Update Agent Readiness]\(Update エージェントの準備\)** 列にある **[トラブルシューティング]** リンクをクリックすると、[Troubleshoot Update Agent]\(Update エージェントのトラブルシューティング\) ページが起動します。 Azure 以外のマシンの場合は、リンクをクリックすると、この記事が表示されます。 Azure 以外のマシンをトラブルシューティングするには、オフラインの手順を参照してください。

![VM リスト ページ](../media/update-agent-issues-linux/vm-list.png)

> [!NOTE]
> 検査を行うには VM が実行中である必要があります。 VM が実行されていない場合、 **[VM の開始]** ボタンが表示されます。

[Troubleshoot Update Agent]\(Update エージェントのトラブルシューティング\) ページで **[チェックを実行]** をクリックすると、トラブルシューティング ツールが開始されます。 トラブルシューティング ツールは、[[実行コマンド]](../../virtual-machines/linux/run-command.md) を使用してマシンでスクリプトを実行し、依存関係を検証します。 トラブルシューティング ツールの実行が完了すると、チェック結果が返されます。

![トラブルシューティング ページ](../media/update-agent-issues-linux/troubleshoot-page.png)

完了すると、結果がウィンドウに返されます。 チェック セクションには、各チェックの対象が示されます。

![Update エージェントのチェック ページ](../media/update-agent-issues-linux/update-agent-checks.png)

## <a name="prerequisite-checks"></a>前提条件のチェック

### <a name="operating-system"></a>オペレーティング システム

オペレーティング システム チェックでは、Hybrid Runbook Worker が次のいずれかのオペレーティング システムを実行しているかどうかが確認されます。

|オペレーティング システム  |Notes  |
|---------|---------|
|CentOS 6 (x86/x64) および 7 (x64)      | Linux エージェントは、更新リポジトリへのアクセスが必要です。 分類に基づく修正プログラムでは、CentOS に既定では設定されていない、セキュリティ データを返すための "yum" が必須です。         |
|Red Hat Enterprise 6 (x86/x64) および 7 (x64)     | Linux エージェントは、更新リポジトリへのアクセスが必要です。        |
|SUSE Linux Enterprise Server 11 (x86/x64) および 12 (x64)     | Linux エージェントは、更新リポジトリへのアクセスが必要です。        |
|Ubuntu 14.04 LTS、16.04 LTS、および 18.04 LTS (x86/x64)      |Linux エージェントは、更新リポジトリへのアクセスが必要です。         |

## <a name="monitoring-agent-service-health-checks"></a>監視エージェント サービスの正常性チェック

### <a name="log-analytics-agent"></a>Log Analytics エージェント

このチェックでは、Linux 用 Log Analytics エージェントがインストールされているかが確認されます。 インストールする方法については、「[Linux 用エージェントのインストール](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux
)」をご覧ください。

### <a name="log-analytics-agent-status"></a>Log Analytics エージェントの状態

このチェックでは、Linux 用 Log Analytics エージェントが実行されているかが確認されます。 このエージェントが実行されていない場合は、次のコマンドを実行してその再起動を試みることができます。 エージェントのトラブルシューティングの詳細については、[Linux Hybrid Runbook Worker のトラブルシューティング](hybrid-runbook-worker.md#linux)に関するページを参照してください。

```bash
sudo /opt/microsoft/omsagent/bin/service_control restart
```

### <a name="multihoming"></a>マルチホーム

このチェックでは、エージェントが複数のワークスペースに報告しているかどうかが判断されます。 Update Management では、マルチホームはサポートされていません。

### <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker

このチェックでは、Linux 用 Log Analytics エージェントに Hybrid Runbook Worker パッケージが含まれているかどうかが確認されます。 Update Management が動作するにはこのパッケージが必要です。

### <a name="hybrid-runbook-worker-status"></a>Hybrid Runbook Worker の状態

このチェックでは、Hybrid Runbook Worker がマシンで実行されていることが確認されます。 Hybrid Runbook Worker が正しく実行されている場合は、次のプロセスが存在する必要があります。 詳しくは、[Log Analytics エージェント for Linux のトラブルシューティング](hybrid-runbook-worker.md#oms-agent-not-running)に関する記事をご覧ください。

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

## <a name="connectivity-checks"></a>接続チェック

### <a name="general-internet-connectivity"></a>一般的なインターネット接続

このチェックでは、マシンがインターネットにアクセスできることが確認されます。

### <a name="registration-endpoint"></a>登録エンドポイント

このチェックでは、Hybrid Runbook Worker が Azure Automation および Log Analytics ワークスペースと正しく通信できるかどうかが確認されます。

Hybrid Runbook Worker エージェントが登録エンドポイントと通信できるように、プロキシとファイアウォールが構成されている必要があります。 アドレスと開くポートの一覧については、「[Hybrid Worker 用のネットワーク計画](../automation-hybrid-runbook-worker.md#network-planning)」を参照してください

### <a name="operations-endpoint"></a>操作エンドポイント

このチェックでは、エージェントが Job Runtime Data Service と正しく通信できるかどうかを判別します。

Hybrid Runbook Worker エージェントが Job Runtime Data Service と通信できるように、プロキシとファイアウォールが構成されている必要があります。 アドレスと開くポートの一覧については、「[Hybrid Worker 用のネットワーク計画](../automation-hybrid-runbook-worker.md#network-planning)」を参照してください

### <a name="log-analytics-endpoint-1"></a>Log Analytics エンドポイント 1

このチェックでは、Log Analytics エージェントで必要なエンドポイントにマシンがアクセスできることが確認されます。

### <a name="log-analytics-endpoint-2"></a>Log Analytics エンドポイント 2

このチェックでは、Log Analytics エージェントで必要なエンドポイントにマシンがアクセスできることが確認されます。

### <a name="log-analytics-endpoint-3"></a>Log Analytics エンドポイント 3

このチェックでは、Log Analytics エージェントで必要なエンドポイントにマシンがアクセスできることが確認されます。

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>オフライン トラブルシューティング

スクリプトをローカルに実行することで、Hybrid Runbook Worker のトラブルシューティング ツールをオフラインで使用できます。 python スクリプト [update_mgmt_health_check.py](https://gallery.technet.microsoft.com/scriptcenter/Troubleshooting-utility-3bcbefe6) は、スクリプト センターにあります。 このスクリプトの出力例を次に示します。

```output
Debug: Machine Information:   Static hostname: LinuxVM2
         Icon name: computer-vm
           Chassis: vm
        Machine ID: 00000000000000000000000000000000
           Boot ID: 00000000000000000000000000000000
    Virtualization: microsoft
  Operating System: Ubuntu 16.04.5 LTS
            Kernel: Linux 4.15.0-1025-azure
      Architecture: x86-64


Passed: Operating system version is supported

Passed: Microsoft Monitoring agent is installed

Debug: omsadmin.conf file contents:
        WORKSPACE_ID=00000000-0000-0000-0000-000000000000
        AGENT_GUID=00000000-0000-0000-0000-000000000000
        LOG_FACILITY=local0
        CERTIFICATE_UPDATE_ENDPOINT=https://00000000-0000-0000-0000-000000000000.oms.opinsights.azure.com/ConfigurationService.Svc/RenewCertificate
        URL_TLD=opinsights.azure.com
        DSC_ENDPOINT=https://scus-agentservice-prod-1.azure-automation.net/Accou            nts/00000000-0000-0000-0000-000000000000/Nodes\(AgentId='00000000-0000-0000-0000-000000000000'\)
        OMS_ENDPOINT=https://00000000-0000-0000-0000-000000000000.ods.opinsights            .azure.com/OperationalData.svc/PostJsonDataItems
        AZURE_RESOURCE_ID=/subscriptions/00000000-0000-0000-0000-000000000000/re            sourcegroups/myresourcegroup/providers/microsoft.compute/virtualmachines/linuxvm            2
        OMSCLOUD_ID=0000-0000-0000-0000-0000-0000-00
        UUID=00000000-0000-0000-0000-000000000000


Passed: Microsoft Monitoring agent is running

Passed: Machine registered with log analytics workspace:['00000000-0000-0000-0000-000000000000']

Passed: Hybrid worker package is present

Passed: Hybrid worker is running

Passed: Machine is connected to internet

Passed: TCP test for {scus-agentservice-prod-1.azure-automation.net} (port 443)             succeeded

Passed: TCP test for {eus2-jobruntimedata-prod-su1.azure-automation.net} (port 4            43) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.ods.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.oms.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {ods.systemcenteradvisor.com} (port 443) succeeded

```

## <a name="next-steps"></a>次のステップ

Hybrid Runbook Worker のその他の問題をトラブルシューティングする方法については、「[Hybrid Runbook Worker のトラブルシューティング](hybrid-runbook-worker.md)」を参照してください。
