---
title: Keresési Explorer lekérdezési eszköz a Azure Portal
titleSuffix: Azure Cognitive Search
description: Ebben a Azure Portal gyors útmutatóban a keresési ablak segítségével megismerheti a lekérdezési szintaxist, a lekérdezési kifejezéseket, vagy megvizsgálhat egy keresési dokumentumot. Keresési Explorer lekérdezési indexek az Azure Cognitive Searchban.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 01/12/2021
ms.openlocfilehash: e9607a71ed6b045ac704c43bf4ea54c9f181bbf4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98179775"
---
# <a name="quickstart-use-search-explorer-to-run-queries-in-the-portal"></a>Gyors útmutató: lekérdezések futtatása a portálon a keresési tallózó használatával

A **Search Explorer** egy beépített lekérdezési eszköz, amellyel lekérdezéseket lehet futtatni az Azure Cognitive Search keresési indexében. Ez az eszköz megkönnyíti a lekérdezési szintaxis, a lekérdezési vagy szűrési kifejezés tesztelését vagy az Adatfrissítés megerősítését, ha ellenőrzi, hogy létezik-e új tartalom az indexben.

Ez a rövid útmutató egy meglévő indexet használ a Search Explorer bemutatására. A kérelmek a [keresési REST API](/rest/api/searchservice/search-documents)alapján lettek kialakítva, és a VÁLASZOKAT részletes JSON-dokumentumként adja vissza.

## <a name="prerequisites"></a>Előfeltételek

A Kezdés előtt a következő előfeltételek vonatkoznak:

+ Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/).

+ Egy Azure Cognitive Search szolgáltatás. [Hozzon létre egy szolgáltatást](search-create-service-portal.md) , vagy [keressen egy meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) a jelenlegi előfizetése alatt. Ehhez a rövid útmutatóhoz ingyenes szolgáltatást is használhat. 

+ Ebben a rövid útmutatóban a *Realestate-US-Sample-index* használatos. A rövid útmutató [: index létrehozása](search-import-data-portal.md) az index létrehozásához az alapértelmezett értékek használatával. A Microsoft által üzemeltetett, beépített minta adatforrások (**Realestate-US-Sample**) biztosítják az adattárolást.

## <a name="start-search-explorer"></a>A Search Explorer indítása

