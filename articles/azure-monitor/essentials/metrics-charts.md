---
title: Az Azure Metrics Explorer speciális funkciói
description: Ismerje meg az Azure Metrics Explorer speciális felhasználási feladatait.
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: vitalyg
ms.openlocfilehash: d728dfb364cb0f82326a472196cb28d79b85b1e9
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102031483"
---
# <a name="advanced-features-of-the-azure-metrics-explorer"></a>Az Azure Metrics Explorer speciális funkciói

> [!NOTE]
> Ez a cikk azt feltételezi, hogy már ismeri a Azure Monitor Azure Metrics Explorer szolgáltatásának alapvető funkcióit. Ha Ön új felhasználó, és szeretné megismerni, hogyan hozhatja létre első mérőszámát, tekintse meg a következő témakört: első [lépések a metrikák Explorerrel](./metrics-getting-started.md).

Azure Monitor a [metrikák](data-platform-metrics.md) a mért értékek, valamint a begyűjtött és az idő múlásával tárolt darabszámok sorozata. A metrikák lehetnek standard (más néven "platform") vagy egyéni. 

Az Azure platform standard mérőszámokat biztosít. Az Azure-erőforrások állapot-és használati statisztikáit tükrözik. 

## <a name="resource-scope-picker"></a>Erőforrás hatókör-választója
Az erőforrás-hatókör választó lehetővé teszi, hogy az egyes erőforrások és több erőforrás metrikáit megtekinthesse. A következő szakasz ismerteti, hogyan használható az erőforrás hatókör-választója. 

### <a name="select-a-single-resource"></a>Egyetlen erőforrás kiválasztása
Válassza a **metrikák** lehetőséget a **Azure monitor** menüből vagy az erőforrás menüjének **figyelés** szakaszában. Ezután a hatókör **kiválasztása elemre** kattintva nyissa meg a hatókör-választót. 

A hatókör-választó használatával válassza ki azokat az erőforrásokat, amelyek mérőszámait meg szeretné jeleníteni. Ha az Azure metrika-kezelőt egy erőforrás menüjéből nyitotta meg, akkor a hatókört fel kell tölteni. 

![Az erőforrás hatókör-választójának megnyitását bemutató képernyőkép.](./media/metrics-charts/scope-picker.png)

Egyes erőforrások esetében egyszerre csak egy erőforrás metrikáját lehet megtekinteni. Az **erőforrástípusok** menüben ezek az erőforrások a **minden erőforrástípus** szakaszban találhatók.

![Egyetlen erőforrást ábrázoló képernyőfelvétel](./media/metrics-charts/single-resource-scope.png)

Az erőforrás kiválasztása után megjelenik az adott erőforrást tartalmazó összes előfizetés és erőforráscsoport.

![A rendelkezésre álló erőforrásokat bemutató képernyőkép.](./media/metrics-charts/available-single-resource.png)

> [!TIP]
> Ha azt szeretné, hogy a szolgáltatás egyszerre több erőforrás metrikáit tekintse meg, vagy az előfizetésben vagy az erőforráscsoport mérőszámait is megtekintheti, válassza a **felszavazás** lehetőséget.

Ha elégedett a kijelöléssel, válassza az **alkalmaz** lehetőséget.

### <a name="view-metrics-across-multiple-resources"></a>Metrikák megtekintése több erőforrás között
Néhány erőforrástípus több erőforráson is lekérdezheti a metrikákat. Az erőforrásoknak ugyanahhoz az előfizetéshez és helyhez kell esniük. **Az erőforrástípusok menü felső** részén keresse meg ezeket az erőforrásokat. 

