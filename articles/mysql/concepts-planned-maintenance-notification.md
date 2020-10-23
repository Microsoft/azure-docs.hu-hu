---
title: Tervezett karbantartási értesítés – Azure Database for MySQL – egyetlen kiszolgáló
description: Ez a cikk a Azure Database for MySQL-Single Server tervezett karbantartási értesítési funkcióját ismerteti
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 10/21/2020
ms.openlocfilehash: 3a5a4b3997a9286901fc24173e57563c5abe68a1
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92428922"
---
# <a name="planned-maintenance-notification-in-azure-database-for-mysql---single-server"></a>Tervezett karbantartási értesítések Azure Database for MySQL – egyetlen kiszolgálón

Megtudhatja, hogyan készítheti elő a tervezett karbantartási eseményeket a Azure Database for MySQL.

## <a name="what-is-a-planned-maintenance"></a>Mi a tervezett karbantartás?

A Azure Database for MySQL szolgáltatás a mögöttes hardver, operációs rendszer és adatbázismotor automatizált javítását végzi. A javítás új szolgáltatás-funkciókat, biztonságot és szoftverfrissítéseket tartalmaz. A MySQL-motor esetében az alverziók frissítése automatikusan megtörténik, és a javítási ciklus részeként szerepel. Nincs szükség felhasználói műveletre vagy konfigurációs beállításra a javításhoz. A javítást széles körben tesztelik, és a biztonságos üzembe helyezési eljárások használatával végezhető el.

A tervezett karbantartás karbantartási időszak, ha ezek a szolgáltatások egy adott Azure-régióban lévő kiszolgálókra vannak telepítve. A tervezett karbantartás során létrejön egy értesítési esemény, amely tájékoztatja a felhasználókat, ha a szolgáltatás frissítését a kiszolgálókat üzemeltető Azure-régióban helyezik üzembe. A két tervezett karbantartás közötti minimális időtartam 30 nap. Értesítést kap a következő karbantartási időszak 72 órával előre.

## <a name="planned-maintenance---duration-and-customer-impact"></a>Tervezett karbantartás – időtartam és az ügyfelek hatása

Egy adott Azure-régió tervezett karbantartása általában 15 órával várható. Ha szükséges, az ablak a visszagörgetési terv végrehajtásához is tartalmaz pufferelési időt. A tervezett karbantartás során az adatbázis-kiszolgáló újraindítása vagy feladatátvétele is lehetséges, ami a végfelhasználók adatbázis-kiszolgálóinak rövid elérhetőségét eredményezheti. Azure Database for MySQL-kiszolgálók tárolókban futnak, így az adatbázis-kiszolgáló újraindítása általában gyors, és általában 60-120 másodpercen belül várható. A mérnöki csapat gondosan figyeli a teljes tervezett karbantartási eseményt, beleértve az egyes kiszolgálók újraindítását is. A kiszolgáló feladatátvételi ideje függ az adatbázis helyreállításának időpontjától, ami azt eredményezheti, hogy az adatbázis továbbra is online állapotba kerül, ha a feladatátvétel időpontjában nagy tranzakciós tevékenységet végez a kiszolgálón. Ha el szeretné kerülni a hosszabb újraindítási időt, ajánlott elkerülni a hosszú ideig futó tranzakciókat (tömeges terheléseket) a tervezett karbantartási események során.

Összefoglalva, míg a tervezett karbantartási esemény 15 óráig fut, az egyes kiszolgálók esetében a kiszolgáló tranzakciós tevékenységeitől függően általában 60 másodpercre van hatással. Az értesítés elküldése 72 naptári óra előtt történik a tervezett karbantartás elindítása előtt, és egy másikat, miközben egy adott régió karbantartását folyamatban van.

## <a name="how-can-i-get-notified-of-planned-maintenance"></a>Hogyan Kérhetek értesítést a tervezett karbantartásról?

A tervezett karbantartási értesítések funkció használatával riasztásokat fogadhat egy közelgő tervezett karbantartási eseményhez. Értesítést kap a közelgő karbantartási 72 naptári órával az esemény előtt, és egy másikat, miközben a karbantartás folyamatban van egy adott régióban.

### <a name="planned-maintenance-notification"></a>Tervezett karbantartási értesítés

> [!IMPORTANT]
> A tervezett karbantartási értesítések jelenleg előzetes verzióban érhetők el minden régióban, **kivéve** az USA nyugati középső régióját

A **tervezett karbantartási értesítések** lehetővé teszik, hogy riasztásokat kapjon a közelgő tervezett karbantartási eseményekről a Azure Database for MySQL. Ezek az értesítések a [Service Health](../service-health/overview.md) tervezett karbantartásával vannak integrálva, és lehetővé teszik az előfizetések összes ütemezett karbantartásának megtekintését egy helyen. Emellett segít az értesítések méretezésében a megfelelő célközönségek számára a különböző erőforráscsoportok esetében, mivel előfordulhat, hogy a különböző kapcsolattartók különböző erőforrásokért felelősek. Az eseményt az esemény előtt a közelgő karbantartási 72 naptári órával fogja kapni.

