---
title: Az Azure hatáskörébe tartozó adatain alapuló adatelemzés (előzetes verzió)
description: Ez a útmutató ismerteti, hogyan lehet megtekinteni és használni az adatain alapuló, a hatáskörébe bepillantást tevő eszköz jelentéseit.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: b9a207ffa14a18a5f4421fd21cebed28290b5ea6
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102183080"
---
# <a name="asset-insights-on-your-data-in-azure-purview"></a>Az Azure-beli adatain alapuló adatelemzések

Ez a útmutató azt ismerteti, hogyan lehet elérni, megtekinteni és szűrni a hatáskörébe tartozó adatelemzési jelentéseket az adataihoz.

Ebben a útmutatóban megismerheti a következőket:

> [!div class="checklist"]
> * Megtekintheti a hatáskörébe tartozó fiókból származó bepillantást.
> * Lekérheti az adataihoz tartozó madártávlatból megtekintését.
> * Részletezés a további eszközök számának részleteiről.

## <a name="prerequisites"></a>Előfeltételek

A hatáskörébe tartozó ismeretek megismerése előtt győződjön meg arról, hogy végrehajtotta a következő lépéseket:

* Állítsa be az Azure-erőforrásokat, és töltse fel a fiókot adatokkal.

* Állítsa be és végezzen el egy vizsgálatot a forrás típuson.

További információ: [adatforrások kezelése az Azure hatáskörébe (előzetes verzió)](manage-data-sources.md).

## <a name="use-purview-asset-insights"></a>A hatáskörébe tartozó adatfelismerések használata

Az Azure-ban a források regisztrálása és bevizsgálása végezhető el. Miután a vizsgálat befejeződött, megtekintheti az adategység eloszlását az Asset-elemzésekben, amely a besorolás és az erőforrás-készletek alapján mutatja be az adatbirtok állapotát. Azt is megadja, hogy van-e változás az adatméretben.

> [!NOTE]
> Miután beolvasta a forrás típusait, a 3-8 órát adja az eszköznek, hogy tükrözze az új eszközöket. A késleltetés oka lehet a nagy forgalom a telepítési régióban vagy a munkaterhelés mérete. További információért forduljon a mező támogatási csapatához.

1. Navigáljon az Azure-beli hatáskörébe erőforráshoz a Azure Portal.

1. Az **Áttekintés** lap első **lépések** szakaszában válassza ki a **hatáskörébe tartozó Studio** -csempét.

   :::image type="content" source="./media/asset-insights/portal-access.png" alt-text="A hatáskörébe tartozó Azure Portal elindítása":::

1. A hatáskörébe tartozó **kezdőlapon** válassza az eredmények **megtekintése** csempét az információhoz való hozzáféréshez  :::image type="icon" source="media/asset-insights/ico-insights.png" border="false"::: .

   :::image type="content" source="./media/asset-insights/view-insights.png" alt-text="Tekintse meg az Azure Portal":::

1. Az **áttekintések** :::image type="icon" source="media/asset-insights/ico-insights.png" border="false"::: területen válassza az **eszközök** lehetőséget a hatáskörébe tartozó adatellenőrzési jelentés megjelenítéséhez. 

### <a name="view-asset-insights"></a>Asset-áttekintések megtekintése

1. A fő  adatforrások oldal a következő területeket jeleníti meg:

2. Magas szintű KPI-k a források, a besorolási eszközök és a felderített eszközök megjelenítéséhez
 
3. Az első grafikonon a **forrás típusú eszközök** láthatók.

4. Megtekintheti az eszközök eloszlását Forrástípus szerint. Válasszon egy besorolást vagy egy teljes besorolási kategóriát az eszközök forrás szerinti elosztásának megtekintéséhez. 
 
5. Ha többet szeretne megtekinteni, válassza a **továbbiak** lehetőséget, amely a forrás típusok és az eszközök számát táblázatos formában jeleníti meg. A besorolási szűrők ezen a lapon lesznek végrehajtva.

   :::image type="content" source="./media/asset-insights/highlight-kpis.png" alt-text="KPI-k és gráf megtekintése az Asset bepillantást":::
 
6. Válassza ki azt a forrást, amelynek a felső mappáit meg szeretné tekinteni az Asset Counts használatával. 

   :::image type="content" source="./media/asset-insights/select-data-source.png" alt-text="Forrás típusának kiválasztása":::
 
7. Válassza ki az összes eszközt a fent kiválasztott Forrástípus felső mappájához.

   :::image type="content" source="./media/asset-insights/file-path.png" alt-text="Fájlelérési utak megtekintése":::

8. A mappában található fájlok listájának megtekintése. A zsemlemorzsával visszatérhet a bepillantást a bepillantásokra.

   :::image type="content" source="./media/asset-insights/list-page.png" alt-text="Eszközök listájának megtekintése":::  

### <a name="file-based-source-types"></a>Fájl alapú források típusai
Az Asset bepillantást a következő néhány gráf a fájl alapú források eloszlását mutatja. Az első gráf, amelynek a típusa a **fájltípusban található, a méret trend (GB)**, az elmúlt 30 napban jeleníti meg a leggyakoribb fájltípusok méretét. 
 
1. Válassza ki a forrás típusát a fájl típusának megtekintéséhez a forráson belül. 
 
1. A **részletek megtekintése** elemre kattintva megtekintheti az aktuális adatméretet, megváltoztathatja a méretet, a jelenlegi eszközök darabszámát és az eszközök számának változását
 
   > [!NOTE]
   > Ha a vizsgálat az elmúlt 30 napban csak egyszer fut, vagy bármely katalógusbeli változás, például a besorolási Hozzáadás/Eltávolítás csak egyszer történt meg 30 nap múlva, akkor a fenti módosítási információ üresen jelenik meg.

1. A forrás típusa elemre kattintva megtekintheti a legfelső szintű objektumok megváltozásakor megjelenő legfontosabb mappákat.

1. Az eszközök listájának megtekintéséhez válassza ki az elérési utat.

A fájl alapú Forrástípus második gráfja olyan fájlok, amelyek ***nincsenek társítva az erőforrás-készlethez***. Ha azt várta, hogy az összes fájlnak egy erőforrás-készletbe kell tartoznia, ez a diagram segít megérteni, hogy mely eszközök nem lettek összesítve. A hiányzó eszközök jelezhetik a mappában található helytelen fájl-mintát. A fájlokkal kapcsolatos további részletek megtekintéséhez kövesse a többi gráftal megegyező lépéseket.

   :::image type="content" source="./media/asset-insights/file-based-assets.png" alt-text="Fájl alapú eszközök megtekintése":::  

## <a name="next-steps"></a>Következő lépések

További információ az Azure-beli felügyeleti jelentésekről és a [vizsgálati](./scan-insights.md) eredményekről
