---
title: Az Azure Monitor-munkafüzetek áttekintése
description: Ebből a témakörből megtudhatja, hogy a munkafüzetek Hogyan biztosítják az adatelemzést és a részletes vizualizációs jelentések létrehozását a Azure Portalon belül.
services: azure-monitor
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: a02e5fced0a9e338a32d8d8beaa9e4b5fca994e8
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107309477"
---
# <a name="azure-monitor-workbooks"></a>Munkafüzetek Azure Monitor

A munkafüzetek Azure Portalon belül kínálnak rugalmas vásznat az adatelemzéshez és a részletgazdag vizuális jelentések létrehozásához. Lehetővé teszik, hogy több adatforrásra koppintson az Azure-ból, és egyesítse azokat egységes interaktív környezetekben.

Íme egy videós bemutató a munkafüzetek létrehozásához.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4B4Ap]

## <a name="data-sources"></a>Adatforrások

A munkafüzetek több Azure-on belüli adatforrásból képesek adatokat lekérdezni. A munkafüzetek készítői ezeket az adatokat átalakítva megállapításokat tehetnek a mögöttes összetevők rendelkezésre állásáról, teljesítményéről, használatáról és általános állapotáról. A virtuális gépekről származó teljesítményadatokat például a magas CPU-vagy alacsony memória-példányok azonosítására, valamint az eredmények egy interaktív jelentésben lévő rácsként való megjelenítésére lehet elemezni.
  
A munkafüzet legnagyobb előnye azonban az a képesség, hogy különálló forrásokból származó adatokat kombinálnak egyetlen jelentésben. Ez lehetővé teszi az összetett erőforrás-nézetek létrehozását, illetve az erőforrások közötti összekapcsolást, amely lehetővé teszi, hogy a gazdagabb adatmennyiség és az egyéb módon lehetetlenné válik.

A munkafüzetek jelenleg a következő adatforrásokkal kompatibilisek:

