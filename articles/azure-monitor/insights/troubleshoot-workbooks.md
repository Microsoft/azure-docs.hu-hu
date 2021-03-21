---
title: A munkafüzet-alapú adatáttekintések Azure Monitor hibaelhárítása
description: Hibaelhárítási útmutatót biztosít Azure Monitor a Redis-hez hasonló szolgáltatások, például a Azure Key Vault, az Azure CosmosDB, az Azure Storage és az Azure cache szolgáltatáshoz.
services: azure-monitor
ms.author: lagayhar
author: lgayhardt
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: a823b5e2ae31e62ff6d876f0b3468953c0ed9c35
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100587153"
---
# <a name="troubleshooting-workbook-based-insights"></a>Munkafüzet-alapú adatellenőrzések hibaelhárítása

Ez a cikk segítséget nyújt a Azure Monitor munkafüzet-alapú elemzések használatakor felmerülő gyakori problémák diagnosztizálásában és hibaelhárításában.


## <a name="why-can-i-only-see-200-resources"></a>Miért csak a 200-erőforrások láthatók

A kiválasztott erőforrások számának korlátja 200, a kiválasztott előfizetések számától függetlenül.

## <a name="what-happens-when-i-click-on-a-recently-pinned-tile-in-the-dashboard"></a>Mi történik, ha az irányítópulton egy nemrég rögzített csempére kattintok

* Ha a csempén bárhová kattint, azzal a lapra kerül, ahol a csempét rögzítette. Ha például rögzít egy gráfot az "áttekintés" lapon, majd az irányítópulton a csempére kattint, megnyílik az alapértelmezett nézet, azonban ha a saját mentett másolási diagramját rögzíti, megnyílik a mentett másolat nézete.
* A cím bal felső részén található szűrő ikon a "csempe beállításainak konfigurálása" lapot nyitja meg.
* A jobb felső sarokban található ellipszis ikonra kattintva megadhatja a "title-adatok testreszabása", a "Testreszabás", a "frissítés" és az "Eltávolítás az irányítópultról" lehetőséget.

## <a name="what-happens-when-i-save-a-workbook"></a>Mi történik a munkafüzet mentésekor

* A munkafüzet mentésekor lehetővé teszi a munkafüzet új másolatának létrehozását a szerkesztéssel, és módosítja a címet. A Mentés nem írja felül a munkafüzetet, az aktuális munkafüzet mindig az alapértelmezett nézet lesz.
* Egy nem **mentett** munkafüzet csak az alapértelmezett nézet.

## <a name="why-dont-i-see-all-my-subscriptions-in-the-portal"></a>Miért nem látom az összes előfizetést a portálon

A portálon csak a kiválasztott előfizetések adatai jelennek meg a portál indításakor. A kiválasztott előfizetések módosításához válassza a jobb felső sarokban található jegyzetfüzetet, és kattintson a szűrő ikonra. Ez a beállítás a **címtár + előfizetések** fület jeleníti meg.

![Címtár és előfizetés](./media/storage-insights-overview/fqa3.png)

## <a name="what-is-time-range"></a>Mi az Időtartomány?

Az időtartomány egy adott időkeretből származó adatokra mutat. Ha például az időtartomány 24 óra, akkor az elmúlt 24 órában megjelenített adatok láthatók.

## <a name="what-is-time-granularity-time-grain"></a>Mi az idő részletessége (Time Grain)

Az idő részletessége két adatpont közötti időeltérés. Ha például az időtartam 1 másodpercre van állítva, ami azt jelenti, hogy a rendszer másodpercenként gyűjt metrikákat.

## <a name="what-is-the-time-granularity-once-we-pin-any-part-of-the-workbooks-to-a-dashboard"></a>Mi az idő részletessége, ha a munkafüzetek bármely részét egy irányítópultra rögzítjük

Az alapértelmezett időrészletesség beállítása automatikus, jelenleg nem módosítható.

## <a name="how-do-i-change-the-timespan-time-range-of-the-workbook-step-on-my-dashboard"></a>Hogyan az irányítópulton a munkafüzet TimeSpan/időtartományának módosítása

Alapértelmezés szerint az irányítópult csempén lévő TimeSpan/időtartomány 24 órára van állítva, hogy a jobb felső sarokban lévő három pontra mutasson, majd válassza a **csempék testreszabása** elemet, jelölje be az irányítópult időbeállításainak felülbírálása jelölőnégyzetet, majd válasszon ki egy TimeSpan a legördülő menüből.  

![Válassza ki a csempe jobb felső sarkában található három pontot, majd válassza az adatelemek testreszabása lehetőséget.](./media/storage-insights-overview/fqa-data-settings.png)

![A csempe beállításainak konfigurálása lapon válassza a TimeSpan legördülő menüt a TimeSpan/időtartomány módosításához.](./media/storage-insights-overview/fqa-timespan.png)

## <a name="how-do-i-change-the-title-of-the-workbook-or-a-workbook-step-i-pinned-to-a-dashboard"></a>Hogyan módosíthatja a munkafüzet címét vagy az irányítópultra rögzített munkafüzet lépéseit.

Az irányítópultra rögzített munkafüzet vagy munkafüzet lépésének címe megőrzi a munkafüzetben megegyező nevet. A cím módosításához mentenie kell a munkafüzet saját példányát. Ezután a Save (Mentés) gombra kattintva megadhatja a munkafüzet nevét.

![A felső Mentés elemre kattintva mentheti a munkafüzet másolatát, és módosíthatja annak nevét](./media/storage-insights-overview/fqa-change-workbook-name.png)

Ha módosítani szeretné a mentett munkafüzet egyik lépésének a nevét, válassza a szerkesztés lehetőséget a lépés alatt, majd válassza ki a kívánt felszerelést a beállítások alján.

![A munkafüzet alján található Szerkesztés gombra kattintva megnyithatja a beállítások beállításokat a ](./media/storage-insights-overview/fqa-edit.png)
 ![ Beállítások területen válassza ki az alsó sebességfokozatot, hogy módosítani tudja a lépés nevét](./media/storage-insights-overview/fqa-change-name.png)

## <a name="next-steps"></a>Következő lépések

További információ a forgatókönyvekhez készült munkafüzetek támogatásáról, a meglévő jelentések új és testre szabásának létrehozásáról, valamint az [interaktív jelentések Azure monitor-munkafüzetekkel való létrehozásának](../visualize/workbooks-overview.md)áttekintéséről.
