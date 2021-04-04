---
title: Metrikák Advisor Szószedet
titleSuffix: Azure Cognitive Services
description: A metrikai tanácsadó szolgáltatás legfontosabb elképzelései és fogalmai
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: 6c461983053a145dfda58b9e3d26b39db0c339e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "92893420"
---
# <a name="metrics-advisor-glossary-of-common-vocabulary-and-concepts"></a>Metrikai tanácsadó Szószedet a gyakori szószedetekről és fogalmakról

Ez a dokumentum a metrikák Advisorban használt műszaki kifejezéseket ismerteti. Ebből a cikkből megtudhatja, milyen általános fogalmakkal és objektumokkal találkozhat a szolgáltatás használata során.

## <a name="data-feed"></a>Adatcsatorna

> [!NOTE]
> Több metrika is megoszthatja ugyanazt az adatforrást, és akár ugyanazzal az adatcsatornával is.

Az adatcsatorna az a mérőszám, amely az adatforrásból (például Cosmos DB vagy egy SQL Serverből) tölti le az adatokat. Az adatcsatorna sorait tartalmaz:
* időbélyegei
* nulla vagy több dimenzió
* egy vagy több mértéket. 

## <a name="metric"></a>Metric

A metrika egy számszerűsíthető mérték, amely egy adott üzleti folyamat állapotának figyelésére és értékelésére szolgál. Több idősorozat-érték kombinációját is kioszthatja dimenzióra. A *webes állapot* mérőszáma például a *felhasználók számának* és az *en-us piacnak* a dimenzióit is tartalmazhatja.

## <a name="dimension"></a>Dimenzió

Egy dimenzió egy vagy több kategorikus érték. Ezeknek az értékeknek a kombinációja egy adott univariate-idősorozatot azonosít, például: ország, nyelv, bérlő stb.

## <a name="multi-dimensional-metric"></a>Többdimenziós metrika

Mi az a többdimenziós metrika? Két példát használhat. 

**Üzleti bevétel**

Tegyük fel, hogy rendelkezik a vállalata bevételeinek adataival. Az idősorozat-adatai a következőhöz hasonló módon jelenhetnek meg:

| Időbélyeg | Kategória | Piaci | Bevétel |
| ----------|----------|--------|----- |
| 2020-6-1 | Élelmiszer | USA | 1000 |
| 2020-6-1 | Ruházat | USA | 2000 |
| 2020-6-2 | Élelmiszer | Egyesült Királyság | 800 | 
| ...      | ...  |... | ... |

Ebben a példában a *Kategória* és a *piac* dimenzió. A *bevétel* a fő TELJESÍTMÉNYMUTATÓ (KPI), amely különböző kategóriákba és/vagy piacokon is feldarabolható, és összesíthető is. Például az *élelmiszer* bevétele az összes piacon.
 
**Egy összetett alkalmazáshoz tartozó hibák száma**

Tegyük fel, hogy rendelkezik az alkalmazásban naplózott hibák számával. Az idősorozat-adatai a következőhöz hasonló módon jelenhetnek meg:

| Időbélyeg | Alkalmazás-összetevő | Region | Hibák száma |
| ----------|----------|--------|----- |
| 2020-6-1 | Alkalmazotti adatbázis | NYUGAT-EU | 9000 |
| 2020-6-1 | Üzenetsor | USA KELETI RÉGIÓJA | 1000 |
| 2020-6-2 | Üzenetsor | USA KELETI RÉGIÓJA | 8000| 
| ...      | ...           | ...     |  ...|

Ebben a példában az *alkalmazás-összetevő* és a *régió* dimenziók. A *hibák száma* az a KPI, amelyet különböző kategóriákba és/vagy piacokon lehet darabolni, és összesíthető is. Például az *üzenetsor* száma az összes régióban.

## <a name="measure"></a>Measure

A mérték egy alapvető vagy egység-specifikus kifejezés, valamint a metrika számszerűsíthető értéke.

## <a name="time-series"></a>idősorok

Az idősorozatok időbeli sorrendben indexelt adatpontok sorozata (vagy megjelenő vagy grafikonos). Az idősorozatok leggyakrabban az egymást követő, egyenletesen elhelyezkedő időpontok sorozata. Ez a különálló idejű adatmennyiség sorozata.

