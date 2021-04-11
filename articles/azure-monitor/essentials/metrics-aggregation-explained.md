---
title: Azure Monitor metrikák metrikáinak összesítése és megjelenítése
description: Részletes információk a metrikák összesítéséről Azure Monitor
author: rboucher
ms.author: robb
services: azure-monitor
ms.topic: conceptual
ms.date: 03/10/2021
ms.openlocfilehash: c89b352954f114ec9da22cad6751bb57ef59899b
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2021
ms.locfileid: "106381797"
---
# <a name="azure-monitor-metrics-metrics-aggregation-and-display-explained"></a>Azure Monitor metrikák metrikáinak összesítése és megjelenítése

Ez a cikk a metrikák összesítését ismerteti a Azure Monitor idősoros adatbázisban, amely Azure Monitor [platform metrikáit](../data-platform.md) és [Egyéni metrikákat](../essentials/metrics-custom-overview.md)tartalmaz. Ez a cikk a standard [Application Insights mérőszámokra](../app/app-insights-overview.md)is vonatkozik. 

Ez egy összetett témakör, és nem szükséges megérteni a cikkben szereplő összes információt, hogy Azure Monitor metrikákat hatékonyan használhassa.

## <a name="overview-and-terms"></a>Áttekintés és feltételek

Ha mérőszámot ad hozzá egy diagramhoz, a metrikák Explorer automatikusan kiválasztja az alapértelmezett összesítést. Az alapértelmezett megoldás az alapvető forgatókönyvek értelmezése, de különböző összesítéseket használhat a metrikával kapcsolatos további információk eléréséhez. A diagramokon a különböző összesítések megtekintéséhez meg kell ismernie, hogy a metrikák kezelője hogyan kezeli őket.

Hozzunk létre néhány kifejezést egyértelműen:

- **Metrika értéke** – egy adott erőforráshoz összegyűjtött egyetlen mérési érték.
- **Idősorozat-adatbázis** – az adatpontok tárolásához és lekéréséhez optimalizált adatbázis, amely egy értéket és egy megfelelő időbélyeget tartalmaz. 
- **Időtartam – általános** időtartam.
- **Időköz** – a két metrikai érték összegyűjtése között eltelt idő. 
- **Time Range (időtartomány** ) – a diagramon megjelenő időtartam. A tipikus alapértelmezett érték 24 óra. Csak meghatározott tartományok érhetők el. 
- **Idő részletessége** vagy **időgabona** – az értékek összesítéséhez használt időtartam, amely lehetővé teszi a diagramon való megjelenítést. Csak meghatározott tartományok érhetők el. Az aktuális minimum 1 perc. Az időrészletességi értéknek kisebbnek kell lennie a kiválasztott időtartománynál, hogy a hasznos legyen, ellenkező esetben csak egy érték jelenik meg a teljes diagramon. 
- **Összesítési típus** – a statisztikai típus több metrikai értékből számított.  
- **Összesítés** – több bemeneti érték beírásának folyamata, majd ezek használatával egyetlen kimeneti érték hozható létre az összesítési típus által definiált szabályok segítségével. Például átlagosan több értéket kell megtenni.  

## <a name="summary-of-process"></a>Folyamat összefoglalása