Minden eseményről minden kísérletet megteszünk a **tervezett karbantartási értesítés** 72 óra megadására. A kritikus vagy biztonsági javítások esetében azonban előfordulhat, hogy az értesítések közelebb kerülnek az eseményhez, vagy kimaradnak.

Megtekintheti a tervezett karbantartási értesítést Azure Portal vagy konfigurálhatja a riasztásokat az értesítés fogadásához. 

### <a name="check-planned-maintenance-notification-from-azure-portal"></a>Az Azure Portal tervezett karbantartási értesítésének keresése

1. A [Azure Portal](https://portal.azure.com)válassza a **Service Health**lehetőséget.
2. **Tervezett karbantartás** lap kiválasztása
3. Válassza ki az **előfizetést**, * * régiót és **szolgáltatást** , amelyre vonatkozóan meg szeretné adni a tervezett karbantartási értesítést. 
   
### <a name="to-receive-planned-maintenance-notification"></a>Tervezett karbantartási értesítés fogadása

1. A [portálon](https://portal.azure.com)válassza a **Service Health**lehetőséget.
2. A **riasztások** szakaszban válassza az **állapot riasztások**lehetőséget.
3. Válassza a **+ szolgáltatás állapotára vonatkozó riasztás hozzáadása** lehetőséget, és töltse ki a mezőket.
4. Töltse ki a kötelező mezőket. 
5. Válassza ki az **esemény típusát**, válassza a **tervezett karbantartás** lehetőséget, vagy **válassza az összes lehetőséget** .
6. A **műveleti csoportok** azt határozzák meg, hogyan szeretné fogadni a riasztást (e-mail küldése, logikai alkalmazás elindítása stb.)  
7. Győződjön meg arról, hogy az engedélyezés szabály a létrehozáskor Igen értékre van állítva.
8. Válassza a riasztási **szabály létrehozása** lehetőséget a riasztás befejezéséhez

A **szolgáltatás állapotára vonatkozó riasztások**létrehozásával kapcsolatos részletes útmutatásért lásd: [műveletnapló riasztások létrehozása a szolgáltatási értesítéseken](../service-health/alerts-activity-log-service-notifications.md).

## <a name="can-i-control-or-change-planned-maintenance-for-my-servers-after-i-receive-a-notification-event"></a>Megadhatom vagy módosíthatom a kiszolgálók tervezett karbantartását, miután értesítést kaptam egy értesítési eseményről?

A kiszolgáló biztonságos, stabil és naprakészen tartásához karbantartásra van szükség. A tervezett karbantartási eseményt nem lehet megszakítani vagy elhalasztani. Az értesítésnek az adott Azure-régióba való küldése után a javítási ütemezés módosításai nem hajthatók végre az adott régióban található egyes kiszolgálókon. A javítás a teljes régióra vonatkozóan egyszerre van bevezetve. Azure Database for MySQL – az egykiszolgálós szolgáltatás olyan felhőalapú natív alkalmazásokhoz készült, amely nem igényli a szolgáltatás részletes szabályozását vagy testreszabását. Ha szeretné, hogy a kiszolgálók karbantartását ütemezni tudja, javasoljuk, hogy [rugalmas kiszolgálókat](./flexible-server/overview.md)vegyen fontolóra.

## <a name="are-all-the-azure-regions-patched-at-the-same-time"></a>Az összes Azure-régió egyszerre lett kijavítottan?

Nem, az összes Azure-régió javítás alatt áll a telepítési Wise-ablak időzítése során. Az üzembe helyezési Wise-ablak általában egy adott Azure-régióban, a következő naptól számítva 5 – 8. helyi idő szerint nyúlik le. A Geo-párosítású Azure-régiók különböző napokon vannak kijavítani. Az adatbázis-kiszolgálók magas rendelkezésre állása és üzletmenet-folytonossága érdekében ajánlott a [régiók közötti olvasási replikák](./concepts-read-replicas.md#cross-region-replication) használata.

## <a name="retry-logic"></a>Újrapróbálkozási logika

Egy átmeneti hiba (más néven átmeneti hiba) egy olyan hiba, amely magát a megoldást fogja megoldani. [Átmeneti hibák](./concepts-connectivity.md#transient-errors) merülhetnek fel a karbantartás során. Az események többségét a rendszer automatikusan csökkenti a 60 másodpercnél kisebb mértékben. Az átmeneti hibákat az [újrapróbálkozási logikával](./concepts-connectivity.md#handling-transient-errors)kell kezelni.


## <a name="next-steps"></a>További lépések

- Ha bármilyen kérdése vagy javaslata van a Azure Database for MySQL használatáról, küldjön e-mailt a Azure Database for MySQL csapatának a következő címen: *AskAzureDBforMySQL@service.microsoft.com* .
- A riasztások metrikai létrehozásával kapcsolatos útmutatást a riasztások [beállítása](howto-alert-on-metric.md) című témakörben tekintheti meg.
- [Azure Database for MySQL – egyetlen kiszolgáló kapcsolódási problémáinak elhárítása](howto-troubleshoot-common-connection-issues.md)
- [Átmeneti hibák kezelése és hatékony kapcsolódás Azure Database for MySQL – egyetlen kiszolgálóhoz](concepts-connectivity.md)
