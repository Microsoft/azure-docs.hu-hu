---
title: Keresések mentése és adategységek rögzítése Azure Data Catalog
description: Útmutató a Azure Data Catalog képességeinek kiemeléséhez az adatforrások és adategységek későbbi használatra való mentéséhez.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 52a8d8fcd0b9b9ee1ff918ead250c88b83f86cb7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674835"
---
# <a name="save-searches-and-pin-data-assets-in-azure-data-catalog"></a>Keresések mentése és adategységek rögzítése Azure Data Catalog

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

## <a name="introduction"></a>Bevezetés
Azure Data Catalog az adatforrás-felderítés funkcióit biztosítja. Gyorsan kereshet és szűrheti a katalógust, hogy megkeresse az adatforrásokat, és megértse a kívánt célokat, így könnyebben megtalálhatja a feladathoz kapcsolódó megfelelő adatok körét.

De mi a teendő, ha rendszeresen ugyanazt az adatmennyiséget kell használnia? És mi a teendő, ha Ön és más felhasználók rendszeresen járulnak hozzá az Ön tudomásához a katalógusban szereplő adatforrásokhoz? Ezekben az esetekben az azonos keresések ismételt kibocsátása nem hatékony. A mentett keresés és a rögzített adategységek segíthetnek.

## <a name="saved-searches"></a>Mentett keresések
A Data Catalogban mentett keresés újrafelhasználható, felhasználónkénti keresési definíciót használ. Megadhat egy keresést, beleértve a keresési kifejezéseket, a címkéket és más szűrőket, majd mentheti azt. A mentett keresési definíciót később újra futtatva visszaállíthatja a keresési feltételeknek megfelelő adategységeket.

### <a name="create-a-saved-search"></a>Mentett keresés létrehozása
Mentett keresés létrehozásához tegye a következőket:
1. A Azure Data Catalog portál **aktuális keresési** ablakában kattintson a **Mentés** gombra. 

    ![Aktuális keresési beállítások mentés hivatkozása](./media/data-catalog-how-to-save-pin/01-save-option.png) 

2. Adja meg az újra felhasználni kívánt keresési feltételeket, majd kattintson a **Mentés** gombra.

    ![Aktuális keresési beállítások mentett keresési neve](./media/data-catalog-how-to-save-pin/02-name.png)

3. Amikor a rendszer kéri, adja meg a mentett keresés nevét. Válasszon egy hasznos nevet, amely leírja a keresés által visszaadott adategységeket.

### <a name="manage-saved-searches"></a>Mentett keresések kezelése
Egy vagy több Keresés mentése után az **aktuális keresőmező** alatt megjelenik egy **mentett keresések** lehetőség. A lista kibontásakor Az összes mentett keresés megjelenik.

 ![Data Catalog – mentett keresések listája](./media/data-catalog-how-to-save-pin/03-list.png)

Végezze el az alábbi műveletek bármelyikét:

* A keresés végrehajtásához válasszon egy mentett keresést a listában.

* A mentett keresések kezelési beállításainak listájának megtekintéséhez kattintson a keresés neve melletti lefelé mutató nyílra.

    ![A mentett keresések kezelésének lehetőségei](./media/data-catalog-how-to-save-pin/04-managing.png)

* A mentett keresés új nevének megadásához válassza az **Átnevezés** lehetőséget. A keresési definíció nem módosul.

* Ha el szeretné távolítani a mentett keresést a listából, válassza a **Törlés** lehetőséget, majd erősítse meg a törlést.

* A mentett keresés alapértelmezett keresésként való megjelöléséhez válassza a **Mentés alapértelmezettként** lehetőséget. Ha "üres" keresést végez a Azure Data Catalog kezdőlapján, a rendszer az alapértelmezett keresést hajtja végre. Emellett az alapértelmezett keresésként megjelölt keresés a **mentett keresések** listájának tetején jelenik meg.

### <a name="organizational-saved-searches"></a>Szervezet által mentett keresések
A szervezet minden felhasználója a saját használatra mentheti a kereséseket. Data Catalog rendszergazdák a szervezeten belüli összes felhasználó keresését is menthetik. Ha a rendszergazdák egy keresést mentenek, a **vállalaton belül egy megosztás jelenik meg** . A beállítás kiválasztásával megoszthatja a szervezet összes felhasználójának mentett keresését.

 ![Data Catalog – szervezet által mentett keresések](./media/data-catalog-how-to-save-pin/08-organizational-saved-search.png)

## <a name="pinned-data-assets"></a>Rögzített adategységek
A mentett keresések segítségével mentheti és újrahasznosíthatja a keresési definíciókat. A keresés által visszaadott adategységek az idő múlásával változhatnak a katalógus tartalmának változásakor. Az adategységek rögzítésekor explicit módon azonosíthatja a konkrét adategységeket, hogy azok könnyebben hozzáférhessenek a kereséshez anélkül, hogy a keresést kellene használnia.

Az adategységek rögzítése egyszerű. Ha az adategységet fel szeretné venni a rögzített listára, egyszerűen kattintson a **rögzítés** ikonra. Az ikon megjelenik az objektum csempe sarkában a csempe nézetben, valamint a Azure Data Catalog portál listájának bal szélső oszlopában.

![Data Catalog – az adategység PIN-kódjának ikonja](./media/data-catalog-how-to-save-pin/05-pinning.png)

Az adategységek kitűzése ugyanilyen egyszerű. A kiválasztott objektum beállításának váltásához egyszerűen kattintson a **rögzítés** ikonra.

![Data Catalog – az adategység rögzítésének ikonja](./media/data-catalog-how-to-save-pin/06-unpinning.png)

## <a name="the-my-assets-section"></a>A saját eszközök szakasz
A Data Catalog portál kezdőlapján szerepel a **saját eszközök** szakasz, amely az aktuális felhasználó számára érdekes eszközöket jelenít meg. Ez a szakasz a rögzített eszközöket és a mentett kereséseket is tartalmazza.

![A Kezdőlap saját eszközök szakasza](./media/data-catalog-how-to-save-pin/07-my-assets.png)

## <a name="summary"></a>Összefoglalás
Azure Data Catalog olyan képességeket biztosít, amelyek megkönnyítik a szükséges adatforrások felderítését, így Ön és más szervezeti tagok kevesebb időt keresnek az adatkeresésre, és több időt is igénybe vehetnek. A mentett keresések és a rögzített adategységek ezekre az alapvető képességekre épülnek, így a felhasználók könnyen azonosíthatják azokat az adatforrásokat, amelyeket többször is dolgoznak.
