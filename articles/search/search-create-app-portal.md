---
title: Bemutató alkalmazás létrehozása Azure Portal
titleSuffix: Azure Cognitive Search
description: Futtassa a demó alkalmazás létrehozása (előzetes verzió) varázslót, amellyel HTML-lapokat és parancsfájlokat hozhat létre egy operatív webalkalmazáshoz. Az oldalon található egy keresősáv, az eredmények, az oldalsáv és a typeahead támogatása.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 01/23/2021
ms.openlocfilehash: 590afe4c396942c5179826cd831908e37f48c3e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98745750"
---
# <a name="quickstart-create-a-demo-app-in-the-portal-azure-cognitive-search"></a>Rövid útmutató: bemutató alkalmazás létrehozása a portálon (Azure Cognitive Search)

A Azure Portal a **bemutató alkalmazás létrehozása** varázslóval létrehozhat egy letölthető, "localhost" stílusú webes alkalmazást, amely böngészőben fut. A konfigurációtól függően a létrehozott alkalmazás első használatban működik, és egy élő írásvédett kapcsolattal rendelkezik egy távoli indexhez. Egy alapértelmezett alkalmazás tartalmazhatja a keresősáv, az eredmények, a Sidebar-szűrők és a typeahead támogatását.

A bemutató alkalmazás segítségével megjelenítheti, hogyan fog működni az index egy ügyfélalkalmazás számára, de nem éles környezetekhez készült. Az ügyfélalkalmazások olyan biztonságot, hibakezelés és üzemeltetési logikát tartalmaznak, amelyet a generált HTML-oldal nem biztosít. Ha készen áll egy ügyfélalkalmazás létrehozására, tekintse [meg az első keresőalkalmazás létrehozása a .net SDK](tutorial-csharp-create-first-app.md) -val című témakört a következő lépésekhez.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, a következőkkel kell rendelkeznie:

+ Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/).

+ Egy Azure Cognitive Search szolgáltatás. [Hozzon létre egy szolgáltatást](search-create-service-portal.md) , vagy [keressen egy meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) a jelenlegi előfizetése alatt. Ehhez a rövid útmutatóhoz ingyenes szolgáltatást is használhat. 

