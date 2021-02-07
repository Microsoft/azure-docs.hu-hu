---
title: Szabványos jelentések és adatforgalmi naplók Azure DDoS Protection
description: Ismerje meg, hogyan konfigurálhatja a jelentéseket és a folyamat naplóit.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/28/2020
ms.author: yitoh
ms.openlocfilehash: 42d4467a6441e517ef3194f701eb47e7e68505b1
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2021
ms.locfileid: "99806307"
---
# <a name="view-and-configure-ddos-diagnostic-logging"></a>DDoS-diagnosztikai naplózás megtekintése és konfigurálása

Azure DDoS Protection a standard részletes támadási elemzéseket és vizualizációkat biztosít a DDoS Attack Analytics használatával. A DDoS-támadásokkal szemben a virtuális hálózatokat védő ügyfelek részletesen megtekinthetik a támadási forgalmat, valamint a támadás enyhítésére tett lépéseket a támadásokkal kapcsolatos kockázatcsökkentő jelentésekben & a kockázatcsökkentő folyamat naplófájljait. A gazdag telemetria Azure Monitoron keresztül érhető el, beleértve a DDoS-támadások időtartama alatt részletes mérőszámokat is. A riasztás konfigurálható a DDoS Protection által elérhetővé tett Azure Monitor metrikák bármelyikéhez. A naplózás az [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md), a splunk (Azure Event Hubs), a OMS log Analytics és az Azure Storage szolgáltatással is integrálható a Azure monitor diagnosztikai felületen keresztül haladó elemzésekhez.

A következő diagnosztikai naplók érhetők el Azure DDoS Protection standardhoz: 

- **DDoSProtectionNotifications**: az értesítések értesítést küldenek arról, ha a nyilvános IP-erőforrás támadás alatt áll, és a támadás mérséklése túl van.
- **DDoSMitigationFlowLogs**: a támadás-elhárítási folyamat naplófájljai lehetővé teszik az eldobott forgalom, a továbbított forgalom és az egyéb érdekes datapoints áttekintését egy aktív DDoS-támadás során közel valós időben. Az adatok állandó streamjét betöltheti az Azure Sentinelbe vagy a harmadik féltől származó SIEM-rendszerbe az Event hub használatával a közel valós idejű monitorozáshoz, a lehetséges műveletek elvégzéséhez és a védelmi műveletek igényének kielégítéséhez.
- **DDoSMitigationReports**: a támadás-elhárítási jelentések a Netflow protokoll azon adatait használják, amelyek összesítése az erőforrás támadásával kapcsolatos részletes információk biztosítására szolgál. Ha a nyilvános IP-cím a támadás alatt áll, a jelentés létrehozása azonnal elindul, amint a megoldás elindul. Az 5 percenként generált növekményes jelentés és a teljes kockázatcsökkentő időszakra vonatkozó utólagos jelentés. Így biztosíthatja, hogy a DDoS-támadás hosszabb ideig továbbra is fennmaradjon, és 5 percenként megtekintheti a kockázatcsökkentő jelentés legfrissebb pillanatképét, valamint egy teljes összegzést, amint a támadás feloldható. 
- **AllMetrics**: a DDoS-támadás időtartama alatt rendelkezésre álló összes lehetséges mérőszámot tartalmazza. 

Ebből az oktatóanyagból az alábbiakat sajátíthatja el:

