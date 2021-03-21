---
title: Ismerje meg, hogyan működik a Azure Monitor klasszikus riasztások automatikus áttelepítési folyamata
description: Ismerje meg, hogyan működik az automatikus áttelepítési folyamat.
ms.topic: conceptual
ms.date: 02/14/2021
ms.openlocfilehash: 92aaffcea7a7c96cd77aade318520b093eed3e14
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102045462"
---
# <a name="understand-the-automatic-migration-process-for-your-classic-alert-rules"></a>A klasszikus riasztási szabályok automatikus áttelepítési folyamatának megismerése

Amint azt [korábban bejelentettük](monitoring-classic-retirement.md), a Azure monitor klasszikus riasztásai kimaradnak a nyilvános Felhőbeli felhasználók számára, bár a használat továbbra is korlátozott, **2021**-ig. A Azure Government Cloud és az Azure China 21Vianet klasszikus riasztásai a **2024. február 29**-én törlődnek.

[Egy áttelepítési eszköz](alerts-using-migration-tool.md) is elérhető a Azure Portal az ügyfelek számára az áttelepítés elindításához. Ez a cikk az automatikus áttelepítési folyamatot ismerteti a nyilvános felhőben, amely 2021. május 31-ig indul el. Emellett az esetlegesen futtatott problémákat és megoldásokat is részletezi.

## <a name="important-things-to-note"></a>Fontos Tudnivaló

Az áttelepítési folyamat a klasszikus riasztási szabályokat új, egyenértékű riasztási szabályokra konvertálja, és műveleti csoportokat hoz létre. Az előkészítés során vegye figyelembe a következő szempontokat:

- Az új riasztási szabályokhoz tartozó értesítési adattartalom-formátumok eltérnek a klasszikus riasztási szabályok hasznos adataitól, mert több funkciót támogatnak. Ha a klasszikus riasztási szabályt logikai alkalmazásokkal, runbookok vagy webhookokkal rendelkezik, akkor előfordulhat, hogy az áttelepítést követően nem működnek megfelelően a hasznos adatok. [Ismerje meg, hogyan készítheti elő az áttelepítést](alerts-prepare-migration.md).

- Néhány klasszikus riasztási szabályt nem lehet áttelepíteni az eszköz használatával. [Megtudhatja, hogy mely szabályok nem telepíthetők át, és mire használhatók](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts).

## <a name="what-will-happen-during-the-automatic-migration-process-in-public-cloud"></a>Mi fog történni a nyilvános felhőben az automatikus áttelepítés során?

- 2021. május 31-ig nem hozhat létre új klasszikus riasztási szabályokat, és a klasszikus riasztások áttelepítését kötegekben váltja ki a rendszer.
- A törölt cél erőforrásokat vagy a [már nem támogatott metrikákat](alerts-understand-migration.md#classic-alert-rules-on-deprecated-metrics) figyelő klasszikus riasztási szabályok érvénytelennek tekintendők.
- A klasszikus riasztási szabályokat a rendszer a 2021. május 31. után távolítja el.
- Az előfizetés elindítását követően egy órán belül végre kell hajtania az áttelepítést. Az áttelepítési [eszközön a Azure monitorban](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)az ügyfelek is megfigyelheti az áttelepítés állapotát.
- Az előfizetés tulajdonosai e-mailt kapnak az áttelepítés sikerességéről vagy meghibásodásáról.

    > [!NOTE]
    > Ha nem szeretné, hogy az automatikus áttelepítési folyamat elinduljon, továbbra is aktiválhatja az áttelepítést az áttelepítési eszköz használatával.

## <a name="what-if-the-automatic-migration-fails"></a>Mi a teendő, ha az automatikus áttelepítés meghiúsul?

Ha az automatikus áttelepítési folyamat meghiúsul, az előfizetés tulajdonosai e-mailben értesítik a problémát. A probléma részletes ismertetését a Azure Monitor áttelepítési eszköz használatával tekintheti meg. Az áttelepítés során esetlegesen felmerülő problémákkal kapcsolatos segítségért tekintse meg a [hibaelhárítási útmutatót](alerts-understand-migration.md#common-problems-and-remedies) .

  > [!NOTE]
  > Ha az ügyfelektől beavatkozásra van szükség, például egy erőforrás zárolásának ideiglenes letiltására vagy egy szabályzat-hozzárendelés módosítására, akkor az ügyfeleknek meg kell oldaniuk az ilyen problémákat. Ha a problémákat nem oldja meg, a klasszikus riasztások sikeres áttelepítése nem garantálható.

## <a name="next-steps"></a>Következő lépések

- [Előkészületek a migráláshoz](alerts-prepare-migration.md)
- [A migrálási eszköz működésének ismertetése](alerts-understand-migration.md)