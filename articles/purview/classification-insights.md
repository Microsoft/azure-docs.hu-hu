---
title: Az Azure hatáskörébe tartozó adataira vonatkozó besorolási jelentések a hatáskörébe-elemzések használatával
description: Ez a útmutató ismerteti, hogyan lehet megtekinteni és használni a hatáskörébe besorolási jelentéseket az adatain.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 01/17/2021
ms.openlocfilehash: b6ea3e762ad098e373baa8487d8926105820f226
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101666519"
---
# <a name="classification-insights-about-your-data-from-azure-purview"></a>Az Azure hatáskörébe tartozó adatok besorolása

Ez a útmutató ismerteti, hogyan lehet hozzáférni, megtekinteni és szűrni az adataihoz tartozó hatáskörébe besorolási jelentéseit.

A támogatott adatforrások közé tartoznak a következők: Azure Blob Storage, Azure Data Lake Storage (ADLS) GEN 1, Azure Data Lake Storage (ADLS) GEN 2, Azure Cosmos DB (SQL API), Azure szinapszis Analytics (korábban SQL DW), Azure SQL Database, Azure SQL felügyelt példány, SQL Server, Amazon S3 gyűjtők

Ebben a útmutatóban megismerheti a következőket:

> [!div class="checklist"]
> - A hatáskörébe tartozó fiók elindítása az Azure-ból
> - Az adataira vonatkozó besorolási elemzések megtekintése
> - Részletezés az adatokra vonatkozó további besorolási részletekről

## <a name="prerequisites"></a>Előfeltételek

A hatáskörébe tartozó ismeretek megismerése előtt győződjön meg arról, hogy végrehajtotta a következő lépéseket:

- Állítsa be az Azure-erőforrásokat, és töltse fel a megfelelő fiókokat a tesztelési adatokkal

- Állítson be és végezzen vizsgálatot az egyes adatforrásokban. További információkért lásd: [adatforrások kezelése az Azure-ban (előzetes verzió)](manage-data-sources.md) , és [hozzon létre egy vizsgálati szabályt](create-a-scan-rule-set.md).

