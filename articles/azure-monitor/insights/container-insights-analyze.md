---
title: Kubernetes-figyelés a Azure Monitor for containers szolgáltatással | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan tekintheti meg és elemezheti a Kubernetes-fürtök teljesítményét a tárolók Azure Monitorával.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: f57f8982b2aa045156e6f48316610137260d6597
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2020
ms.locfileid: "75731016"
---
# <a name="monitor-your-kubernetes-cluster-performance-with-azure-monitor-for-containers"></a>A Kubernetes-fürt teljesítményének figyelése a Azure Monitor for containers szolgáltatással

A tárolók Azure Monitor a teljesítmény-diagramok és az állapotadatok segítségével figyelheti az Azure Kubernetes szolgáltatásban (ak), Azure Stack vagy más környezetben üzemeltetett Kubernetes-fürtök munkaterhelését két perspektívából. Közvetlenül a fürtből is megfigyelhető, vagy megtekintheti az előfizetésben lévő összes fürtöt Azure Monitorból. A Azure Container Instances megtekintése akkor is lehetséges, ha egy adott AK-fürtöt figyel.

Ez a cikk segít megérteni a két perspektívát, és hogyan segíti a Azure Monitor az észlelt problémák gyors felmérését, kivizsgálását és megoldását.

További információ a tárolók Azure Monitorének engedélyezéséről: [Azure monitor a tárolók számára](container-insights-onboard.md).

A Azure Monitor egy több fürtből álló nézetet biztosít, amely a Linux és a Windows Server 2019 operációs rendszert futtató összes figyelt Kubernetes-fürt állapotát megjeleníti az előfizetésekben található erőforráscsoportok között. Megjeleníti a megoldás által nem figyelt környezetekben felderített fürtöket. Azonnal megismerheti a fürt állapotát, és itt megtekintheti a csomópont-és vezérlő teljesítmény lapját, vagy megnyithatja a fürthöz tartozó teljesítménymutatókat. A felderített és nem ellenőrzöttként azonosított AK-fürtök esetében bármikor engedélyezheti a figyelést. 

