---
title: Log Analytics virtuális gépi bővítmény Linuxhoz
description: Telepítse a Log Analytics-ügynököt a Linux rendszerű virtuális gépen a virtuálisgép-bővítmény használatával.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 02/18/2020
ms.openlocfilehash: 3ac6937d83bd2d21fefc09878408a54aa0eb41f1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102559086"
---
# <a name="log-analytics-virtual-machine-extension-for-linux"></a>Log Analytics virtuális gépi bővítmény Linuxhoz

## <a name="overview"></a>Áttekintés

Azure Monitor naplók a Felhőbeli és a helyszíni eszközökön végzett figyelési, riasztási és riasztási lehetőségeket biztosítanak. A Linux rendszerhez készült Log Analytics virtuálisgép-bővítményt a Microsoft közzétette és támogatja. A bővítmény telepíti a Log Analytics ügynököt az Azure Virtual Machines szolgáltatásban, és egy meglévő Log Analytics-munkaterületre regisztrálja a virtuális gépeket. Ez a dokumentum részletesen ismerteti a Linux rendszerhez készült Log Analytics virtuálisgép-bővítmény támogatott platformokat, konfigurációkat és telepítési lehetőségeit.

>[!NOTE]
>A Microsoft Operations Management Suite (OMS) és a Azure Monitor közötti folyamatos áttérés részeként a Windows vagy Linux rendszerhez készült OMS-ügynök a Windows rendszerhez és a Linux Log Analytics-ügynökhöz Log Analytics ügynökként fog hivatkozni.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

