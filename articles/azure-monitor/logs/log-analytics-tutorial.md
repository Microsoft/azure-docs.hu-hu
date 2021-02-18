---
title: Log Analytics-oktatóanyag
description: Ebből az oktatóanyagból megtudhatja, hogyan használhatja a Azure Monitor Log Analytics funkcióit egy naplófájl futtatására és az eredmények elemzésére a Azure Portal.
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 10/07/2020
ms.openlocfilehash: 038663a6b8fe015f36f70b7bf13d25accb470903
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100627871"
---
# <a name="log-analytics-tutorial"></a>Log Analytics-oktatóanyag
A Log Analytics egy eszköz a Azure Portalban, amellyel szerkesztheti és futtathatja a Azure Monitor naplók által összegyűjtött adatokból származó naplózott lekérdezéseket, és interaktív módon elemezheti az eredményeket. Log Analytics lekérdezések használatával lekérheti az adott feltételeknek megfelelő rekordokat, azonosíthatja a trendeket, elemezheti a mintákat, és különféle elemzéseket biztosíthat az adataihoz. 

Ez az oktatóanyag végigvezeti a Log Analytics felületen, és megkezdi az alapszintű lekérdezéseket, és bemutatja, hogyan dolgozhat az eredményekkel. Az alábbiakat sajátítja majd el:

> [!div class="checklist"]
> * A naplózási Adatséma ismertetése
> * Egyszerű lekérdezések írása és futtatása, valamint a lekérdezések időtartományának módosítása
> * Lekérdezés eredményeinek szűrése, rendezése és csoportosítása
> * A lekérdezési eredmények vizualizációinak megtekintése, módosítása és megosztása
> * Lekérdezések és eredmények betöltése, exportálása és másolása

> [!IMPORTANT]
> Ez az oktatóanyag a Log Analytics funkcióit használja egy lekérdezés létrehozásához és futtatásához a lekérdezés használata helyett. A Log Analytics-funkciók használatával egyetlen lekérdezést hozhat létre, és egy másik példa lekérdezést is használhat. Ha készen áll a lekérdezések szintaxisának megismerésére és a lekérdezés közvetlen szerkesztésének megkezdésére, folytassa a [Kusto lekérdezési nyelvének oktatóanyagával](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor). Ez az oktatóanyag számos példás lekérdezést mutat be, amelyeket Log Analytics szerkeszthet és futtathat, és számos olyan funkciót kihasználhat, amelyeket ebben az oktatóanyagban elsajátíthat.


## <a name="prerequisites"></a>Előfeltételek
Ez az oktatóanyag a [log Analytics bemutató környezetét](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade)használja, amely rengeteg, a lekérdezéseket támogató mintát tartalmaz. Használhatja saját Azure-előfizetését is, de előfordulhat, hogy nem rendelkezik azonos táblákban található adataival.

## <a name="open-log-analytics"></a>Log Analytics megnyitása
Nyissa meg a [log Analytics bemutató környezetet](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade) , vagy válassza a **naplók** lehetőséget az előfizetés Azure monitor menüjében. Ez a beállítás a kezdeti hatókört egy Log Analytics munkaterületre állítja be, ami azt jelenti, hogy a lekérdezés az adott munkaterületen lévő összes adattal ki lesz választva. Ha az Azure-erőforrás menüjéből kiválasztja a **naplók** lehetőséget, a hatókör csak az adott erőforrás rekordjaira van beállítva. A hatókör részleteiért lásd a [naplózási lekérdezés hatókörét](../log-query/scope.md) .

A hatókört a képernyő bal felső sarkában tekintheti meg. Ha saját környezetét használja, egy másik hatókört választhat, de ez a lehetőség nem érhető el a bemutató környezetben.