További információ: [több erőforrás kiválasztása](./metrics-dynamic-scope.md#select-multiple-resources).

![Képernyőkép a több erőforrást ábrázoló típusokról.](./media/metrics-charts/multi-resource-scope.png)

Több erőforrással kompatibilis típusok esetén lekérdezheti a metrikákat egy előfizetésben vagy több erőforráscsoport között. További információ: [válasszon erőforráscsoportot vagy előfizetést](./metrics-dynamic-scope.md#select-a-resource-group-or-subscription).

## <a name="multiple-metric-lines-and-charts"></a>Több metrikai vonal és diagram

Az Azure mérőszámok Explorerben olyan diagramokat hozhat létre, amelyek több mérőszámot ábrázolnak, vagy egyszerre több metrikai diagramot is megjeleníthetnek. Ez a funkció lehetővé teszi a következőket:

- Az egyazon gráfon található kapcsolódó metrikák összekapcsolásával megtekintheti, hogy egy érték hogyan kapcsolódik egy másikhoz.
- A különböző mértékegységeket használó mérőszámok megjelenítése a közeli közelségben.
- Vizuálisan összesítheti és összehasonlíthatja több erőforrás mérőszámait.

Tegyük fel például, hogy öt Storage-fiókkal rendelkezik, és szeretné tudni, hogy mennyi tárhelyet használnak együtt. Létrehozhat egy (halmozott) diagramot, amely megjeleníti az egyes értékeket, valamint az adott időpontokban lévő értékek összegét.

### <a name="multiple-metrics-on-the-same-chart"></a>Több metrika ugyanazon a diagramon

Ha több metrikát szeretne megtekinteni ugyanazon a diagramon, először [hozzon létre egy új diagramot](./metrics-getting-started.md#create-your-first-metric-chart). Ezután válassza a **metrika hozzáadása** elemet. Ismételje meg ezt a lépést egy másik metrika egyazon diagramon való hozzáadásához.

> [!NOTE]
> A diagramok általában nem keverik a különböző mértékegységeket használó mérőszámokat. Tegyük fel például, hogy nem keveri az egyik mérőszámot, amely ezredmásodperceket használ egy másikhoz. Ne keverje az olyan metrikákat, amelyek méretezése jelentősen eltér. 
>
> Ezekben az esetekben érdemes inkább több diagramot használni. A mérőszámok Explorerben válassza a **diagram hozzáadása** lehetőséget új diagram létrehozásához.

### <a name="multiple-charts"></a>Több diagram

Ha más metrikát használó diagramot szeretne létrehozni, válassza a **diagram hozzáadása** lehetőséget.

Több diagram átrendezéséhez vagy törléséhez kattintson a három pont (**..**.) gombra a diagram menü megnyitásához. Ezután válassza a **feljebb**, **a lejjebb vagy** a **Törlés** lehetőséget.

## <a name="aggregation"></a>Összesítés

Ha mérőszámot ad hozzá egy diagramhoz, a metrikák kezelője automatikusan egy alapértelmezett összesítést alkalmaz. Az alapértelmezett érték az alapszintű forgatókönyvekben van. Más összesítéssel azonban további információkhoz juthat a metrikáról. 

Mielőtt eltérő összesítéseket használ egy diagramon, tisztában kell lennie azzal, hogy a metrikák kezelője hogyan kezeli őket. A metrikák egy adott időszakban rögzített mérési (vagy "metrikus értékek") sorozatok. Amikor Kirajzol egy diagramot, a kiválasztott metrika értékei külön lesznek összesítve az *időgabona* során. 

Kiválaszthatja az időgabona méretét a metrikai tallózó [időválasztó paneljének](./metrics-getting-started.md#select-a-time-range)használatával. Ha nem választja ki explicit módon az időkeretet, a rendszer alapértelmezés szerint a jelenleg kijelölt időtartományt használja. Az időtartam megállapítása után az egyes időkeretek során rögzített metrikai értékeket összesíti a diagramon, egy adatpontot és egy időszeletet.

Tegyük fel például, hogy egy diagram a *kiszolgáló válaszideje* mérőszámot jeleníti meg. Az *átlagos* összesítést használja az *elmúlt 24 órában*. Ebben a példában:

- Ha az idő részletessége 30 percre van beállítva, a diagram 48 összesített adatpontból készül. Ez azt eredményezi, hogy a vonalas diagram 48 pontot csatlakoztat a diagram ábrázolási területéhez (24 óra x 2 adatpont/óra). Az egyes adatpontok az összes rögzített válaszidő *átlagát* jelentik a kiszolgálói kérelmekre vonatkozóan, amelyek az egyes kapcsolódó 30 perces időszakok során fordultak elő.
- Ha 15 percre vált az időrészletesség, 96 összesített adatpontot kap.  Ez azt jelentik, hogy óránként 24 óra x 4 adatpontot kap.

A metrikák Explorer öt alapvető statisztikai összesítési típust tartalmaz: Sum, Count, min, Max és Average. Az *összeg* összesítését néha a *teljes* összesítésnek is nevezik. Számos metrika esetében a metrikák kezelője elrejti a nem releváns és nem használható összesítéseket.

* **Sum**: az összesítési intervallumban rögzített összes érték összege.

    ![Képernyőkép a Sum kérelemről.](./media/metrics-charts/request-sum.png)

* **Darabszám**: az összesítési intervallumban rögzített mértékegységek száma. 
    
    Ha a metrika mindig 1 értékkel van rögzítve, a Count összesítés egyenlő a Sum összesítéssel. Ez a forgatókönyv gyakori, ha a metrika nyomon követi a különböző események számát, és az egyes mérések egy eseményt jelölnek. A kód egy metrikai rekordot bocsát ki minden alkalommal, amikor új kérelem érkezik.

    ![Képernyőkép a Count kérelemről.](./media/metrics-charts/request-count.png)

* **Átlag**: az összesítési intervallumban rögzített metrikai értékek átlaga.

    ![Képernyőkép egy átlagos kérelemről.](./media/metrics-charts/request-avg.png)

* **Min**: az összesítési intervallumban rögzített legkisebb érték.

    ![A minimális kérés képernyőképe.](./media/metrics-charts/request-min.png)

* **Max**: az összesítési intervallumban rögzített legnagyobb érték.

    ![Képernyőkép a maximális kérelemről.](./media/metrics-charts/request-max.png)

## <a name="filters"></a>Szűrők

Szűrőket alkalmazhat olyan diagramokra, amelyek mérőszámai rendelkeznek dimenziókkal. Tegyük fel például, hogy a "tranzakció száma" metrika "Response type" (válasz típusa) dimenzióval rendelkezik. Ez a dimenzió azt jelzi, hogy a tranzakciók válasza sikeres vagy sikertelen volt-e. Ha ezt a dimenziót szűri, akkor csak a sikeres (vagy csak sikertelen) tranzakciókat tartalmazó diagram jelenik meg. 

### <a name="add-a-filter"></a>Szűrők hozzáadása

1. A diagram felett válassza a **szűrő hozzáadása** elemet.

2. Válassza ki a szűrni kívánt dimenziót (tulajdonságot).

   ![A szűrni kívánt dimenziókat (tulajdonságokat) megjelenítő képernyőkép.](./media/metrics-charts/028.png)

3. Válassza ki a dimenzióra alkalmazni kívánt operátort (tulajdonság). Az alapértelmezett operátor = (egyenlő)

   ![A szűrővel használható operátort bemutató képernyőkép.](./media/metrics-charts/filter-operator.png)

4. Válassza ki, hogy mely dimenzió értékeket kívánja alkalmazni a szűrőre a diagram ábrázolásakor (ez a példa a sikeres tárolási tranzakciók kiszűrését mutatja):

   ![A sikeres szűrt tárolási tranzakciókat bemutató képernyőkép.](./media/metrics-charts/029.png)

5. Miután kiválasztotta a szűrő értékeit, kattintson a szűrési választóból a bezáráshoz. A diagramon most már látható, hogy hány tárolási tranzakciót sikerült végrehajtani:

   ![Képernyőkép, amely azt mutatja, hogy hány tárolási tranzakció meghiúsult.](./media/metrics-charts/030.png)

6. Az 1-5-es lépések megismétlésével több szűrőt is alkalmazhat ugyanarra a diagramra.


## <a name="metric-splitting"></a>Metrika felosztása

A mérőszámok dimenzió alapján való felosztásával megjelenítheti a metrika különböző szakaszainak összehasonlítását. A felosztással a dimenzió kihelyezhető szegmensei is azonosíthatók.

### <a name="apply-splitting"></a>Felosztás alkalmazása

1. A diagram felett válassza a **felosztás alkalmazása** lehetőséget.
 
   > [!NOTE]
   > A több metrikával rendelkező diagramok nem használhatják a felosztási funkciót. Bár a diagram több szűrővel is rendelkezhet, csak egy felosztási dimenzióval rendelkezhet.

2. Válassza ki azt a dimenziót, amelyre a diagramot szeretné szegmentálni:

   ![Képernyőkép, amely a diagram szegmentálására kijelölt dimenziót jeleníti meg.](./media/metrics-charts/031.png)

   A diagram most több sort mutat, egyet az egyes dimenziók szegmenséhez:

   ![Képernyőkép, amely több sort mutat be, egyet a dimenzió minden egyes szegmenséhez.](./media/metrics-charts/segment-dimension.png)
   
3. Válasszon egy korlátot a kiválasztott dimenzió szerinti felosztás után megjelenítendő értékek számának megjelöléséhez. Az alapértelmezett korlát 10, ahogy az a fenti ábrán látható. A korlát tartománya 1-50.
   
   ![A felosztási korlátot megjelenítő képernyőkép, amely a felosztás után korlátozza az értékek számát.](./media/metrics-charts/segment-dimension-limit.png)
   
4. Válassza ki a rendezési sorrendet a szegmensek: növekvő vagy csökkenő sorrendben. Az alapértelmezett kijelölés csökkenő.
   
   ![Képernyőkép, amely a rendezési sorrendet jeleníti meg a felosztott értékeken.](./media/metrics-charts/segment-dimension-sort.png)

5. Kattintson a **csoportosítási választóból** a bezáráshoz.
   

   > [!NOTE]
   > A forgatókönyv szempontjából lényegtelen szegmensek elrejtéséhez, valamint a diagramok könnyebb olvashatóságának elvégzéséhez használja a szűrést és a felosztást ugyanazon a dimenzión.

## <a name="locking-the-range-of-the-y-axis"></a>Az y tengely tartományának zárolása

Az érték (y) tengelyének zárolása a nagy értékek kis ingadozásait bemutató diagramok esetében fontos. 

Például a 99,99 százalékról 99,5 százalékra történő sikeres kérések mennyiségének csökkenése a szolgáltatás minőségének jelentős csökkenését jelenthetheti. Egy kis numerikus érték ingadozása azonban nehéz vagy akár lehetetlen is lehet, ha az alapértelmezett diagram beállításait használja. Ebben az esetben a diagram legalacsonyabb határát 99 százalékra lehet zárolni, hogy a kis mennyiségű csökkenés nyilvánvalóvá válik. 

Egy másik példa a rendelkezésre álló memória ingadozása. Ebben az esetben az érték műszakilag soha nem éri el a 0 értéket. A tartomány magasabb értékre való kijavítása megkönnyíti a rendelkezésre álló memória kitételét. 

Az y tengely tartományának szabályozásához nyissa meg a diagram menüt (**..**.). Ezután válassza a **Diagram beállításai** lehetőséget a speciális diagram beállításainak eléréséhez.

![Képernyőkép a diagram beállításainak kiválasztásáról.](./media/metrics-charts/033.png)

Módosítsa az **Y tengely tartománya** szakaszban szereplő értékeket, vagy válassza az **automatikus** lehetőséget az alapértelmezett értékre való visszalépéshez.
 
 ![Képernyőkép, amely kiemeli az Y tengely Range szakaszt.](./media/metrics-charts/034.png)

> [!WARNING]
> Ha le kell zárnia az y tengely határait olyan diagramoknál, amelyek nyomon követik a darabszámot vagy az összegeket egy adott időtartamon belül (darabszám, Sum, min vagy Max összesítés használatával), akkor általában meg kell adnia a rögzített idő részletességét. Ebben az esetben ne használja az automatikus alapértékeket. 
>
> Kiválaszthat egy rögzített idejű részletességet, mert a diagram értékei változnak, ha az időrészletesség automatikusan módosul, miután a felhasználó átméretezi a böngészőablakot, vagy megváltoztatja a képernyőfelbontást. Az időbeli részletesség eredményeként létrejövő változás befolyásolja a diagram megjelenését, és érvényteleníti az y tengely tartományának aktuális kijelölését.

## <a name="line-colors"></a>Vonal színei

A diagramok konfigurálása után a rendszer automatikusan egy alapértelmezett paletta színeit rendeli hozzá a diagram soraihoz. Módosíthatja ezeket a színeket.

Egy diagramterület színének módosításához válassza ki a diagramhoz tartozó jelmagyarázatban található színes sávot. Megnyílik a színválasztó párbeszédpanel. A színválasztó segítségével konfigurálja a vonal színét.

![Képernyőkép, amely bemutatja a szín módosításának módját.](./media/metrics-charts/035.png)

A testreszabott színeket a diagram irányítópulton való rögzítésekor megőrzi a rendszer. A következő szakasz bemutatja, hogyan rögzítheti a diagramokat.

## <a name="pinning-to-dashboards"></a>Rögzítés az irányítópultokon 

A diagram konfigurálását követően érdemes lehet hozzáadni egy irányítópulthoz. A diagramokat egy irányítópultra rögzítve elérhetővé teheti a csapat számára. Az elemzéseket más figyelési telemetria kontextusában is megtekintheti.

Egy konfigurált diagram irányítópulton való rögzítéséhez a diagram jobb felső sarkában válassza a **rögzítés az irányítópulton** lehetőséget.

![A diagram irányítópulton való rögzítését bemutató képernyőkép.](./media/metrics-charts/036.png)

## <a name="alert-rules"></a>Riasztási szabályok

A vizualizációs feltételek használatával mérőszám-alapú riasztási szabályt hozhat létre. Az új riasztási szabály tartalmazni fogja a diagram cél erőforrását, metrikáját, felosztását és szűrési dimenzióit. Ezeket a beállításokat a riasztási szabály létrehozása panel használatával módosíthatja.

A kezdéshez válassza az **új riasztási szabály** lehetőséget.

![Képernyőkép, amely az új riasztási szabály piros színnel jelölt gombját mutatja.](./media/metrics-charts/042.png)

Megnyílik a riasztási szabály létrehozási panelje. A panelen megjelenik a diagram metrikájának méretei. A panelen lévő mezők előre vannak feltöltve a szabály testreszabásához.

![A szabály létrehozási ablaktábláját ábrázoló képernyőfelvétel.](./media/metrics-charts/041.png)

További információ: [metrikai riasztások létrehozása, megtekintése és kezelése](../alerts/alerts-metric.md).

## <a name="correlate-metrics-to-logs"></a>Metrikák korrelációja a naplókhoz
Annak érdekében, hogy az ügyfél a metrikák diagramján észlelje a rendellenességek kiváltó okát, a naplókba való részletezést hoztunk létre. A naplók részletezése lehetővé teszi az ügyfelek számára, hogy a metrikák diagramon a naplókhoz és lekérdezésekhez kapcsolódnak. 

Mielőtt belemerülünk a felhasználói élménybe, először be szeretnénk írni a különböző típusú naplókat és lekérdezéseket. 

| Időszak             | Definíció  | 
|------------------|-------------|
| Tevékenységnaplók    | Betekintést nyújt a műveletekre az előfizetésben lévő egyes Azure-erőforrásokon kívülről (a felügyeleti síkon), valamint a Service Health események frissítései mellett. A tevékenység naplójának használatával meghatározhatja a mi, ki és mikor az előfizetésben lévő erőforrásokra vonatkozó írási műveletek (PUT, POST, DELETE) esetében. Minden egyes Azure-előfizetéshez egyetlen tevékenységi napló van.  |   
| Diagnosztikai napló   | Betekintést nyerhet az Azure-erőforrásokon (az adatsíkon) végrehajtott műveletekre, például a titkos kulcs beszerzése egy Key Vault vagy egy adatbázisra vonatkozó kérelem elkészítése. Az erőforrás-naplók tartalma az Azure-szolgáltatás és az erőforrás típusa szerint változik. **Megjegyzés:** A szolgáltatást a szolgáltatásnak kell megadnia, és az ügyfélnek kell engedélyeznie  | 
| Ajánlott napló | Forgatókönyv-alapú lekérdezések, amelyekkel az ügyfél kihasználhatja a metrikák a mérőszámok Explorerben való vizsgálatát.  |

Jelenleg a naplók részletezése elérhető az erőforrás-szolgáltatók kiválasztásához. A naplók felhasználói felületének teljes részletezését biztosító erőforrás-szolgáltatók a következők: 

* Application Insights 
* Automatikus méretezés 
* App Services  
* Tárolás  

Alább látható egy példa a Application Insights erőforrás-szolgáltatóra.

![Az App bepillantások metrikáinak paneljén észlelt hibák](./media/metrics-charts/drill-into-log-ai.png)

A sikertelen kérelmekben lévő tüske diagnosztizálásához kattintson a "részletezés naplókba" elemre.

![Képernyőkép a naplók legördülő menüjéről](./media/metrics-charts/drill-into-logs-dropdown.png)

Ha a hiba lehetőségre kattint, a rendszer egy egyéni meghibásodási panelt fog eredményezni, amely a sikertelen műveleti műveleteket, a leggyakoribb kivételeket és a függőségeket biztosítja. 

![Képernyőkép az App bepillantást nem okozó panelről](./media/metrics-charts/ai-failure-blade.png)

### <a name="common-problems-with-drill-into-logs"></a>Gyakori problémák a naplók részletezésével

* A napló és a lekérdezések le vannak tiltva – az ajánlott naplók és lekérdezések megtekintéséhez át kell irányítani a diagnosztikai naplókat a Log Analytics. Ebből a [dokumentumból](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings) megtudhatja, hogyan teheti ezt meg. 
* Csak a tevékenységi naplók vannak megadva – a naplózási funkció csak az erőforrás-szolgáltatók kiválasztása esetén érhető el. Alapértelmezés szerint a rendszer a tevékenységek naplóit is megadja. 

 
## <a name="troubleshooting"></a>Hibaelhárítás

Ha a diagramon nem jelennek meg adatok, tekintse át a következő hibaelhárítási információkat:

* A szűrők a panel összes diagramján érvényesek. Egy diagramra összpontosítva ügyeljen arra, hogy ne állítson be olyan szűrőt, amely kizár egy másik diagramon lévő összes adattípust.

* Ha különböző szűrőket szeretne beállítani különböző diagramokon, hozza létre a diagramokat különböző lapokon. Ezután mentse a diagramokat külön kedvencekként. Ha szeretné, rögzítheti a diagramokat az irányítópulton, így együtt láthatja őket.

* Ha a diagramot olyan tulajdonság szerint szegmentálja, amelyet a metrika nem definiál, a diagram nem jelenít meg tartalmat. Próbálja meg törölni a szegmentálást (felosztás), vagy válasszon másik tulajdonságot.

## <a name="next-steps"></a>Következő lépések

A használható irányítópultok metrikák használatával történő létrehozásával kapcsolatban lásd: [Egyéni KPI-irányítópultok létrehozása](../app/tutorial-app-dashboards.md).