* [Naplók](../visualize/workbooks-data-sources.md#logs)
* [Metrikák](../visualize/workbooks-data-sources.md#metrics)
* [Azure Resource Graph](../visualize/workbooks-data-sources.md#azure-resource-graph)
* [Riasztások (előzetes verzió)](../visualize/workbooks-data-sources.md#alerts-preview)
* [Munkaterhelés állapota](../visualize/workbooks-data-sources.md#workload-health)
* [Azure Resource Health](../visualize/workbooks-data-sources.md#azure-resource-health)
* [Azure Data Explorer](../visualize/workbooks-data-sources.md#azure-data-explorer)

## <a name="visualizations"></a>Vizualizációk

A munkafüzetek számos képességet biztosítanak az adatmegjelenítéshez. Az egyes vizualizációk típusaira vonatkozó részletes példákat az alábbi hivatkozásokra kattintva tekintheti meg:

* [Szöveg](../visualize/workbooks-text-visualizations.md)
* [Diagramok](../visualize/workbooks-chart-visualizations.md)
* [Rácsok](../visualize/workbooks-grid-visualizations.md)
* [Csempék](../visualize/workbooks-tile-visualizations.md)
* [Fák](../visualize/workbooks-tree-visualizations.md)
* [Grafikonok](../visualize/workbooks-graph-visualizations.md)
* [Összetett sáv](../visualize/workbooks-composite-bar.md)

:::image type="content" source="./media/workbooks-overview/visualizations.png" alt-text="Példa a munkafüzetek vizualizációinak megjelenítésére" border="false" lightbox="./media/workbooks-overview/visualizations.png":::

## <a name="getting-started"></a>Első lépések

A munkafüzetek felületének megismeréséhez először navigáljon a Azure Monitor szolgáltatáshoz. Ezt úgy teheti meg, hogy beírja a **figyelő** kifejezést a Azure Portal található keresőmezőbe.

Ezután válassza a **munkafüzetek** lehetőséget.

:::image type="content" source="./media/workbooks-overview/workbooks.png" alt-text="Egy piros mezőben Kiemelt munkafüzetek képernyőképe" border="false":::

### <a name="gallery"></a>Katalógus

A katalógus használatával kényelmesen rendezheti, rendezheti és kezelheti az összes típusú munkafüzetet.

:::image type="content" source="./media/workbooks-overview/gallery-all-tab.png" alt-text="Képernyőkép a gyűjteményről az összes lapon." lightbox="media/workbooks-overview/gallery-all-tab.png":::

#### <a name="gallery-tabs"></a>Katalógus lapjai

A katalógusban négy lap segíti a munkafüzetek rendszerezését.

| Tab              | Description                                       |
|------------------|---------------------------------------------------|
| Mind | Megjeleníti az első négy elemet minden Type-munkafüzethez, nyilvános sablonhoz és saját sablonokhoz. A munkafüzetek a módosítás dátuma szerint vannak rendezve, így látni fogja a legutóbbi nyolc módosított munkafüzetet.|
| Munkafüzetek | Megjeleníti a létrehozott vagy Önnel megosztva elérhető munkafüzetek listáját. |
| Nyilvános sablonok | Megjeleníti a rendelkezésre álló használatra kész, a Microsoft által közzétett funkcionális munkafüzet-sablonokat. Kategória szerint csoportosítva. |
| Saját sablonok | Megjeleníti az Ön által létrehozott vagy Önnel megosztott összes elérhető, telepített munkafüzetet tartalmazó sablon listáját. Kategória szerint csoportosítva. |

#### <a name="features"></a>Funkciók

* Az egyes lapokon a munkafüzetek adatait tartalmazó rács található. Tartalmazza a leírást, az utolsó módosítás dátumát, a címkéket, az előfizetést, az erőforráscsoportot, a régiót és a megosztott állapotot. Ezen információk alapján is rendezheti a munkafüzeteket.
* Szűrés erőforráscsoport, előfizetések, munkafüzet/sablon neve vagy sablon kategória alapján.
* Jelöljön ki több munkafüzetet a törléshez vagy a tömeges törléshez.
* Minden munkafüzetnek van helyi menüje (három pont/három pont a végén), és a kijelöléssel megnyílik a gyors műveletek listája.
    * Az erőforrás-hozzáférés munkafüzet-erőforrás lapján megtekintheti a munkafüzet erőforrás-AZONOSÍTÓját, címkéket adhat hozzá, kezelheti a zárolásokat stb.
    * Törölje vagy nevezze át a munkafüzetet.
    * Munkafüzet rögzítése az irányítópulton.

### <a name="workbooks-versus-workbook-templates"></a>Munkafüzetek és munkafüzet-sablonok

Megtekintheti a _munkafüzetet_ zöld színnel és számos _munkafüzet-sablonban_ a lila nyelven. A sablonok a több felhasználó és csapat számára rugalmas újrafelhasználásra tervezett, kurátori jelentésként szolgálnak. A sablon megnyitása egy átmeneti munkafüzetet hoz létre, amely a sablon tartalmával van feltöltve.

Módosíthatja a sablon alapú munkafüzet paramétereit, és elvégezheti az elemzést anélkül, hogy a munkatársainak jövőbeli jelentéskészítési élményét kellene megszakítani. Ha megnyit egy sablont, hajtson végre néhány módosítást, majd válassza a Mentés ikont, majd mentse a sablont egy olyan munkafüzetként, amely az eredeti sablon érintetlen állapotának elhagyásakor zöldre vált.

A motorháztető alatt a sablonok a mentett munkafüzetektől is eltérhetnek. A munkafüzet mentése egy társított Azure Resource Manager erőforrást hoz létre, míg a sablon megnyitásakor létrehozott átmeneti munkafüzethez nincs egyedi erőforrás társítva. Ha többet szeretne megtudni arról, hogy a hozzáférés-vezérlés hogyan legyen kezelve a munkafüzetekben, tekintse meg a [munkafüzetek hozzáférés-vezérlése című cikket](../visualize/workbooks-access-control.md).

### <a name="exploring-a-workbook-template"></a>Munkafüzet-sablon feltárása

Válassza az **alkalmazáshiba elemzése** lehetőséget az egyik alapértelmezett alkalmazás-munkafüzet-sablon megtekintéséhez.

:::image type="content" source="./media/workbooks-overview/failure-analysis.png" alt-text="Képernyőkép az alkalmazáshiba-elemzési sablonról" border="false" lightbox="./media/workbooks-overview/failure-analysis.png":::

Amint azt korábban már említettük, a sablon megnyitása egy ideiglenes munkafüzetet hoz létre, amely lehetővé teszi a kommunikációt. Alapértelmezés szerint a munkafüzet olvasási módban nyílik meg, amely csak az eredeti sablon szerzője által létrehozott kívánt elemzési felület adatait jeleníti meg.

Az adott munkafüzet esetében az élmény interaktív. Módosíthatja az előfizetést, a megcélzó alkalmazásokat és a megjeleníteni kívánt adattartományt. Ha elvégezte ezeket a beállításokat, a HTTP-kérések rácsa is interaktív, amely során az egyes sorok kiválasztásakor a jelentés alján található két diagramon megváltoztathatja a megjelenített adatmennyiséget.

### <a name="editing-mode"></a>Szerkesztési mód

Annak megismeréséhez, hogy a munkafüzet-sablon hogyan legyen összekapcsolva, **a Szerkesztés gombra** kattintva kell cserélnie a szerkesztési módba.

:::image type="content" source="./media/workbooks-overview/edit.png" alt-text="A munkafüzetek szerkesztési gombjának képernyőképe." border="false" :::

Miután átváltotta a szerkesztési módot, megfigyelheti, hogy számos **szerkesztési** mező jelenik meg a munkafüzet minden egyes aspektusának megfelelő módon.

:::image type="content" source="./media/workbooks-overview/edit-mode.png" alt-text="Képernyőkép a Szerkesztés gombról" border="false" lightbox="./media/workbooks-overview/edit-mode.png":::

Ha a Szerkesztés gombot azonnal bejelöli a kérelmek adatainak rácsa alatt, láthatjuk, hogy a munkafüzet ezen része egy Application Insights erőforrásból származó adatokkal kapcsolatos Kusto-lekérdezést tartalmaz.

:::image type="content" source="./media/workbooks-overview/kusto.png" alt-text="Képernyőkép az alapul szolgáló Kusto-lekérdezésről" border="false" lightbox="./media/workbooks-overview/kusto.png":::


A jobb oldalon található egyéb **szerkesztési** gombokra kattintva megjelenítheti a munkafüzetek, például a Markdown-alapú [szövegmezők](../visualize/workbooks-text-visualizations.md), a [paraméter-kiválasztási](../visualize/workbooks-parameters.md) felhasználóifelület-elemek, valamint az egyéb [diagram/vizualizáció típusok](#visualizations)számát.

Szerkessze az előre elkészített sablonokat szerkesztési módban, majd módosítsa őket az igényeinek megfelelően, és mentse a saját egyéni munkafüzetét, hogy megismerkedjen a Azure Monitor munkafüzetek lehetséges lehetőségeivel.

## <a name="pinning-visualizations"></a>Vizualizációk rögzítése

A munkafüzetben lévő szöveg-, lekérdezési és mérőszám-lépések a PIN-kód gomb használatával állíthatók be, miközben a munkafüzet PIN-módban van, vagy ha a munkafüzet szerzője engedélyezte az adott elemhez tartozó beállításokat, hogy a PIN-ikon látható legyen.

A PIN-kód mód eléréséhez kattintson a **Szerkesztés** gombra a szerkesztési mód megadásához, majd a felső sávon a kék PIN-kód ikonra. A képernyő jobb oldalán megjelenik egy egyedi PIN-kód ikon, amely a megfelelő munkafüzet részének *szerkesztési* mezője fölé kerül.

:::image type="content" source="./media/workbooks-overview/pin-experience.png" alt-text="Képernyőfelvétel a PIN-kód felhasználói felületéről." border="false":::

> [!NOTE]
> A rendszer a PIN-kód időpontjában menti a munkafüzet állapotát, és az irányítópulton rögzített munkafüzetek nem frissülnek, ha módosul a mögöttes munkafüzet. Egy rögzített munkafüzet-rész frissítéséhez törölnie kell és újra rögzítenie kell ezt a részt.

## <a name="dashboard-time-ranges"></a>Irányítópult időtartományai

A rögzített munkafüzet-lekérdezési részek figyelembe veszik az irányítópult időtartományát, ha a rögzített elem *időtartomány* -paraméter használatára van konfigurálva. Az irányítópult időtartományának értéke az időtartomány paraméterének értéke lesz, az irányítópult időtartományának bármilyen módosítása pedig a rögzített elem frissítését eredményezi. Ha egy rögzített rész az irányítópult időtartományát használja, látni fogja a rögzített rész frissítésének alcímét, hogy megjelenjen az irányítópult időtartománya, amikor az időtartomány megváltozik.

Emellett az időtartomány-paramétert használó rögzített munkafüzet-részek automatikusan frissülnek az irányítópult időtartománya által meghatározott sebességgel. Az utolsó alkalommal, amikor a lekérdezés futott, megjelenik a rögzített rész alcíme.

Ha egy rögzített lépés explicit módon beállított időtartományt tartalmaz (nem használ időtartomány-paramétert), akkor a rendszer az irányítópult beállításaitól függetlenül mindig használni fogja az adott időtartományt. A rögzített rész alcíme nem jeleníti meg az irányítópult időtartományát, és a lekérdezés nem fog automatikusan frissülni az irányítópulton. Az alcím a lekérdezés legutóbbi futásakor jelenik meg.

> [!NOTE]
> Az *egyesítési* adatforrást használó lekérdezések jelenleg nem támogatottak az irányítópultokra való rögzítéskor.

## <a name="sharing-workbook-templates"></a>Munkafüzet-sablonok megosztása

Miután elindította saját munkafüzet-sablonjait, érdemes megosztania azt a szélesebb Közösséggel. Ha többet szeretne megtudni, és más olyan sablonokat is meg szeretne vizsgálni, amelyek nem részei az alapértelmezett Azure Monitor Gallery nézetnek, látogasson el a [GitHub-tárházba](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md). A meglévő munkafüzetek tallózásához látogasson el a GitHubon található [munkafüzet-tárba](https://github.com/microsoft/Application-Insights-Workbooks/tree/master/Workbooks) .

## <a name="next-step"></a>Következő lépés

* [Ismerkedjen](#visualizations) meg a munkafüzetek számos gazdag vizualizációs lehetőségével.
* A munkafüzet erőforrásaihoz való hozzáférés [szabályozása](../visualize/workbooks-access-control.md) és megosztása.