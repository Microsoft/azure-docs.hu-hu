---
title: Az Azure DNS metrikákkal és riasztásokkal |} A Microsoft Docs
description: Ismerje meg az Azure DNS metrikákkal és riasztásokkal.
services: dns
documentationcenter: na
author: vhorne
manager: jennoc
editor: ''
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2018
ms.author: victorh
ms.openlocfilehash: a9d8bc172eb5f5e0e119a0bde56fb167f7a0c2b2
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64699158"
---
# <a name="azure-dns-metrics-and-alerts"></a>Az Azure DNS-mérőszámok és riasztások
Az Azure DNS DNS-tartományok egy üzemeltetési szolgáltatás, amely a Microsoft Azure infrastruktúráját használja a névfeloldás. Ez a cikk azt ismerteti, metrikákkal és riasztásokkal az Azure DNS szolgáltatás.

## <a name="azure-dns-metrics"></a>Az Azure DNS-mérőszámok

Az Azure DNS mérőszámait és azok a szolgáltatásban üzemeltetett DNS-zónák konkrét elemeit figyelése lehetővé teszi az ügyfelek számára. Emellett az Azure DNS metrikákkal, konfigurálhat és kaphat riasztásokat a lényeges feltételek alapján. A metrikákat keresztül a [Azure Monitor szolgáltatásban](../azure-monitor/index.yml). Az Azure DNS a DNS-zónák az Azure monitoron keresztül a következő metrikákat biztosítja:

-   QueryVolume
-   RecordSetCount
-   RecordSetCapacityUtilization

Emellett megtekintheti a [metrikák definíciójának](../azure-monitor/platform/metrics-supported.md#microsoftnetworkdnszones) az Azure Monitor dokumentációja oldalon.
>[!NOTE]
> Jelenleg ezek a metrikák érhetők csak az Azure DNS Szolgáltatásban üzemeltetett nyilvános DNS-zónák. Ha rendelkezik saját zónák az Azure DNS Szolgáltatásban üzemeltetett, ezek a metrikák nem adatot szolgáltatja majd azokat a zónákat. Emellett a metrikák és a riasztási funkció csak az Azure-beli nyilvános felhőben támogatott. Szuverén felhők támogatása egy későbbi időpontban követi. 

A szigorúbban, megtekintheti a metrikákat a elem egy DNS-zónát. Metrikák egyes erőforrásrekordokhoz zónán belül jelenleg nem látható.

### <a name="query-volume"></a>Lekérdezés kötet

A *lekérdezés kötet* metrika az Azure DNS a DNS-zóna Azure DNS által fogadott DNS-lekérdezések (lekérdezés-forgalom) mennyiségét mutatja. A mértékegységet száma és az összesítés a időn keresztül fogadott összes lekérdezés az összes. 

Ez a metrika megtekintéséhez jelölje ki a figyelő lapon, az Azure Portalon (előzetes verzió) felületen. Válassza ki a DNS-zónát az erőforrás legördülő listából válassza ki a lekérdezés mennyiségi mérőszám, válassza ki a Sum, az összesítés. Az alábbi képernyőfelvétel egy példa látható.  További információk a Metrikaböngésző élményt és diagramkészítési, lásd: [Azure Monitor Metrikaböngészőjének](../azure-monitor/platform/metrics-charts.md).

![Lekérdezés kötet](./media/dns-alerts-metrics/dns-metrics-query-volume.png)

*Ábra: Az Azure DNS-lekérdezés kötet-metrikák*

### <a name="record-set-count"></a>Rekord beállítása száma
A *beállítása rekordszám* metrika számát jeleníti meg a rekordhalmazok az Azure DNS a DNS-zóna. A rekordhalmazok definiálva a zónában lévő összes bájtjai számítanak. Mértékegység meghatározásáért száma és az összesítés a a maximális számát az összes rekordhalmazt. Ez a metrika megtekintéséhez jelölje ki **metrikák (előnézet)** a felületen a **figyelő** lap az Azure Portalon. Válassza ki a DNS-zónáját az **erőforrás** legördülő menüben válassza a **beállítása rekordszám** metrika, és adja meg **maximális** , a **összesítési** . További információk a Metrikaböngésző élményt és diagramkészítési, lásd: [Azure Monitor Metrikaböngészőjének](../azure-monitor/platform/metrics-charts.md). 

![Rekord beállítása száma](./media/dns-alerts-metrics/dns-metrics-record-set-count.png)

*Ábra: Az Azure DNS-rekord beállítása száma metrikák*


### <a name="record-set-capacity-utilization"></a>Record Set Capacity Utilization
A *rekord beállítása tárolókapacitás kihasználtságát* az Azure DNS-ben a mérőszám tartalmazza. a DNS-zónák az rekordhalmaz-kapacitás kihasználását. Az Azure DNS minden DNS-zóna van egy rekordhalmaz korlátozás, amely meghatározza a zónában engedélyezett rekordhalmazok maximális számát (lásd: [DNS korlátok](dns-zones-records.md#limits)). Ezért ez a metrika bemutatja, hogyan zárja be, amelyek a rekordhalmaz korlát lenyomásával. Például ha 500 rekordhalmazok konfigurálva a DNS-zónát, és a zóna korlátja az alapértelmezett rekordhalmaz 5000-es, a RecordSetCapacityUtilization metrika jelennek meg az érték 10 %-os (amely 5000 elválasztó 500 által előállított,). A Mértékegység **százalékos** és a **összesítési** típus **maximális**. Ez a metrika megtekintéséhez jelölje ki a figyelő lapon, az Azure Portalon (előzetes verzió) felületen. A DNS-zóna az erőforrás legördülő listából, válassza ki a rekord beállítása kapacitás Utilization metrika válassza ki és a maximális, az összesítést. Az alábbi képernyőfelvétel egy példa látható. További információk a Metrikaböngésző élményt és diagramkészítési, lásd: [Azure Monitor Metrikaböngészőjének](../azure-monitor/platform/metrics-charts.md). 

![Rekord beállítása száma](./media/dns-alerts-metrics/dns-metrics-record-set-capacity-uitlization.png)

*Ábra: Az Azure DNS rekord beállítása kapacitáskihasználás-metrikák*

## <a name="alerts-in-azure-dns"></a>Riasztások az Azure DNS-ben
Az Azure Monitor lehetővé teszi, hogy a riasztás elérhető metrikaértékek ellen. A DNS-mérőszámok az Új riasztási konfigurációs felület érhető el. Részletesebben leírtak szerint a [Azure Monitor riasztásainak dokumentáció](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md), jelölje be a DNS-zóna és az erőforrásnak, a metrika jel típust választhat, és konfigurálja a riasztási logika és más paramétereket például **időszak**és **gyakorisága**. További definiálhat egy [műveletcsoport](../azure-monitor/platform/action-groups.md) számára, amikor a riasztási feltétel teljesül, amelynek során a riasztás kézbesíti a rendszer a kiválasztott műveletek keresztül. Riasztási az Azure Monitor-metrikák konfigurálásáról további információkért lásd: [létrehozása, megtekintése és kezelése az Azure Monitor használatával riasztások](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md). 

## <a name="next-steps"></a>További lépések
- Tudjon meg többet [Azure DNS](dns-overview.md).
