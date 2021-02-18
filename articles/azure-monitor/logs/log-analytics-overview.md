---
title: A Azure Monitor Log Analytics áttekintése
description: Leírja, hogy Log Analytics melyik az a Azure Portal, amely a Azure Monitor naplókban lévő adatok elemzésére szolgáló naplók szerkesztésére és futtatására szolgál.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/04/2020
ms.openlocfilehash: 485c2cc6c95f5922025fe887f7c25131e849a01d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100620795"
---
# <a name="overview-of-log-analytics-in-azure-monitor"></a>A Azure Monitor Log Analytics áttekintése
A Log Analytics egy olyan eszköz a Azure Portal, amely Azure Monitor naplókba tartozó adatnapló-lekérdezések szerkesztésére és futtatására szolgál. Írhat olyan egyszerű lekérdezést, amely rekordokat ad vissza, majd a Log Analytics funkcióit használja a rendezésre, szűrésre és elemzésre. Vagy egy összetettebb lekérdezést is írhat a statisztikai elemzések elvégzéséhez, és megjelenítheti az eredményeket egy diagramon egy adott trend azonosításához. Függetlenül attól, hogy a lekérdezések eredményét interaktív módon használja-e, vagy más Azure Monitor funkciókkal, például a log lekérdezési riasztásokkal vagy a munkafüzetekkel kívánja használni, Log Analytics az az eszköz, amelyet írási és tesztelési műveletek végrehajtásához használni fog. 


> [!TIP]
> Ez a cikk a Log Analytics és minden funkciójának leírását tartalmazza. Ha egy oktatóanyagra szeretne ugrani, tekintse meg a [log Analytics oktatóanyagot](../log-query/log-analytics-tutorial.md).



## <a name="starting-log-analytics"></a>Kezdés Log Analytics
Indítsa el Log Analytics a **naplókból** a Azure Portal **Azure monitor** menüjében. Ezt a lehetőséget a legtöbb Azure-erőforráshoz tartozó menüben is megtekintheti. Függetlenül attól, hogy honnan indítja el a alkalmazást, ugyanaz lesz Log Analytics eszköz. A Log Analytics elindításához használt menü meghatározza azokat az adatmennyiségeket, amelyek elérhetők lesznek. Ha a **Azure monitor** vagy a **log Analytics munkaterületek** menüjéből indítja el, akkor a munkaterület összes rekordját elérheti. Ha más típusú erőforrásból választja ki a **naplókat** , az adatok az adott erőforrás naplózási adataira lesznek korlátozva. Részletekért lásd: [a naplózási lekérdezés hatóköre és időbeli tartománya Azure Monitor log Analytics](../log-query/scope.md) .

