---
title: Azure Monitor riasztási szabályok áttelepíthetők
description: Ismerje meg, hogyan telepítheti át klasszikus riasztási szabályait az önkéntes áttelepítési eszköz használatával.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 03/19/2018
ms.subservice: alerts
ms.openlocfilehash: ab5c16995a2d2bad6e44f0f9d1187ca3d66be1b6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "81114253"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>A klasszikus riasztási szabályok áttelepítéséhez használja az önkéntes áttelepítési eszközt

Amint azt [korábban bejelentettük](monitoring-classic-retirement.md), a Azure monitor klasszikus riasztásai 2019 szeptemberében megszűnnek (eredetileg 2019. július). Az áttelepítési eszköz a Azure Portalban olyan ügyfelek számára érhető el, akik klasszikus riasztási szabályokat használnak, és magukat a migrációt szeretnék elindítani. Ez a cikk azt ismerteti, hogyan használható az áttelepítési eszköz a klasszikus riasztási szabályok önkéntes áttelepítéséhez, mielőtt az automatikus áttelepítés 2019 szeptemberében megkezdődik.

> [!NOTE]
> Az áttelepítési eszköz késése miatt a klasszikus riasztások áttelepítésének lejárati dátuma [2019 augusztus 31-ig](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) , az eredetileg bejelentett, 2019. június 30-ig.

## <a name="benefits-of-new-alerts"></a>Az új riasztások előnyei

A klasszikus riasztásokat a Azure Monitor új, egyesített riasztása váltja fel. Az új riasztások platform a következő előnyöket nyújtja:

