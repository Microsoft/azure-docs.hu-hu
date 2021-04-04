---
title: 'Azure VPN Gateway: riasztások konfigurálása diagnosztikai erőforrás-naplózási eseményekhez'
description: Megtudhatja, hogyan állíthatja be a riasztásokat az Azure VPN Gateway erőforrás-naplózási eseményei alapján Azure Monitor Log Analytics használatával.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/18/2020
ms.author: alzam
ms.openlocfilehash: 70cac6ef566ef52409cd9667708c2fc297e046f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97656614"
---
# <a name="set-up-alerts-on-resource-log-events-from-vpn-gateway"></a>Riasztások beállítása az erőforrás-naplózási eseményekről VPN Gateway

Ebből a cikkből megtudhatja, hogyan állíthatja be a riasztásokat az Azure VPN Gateway erőforrás-naplózási eseményei alapján Azure Monitor Log Analytics használatával. 

A következő erőforrás-naplók érhetők el az Azure-ban:

|***Név** _ | _ *_Leírás_** |
|--- | --- |
|GatewayDiagnosticLog | Erőforrás-naplókat tartalmaz az átjáró konfigurációs eseményeihez, az elsődleges módosításokhoz és a karbantartási eseményekhez. |
|TunnelDiagnosticLog | Bújtatási állapot változási eseményeit tartalmazza. Az alagút kapcsolódási/leválasztási eseményeinek összefoglaló oka van az állapot változásához, ha van ilyen. |
|RouteDiagnosticLog | A statikus útvonalak és az átjárón előforduló BGP-események változásainak naplózása |
|IKEDiagnosticLog | Az átjárón lévő IKE-vezérlési üzenetek és események naplózása |
|P2SDiagnosticLog | A pont – hely típusú vezérlő üzeneteinek és eseményeinek naplózása az átjárón. A kapcsolati forrás adatai csak a IKEv2 és az OpenVPN-kapcsolatokhoz vannak megadva |

## <a name="set-up-alerts-in-the-azure-portal"></a><a name="setup"></a>Riasztások beállítása a Azure Portalban

A következő példa egy riasztást hoz létre egy olyan leválasztási eseményhez, amely helyek közötti VPN-alagutat foglal magában:


1. A Azure Portal a **minden szolgáltatás** területen keresse meg a **log Analytics** , és válassza a **log Analytics munkaterületek** lehetőséget.

   ![Log Analytics munkaterületek kijelölésének kiválasztása](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "Létrehozás")

2. Válassza a **Létrehozás** lehetőséget a **log Analytics** oldalon.

   ![Log Analytics lap létrehozás gombbal](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "Válassza ezt:")

3. Válassza az **új létrehozása** lehetőséget, és adja meg a részleteket.

   ![Log Analytics munkaterület létrehozásával kapcsolatos részletek](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "Válassza ezt:")

4. Keresse meg a VPN-átjárót a **figyelő**  >  **diagnosztika beállításai** panelen.

   ![A VPN Gateway diagnosztikai beállításokban való keresésének kiválasztása](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "Válassza ezt:")

5. A diagnosztika bekapcsolásához kattintson duplán az átjáróra, majd válassza a **diagnosztika bekapcsolása** elemet.

   ![A diagnosztika bekapcsolásának kiválasztása](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "Válassza ezt:")

6. Adja meg a részleteket, és győződjön meg arról, hogy a **küldés log Analytics** és **TunnelDiagnosticLog** be van jelölve. Válassza ki a 3. lépésben létrehozott Log Analytics munkaterületet.

   ![Bejelölt jelölőnégyzetek](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "Válassza ezt:")

   > [!NOTE]
   > Az adat kezdeti megjelenítéséhez több óráig is eltarthat.

