---
title: Ismerje meg, hogyan működik a Azure Monitor klasszikus riasztások automatikus áttelepítési folyamata
description: Ismerje meg, hogyan működik az automatikus áttelepítési folyamat.
ms.topic: conceptual
ms.date: 08/19/2019
ms.subservice: alerts
ms.openlocfilehash: 9ce3550dcef174a4d970917fabfda3c4fd2a8df4
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526551"
---
# <a name="understand-the-automatic-migration-process-for-your-classic-alert-rules"></a>A klasszikus riasztási szabályok automatikus áttelepítési folyamatának megismerése

Amint azt [korábban bejelentettük](monitoring-classic-retirement.md), a Azure monitor klasszikus riasztásai kimaradnak a nyilvános Felhőbeli felhasználók számára, bár továbbra is korlátozott használatban vannak az új riasztásokat még nem támogató erőforrások esetében. A nyugdíjazási folyamat részeként [egy áttelepítési eszköz](alerts-using-migration-tool.md) is elérhető a Azure Portal az ügyfelek számára az áttelepítés elindításához.
Ez a cikk végigvezeti az automatikus áttelepítési folyamaton, és segít megoldani az esetlegesen futtatott problémákat.

  > [!NOTE]
  > Ez a cikk csak az Azure nyilvános felhőre vonatkozik. A Azure Government Cloud és az Azure China 21Vianet Azure Monitor klasszikus riasztások kivonási folyamata jövőbeli időpontban lesz bejelentve.

## <a name="what-will-happen-during-the-automatic-migration-process"></a>Mi fog történni az automatikus áttelepítési folyamat során?

- **2019. szeptember 1-től** az ügyfelek nem tudnak új klasszikus riasztási szabályokat létrehozni, kivéve [bizonyos mérőszámokat](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts).
- A kivételek miatt az ügyfél továbbra is új klasszikus riasztási szabályokat hozhat létre, és a klasszikus riasztásokat a további bejelentésig használhatja.
- **2019. szeptember 1-től** kezdődően a klasszikus riasztások áttelepítése a klasszikus riasztásokkal rendelkező ügyfelek kötegében aktiválódik.
- Hasonlóan az önkéntes áttelepítési eszközhöz, bizonyos klasszikus riasztási szabályok, amelyek nem telepíthetők át, maradnak. A klasszikus riasztási szabályok továbbra is támogatottak lesznek a további bejelentések előtt. Azonban minden érvénytelen klasszikus riasztási szabály törlődik, mivel azok nem működőképesek.
A törölt cél erőforrásokat vagy a [már nem támogatott metrikákat](alerts-understand-migration.md#classic-alert-rules-on-deprecated-metrics) figyelő klasszikus riasztási szabályok érvénytelennek tekintendők.
- Ha az előfizetés áttelepítése megkezdődik, hacsak nincs probléma, az áttelepítés egy órán belül befejeződik. Az áttelepítés állapotát a [Azure monitor áttelepítési](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)paneljén is figyelheti.
- Az előfizetés tulajdonosai e-mailt kapnak az áttelepítés sikeres befejezéséről.
- Ha az áttelepítés során problémák merülnek fel, az előfizetések tulajdonosai is kapnak egy e-mailt, amely arról tájékoztatja őket. Az ügyfelek az áttelepítési panelen tekinthetik meg a probléma részletes adatait.
- Ha az ügyfelektől beavatkozásra van szükség, például egy erőforrás zárolásának ideiglenes letiltására vagy egy szabályzat-hozzárendelés módosítására, akkor az ügyfeleknek meg kell oldaniuk az ilyen problémákat. Ha a problémákat nem oldja meg, a klasszikus riasztások sikeres áttelepítése nem garantálható.

    > [!NOTE]
    > Ha nem szeretné, hogy az automatikus áttelepítési folyamat elinduljon, továbbra is aktiválhatja az áttelepítést az áttelepítési eszköz használatával.

## <a name="important-things-to-note"></a>Fontos Tudnivaló

Az áttelepítési folyamat a klasszikus riasztási szabályokat új, egyenértékű riasztási szabályokra konvertálja, és műveleti csoportokat hoz létre. Az előkészítés során vegye figyelembe a következő szempontokat:

- Az új riasztási szabályokhoz tartozó értesítési hasznos formátumok eltérnek a klasszikus riasztási szabályoktól, mert több funkciót támogatnak. Ha olyan logikai alkalmazásokkal, runbookok vagy webhookokkal rendelkezik, amelyeket klasszikus riasztási szabály indít el, akkor előfordulhat, hogy az áttelepítést követően az áttelepítés befejeződése után leáll a várt működés. [Ismerje meg, hogyan készítheti elő az áttelepítést](alerts-prepare-migration.md).

- Néhány klasszikus riasztási szabályt nem lehet áttelepíteni az eszköz használatával. [Megtudhatja, hogy mely szabályok nem telepíthetők át, és mire használhatók](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts).

    > [!NOTE]
    > Az áttelepítési folyamat nem befolyásolja a klasszikus riasztási szabályok kiértékelését. A rendszer továbbra is futtatja és riasztásokat küld, amíg át nem telepíti őket, és az új riasztási szabályok érvénybe lépnek.

## <a name="what-if-the-automatic-migration-fails"></a>Mi a teendő, ha az automatikus áttelepítés meghiúsul?

Ha az automatikus áttelepítési folyamat meghiúsul, az előfizetés tulajdonosai e-mailben értesítik a problémát. A probléma részletes ismertetését az Azure Monitor áttelepítési paneljén végezheti el.

Az áttelepítés során esetlegesen felmerülő problémákkal kapcsolatos segítségért tekintse meg a [hibaelhárítási útmutatót](alerts-understand-migration.md#common-problems-and-remedies) .

  > [!NOTE]
  > Ha az ügyfelektől beavatkozásra van szükség, például egy erőforrás zárolásának ideiglenes letiltására vagy egy szabályzat-hozzárendelés módosítására, akkor az ügyfeleknek meg kell oldaniuk az ilyen problémákat. Ha a problémákat nem oldja meg, a klasszikus riasztások sikeres áttelepítése nem garantálható.

## <a name="next-steps"></a>Következő lépések

- [Előkészületek a migráláshoz](alerts-prepare-migration.md)
- [A migrálási eszköz működésének ismertetése](alerts-understand-migration.md)
