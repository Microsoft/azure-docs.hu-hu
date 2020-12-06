---
title: Fájlkiterjesztés jelentéskészítés az adataihoz a hatáskörébe-elemzések használatával
description: Ez a útmutató ismerteti, hogyan lehet megtekinteni és használni az adatain a hatáskörébe tartozó elemzések fájlkiterjesztés-jelentését.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/24/2020
ms.openlocfilehash: 222106f5838a1eb237987afaa93924f1efe4a1f4
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2020
ms.locfileid: "96746284"
---
# <a name="file-extension-insights-about-your-data-from-azure-purview"></a>Fájlkiterjesztés-elemzések az Azure hatáskörébe tartozó adatokról 

Ez a útmutató ismerteti, hogyan lehet hozzáférni, megtekinteni és szűrni az adataiban található fájlkiterjesztések vagy fájltípusok információit.

A támogatott adatforrások a következők: Azure Blob Storage, Azure Data Lake Storage (ADLS) GEN 1, Azure Data Lake Storage (ADLS) GEN 2

Ebben a útmutatóban megismerheti a következőket:
> [!div class="checklist"]
> * Nyissa meg a hatáskörébe tartozó fiókját az Azure-ból. 
> - A fájlkiterjesztés-elemzések megtekintése az adatain
> - Részletezés a fájlkiterjesztés további részleteiről az adatokon

## <a name="prerequisites"></a>Előfeltételek 

A hatáskörébe tartozó ismeretek megismerése előtt győződjön meg arról, hogy végrehajtotta a következő lépéseket:

- Állítsa be az Azure-erőforrásokat, és töltse fel a megfelelő fiókokat a tesztelési adatokkal

- Az egyes adatforrások tesztelési célú ellenőrzésének beállítása és befejezése

További információ: [adatforrások kezelése az Azure hatáskörébe (előzetes verzió)](manage-data-sources.md).

## <a name="use-purview-file-extension-insights"></a>A hatáskörébe tartozó fájlkiterjesztés-felismerések használata

Az eszközök vizsgálata során az Azure-beli alkalmazás képes észlelni az adatbirtokban található fájltípusokat, és további részleteket biztosít az egyes fájltípusokról. A részletekből megtudhatja, hogy az egyes típusok hány fájlból állnak, hol vannak a fájlok, és hogy bizalmas adatok leolvasható-e.

> [!NOTE]
> Miután beolvasta a forrás típusait, **adjon meg** néhány órát a fájlkiterjesztés alapján, hogy tükrözze az új eszközöket.

**A fájlkiterjesztés-felismerések megtekintéséhez:**

1. Nyissa meg a Azure Portal **Azure hatáskörébe** tartozó [példány képernyőjét](https://aka.ms/purviewportal) , és válassza ki a hatáskörébe tartozó fiókot.

1. Az **Áttekintés** lap első **lépések** szakaszában válassza a **hatáskörébe tartozó fiók indítása** csempét.

1. A hatáskörébe területen válassza ki **a** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: bal oldali elemzése menüpontot az információ eléréséhez. **Insights**
    
1. Az **eredmények között válassza** a **fájlkiterjesztések** fület.

    A jelentés megjeleníti az egyedi fájlkiterjesztések számát, valamint az első 10 bővítmény gráfját a kiválasztott időkereten belül (alapértelmezés: 30 nap).

    :::image type="content" source="media/file-extension-insights/file-extension-overview-small.png" alt-text="Fájlkiterjesztés-jelentés – áttekintés" lightbox="media/file-extension-insights/file-extension-overview.png":::

    További információkért tegye a következők valamelyikét:

    - Válassza ki a jelentés tetején a **Time** választót annak az időtartománynak a megváltoztatásához, amelyre a fájlkiterjesztés található.
    
    - A megtalált fájlkiterjesztések teljes listájának megtekintéséhez válassza a diagram alatt a **továbbiak megtekintése** lehetőséget. További információ: fájlkiterjesztés-információk [lebontása](#file-extension-insights-drilldown). 

### <a name="file-extension-insights-drilldown"></a>Fájlkiterjesztés-felismerések részletezése

Miután megtekintette az adatbirtokban található fájltípusokkal kapcsolatos magas szintű információkat, részletezheti a helyét, és megtekintheti a bizalmas adatok vizsgálatára szolgáló adatokat.

Például:

:::image type="content" source="media/file-extension-insights/file-extension-drilldown-small.png" alt-text="Fájlkiterjesztés-jelentés – részletezés" lightbox="media/file-extension-insights/file-extension-drilldown.png":::

A rács minden talált fájlkiterjesztés részleteit jeleníti meg, beleértve a következőket:

- **Fájlok száma**. A megadott kiterjesztésű fájlok száma.
- **Tartalom vizsgálata**. Azt határozza meg, hogy a fájlkiterjesztés támogatott-e a bizalmas adatok vizsgálatához.
- **Előfizetések**. Azon előfizetések száma, amelyeken a megadott bővítmény található.
- **Források**. A megadott fájlkiterjesztés által talált források száma.



A rács feletti szűrők használatával szűrheti a megjelenített adatmezőket:

|Beállítás  |Leírás  |
|---------|---------|
|**Szűrés kulcsszó alapján**     |    Adja meg a **Szűrés keresőszóval**  mezőben szöveget a fájltípusok név szerinti szűréséhez. Például csak a PDF-fájlok megtekintéséhez írja be a következőt: `PDF` .     |
|**Idő**        | Kiválaszthatja, hogy a rendszer egy adott időtartomány alapján szűrje az adatait az adatai létrehozásakor. <br>**Alapértelmezett:** 30 nap  |
|**Fájlkiterjesztés**     |Válassza ki a rács egy vagy több fájltípus szerinti szűrését.        |
|**Források**    |Ezzel a beállítással szűrheti a rácsot az adott adatforrások alapján. |
|**Tartalom vizsgálata**     |Válassza ki a **támogatott** vagy **nem támogatott** lehetőséget, hogy csak azok a fájltípusok jelenjenek meg, amelyek további vizsgálatra képesek a bizalmas adatokhoz, vagy olyan adatokat, amelyeket nem lehet beszkennelni, például **. CERT** vagy **. jpg** fájlokat. |
| | |

A szűrők felett válassza az **Oszlopok szerkesztése** lehetőséget a :::image type="icon" source="media/insights/ico-columns.png" border="false"::: rácsban található több vagy kevesebb oszlop megjelenítéséhez, vagy a sorrend megváltoztatásához. 

A rács rendezéséhez válasszon ki egy oszlopfejlécet az oszlop szerinti rendezéshez.
## <a name="next-steps"></a>További lépések

További információ az Azure-beli hatáskörébe Insight-jelentésekről
> [!div class="nextstepaction"]
> [Szószedet-áttekintés](glossary-insights.md)

> [!div class="nextstepaction"]
> [Az észlelések vizsgálata](scan-insights.md)

> [!div class="nextstepaction"]
> [Besorolási ismeretek](./classification-insights.md)

> [!div class="nextstepaction"]
> [Érzékenység feliratának felismerése](sensitivity-insights.md)
