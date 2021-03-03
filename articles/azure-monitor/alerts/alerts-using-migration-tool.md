---
title: Azure Monitor riasztási szabályok áttelepíthetők
description: Ismerje meg, hogyan telepítheti át klasszikus riasztási szabályait az önkéntes áttelepítési eszköz használatával.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 02/14/2020
ms.subservice: alerts
ms.openlocfilehash: 644346c7355df581843fa8a838eea50cfeccefd6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101729096"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>A klasszikus riasztási szabályok áttelepítéséhez használja az önkéntes áttelepítési eszközt

Amint azt [korábban bejelentettük](monitoring-classic-retirement.md), a Azure monitor klasszikus riasztásai kimaradnak a nyilvános Felhőbeli felhasználók számára, bár a használat továbbra is korlátozott, **2021**-ig. A Azure Government Cloud és az Azure China 21Vianet klasszikus riasztásai a **2024. február 29**-én törlődnek.

Az áttelepítési eszköz a Azure Portalban olyan ügyfelek számára érhető el, akik klasszikus riasztási szabályokat használnak, és maguknak szeretnék elindítani az áttelepítést. Ez a cikk az áttelepítési eszköz használatát ismerteti.

## <a name="before-you-migrate"></a>Migrálás előtt

Az áttelepítési folyamat a klasszikus riasztási szabályokat új, egyenértékű riasztási szabályokra konvertálja, és műveleti csoportokat hoz létre. Az előkészítés során vegye figyelembe a következő szempontokat:

- Az értesítési hasznos adatok formátuma és az új riasztási szabályok létrehozására és kezelésére szolgáló API-k eltérnek a klasszikus riasztási szabályoktól, mert több funkciót támogatnak. [Ismerje meg, hogyan készítheti elő az áttelepítést](alerts-prepare-migration.md).

- Néhány klasszikus riasztási szabály nem telepíthető át az eszköz használatával. [Megtudhatja, hogy mely szabályok nem telepíthetők át, és mire használhatók](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts).

    > [!NOTE]
    > Az áttelepítési folyamat nem befolyásolja a klasszikus riasztási szabályok kiértékelését. A rendszer továbbra is futtatja és riasztásokat küld, amíg át nem telepíti őket, és az új riasztási szabályok érvénybe lépnek.

## <a name="how-to-use-the-migration-tool"></a>A migrálási eszköz használata

A klasszikus riasztási szabályok áttelepítésének elindításához a Azure Portal hajtsa végre az alábbi lépéseket:

1. A [Azure Portal](https://portal.azure.com)területen válassza a **figyelés** lehetőséget.

1. Válassza a **riasztások** lehetőséget, majd a **riasztási szabályok kezelése** vagy a **klasszikus riasztások megtekintése** lehetőséget.

1. Válassza az áttelepítés **új szabályokra** lehetőséget az áttelepítési kezdőlapra való ugráshoz. Ezen az oldalon az összes előfizetésének és a Migrálás állapotának listája látható:

    ![Képernyőfelvétel: a riasztási szabályok áttelepíthetők lapja.](media/alerts-using-migration-tool/migration-landing.png "Szabályok áttelepíthetők")

    Az eszköz használatával áttelepíthető összes előfizetés készként van megjelölve az **áttelepítésre**.

    > [!NOTE]
    > Az áttelepítési eszköz a klasszikus riasztási szabályokat használó összes előfizetés fázisában van. A bevezetés korai fázisaiban előfordulhat, hogy egyes előfizetések nem készen állnak az áttelepítésre.

1. Válasszon ki egy vagy több előfizetést, majd válassza az **áttelepítés előnézete** lehetőséget.

    Az eredményül kapott oldal megjeleníti a klasszikus riasztási szabályok részleteit, amelyek egy adott előfizetéshez lesznek áttelepítve. Az **előfizetéshez tartozó áttelepítési adatok letöltésével** is kiválaszthatja a részleteket CSV formátumban.

    ![Képernyőfelvétel: a riasztási szabályok áttelepítése lap az előfizetésre vonatkozó áttelepítési adatok letöltésére szolgáló hivatkozásra kattintva megadhatja az e-mailek áttelepítési értesítését.](media/alerts-using-migration-tool/migration-preview.png "Áttelepítés előzetes verziója")

1. Egy vagy több e-mail-címet kell megadnia az áttelepítési állapot értesítéséhez. E-mailt fog kapni, ha az áttelepítés befejeződik, vagy ha bármilyen műveletre szükség van.

1. Válassza az **áttelepítés indítása** lehetőséget. Olvassa el a megerősítő párbeszédpanelen megjelenő információkat, és erősítse meg, hogy készen áll az áttelepítési folyamat elindítására.

    > [!IMPORTANT]
    > Miután elindította az áttelepítést egy előfizetéshez, nem szerkesztheti és nem hozhatja létre klasszikus riasztási szabályokat az adott előfizetéshez. Ez a korlátozás biztosítja, hogy a klasszikus riasztási szabályok módosítása ne vesszenek el az új szabályokba való áttelepítés során. Bár a klasszikus riasztási szabályok nem módosíthatók, a rendszer továbbra is futtatja, és a riasztásokat csak az áttelepítés előtt küldi el. Az előfizetéshez tartozó áttelepítés befejezése után már nem használhat klasszikus riasztási szabályokat.

    ![A képernyőképen egy megerősítő üzenet jelenik meg az áttelepítéshez, beleértve a fontos információkat is, amelyek további információt tartalmaznak a továbblépés előtt.](media/alerts-using-migration-tool/migration-confirm.png "Áttelepítés megkezdésének megerősítése")

1. Ha az áttelepítés befejeződik, vagy ha szükséges, a rendszer e-mailt fog kapni a korábban megadott címekről. Azt is megteheti, hogy rendszeresen ellenőrzi az állapotot az áttelepítés kezdőlapján a portálon.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="why-is-my-subscription-listed-as-not-ready-for-migration"></a>Miért van felsorolva az előfizetésem, mert nem áll készen az áttelepítésre?

Az áttelepítési eszköz az ügyfelek számára fázisban van. A korai fázisokban előfordulhat, hogy az előfizetések többsége vagy mindegyike **nem áll készen az áttelepítésre**. 

Ha egy előfizetés készen áll az áttelepítésre, az előfizetés tulajdonosa egy e-mail-üzenetet kap arról, hogy az eszköz elérhető lesz. Figyelje meg ezt az üzenetet.

### <a name="who-can-trigger-the-migration"></a>Kik indíthatják el az áttelepítést?

Azok a felhasználók, akik az előfizetés szintjén hozzárendelt figyelési közreműködő szerepkörrel rendelkeznek, aktiválhatja az áttelepítést. [További információ az áttelepítési folyamat Azure szerepköralapú hozzáférés-vezérléséről](alerts-understand-migration.md#who-can-trigger-the-migration).

### <a name="how-long-will-the-migration-take"></a>Mennyi időt vesz igénybe az áttelepítés?

Az áttelepítés a legtöbb előfizetéshez egy órán belül befejeződik. Nyomon követheti az áttelepítés folyamatát az áttelepítés kezdőlapján. Az áttelepítés során ügyeljen arra, hogy a riasztások továbbra is a klasszikus riasztások rendszerében vagy az újat futtassanak.

### <a name="what-can-i-do-if-i-run-into-a-problem-during-migration"></a>Mi a teendő, ha az áttelepítés során problémát tapasztalok?

Az áttelepítés során esetlegesen felmerülő problémákkal kapcsolatos segítségért tekintse meg a [hibaelhárítási útmutatót](alerts-understand-migration.md#common-problems-and-remedies) . Ha bármilyen műveletre van szükség az áttelepítés befejezéséhez, értesítést kap az eszköz beállításakor megadott e-mail-címekről.

## <a name="next-steps"></a>Következő lépések

- [Előkészületek a migráláshoz](alerts-prepare-migration.md)
- [A migrálási eszköz működésének ismertetése](alerts-understand-migration.md)