[![Kezdés Log Analytics](media/log-analytics-overview/start-log-analytics.png)](media/log-analytics-overview/start-log-analytics.png#lightbox)

A Log Analytics indításakor az első dolog, amit látni fog, [például lekérdezéseket](../logs/example-queries.md)tartalmazó párbeszédpanel. Ezeket a megoldás kategorizálja, és böngészheti vagy keresheti meg az adott követelményeknek megfelelő lekérdezéseket. Lehet, hogy megtalálja a megfelelőt, amire szüksége van, vagy töltsön be egyet a szerkesztőben, és módosítsa a szükséges módon. A lekérdezéseken keresztüli tallózással megtudhatja, hogyan írhat saját lekérdezéseket. Természetesen ha üres parancsfájllal szeretne kezdeni, és saját maga is megírni, lezárhatja a lekérdezéseket. Ha vissza szeretné állítani őket, kattintson a képernyő felső részén található **lekérdezésekre** .

## <a name="log-analytics-interface"></a>Log Analytics felület
Az alábbi ábra a Log Analytics különböző összetevőit azonosítja.

[![Log Analytics](media/log-analytics-overview/log-analytics.png)](media/log-analytics-overview/log-analytics.png#lightbox)

### <a name="1-top-action-bar"></a>1. felső műveleti sáv
A lekérdezésnek a lekérdezési ablakban való használatának szabályozása.

| Beállítás | Leírás |
|:---|:---|
| Hatókör | Meghatározza a lekérdezéshez használt adattartományt. Ez lehet egy Log Analytics munkaterület összes adata, vagy egy adott erőforrás adatai több munkaterületen. Lásd a [lekérdezés hatókörét](../log-query/scope.md). |
| Futtatás gomb | Kattintson ide a kijelölt lekérdezés futtatásához a lekérdezési ablakban. Egy lekérdezés futtatásához nyomja le a SHIFT + ENTER billentyűkombinációt is. |
| Időválasztó | Válassza ki a lekérdezés számára elérhető adattartományt. Ezt felülbírálja, ha a lekérdezésben időszűrőt is tartalmaz. Lásd: [Azure Monitor log Analytics napló lekérdezési hatóköre és időtartománya](../log-query/scope.md). |
| Mentés gomb | Mentse a lekérdezést a munkaterülethez tartozó lekérdezési Tallózóba. |
 a képernyő jobb oldalán található Másolás gombot | A lekérdezésre, a lekérdezési szövegre vagy a lekérdezés eredményeire mutató hivatkozás másolása a vágólapra. |
| Új riasztási szabály gomb | Hozzon létre egy új fület üres lekérdezéssel. |
| Exportálás gomb | Exportálja a lekérdezés eredményeit egy CSV-fájlba vagy a lekérdezésbe, hogy Power Query a képlet nyelvi formátumát a Power BI-ban való használathoz. |
| Rögzítés az irányítópulton gomb | Adja hozzá a lekérdezés eredményét egy Azure-irányítópulthoz. |
| Lekérdezés formázása gomb | A kijelölt szöveg elrendezése az olvashatóság érdekében. |
| Példa lekérdezések gombra | Nyissa meg az Log Analytics megnyitásakor megjelenő lekérdezési példák párbeszédpanelt. |
| Lekérdezési tallózó gomb | Nyissa meg a **query Explorert** , amely hozzáférést biztosít a mentett lekérdezésekhez a munkaterületen. |


### <a name="2-sidebar"></a>2. Sidebar
A munkaterületen található táblák listája, a lekérdezési lekérdezések és a szűrési beállítások az aktuális lekérdezéshez.

| Tab | Description |
|:---|:---|
| Táblák | Felsorolja a kiválasztott hatókör részét képező táblákat. Válassza a **Csoportosítás** a következővel lehetőséget a táblák csoportosításának módosításához. Vigye a kurzort a táblázat neve fölé, és jelenítse meg a táblázat leírását, valamint a dokumentáció megtekintését és az adatmegjelenítést tartalmazó párbeszédpanelt. A táblázat kibontásával megtekintheti az oszlopait. Kattintson duplán egy táblázatra vagy oszlop nevére a lekérdezéshez való hozzáadásához. |
| Lekérdezések | A lekérdezési ablakban megnyitható lekérdezési példák listája. Ez az Log Analytics megnyitásakor megjelenő lista. Válassza a **Csoportosítás** lehetőséget a lekérdezések csoportosításának módosításához. Kattintson duplán egy lekérdezésre a lekérdezési ablakba való felvételéhez, vagy vigye a kurzort a többi beállításhoz. |
| Szűrő | A lekérdezés eredményei alapján hozza létre a szűrő beállításait. A lekérdezés futtatása után az oszlopok különböző értékekkel jelennek meg az eredményekből. Jelöljön ki egy vagy több értéket, majd kattintson az **alkalmazás & Futtatás** elemre, és adjon hozzá egy **Where** parancsot a lekérdezéshez, majd futtassa újra. |

### <a name="3-query-window"></a>3. lekérdezési ablak
A lekérdezési ablakban szerkesztheti a lekérdezést. Ez magában foglalja a KQL parancsok és színkódolások IntelliSense-használatát az olvashatóság javítása érdekében. Kattintson az **+** ablak tetején egy másik lap megnyitásához.

Egyetlen ablak több lekérdezést is tartalmazhat. Egy lekérdezés nem tartalmazhat üres sorokat, így több lekérdezés is elkülöníthető egy olyan ablakban, amely egy vagy több üres sorral rendelkezik. Az aktuális lekérdezés az a kurzor, amelyben bárhol elhelyezhető.

Az aktuális lekérdezés futtatásához kattintson a **Futtatás** gombra, vagy nyomja le a SHIFT + ENTER billentyűkombinációt.

### <a name="4-results-window"></a>4. eredmények ablak
A lekérdezés eredményei az eredmények ablakban jelennek meg. Alapértelmezés szerint a rendszer táblaként jeleníti meg az eredményeket. Diagramként való megjelenítéshez válassza a **diagram** lehetőséget az eredmények ablakban, vagy adjon hozzá egy **renderelési** parancsot a lekérdezéshez.

#### <a name="results-view"></a>Eredmények nézet
Megjeleníti a lekérdezés eredményeit oszlopok és sorok szerint rendezve. Kattintson a sor bal oldalán az értékek kibontásához. Kattintson az **oszlopok** legördülő listára az oszlopok listájának módosításához. Rendezze az eredményeket az oszlop nevére kattintva. Az eredmények szűréséhez kattintson az oszlopnév melletti tölcsérre. Törölje a szűrőket, és állítsa vissza a rendezést a lekérdezés ismételt futtatásával.

Válassza ki a **csoportok oszlopokat** a csoportosítási sáv megjelenítéséhez a lekérdezés eredményei felett. Az eredményeket bármely oszlop szerint csoportosíthatja, ha a sáv fölé húzza. Hozzon létre beágyazott csoportokat az eredményekben további oszlopok hozzáadásával. 

#### <a name="chart-view"></a>Diagramnézet
Megjeleníti az eredményeket több elérhető diagramtípus közül. A diagram típusát megadhatja egy **renderelési** parancsban a lekérdezésben, vagy kiválaszthatja a **vizualizáció típusa** legördülő menüből.

| Beállítás | Leírás |
|:---|:---|
| **Vizualizáció típusa** | A megjelenítendő diagram típusa. |
| **X tengely** | Az X tengelyhez használandó eredmények oszlopa 
| **Y tengely** | Az Y tengelyhez használandó eredmények oszlopa. Ez általában egy numerikus oszlop. |
| **Felosztási szempont** | A diagram adatsorozatát meghatározó eredmények oszlopa. Létrejön egy adatsorozat az oszlop minden értékéhez. |
| **Összesítés** | Az Y tengely numerikus értékein végrehajtandó összesítés típusa. |

## <a name="relationship-to-azure-data-explorer"></a>Kapcsolat az Azure Adatkezelő
Ha már ismeri az Azure Adatkezelő webes felhasználói felületét, Log Analytics érdemes megismernie a munkát. Ennek az az oka, hogy az Azure Adatkezelőra épül, és ugyanazt a Kusto lekérdezési nyelvet (KQL) használja. Log Analytics olyan szolgáltatásokat biztosít, amelyek Azure Monitor például az időtartomány szerinti szűrésre, valamint a riasztási szabályok lekérdezésből való létrehozásának képességére. Mindkét eszköz tartalmaz egy Explorert, amely lehetővé teszi az elérhető táblák szerkezetének vizsgálatát, de az Azure Adatkezelő webes felhasználói felülete elsősorban az Azure Adatkezelő-adatbázisok tábláival működik, miközben a Log Analytics együttműködik Log Analytics munkaterületen található táblázatokkal. 

## <a name="next-steps"></a>Következő lépések
- [Útmutató a Azure Portal log Analyticsjának használatáról](../log-query/log-analytics-tutorial.md).
- [Útmutató a lekérdezések írásához](../log-query/get-started-queries.md).