> [!div class="checklist"]
> * A DDoS diagnosztikai naplók konfigurálása, beleértve az értesítéseket, a kockázatcsökkentő jelentéseket és a kockázatcsökkentő folyamat naplóit. 
> * Diagnosztikai naplózás engedélyezése az összes nyilvános IP-címen egy meghatározott hatókörben.
> * Naplófájlok megtekintése a munkafüzetekben.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.
- Az oktatóanyag lépéseinek elvégzése előtt először létre kell hoznia egy [Azure DDoS standard Protection-csomagot](manage-ddos-protection.md) , és a DDoS Protection szabványnak engedélyezve kell lennie egy virtuális hálózaton.
- A DDoS figyeli a virtuális hálózaton belüli erőforrásokhoz rendelt nyilvános IP-címeket. Ha nem rendelkezik nyilvános IP-címmel rendelkező erőforrásokkal a virtuális hálózaton, először létre kell hoznia egy nyilvános IP-címmel rendelkező erőforrást. Az [Azure-szolgáltatások virtuális hálózata](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) (beleértve az Azure Load balancert, ahol a háttérbeli virtuális gépek a virtuális hálózaton vannak), a Resource Managerrel telepített összes erőforrás nyilvános IP-címét nyomon követheti, kivéve Azure app Service környezetekben. Az oktatóanyag folytatásához gyorsan létrehozhat egy Windows vagy [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [rendszerű](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális gépet.    

## <a name="configure-ddos-diagnostic-logs"></a>DDoS diagnosztikai naplók konfigurálása

Ha automatikusan engedélyezni szeretné a diagnosztikai naplózást az összes nyilvános IP-címen egy adott környezetben, ugorjon a [diagnosztikai naplózás engedélyezése az összes nyilvános IP](#enable-diagnostic-logging-on-all-public-ips)-címen lehetőségre.

1. A portál bal oldalán válassza a **minden szolgáltatás** lehetőséget.
2. A **szűrő** mezőbe írja be a *figyelőt* . Ha a **figyelő** megjelenik az eredmények között, válassza ki.
3. A **Beállítások** területen válassza a **diagnosztikai beállítások** elemet.
4. Válassza ki azt az **előfizetést** és **erőforráscsoportot** , amely tartalmazza a NAPLÓZNI kívánt nyilvános IP-címet.
5. Válassza ki a **nyilvános IP-címet** az **erőforrástípus mezőben**, majd válassza ki azt az adott nyilvános IP-címet, amelyen engedélyezni szeretné a naplókat.
6. Válassza a **Diagnosztikai beállítások megadása** lehetőséget. A **Kategória részletei** területen válassza ki a kívánt beállítások közül az alábbi lehetőségek közül, majd kattintson a **Mentés** gombra.

    ![DDoS diagnosztikai beállítások](./media/ddos-attack-telemetry/ddos-diagnostic-settings.png)

7. A **célhely részletei** területen válassza ki a következő lehetőségek közül a kívántat:

    - **Archiválás egy Storage-fiókba**: az adatok egy Azure Storage-fiókba íródnak. Ha többet szeretne megtudni erről a lehetőségről, olvassa el az [erőforrás-naplók archiválása](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage)című témakört.
    - **Stream az Event hub**-ba: lehetővé teszi, hogy a naplók egy Azure Event hub használatával vegyenek fel naplókat. Az Event hubok lehetővé teszik az integrációt a splunk vagy más SIEM-rendszerekkel. Ha többet szeretne megtudni erről a lehetőségről, tekintse meg a [stream-erőforrás naplófájljait az Event hub](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs)-ban.
    - **Küldés log Analyticsba**: a naplók beírása a Azure monitor szolgáltatásba. Ha többet szeretne megtudni erről a lehetőségről, tekintse meg a [naplók összegyűjtése Azure monitor naplókban való használatát](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-log-analytics-workspace)ismertető témakört.

### <a name="log-schemas"></a>Naplózási sémák

A következő táblázat a mezőneveket és a leírásokat tartalmazza:

# <a name="ddosprotectionnotifications"></a>[DDoSProtectionNotifications](#tab/DDoSProtectionNotifications)

| Mező neve | Description |
| --- | --- |
| **TimeGenerated** | Az értesítés létrehozásának dátuma és időpontja (UTC). |
| **ResourceId** | A nyilvános IP-cím erőforrás-azonosítója. |
| **Kategória** | Az értesítések esetében ez a következő lesz: `DDoSProtectionNotifications` .|
| **ResourceGroup** | A nyilvános IP-címet és a virtuális hálózatot tartalmazó erőforráscsoport. |
| **SubscriptionId** | A DDoS Protection-csomag előfizetés-azonosítója. |
| **Erőforrás** | A nyilvános IP-cím neve. |
| **ResourceType** | Ez mindig a következő lesz: `PUBLICIPADDRESS` . |
| **OperationName** | Az értesítések esetében ez a következő lesz: `DDoSProtectionNotifications` .  |
| **Üzenet** | A támadás részletei. |
| **Típus** | Az értesítés típusa. A lehetséges értékek a következők: `MitigationStarted` . `MitigationStopped`. |
| **PublicIpAddress** | A nyilvános IP-címe. |

# <a name="ddosmitigationflowlogs"></a>[DDoSMitigationFlowLogs](#tab/DDoSMitigationFlowLogs)

| Mező neve | Description |
| --- | --- |
| **TimeGenerated** | A folyamat naplójának létrehozásakor megadott dátum és idő (UTC). |
| **ResourceId** | A nyilvános IP-cím erőforrás-azonosítója. |
| **Kategória** | A flow-naplók esetében ez a következő lesz: `DDoSMitigationFlowLogs` .|
| **ResourceGroup** | A nyilvános IP-címet és a virtuális hálózatot tartalmazó erőforráscsoport. |
| **SubscriptionId** | A DDoS Protection-csomag előfizetés-azonosítója. |
| **Erőforrás** | A nyilvános IP-cím neve. |
| **ResourceType** | Ez mindig a következő lesz: `PUBLICIPADDRESS` . |
| **OperationName** | A flow-naplók esetében ez a következő lesz: `DDoSMitigationFlowLogs` . |
| **Üzenet** | A támadás részletei. |
| **SourcePublicIpAddress** | Az ügyfél nyilvános IP-címe, amely a nyilvános IP-címére irányuló forgalmat generál. |
| **SourcePort** | A portszám 0-tól 65535-ig terjed. |
| **DestPublicIpAddress** | A nyilvános IP-címe. |
| **DestPort** | A portszám 0-tól 65535-ig terjed. |
| **Protokoll** | A protokoll típusa. A lehetséges értékek a következők:, `tcp` `udp` , `other` .|

# <a name="ddosmitigationreports"></a>[DDoSMitigationReports](#tab/DDoSMitigationReports)

| Mező neve | Description |
| --- | --- |
| **TimeGenerated** | A jelentés létrehozásának dátuma és időpontja (UTC). |
| **ResourceId** | A nyilvános IP-cím erőforrás-azonosítója. |
| **Kategória** | Az értesítések esetében ez a következő lesz: `DDoSProtectionNotifications` .|
| **ResourceGroup** | A nyilvános IP-címet és a virtuális hálózatot tartalmazó erőforráscsoport. |
| **SubscriptionId** | A DDoS Protection-csomag előfizetés-azonosítója. |
| **Erőforrás** | A nyilvános IP-cím neve. |
| **ResourceType** | Ez mindig a következő lesz: `PUBLICIPADDRESS` . |
| **OperationName** | A kockázatcsökkentő jelentések esetében ez a következő lesz: `DDoSMitigationReports` . |
| **ReportType** | A lehetséges értékek `Incremental` a következők: `PostMitigation` .|
| **MitigationPeriodStart** | A mérséklés megkezdésének dátuma és időpontja (UTC).  |
| **MitigationPeriodEnd** | A mérséklés befejezésének dátuma és időpontja (UTC). |
| **IPAddress** | A nyilvános IP-címe. |
| **AttackVectors** |  A támadási típusok részletezése. Kulcsok a következők:,,, `TCP SYN flood` `TCP flood` `UDP flood` `UDP reflection` `Other packet flood` .|
| **TrafficOverview** |  A támadási forgalom részletezése. A kulcsok a következők:,,,,,, `Total packets` `Total packets dropped` `Total TCP packets` `Total TCP packets dropped` `Total UDP packets` `Total UDP packets dropped` `Total Other packets` , `Total Other packets dropped` . |
| **Protokollok** | Az érintett protokollok részletezése. A kulcsok a következők:, `TCP` `UDP` `Other` . |
| **DropReasons** | Az eldobott csomagok okainak részletezése. Kulcsok a következők:,,,,,,,, `Protocol violation invalid TCP syn` `Protocol violation invalid TCP` `Protocol violation invalid UDP` `UDP reflection` `TCP rate limit exceeded` `UDP rate limit exceeded` `Destination limit exceeded` `Other packet flood` `Rate limit exceeded` , `Packet was forwarded to service` . |
| **TopSourceCountries** | A bejövő forgalom első 10 forrásoldali országának részletezése. |
| **TopSourceCountriesForDroppedPackets** | A támadással nem rendelkező, 10 legjobb forrású országok részletezése. |
| **TopSourceASNs** | A bejövő forgalom legfelső 10 forrású autonóm rendszerszámának (ASN) részletezése.  |
| **SourceContinents** | A bejövő forgalom forrásként szolgáló kontinensének bontása. |
***

## <a name="enable-diagnostic-logging-on-all-public-ips"></a>Diagnosztikai naplózás engedélyezése az összes nyilvános IP-címen

Ez a [sablon](https://aka.ms/ddosdiaglogs) egy Azure Policy definíciót hoz létre, amellyel automatikusan engedélyezheti a diagnosztikai naplózást az összes nyilvános IP-naplóban egy meghatározott hatókörben.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FEnable%2520Diagnostic%2520Logging%2FAzure%2520Policy%2FDDoSLogs.json)

## <a name="view-log-data-in-workbooks"></a>Naplófájlok megtekintése a munkafüzetekben

### <a name="azure-sentinel-data-connector"></a>Az Azure Sentinel adatösszekötője

A naplókat összekapcsolhatja az Azure Sentinel szolgáltatással, megtekintheti és elemezheti az adatait a munkafüzetekben, egyéni riasztásokat hozhat létre, és beépítheti azokat a vizsgálati folyamatba. Az Azure Sentinelhez való kapcsolódáshoz lásd: [Kapcsolódás az Azure sentinelhez](../sentinel/connect-azure-ddos-protection.md). 

![Azure Sentinel DDoS-összekötő](./media/ddos-attack-telemetry/azure-sentinel-ddos.png)

### <a name="azure-ddos-protection-workbook"></a>Azure DDoS Protection munkafüzet

[Ez a Azure Resource Manager (ARM) sablon](https://aka.ms/ddosworkbook) használatával helyezhet üzembe egy támadási elemzési munkafüzetet. Ez a munkafüzet lehetővé teszi a támadási adatgyűjtés több szűrhető panelen történő megjelenítését, így könnyen megismerheti, hogy mi a tét. 

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FAzure%2520DDoS%2520Protection%2520Workbook%2FAzureDDoSWorkbook_ARM.json)

![DDoS Protection munkafüzet](./media/ddos-attack-telemetry/ddos-attack-analytics-workbook.png)

## <a name="validate-and-test"></a>Ellenőrzés és tesztelés

A DDOS-támadások szimulálása a naplók ellenőrzéséhez: a [DDoS-észlelés ellenőrzése](test-through-simulations.md).

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

- A DDoS diagnosztikai naplók konfigurálása, beleértve az értesítéseket, a kockázatcsökkentő jelentéseket és a kockázatcsökkentő folyamat naplóit. 
- Diagnosztikai naplózás engedélyezése az összes nyilvános IP-címen egy meghatározott hatókörben.
- Naplófájlok megtekintése a munkafüzetekben.

A támadási riasztások konfigurálásának megismeréséhez folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [DDoS Protection-riasztások megtekintése és konfigurálása](alerts.md)
