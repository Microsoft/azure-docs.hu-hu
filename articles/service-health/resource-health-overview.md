---
title: Azure Resource Health áttekintése
description: Ismerje meg, hogyan segít a Azure Resource Health az Azure-erőforrásokat érintő szolgáltatási problémák diagnosztizálásában és támogatásában.
ms.topic: conceptual
ms.date: 05/10/2019
ms.openlocfilehash: 25425eadfaf54ba56cde3b377a44cd85c2b68959
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "90967867"
---
# <a name="resource-health-overview"></a>Resource Health áttekintése
 
Azure Resource Health segítséget nyújt az Azure-erőforrásokat érintő szolgáltatási problémák diagnosztizálásában és támogatásában. Az erőforrások aktuális és korábbi állapotáról is beszámol.

Az [Azure-állapotjelentések](https://status.azure.com) az Azure-ügyfelek széles körét érintő szolgáltatásokkal kapcsolatos problémákra vonatkoznak. A Resource Health személyre szabott irányítópultot biztosít az erőforrások állapotáról. Resource Health azt mutatja, hogy az erőforrások az Azure-szolgáltatásokkal kapcsolatos problémák miatt nem voltak elérhetők. Ezek az információk megkönnyítik annak megtekintését, hogy az SLA-t megsértették-e.

## <a name="resource-definition-and-health-assessment"></a>Erőforrás-definíció és állapot-Értékelés

Az *erőforrás* egy Azure-szolgáltatás adott példánya, például egy virtuális gép, egy webalkalmazás vagy egy SQL Database. A Resource Health különböző Azure-szolgáltatásokból származó jelek alapján állapítja meg, hogy az adott erőforrás kifogástalan állapotú-e. Ha egy erőforrás nem kifogástalan állapotú, Resource Health elemzi a probléma forrásának megállapításához szükséges további információkat. Emellett jelentéseket készít a Microsoft által a probléma megoldásához szükséges műveletekről, és azonosítja azokat a műveleteket, amelyeket el tud végezni.

Az állapot értékelésével kapcsolatos további információkért tekintse meg az erőforrástípusok és állapot-ellenőrzések listáját [Azure Resource Health](resource-health-checks-resource-types.md).

## <a name="health-status"></a>Állapotadatok

Egy erőforrás állapota a következő állapotok egyike jelenik meg.

### <a name="available"></a>Elérhető

Az *elérhető* érték azt jelenti, hogy nem észlelhetők olyan események, amelyek hatással vannak az erőforrás állapotára. Azokban az esetekben, amikor az erőforrás az elmúlt 24 órában nem tervezett állásidőből lett helyreállítva, "nemrég megoldott" értesítést fog látni.

![Az * elérhető * állapot a "nemrég megoldott" értesítéssel rendelkező virtuális géphez](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>Nem érhető el

A nem *érhető el* érték azt jelenti, hogy a szolgáltatás egy folyamatos platformot észlelt, vagy az erőforrás állapotát befolyásoló nem platformos eseményt.

#### <a name="platform-events"></a>Platformesemények

A platform eseményeire az Azure-infrastruktúra több összetevője is aktiválódik. Ezek közé tartoznak az ütemezett műveletek (például a tervezett karbantartás) és a váratlan incidensek (például egy nem tervezett gazdagép újraindítása vagy csökkentett teljesítményű gazdagép hardvere, amelyet egy adott időablak után kell ellátni).

Resource Health további részleteket tartalmaz az eseményről és a helyreállítási folyamatról. Azt is lehetővé teszi, hogy kapcsolatba lépjen Microsoft ügyfélszolgálata akkor is, ha nem rendelkezik aktív támogatási szerződéssel.

![A * nem érhető el * állapot a virtuális géphez a platform eseménye miatt](./media/resource-health-overview/Unavailable.png)

#### <a name="non-platform-events"></a>Nem platformos események

A platformon kívüli eseményeket a felhasználói műveletek aktiválják. Ilyenek például a virtuális gépek leállítása vagy az Azure cache-hez való csatlakozások maximális száma a Redis.

![A virtuális gép "nem érhető el" állapota a platformon kívüli események miatt](./media/resource-health-overview/Unavailable_NonPlatform.png)

### <a name="unknown"></a>Ismeretlen

Az *ismeretlen* érték azt jelenti, hogy Resource Health több mint 10 percen belül nem kapott információt az erőforrásról. Bár ez az állapot nem az erőforrás állapotának végleges jele, a hibaelhárításhoz fontos adatpont.

Ha az erőforrás a várt módon fut, az erőforrás állapota néhány perc múlva *elérhetővé* válik.

Ha az erőforrással kapcsolatos problémákat tapasztal, az *ismeretlen* állapot azt jelentheti, hogy a platformon lévő esemény hatással van az erőforrásra.

![Ismeretlen * állapotú virtuális gép esetén](./media/resource-health-overview/Unknown.png)

### <a name="degraded"></a>Csökkentett teljesítményű

Az *elromlott* érték azt jelenti, hogy az erőforrás a teljesítmény csökkenését észlelte, bár a szolgáltatás továbbra is használható.

A különböző erőforrásoknak a saját feltételeiket kell megadniuk, amikor jelentést készítenek azok teljesítményéről.

![* A virtuális gép csökkentett teljesítményű * állapota](./media/resource-health-overview/degraded.png)

## <a name="reporting-an-incorrect-status"></a>Helytelen állapot jelentése

Ha úgy gondolja, hogy az aktuális állapot helytelen, a **jelentés helytelen állapot állapotának** kiválasztásával megadhatja a számunkra. Abban az esetben, ha egy Azure-probléma hatással van az Ön számára, javasoljuk, hogy forduljon a Resource Health támogatási szolgálatához.

![Helytelen állapottal kapcsolatos adatok beküldésére szolgáló űrlap](./media/resource-health-overview/incorrect-status.png)

## <a name="history-information"></a>Előzmények adatai

A Resource Health **állapotának előzményei** részben akár 30 napig is elérheti az előzményeket.

![Resource Health események listája az elmúlt két hétben](./media/resource-health-overview/history-blade.png)

## <a name="get-started"></a>Bevezetés

Resource Health megnyitása egy erőforráshoz:

1. Jelentkezzen be az Azure Portalra.
2. Keresse meg az erőforrást.
3. A bal oldali ablaktábla erőforrás menüjében válassza az **erőforrás állapota** lehetőséget.

![Resource Health megnyitása az erőforrás nézetből](./media/resource-health-overview/from-resource-blade.png)

A Resource Health a **minden szolgáltatás** lehetőség kiválasztásával és az **erőforrás állapotának** beírásával is elérheti a szűrő szövegmezőben. A **Súgó + támogatás** ablaktáblán válassza az [erőforrás állapota](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/resourceHealth)lehetőséget.

![Resource Health megnyitása a "minden szolgáltatás"](./media/resource-health-overview/FromOtherServices.png)

## <a name="next-steps"></a>Következő lépések

Tekintse át ezeket a hivatkozásokat, hogy többet tudjon meg a Resource Healthról:
-  [Erőforrástípusok és állapot-ellenőrzések Azure Resource Health](resource-health-checks-resource-types.md)
-  [Gyakori kérdések a Azure Resource Health](resource-health-faq.md)