[![Lekérdezési hatókör](media/log-analytics-tutorial/scope.png)](media/log-analytics-tutorial/scope.png#lightbox)

## <a name="table-schema"></a>Tábla sémája
A képernyő bal oldalán található a **Tables (táblák** ) lap, amely lehetővé teszi az aktuális hatókörben elérhető táblák vizsgálatát. Ezek alapértelmezés szerint a **megoldás** szerint vannak csoportosítva, de a csoportosításuk vagy szűrésük módosítható. 

Bontsa ki a **naplózási felügyeleti** megoldást, és keresse meg a **AzureActivity** táblát. A táblázat kibontásával megtekintheti a sémáját, vagy áthelyezheti a nevét, hogy további információkat jelenítsen meg. 

[![Táblák nézet](media/log-analytics-tutorial/table-details.png)](media/log-analytics-tutorial/table-details.png#lightbox)

Kattintson a **továbbiak** gombra, hogy megnyissa a táblázat hivatkozását, amely az egyes táblákat és oszlopokat dokumentálja. Az **adatok előnézete** elemre kattintva gyorsan megtekintheti a táblázat néhány új rekordját. Ez akkor lehet hasznos, ha azt szeretné, hogy ez a várt adatok legyenek, mielőtt ténylegesen futtat egy lekérdezést.

[![Mintaadatok](media/log-analytics-tutorial/sample-data.png)](media/log-analytics-tutorial/sample-data.png#lightbox)

## <a name="write-a-query"></a>Lekérdezés írása
Nézzük meg, és írjon egy lekérdezést a **AzureActivity** tábla használatával. Kattintson duplán a nevére a lekérdezési ablakba való felvételhez. Közvetlenül is beírhatja az ablakba, és még akkor is beolvashatja az IntelliSense-t, hogy az aktuális hatókörben és a KQL parancsban szereplő táblák nevei is befejeződik.

Ez az a legegyszerűbb lekérdezés, amelyet írhatunk. Csak egy tábla összes rekordját adja vissza. Futtassa azt a **Futtatás** gombra kattintva, vagy a SHIFT + ENTER billentyűkombináció lenyomásával a kurzort a lekérdezés szövegében bárhol elhelyezve.

[![Lekérdezés eredményei](media/log-analytics-tutorial/query-results.png)](media/log-analytics-tutorial/query-results.png#lightbox)

Láthatja, hogy eredményeink vannak. A lekérdezés által visszaadott rekordok száma a jobb alsó sarokban jelenik meg. 

## <a name="filter"></a>Szűrő

Vegyünk fel egy szűrőt a lekérdezéshez, hogy csökkentse a visszaadott rekordok számát. Válassza a **szűrő** fület a bal oldali ablaktáblán. Ez a lekérdezés eredményei között különböző oszlopokat jelenít meg, amelyek segítségével szűrheti az eredményeket. Az oszlopok legfelső értékei az adott értékkel rendelkező rekordok számával jelennek meg. Kattintson a **felügyelet** elemre a **CategoryValue** területen, majd **alkalmazza & Futtatás parancsot**. 

[![Lekérdezési ablaktábla](media/log-analytics-tutorial/query-pane.png)](media/log-analytics-tutorial/query-pane.png#lightbox)

A kiválasztott értékkel a lekérdezésbe bekerül egy **Where** utasítás. Az eredmények mostantól csak az adott értékkel rendelkező rekordokat tartalmazzák, így láthatja, hogy a rekordok száma csökken.

[![Lekérdezési eredmények szűrve](media/log-analytics-tutorial/query-results-filter-01.png)](media/log-analytics-tutorial/query-results-filter-01.png#lightbox)


## <a name="time-range"></a>Időtartomány
Egy Log Analytics munkaterület összes táblájának van egy **TimeGenerated** nevű oszlopa, amely a rekord létrehozásának időpontja. Minden lekérdezésnek van olyan időtartománya, amely az eredményeket az adott tartományon belüli **TimeGenerated** értékre korlátozza. Az időtartomány beállítható a lekérdezésben vagy a képernyő felső részén található választóval.

Alapértelmezés szerint a lekérdezés a rekordokat az elmúlt 24 órában fogja visszaadni. Válassza ki az **időtartomány** legördülő menüt, és módosítsa **7 napra**. Kattintson ismét a **Futtatás** elemre az eredmények visszaküldéséhez. Láthatja, hogy a rendszer visszaadja az eredményeket, de van egy üzenet arról, hogy nem látjuk az összes eredményt. Ennek az az oka, hogy Log Analytics legfeljebb 10 000 rekordot tud visszaadni, és a lekérdezés további rekordokat adott vissza. 

[![Időtartomány](media/log-analytics-tutorial/query-results-max.png)](media/log-analytics-tutorial/query-results-max.png#lightbox)


## <a name="multiple-query-conditions"></a>Több lekérdezési feltétel
Csökkentse az eredményeket további szűrési feltétel hozzáadásával. A lekérdezések tetszőleges számú szűrőt tartalmazhatnak, amelyek pontosan a kívánt rekordok halmazát célozzák meg. Válassza  a sikeres **oszlopazonosítón** elemet, majd kattintson az **alkalmazás & Futtatás** elemre. 

[![Lekérdezés eredménye több szűrő](media/log-analytics-tutorial/query-results-filter-02.png)](media/log-analytics-tutorial/query-results-filter-02.png#lightbox)


## <a name="analyze-results"></a>Eredmények elemzése
A lekérdezések írásának és futtatásának segítése mellett Log Analytics az eredményekkel való munkához nyújt szolgáltatásokat. Először egy rekord kibontásával tekintheti meg az összes oszlop értékeit.

[![Rekord kibontása](media/log-analytics-tutorial/expand-record.png)](media/log-analytics-tutorial/expand-record.png#lightbox)

Kattintson bármely oszlop nevére, hogy az adott oszlop szerint rendezze az eredményeket. A szűrési feltételhez kattintson a mellette található szűrő ikonra. Ez hasonló a szűrő feltételének a lekérdezéshez való hozzáadásához, azzal a különbséggel, hogy a szűrő törlődik, ha a lekérdezés újra fut. Ezt a módszert akkor használja, ha a rekordok egy halmazát szeretné gyorsan elemezni az interaktív elemzés részeként.

Beállíthat például egy szűrőt a **CallerIpAddress** oszlopra, hogy a rekordok egyetlen hívóra legyenek korlátozva. 

[![Lekérdezési eredmények szűrője](media/log-analytics-tutorial/query-results-filter.png)](media/log-analytics-tutorial/query-results-filter.png#lightbox)

Az eredmények szűrése helyett a rekordok csoportosítása egy adott oszlop alapján végezhető el. Törölje az imént létrehozott szűrőt, majd kapcsolja be a **csoport oszlopai** csúszkát. 

[![Csoportok oszlopai](media/log-analytics-tutorial/query-results-group-columns.png)](media/log-analytics-tutorial/query-results-group-columns.png#lightbox)

Most húzza a **CallerIpAddress** oszlopot a csoportosítási sorba. Az eredmények mostantól az oszlop szerint vannak rendszerezve, és az egyes csoportok összecsukása segíti az elemzést.

[![Lekérdezés eredményei csoportosítva](media/log-analytics-tutorial/query-results-grouped.png)](media/log-analytics-tutorial/query-results-grouped.png#lightbox)

## <a name="work-with-charts"></a>Diagramok használata
Nézzük meg egy olyan lekérdezést, amely egy diagramon megtekinthető numerikus adattípust használ. A lekérdezés létrehozása helyett egy példa típusú lekérdezést fogunk kijelölni.

Kattintson a bal oldali ablaktábla **lekérdezések** elemére. Ez a panel példalekérdezéseket tartalmaz, amelyeket hozzáadhat a lekérdezési ablakhoz. Ha saját munkaterületet használ, többféle lekérdezéssel több kategóriát is használhat, de ha a bemutató környezetet használja, akkor csak egyetlen **log Analytics munkaterület** -kategóriát láthat. Bontsa ki az elemet, hogy megtekinthesse a lekérdezéseket a kategóriában.

Kattintson a **kérelmek száma ResponseCode alapján** nevű lekérdezésre. Ezzel hozzáadja a lekérdezést a lekérdezési ablakhoz. Figyelje meg, hogy az új lekérdezés el van választva a másiktól egy üres vonallal. A KQL egy lekérdezése akkor ér véget, amikor üres vonalat észlel, ezért ezeket külön lekérdezéseknek tekinti a rendszer. 

[![Új lekérdezés](media/log-analytics-tutorial/example-query.png)](media/log-analytics-tutorial/example-query.png#lightbox)

Az aktuális lekérdezés az a hely, amelyre a kurzor be van pozícionálva. Láthatja, hogy az első lekérdezés ki van jelölve, ami azt jelzi, hogy az aktuális lekérdezés. Kattintson bárhová az új lekérdezésben a kiválasztásához, majd kattintson a **Futtatás** gombra a futtatásához.

[![Lekérdezési eredmények diagramja](media/log-analytics-tutorial/example-query-output-chart.png)](media/log-analytics-tutorial/example-query-output-chart.png#lightbox)

Figyelje meg, hogy ez a kimenet diagram a legutóbbi lekérdezéshez hasonló táblázat helyett. Ennek oka, hogy a példában a lekérdezés egy [Render](/azure/data-explorer/kusto/query/renderoperator?pivots=azuremonitor) parancsot használ a végén. Figyelje meg, hogy különböző lehetőségek állnak rendelkezésre a diagrammal való használathoz, például egy másik típusra való váltáshoz.

Ha a lekérdezés kimenetét táblaként szeretné megtekinteni, próbálkozzon az **eredmények** kiválasztásával. 

[![Lekérdezési eredmények táblázata](media/log-analytics-tutorial/example-query-output-table.png)](media/log-analytics-tutorial/example-query-output-table.png#lightbox)



## <a name="next-steps"></a>Következő lépések

Most, hogy már ismeri a Log Analytics használatát, fejezze be az oktatóanyagot a naplók használatával.
> [!div class="nextstepaction"]
> [Írási Azure Monitor log-lekérdezések](get-started-queries.md)
