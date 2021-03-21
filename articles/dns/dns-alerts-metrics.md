---
title: Metrikák és riasztások – Azure DNS
description: Ennek a képzési útnak a megismeréséhez Azure DNS mérőszámok és riasztások.
services: dns
documentationcenter: na
author: rohinkoul
manager: kumudD
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2018
ms.author: rohink
ms.openlocfilehash: 391109727877544a4e94bae376ecef4d33a13cfb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100575275"
---
# <a name="azure-dns-metrics-and-alerts"></a>Metrikák és riasztások Azure DNS
A Azure DNS olyan DNS-tartományok üzemeltetési szolgáltatása, amelyek névfeloldást biztosítanak a Microsoft Azure-infrastruktúra használatával. Ez a cikk a Azure DNS szolgáltatás metrikáit és riasztásait ismerteti.

## <a name="azure-dns-metrics"></a>Azure DNS metrikák

A Azure DNS metrikákat biztosít az ügyfeleknek, hogy a szolgáltatásban üzemeltetett DNS-zónák konkrét szempontjait ellenőrizzék. Emellett Azure DNS metrikákkal is konfigurálhatja és fogadhatja a riasztásokat az érdeklődési feltételek alapján. A metrikák a [Azure monitor szolgáltatáson](../azure-monitor/index.yml)keresztül érhetők el. A Azure DNS a következő metrikákat biztosítja a DNS-zónák Azure Monitorn keresztül:

-   QueryVolume
-   RecordSetCount
-   RecordSetCapacityUtilization

A [metrikák definícióját](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkdnszones) a Azure monitor Dokumentációs oldalon is láthatja.
>[!NOTE]
> Jelenleg ezek a metrikák csak a Azure DNS üzemeltetett nyilvános DNS-zónák esetében érhetők el. Ha Azure DNS üzemeltetett privát zónákkal rendelkezik, ezek a metrikák nem biztosítanak adatokat ezekhez a zónákhoz. Emellett a mérőszámok és a riasztási funkció csak az Azure nyilvános felhőben támogatott. A szuverén felhők támogatása későbbi időpontban történik. 

A mérőszámokat tartalmazó legrészletesebb elem egy DNS-zóna. A zónán belüli egyes erőforrásrekordok mérőszámai jelenleg nem láthatók.

### <a name="query-volume"></a>Lekérdezési kötet

A Azure DNS *lekérdezési mennyiségi* mérőszáma a DNS-zóna Azure DNS által fogadott DNS-lekérdezések (lekérdezési forgalom) mennyiségét jeleníti meg. A mértékegység a Count, az Összesítés pedig az adott időszakban fogadott lekérdezések teljes összege. 

A metrika megtekintéséhez válassza a metrikák (előzetes verzió) Explorer felületét a Azure Portal figyelés lapján. Válassza ki a DNS-zónát az erőforrás legördülő menüből, válassza ki a mennyiségi lekérdezés mérőszámát, és válassza az összeg lehetőséget az Összesítés elemnél. Az alábbi képernyőképen egy példa látható.  További információ a Metrikaböngészői élményről és a diagramokról: [Azure Monitor Metrikaböngésző](../azure-monitor/essentials/metrics-charts.md).

![Lekérdezési kötet](./media/dns-alerts-metrics/dns-metrics-query-volume.png)

*Ábra: Azure DNS lekérdezési kötetek mérőszámai*

### <a name="record-set-count"></a>Rekordok készletének száma
A rekordhalmazok *száma metrika a* DNS-zóna Azure DNSban lévő rekordhalmazok számát jeleníti meg. A zónában definiált összes rekordhalmaz számít. A mértékegység a darabszám, az Összesítés pedig a rekordhalmazok maximális száma. A metrika megtekintéséhez válassza a **metrikák (előzetes verzió)** Explorer felületét a Azure Portal **figyelés** lapján. Válassza ki a DNS-zónát az **erőforrás** legördülő menüből, válassza a **rekordok száma** mérőszámot, majd válassza a **Max** elemet az **Összesítés** elemnél. További információ a Metrikaböngészői élményről és a diagramokról: [Azure Monitor Metrikaböngésző](../azure-monitor/essentials/metrics-charts.md). 

![Rekordok készletének száma](./media/dns-alerts-metrics/dns-metrics-record-set-count.png)

*Ábra: Azure DNS Record set Count mérőszámai*


### <a name="record-set-capacity-utilization"></a>Rekordazonosító kapacitásának kihasználtsága
A rekordhalmaz *kapacitás-kihasználtsági* mérőszáma Azure DNS a DNS-zónák kihasználtságának százalékos arányát jeleníti meg. Azure DNS minden DNS-zónája egy rekordhalmaz-korlátra vonatkozik, amely meghatározza a zóna számára engedélyezett rekordhalmazok maximális számát (lásd: [DNS-korlátok](dns-zones-records.md#limits)). Ezért ez a mérőszám azt mutatja be, hogy milyen módon közelítheti meg a rekordhalmaz korlátját. Ha például 500 rekordhalmaz van konfigurálva a DNS-zónához, és a zóna alapértelmezett rekordhalmaz-korlátja 5000, a RecordSetCapacityUtilization metrika a 10%-os értéket jeleníti meg (amelyet a 500 a 5000-nek való felosztásával szerez be). A mérték mértékegysége **százalék** , az **Összesítés** típusa pedig **maximum**. A metrika megtekintéséhez válassza a metrikák (előzetes verzió) Explorer felületét a Azure Portal figyelés lapján. Válassza ki a DNS-zónát az erőforrás legördülő listából, válassza ki a kapacitás kihasználtságának beállítása metrikát, és válassza a Max elemet az Összesítés lehetőségnél. Az alábbi képernyőképen egy példa látható. További információ a Metrikaböngészői élményről és a diagramokról: [Azure Monitor Metrikaböngésző](../azure-monitor/essentials/metrics-charts.md). 

![Képernyőkép, amely a metrikák megtekintésének whows szemlélteti.](./media/dns-alerts-metrics/dns-metrics-record-set-capacity-uitlization.png)

*Ábra: Azure DNS rekordazonosító kapacitás kihasználtsági metrikái*

## <a name="alerts-in-azure-dns"></a>Riasztások Azure DNS
A Azure Monitor lehetővé teszi az elérhető metrikai értékek elleni riasztás lehetőségét. A DNS-metrikák a riasztások új konfigurációs felületén érhetők el. A [Azure monitor riasztások dokumentációjában](../azure-monitor/alerts/alerts-metric.md)részletesen leírtak szerint kiválaszthatja a DNS-zónát erőforrásként, kiválaszthatja a metrikai jel típusát, valamint konfigurálhatja a riasztási logikát és egyéb paramétereket, például az **időtartamot** és a **gyakoriságot**. A riasztási feltétel teljesülése esetén tovább definiálhat egy [műveleti csoportot](../azure-monitor/alerts/action-groups.md) , amely a riasztást a kiválasztott műveleteken keresztül továbbítja. A Azure Monitor metrikákkal kapcsolatos riasztások konfigurálásával kapcsolatos további információkért lásd: [riasztások létrehozása, megtekintése és kezelése Azure monitor használatával](../azure-monitor/alerts/alerts-metric.md). 

## <a name="next-steps"></a>Következő lépések
- További információ a [Azure DNSról](dns-overview.md).