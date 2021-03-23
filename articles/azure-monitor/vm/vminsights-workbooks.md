---
title: Interaktív jelentések készítése a virtuális gépekről a munkafüzetekből
description: Egyszerűsítse a komplex jelentéskészítést előre definiált és egyéni paraméteres munkafüzetekkel a virtuális gépekhez.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 874df4301165aaf253dd0f4316adee7ab9ce7ce5
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104798850"
---
# <a name="create-interactive-reports-vm-insights-with-workbooks"></a>Interaktív jelentések készítése a virtuális gépekről a munkafüzetekből

A munkafüzetek szövegeket, [naplókat](/azure/data-explorer/kusto/query/), metrikákat és paramétereket egyesítenek gazdag interaktív jelentésekben. A munkafüzetek szerkeszthető más csapattagok számára, akik ugyanahhoz az Azure-erőforrásokhoz férnek hozzá.

A munkafüzetek hasznosak a következő forgatókönyvekhez:

* A virtuális gép használatának megvizsgálása, ha nem ismeri a kamat metrikáit: CPU-kihasználtság, lemezterület, memória, hálózati függőségek stb. A többi használati elemzési eszköztől eltérően a munkafüzetek több fajta vizualizációt és elemzést is lehetővé tesznek, így nagyszerűen használhatók az ilyen típusú, ingyenes űrlapos feltáráshoz.
* Elmagyarázza a csapatának, hogy a közelmúltban kiépített virtuális gép hogyan végezze el a teljesítménymutatók megjelenítését a főszámlálók és más naplózási események mérőszámai alapján.
* Megoszthatja a virtuális gép átméretezési kísérletének eredményét a csapat más tagjaival. Megadhatja a kísérlet szöveggel kapcsolatos céljait, majd megjelenítheti a kísérlet kiértékeléséhez használt egyes használati metrikákat és elemzési lekérdezéseket, valamint egyértelmű hívási feladatokkal, hogy az egyes metrikák a fenti vagy a cél alatt voltak-e.
* A leállás következményeinek jelentése a virtuális gép használatáról, az adatok összevonásáról, a szöveg magyarázatáról, valamint a következő lépések megvitatására a leállások jövőbeli megelőzése érdekében.

A következő táblázat összefoglalja azokat a munkafüzeteket, amelyekkel a virtuális gépek bepillantást nyerhet a kezdéshez.

| Munkafüzet | Leírás | Hatókör |
|----------|-------------|-------|
| Teljesítmény | A Top N List és Diagrams nézet testreszabható verzióját jeleníti meg egyetlen munkafüzetben, amely az összes engedélyezett Log Analytics teljesítményszámlálókat kihasználja.| Több virtuális gép |
| Teljesítményszámlálók | Felső N diagramos nézet a teljesítményszámlálók széles készletében. | Több virtuális gép |
| Kapcsolatok | A kapcsolatok részletes áttekintést nyújtanak a figyelt virtuális gépek bejövő és kimenő kapcsolatairól. | Több virtuális gép |
| Aktív portok | A figyelt virtuális gépek portjaira és a kiválasztott időkeretbe tartozó tevékenységekre vonatkozó folyamatok listáját jeleníti meg. | Több virtuális gép |
| Nyitott portok | Megadja a megfigyelt virtuális gépeken megnyitott portok számát, valamint a nyitott portok részleteit. | Több virtuális gép |
| Sikertelen kapcsolatok | Megjeleníti a sikertelen kapcsolatok számát a figyelt virtuális gépeken, a meghibásodási trendet, és ha a hibák százalékos aránya növekszik az idő múlásával. | Több virtuális gép |
| Biztonság és naplózás | A TCP/IP-forgalom elemzése, amely a teljes kapcsolatokra, valamint a rosszindulatú kapcsolatokra mutat, ahol az IP-végpontok globálisan találhatók.  Az összes funkció engedélyezéséhez engedélyeznie kell a biztonsági észlelést. | Több virtuális gép |
| TCP-forgalom | Egy rangsorolt jelentés a megfigyelt virtuális gépekhez, valamint az elküldött, fogadott és teljes hálózati forgalomhoz egy rácsban, és egy trendvonalként jelenik meg. | Több virtuális gép |
| Adatforgalom-összehasonlítás | Ez a munkafüzet lehetővé teszi a hálózati forgalom trendjeinek összehasonlítását egyetlen gép vagy egy számítógépcsoport esetében. | Több virtuális gép |
| Teljesítmény | A teljesítmény nézet testreszabható verzióját nyújtja, amely az összes engedélyezett Log Analytics teljesítményszámlálókat kihasználja. | Egyetlen virtuális gép | 
| Kapcsolatok | A kapcsolatok részletes áttekintést nyújtanak a virtuális gépről érkező bejövő és kimenő kapcsolatokról. | Egyetlen virtuális gép |
 