7. Nyissa meg a virtuális hálózati átjáró erőforrásának áttekintését, és a **figyelés** lapon válassza a **riasztások** lehetőséget. Ezután hozzon létre egy új riasztási szabályt, vagy szerkesszen egy meglévő riasztási szabályt.

   ![Új riasztási szabály létrehozásának kijelölése](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Válassza ezt:")

   ![pont – hely kapcsolat](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Válassza ezt:")
8. Válassza ki a Log Analytics munkaterületet és az erőforrást.

   ![Munkaterület és erőforrás kiválasztása](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "Válassza ezt:")

9. Válassza az **egyéni naplók keresése** lehetőséget a **feltétel hozzáadása** alatt lévő jel logikaként.

   ![Egyéni naplók keresésének kiválasztása](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "Válassza ezt:")

10. Adja meg az alábbi lekérdezést a **Keresési lekérdezés** szövegmezőben. Szükség szerint cserélje le a <> és a TimeGenerated értékeit.

    ```
    AzureDiagnostics
    | where Category == "TunnelDiagnosticLog"
    | where _ResourceId == tolower("<RESOURCEID OF GATEWAY>")
    | where TimeGenerated > ago(5m) 
    | where remoteIP_s == "<REMOTE IP OF TUNNEL>"
    | where status_s == "Disconnected"
    | project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup, _ResourceId 
    | sort by TimeGenerated asc
    ```

    Állítsa 0 értékre a küszöbértéket, és válassza a **kész** lehetőséget.

    ![Lekérdezés megadása és küszöbérték kiválasztása](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "Válassza ezt:")

11. A **szabály létrehozása** lapon válassza az **új létrehozása** lehetőséget a **műveleti csoportok** szakaszban. Adja meg a részleteket, és kattintson **az OK gombra**.

    ![Új műveleti csoport részletei](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "Válassza ezt:")

12. A **szabály létrehozása** lapon adja meg a **műveletek testreszabásának** részleteit, és győződjön meg arról, hogy a megfelelő név jelenik meg a **műveleti csoport neve** szakaszban. A szabály létrehozásához válassza a **riasztási szabály létrehozása** lehetőséget.

    ![Szabály létrehozására szolgáló kiválasztások](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "Válassza ezt:")

## <a name="set-up-alerts-by-using-powershell"></a><a name="setuppowershell"></a>Riasztások beállítása a PowerShell használatával

A következő példa lépésekben riasztást hoz létre egy olyan leválasztási eseményhez, amely helyek közötti VPN-alagutat foglal magában.

1. Log Analytics munkaterület létrehozása:

   ```powershell
   $Location           = 'westus2'
   $ResourceGroupName  = 'TestRG1'
   $Sku                = 'pergb2018'
   $WorkspaceName      = 'LogAnalyticsWS123'

   New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku $Sku -ResourceGroupName $ResourceGroupName
   ```

2. A VPN-átjáró diagnosztika bekapcsolása:

   ```powershell
   $ResourceGroupName  = 'TestRG1'
   $VpnGatewayName     = 'VNet1GW'
   $WorkspaceName      = 'LogAnalyticsWS123'

   $VpnGateway         = Get-AzVirtualNetworkGateway -Name $VpnGatewayName -ResourceGroupName $ResourceGroupName
   $Workspace          = Get-AzOperationalInsightsWorkspace -Name $WorkspaceName -ResourceGroupName $ResourceGroupName

   Set-AzDiagnosticSetting `
       -Name 'VPN tunnel' `
       -ResourceId $VpnGateway.Id `
       -WorkspaceId $Workspace.ResourceId `
       -Enabled $true `
       -Category 'TunnelDiagnosticLog'
   ```

3. Hozzon létre egy műveleti csoportot.

   Ez a kód olyan műveleti csoportot hoz létre, amely e-mailben értesítést küld a riasztás indításakor:

   ```powershell
   $ActionGroupName            = 'EmailAdmins'   # Max. 60 characters long
   $ActionGroupShortName       = 'EmailAdmins'   # Max. 12 characters long
   $ActionGroupReceiverName    = 'My receiver Name'
   $EmailAddress               = 'xyz@contoso.com'
   $ResourceGroupName          = 'TestRG1'

   $ActionGroupReceiver = New-AzActionGroupReceiver -Name $ActionGroupReceiverName -UseCommonAlertSchema -EmailReceiver -EmailAddress $EmailAddress

   Set-AzActionGroup `
      -ResourceGroupName $ResourceGroupName `
      -Name $ActionGroupName `
      -ShortName $ActionGroupShortName `
      -Receiver @($ActionGroupReceiver)
   ```

4. Riasztási szabály létrehozása egyéni napló alapján – keresés:

   ```powershell
   $ActionGroupName    = 'EmailAdmins'
   $EmailSubject       = 'Redmond VPN tunnel is disconnected'
   $Location           = 'westus2'
   $RemoteIp           = '104.42.209.46'
   $ResourceGroupName  = 'TestRG1'
   $VpnGatewayName     = 'VNet1GW'
   $WorkspaceName      = 'LogAnalyticsWS123'

   $VpnGateway         = Get-AzVirtualNetworkGateway -Name $VpnGatewayName -ResourceGroupName $ResourceGroupName
   $Workspace          = Get-AzOperationalInsightsWorkspace -Name $WorkspaceName -ResourceGroupName $ResourceGroupName

   $Query = @"
   AzureDiagnostics |
   where Category == "TunnelDiagnosticLog" |
   where TimeGenerated > ago(5m) |
   where _ResourceId == tolower("$($VpnGateway.id)") |
   where remoteIP_s == "$($RemoteIp)" |
   where status_s == "Disconnected" |
   project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup, _ResourceId |
   sort by TimeGenerated asc
   "@

   $Source             = New-AzScheduledQueryRuleSource -Query $Query -DataSourceId $Workspace.ResourceId
   $Schedule           = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 5 -TimeWindowInMinutes 5
   $TriggerCondition   = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator 'GreaterThan' -Threshold 0

   $ActionGroup        = Get-AzActionGroup -ResourceGroupName $ResourceGroupName -Name $ActionGroupName
   $AznsActionGroup    = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup $ActionGroup.Id -EmailSubject $EmailSubject
   $AlertingAction     = New-AzScheduledQueryRuleAlertingAction -AznsAction $AznsActionGroup -Severity '1' -Trigger $TriggerCondition

   New-AzScheduledQueryRule `
       -ResourceGroupName $ResourceGroupName `
       -Location $Location `
       -Action $AlertingAction `
       -Enabled $true `
       -Description 'The tunnel between Azure and Redmond with IP address 104.42.209.46 is disconnected' `
       -Schedule $Schedule `
       -Source $Source `
       -Name 'The Azure to Redmond tunnel is disconnected'
   ```

## <a name="next-steps"></a>Következő lépések

A riasztások bújtatási metrikákkal való konfigurálásáról lásd: [riasztások beállítása VPN Gateway mérőszámokon](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).
