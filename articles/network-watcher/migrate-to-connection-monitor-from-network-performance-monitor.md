---
title: Áttelepítés kapcsolatfigyelő Network Performance Monitor
titleSuffix: Azure Network Watcher
description: Útmutató a kapcsolatfigyelő való Network Performance Monitor.
services: network-watcher
documentationcenter: na
author: vinynigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: vinigam
ms.openlocfilehash: be12a9054fd67b243530ff671c10fa53acafc308
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366351"
---
# <a name="migrate-to-connection-monitor-from-network-performance-monitor"></a>Áttelepítés kapcsolatfigyelő Network Performance Monitor

> [!IMPORTANT]
> 2021. július 1-től kezdődően nem adhat hozzá új teszteket egy meglévő munkaterülethez, és nem engedélyezheti a Network Performance Monitor. A 2021. július 1. előtt létrehozott teszteket továbbra is használhatja. A jelenlegi számítási feladatok szolgáltatáskimaradásának minimálisra csökkentése érdekében 2024. február 29. előtt Network Performance Monitor át a teszteket az Azure kapcsolatfigyelő új Network Watcher szolgáltatásba.

A teszteket az Network Performance Monitor (NPM) új, továbbfejlesztett kapcsolatfigyelő egyetlen kattintással és állásidő nélkül is átveheti. További információ az előnyökről: [kapcsolatfigyelő.](./connection-monitor-overview.md)


## <a name="key-points-to-note"></a>Fontos megjegyzések

A migrálás a következő eredményekhez nyújt segítséget:

* A helyszíni ügynökök és a tűzfalbeállítások így működnek. Nincs szükség módosításokra. Az Azure-beli virtuális gépekre telepített Log Analytics-ügynököket a [bővítményre kell Network Watcher lecserélni.](../virtual-machines/extensions/network-watcher-windows.md)
* A meglévő tesztek a tesztcsoport kapcsolatfigyelő > tesztformátum > vannak leképezve. A Szerkesztés **lehetőség** kiválasztásával megtekintheti és módosíthatja az új kapcsolatfigyelő tulajdonságait, letölthet egy sablont a módosításaihoz, és elküldheti a sablont a Azure Resource Manager.
* Az ügynökök a Log Analytics-munkaterületre és a metrikákra is küldenek adatokat.
* Adatfigyelés:
   * **Adatok a Log Analyticsben:** A migrálás előtt az adatok a Munkaterületen maradnak, ahol az NPM konfigurálva van a NetworkMonitoring táblában. A migrálás után az adatok a NetworkMonitoring táblába, az NWConnectionMonitorTestResult táblába és az NWConnectionMonitorPathResult táblába kerülnek ugyanazon a munkaterületen. Miután a tesztek le vannak tiltva az NPM-ben, az adatok csak az NWConnectionMonitorTestResult táblában és az NWConnectionMonitorPathResult táblában vannak tárolva.
   * **Naplóalapú riasztások,** irányítópultok és integrációk: Manuálisan kell szerkesztenie a lekérdezéseket az új NWConnectionMonitorTestResult és NWConnectionMonitorPathResult tábla alapján. A metrikákban a riasztások újra létrehozásához lásd: Hálózati [kapcsolatok monitorozása a kapcsolatfigyelő.](./connection-monitor-overview.md#metrics-in-azure-monitor)
* ExpressRoute-monitorozáshoz:
    * **Végpontok közötti** veszteség és késés: kapcsolatfigyelő teljesítmény, és ez egyszerűbb, mint az NPM, mivel a felhasználóknak nem kell konfigurálni, hogy mely kapcsolathálózatokat és társviszonyokat kell figyelniük. Az elérési úton található kapcsolati körök automatikusan felderítve lesznek, az adatok metrikákban lesznek elérhetők (gyorsabbak, mint la la, ahol az NPM tárolta az eredményeket). A topológia is működik.
    * **Sávszélesség-mérések:** A sávszélességgel kapcsolatos metrikák bevezetésekor az NPM naplóelemzésen alapuló megközelítése nem volt hatékony az ExpressRoute-ügyfelek sávszélesség-figyelésében. Ez a képesség mostantól nem érhető el a kapcsolatfigyelő.
    
## <a name="prerequisites"></a>Előfeltételek

* Győződjön meg Network Watcher, hogy az adatok engedélyezve vannak az előfizetésben és a Log Analytics-munkaterület régiójában. Ha nem tette meg, a következő hibaüzenet jelenik meg: "Mielőtt megkísérli az áttelepítést, engedélyezze a Network Watcher bővítményt a kiválasztott előfizetésben és a kiválasztott LA-munkaterület helyén."
* Ha a Log Analytics-munkaterülettől eltérő régióhoz/előfizetéshez tartozó Azure-beli virtuális gépet használ végpontként, győződjön meg arról, hogy az Network Watcher engedélyezve van az előfizetéshez és a régióhoz.   
* A telepített Log Analytics-ügynökkel rendelkező Azure-beli virtuális gépeket engedélyezni kell a Network Watcher bővítvekkel.

## <a name="migrate-the-tests"></a>A tesztek mi áttelepítése

A teszteket a következő Network Performance Monitor kapcsolatfigyelő át:

1. A Network Watcher válassza a **kapcsolatfigyelő,** majd a **Tesztek áttelepítése az NPM lapról** lehetőséget. 

    :::image type="content" source="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png" alt-text="Tesztek áttelepítése Network Performance Monitor kapcsolatfigyelő" lightbox="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png":::
    
1. A legördülő listában válassza ki az előfizetést és a munkaterületet, majd válassza ki az átemelni kívánt NPM-szolgáltatást. 
1. A **tesztek áttelepítéshez** válassza az Importálás lehetőséget.
* Ha az NPM nincs engedélyezve a munkaterületen, a következő hibaüzenet jelenik meg: "Nem található érvényes NPM-konfiguráció". 
* Ha nincs teszt a 2. lépésben kiválasztott szolgáltatásban, a következő hibaüzenet jelenik meg: "A kiválasztott munkaterület nem rendelkezik <feature> konfigurációval".
* Ha nincsenek érvényes tesztek, a következő hibaüzenet jelenik meg: "A kiválasztott munkaterület nem rendelkezik érvényes tesztekkel"
* Előfordulhat, hogy a tesztek olyan ügynököket tartalmaznak, amelyek már nem aktívak, de korábban már aktívak voltak. A következő hibaüzenet jelenik meg: "Néhány teszt már nem aktív ügynököket tartalmaz. Inaktív ügynökök listája – {0} . Előfordulhat, hogy ezek az ügynökök a múltban futnak, de leállnak/nem futnak tovább. Ügynökök engedélyezése és áttelepítése kapcsolatfigyelő. Kattintson a Folytatás gombra a nem aktív ügynököket nem tartalmazó tesztek áttelepítéshez."

A migrálás megkezdése után a következő módosításokra kerül sor: 
* Létrejön egy új kapcsolatfigyelő erőforrás.
   * Régiónként és előfizetésenként egy kapcsolatfigyelő jön létre. Helyszíni ügynökökkel végzett tesztek esetén az új kapcsolatfigyelő neve a következő: `<workspaceName>_"workspace_region_name"` . Az Azure-ügynökkel végzett tesztek esetén az új kapcsolatfigyelő neve a következő lesz: `<workspaceName>_<Azure_region_name>` .
   * A figyelési adatok most ugyanazon a Log Analytics-munkaterületen vannak tárolva, amelyen az NPM engedélyezve van, az új, NWConnectionMonitorTestResult és NWConnectionMonitorPathResult táblában. 
   * A teszt neve a tesztcsoport neveként lesz továbbítva. A teszt leírása nincs migrálva.
   * A forrás- és célvégpontokat a rendszer létrehoz és használ az új tesztcsoportban. Helyszíni ügynökök esetén a végpontok a következő formátumban vannak formázva: `<workspaceName>_<FQDN of on-premises machine>` . Az ügynök leírása nem lesz migrálva.
   * A célport és az vizsgálati időköz egy és nevű tesztkonfigurációba lesz `TC_<protocol>_<port>` `TC_<protocol>_<port>_AppThresholds` áthelyezve. A protokoll a portértékek alapján van beállítva. Az ICMP esetén a tesztkonfigurációk neve és `TC_<protocol>` `TC_<protocol>_AppThresholds` . Ha a beállítás migrálva van, a sikeresség küszöbértékei és egyéb nem kötelező tulajdonságok üresen maradnak.
   * Ha az áttelepítési tesztek nem futó ügynököket tartalmaznak, akkor engedélyeznie kell az ügynököket, majd újra át kell azt végeznie.
* Az NPM nincs letiltva, így a migrált tesztek továbbra is küldhetnek adatokat a NetworkMonitoring táblába, az NWConnectionMonitorTestResult táblába és az NWConnectionMonitorPathResult táblába. Ez a megközelítés biztosítja, hogy a meglévő naplóalapú riasztásokat és integrációkat ez ne befolyásolja.
* Az újonnan létrehozott kapcsolatfigyelő látható a kapcsolatfigyelő.

A migrálás után győződjön meg arról, hogy:
* Manuálisan tiltsa le a teszteket az NPM-ban. Addig is díjat számítunk fel értük. 
* Az NPM letiltása közben hozza létre újra a riasztásokat az NWConnectionMonitorTestResult és az NWConnectionMonitorPathResult táblákon, vagy használjon metrikákat. 
* Migrálhatja a külső integrációkat az NWConnectionMonitorTestResult és NWConnectionMonitorPathResult táblákba. Külső integráció például az irányítópultok a Power BI és a Grafana szolgáltatásban, valamint a Biztonsági információk és események kezelése (SIEM) rendszerekkel való integráció.


## <a name="next-steps"></a>Következő lépések

További információ a kapcsolatfigyelő:
* [Áttelepítés kapcsolatfigyelő (klasszikus) kapcsolatfigyelő](./migrate-to-connection-monitor-from-connection-monitor-classic.md)
* [Hozzon kapcsolatfigyelő a következővel: Azure Portal](./connection-monitor-create-using-portal.md)