A támogatott Linux-disztribúciókkal kapcsolatos részletekért tekintse meg az [Azure monitor ügynökök áttekintése](../../azure-monitor/agents/agents-overview.md#supported-operating-systems) című cikket.

### <a name="agent-and-vm-extension-version"></a>Ügynök és virtuálisgép-bővítmény verziója
Az alábbi táblázat a Log Analytics virtuálisgép-bővítmény és a Log Analytics Agent Bundle verziójának leképezését tartalmazza minden kiadáshoz. A Log Analytics Agent csomag verziójának kibocsátási megjegyzésekre mutató hivatkozás is szerepel. A kibocsátási megjegyzések tartalmazzák az adott ügynök kiadásával kapcsolatos hibajavítások és új funkciók részleteit.  

| Log Analytics linuxos virtuálisgép-bővítmény verziója | Log Analytics ügynök csomagjának verziója | 
|--------------------------------|--------------------------|
| 1.13.33 | [1.13.33](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.13.33-0) |
| 1.13.27 | [1.13.27](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.13.27-0) |
| 1.13.15 | [1.13.9 – 0](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.13.9-0) |
| 1.12.25 | [1.12.15 – 0](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.12.15-0) |
| 1.11.15 | [1.11.0 – 9](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.11.0-9) |
| 1.10.0 | [1.10.0 – 1](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.10.0-1) |
| 1.9.1 | [1.9.0 – 0](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.9.0-0) |
| 1.8.11 | [1.8.1 – 256](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.8.1.256)| 
| 1.8.0 | [1.8.0 – 256](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/1.8.0-256)| 
| 1.7.9 | [1.6.1 – 3](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.6.1.3)| 
| 1.6.42.0 | [1.6.0 – 42](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.6.0-42)| 
| 1.4.60.2 | [1.4.4 – 210](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.4-210)| 
| 1.4.59.1 | [1.4.3 – 174](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.3-174)|
| 1.4.58.7 | [14.2 – 125](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.2-125)|
| 1.4.56.5 | [1.4.2 – 124](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.2-124)|
| 1.4.55.4 | [1.4.1 – 123](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.1-123)|
| 1.4.45.3 | [1.4.1 – 45](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.1-45)|
| 1.4.45.2 | [1.4.0 – 45](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.0-45)|
| 1.3.127.5 | [1.3.5 – 127](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201705-v1.3.5-127)| 
| 1.3.127.7 | [1.3.5 – 127](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201705-v1.3.5-127)|
| 1.3.18.7 | [1.3.4 – 15](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201704-v1.3.4-15)|  

### <a name="azure-security-center"></a>Azure Security Center

A Azure Security Center automatikusan kiépíti a Log Analytics ügynököt, és csatlakoztatja azt egy, az Azure-előfizetésben az ASC által létrehozott alapértelmezett Log Analytics munkaterülethez. Ha Azure Security Center használ, ne futtassa a jelen dokumentumban ismertetett lépéseket. Ezzel felülírja a konfigurált munkaterületet, és megszakítja a Azure Security Centerval való kapcsolatfelvételt.

### <a name="internet-connectivity"></a>Internetkapcsolat

A Linux rendszerhez készült Log Analytics-ügynökhöz szükséges, hogy a célként megadott virtuális gép csatlakoztatva legyen az internethez. 

## <a name="extension-schema"></a>Bővítményséma

A következő JSON a Log Analytics ügynök bővítmény sémáját jeleníti meg. A kiterjesztéshez a munkaterület-azonosító és a munkaterület kulcsa szükséges a cél Log Analytics munkaterületről; Ezek az értékek a Azure Portal [log Analytics munkaterületén találhatók](../../azure-monitor/vm/quick-collect-linux-computer.md#obtain-workspace-id-and-key) . Mivel a munkaterület kulcsát bizalmas adatokként kell kezelni, a védett beállítási konfigurációban kell tárolni. Az Azure virtuálisgép-bővítmény védett beállítási adatbeállításai titkosítottak, és csak a célként megadott virtuális gépen lettek visszafejtve. Vegye figyelembe, hogy a **munkaterület azonosítója** és a **workspaceKey** a kis-és nagybetűk megkülönböztetése.

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.13",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

>[!NOTE]
>A fenti séma feltételezi, hogy a sablon legfelső szintjén lesz elhelyezve. Ha a sablonban elhelyezi a virtuális gép erőforrásában, a `type` és a `name` tulajdonságokat módosítani kell, a [lejjebb](#template-deployment)leírtak szerint.

### <a name="property-values"></a>Tulajdonságértékek

| Name | Érték/példa |
| ---- | ---- |
| apiVersion | 2018-06-01 |
| közzétevő | Microsoft. EnterpriseCloud. monitoring |
| típus | OmsAgentForLinux |
| typeHandlerVersion | 1.13 |
| Munkaterület azonosítója (például) | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (például) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI + rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ = = |


## <a name="template-deployment"></a>Sablonalapú telepítés

>[!NOTE]
>A Log Analytics virtuálisgép-bővítmény egyes összetevőit a diagnosztika virtuálisgép- [bővítmény](./diagnostics-linux.md)is tartalmazza. Az architektúra miatt ütközések merülhetnek fel, ha mindkét bővítmény ugyanabban az ARM-sablonban van létrehozva. A telepítési idejű ütközések elkerülése érdekében használja az [ `dependsOn` irányelvet](../../azure-resource-manager/templates/define-resource-dependency.md#dependson) annak biztosítására, hogy a bővítmények egymás után legyenek telepítve. A bővítmények mindkét sorrendben telepíthetők.

Az Azure virtuálisgép-bővítmények Azure Resource Manager-sablonokkal is üzembe helyezhetők. A sablonok ideálisak egy vagy több olyan virtuális gép üzembe helyezéséhez, amely az üzembe helyezés utáni konfigurációt igényli, például Azure Monitor naplók bevezetését. A Log Analytics Agent virtuálisgép-bővítményt tartalmazó példa Resource Manager-sablon az [Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm)rövid útmutatójában található. 

A virtuálisgép-bővítmény JSON-konfigurációja beágyazható a virtuális gép erőforrásaiba, vagy egy Resource Manager JSON-sablon legfelső szintű vagy legfelső szintjén helyezhető el. A JSON-konfiguráció elhelyezése hatással van az erőforrás nevének és típusának értékére. További információ: [a gyermek erőforrások nevének és típusának beállítása](../../azure-resource-manager/templates/child-resource-name-type.md). 

Az alábbi példa azt feltételezi, hogy a virtuálisgép-bővítmény a virtuális gép erőforrásán belül van beágyazva. A bővítmény erőforrásának beágyazásakor a JSON a `"resources": []` virtuális gép objektumára kerül.

```json
{
  "type": "extensions",
  "name": "OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.13",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

Ha a bővítmény JSON-fájlját a sablon gyökerébe helyezi, az erőforrás neve tartalmaz egy hivatkozást a szülő virtuális gépre, és a típus a beágyazott konfigurációt tükrözi.  

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "<parentVmResource>/OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.13",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

## <a name="azure-cli-deployment"></a>Azure CLI üzembe helyezése

Az Azure CLI használatával telepítheti az Log Analytics Agent virtuálisgép-bővítményt egy meglévő virtuális gépre. Cserélje le az alábbi *myWorkspaceKey* értéket a munkaterület kulcsával és a *myWorkspaceId* értékkel a munkaterület-azonosítójával. Ezek az értékek a Azure Portal *Speciális beállítások* területén található log Analytics munkaterületén találhatók. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --protected-settings '{"workspaceKey":"myWorkspaceKey"}' \
  --settings '{"workspaceId":"myWorkspaceId"}'
```

## <a name="troubleshoot-and-support"></a>Hibakeresés és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

A bővítmények állapotával kapcsolatos adatok a Azure Portalból és az Azure CLI használatával kérhetők le. Egy adott virtuális gép bővítményeinek telepítési állapotának megtekintéséhez futtassa az alábbi parancsot az Azure CLI használatával.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

A bővítmény végrehajtásának kimenete a következő fájlba van naplózva:

```
/opt/microsoft/omsagent/bin/stdout
```

### <a name="error-codes-and-their-meanings"></a>Hibakódok és jelentéseik

| Hibakód | Értelmezés | Lehetséges művelet |
| :---: | --- | --- |
| 9 | Az engedélyezés idő előtt megszakadt | [Frissítse az Azure Linux-ügynököt](./update-linux-agent.md) a legújabb elérhető verzióra. |
| 10 | A virtuális gép már csatlakoztatva van egy Log Analytics munkaterülethez | Ha a virtuális gépet a bővítmény sémájában megadott munkaterülethez szeretné kapcsolni, állítsa a stopOnMultipleConnections hamis értékre a nyilvános beállításokban, vagy távolítsa el ezt a tulajdonságot. Ez a virtuális gép minden munkaterülethez, amelyhez kapcsolódik, egyszer kell fizetnie. |
| 11 | Érvénytelen a bővítmény számára megadott konfiguráció | Az előző példákat követve állítsa be a telepítéshez szükséges összes tulajdonság értékét. |
| 17 | Log Analytics csomag telepítési hibája | 
| 19 | A nem-csomag telepítési hibája | 
| 20 | SCX-csomag telepítési hibája |
| 51 | Ez a bővítmény nem támogatott a virtuális gép operációs rendszerében | |
| 52 | Ez a bővítmény hiányzó függőség miatt nem sikerült | A kimenet és a naplók további információkkal szolgálnak arról, hogy mely függőségek hiányoznak. |
| 53 | Ez a bővítmény hiányzó vagy helytelen konfigurációs paraméterek miatt nem sikerült | A hibákkal kapcsolatos további információkért tekintse meg a kimenetet és a naplókat. Emellett ellenőrizze a munkaterület AZONOSÍTÓjának helyességét, és ellenőrizze, hogy a számítógép csatlakozik-e az internethez. |
| 55 | Nem lehet csatlakozni a Azure Monitor szolgáltatáshoz, vagy hiányzik a szükséges csomagok, vagy a dpkg Package Manager zárolva van| Győződjön meg arról, hogy a rendszer rendelkezik internet-hozzáféréssel, vagy hogy érvényes HTTP-proxy van megadva. Emellett ellenőrizze a munkaterület-azonosító helyességét, és ellenőrizze, hogy telepítve vannak-e a curl és a Tar segédprogramok. |

További hibaelhárítási tudnivalókat a [log Analytics-Agent-Linux hibaelhárítási útmutatójában](../../azure-monitor/visualize/vmext-troubleshoot.md)talál.

### <a name="support"></a>Támogatás

Ha a cikk bármely pontján további segítségre van szüksége, vegye fel a kapcsolatot az Azure-szakértőkkel az [MSDN Azure-ban, és stack overflow fórumokat](https://azure.microsoft.com/support/forums/)is. Másik lehetőségként egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a támogatás kérése lehetőséget. További információ az Azure-támogatás használatáról: [Microsoft Azure támogatással kapcsolatos gyakori kérdések](https://azure.microsoft.com/support/faq/).