- Jelentkezzen be a hatáskörébe egy [Adatolvasóval vagy egy adatkezelői szerepkörrel](catalog-permissions.md#azure-purviews-pre-defined-data-plane-roles)rendelkező fiókkal.

További információ: [adatforrások kezelése az Azure hatáskörébe (előzetes verzió)](manage-data-sources.md).

## <a name="use-purview-classification-insights"></a>A hatáskörébe besorolási ismeretek használata

Az Azure-ban a besorolások hasonlók a tulajdonosi címkékhez, és az adatkészletben található, a vizsgálat során talált adott típusú adattípusok azonosítására szolgálnak.

A hatáskörébe ugyanazok a bizalmas adattípusok tartoznak, mint a Microsoft 365, így a meglévő biztonsági szabályzatokat és a teljes adategység védelmét is kiterjesztheti.

> [!NOTE]
> Miután beolvasta a forrás típusait, az új eszközök megjelenítéséhez adjon meg néhány órát az **osztályozáshoz** .

**A besorolási eredmények megtekintéséhez:**

1. Nyissa meg a Azure Portal **Azure hatáskörébe** tartozó [példány képernyőjét](https://aka.ms/purviewportal) , és válassza ki a hatáskörébe tartozó fiókot.

1. Az **Áttekintés** lap első **lépések** szakaszában válassza a **hatáskörébe tartozó fiók indítása** csempét.

1. A hatáskörébe területen válassza ki **a** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: bal oldali elemzése menüpontot az információ eléréséhez. 

1. Az **Áttekintés** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: területen válassza a **besorolás** lehetőséget a hatáskörébe **besorolási** információk megjelenítéséhez.

   :::image type="content" source="./media/insights/select-classification-labeling-small.png" alt-text="Besorolási ismeretek jelentés" lightbox="media/insights/select-classification-labeling.png":::

   A fő **besorolási** információk oldal a következő területeket jeleníti meg:

   |Terület  |Leírás  |
   |---------|---------|
   |**Besorolásokkal rendelkező források áttekintése**     |A következőket biztosító csempéket jeleníti meg: <br>– Az adataiban talált előfizetések száma <br>– Az adataiban található egyedi besorolások száma <br>– A talált kategorizált források száma <br>– A talált minősített fájlok száma <br>– A talált kategorizált táblák száma         |
   |**Legtöbbet besorolt adatforrások (utolsó 30 nap)**     |Az elmúlt 30 napban a minősített adatokat tartalmazó források számának alakulását mutatja.            |
   |**Legnépszerűbb besorolási kategóriák források szerint**     |A besorolási kategória (például **pénzügyi** vagy **kormányzati**) által talált források számát jeleníti meg.      |
   |**A fájlok leggyakoribb besorolása**     |Megjeleníti az adatokban található fájlokra alkalmazott legfontosabb besorolásokat, például a hitelkártyaszám vagy a nemzeti azonosítók számát.         |
   |**Táblázatok leggyakoribb besorolásai**     | Megjeleníti az adatok tábláira alkalmazott legfelső szintű besorolásokat, például a személyes azonosítási adatokat. |   
   |  **Besorolási tevékenység** <br>(fájlok és táblák) |  Külön diagramokat jelenít meg a fájlokhoz és táblákhoz, amelyek mindegyike a kijelölt időkereten belül besorolt fájlok vagy táblák számát mutatja. <br>**Alapértelmezett**: 30 nap<br>Válassza ki az **időszűrőt a** gráfok felett, és válasszon egy másik időkeretet.    |
   |    |    |

## <a name="classification-insights-drilldown"></a>Besorolási eredmények részletezése

A következő **besorolási** ábrák bármelyikében a további részletekért kattintson a **továbbiak megtekintése** hivatkozásra:

- **Legnépszerűbb besorolási kategóriák források szerint**
- **A fájlok leggyakoribb besorolása**
- **Táblázatok leggyakoribb besorolásai**
- **Besorolási tevékenység > besorolási adatként**

Például:

:::image type="content" source="media/insights/view-classifications-small.png" alt-text="Az összes besorolás megtekintése" lightbox="media/insights/view-classifications.png":::

További információért tegye a következők valamelyikét:

|Beállítás  |Leírás  |
|---------|---------|
|**Adatok szűrése**     |  A rács feletti szűrők használatával szűrheti a megjelenített adattípusokat, beleértve a besorolás nevét, az előfizetés nevét vagy a forrás típusát. <br><br>Ha nem biztos abban, hogy a pontos besorolási nevet használja, megadhatja a név egy részét vagy egészét a **szűrés kulcsszó alapján** mezőben.       |
|**A rács rendezése** |Válassza ki az oszlop fejlécét, hogy az oszlop szerint rendezze a rácsot. | 
|**Oszlopok szerkesztése**     |  Ha több vagy kevesebb oszlopot szeretne megjeleníteni a rácsban, válassza az **Oszlopok szerkesztése** lehetőséget :::image type="icon" source="media/insights/ico-columns.png" border="false"::: , majd válassza ki azokat az oszlopokat, amelyeket meg szeretne tekinteni, vagy módosítani szeretné a sorrendet.   |
|**További részletezés**     | Egy adott besoroláshoz való részletezéshez jelöljön ki egy nevet a **besorolás** oszlopban, hogy megtekinthesse a **besorolást a forrás jelentés alapján** . <br><br>Ez a jelentés a kiválasztott besorolás adatait jeleníti meg, beleértve a forrás nevét, a forrás típusát, az előfizetés AZONOSÍTÓját, valamint a minősített fájlok és táblák számát.      |
|**Eszközök tallózása**     |  Ha egy adott besorolással vagy forrással rendelkező eszközöket szeretne böngészni, válasszon ki egy besorolást vagy forrást a megtekintett jelentéstől függően, majd válassza a szűrők alatt található **eszközök tallózása** lehetőséget :::image type="icon" source="media/insights/ico-browse-assets.png" border="false"::: . <br><br>A keresési eredmények megjelenítik a kiválasztott szűrőhöz talált összes besorolt eszközt.  További információ: [Search The Azure hatáskörébe Data Catalog](how-to-search-catalog.md).       |
| | |

## <a name="next-steps"></a>Következő lépések

További információ az Azure-beli hatáskörébe Insight-jelentésekről
> [!div class="nextstepaction"]
> [Szószedet-áttekintés](glossary-insights.md)

> [!div class="nextstepaction"]
> [Az észlelések vizsgálata](scan-insights.md)

> [!div class="nextstepaction"]
> [Az adatfelismerések érzékenységének címkézése](./sensitivity-insights.md)

> [!div class="nextstepaction"]
> [Fájlkiterjesztés-felismerések](file-extension-insights.md)