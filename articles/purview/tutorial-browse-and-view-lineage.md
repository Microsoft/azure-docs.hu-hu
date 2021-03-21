---
title: 'Oktatóanyag: az adategységek böngészése az Azure-ban és a kifejlődésük megtekintése'
description: Ez az oktatóanyag azt ismerteti, hogyan lehet megkeresni a katalógusban lévő adatforrásokat és megtekinteni az adatok eredetét
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: 7ffbe2ded44ded4f580655f6ae9e98391490f94a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97696091"
---
# <a name="tutorial-browse-assets-in-azure-purview-preview-and-view-their-lineage"></a>Oktatóanyag: az adatforrások böngészése az Azure hatáskörébe (előzetes verzió) és a kifejlődésük megtekintése

> [!IMPORTANT]
> Az Azure-beli hatáskörébe jelenleg előzetes verzió érhető el. A [Microsoft Azure előzetes verzióinak kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) közé tartoznak azok a további jogi feltételek, amelyek az Azure olyan szolgáltatásaira vonatkoznak, amelyek béta-, előzetes verziójú vagy egyéb módon még nem lettek nyilvánosan elérhetők.

Ez az oktatóanyag bemutatja, hogyan böngészheti az adategységeket az Azure-ban, és megtekintheti a fontos adatokat, például a lineaget.

Ez az oktatóanyag *egy öt részes oktatóanyag-Sorozat 3. része* , amelyben megtudhatja, hogyan kezelheti az adatkezelési szabályozást az Azure hatáskörébe. Ez az oktatóanyag a 2. részben befejezett munkára épül [: navigáljon az Azure-beli hatáskörébe (előzetes verzió) kezdőlapjára, és keressen rá egy](tutorial-asset-search.md)adategységre.

Az oktatóanyagban a következőket végezheti el:

> [!div class="checklist"]
>
> * Keresse meg a katalógusban található eszközöket.
> * Az eszközök kifejlődésének megtekintése.

## <a name="prerequisites"></a>Előfeltételek

* Teljes [oktatóanyag: navigáljon az Azure-beli hatáskörébe (előzetes verzió) kezdőlapjára, és keressen rá egy](tutorial-asset-search.md)adategységre.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="browse-for-assets-in-the-catalog"></a>Erőforrások tallózása a katalógusban

Az előző oktatóanyagban megtanulta, hogy a Search hogyan segít felderíteni az eszközöket az Azure hatáskörébe katalógusában. A katalógusban lévő eszközök felderítésének másik módja a katalógus böngészési felületének használata.

Ez a szakasz bemutatja, hogyan böngészhet az Azure-beli hatáskörébe-katalógusban.

1. Nyissa meg az Azure-beli hatáskörébe tartozó erőforrást a Azure Portalon, és válassza a **hatáskörébe Studio megnyitása** lehetőséget. A rendszer automatikusan átveszi a hatáskörébe Studio kezdőlapját.

1. A **kezdőlapon** válassza az **eszközök tallózása** lehetőséget.

   Megjelenik az **eszközök tallózása** lap, amely a katalógusban szereplő összes eszköz típusának összegzését jeleníti meg.

1. A katalógusban elérhető különböző Azure Data Lake Gen2-eszközök megismeréséhez válassza a **Azure Data Lake Gen2** csempét.

   :::image type="content" source="./media/tutorial-browse-and-view-lineage/browse-by-asset-type.png" alt-text="Képernyőfelvétel: a Tallózás az eszköz típusa szerint oldalon Azure Data Lake Gen2 kijelölve.":::

1. Ha több eszköz is van, kiválaszthatja a **Name (név** ) oszlop fejlécét az eszközök betűrendbe rendezéséhez. Ebben az oktatóanyagban csak egy Azure Data Lake Storage Gen2 eszköz található.

1. Válassza ki az eszköz nevét.

1. Válassza ki a **Contoso_GrossProfit_ {N}. SSV** erőforrás-készletet. Ha ez az eszköz nem létezik a katalógusban, válasszon egy másikat.

   :::image type="content" source="media/tutorial-browse-and-view-lineage/view-adls-assets.png" alt-text="Azure Data Lake Storage Gen2 erőforrások listája":::

## <a name="view-the-lineage-of-assets"></a>Az eszközök kifejlődésének megtekintése

Az eszköz adatai lapon vizsgálja meg az adatok forrását.

1. Válassza ki a **Contoso_GrossProfit_ {N}. SSV** erőforrás-készlet **Lineage** lapját.

   Megjelenik a kiválasztott eszköz. A megjelenített információ azt mutatja, hogy ezt az erőforrás-készletet az Azure Blob Storage-fiókjából Azure Data Lake Storage Gen2ba másolták.

   :::image type="content" source="./media/tutorial-browse-and-view-lineage/view-lineage.png" alt-text="Képernyőfelvétel: az eszköz Lineage nézete.":::

1. Válassza ki a blob-eszközt ezen a lapon, és válassza a **Váltás az eszközre** hivatkozásra.

   Figyelje meg, hogy az ablak át lett állítva az Azure Blob-erőforrás-készletre, amely az eredeti Azure Data Lake Storage Gen2 forrása volt.

1. Az **Áttekintés** lapon megvizsgálhatja az eszközt, és megerősítheti annak részleteit.

További információ arról, hogyan hozható létre Azure Data Factory adatfolyam-tevékenység egy Azure-Blob és Azure Data Lake Storage Gen2 erőforrás-készlet között, és figyelje a leszármazást: [Azure Data Factory adatfolyam tevékenysége](../data-factory/concepts-data-flow-overview.md).

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
>
> * Keresse meg a katalógusban található eszközöket.
> * Az eszközök kifejlődésének megtekintése.

Folytassa a következő oktatóanyaggal, amely az erőforrás-készletek, az eszköz részletei, a sémák és a besorolások megismerését ismerteti.

> [!div class="nextstepaction"]
> [Erőforrás-készletek, eszköz adatai, sémái és besorolásai](tutorial-schemas-and-classifications.md)
