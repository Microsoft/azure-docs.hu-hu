---
title: Riasztások naplózása Azure Monitor
description: E-mailek, értesítések, a webhelyek URL-címeinek (webhookok), vagy az automatizálás elindítása a megadott naplózási lekérdezési feltétel teljesülése esetén
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/22/2020
ms.subservice: alerts
ms.openlocfilehash: 5e7909725f5e390f4e42a7d62e80f90f897c840f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101714153"
---
# <a name="log-alerts-in-azure-monitor"></a>Riasztások naplózása Azure Monitor

## <a name="overview"></a>Áttekintés

A naplózási riasztások az [Azure-riasztások](./alerts-overview.md)által támogatott riasztási típusok egyike. A riasztások naplózása lehetővé teszi, hogy a felhasználók egy [log Analytics](../logs/log-analytics-tutorial.md) lekérdezést használjanak, amellyel kiértékelheti az erőforrások összes készletének gyakoriságát, és egy riasztást az eredmények alapján. A szabályok egy vagy több műveletet aktiválnak a [műveleti csoportok](./action-groups.md)használatával.

> [!NOTE]
> [Log Analytics munkaterületről](../logs/log-analytics-tutorial.md) származó adatok naplózása elküldhető a Azure monitor metrikák tárolójába. A metrikák riasztásai [eltérő viselkedéssel](alerts-metric-overview.md)rendelkeznek, ami kívánatosabb lehet a használt adatoktól függően. A naplók mérőszámokra való átirányításával kapcsolatos információkért lásd: [metrika riasztás a naplókhoz](alerts-metric-logs.md).

> [!NOTE]
> Az API `2020-05-01-preview` -verzió és az erőforrás-központú naplózási riasztások esetében jelenleg nem számítunk fel további díjakat.  Az előzetes verzióban elérhető szolgáltatások díjszabása a jövőben lesz bejelentve, és a számlázás megkezdése előtt értesítést küld. Ha továbbra is az új API-verziót és az erőforrás-központú napló riasztásait szeretné használni a hirdetmény lejárta után, akkor a megfelelő díjszabás alapján számítjuk fel a díjat.

## <a name="prerequisites"></a>Előfeltételek

A naplózási riasztások Log Analytics-adatlekérdezéseket futtatnak. Először el kell kezdenie a [naplózási adatok gyűjtését](../essentials/resource-logs.md) és a naplózási adatok lekérdezését a hibákért. A Log Analytics a [riasztások lekérdezése témakört](../logs/example-queries.md) használhatja, amelyből megismerheti, hogy miként derítheti fel vagy kezdheti [meg a saját lekérdezések írását](../logs/log-analytics-tutorial.md).

Az [Azure monitoring közreműködője](../roles-permissions-security.md) a naplók létrehozásához, módosításához és frissítéséhez szükséges általános szerepkör. Az erőforrás-naplókhoz való hozzáférés & a lekérdezés végrehajtási jogosultságai is szükségesek. Az erőforrás-naplók részleges hozzáférése sikertelen lekérdezésekhez vagy részleges eredmények visszaadásához vezethet. [További információ a naplózási riasztások konfigurálásáról az Azure-ban](./alerts-log.md).

> [!NOTE]
> A régi [log Analytics riasztási API](./api-alerts.md)-val felügyelhető log Analytics naplózási riasztásai. [További információ az aktuális SCHEDULEDQUERYRULES API-ra való áttérésről](../alerts/alerts-log-api-switch.md).

## <a name="query-evaluation-definition"></a>Lekérdezés értékelésének definíciója

A naplóbeli keresési szabályok feltétel-definíciója a következőtől kezdődik:

- Milyen lekérdezést kell futtatni?
- Az eredmények használata

A következő szakaszok ismertetik a fenti logika beállításához használható különböző paramétereket.

### <a name="log-query"></a>Napló lekérdezése
A szabály kiértékeléséhez használt [log Analytics](../logs/log-analytics-tutorial.md) lekérdezés. A lekérdezés által visszaadott eredmények alapján megállapítható, hogy egy riasztás aktiválva van-e. A lekérdezés hatóköre a következő lehet:

- Egy adott erőforrás, például egy virtuális gép.
- Méretezési erőforrás, például előfizetés vagy erőforráscsoport.
- Több erőforrás több [erőforrást használó lekérdezéssel](../logs/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights). 
 
> [!IMPORTANT]
> A riasztási lekérdezések megkötésekkel biztosítják az optimális teljesítményt és az eredmények helytállóságát. [További információt itt talál](./alerts-log-query.md).

> [!IMPORTANT]
> Az erőforrás-központú és az [erőforrások közötti lekérdezés](../logs/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) csak a jelenlegi scheduledQueryRules API használatával támogatott. Ha az örökölt [log Analytics riasztási API](./api-alerts.md)-t használja, váltania kell. [További információ a váltásról](./alerts-log-api-switch.md)

#### <a name="query-time-range"></a>Lekérdezés időtartománya

Az időtartomány a szabály feltételének definíciójában van beállítva. A munkaterületek és a Application Insights a neve **pont**. Az összes többi erőforrástípus esetében a **lekérdezés időtartományának felülbírálását** nevezzük.

A log analyticshez hasonlóan az időtartomány is a megadott tartományra korlátozza a lekérdezési időt. Akkor is alkalmazza az időtartományt **, ha a** lekérdezésben már szerepel a futtatási parancs.

Egy lekérdezés például 60 percet vizsgál, ha az időtartomány 60 perc, még akkor is, ha a szöveg már tartalmazza a **következőt: ago (1d)**. Az időtartománynak és a lekérdezési idő szűrésének meg kell egyeznie. A példában az **adott időszakra** vonatkozó  /  **felülbírálás lekérdezési időtartománya** egy napra változik, a várt módon fog működni.

### <a name="measure"></a>Measure

A naplózási riasztások olyan numerikus értékekre jelentkeznek be, amelyek kiértékelése lehetséges. Két különböző dolgot mérhető fel:

#### <a name="count-of-the-results-table-rows"></a>Az eredmények tábla sorainak száma

Az eredmények száma az alapértelmezett mérték. Ideális megoldás az olyan események, mint például a Windows-eseménynaplók, a syslog és az alkalmazás-kivételek használatához. Akkor aktiválódik, ha a naplózási rekordok történnek, vagy nem történnek a kiértékelt idő ablakban.

A naplózási riasztások akkor működnek a legjobban, ha a naplóban lévő adatészlelési kísérletet végez. Kevésbé jól működik, ha a naplókban lévő adathiány észlelését kísérli meg. Például a virtuális gép szívverésének riasztása.

Munkaterületek és Application Insights esetén a rendszer a kiválasztott **számú találatok** **alapján** hívja meg. Az összes többi erőforrástípus esetében ez a **mérték** a kiválasztási **táblázat soraival** együtt.

> [!NOTE]
> Mivel a naplók részben strukturált adatok, eredendően látensek, mint a metrikák, előfordulhat, hogy a rendszer a naplókban észlelt adatok hiányának észlelésére tett kísérlet során a [metrikai riasztásokat](alerts-metric-overview.md)használja. Adatokat küldhet a metrikai tárolóba a naplók [metrikai riasztásai](alerts-metric-logs.md)használatával.

##### <a name="example-of-results-table-rows-count-use-case"></a>Példa az eredmények tábla sorok száma használati esetére

Tudni szeretné, hogy az alkalmazás az 500-es hibakód miatt (belső kiszolgálóhiba) válaszol-e. Hozzon létre egy riasztási szabályt a következő részletekkel:

- **Lekérdezés** 

```Kusto
requests
| where resultCode == "500"
```

- Időtartam **/Összesítés részletessége:** 15 perc
- **Riasztás gyakorisága:** 15 perc
- **Küszöbérték:** Nagyobb, mint 0

