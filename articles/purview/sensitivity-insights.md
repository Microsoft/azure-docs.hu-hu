---
title: Az Azure hatáskörébe tartozó adataira vonatkozó, a hatáskörébe tartozó elemzések használatával történő jelentéskészítés
description: Ez a útmutató ismerteti, hogyan lehet megtekinteni és használni az adatain a hatáskörébe tartozó érzékenységi címke jelentését.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 01/17/2021
ms.openlocfilehash: bb8ac82b2e59ec86db89c7eba0ce607fcfc0ac2d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101676561"
---
# <a name="sensitivity-label-insights-about-your-data-in-azure-purview"></a>Az Azure hatáskörébe tartozó adataival kapcsolatos adatelemzések

Ez a útmutató azt ismerteti, hogyan lehet hozzáférni, megtekinteni és szűrni az adataira alkalmazott érzékenységi címkék által biztosított biztonsági információkat.

A támogatott adatforrások közé tartoznak a következők: Azure Blob Storage, Azure Data Lake Storage (ADLS) GEN 1, Azure Data Lake Storage (ADLS) GEN 2, SQL Server, Azure SQL Database, Azure SQL felügyelt példány, Amazon S3 gyűjtők

Ebben a útmutatóban megismerheti a következőket:

> [!div class="checklist"]
> - Nyissa meg a hatáskörébe tartozó fiókját az Azure-ból.
> - Az adatelemzési címkék érzékenységének megtekintése
> - Részletezés az adatok részletesebb címkézéséhez

> [!NOTE]
> A rendszer nem jeleníti meg a hatáskörébe tartozó, [Power bi eszközökön](register-scan-power-bi-tenant.md) található érzékenységi címkéket. 
>
> Power BI adategységek érzékenységi címkéjének megtekintéséhez tekintse meg az eszközt a [hatáskörébe Data Catalog](how-to-search-catalog.md).
> 
## <a name="prerequisites"></a>Előfeltételek

A hatáskörébe tartozó ismeretek megismerése előtt győződjön meg arról, hogy végrehajtotta a következő lépéseket:

- Állítsa be az Azure-erőforrásokat, és töltse fel a megfelelő fiókokat a tesztelési adatokkal

- [Kibővített Microsoft 365 érzékenységi címkéket az Azure hatáskörébe tartozó eszközökre](create-sensitivity-label.md), és létrehozta vagy kiválasztja az adatokra alkalmazni kívánt címkéket.

- Állítson be és végezzen vizsgálatot az egyes adatforrásokban. További információkért lásd: [adatforrások kezelése az Azure-ban (előzetes verzió)](manage-data-sources.md) , és [hozzon létre egy vizsgálati szabályt](create-a-scan-rule-set.md).