- [Számos további Azure-szolgáltatáshoz több](alerts-metric-near-real-time.md#metrics-and-dimensions-supported)többdimenziós mérőszámot is felhasználhat.
- Az új metrikai riasztások támogatják a [több erőforrásból álló riasztási szabályokat](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor) , amelyek nagy mértékben csökkentik a sok szabály felügyeletének terhét.
- Az egyesített értesítési mechanizmus, amely a következőket támogatja:
  - [Műveleti csoportok](action-groups.md), moduláris értesítési mechanizmus, amely minden új riasztási típussal működik (metrika, napló és tevékenység napló).
  - Új értesítési mechanizmusok, például SMS, Voice és ITSM-csatoló.
- Az [egységes riasztási élmény](alerts-overview.md) egy helyre helyezi az összes riasztást a különböző jelek (metrika, napló és tevékenység napló) alapján.

## <a name="before-you-migrate"></a>Migrálás előtt

Az áttelepítési folyamat a klasszikus riasztási szabályokat új, egyenértékű riasztási szabályokra konvertálja, és műveleti csoportokat hoz létre. Az előkészítés során vegye figyelembe a következő szempontokat:

- Az értesítési hasznos adatok formátuma és az új riasztási szabályok létrehozásához és kezeléséhez szükséges API-k eltérnek a klasszikus riasztási szabályoktól, mivel azok több funkciót is támogatnak. [Ismerje meg, hogyan készítheti elő az áttelepítést](alerts-prepare-migration.md).

- Néhány klasszikus riasztási szabály nem telepíthető át az eszköz használatával. [Megtudhatja, hogy mely szabályok nem telepíthetők át, és mire használhatók](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated).

    > [!NOTE]
    > Az áttelepítési folyamat nem befolyásolja a klasszikus riasztási szabályok kiértékelését. A rendszer továbbra is futtatja és riasztásokat küld, amíg át nem telepíti őket, és az új riasztási szabályok érvénybe lépnek.

## <a name="how-to-use-the-migration-tool"></a>A migrálási eszköz használata

A klasszikus riasztási szabályok áttelepítésének elindításához a Azure Portal hajtsa végre az alábbi lépéseket:

1. A [Azure Portal](https://portal.azure.com)területen válassza a **figyelés**lehetőséget.

1. Válassza a **riasztások**lehetőséget, majd a **riasztási szabályok kezelése** vagy a **klasszikus riasztások megtekintése**lehetőséget.

1. Válassza az áttelepítés **új szabályokra** lehetőséget az áttelepítési kezdőlapra való ugráshoz. Ezen az oldalon az összes előfizetésének és a Migrálás állapotának listája látható:

    ![áttelepítés – kiszállás](media/alerts-migration/migration-landing.png "Szabályok áttelepíthetők")

    Az eszköz használatával áttelepíthető összes előfizetés készként van megjelölve az **áttelepítésre**.

    > [!NOTE]
    > Az áttelepítési eszköz a klasszikus riasztási szabályokat használó összes előfizetés fázisában van. A bevezetés korai fázisaiban előfordulhat, hogy egyes előfizetések nem készen állnak az áttelepítésre.

1. Válasszon ki egy vagy több előfizetést, majd válassza az **áttelepítés előnézete**lehetőséget.

    Az eredményül kapott oldal megjeleníti a klasszikus riasztási szabályok részleteit, amelyek egy adott előfizetéshez lesznek áttelepítve. Az **előfizetéshez tartozó áttelepítési adatok letöltésével** is kiválaszthatja a részleteket CSV formátumban.

    ![áttelepítés – előzetes verzió](media/alerts-migration/migration-preview.png "Áttelepítés előzetes verziója")

1. Egy vagy több e-mail-címet kell megadnia az áttelepítési állapot értesítéséhez. E-mailt fog kapni, ha az áttelepítés befejeződik, vagy ha bármilyen műveletre szükség van.

1. Válassza az **áttelepítés indítása**lehetőséget. Olvassa el a megerősítő párbeszédpanelen megjelenő információkat, és erősítse meg, hogy készen áll az áttelepítési folyamat elindítására.

    > [!IMPORTANT]
    > Miután elindította az áttelepítést egy előfizetéshez, nem szerkesztheti és nem hozhatja létre klasszikus riasztási szabályokat az adott előfizetéshez. Ez a korlátozás biztosítja, hogy a klasszikus riasztási szabályok módosítása ne vesszenek el az új szabályokba való áttelepítés során. Bár a klasszikus riasztási szabályok nem módosíthatók, a rendszer továbbra is futtatja, és a riasztásokat csak az áttelepítés előtt küldi el. Az előfizetéshez tartozó áttelepítés befejezése után már nem használhat klasszikus riasztási szabályokat.

    ![áttelepítés – megerősítés](media/alerts-migration/migration-confirm.png "Áttelepítés megkezdésének megerősítése")

1. Ha az áttelepítés befejeződik, vagy ha szükséges, a rendszer e-mailt fog kapni a korábban megadott címekről. Azt is megteheti, hogy rendszeresen ellenőrzi az állapotot az áttelepítés kezdőlapján a portálon.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="why-is-my-subscription-listed-as-not-ready-for-migration"></a>Miért van felsorolva az előfizetésem, mert nem áll készen az áttelepítésre?

Az áttelepítési eszköz az ügyfelek számára fázisban van. A korai fázisokban előfordulhat, hogy az előfizetések többsége vagy mindegyike **nem áll készen az áttelepítésre**. 

Ha egy előfizetés készen áll az áttelepítésre, az előfizetés tulajdonosa egy e-mail-üzenetet kap arról, hogy az eszköz elérhető lesz. Figyelje meg ezt az üzenetet.

### <a name="who-can-trigger-the-migration"></a>Kik indíthatják el az áttelepítést?

Azok a felhasználók, akik az előfizetés szintjén hozzárendelt figyelési közreműködő szerepkörrel rendelkeznek, képesek elindítani az áttelepítést. [További információ az áttelepítési folyamat szerepköralapú Access Controláról](alerts-understand-migration.md#who-can-trigger-the-migration).

### <a name="how-long-will-the-migration-take"></a>Mennyi időt vesz igénybe az áttelepítés?

Az áttelepítés a legtöbb előfizetéshez egy órán belül befejeződik. Nyomon követheti az áttelepítés folyamatát az áttelepítés kezdőlapján. Az áttelepítés során ügyeljen arra, hogy a riasztások továbbra is a klasszikus riasztások rendszerében vagy az újat futtassanak.

### <a name="what-can-i-do-if-i-run-into-a-problem-during-migration"></a>Mi a teendő, ha az áttelepítés során problémát tapasztalok?

Az áttelepítés során esetlegesen felmerülő problémákkal kapcsolatos segítségért tekintse meg a [hibaelhárítási útmutatót](alerts-understand-migration.md#common-problems-and-remedies) . Ha bármilyen műveletre van szükség az áttelepítés befejezéséhez, értesítést kap az eszköz beállításakor megadott e-mail-címekről.

## <a name="next-steps"></a>További lépések

- [Előkészületek a migráláshoz](alerts-prepare-migration.md)
- [A migrálási eszköz működésének ismertetése](alerts-understand-migration.md)
