---
title: Az Azure Monitor-munkafüzetek áttekintése
description: Ebből a témakörből megtudhatja, hogy a munkafüzetek Hogyan biztosítják az adatelemzést és a részletes vizualizációs jelentések létrehozását a Azure Portalon belül.
manager: carmonm
services: azure-monitor
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: e401eb4a5608db6e5767298f53046099a42679b4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100613738"
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

A munkafüzetek számos képességet biztosítanak az adatmegjelenítéshez. Az egyes vizualizációs típusok részletes példáit az alábbi hivatkozásokra kattintva tekintheti meg:

* [Szöveg](../visualize/workbooks-text-visualizations.md)
* [Diagramok](../visualize/workbooks-chart-visualizations.md)
* [Rácsok](../visualize/workbooks-grid-visualizations.md)
* [Csempék](../visualize/workbooks-tile-visualizations.md)
* [Fák](../visualize/workbooks-tree-visualizations.md)
* [Grafikonok](../visualize/workbooks-graph-visualizations.md)
* [Összetett sáv](../visualize/workbooks-composite-bar.md)

![Példa munkafüzet-vizualizációk](./media/workbooks-overview/visualizations.png)

## <a name="getting-started"></a>Első lépések

A munkafüzetek felületének megismeréséhez először navigáljon a Azure Monitor szolgáltatáshoz. Ezt úgy teheti meg, hogy beírja a **figyelő** kifejezést a Azure Portal található keresőmezőbe.

Ezután válassza a **munkafüzetek** lehetőséget.

![Egy piros mezőben Kiemelt munkafüzetek képernyőképe](./media/workbooks-overview/workbooks.png)

### <a name="gallery"></a>Katalógus

Ekkor megjelenik a munkafüzetek gyűjteménye:

![Képernyőkép Azure Monitor munkafüzetek gyűjteményének nézetéről](./media/workbooks-overview/gallery.png)

### <a name="workbooks-versus-workbook-templates"></a>Munkafüzetek és munkafüzet-sablonok

Megtekintheti a _munkafüzetet_ zöld színnel és számos _munkafüzet-sablonban_ a lila nyelven. A sablonok a több felhasználó és csapat számára rugalmas újrafelhasználásra tervezett, kurátori jelentésként szolgálnak. A sablon megnyitása egy átmeneti munkafüzetet hoz létre, amely a sablon tartalmával van feltöltve. 

Módosíthatja a sablon alapú munkafüzet paramétereit, és elvégezheti az elemzést anélkül, hogy a munkatársainak jövőbeli jelentéskészítési élményét kellene megszakítani. Ha megnyit egy sablont, hajtson végre néhány módosítást, majd válassza a Mentés ikont, majd mentse a sablont egy olyan munkafüzetként, amely az eredeti sablon érintetlen állapotának elhagyásakor zöldre vált. 

A motorháztető alatt a sablonok a mentett munkafüzetektől is eltérhetnek. A munkafüzet mentése egy társított Azure Resource Manager erőforrást hoz létre, míg a sablon megnyitásakor létrehozott átmeneti munkafüzethez nincs egyedi erőforrás társítva. Ha többet szeretne megtudni arról, hogy a hozzáférés-vezérlés hogyan legyen kezelve a munkafüzetekben, tekintse meg a [munkafüzetek hozzáférés-vezérlése című cikket](../visualize/workbooks-access-control.md).

### <a name="exploring-a-workbook-template"></a>Munkafüzet-sablon feltárása

Válassza az **alkalmazáshiba elemzése** lehetőséget az egyik alapértelmezett alkalmazás-munkafüzet-sablon megtekintéséhez.

![Képernyőkép az alkalmazáshiba-elemzési sablonról](./media/workbooks-overview/failure-analysis.png)

Amint azt korábban már említettük, a sablon megnyitása egy ideiglenes munkafüzetet hoz létre, amely lehetővé teszi a kommunikációt. Alapértelmezés szerint a munkafüzet olvasási módban nyílik meg, amely csak az eredeti sablon szerzője által létrehozott kívánt elemzési felület adatait jeleníti meg.

Az adott munkafüzet esetében az élmény interaktív. Módosíthatja az előfizetést, a megcélzó alkalmazásokat és a megjeleníteni kívánt adattartományt. Ha elvégezte ezeket a beállításokat, a HTTP-kérések rácsa is interaktív, amely során az egyes sorok kiválasztásakor a jelentés alján található két diagramon megváltoztathatja a megjelenített adatmennyiséget.

### <a name="editing-mode"></a>Szerkesztési mód

Annak megismeréséhez, hogy a munkafüzet-sablon hogyan legyen összekapcsolva, **a Szerkesztés gombra** kattintva kell cserélnie a szerkesztési módba.

![A munkafüzetek szerkesztési gombjának képernyőképe.](./media/workbooks-overview/edit.png)

Miután átváltotta a szerkesztési módot, megfigyelheti, hogy számos **szerkesztési** mező jelenik meg a munkafüzet minden egyes aspektusának megfelelő módon.

![Képernyőkép a Szerkesztés gombról](./media/workbooks-overview/edit-mode.png)

Ha a Szerkesztés gombot azonnal bejelöli a kérelmek adatainak rácsa alatt, láthatjuk, hogy a munkafüzet ezen része egy Application Insights erőforrásból származó adatokkal kapcsolatos Kusto-lekérdezést tartalmaz.

![Képernyőkép az alapul szolgáló Kusto-lekérdezésről](./media/workbooks-overview/kusto.png)

A jobb oldalon található egyéb **szerkesztési** gombokra kattintva megjelenítheti a munkafüzetek, például a Markdown-alapú [szövegmezők](../visualize/workbooks-text-visualizations.md), a [paraméter-kiválasztási](../visualize/workbooks-parameters.md) felhasználóifelület-elemek, valamint az egyéb [diagram/vizualizáció típusok](#visualizations)számát. 

Szerkessze az előre elkészített sablonokat szerkesztési módban, majd módosítsa őket az igényeinek megfelelően, és mentse a saját egyéni munkafüzetét, hogy megismerkedjen a Azure Monitor munkafüzetek lehetséges lehetőségeivel.

## <a name="pinning-visualizations"></a>Vizualizációk rögzítése

A munkafüzetben lévő szöveg-, lekérdezési és mérőszám-lépések a PIN-kód gomb használatával állíthatók be, miközben a munkafüzet PIN-módban van, vagy ha a munkafüzet szerzője engedélyezte az adott elemhez tartozó beállításokat, hogy a PIN-ikon látható legyen. 

A PIN-kód mód eléréséhez kattintson a **Szerkesztés** gombra a szerkesztési mód megadásához, majd a felső sávon a kék PIN-kód ikonra. A képernyő jobb oldalán megjelenik egy egyedi PIN-kód ikon, amely a megfelelő munkafüzet részének *szerkesztési* mezője fölé kerül.

![PIN-kód felhasználói élmény](./media/workbooks-overview/pin-experience.png)

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