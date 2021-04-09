---
title: VPN-átjárók hibakeresése és figyelése – Azure Automation
titleSuffix: Azure Network Watcher
description: Ez a cikk bemutatja, hogyan diagnosztizálhatja a helyszíni kapcsolatot Azure Automation és Network Watcher
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: af671996722524de9af1a90ae8dfde27f814c8c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011812"
---
# <a name="monitor-vpn-gateways-with-network-watcher-troubleshooting"></a>VPN-átjárók figyelése Network Watcher hibaelhárítással

A hálózati teljesítmény alapos betekintést biztosít a megbízható szolgáltatások biztosításához az ügyfeleknek. Ezért fontos, hogy gyorsan felderítse a hálózati leállás feltételeit, és javítsa a kiesési feltétel enyhítését. A Azure Automation lehetővé teszi a feladatok programozott módon történő megvalósítását és futtatását a runbookok használatával. A Azure Automation használata tökéletes receptet hoz létre a folyamatos és proaktív hálózati figyelés és riasztások elkészítése során.

## <a name="scenario"></a>Eset

Az alábbi képen egy többrétegű alkalmazás található, amely VPN Gateway és alagút használatával létesített helyszíni kapcsolattal rendelkezik. A VPN Gateway működésének biztosítása kritikus fontosságú az alkalmazások teljesítményében.

A rendszer létrehoz egy runbook a VPN-alagút kapcsolati állapotának keresésére szolgáló parancsfájllal, a kapcsolati alagút állapotának kereséséhez az erőforrás-hibaelhárítási API használatával. Ha az állapot nem kifogástalan, a rendszer e-mail-triggert küld a rendszergazdáknak.

![Példaforgatókönyv][scenario]

Ez a forgatókönyv a következőket teszi:

- A parancsmagot hívó runbook létrehozása a `Start-AzureRmNetworkWatcherResourceTroubleshooting` kapcsolódási állapot megoldásához
- Ütemterv összekapcsolása a runbook

## <a name="before-you-begin"></a>Előkészületek

A forgatókönyv elindítása előtt a következő előfeltételeknek kell megfelelnie:

- Azure Automation-fiók az Azure-ban. Győződjön meg arról, hogy az Automation-fiók rendelkezik a legújabb modulokkal, és rendelkezik a AzureRM. Network modullal is. Ha hozzá szeretné adni az Automation-fiókhoz, a AzureRM. Network modul a modul-galériában érhető el.
- A Azure Automationban konfigurálnia kell a hitelesítő adatok készletét. További információ: [Azure Automation biztonság](../automation/automation-security-overview.md)
- Érvényes SMTP-kiszolgáló (Microsoft 365, a helyszíni e-mail-cím vagy egy másik) és a Azure Automationban definiált hitelesítő adatok
- Egy konfigurált Virtual Network átjáró az Azure-ban.
- Meglévő Storage-fiók meglévő tárolóval a naplók tárolásához.

> [!NOTE]
> Az előző képen látható infrastruktúra szemléltetési célokat szolgál, és nem jön létre a cikkben foglalt lépésekkel.

### <a name="create-the-runbook"></a>A runbook létrehozása

A példa konfigurálásának első lépése a runbook létrehozása. Ez a példa egy futtató fiókot használ. A futtató fiókokkal kapcsolatos további információkért látogasson el a [Runbookok hitelesítése Azure-beli futtató fiókkal](../automation/manage-runas-account.md) című témakörre.

### <a name="step-1"></a>1. lépés

Navigáljon Azure Automation a [Azure Portal](https://portal.azure.com) , majd kattintson a **runbookok** elemre.

![az Automation-fiók áttekintése][1]

### <a name="step-2"></a>2. lépés

Kattintson a **Runbook hozzáadása** elemre a runbook létrehozási folyamatának elindításához.

![runbookok panel][2]

### <a name="step-3"></a>3. lépés

A **gyors létrehozás** területen kattintson az **új runbook létrehozása** elemre a runbook létrehozásához.

![runbook panel hozzáadása][3]

### <a name="step-4"></a>4. lépés

Ebben a lépésben a runbook egy nevet adunk, a példában a **Get-VPNGatewayStatus**. Fontos, hogy a runbook adjon meg egy leíró nevet, és ajánlott megadni egy nevet, amely a szabványos PowerShell elnevezési szabványokat követi. A példában szereplő runbook a **PowerShell**, a többi lehetőség a grafikus, a PowerShell-munkafolyamat és a grafikus PowerShell-munkafolyamat.

![runbook panel][4]

### <a name="step-5"></a>5. lépés

Ebben a lépésben a runbook jön létre, az alábbi kódrészlet tartalmazza a példához szükséges összes kódot. A kód azon elemeit, amelyeknek tartalmaznia \<value\> kell az előfizetés értékeit, le kell cserélni.

Használja a következő kódot, kattintson a **Mentés** gombra.

```powershell
# Set these variables to the proper values for your environment
$automationCredential = "<work or school account>"
$fromEmail = "<from email address>"
$toEmail = "<to email address>"
$smtpServer = "<smtp.office365.com>"
$smtpPort = 587
$runAsConnectionName = "<AzureRunAsConnection>"
$subscriptionId = "<subscription id>"
$region = "<Azure region>"
$vpnConnectionName = "<vpn connection name>"
$vpnConnectionResourceGroup = "<resource group name>"
$storageAccountName = "<storage account name>"
$storageAccountResourceGroup = "<resource group name>"
$storageAccountContainer = "<container name>"

# Get credentials for work or school account
$cred = Get-AutomationPSCredential -Name $automationCredential

# Get the connection "AzureRunAsConnection "
$servicePrincipalConnection=Get-AutomationConnection -Name $runAsConnectionName

"Logging in to Azure..."
Connect-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $servicePrincipalConnection.TenantId `
    -ApplicationId $servicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
"Setting context to a specific subscription"
Set-AzureRmContext -SubscriptionId $subscriptionId

$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $region }
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName
$connection = Get-AzureRmVirtualNetworkGatewayConnection -Name $vpnConnectionName -ResourceGroupName $vpnConnectionResourceGroup
$sa = Get-AzureRmStorageAccount -Name $storageAccountName -ResourceGroupName $storageAccountResourceGroup 
$storagePath = "$($sa.PrimaryEndpoints.Blob)$($storageAccountContainer)"
$result = Start-AzureRmNetworkWatcherResourceTroubleshooting -NetworkWatcher $networkWatcher -TargetResourceId $connection.Id -StorageId $sa.Id -StoragePath $storagePath

