---
title: Az üzleti Szószedet beállítása Azure Data Catalog
description: 'Útmutató: az üzleti Szószedet kiemelése Azure Data Catalog a regisztrált adategységek címkézéséhez és használatához közös üzleti szókincs definiálására és használatára.'
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 6137072b443d54dfc63c2be69e20228d428ad1b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674716"
---
# <a name="set-up-the-business-glossary-for-governed-tagging"></a>Az üzleti Szószedet beállítása az irányított címkézéshez

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

## <a name="introduction"></a>Bevezetés

Azure Data Catalog lehetővé teszi az adatforrások felderítését, így könnyen felderítheti és értelmezheti az elemzéshez és a döntések végrehajtásához szükséges adatforrásokat. Ezek a képességek a legnagyobb hatással lehetnek az elérhető adatforrások legszélesebb körének megtalálása és megismerése érdekében.

Egy Data Catalog funkció, amely elősegíti az adategységek jobb megértését. A címkézés használatával a kulcsszavakat társíthatja egy objektumhoz vagy egy oszlophoz, ami viszont megkönnyíti az eszköz felderítését keresés vagy Tallózás útján. A címkézés az eszköz kontextusának és szándékának könnyebb megismerését is segíti.

A címkézés azonban időnként a saját problémáit is okozhatja. Néhány példa a címkézés által bevezethető problémákra:

* A rövidítések használata egyes eszközökön és kibővített szöveg másokon. Ez a következetlenség akadályozza az adategységek felderítését, annak ellenére, hogy az azonos címkével rendelkező eszközöket címkézték.
* A szövegkörnyezettől függően lehetséges variációk a jelentésekben. Az ügyfél adatkészletének *bevétele* nevű címke például az ügyfél bevételét jelentheti, de a negyedéves értékesítési adatkészletek ugyanazon címkéje a vállalat negyedéves bevételét is jelentheti.  

Ezen és más hasonló problémák megoldásához Data Catalog tartalmaz egy üzleti szószedetet.

A Data Catalog üzleti Szószedet használatával a szervezet a kulcsfontosságú üzleti kifejezéseket és azok definícióit is dokumentálhatja közös üzleti szókincs létrehozásához. Ez a szabályozás lehetővé teszi az adatfelhasználás egységességét a szervezeten belül. Miután meghatározta a kifejezést az üzleti szószedetben, azt hozzárendelheti egy adategységhez a katalógusban. Ez a megközelítés, amely az *irányadó címkézés*, ugyanaz a megközelítés, mint a címkézés.

## <a name="glossary-availability-and-privileges"></a>Szószedet elérhetősége és jogosultságai

Az üzleti Szószedet csak a Azure Data Catalog standard kiadásában érhető el. A Data Catalog ingyenes kiadása nem tartalmaz szószedetet, és nem biztosít képességeket az irányított címkézéshez.

Az üzleti szószedetet a Data Catalog portál navigációs menüjének **Szószedet** lehetőségével érheti el.  

![Data Catalog – hozzáférés az üzleti szószedethez](./media/data-catalog-how-to-business-glossary/01-portal-menu.png)

Data Catalog a rendszergazdák és a Szószedet-rendszergazdák szerepkör tagjai létrehozhatják, szerkeszthetik és törölhetik a Szószedet feltételeit az üzleti szószedetben. Az összes Data Catalog felhasználó megtekintheti a kifejezések definícióit, és címkézheti az eszközöket a Szószedet feltételeit követve.

![Data Catalog – új Szószedet-kifejezés hozzáadása](./media/data-catalog-how-to-business-glossary/02-new-term.png)

## <a name="creating-glossary-terms"></a>Szószedet-kifejezések létrehozása

Data Catalog rendszergazdák és Szószedet-rendszergazdák létrehozhatnak szószedeti feltételeket az **új kifejezés** gombra kattintva. Minden Szószedet-kifejezés a következő mezőket tartalmazza:

* A kifejezés üzleti definíciója
* Az eszköz vagy oszlop kívánt használati vagy üzleti szabályait rögzítő Leírás
* Azon érdekelt felek listája, akik ismerik a legtöbbet a kifejezéssel kapcsolatban
* A szülő kifejezés, amely meghatározza azt a hierarchiát, amelyben a kifejezést rendszerezik

## <a name="glossary-term-hierarchies"></a>Szószedet-kifejezési hierarchiák

A Data Catalog üzleti Szószedet használatával a vállalatok a feltételek hierarchiájának megfelelően leírják üzleti szókincsét, és olyan kifejezések besorolását is létrehozhatják, amelyek az üzleti besorolást jobban reprezentálják.

A kifejezésnek egyedinek kell lennie a hierarchia adott szintjén. Az ismétlődő nevek nem engedélyezettek. A hierarchiában lévő szintek száma nincs korlátozva, de a hierarchiák gyakran könnyebben megérthetők, ha három vagy kevesebb szint van.

A hierarchiák használata az üzleti szószedetben nem kötelező. Ha a szülő kifejezés mezőt üresen hagyja a Szószedet kifejezésben, a kifejezés a szószedetben a kifejezések egy egyszerű (nem hierarchikus) listáját hozza létre.  

## <a name="tagging-assets-with-glossary-terms"></a>Eszközök címkézése a Szószedet feltételeivel

Miután definiálta a szószedetet a katalógusban, a címkézett eszközök címkézése úgy van optimalizálva, hogy a Szószedet felhasználó típusú címkét keressen. A Data Catalog-portál megjeleníti a megfelelő Szószedet-kifejezések listáját, amely közül választhat. Ha a felhasználó a listából kiválasztja a Szószedet kifejezését, a kifejezést címkeként adja hozzá a rendszer (más néven Szószedet-címke). A felhasználó úgy is dönthet, hogy új címkét hoz létre egy olyan kifejezés beírásával, amely nem szerepel a szószedetben (más néven felhasználói címke).

![Adategység címkézett egy felhasználói címkével és két Szószedet címkével](./media/data-catalog-how-to-business-glossary/03-tagged-asset.png)

> [!NOTE]
> A felhasználók címkéi csak az Data Catalog ingyenes kiadásában támogatott címke típusúak.

### <a name="hover-behavior-on-tags"></a>Hover-viselkedés a címkéken

A Data Catalog-portálon a címkék két típusa vizuálisan eltérő, és különböző hover-viselkedéseket mutat be. Amikor egy felhasználói címke fölé viszi a mutatót, megtekintheti a címke szövegét és a címkét hozzáadó felhasználót vagy felhasználókat. Ha egy Szószedetbeli címkére mutat, megjelenik a Szószedet kifejezés definíciója is, valamint egy hivatkozás, amely megnyitja az üzleti szószedetet a kifejezés teljes definíciójának megtekintéséhez.

### <a name="search-filters-for-tags"></a>Címkék keresési szűrői

A Szószedetbeli címkék és a felhasználói címkék egyaránt kereshetők, és szűrőként is alkalmazhatók a keresés során.

## <a name="summary"></a>Összefoglalás

A Azure Data Catalog üzleti szószedetének használatával és a szabályozott címkézéssel lehetővé teszi az adategységek egységes módon történő azonosítását, kezelését és felderítését. Az üzleti Szószedet a szervezet tagjai által az üzleti szókincs megismerésére is képes. A Szószedet Emellett támogatja az értelmes metaadatok rögzítését is, ami leegyszerűsíti az eszközök felderítését és megértését.

## <a name="next-steps"></a>Következő lépések

* [REST API dokumentáció vállalati Szószedet-műveletekhez](/rest/api/datacatalog/data-catalog-glossary)
