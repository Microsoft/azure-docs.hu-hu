---
title: Áttelepítés kapcsolatfigyelő kapcsolatfigyelő
titleSuffix: Azure Network Watcher
description: Útmutató a kapcsolatfigyelő való kapcsolatfigyelő.
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
ms.openlocfilehash: fc5bcc7f0cd11160b33bb6501526fce9f29d710b
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366385"
---
# <a name="migrate-to-connection-monitor-from-connection-monitor-classic"></a>Áttelepítés kapcsolatfigyelő kapcsolatfigyelő (klasszikus)

> [!IMPORTANT]
> 2021. július 1-től kezdve nem fog tudni új kapcsolatfigyelőket hozzáadni a kapcsolatfigyelő -ben (klasszikus), de továbbra is használhatja a 2021. július 1. előtt létrehozott meglévő kapcsolatfigyelőket. A jelenlegi számítási feladatok szolgáltatáskimaradásának minimalizálása érdekében 2024. február 29., kapcsolatfigyelő [(klasszikus)](migrate-to-connection-monitor-from-connection-monitor-classic.md)  áttelepítése az Azure kapcsolatfigyelő új Network Watcher szolgáltatásba.

A meglévő kapcsolatfigyelőket új, továbbfejlesztett kapcsolatfigyelő át néhány kattintással, állásidő nélkül. További információ az előnyökről: [kapcsolatfigyelő.](./connection-monitor-overview.md)

## <a name="key-points-to-note"></a>Fontos pontok

A migrálás a következő eredményeket teszi lehetővé:

* Az ügynökök és a tűzfalbeállítások a rendszertől függően működnek. Nincs szükség módosításra. 
* A meglévő kapcsolatfigyelők tesztformátumban kapcsolatfigyelő > tesztcsoporthoz > vannak leképezve. A Szerkesztés **lehetőség** kiválasztásával megtekintheti és módosíthatja az új kapcsolatfigyelő tulajdonságait, letölthet egy sablont a kapcsolatfigyelő módosításához, majd elküldheti Azure Resource Manager. 
* A Network Watcher azure-beli virtuális gépek adatokat küldenek a munkaterületre és a metrikákra is. kapcsolatfigyelő a régi metrikák (ProbesFailedPercent és AverageRoundtripMs) helyett az új metrikákon (ChecksFailedPercent és RoundTripTimeMs) keresztül teszi elérhetővé az adatokat. A régi metrikák új metrikákba lesznek migrálva a ProbesFailedPercent -> ChecksFailedPercent és az AverageRoundtripMs -> RoundTripTimeMs.
* Adatfigyelés:
   * **Riasztások:** Automatikusan áttelepítve az új metrikákra.
   * **Irányítópultok és integrációk:** A metrikakészlet manuális szerkesztését igényli. 
    
## <a name="prerequisites"></a>Előfeltételek

1. Ha egyéni munkaterületet használ, győződjön meg arról, hogy a Network Watcher engedélyezve van az előfizetésében és a Log Analytics-munkaterület régiójában. Ha nem jelenik meg, a következő hibaüzenet jelenik meg: "Az áttelepítés előtt engedélyezze a Network Watcher bővítményt a kiválasztott előfizetésben és a kiválasztott LA-munkaterületen."
1. Ha a kapcsolatfigyelőben (klasszikus) forrásként használt virtuális gépeken már nincs engedélyezve a Network Watcher-bővítmény, a következő hibaüzenet jelenik meg: "A következő teszteket követő kapcsolatfigyelőket nem lehet importálni, mivel egy vagy több Azure-beli virtuális gépen nincs telepítve a Network Watcher bővítmény. Telepítse a Network Watcher bővítményt, és kattintson a frissítés gombra az importálásukhoz."



## <a name="migrate-the-connection-monitors"></a>A kapcsolatfigyelők áttelepítése

1. A régebbi kapcsolatfigyelőknek az újabbra való áttelepítéshez válassza a kapcsolatfigyelő **lehetőséget,** majd válassza a **Kapcsolatfigyelők áttelepítése lehetőséget.**

    ![A kapcsolatfigyelők áttelepítését a kapcsolatfigyelő.](./media/connection-monitor-2-preview/migrate-cm-to-cm-preview.png)
    
1. Válassza ki az előfizetését és az átemelni kívánt kapcsolatfigyelőket, majd válassza a **Kiválasztott áttelepítés lehetőséget.** 

Mindössze néhány kattintással áttelepíti a meglévő kapcsolatfigyelőket a kapcsolatfigyelő. Miután migrálta a cm-ről (klasszikus) a CM-re, nem fogja látni a figyelőt a CM (klasszikus) alatt

Mostantól testre szabhatja kapcsolatfigyelő tulajdonságait, módosíthatja az alapértelmezett munkaterületet, letöltheti a sablonokat, és ellenőrizheti az áttelepítés állapotát. 

A migrálás megkezdése után a következő módosításokra kerül sor: 
* A Azure Resource Manager erőforrás az újabb kapcsolatfigyelőre változik.
    * A kapcsolatfigyelő neve, régiója és előfizetése változatlan marad. Az erőforrás-azonosítót ez nem befolyásolja.
    * Ha a kapcsolatfigyelő nincs testre szabva, a rendszer létrehoz egy alapértelmezett Log Analytics-munkaterületet az előfizetésben és a kapcsolatfigyelő régiójában. Ezen a munkaterületen vannak tárolva a figyelési adatok. A teszteredmények adatait a rendszer a metrikákban is tárolja.
    * Minden teszt egy *defaultTestGroup nevű tesztcsoportba lesz migrálva.*
    * A forrás- és célvégpontokat a rendszer létrehoz és használ az új tesztcsoportban. Az alapértelmezett nevek a *defaultSourceEndpoint* és *a defaultDestinationEndpoint.*
    * A célport és az tesztelési időköz egy *defaultTestConfiguration nevű tesztkonfigurációba lesz áthelyezve.* A protokoll a portértékek alapján van beállítva. A sikeresség küszöbértékei és az egyéb választható tulajdonságok üresen maradnak.
* A rendszer metrikákra vonatkozó riasztásokat migrál kapcsolatfigyelő metrikák riasztásaiba. A metrikák eltérőek, ezért változik. További információ: Hálózati kapcsolatok [monitorozása a kapcsolatfigyelő.](./connection-monitor-overview.md#metrics-in-azure-monitor)
* Az áttelepített kapcsolatfigyelők már nem jelennek meg régebbi kapcsolatfigyelő megoldásként. Most már csak a szolgáltatásban használhatók kapcsolatfigyelő.
* Minden külső integrációt, például az Power BI és a Grafana irányítópultját, valamint a Biztonsági információ- és eseménykezelési (SIEM) rendszerekkel való integrációt manuálisan kell áttelepíteni. Ez az egyetlen manuális lépés, amely a beállítás áttelepítése során szükséges.

## <a name="next-steps"></a>Következő lépések

További információ a kapcsolatfigyelő:
* [Áttelepítés Network Performance Monitor kapcsolatfigyelő](./migrate-to-connection-monitor-from-network-performance-monitor.md)
* [Hozzon kapcsolatfigyelő a következővel: Azure Portal](./connection-monitor-create-using-portal.md)