A metrikai tanácsadóban egy adott dimenzió kombináció egy mérőszámának értékeit egy adatsorozatnak nevezzük.

## <a name="granularity"></a>Részletesség

A részletesség azt jelzi, hogy az adatforráson milyen gyakori adatpontok lesznek létrehozva. Például naponta, óránként.

## <a name="start-time"></a>Kezdési idő

A kezdési idő az az idő, ameddig a metrikák tanácsadójának meg kell kezdenie az adatok adatforrásból való betöltését. Az adatforrásnak a megadott kezdési időpontban kell lennie az adataival.

## <a name="confidence-boundaries"></a>Megbízhatósági határok

> [!NOTE]
> A megbízhatósági határok nem az egyetlen, a rendellenességek megtalálására szolgáló mérés. Lehetséges, hogy a határon kívüli adatpontok az észlelési modell szerint a szokásos módon vannak megjelölve. 

A metrikai tanácsadóban a megbízhatósági határok a használt algoritmus érzékenységét jelentik, és a túlságosan érzékeny rendellenességek kiszűrésére szolgálnak. A webes portálon a megbízhatósági határok áttetsző kék sávokként jelennek meg. A sávon belüli összes pont normál pontként lesz kezelve.

A metrikai tanácsadó eszközöket biztosít a használt algoritmusok érzékenységének módosításához. További információkért lásd [: útmutató: mérőszámok konfigurálása és Finomhangolás észlelése konfiguráció](how-tos/configure-metrics.md) .

![Megbízhatósági határok](media/confidence-bounds.png)


## <a name="hook"></a>Horog

A metrikák tanácsadója lehetővé teszi a valós idejű riasztások létrehozását és előfizetését. Ezeket a riasztásokat a rendszer [Hook használatával](how-tos/alerts.md)küldi el az interneten keresztül.

## <a name="anomaly-incident"></a>Anomália incidens

Miután az észlelési konfigurációt alkalmazta a mérőszámokra, az incidensek akkor jönnek létre, amikor az összes sorozata anomáliával rendelkezik. Nagyméretű adatkészletek esetén ez túlterhelő lehet, így a metrikai tanácsadó a metrikán belül egy adott incidensbe csoportosítja a rendellenességek sorozatát. A szolgáltatás emellett kiértékeli a súlyosságot, és eszközöket biztosít [az incidens diagnosztizálásához](how-tos/diagnose-incident.md).

### <a name="incident-tree"></a>Incidens fája

A metrikai tanácsadóban alkalmazhat rendellenességek észlelését a mérőszámokon, majd a metrikai tanácsadó automatikusan figyeli az összes dimenzió-kombináció összes idősorát. Ha valamilyen rendellenesség észlelhető, a metrikai tanácsadó a rendellenességeket az incidensekre összesíti.
Az incidensek bekövetkezése után a metrikai tanácsadó az incidensek fáját a hozzájuk tartozó anomáliák hierarchiájában adja meg, és a legnagyobb hatással azonosítja azokat. Minden incidensnél van egy kiváltó ok rendellenessége, amely a fa legfelső csomópontja.

### <a name="anomaly-grouping"></a>Anomália csoportosítása

A metrikai tanácsadó lehetővé teszi a kapcsolódó idősorozatok hasonló mintázatú keresését. Mélyebb betekintést nyújt a más dimenziókkal kapcsolatos hatásokra, és korrelálhatja a rendellenességeket.

### <a name="time-series-comparison"></a>Idősorozat-összehasonlítás

Több idősorozatot is választhat, hogy egyetlen vizualizációban hasonlítsa össze a trendeket. Ez egyértelmű és éleslátó módszert biztosít a kapcsolódó adatsorozatok megtekintésére és összehasonlítására.

## <a name="detection-configuration"></a>Észlelési konfiguráció

>[!Note]
>Az észlelési konfigurációk csak egy adott metrikán belül alkalmazhatók.

A metrikai tanácsadó webportálon az észlelési konfiguráció (például az érzékenység, az automatikus késleltetés és az irány) a bal oldali panelen jelenik meg a metrika megtekintésekor. A paraméterek beállítható és alkalmazhatók a metrika összes adatsorozatára.