if($result.code -ne "Healthy")
    {
        $body = "Connection for $($connection.name) is: $($result.code) `n$($result.results[0].summary) `nView the logs at $($storagePath) to learn more."
        Write-Output $body
        $subject = "$($connection.name) Status"
        Send-MailMessage `
        -To $toEmail `
        -Subject $subject `
        -Body $body `
        -UseSsl `
        -Port $smtpPort `
        -SmtpServer $smtpServer `
        -From $fromEmail `
        -BodyAsHtml `
        -Credential $cred
    }
else
    {
    Write-Output ("Connection Status is: $($result.code)")
    }
```

### <a name="step-6"></a>6. lépés

A runbook mentését követően egy ütemtervet kell csatolni ahhoz, hogy automatizálja a runbook indítását. A folyamat elindításához kattintson az **ütemterv** gombra.

![6. lépés][6]

## <a name="link-a-schedule-to-the-runbook"></a>Ütemterv összekapcsolása a runbook

Létre kell hozni egy új ütemtervet. Kattintson az **ütemterv csatolása a runbook** elemre.

![7. lépés][7]

### <a name="step-1"></a>1. lépés

Az **ütemterv** panelen kattintson az **új ütemterv létrehozása** lehetőségre.

![8. lépés][8]

### <a name="step-2"></a>2. lépés

Az **új ütemterv** panelen töltse ki az ütemtervre vonatkozó információkat. A beállítható értékek az alábbi listában láthatók:

- **Név** – az ütemterv rövid neve.
- **Leírás** – az ütemterv leírása.
- **Starts (indítás** ) – Ez az érték a dátum, idő és időzóna kombinációja, amely az ütemezett eseményindítók időpontját hozza létre.
- Ismétlődés – ez az érték határozza meg az ütemezések **ismétlődését** .  Az érvényes értékek **egyszer** vagy **ismétlődőek**.
- **Ismétlődik minden** – az ütemterv ismétlődési időköze órában, napban, hetekben vagy hónapokban.
- **Lejárat beállítása** – az érték határozza meg, hogy az ütemterv lejár-e vagy sem. Értéke **Igen** vagy **nem** lehet. Ha az Igen lehetőséget választja, érvényes dátumot és időpontot kell megadni.

> [!NOTE]
> Ha egy runbook gyakrabban fut le óránként, több ütemtervet kell létrehoznia különböző intervallumokban (azaz 15, 30, 45 perccel az óra után)

![9. lépés][9]

### <a name="step-3"></a>3. lépés

Kattintson a Save (Mentés) gombra az ütemterv runbook való mentéséhez.

![10. lépés][10]

## <a name="next-steps"></a>Következő lépések

Most, hogy megértette, hogyan integrálhatja Network Watcher hibaelhárítást a Azure Automationval, megtudhatja, hogyan indíthat el a csomagok rögzítését a virtuális gépek riasztásai számára, ha meglátogatja a [riasztás által aktivált csomagok rögzítését az Azure Network Watcher](network-watcher-alert-triggered-packet-capture.md)használatával.

<!-- images -->
[scenario]: ./media/network-watcher-monitor-with-azure-automation/scenario.png
[1]: ./media/network-watcher-monitor-with-azure-automation/figure1.png
[2]: ./media/network-watcher-monitor-with-azure-automation/figure2.png
[3]: ./media/network-watcher-monitor-with-azure-automation/figure3.png
[4]: ./media/network-watcher-monitor-with-azure-automation/figure4.png
[5]: ./media/network-watcher-monitor-with-azure-automation/figure5.png
[6]: ./media/network-watcher-monitor-with-azure-automation/figure6.png
[7]: ./media/network-watcher-monitor-with-azure-automation/figure7.png
[8]: ./media/network-watcher-monitor-with-azure-automation/figure8.png
[9]: ./media/network-watcher-monitor-with-azure-automation/figure9.png
[10]: ./media/network-watcher-monitor-with-azure-automation/figure10.png