Ezt követően a riasztási szabályok figyelik az 500 hibakódú kérelmeket. A lekérdezés 15 percenként fut az elmúlt 15 percben. Ha még egy rekordot talál, a riasztást küld, és elindítja a konfigurált műveleteket.

#### <a name="calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value"></a>Mérték kiszámítása numerikus oszlop alapján (például CPU-számláló értéke)

Munkaterületek és Application Insights esetén a **rendszer** a kiválasztási **metrika mértékének** megfelelően hívja meg. Az összes többi erőforrástípus esetében ez a **mérték** a szám oszlop nevének kiválasztásával történik.

### <a name="aggregation-type"></a>Összesítés típusa

A több rekordon elvégzett számítások egy numerikus értékre összesítve. Például:
- A **Count** a lekérdezésben lévő rekordok számát adja vissza.
- Az **átlagos** érték a mérték oszlop [**összesítési részletességének**](#aggregation-granularity) átlagát adja meg.

A munkaterületek és a Application Insights esetében ez csak **metrikus mérési** mértékekben támogatott. A lekérdezés eredményének tartalmaznia kell egy AggregatedValue nevű oszlopot, amely numerikus értéket biztosít a felhasználó által definiált összesítés után. Minden más erőforrástípus esetében az **összesítési típus** van kiválasztva az adott név mezőjéből.

### <a name="aggregation-granularity"></a>Összesítés részletessége

Meghatározza a több rekord egy numerikus értékre való összesítéséhez használt időközt. Ha például **5 percet** adott meg, a rekordok 5 percenként lesznek csoportosítva a megadott **összesítési típus** használatával.

A munkaterületek és a Application Insights esetében ez csak **metrikus mérési** mértékekben támogatott. A lekérdezés eredményének tartalmaznia kell egy [bin ()](/azure/kusto/query/binfunction) értéket, amely beállítja az intervallumot a lekérdezés eredményei között. Az összes többi erőforrástípus esetében az ezt a beállítást vezérlő mezőt **aggregációs részletességnek** nevezzük.

> [!NOTE]
> Mivel a [bin ()](/azure/kusto/query/binfunction) nem egyenletes időintervallumot eredményezhet, a riasztási szolgáltatás automatikusan átalakítja a [bin ()](/azure/kusto/query/binfunction) függvényt, hogy [bin_at ()](/azure/kusto/query/binatfunction) függvényt a megfelelő időpontra konvertálja, hogy az eredmények egy rögzített ponttal legyenek biztosítva.

### <a name="split-by-alert-dimensions"></a>Felosztás riasztási méretek alapján

A riasztások szám vagy karakterlánc típusú oszlopokból való felosztása különálló riasztásokra az egyedi kombinációk csoportosításával. Az erőforrás-központú riasztások méretezéskor (előfizetés vagy erőforráscsoport-hatókör) való létrehozásakor az Azure erőforrás-azonosító oszlopával lehet felosztani. Az Azure Resource ID oszlop felosztása megváltoztatja a riasztás célját a megadott erőforrásra.

Az Azure Resource ID oszlop szerinti felosztás ajánlott, ha ugyanazon feltételt szeretné figyelni több Azure-erőforráson. Például az összes virtuális gép figyelése a CPU használatára 80% felett. Dönthet úgy is, hogy nem bontja a felosztást, ha a hatókörben több erőforrásra vonatkozó feltételt szeretne használni, például a figyelést, hogy az erőforráscsoport hatókörében legalább öt gépen van CPU-használat a 80%-nál.

A munkaterületek és a Application Insights esetében ez csak **metrikus mérési** mértékekben támogatott. A mező neve **Összesítés**. Legfeljebb három oszlop lehet. A lekérdezés oszlopainak több mint három csoportja nem várt eredményekhez vezethet. Az összes többi erőforrástípus esetében a feltétel **felosztási dimenziók** szakasza (legfeljebb hat felosztás) van konfigurálva.

#### <a name="example-of-splitting-by-alert-dimensions"></a>Példa riasztási dimenziók szerinti felosztásra

Például a webhelyet vagy alkalmazást futtató több virtuális gép hibáit szeretné figyelni egy adott erőforráscsoport esetében. A naplózási riasztási szabályt a következőképpen teheti meg:

- **Lekérdezés** 

    ```Kusto
    // Reported errors
    union Event, Syslog // Event table stores Windows event records, Syslog stores Linux records
    | where EventLevelName == "Error" // EventLevelName is used in the Event (Windows) records
    or SeverityLevel== "err" // SeverityLevel is used in Syslog (Linux) records
    ```

    Munkaterületek és Application Insights **metrikus mérési** riasztási logikával való használatakor ezt a sort fel kell venni a lekérdezési szövegbe:

    ```Kusto
    | summarize AggregatedValue = count() by Computer, bin(TimeGenerated, 15m)
    ```

- **Erőforrás-azonosító oszlop:** _ResourceId (a riasztási szabályok erőforrás-azonosító oszlopának felosztása csak az előfizetések és az erőforráscsoportok esetében érhető el)
- **Méretek/összesítve:**
  - Computer = VM1, VM2 (a riasztási szabályok definíciójában lévő szűrési értékek jelenleg nem érhetők el munkaterületekhez és Application Insightsokhoz. Szűrés a lekérdezési szövegben.)
- Időtartam **/Összesítés részletessége:** 15 perc
- **Riasztás gyakorisága:** 15 perc
- **Küszöbérték:** Nagyobb, mint 0

Ez a szabály figyeli, hogy a virtuális gép az elmúlt 15 percben észlelt-e hibás eseményeket. Az egyes virtuális gépeket külön kell figyelni, és egyenként kell elindítani a műveleteket.

> [!NOTE]
> A riasztási dimenziók felosztása csak az aktuális scheduledQueryRules API esetében érhető el. Ha az örökölt [log Analytics riasztási API](./api-alerts.md)-t használja, váltania kell. [További információ a váltásról](./alerts-log-api-switch.md). Az erőforrás-központú riasztások méretezése csak az API-verzióban `2020-05-01-preview` és a fentiekben támogatott.

## <a name="alert-logic-definition"></a>Riasztási logika definíciója

Miután megtörtént a lekérdezés futtatása és az eredmények kiértékelése, meg kell határoznia a riasztási logikát és a műveletek menetét. A következő szakaszok ismertetik a különböző használható paramétereket:

### <a name="threshold-and-operator"></a>Küszöbérték és operátor

A lekérdezés eredményei egy számra vannak átalakítva, amely a küszöbértékhez és a kezelőhöz hasonlít.

### <a name="frequency"></a>Gyakoriság

A lekérdezés futtatásának időköze. 5 perctől akár egy napig is beállítható. A naplózási rekordok kihagyása érdekében a [lekérdezési időtartománynak](#query-time-range) meg kell egyeznie vagy annál kisebbnek kell lennie.

Ha például az időtartamot 30 percre állítja be, és a gyakoriságot 1 óráig adja meg.  Ha a lekérdezés a 00:00-es verzióban fut, akkor a a 23:30 és 00:00 közötti rekordokat adja vissza. A lekérdezés következő futtatása a 01:00, amely a 00:30 és 01:00 közötti rekordokat adja vissza. A 00:00 és 00:30 között létrehozott rekordok soha nem lesznek kiértékelve.

### <a name="number-of-violations-to-trigger-alert"></a>Riasztást kiváltó szabálysértések száma

Megadhatja a riasztás kiértékelésének időtartamát és a riasztások elindításához szükséges hibák számát. Lehetővé teszi a riasztások kiváltásának időpontjának jobb meghatározását. 

Ha például a szabály [**összesítési részletessége**](#aggregation-granularity) "5 perc", akkor a riasztás csak akkor aktiválható, ha az elmúlt egy órában három hiba (15 perc) történt. Ezt a beállítást az alkalmazás üzleti szabályzata határozza meg.

## <a name="state-and-resolving-alerts"></a>Riasztások állapota és feloldása

A naplózási riasztások állapot nélküliek. Riasztások tűz a feltétel teljesülése esetén, még akkor is, ha korábban kirúgták. A felgyújtott riasztások nem oldhatók fel. [A riasztást lezártként jelölheti](../alerts/alerts-managing-alert-states.md)meg. A műveletek elnémításával megakadályozhatja, hogy a riasztási szabály elindítását követően egy adott időszakban aktiválják őket.

A munkaterületek és a Application Insights esetében ez a **riasztások letiltása**. Az összes többi erőforrástípus esetében az **Elnémítás műveletnek** nevezzük. 

Tekintse meg ezt a riasztási kiértékelési példát:

| Idő    | Naplózási feltétel kiértékelése | Eredmény 
| ------- | ----------| ----------| ------- 
| 00:05 | HAMIS | A riasztás nem tűz. Nincs hívott művelet.
| 00:10 | IGAZ  | Riasztási tüzek és műveleti csoportok hívása. Új riasztási állapot aktív.
| 00:15 | IGAZ  | Riasztási tüzek és műveleti csoportok hívása. Új riasztási állapot aktív.
| 00:20 | HAMIS | A riasztás nem tűz. Nincs hívott művelet. A áteresztő-riasztások állapota aktív marad.

## <a name="pricing-and-billing-of-log-alerts"></a>A naplózási riasztások díjszabása és számlázása

A díjszabási információk a [Azure monitor díjszabási oldalán](https://azure.microsoft.com/pricing/details/monitor/)találhatók. A naplózási riasztások az erőforrás-szolgáltató területen találhatók `microsoft.insights/scheduledqueryrules` :

- Naplózási riasztások Application Insights látható az erőforrás neve és az erőforráscsoport és a riasztás tulajdonságai között.
- Naplózási riasztások Log Analytics látható az erőforrás neve és az erőforráscsoport és a riasztás tulajdonságai között. [SCHEDULEDQUERYRULES API](/rest/api/monitor/scheduledqueryrules)használatával történő létrehozáskor.
- A [régi log Analytics API](./api-alerts.md) -ból létrehozott naplózási riasztások nem követik nyomon az [Azure-erőforrásokat](../../azure-resource-manager/management/overview.md) , és nincsenek kényszerített egyedi erőforrásnevek. Ezek a riasztások továbbra is `microsoft.insights/scheduledqueryrules` rejtett erőforrásként jönnek létre, amelyek ezt az erőforrás-elnevezési struktúrát használják `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` . Az örökölt API-k naplófájljai a rejtett erőforrás neve mellett az erőforráscsoport és a riasztás tulajdonságai között jelennek meg.

> [!NOTE]
> A nem támogatott erőforrás-karakterek, például a `<, >, %, &, \, ?, /` rejtett erőforrás-nevekben vannak lecserélve, `_` és ez a számlázási adatokban is szerepelni fog.

> [!NOTE]
> Az örökölt [log Analytics riasztási API](./api-alerts.md) -val és a [log Analytics mentett keresések és riasztások](../insights/solutions.md)örökölt sablonjaival felügyelhető log Analytics naplózási riasztásai. [További információ az aktuális SCHEDULEDQUERYRULES API-ra való áttérésről](../alerts/alerts-log-api-switch.md). A riasztási szabályok kezelését a [régi log Analytics API](./api-alerts.md) használatával kell elvégezni, amíg nem dönt, és nem tudja használni a rejtett erőforrásokat.

## <a name="next-steps"></a>Következő lépések

* Ismerje meg [, hogyan hozhat létre naplóbeli riasztásokat az Azure](./alerts-log.md)-ban.
* Ismerkedjen meg [a webhookokkal a log-riasztásokban az Azure-ban](../alerts/alerts-log-webhook.md).
* Ismerje meg az [Azure-riasztásokat](./alerts-overview.md).
* További információ a [log Analyticsról](../logs/log-query-overview.md).