## <a name="creating-a-new-workbook"></a>Új munkafüzet létrehozása

A munkafüzetek egymástól függetlenül szerkeszthető diagramokat, táblázatokat, szövegeket és beviteli vezérlőket tartalmazó részből állnak. A munkafüzetek jobb megismeréséhez nyisson meg egy sablont, és ismerkedjen meg az egyéni munkafüzetek létrehozásával. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Válassza a **Virtual Machines** lehetőséget.

3. Válasszon ki egy virtuális gépet a listából.

4. A virtuális gép lap **figyelés** **területén válassza az** eredmények elemet.

5. A virtuális gépek elemzése lapon válassza a **teljesítmény** vagy **Maps** fület, majd válassza a **munkafüzetek megtekintése** lehetőséget az oldalon található hivatkozásra kattintva. A legördülő listában válassza a **Ugrás a** katalógusba lehetőséget.

    ![Képernyőfelvétel a munkafüzet legördülő listájáról](media/vminsights-workbooks/workbook-dropdown-gallery-01.png)

    Ezzel elindítja a munkafüzet-katalógust számos előre elkészített munkafüzettel, amelyek segítenek az első lépések megtételében.

7. Hozzon létre egy új munkafüzetet az **új** lehetőség kiválasztásával.

    ![Képernyőfelvétel a munkafüzet-gyűjteményről](media/vminsights-workbooks/workbook-gallery-01.png)

## <a name="editing-workbook-sections"></a>Munkafüzetszakaszok szerkesztése

A munkafüzeteknek két módja van: **szerkesztési mód** és **olvasási mód**. Új munkafüzet első indításakor a **szerkesztési módban** nyílik meg. Megjeleníti a munkafüzet összes tartalmát, beleértve az egyébként rejtett lépéseket és paramétereket is. Az **olvasási mód** egy egyszerűsített jelentési stílus nézetet jelenít meg. Az olvasási mód lehetővé teszi, hogy absztrakt legyen a jelentés létrehozása során felmerülő összetettség, miközben az alapul szolgáló mechanika még csak néhány kattintással elérhető, ha a módosításra szükség van.

![Képernyőfelvétel: a Azure Monitor Virtual Machines munkafüzet szakasza, amely egy új munkafüzetet mutat be szerkesztési módban a szerkesztési vezérlők kiemelésével.](media/vminsights-workbooks/workbook-new-workbook-editor-01.png)

1. Ha elkészült egy szakasz szerkesztésével, kattintson a **kész szerkesztés** gombra a szakasz bal alsó sarkában.

2. Egy szakasz másodpéldányának létrehozásához kattintson a **szakasz klónozása** ikonra. Az ismétlődő lépések létrehozása nagyszerű módja annak, hogy a lekérdezéseket az előző iterációk elvesztése nélkül lehessen megismételni.

3. Ha feljebb szeretne helyezni egy szakaszt egy munkafüzetben, kattintson a **fel** vagy **a lejjebb ikonra** .

4. Egy szakasz végleges eltávolításához kattintson az **Eltávolítás** ikonra.

## <a name="adding-text-and-markdown-sections"></a>Szöveg és Markdown-szakaszok hozzáadása