A Windows Server-fürtök egy Linux-fürthöz képest Azure Monitor használatával történő figyelésének fő különbségeit [itt](container-insights-overview.md#what-does-azure-monitor-for-containers-provide) találja az áttekintő cikkben.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure portálra](https://portal.azure.com). 

## <a name="multi-cluster-view-from-azure-monitor"></a>Több fürtből származó nézet Azure Monitor

Az összes telepített Kubernetes-fürt állapotának megtekintéséhez válassza a **figyelő** elemet a Azure Portal bal oldali paneljén. Az **áttekintések** szakaszban válassza a **tárolók**lehetőséget. 

![Példa Azure Monitor több fürtből álló irányítópultra](./media/container-insights-analyze/azmon-containers-multiview.png)

A rácsban megjelenített eredmények köre a következő fürtök megjelenítéséhez használható:

* Az Azure Kubernetes Service-ben üzemeltetett **Azure** -AK-és AK-motor-fürtök
* **Azure stack (előzetes verzió)** – ak – a Azure Stackban üzemeltetett fürtök
* **Nem Azure (előzetes verzió)** – a helyszínen üzemeltetett Kubernetes-fürtök
* **Összes** – az Azure-ban üzemeltetett összes Kubernetes-fürt, Azure stack és helyszíni környezetek megtekintése a tárolók Azure monitor

Egy adott környezetből származó fürtök megtekintéséhez válassza ki az oldal bal felső sarkában található **környezetek** pirulát.

![Példa a környezeti pirula kiválasztására](./media/container-insights-analyze/clusters-multiview-environment-pill.png)

A **figyelt fürtök** lapon megismerheti a következőket:

- Hány fürt kritikus vagy nem kifogástalan állapotban van, illetve hogy hányan vannak kifogástalan vagy nem jelentett jelentések (más néven ismeretlen állapot).
- Függetlenül attól, hogy az összes [Azure Kubernetes Engine (ak-Engine)](https://github.com/Azure/aks-engine) üzemelő példány kifogástalan állapotú-e.
- A fürtben lévő csomópontok és a felhasználói és a rendszerbeli hüvelyek száma.
- Mekkora lemezterület áll rendelkezésre, és hogy van-e kapacitási probléma.

A benne foglalt állapotok a következők: 

* **Kifogástalan**: a virtuális géphez nem észlelhető probléma, és szükség szerint működik. 
* **Kritikus**: a rendszer egy vagy több kritikus problémát észlelt, amelyeket a várt módon kell kezelni a normál működés állapotának visszaállításához.
* **Figyelmeztetés**: a rendszer egy vagy több olyan problémát észlelt, amelyet meg kell oldani, vagy ha az állapot kritikus fontosságú lehet.
* **Ismeretlen**: Ha a szolgáltatás nem tudott kapcsolódni a csomóponthoz vagy a hüvelyhez, az állapot ismeretlen állapotra vált.
* **Nem található**: vagy a munkaterületet, az erőforráscsoportot vagy az előfizetést, amely a megoldás munkaterületét tartalmazza.
* Nem **engedélyezett**: a felhasználónak nincs engedélye a munkaterületen lévő információk olvasásához.
* **Hiba**: hiba történt az adatok munkaterületről való beolvasására tett kísérlet során.
* **Helytelenül van konfigurálva: Azure monitor**a tárolók nem megfelelően vannak konfigurálva a megadott munkaterületen.
* **Nincsenek adatértékek**: az elmúlt 30 percben a munkaterületre nem jelentettek adathalmazt.

Az állapot kiszámítja a fürt általános állapotát úgy, hogy a három állapot közül a *legrosszabbat* egy kivétellel adja ki. Ha a három állam bármelyike ismeretlen, a teljes fürt állapota **ismeretlen**. 

A következő táblázat a megfigyelt fürt állapotának a többfürtes nézeten belüli állapotát szabályozó számítás részletezését tartalmazza.

| |Állapot |Elérhetőség |  
|-------|-------|-----------------|  
|**Felhasználói Pod**| | |  
| |Kifogástalan |100% |  
| |Figyelmeztetés |90 – 99% |  
| |Kritikus |< 90% |  
| |Ismeretlen |Ha nem jelentett az elmúlt 30 percben |  
|**System Pod**| | |  
| |Kifogástalan |100% |
| |Figyelmeztetés |– |
| |Kritikus |100% < |
| |Ismeretlen |Ha nem jelentett az elmúlt 30 percben |
|**Csomópont** | | |
| |Kifogástalan |> 85% |
| |Figyelmeztetés |60 – 84% |
| |Kritikus |60% < |
| |Ismeretlen |Ha nem jelentett az elmúlt 30 percben |

A fürtök listájából kiválaszthatja a **fürt** lapját a fürt nevének kiválasztásával. Ezután lépjen a **csomópontok** teljesítmény lapra úgy, hogy kijelöli az adott fürt **csomópontok** oszlopában lévő csomópontok összesítését. Vagy a **vezérlők** teljesítmény lapjára kattintva megtekintheti a **felhasználói hüvelyek** vagy a **System hüvelyek** oszlop összesítését.

## <a name="view-performance-directly-from-a-cluster"></a>Teljesítmény megtekintése közvetlenül a fürtből

A tárolók Azure Monitorhoz való hozzáférése közvetlenül egy AK-fürtből érhető el **, ha a** bal oldali ablaktáblában kijelöli az adatok > a **fürtöt** , vagy ha a fürt lehetőséget választotta a több fürtből álló nézetből. A fürtre vonatkozó információk négy perspektívába vannak rendezve:

- Fürt
- Csomópontok 
- Vezérlők 
- Tárolók

>[!NOTE]
>A cikk további részében ismertetett tapasztalatok a Azure Stack vagy más környezetben üzemeltetett Kubernetes-fürtök teljesítményének és állapotának megtekintésére is érvényesek, ha a több fürtből álló nézetből van kiválasztva. 

Megnyílik az alapértelmezett lap, és a fürt fő teljesítménymutatóit megjelenítő négy soros teljesítményű diagramot jelenít meg. 

![Példa teljesítmény-diagramok a fürt lapon](./media/container-insights-analyze/containers-cluster-perfview.png)

A teljesítmény-diagramok négy teljesítménymutatót jelenítenek meg:

- **Node CPU-kihasználtság&nbsp;%** : a teljes fürt CPU-kihasználtságának összesített perspektívája. Az időtartomány eredményeinek szűréséhez válassza az **AVG**, a **min**, az **50**, a **kilencven**, a **95.** vagy a **Max** lehetőséget a diagram feletti percentilis-választóban. A szűrők akár egyénileg, akár kombinálva is használhatók. 
- A **Node memória kihasználtsága&nbsp;%** : a teljes fürt memória-kihasználtságának összesített perspektívája. Az időtartomány eredményeinek szűréséhez válassza az **AVG**, a **min**, az **50**, a **kilencven**, a **95.** vagy a **Max** lehetőséget a diagram feletti percentilis-választóban. A szűrők akár egyénileg, akár kombinálva is használhatók. 
- **Csomópontok száma**: a csomópontok száma és állapota a Kubernetes. A megjelenített fürtcsomópontok állapota összesen, kész és nem üzemkész. A diagram felett a választóban egyenként vagy kombinálva is szűrhetők. 
- **Aktív Pod darabszám**: a Kubernetes származó Pod-darabszám és-állapot. A képviselt hüvelyek állapota összesen, függőben, fut, ismeretlen, sikeres vagy sikertelen. A diagram felett a választóban egyenként vagy kombinálva is szűrhetők. 

A bal és jobb nyílbillentyűk használatával a diagramon lévő egyes adatpontokon keresztül válthat. Használja a fel és le nyílbillentyűket a percentilis-vonalakon való váltáshoz. Az egyik diagram jobb felső sarkában válassza a rögzítés ikont, hogy rögzítse a kiválasztott diagramot az utoljára megtekintett Azure-irányítópulton. Az irányítópultról átméretezheti és áthelyezheti a diagramot. Ha kiválasztja a diagramot az irányítópultról, a rendszer átirányítja Azure Monitor a tárolók számára, és betölti a megfelelő hatókört és nézetet.

A tárolók Azure Monitor támogatja a Azure Monitor [metrikák Explorert](../platform/metrics-getting-started.md)is, ahol saját diagramokat hozhat létre, összekapcsolhatja és megvizsgálhatja a trendeket, és rögzítheti az irányítópultokat. A metrikák Explorerrel a mérőszámok [metrika-alapú riasztási szabályának](../platform/alerts-metric.md)alapjaként beállított feltételeket is használhatja. 

## <a name="view-container-metrics-in-metrics-explorer"></a>Tároló metrikáinak megtekintése a metrikák Explorerben

A metrikák Explorerben megtekintheti az összesített csomópont-és Pod-kihasználtsági metrikákat Azure Monitor a tárolók számára. A következő táblázat összefoglalja a részleteket, amelyekkel megismerheti, Hogyan jeleníthető meg a metrikai diagramok a tároló metrikáinak megjelenítéséhez.

|Névtér | Metrika | Leírás | 
|----------|--------|-------------|
| bepillantást nyerhet. tároló/csomópontok | |
| | cpuUsageMillicores | A processzor kihasználtságának összesített mérése a fürtön belül. Ez egy CPU-mag, amely 1000 egységre oszlik (Millet = 1000). A magok olyan tárolóban való használatának megállapítására szolgál, amelyben sok alkalmazás egy mag használatát is felhasználhatja.| 
| | cpuUsagePercentage | Összesített átlagos CPU-kihasználtság a fürtben mért százalékban kifejezve.|
| | memoryRssBytes | A tároló RSS-memóriája bájtban használatos.| 
| | memoryRssPercentage | A tárolóban használt RSS-memória százalékban megadva.|
| | memoryWorkingSetBytes | A tároló munkakészletének felhasznált memóriája.| 
| | memoryWorkingSetPercentage | A tároló munkakészletének%-ban használt memóriája. | 
| | nodesCount | Csomópontok száma a Kubernetes.|
| bepillantást nyerhet. tároló/hüvely | |
| | podCount | A Kubernetes származó Pod-szám.|

[A mérőszámokat megtekintheti](../platform/metrics-charts.md#apply-splitting-to-a-chart) dimenzió alapján, és megjelenítheti, hogy a különböző szegmensek hogyan hasonlítanak egymáshoz. Csomópont esetén a diagramot a *gazdagép* dimenziója alapján is szegmentálhatja. Egy Pod-ból a következő méretek alapján szegmentálhatja azt:

* Tartományvezérlő
* Kubernetes-névtér
* Csomópont
* Fázis

## <a name="analyze-nodes-controllers-and-container-health"></a>Csomópontok, vezérlők és tároló állapotának elemzése

Amikor átvált a **csomópontokra**, a **vezérlőkre**és a **tárolók** lapra, a rendszer automatikusan megjeleníti a tulajdonságlap jobb oldalán lévő tulajdonságok ablaktáblát. Megjeleníti a kiválasztott elem tulajdonságait, beleértve a Kubernetes objektumok rendszerezéséhez definiált címkéket is. Linux-csomópont kiválasztásakor a **helyi lemez kapacitása** szakasz a rendelkezésre álló lemezterületet és a csomóponton bemutatott egyes lemezek százalékos arányát is megjeleníti. A panel megjelenítéséhez vagy elrejtéséhez válassza a **>>** hivatkozást a ablaktáblán.

A hierarchiában lévő objektumok kibontásakor a Tulajdonságok ablaktábla a kiválasztott objektum alapján frissül. A panelen megtekintheti az Kubernetes-tárolók (StdOut/stderror), az események és a pod mérőszámok megjelenítését az ablaktábla tetején található **élő adatok megtekintése (előzetes verzió)** hivatkozás kiválasztásával. Az adatok megtekintéséhez és vezérléséhez szükséges konfigurációval kapcsolatos további információkért lásd: [az élő adatok beállítása (előzetes verzió)](container-insights-livedata-setup.md). A fürterőforrások áttekintése közben a tárolóban található adatok valós időben is megtekinthetők. A szolgáltatással kapcsolatos további információkért tekintse meg [a Kubernetes-naplók,-események és a pod-metrikák valós idejű megtekintését](container-insights-livedata-overview.md)ismertető témakört. Ha a munkaterületen tárolt Kubernetes-naplókat előre definiált naplók alapján szeretné megtekinteni, válassza a nézet **tárolói naplók megtekintése** az **elemzések** legördülő listából lehetőséget. További információ erről a témakörről: [keresési naplók az adatok elemzéséhez](container-insights-log-search.md#search-logs-to-analyze-data).

A lap tetején a **+ szűrő hozzáadása** lehetőséggel szűrheti a nézet eredményeit a **szolgáltatás**, a **csomópont**, a **névtér**vagy a **csomópont-készlet**alapján. Miután kiválasztotta a szűrő hatókörét, válassza ki a **kívánt érték (ek)** mezőben megjelenő értékek egyikét. A szűrő konfigurálása után a rendszer globálisan alkalmazza az AK-fürt bármely perspektívájának megtekintésekor. A képlet csak az egyenlőségjel használatát támogatja. Az eredmények további szűkítéséhez további szűrőket is hozzáadhat az elsőhöz. Ha például egy szűrőt ad meg a **csomópont**alapján, csak a második szűrőhöz tartozó **szolgáltatás** vagy **névtér** lehetőséget választhatja.

Egy szűrő egy lapon való megadása továbbra is alkalmazható, amikor kiválaszt egy másikat. A megadott szűrő melletti **x** szimbólum kiválasztása után törlődik. 

Váltson a **csomópontok** lapra, és a sor hierarchiája a Kubernetes objektum modelljét követi, amely a fürt egy csomópontjához indul. Bontsa ki a csomópontot egy vagy több, a csomóponton futó hüvely megtekintéséhez. Ha egynél több tároló van csoportosítva egy Pod-ba, akkor a hierarchia utolsó soraként jelennek meg. Azt is megtekintheti, hogy a gazdagépen hány nem a hüvelyhez kapcsolódó számítási feladat fusson, ha a gazdagép processzor-vagy memória-nyomást tartalmaz.

![Példa a Kubernetes csomópont-hierarchiára a teljesítmény nézetben](./media/container-insights-analyze/containers-nodes-view.png)

A Windows Server 2019 operációs rendszert futtató Windows Server-tárolók a listában szereplő összes Linux-alapú csomópont után jelennek meg. Egy Windows Server-csomópont kibontásakor megtekintheti a csomóponton futó egy vagy több hüvelyt és tárolót. Egy csomópont kijelölése után a Tulajdonságok ablaktábla a verziószámot jeleníti meg. Az ügynök adatai ki vannak zárva, mert a Windows Server-csomópontok nem rendelkeznek ügynökkel. 

![Példa a csomópont-hierarchiára a felsorolt Windows Server-csomópontokkal](./media/container-insights-analyze/nodes-view-windows.png) 

Azure Container Instances a Linux operációs rendszert futtató virtuális csomópontok a listában az utolsó AK-fürt csomópontja után jelennek meg. Container Instances virtuális csomópont kibontásakor megtekintheti a csomóponton futó egy vagy több Container Instances hüvelyt és tárolót. A metrikák nem gyűjtése és jelentése a csomópontok számára, csak hüvelyek esetében.

![Példa Container Instances felsorolt csomópont-hierarchiára](./media/container-insights-analyze/nodes-view-aci.png)

Egy kibontott csomópontból lehatolhat a csomóponton futó Pod vagy tárolóból a vezérlőre, így megtekintheti a vezérlőhöz tartozó teljesítményadatokat. Válassza ki az értéket az adott csomópont **vezérlő** oszlopában.
 
![Példa a csomópontról a teljesítmény nézet vezérlőre való részletezésére](./media/container-insights-analyze/drill-down-node-controller.png)

Válassza az oldal tetején található vezérlők vagy tárolók lehetőséget az objektumok állapotának és erőforrás-kihasználtságának áttekintéséhez. A memóriahasználat áttekintéséhez a **metrika** legördülő listában válassza ki a **memória RSS** vagy a **memória munkakészlete**elemet. A **memória RSS** -je csak a 1,8-es és újabb verziójú Kubernetes esetén támogatott. Ellenkező esetben a **Min&nbsp;%** értékeit kell megtekinteni *Nan&nbsp;%ként* , amely egy numerikus adattípus-érték, amely nem definiált vagy nem reprezentált értéket jelöl.

![Tároló csomópontjainak teljesítmény nézete](./media/container-insights-analyze/containers-node-metric-dropdown.png)

A **memória-munkakészletben** a rezidens memória és a virtuális memória (gyorsítótár) is látható, és az alkalmazás teljes egészében a használatos. A **memória RSS** -je csak a fő memóriát jeleníti meg (amely nem más, mint a rezidens memória, más szóval). Ez a metrika a rendelkezésre álló memória tényleges kapacitását mutatja. Mi a különbség a rezidens memória és a virtuális memória között?

- A rezidens memória vagy a fő memória a fürt csomópontjai számára elérhető tényleges memória mennyisége.

- A virtuális memória az operációs rendszer által a memóriából a lemezre való adatcserére használt szabad lemezterület (gyorsítótár), amely a memória nyomása alatt, majd a memóriába való beolvasása, ha szükséges.

Alapértelmezés szerint a teljesítményadatok az elmúlt hat órában alapulnak, de a bal felső sarokban található **TimeRange** lehetőség használatával módosíthatja az ablakot. Az eredményeket az időtartományon belül is szűrheti, ha a percentilis választóban a **min**, az **AVG**, az **50**, a **kilencven**, a **95.** és a **Max** elemet választja. 

![Az Adatszűrés százalékos értékének kiválasztása](./media/container-insights-analyze/containers-metric-percentile-filter.png)

Ha a **trend** oszlop alatt lévő oszlopdiagram fölé viszi a mutatót, akkor az egyes sávokban a CPU vagy a memóriahasználat érték látható, attól függően, hogy melyik metrikát választotta ki, egy 15 perces mintavételi időszakon belül. Miután kiválasztotta a trend diagramot a billentyűzeten, használja az ALT + Page Up vagy az ALT + Page Down billentyűt az egyes sávokon való váltáshoz. Ugyanazokat az adatokat fogja megkapni, mint a sáv fölé.

![Példa a trend oszlopdiagram fölé](./media/container-insights-analyze/containers-metric-trend-bar-01.png) 

A következő példában a lista első csomópontjának *AK-nodepool1 –* értéke a **tárolók** esetében 9. Ez az érték a telepített tárolók teljes számának összesítése.

![Tárolók összesítése – példa egy csomópontra](./media/container-insights-analyze/containers-nodes-containerstotal.png)

Ezek az információk segítenek gyorsan megállapítani, hogy megfelelő-e a tárolók közötti egyensúly a fürt csomópontjai között. 

A **csomópontok** lap megtekintésekor megjelenő információkat az alábbi táblázat ismerteti.

| Column | Leírás | 
|--------|-------------|
| Név | A gazdagép neve. |
| Állapot | A csomópont állapotának Kubernetes. |
| Min&nbsp;%, átlagos&nbsp;%, 50&nbsp;%, 90&nbsp;%, 95.&nbsp;%, Max&nbsp;%  | A csomópontok átlagos százalékos aránya a megadott időtartamon belül a percentilis alapján. |
| Min, AVG, 50, 90, 95., Max | A csomópontok átlagos tényleges értéke a percentilis alapján a megadott időtartam alatt. Az átlagos értéket a rendszer a csomópont processzor-/memória-korlátja alapján méri. A hüvelyek és a tárolók esetében ez a gazdagép által jelentett átlagos érték. |
| Tárolók | Tárolók száma. |
| Hasznos üzemidő | A csomópont elindítása vagy újraindítása óta eltelt időt jelöli. |
| Tartományvezérlő | Csak a tárolók és a hüvelyek esetében. Azt mutatja, hogy melyik vezérlő található a ben. Nem minden hüvely van vezérlőben, ezért előfordulhat, hogy egyesek **N/a**-t jelenítenek meg. | 
| Trend min&nbsp;%, AVG&nbsp;%, 50&nbsp;%, 90&nbsp;%, 95.&nbsp;%, Max&nbsp;% | A oszlopdiagram trendje a vezérlő átlagos százalékos értékének százalékos arányát jelöli. |

A választóban válassza a **vezérlők**lehetőséget.

![Vezérlők nézet kiválasztása](./media/container-insights-analyze/containers-controllers-tab.png)

Itt megtekintheti a vezérlők teljesítményének állapotát, és Container Instances virtuális csomópont-vezérlőket vagy virtuális csomópontok hüvelyeit, amelyek nem csatlakoznak a vezérlőhöz.

![\<név > vezérlők teljesítmény nézete](./media/container-insights-analyze/containers-controllers-view.png)

A sor hierarchiája egy vezérlővel kezdődik. A vezérlő kibontásakor egy vagy több hüvelyt fog megtekinteni. Bontsa ki a hüvelyt, az utolsó sorban pedig a pod-ba csoportosított tároló látható. Egy kibontott vezérlőből megtekintheti a csomóponton futó csomópontot, és megtekintheti az adott csomópontra szűrt teljesítményadatokat. A lista utolsó részében Container Instances hüvelyek nem csatlakoznak vezérlőhöz.

![Példa vezérlő-hierarchia Container Instances hüvelyekkel](./media/container-insights-analyze/controllers-view-aci.png)

Válassza ki az értéket az adott vezérlő **csomópont** oszlopában.

![Példa a csomópontról a teljesítmény nézet vezérlőre való részletezésére](./media/container-insights-analyze/drill-down-controller-node.png)

A vezérlők megtekintésekor megjelenő információkat az alábbi táblázat ismerteti.

| Column | Leírás | 
|--------|-------------|
| Név | A vezérlő neve.|
| Állapot | A tárolók összesítési állapota, miután befejezte az állapotot, például *az OK*, a leállítva *, a* *sikertelen*, a *leállított*vagy a *szüneteltetve*állapotot. Ha a tároló fut, de az állapot nem volt megfelelően megjelenítve, vagy nem az ügynök vette át, és 30 percnél nem válaszolt, az állapot *ismeretlen*. Az állapotjelző ikon további részleteit a következő táblázat tartalmazza.|
| Min&nbsp;%, átlagos&nbsp;%, 50&nbsp;%, 90&nbsp;%, 95.&nbsp;%, Max&nbsp;%| Az egyes entitások átlagos százalékának összesítési átlaga a kiválasztott metrika és a percentilis esetében. |
| Min, AVG, 50, 90, 95., Max  | A kiválasztott százalékos értékhez tartozó tároló átlagos CPU-millicore vagy memória-teljesítményének összesítése. Az átlagos értéket a hüvely processzor-/memória-korlátja határozza meg. |
| Tárolók | A vezérlő vagy a pod tárolók teljes száma. |
| Újraindítja | A tárolók újraindítási számának összesítése. |
| Hasznos üzemidő | A tároló elindítása óta eltelt időt jelöli. |
| Csomópont | Csak a tárolók és a hüvelyek esetében. Azt mutatja, hogy melyik vezérlő található a ben. | 
| Trend min&nbsp;%, AVG&nbsp;%, 50&nbsp;%, 90&nbsp;%, 95.&nbsp;%, Max&nbsp;% | A oszlopdiagram trend a vezérlő átlagos percentilis metrikáját jelöli. |

Az állapot mezőben lévő ikonok jelzik a tárolók online állapotát.
 
| Ikon | Állapot | 
|--------|-------------|
| ![Futtatásra kész állapot ikonja](./media/container-insights-analyze/containers-ready-icon.png) | Futtatás (kész)|
| ![Várakozó vagy szüneteltetett állapot ikonja](./media/container-insights-analyze/containers-waiting-icon.png) | Várakozás vagy szüneteltetve|
| ![Legutóbbi jelentett Futási állapot ikon](./media/container-insights-analyze/containers-grey-icon.png) | Legutóbbi jelentett Futtatás, de 30 percnél hosszabb ideig nem válaszolt|
| ![Sikeres állapot ikon](./media/container-insights-analyze/containers-green-icon.png) | Sikerült leállítani vagy leállítani|

Az állapot ikon a pod által szolgáltatott darabszámot jeleníti meg. Megjeleníti a legrosszabb két állapotot, és amikor az állapot fölé viszi a mutatót, a tárolóban lévő összes hüvely összesítési állapotát jeleníti meg. Ha nincs Üzemkész állapot, az állapot értéke **(0)** jelenik meg.

A választóban válassza a **tárolók**lehetőséget.

![Tárolók nézet kiválasztása](./media/container-insights-analyze/containers-containers-tab.png)

Itt megtekintheti az Azure-Kubernetes teljesítménybeli állapotát, és Azure Container Instances tárolókat is. 

![\<neve > tárolók teljesítmény nézete](./media/container-insights-analyze/containers-containers-view.png)

Egy tárolóból megtekintheti a hüvely vagy a csomópont részletezését az adott objektumhoz tartozó teljesítményadatokat tartalmazó adatok megtekintéséhez. Válassza ki az értéket az adott tárolóhoz tartozó **Pod** vagy **Node** oszlopban.

![Példa a csomópontok és a tárolók közötti részletezésre a teljesítmény nézetben](./media/container-insights-analyze/drill-down-controller-node.png)

A tárolók megtekintésekor megjelenő információkat az alábbi táblázat ismerteti.

| Column | Leírás | 
|--------|-------------|
| Név | A vezérlő neve.|
| Állapot | A tárolók állapota, ha van ilyen. Az állapot ikon további részleteket tartalmaz a következő táblázatban.|
| Min&nbsp;%, átlagos&nbsp;%, 50&nbsp;%, 90&nbsp;%, 95.&nbsp;%, Max&nbsp;% | Az egyes entitások átlagos százalékos arányának összesítése a kiválasztott metrika és percentilis esetében. |
| Min, AVG, 50, 90, 95., Max | A kiválasztott százalékos értékhez tartozó tároló átlagos CPU-millicore vagy memória-teljesítményének összesítése. Az átlagos értéket a hüvely processzor-/memória-korlátja határozza meg. |
| Pod | A tároló, ahol a pod található.| 
| Csomópont |  Az a csomópont, amelyben a tároló található. | 
| Újraindítja | A tároló elindítása óta eltelt időt jelöli. |
| Hasznos üzemidő | A tároló elindítása vagy újraindítása óta eltelt időt jelöli. |
| Trend min&nbsp;%, AVG&nbsp;%, 50&nbsp;%, 90&nbsp;%, 95.&nbsp;%, Max&nbsp;% | A oszlopdiagram trendje a tároló átlagos százalékos értékének százalékos arányát jelöli. |

Az állapot mezőben látható ikonok a hüvelyek online állapotát jelzik, az alábbi táblázatban leírtak szerint.
 
| Ikon | Állapot |  
|--------|-------------|  
| ![Futtatásra kész állapot ikonja](./media/container-insights-analyze/containers-ready-icon.png) | Futtatás (kész)|  
| ![Várakozó vagy szüneteltetett állapot ikonja](./media/container-insights-analyze/containers-waiting-icon.png) | Várakozás vagy szüneteltetve|  
| ![Legutóbbi jelentett Futási állapot ikon](./media/container-insights-analyze/containers-grey-icon.png) | Legutóbbi jelentett Futtatás, de 30 percen belül nem válaszolt|  
| ![Leállított állapot ikon](./media/container-insights-analyze/containers-terminated-icon.png) | Sikerült leállítani vagy leállítani|  
| ![Sikertelen állapot ikonja](./media/container-insights-analyze/containers-failed-icon.png) | Sikertelen állapot |  

## <a name="workbooks"></a>Munkafüzetek

A munkafüzetek szövegeket, [naplókat](../log-query/query-language.md), [metrikákat](../platform/data-platform-metrics.md)és paramétereket egyesítenek gazdag interaktív jelentésekben. A munkafüzetek szerkeszthető más csapattagok számára, akik ugyanahhoz az Azure-erőforrásokhoz férnek hozzá.

A tárolók Azure Monitor négy munkafüzetet tartalmaznak az első lépések elkezdéséhez:

- **Lemez kapacitása**: interaktív lemezhasználat-diagramokat jelenít meg a tárolóban a csomóponton a következő perspektívák által bemutatott lemezekhez:

    - Az összes lemez százalékos kihasználtsága.
    - Szabad lemezterület az összes lemez számára.
    - Egy rács, amely megjeleníti az egyes csomópontok lemezeit, a felhasznált terület százalékos arányát, a felhasznált terület százalékos arányát, a szabad lemezterületet (GiB) és a szabad lemezterület (GiB) trendjét. Ha a táblázatban egy sor van kiválasztva, a rendszer a felhasznált terület és a szabad lemezterület (GiB) százalékos arányát jeleníti meg a sor alatt. 

- **Lemez i/o**: interaktív lemez-kihasználtsági diagramokat jelenít meg a tárolóban a csomóponton a következő perspektívák által bemutatott lemezekhez:

    - Lemez I/O összegzése az összes lemezről olvasási sebesség (bájt/s), bájt/mp írás, olvasás és írás bájt/mp tendenciák.
    - Nyolc teljesítményű diagramon a fő teljesítménymutatók láthatók a lemez I/O-szűk keresztmetszetek mérése és azonosítása érdekében.

- **Kubelet**: két rácsot tartalmaz, amelyek a fő csomópont működési statisztikáit mutatják:

    - A Node Grid áttekintés a teljes művelet, a teljes hibák és a sikeres műveletek összesítésével összegzi az egyes csomópontok százalékát és trendjét.
    - Az áttekintés a műveleti típus szerint összefoglalja a teljes művelet, az összes hiba és a sikeres műveletek százalékos és trend szerinti műveleteit.

- **Hálózat**: interaktív hálózati kihasználtsági diagramokat jelenít meg az egyes csomópontok hálózati adapterei számára, a rács pedig a fő teljesítménymutatókat mutatja be a hálózati adapterek teljesítményének méréséhez.

Ezeket a munkafüzeteket úgy érheti el, ha kiválasztja a **munkafüzetek megtekintése** legördülő listát.

![Munkafüzetek megtekintése legördülő lista](./media/container-insights-analyze/view-workbooks-dropdown-list.png)

## <a name="next-steps"></a>Következő lépések

- Tekintse át a [teljesítménnyel kapcsolatos riasztások létrehozása a Azure monitor for containers](container-insights-alerts.md) szolgáltatással című témakört, amelyből megtudhatja, hogyan hozhat létre riasztásokat magas CPU-és memóriahasználat esetén a DevOps vagy működési folyamatok és eljárások

- Megtekintheti a [napló lekérdezési példáit](container-insights-log-search.md#search-logs-to-analyze-data) , amelyekkel előre definiált lekérdezéseket és példákat tekinthet meg a fürtök riasztásához, megjelenítéséhez vagy elemzéséhez.

- A Kubernetes-fürt állapotának megtekintéséhez tekintse meg a [fürt állapotának figyelése](container-insights-health.md) című témakört.