Minden idősorozathoz szükség van egy észlelési konfigurációra, amely meghatározza, hogy az idősorozat egy pontja anomália-e. A metrikai tanácsadó a teljes metrika alapértelmezett konfigurációját állítja be az adatok első bevezetéséhez. 

Emellett a konfigurációt úgy is pontosíthatja, hogy az adatsorozatok egy csoportjára hangolja be a paramétereket, vagy egy adott csoportot. Egy idősorozatra csak egy konfiguráció lesz alkalmazva:
* Egy adott adatsorozatra alkalmazott konfigurációk felülírják a csoportok konfigurációit
* A csoportok beállításai felülírják a teljes metrikára alkalmazott konfigurációkat.

A metrikai tanácsadó több [észlelési módszert](how-tos/configure-metrics.md#anomaly-detection-methods)is biztosít, amelyek logikai operátorokkal kombinálhatók.

### <a name="smart-detection"></a>Intelligens észlelés

Rendellenességek észlelése több gépi tanulási algoritmus használatával.

**Érzékenység**: numerikus érték, amely az anomália észlelésének tűréshatárát módosítja. Vizuálisan, minél nagyobb az érték, annál keskenyebb az idősorozatok körüli alsó és felső határ.

### <a name="hard-threshold"></a>Rögzített küszöbérték

Az alsó és felső határértékeken kívüli értékek anomáliák.

**Min**: az alsó határ

**Max**: a felső határ

### <a name="change-threshold"></a>Küszöbérték módosítása

Az előző pont értékének használatával állapítsa meg, hogy ez a pont anomáli-e.

**Változási arány**: az előző ponthoz képest az aktuális pont egy anomália, ha a változás százalékaránya meghaladja ezt a paramétert.

**Váltás a pontok** között: hány pontot kell visszakeresni.

### <a name="common-parameters"></a>Gyakori paraméterek

**Irány**: a pont csak akkor anomália, ha az eltérés a *felfelé*, *lefelé* vagy *mindkettő* irányban történik.

**Nem érvényes anomália, amíg**: egy adatpont csak anomália, ha az előző pontok megadott százaléka is anomália.

## <a name="alert-settings"></a>Riasztási beállítások

A riasztási beállítások határozzák meg, hogy mely rendellenességek esetén kell riasztást indítani. Több riasztást is beállíthat különböző beállításokkal. Létrehozhat például riasztást az alacsonyabb üzleti hatású anomáliák számára, és egy másikat a nagyobb jelentőségű riasztásokhoz.

Riasztást is létrehozhat a metrikák között. Például egy riasztás, amely csak akkor aktiválódik, ha két megadott metrika rendellenességet tartalmaz.

### <a name="alert-scope"></a>Riasztás hatóköre

A riasztás hatóköre arra a hatókörre vonatkozik, amelyre a riasztás vonatkozik. Négy lehetőség áll rendelkezésre:

Az **összes sorozat rendellenességei**: a rendszer a metrikán belüli összes sorozatban rendellenességeket indít el.

**Adatsorozat-csoportba tartozó rendellenességek**: a riasztások csak az adatsorozat-csoport adott méreteiben észlelt rendellenességek esetén lesznek aktiválva. A megadott méretek számának kisebbnek kell lennie az összesített szám dimenziónál.

**Rendellenességek a kedvenc sorozatban**: a riasztások csak a kedvencekként hozzáadott rendellenességek esetén lesznek aktiválva. Az egyes észlelési konfigurációkhoz kedvencként is kiválaszthatja az adatsorozatok egy csoportját.

Az **összes sorozat legfontosabb n-ban észlelt rendellenességek**: a riasztások csak a legfontosabb n sorozatbeli rendellenességek esetén aktiválódnak. A paraméterek beállításával megadhatja, hogy hány időbélyeget szeretne figyelembe venni, és hogy hány rendellenességnek kell szerepelnie a riasztás elküldéséhez.

### <a name="severity"></a>Súlyosság

A súlyossági szint szerint a metrikák tanácsadója az incidens súlyosságának leírására használja, beleértve a *magas*, *közepes* és *alacsony* értéket.

A metrikai tanácsadó jelenleg a következő tényezőket használja a riasztás súlyosságának méréséhez:
1. A mérőszámban található anomáliák értékének és mennyiségének aránya.
1. A rendellenességek megbízhatósága.
1. A kedvenc beállításai is hozzájárulnak a súlyossághoz.

### <a name="auto-snooze"></a>Automatikus késleltetés

Bizonyos anomáliák átmeneti problémák, különösen a kis részletességű mérőszámok esetében. Adott számú időpontra vonatkozóan riasztást *adhat meg* . Ha a megadott számú pontban rendellenességek találhatók, a rendszer nem indít riasztást. Az automatikus késleltetés viselkedése beállítható metrikus vagy adatsorozati szinten is.

A késleltetési mód a metrika szintjén vagy a sorozat szintjén állítható be.

## <a name="data-feed-settings"></a>Adatcsatorna beállításai

### <a name="ingestion-time-offset"></a>Betöltési idő eltolása

Alapértelmezés szerint az adatokat a részletesség (például *naponta*) alapján tölti be a rendszer. Pozitív egész szám használatával késleltetheti az adatbevitelt a megadott értékkel. Negatív szám használatával előre elvégezheti a betöltést a megadott értékkel.

### <a name="max-ingestion-per-minute"></a>Maximális betöltés percenként

Akkor adja meg ezt a paramétert, ha az adatforrás támogatja a korlátozott párhuzamosságot. Ellenkező esetben hagyja meg az alapértelmezett beállításokat.

### <a name="stop-retry-after"></a>Újrapróbálkozás befejezése után

Ha az adatok betöltése nem sikerült, a metrikák Advisor egy adott idő elteltével automatikusan újrapróbálkozik. Az időszak kezdete az az idő, amikor az első adatfeldolgozás történt. Az újrapróbálkozási időszak hossza a részletességnek megfelelően van definiálva. Ha az alapértelmezett értéket () használja `-1` , az újrapróbálkozási időszak a részletesség alapján lesz meghatározva:

| Részletesség       | Újrapróbálkozás befejezése után           |
| :------------ | :--------------- |
| Napi, egyéni (>= 1 nap), hetente, havonta, évente     | 7 nap          |
| Óránként, egyéni (< 1 nap)       | 72 óra |

### <a name="min-retry-interval"></a>Minimális újrapróbálkozási időköz

Megadhatja azt a minimális időtartamot, amikor az adatok a forrásból való lekérése megtörtént. Ha az alapértelmezett értéket () használja `-1` , az újrapróbálkozási időköz a részletesség alapján lesz meghatározva:

| Részletesség       | Minimális újrapróbálkozási időköz           |
| :------------ | :--------------- |
| Napi, egyéni (>= 1 nap), hetente, havonta     | 30 perc          |
| Óránként, egyéni (< 1 nap)      | 10 perc |
| Éves | 1 nap          |

### <a name="grace-period"></a>Türelmi időszak

> [!Note]
> A türelmi időszak a normál betöltési idő és a megadott betöltési idő eltolása szerint kezdődik.
    
A türelmi időszak olyan időszak, amelyben a metrikai tanácsadó továbbra is beolvassa az adatokat az adatforrásból, a riasztásokat azonban nem. Ha a türelmi időszak után nem töltöttek fel adatot, az *adatcsatorna nem érhető el* riasztást fog kiváltani.

### <a name="snooze-alerts-in"></a>Riasztások késleltetése a-ben

Ha ez a beállítás nulla értékűre van állítva, akkor a *nem elérhető* időbélyegek riasztást indítanak. Ha nullától eltérő értékre van állítva, a rendszer a megadott számú *nem elérhető* riasztást késlelteti, ha nem lettek beolvasva.

## <a name="data-feed-permissions"></a>Adatcsatorna engedélyei

Az adatcsatorna-engedélyek kezelésének két szerepköre van: a *rendszergazda* és a *megjelenítő*. 

* A *rendszergazda* teljes mértékben felügyeli az adatcsatornákat és a metrikákat. Aktiválhatja, szüneteltetheti, törölheti az adatcsatornát, és frissítéseket készíthet a hírcsatornák és konfigurációk számára. A *rendszergazdák* általában a metrikák tulajdonosai.

* A *megjelenítők* megtekinthetik az adatcsatornákat vagy a metrikákat, de nem módosíthatják azokat. 

## <a name="next-steps"></a>Következő lépések
- [A metrikai tanácsadó áttekintése](overview.md)
- [A webes portál használata](quickstarts/web-portal.md)