1. A [Azure Portal](https://portal.azure.com)nyissa meg a keresési áttekintés lapot az irányítópulton, vagy keresse meg a [szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).

1. Nyissa meg a keresési Explorert a parancssorból:

   :::image type="content" source="media/search-explorer/search-explorer-cmd2.png" alt-text="Keresési Explorer parancs a portálon" border="true":::

    Vagy használja a beágyazott **keresési Explorer** fület egy megnyitott indexen:

   :::image type="content" source="media/search-explorer/search-explorer-tab.png" alt-text="Keresési Explorer lap" border="true":::

## <a name="unspecified-query"></a>Meghatározatlan lekérdezés

Ha először a tartalmat kívánja megtekinteni, hajtson végre egy üres keresést a kifejezés nélküli **Keresés** gombra kattintva. Az üres keresések első lekérdezésként hasznosak, mivel teljes dokumentumokat ad vissza, így áttekintheti a dokumentumok összeállítását. Üres keresés esetén a keresés nem történik meg, és a dokumentumok tetszőleges sorrendben lesznek visszaadva ( `"@search.score": 1` minden dokumentum esetében). Alapértelmezés szerint a rendszer 50-dokumentumokat ad vissza egy keresési kérelemben.

Üres keresés egyenértékű szintaxisa: `*` vagy `search=*` .
   
   ```http
   search=*
   ```

   **Results (Eredmények)**
   
   :::image type="content" source="media/search-explorer/search-explorer-example-empty.png" alt-text="Nem minősített vagy üres lekérdezési példa" border="true":::

## <a name="free-text-search"></a>Ingyenes szöveges keresés

A szabad formátumú lekérdezések operátorokkal vagy anélkül is hasznosak az egyéni alkalmazásokból az Azure-Cognitive Search küldött, felhasználó által definiált lekérdezések szimulálásához. A találatok között csak az index definíciójában **kereshetőként** megadott mezők lesznek beolvasva. 

Figyelje meg, hogy a keresési feltételek, például a lekérdezési feltételek vagy kifejezések megadásakor a keresési rangsor a lejátszási sorba kerül. Az alábbi példa egy ingyenes szöveges keresést mutat be.

   ```http
   Seattle apartment "Lake Washington" miele OR thermador appliance
   ```

   **Results (Eredmények)**

   A CTRL-F billentyűkombinációval adott feltételek alapján kereshet az eredmények között.

   :::image type="content" source="media/search-explorer/search-explorer-example-freetext.png" alt-text="Példa ingyenes szöveges lekérdezésre" border="true":::

## <a name="count-of-matching-documents"></a>Egyező dokumentumok száma 

Adja hozzá a **$Count = True** értéket az indexben található egyezések számának lekéréséhez. Üres keresés esetén a Count az indexben lévő dokumentumok teljes száma. Minősített keresés esetén a lekérdezés bemenetének megfelelő dokumentumok száma. Ne felejtse el, hogy a szolgáltatás alapértelmezés szerint a legfontosabb 50-as egyezést adja vissza, így előfordulhat, hogy az indexben több találat is szerepel.

   ```http
   $count=true
   ```

   **Results (Eredmények)**

   :::image type="content" source="media/search-explorer/search-explorer-example-count.png" alt-text="A megfelelő dokumentumok száma az indexben" border="true":::

## <a name="limit-fields-in-search-results"></a>Mezők korlátozása a keresési eredmények között

[**$Select**](search-query-odata-select.md) hozzáadása az eredmények a explicit módon megnevezett mezőkre való korlátozásához a **Search Explorerben** olvasható kimenethez. A keresési karakterlánc és a **$Count = True** érték megtartásához előtag argumentumai a következővel: **&** . 

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true
   ```

   **Results (Eredmények)**

   :::image type="content" source="media/search-explorer/search-explorer-example-selectfield.png" alt-text="Mezők korlátozása a keresési eredmények között" border="true":::

## <a name="return-next-batch-of-results"></a>Az eredmények következő kötegének visszaküldése

Az Azure Cognitive Search a keresési rangsor alapján a legfontosabb 50-as egyezést adja vissza. A megfelelő dokumentumok következő készletének lekéréséhez fűzze hozzá a **$Top = 100, &$skip = 50** , hogy növelje az eredményeket 100-dokumentumokra (alapértelmezett érték: 50, maximum 1000), kihagyva az első 50-dokumentumot. A dokumentum kulcsát (listingID) a dokumentumok azonosítására szolgáló dokumentumban tekintheti meg. 

Ne felejtse el, hogy keresési feltételeket kell megadnia, például egy lekérdezési kifejezést vagy kifejezést a rangsorolt eredmények beszerzéséhez. Figyelje meg, hogy a keresési pontszámok csökkentik a keresési eredményekben elért mélyebb eredményeket.

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true&$top=100&$skip=50
   ```

   **Results (Eredmények)**

   :::image type="content" source="media/search-explorer/search-explorer-example-topskip.png" alt-text="Keresési eredmények következő kötegének visszaküldése" border="true":::

## <a name="filter-expressions-greater-than-less-than-equal-to"></a>Szűrési kifejezések (nagyobb, mint, kisebb, mint, egyenlő)

Ha a szabadszöveges keresés helyett pontos feltételeket szeretne megadni, használja a [**$Filter**](search-query-odata-filter.md) paramétert. A mezőnek az indexben **szűrhetőként** kell szerepelnie. Ez a példa a 3-nál nagyobb hálószobákat keres:

   ```http
   search=seattle condo&$filter=beds gt 3&$count=true
   ```
   
   **Results (Eredmények)**

   :::image type="content" source="media/search-explorer/search-explorer-example-filter.png" alt-text="Szűrés feltételek szerint" border="true":::

## <a name="order-by-expressions"></a>Rendezési kifejezések

[**$OrderBy**](search-query-odata-orderby.md) hozzáadása az eredmények egy másik mezővel való rendezéséhez a keresési pontszám mellett. A mezőnek az indexben **rendezhető** kell szerepelnie. A kipróbálható példaként használható kifejezés:

   ```http
   search=seattle condo&$select=listingId,beds,price&$filter=beds gt 3&$count=true&$orderby=price asc
   ```
   
   **Results (Eredmények)**

   :::image type="content" source="media/search-explorer/search-explorer-example-ordery.png" alt-text="Rendezési sorrend módosítása" border="true":::

**$Filter** és **$OrderBy** kifejezések is OData-konstrukciók. További információk: [OData-szűrőszintaxis](/rest/api/searchservice/odata-expression-syntax-for-azure-search).

<a name="start-search-explorer"></a>

## <a name="takeaways"></a>Legfontosabb ismeretek

Ebben a rövid útmutatóban a **keresési tallózó** használatával kérdezett le egy indexet a REST API segítségével.

+ Az eredményeket a rendszer részletes JSON-dokumentumként adja vissza, így teljes egészében megtekintheti a dokumentumok szerkezetét és tartalmát. A lekérdezési kifejezésben szereplő **$Select** paraméter korlátozhatja a visszaadott mezőket.

+ A dokumentumok minden olyan mezőből állnak, amely az indexben **beolvasható** van megjelölve. Ha szeretné megtekinteni az index attribútumokat a portálon, kattintson a *Realestate-US-Sample* elemre az **indexek** listájában a keresés – áttekintés oldalon.

+ A kereskedelmi webböngészőben megadható, a szabad formátumú lekérdezések a végfelhasználói élmény teszteléséhez is hasznosak lehetnek. Tegyük fel például, hogy a beépített Realestate-minta indexe a "Seattle Apartments Lake Washington" kifejezést adja meg, majd a CTRL-F billentyűkombinációval megkeresheti a kifejezéseket a keresési eredmények között. 

+ A lekérdezési és szűrési kifejezések az Azure Cognitive Search által megvalósított szintaxisban vannak tagolva. Az alapértelmezett érték egy [egyszerű szintaxis](/rest/api/searchservice/simple-query-syntax-in-azure-search), de igény szerint a [teljes Lucene](/rest/api/searchservice/lucene-query-syntax-in-azure-search) is használhatja nagyobb teljesítményű lekérdezésekhez. A [szűrési kifejezések](/rest/api/searchservice/odata-expression-syntax-for-azure-search) egy OData szintaxis.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a saját előfizetésében dolgozik, érdemes az egyes projektek végén eldöntenie, hogy szüksége lesz-e még a létrehozott erőforrásokra. A továbbra is futó erőforrások költségekkel járhatnak. Az erőforrásokat törölheti egyesével, vagy az erőforráscsoport törlésével eltávolíthatja a benne lévő összes erőforrást is.

A bal oldali navigációs panelen a **minden erőforrás** vagy **erőforráscsoport** hivatkozás használatával megkeresheti és kezelheti az erőforrásokat a portálon.

Ha ingyenes szolgáltatást használ, ne feledje, hogy Ön legfeljebb három indexet, indexelő és adatforrást használhat. A portálon törölheti az egyes elemeket, hogy a korlát alatt maradjon. 

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a lekérdezési struktúrákról és a szintaxisról, a Poster vagy egy azzal egyenértékű eszköz használatával hozzon létre lekérdezési kifejezéseket, amelyek az API több részét hasznosítják. A [keresési REST API](/rest/api/searchservice/search-documents) különösen hasznos a tanuláshoz és a feltáráshoz.

> [!div class="nextstepaction"]
> [Alapszintű lekérdezés létrehozása a Poster-ben](search-get-started-rest.md)