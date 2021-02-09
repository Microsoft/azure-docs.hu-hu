---
title: Az Azure Network Performance Monitor díjszabása – GYIK | Microsoft Docs
description: Gyakori kérdések – Azure Network Performance Monitor
ms.subservice: logs
ms.topic: conceptual
author: agummadi
ms.author: agummadi
ms.date: 04/02/2018
ms.openlocfilehash: 4b9e8a81095a63c667925f8b51d9f9f2aa081f8d
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99832878"
---
# <a name="pricing-changes-for-azure-network-performance-monitor"></a>Az Azure Network Performance Monitor díjszabásának változásai

> [!IMPORTANT]
> 2021. július 1-től nem lehet új teszteket felvenni egy meglévő munkaterületre, vagy új munkaterületet engedélyezni Network Performance Monitor. Továbbra is használhatja a 2021. július 1. előtt létrehozott teszteket. A szolgáltatás megszakadásának minimalizálásához a jelenlegi számítási [feladatokhoz telepítse át a teszteket Network Performance monitorról az](https://docs.microsoft.com/azure/network-watcher/migrate-to-connection-monitor-from-network-performance-monitor) Azure Network Watcher új, 2024. február 29. előtti kapcsolódási figyelője felé.

Meghallgatta visszajelzését, és a közelmúltban bevezetett egy [új díjszabást](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) az Azure különböző monitorozási szolgáltatásaihoz. Ez a cikk az Azure [Network Performance monitor](../../networking/network-monitoring-overview.md) (NPM) szolgáltatással kapcsolatos árképzési változásokat rögzíti egy könnyen olvasható kérdés-és válasz formátumban.

A Network Performance Monitor három összetevőből áll:
* [Teljesítménymonitorozás](../../networking/network-monitoring-overview.md#performance-monitor)
* [Szolgáltatás végpontjának figyelője](../../networking/network-monitoring-overview.md)
* [ExpressRoute-figyelő](../../networking/network-monitoring-overview.md#expressroute-monitor)

A következő részek a NPM összetevőinek díjszabási változásait ismertetik.

## <a name="performance-monitor"></a>Teljesítménymonitorozás

**Hogyan számlázta a Teljesítményfigyelő a régi modellben?**

A NPM-számlázás a két összetevő használatának és felhasználásának függvénye:
* **Csomópontok**: az összes szintetikus tranzakció származik és leáll a csomópontokon. A csomópontokat ügynöknek vagy Microsoft Management agentnek is nevezzük.
* **Adatmennyiség: a** különböző hálózati tesztek eredményei a log Analytics munkaterületen vannak tárolva.

A régi modellben a számla kiszámítása a csomópontok száma és a generált adatok mennyisége alapján történik. 

**Hogyan számítják fel a Teljesítményfigyelő használatát az új modell alapján?**

A NPM Teljesítményfigyelő funkciója mostantól a következő kombináción alapul: 

* Figyelt alhálózati kapcsolatok
* Adatmennyiség

**Mi az alhálózat-hivatkozás?**

A Teljesítményfigyelő figyeli a hálózat két vagy több helye közötti kapcsolatot. Az egyik alhálózaton lévő csomópontok vagy ügynökök csoportjának, valamint egy másik alhálózat csomópontjainak egy csoportjának a kapcsolatát alhálózati kapcsolatnak nevezik.

**Két alhálózattal rendelkezem (A és B), és az egyes alhálózatokon több ügynök is van. A Teljesítményfigyelő az A alhálózaton lévő összes ügynök kapcsolatát figyeli a B alhálózaton lévő összes ügynökkel. Az alhálózatok közötti kapcsolatok száma alapján kell díjat fizetni?**

Nem. Számlázási célokra az A alhálózatról a B alhálózatra irányuló összes kapcsolat egy alhálózati kapcsolatba van csoportosítva. Egyetlen kapcsolatban számítunk fel díjat. A Teljesítményfigyelő továbbra is figyeli az egyes alhálózatokon lévő különböző ügynökök közötti kapcsolatokat.

**Milyen költségekkel jár az alhálózat kapcsolatának figyelése?**

A teljes hónapra vonatkozó egyetlen alhálózati kapcsolat figyelési díjait a [ping Mesh](https://azure.microsoft.com/pricing/details/network-watcher/) szakaszban találja.

**Mik a Teljesítményfigyelő által generált adatok díjai?**

A betöltési díj (adatfeltöltés Log Analytics munkaterületre Azure Monitor, feldolgozás és indexelés során) a Log Analytics [díjszabási lapján](https://azure.microsoft.com/pricing/details/log-analytics/) érhető el az adatfeldolgozási szakaszban. Az adatmegőrzési díj (vagyis az ügyfél az első hónap után megőrzött adatok) a [díjszabás lapon](https://azure.microsoft.com/pricing/details/log-analytics/)is elérhető az adatmegőrzési szakaszban.


## <a name="expressroute-monitor"></a>ExpressRoute-figyelő

**Milyen díjak vonatkoznak a ExpressRoute-figyelő használatára?**

A ExpressRoute-figyelő díjait a figyelés során generált adatok mennyisége alapján számítjuk fel. További információ: "Mik a Teljesítményfigyelő által generált adatok díjai?"

**ExpressRoute-figyelőt használok több ExpressRoute-áramkör figyelésére. Számítanak fel díjat a figyelt áramkörök száma alapján?**

Nem számítunk fel díjat az áramkörök száma vagy a társítás típusa (például privát partneri kapcsolatok, Microsoft-társ) alapján. Az adatmennyiség alapján számolunk fel díjat, a korábban leírtaknak megfelelően.

**Milyen mennyiségű adatmennyiség keletkezik, amikor a ExpressRoute egyetlen áramkört figyel?**

A havonta generált adatmennyiség, amikor a ExpressRoute a privát társ-létesítési kapcsolatot figyeli, a következő:

|Percentilis      |Adatmennyiség/hónap (MB)|
| :---:          |           ---:|
|50<sup>th</sup> |            192|
|60<sup>th</sup> |            256|
|70<sup>th</sup> |            360|
|80<sup>th</sup> |            498|
|90<sup>th</sup> |            870|
|95<sup>th</sup> |           1560|


A táblázat szerint az 50%-os számú ügyfél 192 MB adatmennyiséget fizet. Az első hónapban $2.30/GB-onként az áramkör figyelésére fordított költségek $0,43 USD (= 192 * 2,30/1024).

**Milyen okai vannak az adatmennyiség változásainak?**

A létrehozott megfigyelési adatok mennyisége több tényezőtől függ, például:
* Ügynökök száma. A hibák elkülönítésének pontossága az ügynökök számának növekedésével növekszik.
* A hálózaton lévő ugrások száma.
* A forrás és a cél közötti elérési utak száma

A magasabb százalékos értékkel rendelkező ügyfelek (az előző táblázatban) általában a helyszíni hálózaton lévő különböző nézőpontokból származó áramköröket figyelik. Több ügynök is mélyebbre kerül a hálózatban, a szolgáltató peremhálózati útválasztóján kívül. Az ügynököket gyakran több felhasználói hely, ág és állvány számára helyezik el az adatközpontokban.

## <a name="service-endpoint-monitor"></a>Szolgáltatás végpontjának figyelője

**Milyen díjak vonatkoznak a Service Endpoint monitor használatára?**

A Service Endpoint monitor használati díjait a következők alapján számítjuk ki:
* Kapcsolatok száma
* Adatmennyiség

**Mi a kapcsolatok?**

A kapcsolat egy végpont (URL-cím vagy hálózati szolgáltatás) egyetlen ügynökből való elérésének tesztelése az egész hónapra. Például a három ügynök bing.com való kapcsolatának figyelése három kapcsolatot jelent.

**Milyen költségekkel jár a szolgáltatás-végpont figyelője?**

A végpontok teljes hónapra történő figyelésének költségeit a [Kapcsolódás figyelése](https://azure.microsoft.com/pricing/details/network-watcher/) című szakaszban találja. Az adatfeltöltés az adatfeldolgozási szakaszban Log Analytics [díjszabási oldalán](https://azure.microsoft.com/pricing/details/log-analytics/) érhető el.

## <a name="references"></a>Hivatkozások

[Log Analytics díjszabás – gyakori](https://azure.microsoft.com/pricing/details/log-analytics/)kérdések: a GYIK szakasz az ingyenes csomaggal, a csomópontok díjszabásával és egyéb díjszabással kapcsolatos információkat tartalmaz.
