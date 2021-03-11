---
title: Log Analytics virtuális gépi bővítmény Windowshoz
description: Telepítse a Log Analytics Agent ügynököt a Windows rendszerű virtuális gépen a virtuálisgép-bővítmény használatával.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 06/26/2020
ms.openlocfilehash: 7757bd765bcb02782b6199f71c4a6e460b7b8143
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102559018"
---
# <a name="log-analytics-virtual-machine-extension-for-windows"></a>Log Analytics virtuális gépi bővítmény Windowshoz

Azure Monitor naplók a Felhőbeli és a helyszíni eszközök figyelési lehetőségeit biztosítják. A Windows rendszerhez készült Log Analytics Agent virtuálisgép-bővítményt a Microsoft közzétette és támogatja. A bővítmény telepíti a Log Analytics ügynököt az Azure Virtual Machines szolgáltatásban, és egy meglévő Log Analytics-munkaterületre regisztrálja a virtuális gépeket. Ez a dokumentum a Windows rendszerhez készült Log Analytics virtuálisgép-bővítmény támogatott platformait, konfigurációit és központi telepítési lehetőségeit részletezi.

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

A támogatott Windows operációs rendszerekkel kapcsolatos részletekért tekintse meg az [Azure monitor ügynökök áttekintése](../../azure-monitor/agents/agents-overview.md#supported-operating-systems) című cikket.

### <a name="agent-and-vm-extension-version"></a>Ügynök és virtuálisgép-bővítmény verziója
Az alábbi táblázat a Windows Log Analytics virtuálisgép-bővítmény verziójának és Log Analytics ügynök csomagjának leképezését tartalmazza minden egyes kiadáshoz. 

| Log Analytics Windows-ügynök csomagjának verziója | Log Analytics Windowsos virtuálisgép-bővítmény verziója | Kiadás dátuma | Release Notes (Kibocsátási megjegyzések) |
|--------------------------------|--------------------------|--------------------------|--------------------------|
| 10.20.18053| 1.0.18053.0 | 2020. október   | <ul><li>Új ügynök-hibakereső</li><li>Frissítések arról, hogy az ügynök hogyan kezeli a tanúsítványok változásait az Azure-szolgáltatásokban</li></ul> |
| 10.20.18040 | 1.0.18040.2 | 2020. augusztus   | <ul><li>Elhárít egy problémát az Azure arc-ban</li></ul> |
| 10.20.18038 | 1.0.18038 | 2020. április   | <ul><li>Privát kapcsolaton keresztüli kapcsolat engedélyezése Azure Monitor privát hivatkozás hatókörök használatával</li><li>Betöltési szabályozást tesz elérhetővé, hogy elkerülje a munkaterületek hirtelen, véletlen beáramlását</li><li>További Azure Government felhők és régiók támogatása</li><li>Feloldja a hibát, ha HealthService.exe összeomlott</li></ul> |
| 10.20.18029 | 1.0.18029 | 2020. március   | <ul><li>Az SHA-2 kód aláírásának támogatása</li><li>Javítja a virtuálisgép-bővítmények telepítését és felügyeletét</li><li>Elhárít egy hibát az Azure arc-kiszolgálók integrációjában</li><li>Beépített hibaelhárítási eszközt biztosít az ügyfélszolgálathoz</li><li>További Azure Government régiók támogatása</li> |
| 10.20.18018 | 1.0.18018 | 2019. október | <ul><li> Kisebb hibajavítások és stabilizáció-javítások </li></ul> |
| 10.20.18011 | 1.0.18011 | 2019. július | <ul><li> Kisebb hibajavítások és stabilizáció-javítások </li><li> Megnövekedett MaxExpressionDepth – 10000 </li></ul> |
| 10.20.18001 | 1.0.18001 | 2019. június | <ul><li> Kisebb hibajavítások és stabilizáció-javítások </li><li> Lehetővé tette az alapértelmezett hitelesítő adatok letiltását proxy-kapcsolatok létrehozásakor (WINHTTP_AUTOLOGON_SECURITY_LEVEL_HIGH támogatása) </li></ul>|
| 10.19.13515 | 1.0.13515 | 2019. március | <ul><li>Kisebb stabilizációs javítások </li></ul> |
| 10.19.10006 | n.a. | Dec 2018 | <ul><li> Kisebb stabilizációs javítások </li></ul> | 
| 8.0.11136 | n.a. | Szeptember 2018 |  <ul><li> Az erőforrás-azonosító változásának észlelése a virtuális gépek áthelyezésének támogatásával </li><li> Jelentéskészítési erőforrás-azonosító támogatása a nem bővítmények telepítésének használatakor </li></ul>| 
| 8.0.11103 | n.a. |  2018. április | |
| 8.0.11081 | 1.0.11081 | November 2017 | | 
| 8.0.11072 | 1.0.11072 | Szeptember 2017 | |
| 8.0.11049 | 1.0.11049 | Feb 2017 | |


### <a name="azure-security-center"></a>Azure Security Center

Azure Security Center automatikusan kiépíti a Log Analytics ügynököt, és az Azure-előfizetés alapértelmezett Log Analytics munkaterületével csatlakoztatja. Ha Azure Security Center használ, ne futtassa a jelen dokumentumban ismertetett lépéseket. Ezzel felülírja a konfigurált munkaterületet, és megszakítja a kapcsolódást a Azure Security Center.

### <a name="internet-connectivity"></a>Internetkapcsolat
A Windowshoz készült Log Analytics-ügynök bővítmény megköveteli, hogy a célként megadott virtuális gép csatlakoztatva legyen az internethez. 

## <a name="extension-schema"></a>Bővítményséma

A következő JSON a Log Analytics ügynök bővítmény sémáját jeleníti meg. A kiterjesztéshez a munkaterület-azonosító és a munkaterület kulcsa szükséges a cél Log Analytics munkaterületről. Ezek a Azure Portal munkaterületének beállításaiban találhatók. Mivel a munkaterület kulcsát bizalmas adatokként kell kezelni, a védett beállítási konfigurációban kell tárolni. Az Azure virtuálisgép-bővítmény védett beállítási adatbeállításai titkosítottak, és csak a célként megadott virtuális gépen lettek visszafejtve. Vegye figyelembe, hogy a **munkaterület azonosítója** és a **workspaceKey** a kis-és nagybetűk megkülönböztetése.

```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```
### <a name="property-values"></a>Tulajdonságértékek

| Name | Érték/példa |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| közzétevő | Microsoft. EnterpriseCloud. monitoring |
| típus | MicrosoftMonitoringAgent |
| typeHandlerVersion | 1.0 |
| Munkaterület azonosítója (például) * | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (például) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI + rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ = = |

\* A munkaterület azonosítója neve consumerId a Log Analytics API-ban.

> [!NOTE]
> További tulajdonságok: Azure [-beli Windows-számítógépek Összekapcsolásának Azure monitor](../../azure-monitor/agents/agent-windows.md).

## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure virtuálisgép-bővítmények Azure Resource Manager-sablonokkal is üzembe helyezhetők. Az előző szakaszban részletezett JSON-séma használható Azure Resource Manager sablonban az Log Analytics Agent bővítmény futtatásához Azure Resource Manager sablon központi telepítésekor. A Log Analytics Agent virtuálisgép-bővítményt tartalmazó minta sablon az [Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-windows-vm)rövid útmutatójában található. 

>[!NOTE]
>A sablon nem támogatja több munkaterület-azonosító és munkaterület-kulcs megadását, ha úgy szeretné konfigurálni az ügynököt, hogy több munkaterületre is jelentsen. Ha úgy szeretné beállítani az ügynököt, hogy több munkaterületnek jelentsen, tekintse meg a [munkaterület hozzáadása vagy eltávolítása](../../azure-monitor/agents/agent-manage.md#adding-or-removing-a-workspace)című témakört.  

A virtuálisgép-bővítmények JSON-je beágyazható a virtuális gép erőforrásaiba, vagy egy Resource Manager JSON-sablon legfelső szintű vagy legfelső szintjén helyezhető el. A JSON elhelyezése hatással van az erőforrás nevének és típusának értékére. További információ: [a gyermek erőforrások nevének és típusának beállítása](../../azure-resource-manager/templates/child-resource-name-type.md). 

Az alábbi példa azt feltételezi, hogy a Log Analytics bővítmény a virtuális gép erőforrásán belül van beágyazva. A bővítmény erőforrásának beágyazásakor a JSON a `"resources": []` virtuális gép objektumára kerül.


```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

Ha a bővítmény JSON-fájlját a sablon gyökerébe helyezi, az erőforrás neve tartalmaz egy hivatkozást a szülő virtuális gépre, és a típus a beágyazott konfigurációt tükrözi. 

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

## <a name="powershell-deployment"></a>A PowerShell telepítése

A `Set-AzVMExtension` parancs használatával telepítheti a log Analytics Agent virtuálisgép-bővítményt egy meglévő virtuális gépre. A parancs futtatása előtt a nyilvános és a privát konfigurációkat egy PowerShell-kivonatoló táblában kell tárolni. 

```powershell
$PublicSettings = @{"workspaceId" = "myWorkspaceId"}
$ProtectedSettings = @{"workspaceKey" = "myWorkspaceKey"}

Set-AzVMExtension -ExtensionName "MicrosoftMonitoringAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
    -ExtensionType "MicrosoftMonitoringAgent" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS 
```

## <a name="troubleshoot-and-support"></a>Hibakeresés és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

A bővítmények állapotával kapcsolatos adatok beolvashatók a Azure Portalból, és az Azure PowerShell modul használatával. Egy adott virtuális gép bővítményeinek telepítési állapotának megtekintéséhez futtassa az alábbi parancsot a Azure PowerShell modul használatával.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

A bővítmény-végrehajtás kimenete a következő könyvtárban található fájlokra van naplózva:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\
```

### <a name="support"></a>Támogatás

Ha a cikk bármely pontján további segítségre van szüksége, vegye fel a kapcsolatot az Azure-szakértőkkel az [MSDN Azure-ban, és stack overflow fórumokat](https://azure.microsoft.com/support/forums/)is. Másik lehetőségként egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a támogatás kérése lehetőséget. További információ az Azure-támogatás használatáról: [Microsoft Azure támogatással kapcsolatos gyakori kérdések](https://azure.microsoft.com/support/faq/).