- Jelentkezzen be a hatáskörébe egy [Adatolvasóval vagy egy adatkezelői szerepkörrel](catalog-permissions.md#azure-purviews-pre-defined-data-plane-roles)rendelkező fiókkal.

További információ: [adatforrások kezelése az Azure hatáskörébe (előzetes verzió)](manage-data-sources.md) és az [adatok automatikus címkézése az Azure hatáskörébe](create-sensitivity-label.md).

## <a name="use-purview-sensitivity-labeling-insights"></a>A hatáskörébe tartozó érzékenységi feliratozás használata

A hatáskörébe tartoznak a besorolások a tulajdonosi címkékhez hasonlók, és az adatkészletben található, a vizsgálat során talált adott típusú adattípusok azonosítására szolgálnak.

Az érzékenységi címkék lehetővé teszik, hogy megtudja, milyen bizalmas adatok legyenek a szervezetében. Előfordulhat például, hogy egy adott projekt neve nagyon bizalmas a szervezeten belül, míg ugyanez a kifejezés nem bizalmas más szervezeteknek. 

A besorolások közvetlenül vannak összehasonlítva, például egy társadalombiztosítási szám, amely a **társadalombiztosítási szám** besorolásával rendelkezik. 

Ezzel szemben az érzékenységi címkék akkor lépnek életbe, ha egy vagy több besorolás és feltétel együtt található. Ebben a kontextusban a [feltételek](/microsoft-365/compliance/apply-sensitivity-label-automatically) a strukturálatlan adatokhoz megadható összes paraméterre vonatkoznak, például **egy másik besoroláshoz való közelségre**, valamint a **megbízhatóságra**. 

A hatáskörébe ugyanazokat a besorolásokat (más néven [bizalmas adattípusokat](/microsoft-365/compliance/sensitive-information-type-entity-definitions)) használja, mint a Microsoft 365. Ez lehetővé teszi a meglévő érzékenységi címkék kiterjesztését az Azure hatáskörébe tartozó eszközökre.

> [!NOTE]
> Miután beolvasta a forrás típusait, az új eszközök megjelenítéséhez **adjon meg** néhány órát az adatoknak.

**Az adatfelismerések címkézésének megtekintése:**

1. Nyissa meg az Azure-beli **hatáskörébe** tartozó kezdőlapot.

1. Az **Áttekintés** lap első **lépések** szakaszában válassza a **hatáskörébe tartozó fiók indítása** csempét.

1. A hatáskörébe területen válassza ki **a** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: bal oldali elemzése menüpontot az információ eléréséhez. 

1. **Az információk** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: területen válassza az **érzékenységi címkék** lehetőséget a hatáskörébe tartozó **érzékenység címkézési** jelentés megjelenítéséhez.

    > [!NOTE]
    > Ha ez a jelentés üres, előfordulhat, hogy nem bővítette az érzékenységi címkéket az Azure hatáskörébe. További információ: [az adatok automatikus címkézése az Azure hatáskörébe](create-sensitivity-label.md).

   :::image type="content" source="media/insights/sensitivity-labeling-insights-small.png" alt-text="Az adatfelismerések érzékenységének címkézése" lightbox="media/insights/sensitivity-labeling-insights.png":::

   A fő **érzékenység címkézése** lap a következő területeket jeleníti meg:

   |Terület  |Leírás  |
   |---------|---------|
   |**Az érzékenységi címkékkel rendelkező források áttekintése**     |A következőket biztosító csempéket jeleníti meg: <br>– Az adataiban található előfizetések száma. <br>– Az adatain alkalmazott egyedi érzékenységi címkék száma <br>– Az érzékenységi címkékkel rendelkező források száma <br>– Az érzékenységi címkékkel megtalált fájlok és táblák száma|
   |**Legfelső szintű források címkézett adattal (utolsó 30 nap)**     | Megjeleníti az elmúlt 30 napban fellépő trendeket, amelyek az érzékenységi címkékkel rendelkező források számát mutatják.       |
   |**Források között alkalmazott leggyakoribb feliratok**     |Megjeleníti az összes hatáskörébe tartozó adaterőforrásban alkalmazott leggyakoribb címkéket. |
   |**A fájlokon alkalmazott leggyakoribb feliratok**     |Megjeleníti az adataiban található fájlokra alkalmazott legfontosabb érzékenységi címkéket.          |
   |**Táblákon alkalmazott leggyakoribb feliratok**     | Megjeleníti az adatbázis tábláira alkalmazott legfontosabb érzékenységi címkéket az adataiban. |   
   |  **Címkézési tevékenység**  |  Külön gráfokat jelenít meg a fájlokhoz és táblákhoz, amelyek mindegyike a kiválasztott időkereten megjelenő fájlok vagy táblák számát mutatja. <br>**Alapértelmezett**: 30 nap<br>Válassza ki az **időszűrőt a** gráfok felett, és válasszon egy másik időkeretet.    |
   |    |    |

## <a name="sensitivity-labeling-insights-drilldown"></a>Érzékenység címkézési részletezés

A további részletekért **tekintse** meg a további részleteket a részletes elemzések **a következővel** :

- **Források között alkalmazott leggyakoribb feliratok**
- **A fájlokon alkalmazott leggyakoribb feliratok**
- **Táblákon alkalmazott leggyakoribb feliratok**
- **Címkéző tevékenység > címkézett adatként**

Például:

:::image type="content" source="media/insights/sensitivity-label-drilldown-small.png" alt-text="Érzékenységi címke részletezése" lightbox="media/insights/sensitivity-label-drilldown.png":::

További információért tegye a következők valamelyikét:

|Beállítás  |Leírás  |
|---------|---------|
|**Adatok szűrése**     |  A rács feletti szűrők használatával szűrheti a megjelenített adattípusokat, beleértve a címke nevét, az előfizetés nevét vagy a forrás típusát. <br><br>Ha nem biztos benne, hogy a címke pontos neve, megadhatja a név egy részét vagy egészét a **szűrés kulcsszó alapján** mezőben.       |
|**A rács rendezése** |Válassza ki az oszlop fejlécét, hogy az oszlop szerint rendezze a rácsot. | 
|**Oszlopok szerkesztése**     |  Ha több vagy kevesebb oszlopot szeretne megjeleníteni a rácsban, válassza az **Oszlopok szerkesztése** lehetőséget :::image type="icon" source="media/insights/ico-columns.png" border="false"::: , majd válassza ki azokat az oszlopokat, amelyeket meg szeretne tekinteni, vagy módosítani szeretné a sorrendet.    <br><br>Válassza ki az oszlop fejlécét, hogy az oszlop szerint rendezze a rácsot.   |
|**További részletezés**     | Egy adott címkére való részletezéshez jelöljön ki egy nevet az **érzékenységi címke** oszlopban, és tekintse meg a **címkét a forrás jelentés alapján** . <br><br>Ez a jelentés a kiválasztott címke adatait jeleníti meg, beleértve a forrás nevét, a forrás típusát, az előfizetés AZONOSÍTÓját, valamint a minősített fájlok és táblák számát.      |
|**Eszközök tallózása**     |  Ha egy adott címkével vagy forrással rendelkező eszközöket szeretne böngészni, jelöljön ki egy vagy több címkét vagy forrást az Ön által megtekintett jelentéstől függően, majd válassza a szűrők alatt található **eszközök tallózása** lehetőséget :::image type="icon" source="media/insights/ico-browse-assets.png" border="false"::: . <br><br>A keresési eredmények megjelenítik a kiválasztott szűrőhöz talált összes címkézett eszközt.  További információ: [Search The Azure hatáskörébe Data Catalog](how-to-search-catalog.md).       |
| | |

## <a name="sensitivity-label-integration-with-microsoft-365-compliance"></a>Érzékenységi címke integrációja Microsoft 365 megfelelőséggel

A Microsoft 365 által kínált [Microsoft Information Protection](/microsoft-365/compliance/information-protection) integrációjának lezárása azt jelenti, hogy a hatáskörébe lehetővé teszi, hogy közvetlen módon terjessze ki az adattárházba a láthatóságot, és osztályozza és címkézze fel az adatait.

Ahhoz, hogy a Microsoft 365 érzékenységi címkét ki lehessen terjeszteni az eszközeire az Azure hatáskörébe, aktívan be kell kapcsolni az Information Protection for Azure hatáskörébe, a Microsoft 365 megfelelőségi központban.

További információ: [az adatok automatikus címkézése az Azure hatáskörébe](create-sensitivity-label.md).

## <a name="next-steps"></a>Következő lépések

További információ ezekről az Azure-beli betekintési jelentésekről:

- [Szószedet-áttekintés](glossary-insights.md)
- [Az észlelések vizsgálata](scan-insights.md)
- [Besorolási ismeretek](./classification-insights.md)
- [Fájlkiterjesztés-felismerések](file-extension-insights.md)
