---
title: Az Azure Monitor-munkafüzetek áttekintése
description: Megtudhatja, hogyan biztosítanak a munkafüzetek rugalmas vásznat az adatelemzéshez és a részletes vizualizációs jelentések létrehozásához a Azure Portal.
services: azure-monitor
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: 3d75d7605ba082aac84973aef247de79d55b4c9c
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482772"
---
# <a name="azure-monitor-workbooks"></a>Azure Monitor munkafüzetek

A munkafüzetek Azure Portalon belül kínálnak rugalmas vásznat az adatelemzéshez és a részletgazdag vizuális jelentések létrehozásához. Lehetővé teszik, hogy az Azure-ból több adatforrásra is rákoppintajon, és egységes interaktív felhasználói élményben egyesítse őket.

Az alábbi videó a munkafüzetek létrehozását bemutató videót tartalmaz.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4B4Ap]

## <a name="data-sources"></a>Adatforrások

A munkafüzetek több Azure-on belüli adatforrásból képesek adatokat lekérdezni. A munkafüzetek készítői ezeket az adatokat átalakítva megállapításokat tehetnek a mögöttes összetevők rendelkezésre állásáról, teljesítményéről, használatáról és általános állapotáról. Például a virtuális gépek teljesítménynaplóinak elemzése a magas CPU- vagy kevés memóriapéldány azonosításához, és az eredmények rácsként való megjelenítése egy interaktív jelentésben.
  
A munkafüzet legnagyobb előnye azonban az a képesség, hogy különálló forrásokból származó adatokat kombinálnak egyetlen jelentésben. Ez lehetővé teszi összetett erőforrásnézetek létrehozását vagy az erőforrások közötti illesztéseket, így olyan adat- és elemzési eredmények is gazdagabbak, amelyek egyébként lehetetlenek volna.

A munkafüzetek jelenleg a következő adatforrásokkal kompatibilisek:

* [Naplók](../visualize/workbooks-data-sources.md#logs)
* [Metrikák](../visualize/workbooks-data-sources.md#metrics)
* [Azure Resource Graph](../visualize/workbooks-data-sources.md#azure-resource-graph)
* [Riasztások (előzetes verzió)](../visualize/workbooks-data-sources.md#alerts-preview)
* [Számítási feladat állapota](../visualize/workbooks-data-sources.md#workload-health)
* [Azure Resource Health](../visualize/workbooks-data-sources.md#azure-resource-health)
* [Azure Data Explorer](../visualize/workbooks-data-sources.md#azure-data-explorer)

## <a name="visualizations"></a>Vizualizációk

A munkafüzetek számos lehetőséget kínálnak az adatok megjelenítésére. Az egyes vizualizációtípusokkal kapcsolatban az alábbi hivatkozásokon talál részletes példákat:

* [Szöveg](../visualize/workbooks-text-visualizations.md)
* [Diagramok](../visualize/workbooks-chart-visualizations.md)
* [Rácsok](../visualize/workbooks-grid-visualizations.md)
* [Csempék](../visualize/workbooks-tile-visualizations.md)
* [Fák](../visualize/workbooks-tree-visualizations.md)
* [Grafikonok](../visualize/workbooks-graph-visualizations.md)
* [Összetett sáv](../visualize/workbooks-composite-bar.md)

:::image type="content" source="./media/workbooks-overview/visualizations.png" alt-text="Példa munkafüzet-vizualizációkra." border="false" lightbox="./media/workbooks-overview/visualizations.png":::

### <a name="pinning-visualizations"></a>Vizualizációk rögzítése

A munkafüzetek szöveg-, lekérdezés- és metrika-lépései rögzíthetőek a rögzítés gombbal, amíg a munkafüzet pin módban van, vagy ha a munkafüzet szerzője engedélyezte az elem beállításait a rögzítés ikon láthatóvá tételéhez.

A rögzítési mód eléréséhez kattintson a **Szerkesztés gombra** a szerkesztési módba való belépéshez, majd válassza a felső sávon a kék gombostű ikont. Ekkor a képernyő jobb oldalán megjelenik a  munkafüzet megfelelő részeinek Szerkesztés mezője fölött egy-egy rögzítés ikon.

:::image type="content" source="./media/workbooks-overview/pin-experience.png" alt-text="Képernyőkép a rögzítési élményről." border="false":::

> [!NOTE]
> A munkafüzet állapota a rögzítéskor lesz mentve, és az irányítópulton rögzített munkafüzetek nem frissülnek, ha az alapul szolgáló munkafüzetet módosítják. A rögzített munkafüzetrészek frissítéséhez törölnie kell, majd újra ki kell tűzni azt.

## <a name="getting-started"></a>Első lépések

A munkafüzetek használatának felfedezéséhez először nyissa meg a Azure Monitor szolgáltatást. Ehhez írja be a **Monitor** szöveget a keresőmezőbe a Azure Portal.

Ezután válassza a **Munkafüzetek lehetőséget.**

:::image type="content" source="./media/workbooks-overview/workbooks.png" alt-text="Képernyőkép a piros mezőben kiemelt Munkafüzetek gombról." border="false":::

### <a name="gallery"></a>Katalógus

A katalógus kényelmessé teszi a munkafüzetek rendszerezését, rendezését és kezelését minden típusú munkafüzetben.

:::image type="content" source="./media/workbooks-overview/gallery-all-tab.png" alt-text="Képernyőkép a katalógusról a Minden lapon." lightbox="media/workbooks-overview/gallery-all-tab.png":::

#### <a name="gallery-tabs"></a>Katalógus lapjai

A katalógusban négy lap található a munkafüzettípusok rendszerezéséhez.

| Tab              | Leírás                                       |
|------------------|---------------------------------------------------|
| Mind | Minden típus első négy elemét jeleníti meg: munkafüzeteket, nyilvános sablonokat és saját sablonokat. A munkafüzetek módosítási dátum szerint vannak rendezve, így a legutóbbi nyolc módosított munkafüzetet fogja látni.|
| Munkafüzetek | Az összes létrehozott vagy megosztott elérhető munkafüzet listáját jeleníti meg. |
| Nyilvános sablonok | A Microsoft által közzétett, használatra kész munkafüzetsablonok listáját jeleníti meg. Kategória szerint csoportosítva. |
| Saját sablonok | Az összes létrehozott vagy megosztott elérhető üzembe helyezett munkafüzetsablon listáját jeleníti meg. Kategória szerint csoportosítva. |

#### <a name="features"></a>Funkciók

* Minden lapon található egy rács, amely a munkafüzetek adatait tartalmaz. Tartalmazza a leírást, az utolsó módosítás dátumát, a címkéket, az előfizetést, az erőforráscsoportot, a régiót és a megosztott állapotot. A munkafüzeteket ezen információk alapján is rendezheti.
* Szűrés erőforráscsoport, előfizetések, munkafüzet/sablon neve vagy sablonkategória alapján.
* Jelöljön ki több munkafüzetet a törléshez vagy tömeges törléshez.
* Minden munkafüzet rendelkezik egy helyi menüvel (három pont/három pont a végén), és a munkafüzet kiválasztásakor megnyílik a gyorsműveletek listája.
    * Erőforrás megtekintése – A munkafüzet erőforráslapjának elérése a munkafüzet erőforrás-azonosítójának megtekintéséhez, címkék hozzáadásához, zárolások kezeléséhez stb.
    * Munkafüzet törlése vagy átnevezése.
    * Munkafüzetet rögzít az irányítópulton.

### <a name="workbooks-versus-workbook-templates"></a>Munkafüzetek és munkafüzetsablonok

Egy munkafüzetet zöld _színnel,_ néhány munkafüzetsablont pedig lila _színnel_ láthat. A sablonok válogatott jelentésekként szolgálnak, amelyek több felhasználó és csapat számára is rugalmasan felhasználhatók. A sablonok megnyitásakor létrejön egy átmeneti munkafüzet, amely a sablon tartalmával van feltöltve.

Módosíthatja a sablonalapú munkafüzet paramétereit, és elemzéseket hajthat végre anélkül, hogy a munkatársak számára a jövőbeli jelentéskészítési élmény megszakadása miatt félnie kell. Ha megnyit egy sablont, módosításokat végez, majd a mentés ikont választva munkafüzetként menti a sablont, amely ekkor zöld színnel jelenik meg, az eredeti sablont változatlanul hagyva.

A háttérben a sablonok is különböznek a mentett munkafüzetektől. A munkafüzet mentése társított erőforrást Azure Resource Manager, míg a sablon megnyitásakor létrehozott átmeneti munkafüzethez nem tartozik egyedi erőforrás. A hozzáférés-vezérlés munkafüzetekben való felügyeletének további tudnivalókért tekintse meg a [munkafüzetek hozzáférés-vezérlését tartalmazó cikket.](../visualize/workbooks-access-control.md)

### <a name="exploring-a-workbook-template"></a>Munkafüzetsablon felfedezése

Válassza **az Alkalmazáshiba-elemzés** lehetőséget az alapértelmezett alkalmazás-munkafüzetsablonok egyikének kiválasztásához.

:::image type="content" source="./media/workbooks-overview/failure-analysis.png" alt-text="Képernyőkép az alkalmazáshiba-elemzési sablonról." border="false" lightbox="./media/workbooks-overview/failure-analysis.png":::

Ahogy korábban már említettük, a sablon megnyitása ideiglenes munkafüzetet hoz létre, amelyet ön is használni tud. Alapértelmezés szerint a munkafüzet olvasó módban nyílik meg, amely csak az eredeti sablon szerzője által létrehozott kívánt elemzési élmény adatait jeleníti meg.

Ebben a munkafüzetben a felhasználói élmény interaktív. Módosíthatja az előfizetést, a megcélzott alkalmazásokat és a megjeleníteni kívánt adatok időtartományát. Miután kiválasztotta ezeket a kijelöléseket, a HTTP-kérések rácsa is interaktív lesz, így egy adott sor kijelölésével megváltoztathatja, hogy milyen adatok jelennek meg a jelentés alján található két diagramon.

### <a name="editing-mode"></a>Szerkesztési mód

A munkafüzetsablon egymáshoz kapcsolásának módját szerkesztési módra kell cserélnie a **Szerkesztés lehetőség kiválasztásával.**

:::image type="content" source="./media/workbooks-overview/edit.png" alt-text="Képernyőkép a munkafüzetek Szerkesztés gombjáról." border="false" :::

Miután átváltott szerkesztési módra, látni  fogja, hogy a munkafüzet egyes aspektusának megfelelő szerkesztési mezők jelennek meg a jobb oldalon.

:::image type="content" source="./media/workbooks-overview/edit-mode.png" alt-text="A Szerkesztés gomb képernyőképe." border="false" lightbox="./media/workbooks-overview/edit-mode.png":::

Ha a szerkesztés gombra kattintunk közvetlenül a kérelemadatok rácsa alatt, láthatjuk, hogy a munkafüzetnek ez a része egy Kusto-lekérdezésből áll, amely egy Application Insights tartalmaz.

:::image type="content" source="./media/workbooks-overview/kusto.png" alt-text="A mögöttes Kusto-lekérdezés képernyőképe." border="false" lightbox="./media/workbooks-overview/kusto.png":::

Ha a jobb **oldalon** kiválasztja a többi Szerkesztés gombot, akkor több olyan alapvető összetevőt is felfedhet, amelyek munkafüzeteket tartalmaznak, például Markdown-alapú szövegmezőket, [](../visualize/workbooks-text-visualizations.md)paraméterkijelölési felhasználói felületi elemeket és más [](../visualize/workbooks-parameters.md) [diagram-/vizualizációtípusokat.](#visualizations)

Az előre felépített sablonok szerkesztési módban való felfedezése, majd az igényeknek megfelelő módosítása és a saját egyéni munkafüzet mentése kiváló módja annak, hogy megismerjük a Azure Monitor lehetőségeket.

## <a name="dashboard-time-ranges"></a>Irányítópult időtartományai

A rögzített munkafüzet lekérdezési részei tiszteletben tartják az irányítópult időtartományát, ha a rögzített elem időtartomány-paraméter *használatára van konfigurálva.* A rendszer az irányítópult időtartomány-értékét használja az időtartomány-paraméter értékeként, és az irányítópult időtartományának bármely módosítása miatt a rögzített elem frissül. Ha egy rögzített rész az irányítópult időtartományát használja, a rögzített rész frissítésének felirata jelenik meg, amely az irányítópult időtartományát mutatja, amikor megváltozik az időtartomány.

Emellett az időtartomány-paraméterrel rögzített munkafüzetrészek automatikusan frissülnek az irányítópult időtartománya által meghatározott sebességgel. A lekérdezés legutóbbi futtatása a rögzített rész alcímében jelenik meg.

Ha egy rögzített lépés explicit módon beállított időtartományt tartalmaz (nem használ időtartomány-paramétert), akkor a rendszer mindig ezt az időtartományt használja az irányítópulthoz, függetlenül az irányítópult beállításaitól. A rögzített rész felirata nem mutatja az irányítópult időtartományát, és a lekérdezés nem frissül automatikusan az irányítópulton. Az alcímen a lekérdezés legutóbbi végrehajtása látható.

> [!NOTE]
> Az egyesítési *adatforrást* használó lekérdezések jelenleg nem támogatottak az irányítópultokon való rögzítéskor.

## <a name="sharing-workbook-templates"></a>Munkafüzetsablonok megosztása

Miután saját munkafüzetsablonokat szeretne létrehozni, érdemes lehet megosztani a szélesebb közösséggel. További tudnivalókért és az alapértelmezett katalógusnézet részét nem Azure Monitor további sablonok megtekintéséhez látogasson el [a GitHub-adattárunkba.](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md) A meglévő munkafüzetek tallózásához látogasson el a [Munkafüzet könyvtárba a](https://github.com/microsoft/Application-Insights-Workbooks/tree/master/Workbooks) GitHubon.

## <a name="next-step"></a>Következő lépés

* [A munkafüzetekkel](#visualizations) kapcsolatos további tudnivalókat számos vizualizációs lehetőséggel megismerheti.
* [Vezérelni](../visualize/workbooks-access-control.md) és megosztani a munkafüzet erőforrásaihoz való hozzáférést.