+ [Microsoft Edge (legújabb verzió)](https://www.microsoft.com/edge) vagy Google Chrome.

+ A generált alkalmazás alapjaként használandó [keresési index](search-what-is-an-index.md) . 

  Ez a rövid útmutató a beépített ingatlan-mintaadatok és-indexet használja, mivel miniatűr képekkel rendelkezik (a varázsló támogatja a képek hozzáadását az eredmények laphoz). Az ebben a gyakorlatban használt index létrehozásához futtassa az **adat importálása** varázslót, és válassza az *Realestate-US-Sample* adatforrást.

  :::image type="content" source="media/search-create-app-portal/import-data-realestate.png" alt-text="adatforrások lapja mintaadatok esetén" border="false":::

Ha az index használatra kész, lépjen tovább a következő lépésre.

## <a name="start-the-wizard"></a>A varázsló elindítása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure-fiókjával.

1. [Keresse meg a keresési szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) , és az Áttekintés oldalon, az oldal közepén található hivatkozások közül válassza az **indexek** lehetőséget. 

1. Válassza a *Realestate-US-Sample-index* elemet a meglévő indexek listájából.

1. A varázsló elindításához az index lap tetején kattintson a **bemutató alkalmazás létrehozása (előzetes verzió)** elemre.

1. A varázsló első lapján jelölje be a **több eredetű erőforrás-megosztás (CORS) engedélyezése** lehetőséget az CORS támogatásának hozzáadásához az index definíciójában. Ez a lépés nem kötelező, de a helyi webalkalmazás nem csatlakozik a távoli indexhez.

## <a name="configure-search-results"></a>Keresési eredmények konfigurálása

A varázsló alapvető elrendezést biztosít a megjelenített keresési eredmények számára, amely tartalmazza a miniatűr képek, a cím és a Leírás területét. Az egyes elemek biztonsági mentése az index egyik mezője, amely az adatokat biztosítja. 

1. A miniatűr mezőben válassza ki a *miniatűr* mezőt a *Realestate-US-Sample* indexben. Ez a minta egy *miniatűr* nevű mezőben tárolt, URL-címekkel elválasztott lemezképek formájában tartalmaz képminiatűröket. Ha az index nem rendelkezik rendszerképekkel, hagyja üresen ezt a mezőt.

1. A title (cím) mezőben válasszon ki egy mezőt, amely az egyes dokumentumok egyediségét közvetíti. Ebben a példában a tőzsdei azonosító egy ésszerű kijelölés.

1. A Description (Leírás) mezőben válasszon ki egy olyan mezőt, amely segítséget nyújthat valakinek annak eldöntésében, hogy az adott dokumentumra kattint-e.

   :::image type="content" source="media/search-create-app-portal/configure-results.png" alt-text="Mintaadatok eredményeinek konfigurálása" border="false":::

## <a name="add-a-sidebar"></a>Oldalsáv hozzáadása

A Search szolgáltatás támogatja a csiszolt navigációt, amely gyakran oldalsávként jelenik meg. Az aspektusok szűrhető és sokrétű mezőkön alapulnak, az index sémában kifejezett módon.

Az Azure Cognitive Searchban a csiszolatlan navigáció egy halmozott szűrési élmény. Egy kategórián belül több szűrő kiválasztásával kibővíti az eredményeket (például kiválasztja a Seattle és a Bellevue lehetőséget a városon belül). A kategóriák között több szűrő kiválasztásával szűkíti az eredményeket.

> [!TIP]
> A teljes index séma a portálon tekinthető meg. Keresse meg az **index Definition (JSON)** hivatkozást az egyes indexek áttekintő oldalán. A csiszolatlan navigálásra jogosult mezők "szűrhetők: true" és "sokrétű: true" attribútumokkal rendelkeznek.

1. A varázslóban válassza az **oldalsáv** fület az oldal tetején. Ekkor megjelenik az összes olyan mező, amely szűrhetőként és kategorizálható van megjelölve az indexben.

1. Fogadja el a sokoldalú mezők aktuális kijelölését, és folytassa a következő oldalra.

## <a name="add-typeahead"></a>Typeahead hozzáadása

A typeahead funkció automatikus kiegészítés és lekérdezési javaslatok formájában érhető el. A varázsló támogatja a lekérdezési javaslatokat. A felhasználó által megadott billentyűleütés-bemenetek alapján a Search szolgáltatás a "befejezett" lekérdezési karakterláncok listáját adja vissza, amelyek bemenetként választhatók ki.

A javaslatok meghatározott mező-definíciókban engedélyezettek. A varázsló lehetővé teszi, hogy beállítsa, hogy mennyi információ szerepeljen a javaslatban. 

Az alábbi képernyőfelvételen a varázsló azon beállításai láthatók, amelyek az alkalmazás megjelenített oldalával vannak összefoglalva. Megtekintheti a mezők kiválasztásának módját, valamint azt, hogy a rendszer hogyan használja a "mezőnév megjelenítése" kifejezést a javaslaton belüli címkézés befoglalására vagy kizárására.

:::image type="content" source="media/search-create-app-portal/suggestions.png" alt-text="Lekérdezési javaslat konfigurálása":::

## <a name="add-suggestions"></a>Javaslatok hozzáadása

A javaslatok a keresési mezőhöz csatolt automatizált lekérdezési kérésekre vonatkoznak. Cognitive Search támogatja a kettőt: egy részben megadott keresési kifejezés *automata befejezése* , valamint *javaslatok* a lehetséges egyezési dokumentumok legördülő listájára.

A varázsló támogatja a javaslatokat, és a javasolt eredmények megadására szolgáló mezők az [`Suggesters`](index-add-suggesters.md) indexben található szerkezetből származnak:

```JSON
  "suggesters": [
    {
      "name": "sg",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": [
        "number",
        "street",
        "city",
        "region",
        "postCode",
        "tags"
      ]
```

1. A varázslóban válassza az oldal tetején található **javaslatok** fület. Ekkor megjelenik az összes olyan mező, amely az index sémában az Ajánlói szolgáltatóként van kijelölve.

1. Fogadja el az aktuális kijelölést, és folytassa a következő oldallal.

## <a name="create-download-and-execute"></a>Létrehozás, letöltés és végrehajtás

1. A HTML-fájl létrehozásához kattintson az oldal alján található **bemutató létrehozása** gombra.

1. Ha a rendszer kéri, válassza az **alkalmazás letöltése** lehetőséget a fájl letöltéséhez.

1. Nyissa meg a fájlt, és kattintson a Keresés gombra. Ez a művelet végrehajt egy lekérdezést, amely lehet egy üres lekérdezés (), amely egy `*` tetszőleges eredményhalmazt ad vissza. Az oldalnak az alábbi képernyőképhez hasonlóan kell kinéznie. Adjon meg egy kifejezést, és használjon szűrőket a szűkített eredményekhez. 

Az alapul szolgáló index olyan fiktív, generált adatokból áll, amelyek a dokumentumok között duplikálva vannak, és a leírások időnként nem egyeznek meg a képpel. Az alkalmazások saját indexek alapján történő létrehozásakor több összetartó élményre számíthat.

:::image type="content" source="media/search-create-app-portal/run-app.png" alt-text="Az alkalmazás futtatása":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a saját előfizetésében dolgozik, érdemes az egyes projektek végén eldöntenie, hogy szüksége lesz-e még a létrehozott erőforrásokra. A továbbra is futó erőforrások költségekkel járhatnak. Az erőforrásokat törölheti egyesével, vagy az erőforráscsoport törlésével eltávolíthatja a benne lévő összes erőforrást is.

A bal oldali navigációs panelen a **minden erőforrás** vagy **erőforráscsoport** hivatkozás használatával megkeresheti és kezelheti az erőforrásokat a portálon.

Ha ingyenes szolgáltatást használ, ne feledje, hogy Ön legfeljebb három indexet, indexelő és adatforrást használhat. A portálon törölheti az egyes elemeket, hogy a korlát alatt maradjon. 

## <a name="next-steps"></a>Következő lépések

A bemutató alkalmazás a prototípusok esetében hasznos, mivel a végfelhasználói élményt szimulálhatja anélkül, hogy JavaScriptet vagy előtér-kódot kellene írnia. További információ az előtér-funkciókról:

> [!div class="nextstepaction"]
> [Dimenziós szűrő létrehozása](search-filters-facets.md)