A metrikák egy időbélyeggel tárolt értékek sorozata. Az Azure-ban a legtöbb metrika az Azure metrika idősorozat-adatbázisában van tárolva. Amikor Kirajzol egy diagramot, a rendszer beolvassa a kijelölt mérőszámok értékeit az adatbázisból, majd külön összesíti azokat a választott idő részletessége (más néven időgabona) alapján. Az időrészletesség méretét a [Metrikaböngésző Time Picker panelen](../essentials/metrics-getting-started.md#select-a-time-range)választhatja ki. Ha nem választ explicit választ, a rendszer automatikusan kijelöli az idő részletességét a jelenleg kijelölt időtartomány alapján. A kiválasztást követően a rendszer összesíti az egyes időmérési intervallumokban rögzített mérőszám-értékeket, és a diagramra helyezi át a Datapoint.

## <a name="aggregation-types"></a>Összesítési típusok 

A mérőszámok Explorerben öt alapvető aggregációs típus érhető el. A metrikák Explorer elrejti a nem releváns összesítéseket, és nem használható egy adott metrika esetében. 

- **Sum (összeg** ) – az összesítési intervallumban rögzített összes érték összege. Más néven a teljes összesítés.
- **Count (darabszám** ) – az összesítési intervallumban rögzített mértékegységek száma. A Count nem veszi át a mérték értékét, csak a rekordok számát. 
- **Average (átlag** ) – az összesítési intervallumban rögzített metrikai értékek átlaga. A legtöbb mérőszám esetében ez az érték az összeg/darabszám. 
- **Min** – az összesítési intervallumban rögzített legkisebb érték.
- **Max** – az összesítési intervallumban rögzített legnagyobb érték.

Tegyük fel például, hogy egy diagram a virtuális gép **hálózati kimenő teljes** metrikáját mutatja az elmúlt 24 órás időtartományon belül az **összeg** összesítésének használatával. Az időtartomány és a részletesség a diagram jobb felső sarkában módosítható, ahogy az alábbi képernyőképen látható.

:::image type="content" source="media/metrics-aggregation-explained/time-range-granularity-picker.png" alt-text="A Time Range és az időrészletességi választót ábrázoló képernyőfelvétel" border="true":::

Az idő részletessége = 30 perc és az időtartomány = 24 óra:

- A diagram 48 datapoints van kirajzolva. Ez a napi 24 óra x 2 datapoints (egyórás/30min) összesítve 1 perces datapoints. 
- A vonalas diagram az 48 pontot köti össze a diagram ábrázolási területén. 
- Mindegyik Datapoint az összes hálózati kimenő bájt összegét jelöli, amelyek mindegyike az adott 30 perces időszakra vonatkozóan el lett küldve. 


 :::image type="content" source="media/metrics-aggregation-explained/24-hour-30-min-gran.png" alt-text="Képernyőfelvétel: a vonalas gráfban lévő adatokat 24 órás időtartományra és 30 percenkénti időpontra állítja be" border="true" lightbox="media/metrics-aggregation-explained/24-hour-30-min-gran.png":::

*Az ebben a szakaszban található lemezképekre kattintva megtekintheti a nagyobb verziókat.*

Ha az időrészletességet 15 percre állítja át, a diagram 96 összesített adatpontból készül. Ez a egyórás/15min = 4 datapoints/óra x 24 óra.

:::image type="content" source="media/metrics-aggregation-explained/24-hour-15-min-gran.png" alt-text="Képernyőfelvétel: a vonalas gráfban lévő adatokat 24 órás időtartományra és 15 percenkénti részletességre állítja be" border="true" lightbox="media/metrics-aggregation-explained/24-hour-15-min-gran.png":::

Az 5 perces idő részletessége esetén 24 x (60/5) = 288 pontot kap. 

:::image type="content" source="media/metrics-aggregation-explained/24-hour-5-min-gran.png" alt-text="Képernyőfelvétel: a vonalas gráfban lévő adatokat 24 órás időtartományra és 5 perces részletességre állítja be" border="true" lightbox="media/metrics-aggregation-explained/24-hour-15-min-gran.png":::

Az 1 perces időrészletességgel (a diagramon a lehető legkisebbre) a 24 x 60/1 = 1440 pontot kapja. 

:::image type="content" source="media/metrics-aggregation-explained/24-hour-1-min-gran.png" alt-text="Képernyőfelvétel: egy vonalas gráfban lévő adatokat 24 órás időtartományra és 1 perces részletességre van állítva" border="true" lightbox="media/metrics-aggregation-explained/24-hour-1-min-gran.png":::

A diagramok az előző képernyőképeken látható összegzések esetében eltérőek.  Figyelje meg, hogy ennek a virtuális gépnek sok kimenete van egy kis időtartamban a hátralévő időszakhoz képest.  

Az idő részletessége lehetővé teszi a "jel-zaj" arány módosítását egy diagramon. A magasabb összesítések a zaj és a kiegyenlítő tüskék eltávolítását végzik.  Figyelje meg az 1 perces diagram alján található változatokat, és azt, hogy a jobb részletességi értékekkel hogyan kisimítottak. 

Ez a zökkenőmentes működés akkor fontos, ha más rendszerekre (például riasztásokra) küldi ezeket az üzeneteket. Általában nem szeretné, hogy a rendszer a CPU-idő során nagyon rövid, 90%-nál nagyobb tüskékkel ne figyelmeztessen. Ha azonban a CPU 5 percig 90%-ban marad, ez valószínűleg fontos. Ha riasztási szabályt állít be a CPU-on (vagy bármely metrika), az idő részletességének növelésével csökkentheti a kapott hamis riasztások számát. 

Fontos, hogy megtudja, mi a "normális" a számítási feladatokhoz, hogy a legmegfelelőbb időintervallumot szeretné-e kialakítani. Ez a [dinamikus riasztások](../alerts/alerts-dynamic-thresholds.md)egyik előnye, amely egy másik, itt nem tárgyalt témakör.  

## <a name="how-the-system-collects-metrics"></a>A rendszerek mérőszámok gyűjtésének módja

Az adatgyűjtés mérőszám szerint változik. 

### <a name="measurement-collection-frequency"></a>Mérési gyűjtemény gyakorisága 

A gyűjtési időszakok két típusa létezik.

- **Normál** – a metrikát olyan konzisztens időközönként gyűjti a rendszer, amely nem változik.

- **Tevékenység-alapú** – a metrikát egy bizonyos típusú tranzakció esetén gyűjti a rendszer. Minden tranzakcióhoz tartozik egy metrikai bejegyzés és egy időbélyegző. A rendszer rendszeres időközönként nem gyűjti össze azokat, így az adott időszakra vonatkozóan eltérő számú rekord van. 

### <a name="granularity"></a>Részletesség

A minimális időtartam 1 perc, de az alapul szolgáló rendszer gyorsabban rögzítheti az adatokat a mérőszámtól függően. A CPU-százalékarány például 15 másodpercenként, rendszeres időközönként nyomon követhető. Mivel a HTTP-hibákat tranzakcióként követik nyomon, könnyen meghaladhatja a több mint egy percet. Más mérőszámok, például az SQL Storage rögzítése 20 percenként történik. Ez a választás az egyes erőforrás-szolgáltató és típus szerint történik. A legtöbb esetben próbálja meg a lehető legkisebb intervallumot megadni.

### <a name="dimensions-splitting-and-filtering"></a>Méretek, felosztás és szűrés

A metrikák az egyes erőforrásokhoz vannak rögzítve. A metrikák gyűjtésének, tárolásának és ábrázolásának szintje azonban eltérő lehet. Ezt a szintet a **mérőszámok méreteiben** elérhető további metrikák jelölik. Minden egyes erőforrás-szolgáltató meghatározza, hogy az összegyűjtött adatok mennyire részletesek. Azure Monitor csak a részletek megjelenítésének és tárolásának módját határozza meg. 

Ha mérőszámot diagramot ábrázol a metrika Explorerben, akkor a diagramot egy dimenzió alapján "feloszthatja".  A diagram felosztása azt jelenti, hogy az alapul szolgáló adatokat részletesebben tekinti át, és azt, hogy az adatok a metrika Explorerben legyenek kiosztva vagy szűrve.

A [Microsoft. ApiManagement/Service](./metrics-supported.md#microsoftapimanagementservice) *például számos mérőszám esetében tartalmaz egy* dimenziót. 

- A **kapacitás** egy ilyen metrika. A *hely* dimenziója azt jelenti, hogy az alapul szolgáló rendszer metrikus rekordot tárol az egyes helyek kapacitása helyett, nem csupán egyet az összesített összeghez. Ezután lekérheti vagy feloszthatja ezeket az információkat egy mérőszám-diagramon.  

- **Az átjáróra vonatkozó kérelmek teljes időtartamát** megvizsgálva 2 dimenziós *hely* és *állomásnév* található, és megtudhatja, hogy az adott időtartam hol található, és melyik állomásnévből származik.  

- Az egyik rugalmasabb metrika, **kérelem**, 7 különböző dimenzióval rendelkezik. 
 
Az egyes metrikákkal és a rendelkezésre álló dimenziókkal kapcsolatos részletekért olvassa el a Azure Monitor [által támogatott mérőszámokat](./metrics-supported.md) . Emellett az egyes erőforrás-szolgáltatók és típusok dokumentációjában további információk is megadhatók a méretekről és azok mértékéről.

A felosztással és szűréssel együtt a probléma kiásására is használható. Az alábbi példa egy olyan ábrát mutat be, amely a virtuális gépek egy csoportjának *átlagos írási bájtjait* mutatja. Az összes virtuális gép összesítését ezzel a metrikával láttuk el, de érdemes lehet bemutatni, hogy a körülbelül 6 – 6. között valóban a felelős. Ugyanaz a gép? Hány gép vesz részt?  

:::image type="content" source="media/metrics-aggregation-explained/total-disk write-bytes-all-VMs.png" alt-text="A contoso Hotels erőforráscsoport összes virtuális géphez tartozó összes lemez írási bájtjait ábrázoló képernyőfelvétel" border="true" lightbox="media/metrics-aggregation-explained/total-disk write-bytes-all-VMs.png":::

*Az ebben a szakaszban található lemezképekre kattintva megtekintheti a nagyobb verziókat.*

A felosztás alkalmazása során láthatjuk a mögöttes adatokat, de ez egy kis rendetlenség. Kiderül, hogy a fenti diagramon 20 virtuális gép van összesítve. Ebben az esetben az egeret arra használtuk, hogy az egérmutatót a nagy csúcs fölé tegyük 6 órakor, ami azt jelzi, hogy a CH-DCVM11 az OK. azonban nehéz megtekinteni a virtuális géphez társított többi adatmennyiséget a diagramot megterhelő más virtuális gépek miatt. 

:::image type="content" source="media/metrics-aggregation-explained/split-total-disk write-bytes-all-VMs.png" alt-text="Képernyőfelvétel: lemezek írási bájtjai az összes virtuális gép számára a contoso Hotels erőforráscsoporthoz a virtuális gép neve alapján felosztva" border="true" lightbox="media/metrics-aggregation-explained/split-total-disk write-bytes-all-VMs.png":::

A szűrés használata lehetővé teszi a diagram törlését, hogy meglássuk, mi történt. A megtekinteni kívánt virtuális gépeket kijelölheti vagy törölheti. Figyelje meg a pontozott vonalakat. Ezek egy későbbi szakaszban szerepelnek.

:::image type="content" source="media/metrics-aggregation-explained/split-filter-total-disk write-bytes-all-VMs.png" alt-text="Képernyőfelvétel: lemezek írási bájtjai az összes virtuális gép számára a contoso Hotels erőforráscsoport felosztása és szűrése a virtuális gép neve alapján" border="true" lightbox="media/metrics-aggregation-explained/split-filter-total-disk write-bytes-all-VMs.png":::

További információ a feldarabolt dimenziók adatainak metrikus Explorer-diagramon való megjelenítéséről: [a metrikák Explorer speciális funkciói – szűrők és felosztás](../essentials/metrics-charts.md#filters).

### <a name="null-and-zero-values"></a>NULL és nulla érték

Ha a rendszer egy erőforrás metrikai adatait várta, de nem kapja meg, akkor a NULL értéket rögzíti.  A NULL értéke eltér a nulla értéktől, ami az összesítések és a diagramok számításakor fontos lesz. A NULL értékek nem számítanak érvényes mérésnek. 

A NULLák különböző diagramokon eltérően jelennek meg. A pontdiagram kihagyja a diagramon látható pontot. A sávdiagramok kihagyását a sáv mutatja. A sorokban a NULL érték [pontozott vagy szaggatott vonalakként](../essentials/metrics-troubleshoot.md#chart-shows-dashed-line) jelenhet meg, például az előző szakaszban található képernyőképen láthatók. A NULL értéket tartalmazó átlagok kiszámításakor kevesebb adatpontra van lehetőség, hogy a rendszer az átlagot használja.  Ez a viselkedés időnként váratlan csökkenést eredményezhet egy diagramon, bár általában kisebb, mint ha az értéket nullára konvertálták, és érvényes Datapoint használták.  

Az [Egyéni metrikák](../essentials/metrics-custom-overview.md) mindig null értéket használnak, ha nem érkezik adat. A [platform metrikái](../data-platform.md)esetében minden erőforrás-szolgáltató eldönti, hogy nulla vagy null értékeket használ-e az adott metrika szempontjából.

Azure Monitor riasztások az erőforrás-szolgáltató által a metrika-adatbázisba írt értékeket használják, ezért fontos tudni, hogy az erőforrás-szolgáltató hogyan kezelje a NULLát az adatok első megtekintésével.

## <a name="how-aggregation-works"></a>Az Összesítés működése

Az előző rendszeren lévő mérőszámok diagramjai különböző típusú összesített adatokat mutatnak be. A rendszer előre összesíti az adatokat, így a kért diagramok sok ismétlődő számítás nélkül is megjelenhetnek.  

Ebben a példában:

- **Http-meghibásodások** nevű **fiktív** tranzakciós mérőszámot gyűjtünk. 
- A *kiszolgáló* a **http-hibák** metrikájának dimenziója.
- 3 kiszolgáló – az A, B és C kiszolgáló.

A magyarázat leegyszerűsítése érdekében a SUM aggregációs típust fogjuk kezdeni. 

### <a name="sub-minute-to-1-minute-aggregation"></a>Egyperces összesítés 1 percenként

Az első nyers metrikai adat gyűjtése és tárolása a Azure Monitor Metrics adatbázisban történik. Ebben az esetben az egyes kiszolgálók esetében a tranzakciós rekordok egy időbélyeggel vannak tárolva, mert a *kiszolgáló* egy dimenzió. Mivel az ügyfélként megtekinthető legkisebb időtartam 1 perc, az időbélyegek először az egyes kiszolgálók 1 perces metrikai értékeire lesznek összesítve.  A B kiszolgáló összesítési folyamata az alábbi ábrán látható. Az A és a C kiszolgáló ugyanúgy történik, mint a különböző adatkezelési módszerekkel.  

:::image type="content" source="media/metrics-aggregation-explained/sub-minute-transaction.png" alt-text="Képernyőfelvétel az alárendelt percenkénti tranzakciós bejegyzésekről 1 percenkénti összesítések esetén. " border="false":::

Az eredményül kapott 1 perces összesített értékek a metrikák adatbázisában új bejegyzésekként tárolódnak, így a későbbi számításokhoz is összegyűjthetők. 

:::image type="content" source="media/metrics-aggregation-explained/sub-minute-transaction-dimension-aggregated.png" alt-text="Képernyőfelvétel: több, egyperces összesített bejegyzés a kiszolgáló dimenzióján belül. Az A, B és C kiszolgáló egyenként látható" border="false":::

### <a name="dimension-aggregation"></a>Dimenzió összesítése

Ezután az 1 perces számításokat a dimenziók összecsukják, és újra különálló rekordokként tárolják.   Ebben az esetben az összes egyes kiszolgáló összes adatát egy 1 perces intervallum metrikába összesíti, és a metrikák adatbázisban tárolja a későbbi összesítésekben való használatra.

:::image type="content" source="media/metrics-aggregation-explained/1-minute-transaction-dimension-flattened-aggregated.png" alt-text="Képernyőfelvétel: az A, B és C kiszolgáló 1 percenként összesített bejegyzéseinek összesítése az összes kiszolgáló teljes egészében" border="false":::

Az egyértelműség kedvéért a következő táblázat az Összesítés metódusát mutatja be.

| Időszak   | A kiszolgáló | B kiszolgáló | C kiszolgáló | Sum (A + B + C)|
| -------- | -------- | -------- | -------- | --------   |  
| 1. perc | 1        | 1        | 1        | 3          |
| 2. perc | 0        | 5        | 1        | 6          |
| 3. perc | 0        | 5        | 1        | 6          |
| 4. perc | 2        | 3        | 4        | 9          |
| 5. perc | 1        | 0        | 3        | 4          |
| 6. perc | 1        | 0        | 4        | 5          |
| 7. perc | 1        | 2        | 4        | 7          |
| 8. perc | 0        | 1        | 0        | 1          |
| 9. perc | 1        | 1        | 4        | 6          |
| 10 perces| 2        | 1        | 0        | 3          |

A fentiekben csak egy dimenzió látható, de ez az összesítési és tárolási folyamat minden mérőszám által támogatott **dimenzió** esetén megtörténik.

- Az értékek összegyűjtése az adott dimenzió szerint 1 percenként összesített készletbe. Tárolja ezeket az értékeket. 
- A dimenzió összecsukása egy 1 perces összesített ÖSSZEGbe. Tárolja ezeket az értékeket. 

Tegyünk egy másik, NetworkAdapter nevű HTTP-hibát. Tegyük fel, hogy kiszolgálónként eltérő számú adapter van.

- Az A kiszolgáló 1 adapterrel rendelkezik
- A B kiszolgáló 2 adapterrel rendelkezik
- A C kiszolgáló 3 adapterrel rendelkezik

A következő tranzakciókra vonatkozó adatokat külön gyűjtjük. Ezek a következő jelöléssel lesznek megjelölve:

- Egy idő
- Egy érték
- Az a kiszolgáló, amelyről a tranzakció származik
- Az adapter, amelyből a tranzakció származik

Az alpercek mindegyike ezután összesítve lesz 1 perces idősorozat-értékekkel, és a Azure Monitor metrikus adatbázisban tárolódik:

- A kiszolgáló, 1. adapter
- B kiszolgáló, 1. adapter
- B kiszolgáló, 2. adapter
- C kiszolgáló, 1. adapter
- C kiszolgáló, 2. adapter
- C kiszolgáló, 3. adapter

Emellett a következő összecsukott összesítések is tárolhatók:

- A kiszolgáló, 1. adapter (mert nincs összeomlása, a rendszer ismét tárolja)
- B kiszolgáló, 1. és 2. adapter
- C kiszolgáló, adapter 1 + 2 + 3
- ÖSSZES kiszolgáló, adapterek

Ez azt mutatja, hogy a nagy mennyiségű dimenzióval rendelkező mérőszámok nagyobb számú összesítéssel rendelkeznek. Nem fontos, hogy megismerje az összes permutációt, csak Értse az okát. A rendszer az egyes diagramokon való hozzáférés gyors lekéréséhez az egyes adatokat és az összesített adatokat is szeretné tárolni. A rendszer a leginkább megfelelő tárolt összesítést vagy a mögöttes nyers adatokat választja, attól függően, hogy mit szeretne megjeleníteni. 

### <a name="aggregation-with-no-dimensions"></a>Összesítés dimenzió nélkül

Mivel ez a metrika egy dimenzió- *kiszolgálóval* rendelkezik, az a, B és C kiszolgáló mögöttes adatai megoszthatók a fentiekben leírtak szerint, a jelen cikk korábbi részében ismertetett módon. Ha a metrika nem rendelkezik a *kiszolgáló* dimenzióval, akkor az ügyfél csak a diagramon feketén látható összesített 1 perces összegekhez férhet hozzá. Ez a 3, 6, 6, 9 stb. érték A rendszerben az alapul szolgáló munkák nem fognak megjelenni a felosztott értékek összesítéséhez, hanem soha nem használják őket a metrika Explorerben, vagy a metrikák REST API keresztül küldik el őket. 

## <a name="viewing-time-granularities-above-1-minute"></a>Az idő részletességének megtekintése 1 percenként

Ha nagyobb részletességgel kéri a metrikákat, a rendszer az 1 percenként összesített összegeket használja az összegek kiszámításához a nagyobb időbeli részletesség érdekében.  Alább a pontozott vonalak az összegzési módszert mutatják a 2 és 5 perces részletességgel. Megint csak a SUM aggregációs típust láthatjuk az egyszerűség kedvéért.

:::image type="content" source="media/metrics-aggregation-explained/1-minute-to-2-min-5-min.png" alt-text="Képernyőfelvétel: több, a kiszolgáló dimenziójában összesen 1 percenként összesített bejegyzés, amely 2 – min és 5 perces időszakra van összesítve." border="false":::

2 perces részletességgel.

| Időszak       | Összegzi       
| -------------|-------------|  
| 1. perc & 2 | (3 + 6) = 9 |
| Perc 3 & 4 | (6 + 9) = 15|
| 4. perc & 5 | (4 + 5) = 9 |
| 6. perc & 7 | (7 + 1) = 8 |
| 8. perc & 9 | (6 + 3) = 9 |

5 perces részletességgel.

| Időszak              |            Összegzi        |
|---------------------|------------------------|  
| 1 – 5 perc  | 3 + 6 + 6 + 9 + 4 = 28 |
| 6 – 10 perc | 5 + 7 + 1 + 6 + 3 = 22 |

A rendszer a legjobb teljesítményt biztosító tárolt összesített adatokat használja. 

Alább látható a nagyobb diagram a fenti 1 percenkénti összesítési folyamathoz, és néhány nyíl kimarad az olvashatóság javítása érdekében.

:::image type="content" source="media/metrics-aggregation-explained/sum-aggregation-full.png" alt-text="A korábbi 3 képernyőképek összevonását ábrázoló képernyőkép. A kiszolgáló dimenzióján belül több, egyperces összesített bejegyzés 1 percenként, 2 percenként és 5 percenként. Az A, B és C kiszolgáló egyenként látható" border="false":::

## <a name="more-complex-example"></a>Összetettebb példa

A következő példában egy nagyobb példa található a HTTP-válaszidő (ezredmásodpercben) nevű fiktív metrika értékeit használva.  Itt további bonyolultsági szinteket mutatjuk be.

1. A Sum, a Count, a min és a Max összesítését, valamint az átlag számítását jelenítjük meg.
2. NULL értékeket látunk, és azt, hogy ezek milyen hatással vannak a számításokra. 

Gondolja át a következő példát. A mezők és a nyilak az értékek összesítésének és kiszámításának példáit mutatják be. 

Az előző szakaszban leírtak szerint az egyperces előösszesítési folyamat az összegeket, a darabszámot, a minimumot és a maximumot tartalmazza.  Az átlag azonban nincs előre összesítve. A számítási hibák elkerülése érdekében az összesített adatokat használva újraszámítjuk.
 
:::image type="content" source="media/metrics-aggregation-explained/full-aggregation-example-all-types.png" alt-text="Képernyőfelvétel: a Sum, a Count, a min, a Max és az Average összesítésének és kiszámításának összetett példája 1 perc és 10 perc között." border="false" lightbox="media/metrics-aggregation-explained/full-aggregation-example-all-types.png":::

A fentiekben látható 1 perces összesítéshez vegye figyelembe a 6. percet. Ez a perc az a pont, ahol a B kiszolgáló offline állapotba került, és leállította a jelentéskészítési adatgyűjtést, például újraindítás miatt. 

A fenti 6. percben a számított 1 percenkénti összesítési típusok a következők: 

| Összesítés típusa | Érték        | Jegyzetek |
|------------------|--------------|-------|
| Sum              | 53 + 20 = 73 | |
| Darabszám            | 2            | A NULL értékek hatását mutatja.  Az érték 3 lenne, ha a kiszolgáló online állapotban volt.  |
| Minimális          | 20           | |
| Maximum          | 53           | |
| Átlag          | 73/2       | Az összeg mindig a darabszám alapján oszlik meg. A rendszer soha nem tárolja és mindig újraszámítja a részletességet az összesített számok használatával. Figyelje meg az újraszámítást az 5 és 10 perces részletességgel, a fentiekben leírtak szerint. |

A piros szöveg színe olyan értékeket jelöl, amelyek a normál tartományból kivehetők, és azt szemléltetik, hogy miként történik a propagálás (vagy sikertelen), ahogy az idő részletessége leáll. Figyelje meg, hogy a *minimális* és a *maximális* érték azt jelzi, hogy a rendszer mögöttes rendellenességeket jelez, miközben az *átlag* és az *összegek* elveszítik az adatokat, ahogy az idő részletessége  

Azt is láthatja, hogy a NULLák nagyobb számítási átlagot biztosítanak, mint ha nullák lettek használva.  

> [!NOTE] 
> Bár ebben a példában nem ez a helyzet, a *Count* egyenlő az *összeggel* abban az esetben, ha egy metrikát mindig 1 értékkel rögzíti a rendszer. Ez akkor gyakori, ha egy metrika nyomon követi a tranzakciós események előfordulását – például a cikkben szereplő előző példában említett HTTP-hibák számát.

## <a name="next-steps"></a>Következő lépések

- [Első lépések a metrikák Explorerrel](../essentials/metrics-getting-started.md)
- [Speciális mérőszámok Explorer](../essentials/metrics-charts.md)