A fejlécek, a magyarázatok és a kommentárok hozzáadása a munkafüzetekhez megkönnyíti a táblázatok és diagramok egy csoportba való bekapcsolását. A munkafüzetek szöveges szakaszai támogatják a [Markdown szintaxisát](https://daringfireball.net/projects/markdown/) , például a fejléceket, a félkövér, a dőlt és a listajeles listákat.

Ha szöveges szakaszt szeretne felvenni a munkafüzetbe, használja a munkafüzet alján található **szöveg hozzáadása** gombot, vagy a szakasz alján.

## <a name="adding-query-sections"></a>Lekérdezési szakaszok hozzáadása

![A munkafüzetek lekérdezési szakasza](media/vminsights-workbooks/005-workbook-query-section.png)

A lekérdezési szakasz a munkafüzethez való hozzáadásához használja a munkafüzet alján található **lekérdezés hozzáadása** gombot, vagy a szakasz alján.

A lekérdezési szakaszban nagyon rugalmasak, és a következő kérdésekre lehet válaszolni:

* Milyen időszakban használta a CPU-kihasználtságot a hálózati forgalom növekedése közben?
* Mi volt az elmúlt hónapban elérhető lemezterület trendje?
* Hány hálózati kapcsolattal kapcsolatos hiba történt a virtuális gépen az elmúlt két hétben? 

Nem csak a virtuális gép kontextusában, a munkafüzetből indított lekérdezésre korlátozódik. Több virtuális gépen is lekérdezheti, Log Analytics munkaterületeken, ha rendelkezik hozzáférési engedéllyel az erőforrásokhoz.

Adatok befoglalása más Log Analytics munkaterületekről vagy egy adott Application Insights alkalmazásból a **munkaterület** -azonosító használatával. Ha többet szeretne megtudni az erőforrások közötti lekérdezésekről, tekintse meg a [hivatalos útmutatást](../logs/cross-workspace-query.md).

### <a name="advanced-analytic-query-settings"></a>Speciális analitikus lekérdezési beállítások

Mindegyik szakasz saját speciális beállításokat tartalmaz, amelyek a ![ ](media/vminsights-workbooks/006-settings.png) **Paraméterek hozzáadása** gomb jobb oldalán található beállítások munkafüzetek szakasz szerkesztési vezérlők ikonján keresztül érhetők el.

![Képernyőkép a Azure Monitor Virtual Machines munkafüzet szakaszának speciális beállítások párbeszédpaneléről. A párbeszédpanelt megnyitó ikon ki van emelve.](media/vminsights-workbooks/007-settings-expanded.png)

|         |          |
| ---------------- |:-----|
| **Egyéni szélesség**    | Az elem tetszőleges méretűvé válik, így több elem is elfér egyetlen sorban, így jobban rendszerezheti a diagramokat és a táblákat gazdag interaktív jelentésekben.  |
| **Feltételesen látható** | Itt adhatja meg, hogy az olvasási módban megadott paraméterek alapján elrejtse a lépéseket. |
| **Paraméter exportálása**| A rács vagy a diagram kiválasztott sora számára lehetővé teheti, hogy későbbi lépéseket okozzon az értékek módosításához vagy láthatóvá váljon.  |
| **Lekérdezés megjelenítése nem szerkesztéskor** | Megjeleníti a diagram vagy a tábla feletti lekérdezést még olvasási módban is.
| **Megnyitás az Analyticsben gomb megjelenítése nem szerkesztéskor** | Hozzáadja a kék elemzési ikont a diagram jobb oldali sarkához az egykattintásos hozzáférés engedélyezéséhez.|

Ezeknek a beállításoknak a nagy része meglehetősen intuitív, de a **Paraméterek exportálásának** megismeréséhez érdemes megvizsgálni egy olyan munkafüzetet, amely használja ezt a funkciót.

Az egyik előre elkészített munkafüzet – **TCP-forgalom**, amely a virtuális gép kapcsolatainak metrikáit ismerteti.

A munkafüzet első szakasza a naplózási lekérdezési adatmennyiségen alapul. A második szakasz a naplózási lekérdezési adatmennyiségen alapul, de az első táblázatban szereplő sorok interaktív módon frissülnek a diagramok tartalmával:

![Képernyőkép a Azure Monitor Virtual Machines szakaszáról, amely az előre elkészített munkafüzet TCP-forgalmát mutatja.](media/vminsights-workbooks/008-workbook-tcp-traffic.png)

A viselkedés az **elem kiválasztásakor lehetséges, a paraméterek** speciális beállításainak exportálása, amelyek engedélyezve vannak a tábla naplójának lekérdezésében.

![Képernyőkép a Virtual Machines-munkafüzet speciális beállítások párbeszédpaneléről, a "Ha egy elem van kiválasztva, a paraméter exportálása" lehetőség be van jelölve.](media/vminsights-workbooks/009-settings-export.png)

A második naplózási lekérdezés ezt követően kijelöli az exportált értékeket, amikor egy sor kiválasztásával létrehoz egy olyan értéket, amelyet a szakasz fejléce és diagramjai használnak. Ha nincs kiválasztva sor, elrejti a szakasz fejlécét és a diagramokat. 

A második szakaszban található rejtett paraméter például a következő hivatkozást használja a rácsban kiválasztott sorból:

```
VMConnection
| where TimeGenerated {TimeRange}
| where Computer in ("{ComputerName}") or '*' in ("{ComputerName}") 
| summarize Sent = sum(BytesSent), Received = sum(BytesReceived) by bin(TimeGenerated, {TimeRange:grain})
```

## <a name="adding-metrics-sections"></a>Metrikaszakaszok hozzáadása

A metrikák részben teljes hozzáférést biztosítanak a Azure Monitor metrikák adatait az interaktív jelentésekhez. A VM-elemzésekben az előre elkészített munkafüzetek általában analitikus lekérdezési adatokat tartalmaznak metrikus adatok helyett.  Dönthet úgy, hogy metrikus adatokkal rendelkező munkafüzeteket hoz létre, így teljes mértékben kihasználhatja mindkét funkció előnyeit egy helyen. Lehetősége van arra is, hogy az erőforrásokból származó metrikus adatokat bármely olyan előfizetésből lehívhatja, amelyhez hozzáfér.

Itt látható egy példa arra, hogy a virtuális gép adatai egy munkafüzetbe kerüljenek, és a CPU-teljesítmény rácsos megjelenítését biztosítják:

![Képernyőfelvétel a Azure Monitor található virtuálisgép-munkafüzetek metrikák szakaszáról. Az egyes virtuális gépek CPU-teljesítménye grafikusan látható.](media/vminsights-workbooks/010-metrics-grid.png)

## <a name="adding-parameter-sections"></a>Paraméterszakaszok hozzáadása

A munkafüzet-paraméterek lehetővé teszik az értékek módosítását a munkafüzetben anélkül, hogy manuálisan kellene szerkeszteni a lekérdezési vagy szöveges szakaszt. Ez megszünteti az alapul szolgáló analitikai lekérdezési nyelv megismerésének követelményét, és nagy mértékben kiterjeszti a munkafüzet-alapú jelentéskészítés lehetséges célközönségét.

A paraméterek értékei a lekérdezés, a szöveg vagy más paraméterek részekben vannak lecserélve, ha a paraméter nevét kapcsos zárójelben adja meg, például: ``{parameterName}`` . A paraméterek nevei a hasonló szabályokra korlátozódnak JavaScript-azonosítóként, alfabetikus karakterként vagy aláhúzásként, amelyet alfanumerikus karakterek vagy aláhúzások követnek. Az **a1** például engedélyezve van, de az **1a** nem engedélyezett.

A paraméterek lineárisak, a munkafüzetek elejétől kezdve, és a későbbi lépésekig áramlanak.  A munkafüzet későbbi részében deklarált paraméterek felülbírálják a korábban bejelentett paramétereket. Ez azt is lehetővé teszi, hogy a lekérdezéseket használó paraméterek hozzáférjenek a korábban definiált paraméterek értékeihez. A paraméter lépésein belül a paraméterek is lineárisak, balról jobbra, ahol a jobb oldali paraméterek az adott lépésben korábban bejelentett paraméterektől függően változhatnak.
 
Négy különböző típusú paraméter létezik, amelyek jelenleg támogatottak:

|                  |      |
| ---------------- |:-----|
| **Szöveg**    | Lehetővé teszi a felhasználó számára a szövegmező szerkesztését, és opcionálisan megadhat egy lekérdezést az alapértelmezett érték kitöltéséhez. |
| **Legördülő lista** | Lehetővé teszi a felhasználó számára az értékek egy halmazának kiválasztását. |
| **Időtartomány-választó**| Lehetővé teszi a felhasználó számára, hogy kiválassza az időtartomány-értékek előre meghatározott készletét, vagy válasszon egy egyéni időtartományból.|
| **Erőforrás-választó** | Lehetővé teszi a felhasználó számára a munkafüzethez kiválasztott erőforrások kiválasztását.|

### <a name="using-a-text-parameter"></a>Szöveges paraméter használata

A szövegmezőben a felhasználói típusok értékét a rendszer közvetlenül a lekérdezésben cseréli le, és nem jár el. Ha a szükséges érték egy karakterlánc, a lekérdezésnek idézőjelekkel kell rendelkeznie a paraméter körül (például **"{paraméter}"**).

A Text paraméter lehetővé teszi, hogy a szövegmezőben lévő értéket bárhol lehessen használni. Ez lehet egy tábla neve, egy oszlop neve, egy függvény neve, operátor stb.  A Text paraméter típusa beállítás az **alapértelmezett érték beolvasása az elemzési lekérdezésből**, amely lehetővé teszi, hogy a munkafüzet szerzője lekérdezés használatával töltse fel az adott szövegmező alapértelmezett értékét.

Amikor az alapértelmezett értéket használja egy napló lekérdezésből, a rendszer csak az első sor első értékét (0. sor, 0. oszlop) használja alapértelmezett értékként. Ezért javasolt a lekérdezés korlátozása, hogy csak egy sort és egy oszlopot adjanak vissza. A rendszer figyelmen kívül hagyja a lekérdezés által visszaadott összes egyéb adatkérést. 

A lekérdezés által visszaadott értéket a rendszer közvetlenül a szökés vagy az idézés nélkül váltja fel. Ha a lekérdezés nem ad vissza sorokat, a paraméter eredménye vagy üres karakterlánc (ha a paraméter nem szükséges) vagy nincs megadva (ha a paraméter megadása kötelező).

### <a name="using-a-drop-down"></a>Legördülő lista használata

A legördülő paraméter típusa lehetővé teszi egy legördülő lista létrehozását, amely lehetővé teszi egy vagy több érték kijelölését.

A legördülő lista egy log lekérdezéssel vagy JSON-vel van feltöltve. Ha a lekérdezés egy oszlopot ad vissza, az abban az oszlopban szereplő értékek mind az érték, mind a felirat szerepel a legördülő menüben. Ha a lekérdezés két oszlopot ad vissza, akkor az első oszlop az érték, a második oszlop pedig a legördülő menüben látható felirat. Ha a lekérdezés három oszlopot ad vissza, a harmadik oszlop a legördülő lista alapértelmezett kijelölését jelzi. Ez az oszlop bármilyen típusú lehet, de a legegyszerűbb a logikai vagy numerikus típusok használata, ahol a 0 hamis, és 1 igaz.

Ha az oszlop karakterlánc típusú, a null/üres karakterlánc hamisnak minősül, és minden más érték igaznak minősül. Az egyszeres kijelölés legördülő menüben a true értékkel rendelkező első érték lesz az alapértelmezett kijelölés.  Több kijelölés legördülő lista esetén az összes igaz értékkel rendelkező érték lesz az alapértelmezett kijelölt készlet. A legördülő listában szereplő elemek bármilyen sorrendben jelennek meg, mint a lekérdezés által visszaadott sorok. 

Tekintsük át a kapcsolatok áttekintése jelentésben szereplő paramétereket. Kattintson a Szerkesztés szimbólumra az **irány** mellett.

![A jelentés paramétereinek hozzáadására és szerkesztésére szolgáló szakasz képernyőképe Azure Monitor. A Direction paraméterhez tartozó szerkesztési ikon van kiválasztva.](media/vminsights-workbooks/011-workbook-using-dropdown.png)

Ekkor elindul a **paraméter szerkesztése** menüelem.

![Képernyőkép a paraméter szerkesztése párbeszédpanelről. A paraméter neve irány, a paraméter típusa legördülő lista, és a JSON-adatok beolvasása beállítás van kiválasztva.](media/vminsights-workbooks/012-workbook-edit-parameter.png)

A JSON lehetővé teszi, hogy létrehoz egy tetszőleges táblát, amely tartalmakkal van feltöltve. A következő JSON például két értéket hoz létre a legördülő menüben:

```
[
    { "value": "inbound", "label": "Inbound"},
    { "value": "outbound", "label": "Outbound"}
]
```

A további alkalmazható példa a legördülő lista használata a teljesítményszámlálók alapján a következő névvel:

```
Perf
| summarize by CounterName, ObjectName
| order by ObjectName asc, CounterName asc
| project Counter = pack('counter', CounterName, 'object', ObjectName), CounterName, group = ObjectName
```

A lekérdezés a következőképpen jeleníti meg az eredményeket:

![Teljesítményszámláló legördülő lista](media/vminsights-workbooks/013-workbook-edit-parameter-perf-counters.png)

A legördülő lista hihetetlenül hatékony eszközöket biztosít az interaktív jelentések testreszabásához és létrehozásához.

### <a name="time-range-parameters"></a>Időintervallum-paraméterek

Míg a legördülő menüből a saját egyéni időtartomány-paramétert is létrehozhatja, a beépített időtartomány-paraméter típusát is használhatja, ha nem kell ugyanolyan fokú rugalmasságot biztosítania. 

Az időtartomány-paraméterek típusának 15 alapértelmezett tartománya van, amelyek öt perctől az utolsó 90 napra mutatnak. Lehetőség van az egyéni időtartomány kiválasztására is, amely lehetővé teszi, hogy a jelentés kezelője explicit indítási és leállítási értékeket válasszon az időtartományhoz.

### <a name="resource-picker"></a>Erőforrás-választó

Az erőforrás-választó paraméterének típusa lehetővé teszi, hogy a jelentést bizonyos típusú erőforrásokra szűkítse. Példa egy előre elkészített munkafüzetre, amely kihasználja az erőforrás-választó típusát a **teljesítmény** munkafüzetben.

![Munkaterületek legördülő lista](media/vminsights-workbooks/014-workbook-edit-parameter-workspaces.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Munkafüzetek mentése és megosztása a csapattal

A munkafüzetek egy Log Analytics munkaterületen vagy egy virtuálisgép-erőforráson belül lesznek mentve attól függően, hogy hogyan fér hozzá a munkafüzetek katalógusához. A munkafüzet menthető az Ön számára magánjellegű **saját jelentések** szakaszba, vagy a **megosztott jelentések** szakaszban, amely mindenki számára elérhető az erőforráshoz való hozzáféréssel. Az erőforrás összes munkafüzetének megtekintéséhez kattintson a művelet sávban a **Megnyitás** gombra.

A jelenleg **saját jelentésekban** lévő munkafüzetek megosztása:

1. Kattintson a **Megnyitás** gombra a műveleti sávon
2. Kattintson a "..." a megosztani kívánt munkafüzet melletti gomb
3. Kattintson **a áthelyezés megosztott jelentésekre** elemre.

Egy hivatkozással vagy e-mailen keresztüli munkafüzet megosztásához kattintson a művelet sávban a **megosztás** elemre. Ne feledje, hogy a hivatkozás címzettjeinek hozzáféréssel kell rendelkezniük ehhez az erőforráshoz a Azure Portal a munkafüzet megtekintéséhez. A módosítások elvégzéséhez a címzetteknek legalább közreműködői engedélyekkel kell rendelkezniük az erőforráshoz.

Egy munkafüzetre mutató hivatkozás rögzítése egy Azure-irányítópulton:

1. Kattintson a **Megnyitás** gombra a műveleti sávon
2. Kattintson a "..." a rögzíteni kívánt munkafüzet melletti gomb
3. Kattintson **a rögzítés az irányítópulton** elemre.

## <a name="next-steps"></a>Következő lépések

- A korlátozások és a virtuális gépek teljes teljesítményének azonosításához lásd: az Azure-beli [virtuális gépek teljesítményének megtekintése](vminsights-performance.md).

- A felderített alkalmazások függőségeivel kapcsolatos további információkért lásd: virtuálisgép- [észlelési Térkép megtekintése](vminsights-maps.md).
