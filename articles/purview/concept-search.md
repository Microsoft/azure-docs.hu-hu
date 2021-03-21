---
title: Az Azure hatáskörébe tartozó keresési funkciók ismertetése (előzetes verzió)
description: Ez a cikk azt ismerteti, hogyan teszi lehetővé az Azure hatáskörébe az adatfelderítést a keresési funkciókon keresztül.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: af8ec9e0aac38240c7da92edd614892ff65712e2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96552699"
---
# <a name="understand-search-features-in-azure-purview"></a>Az Azure hatáskörébe tartozó keresési funkciók ismertetése

Ez a cikk áttekintést nyújt az Azure hatáskörébe tartozó keresési élményről. A keresés a hatáskörébe tartozó alapvető platform-képesség, amely az adatfelderítési és az adatkezelési folyamatokat a szervezeten belül alkalmazza.

## <a name="search"></a>Keresés

A hatáskörébe tartozó keresési élményt felügyelt keresési index látja el. Az adatforrások a hatáskörébe való regisztrálása után a keresési szolgáltatás indexeli a metaadatokat, hogy az egyszerű felderítést engedélyezze. Az index a keresés szempontjából releváns képességeket biztosít, és több millió metaadat-eszköz lekérdezésével hajtja végre a keresési kérelmeket. A keresés segít felderíteni, értelmezni és használni az adatmennyiséget, hogy a lehető legtöbbet hozza ki belőle.

A hatáskörébe tartozó keresési funkció egy három szakaszból álló folyamat:

1. A keresőmezőbe a legutóbb használt kulcsszavakat és eszközöket tartalmazó előzmények láthatók.
1. Amikor elkezdi a billentyűleütések beírását, a keresés a megfelelő kulcsszavakat és eszközöket javasolja. 
1. A találatok oldal a megadott kulcsszónak megfelelő eszközökkel jelenik meg.

## <a name="reduce-the-time-to-discover-data"></a>Csökkentse az adatfelderítési időt

Az adatfelderítés az első lépés az adatelemzési és adatkezelési munkaterhelések számára az adatok felhasználói számára. Az adatfelderítés lehet időigényes, mert előfordulhat, hogy nem tudja, hol találja a kívánt adatmennyiséget. Előfordulhat, hogy az adatkeresés után is kétségei vannak, hogy megbízik-e az adatvédelemben, és függőségeket is felhasználhat. 

Az Azure-beli keresés célja, hogy felgyorsítsa az adatfelderítés folyamatát a kézmozdulatok megadásával, így gyorsan megtalálhatja a fontos adatmennyiséget.

## <a name="recent-search-and-suggestions"></a>Legutóbbi keresés és javaslatok

Sokszor több projekten is dolgozhat egyszerre. Annak érdekében, hogy könnyebben lehessen folytatni a korábbi projekteket, a hatáskörébe való keresés lehetővé teszi a legutóbbi keresési kulcsszavak és javaslatok megtekintését. A legutóbbi keresési előzményeket az **összes megtekintése** elemre kattintva is kezelheti a keresőmező legördülő menüjében.

## <a name="filters"></a>Szűrők

A szűrők (más néven *aspektusok*) a keresés kiegészítéseként szolgálnak. A szűrők a keresési eredmény lapon jelennek meg. A keresési eredmények oldalon található szűrők közé tartozik a besorolás, az érzékenységi címke, az adatforrás és a tulajdonosok. A felhasználók kiválaszthatják a szűrők adott értékeit, így csak a megfelelő adategységeket láthatják, és a keresési eredményeket a megfelelő eszközökre korlátozhatják.

## <a name="hit-highlighting"></a>Találatok kiemelése

A találatok oldalon található egyezési kulcsszavak kiemelve vannak, így könnyebben azonosítható, hogy miért adtak vissza egy adategységet a keresés. A kulcsszavas egyezés több mezőben is megjelenhet, például az adategység neve, leírása és tulajdonosa.

Lehet, hogy nem nyilvánvaló, hogy miért szerepel egy adategység a keresésben, még ha engedélyezve van a találatok kiemelése. A rendszer alapértelmezés szerint minden tulajdonságot keres. Ezért előfordulhat, hogy egy adategységet ad vissza egy oszlop szintű tulajdonság egyezése miatt. Mivel több felhasználó is láthatja az adategységeket saját besorolásokkal és leírásokkal, nem minden metaadat jelenik meg a keresési eredmények listájában.

## <a name="sort"></a>Rendezés

A felhasználók két lehetőség közül választhatnak a keresési eredmények rendezéséhez. Az objektum neve alapján vagy az alapértelmezett keresési relevancia szerint rendezhetők.

## <a name="search-relevance"></a>Keresés relevanciája

A relevancia az alapértelmezett rendezési sorrend a keresési eredmény oldalon. A keresés relevanciája megkeresi azokat a dokumentumokat, amelyek tartalmazzák a keresési kulcsszót (egy vagy több). A keresési kulcsszó számos példányát tartalmazó eszközök magasabb rangú. Emellett az egyéni pontozási mechanizmusok folyamatosan vannak hangolva a keresési relevancia javítása érdekében.

## <a name="next-steps"></a>Következő lépések

* [Gyors útmutató: Azure-beli hatáskörébe tartozó fiók létrehozása a Azure Portal](create-catalog-portal.md)
* [Gyors útmutató: Azure-beli hatáskörébe tartozó fiók létrehozása Azure PowerShell/Azure CLI használatával](create-catalog-powershell.md)
* [Rövid útmutató: a hatáskörébe Studio használata](use-purview-studio.md)
