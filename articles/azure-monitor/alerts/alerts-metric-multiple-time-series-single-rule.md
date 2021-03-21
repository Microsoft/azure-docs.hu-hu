---
title: Több idősorozat figyelése egyetlen metrikai riasztási szabályban
description: Nagy mennyiségű riasztás a több idősorozatra vonatkozó egyetlen riasztási szabály használatával
author: harelbr
ms.author: harelbr
ms.topic: conceptual
ms.date: 01/11/2021
ms.openlocfilehash: bc3e77b560a94b45acf1ae696551dbefbabbdf0a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102038203"
---
# <a name="monitor-multiple-time-series-in-a-single-metric-alert-rule"></a>Több idősorozat figyelése egyetlen metrikai riasztási szabályban

Egyetlen metrikai riasztási szabályt használhat egy vagy több metrikai idősorozat figyelésére, így könnyebben figyelheti az erőforrásokat a skálán.

## <a name="metric-time-series"></a>Metrika idősorozata

A metrika idősorozata egy adott időszakban rögzített mérési (vagy metrikus értékek) sorozat. 

Például:

- Virtuális gép CPU-kihasználtsága
- A beérkező (bejövő) bájtok egy Storage-fiókba
- Egy webalkalmazás sikertelen kéréseinek száma



## <a name="alert-rule-on-a-single-time-series"></a>Riasztási szabály egyetlen idősorozaton
A riasztási szabályok egyetlen idősorozatot figyelnek, ha az megfelel az alábbi feltételeknek:
-   A szabály egyetlen célként megadott erőforrást figyel 
-   Egyetlen feltételt tartalmaz
-   Kiértékel egy metrikát a méretek kiválasztása nélkül (feltéve, hogy a metrika támogatja a dimenziókat)

Példa egy ilyen riasztási szabályra (csak a vonatkozó tulajdonságok láthatók):
-   Cél erőforrás: *myVM1*
-   Metrika: *százalékos CPU*
-   Operátor: *nagyobb, mint*
-   Küszöbérték: *70*


Ebben a riasztási szabályban egyetlen metrikai idősorozat figyelhető meg:
-   Százalékos CPU, ahol *Resource*= ' myVM1 ' > 70%

![Egyetlen idősorozatra vonatkozó riasztási szabály](media/alerts-metric-multiple-time-series-single-rule/simple-alert-rule.png)

## <a name="alert-rule-on-multiple-time-series"></a>Riasztási szabály több idősorozatra
A riasztási szabályok több idősorozatot figyelnek, ha az a következő funkciók legalább egyikét használja: 
-   Több erőforrás
-   Több feltétel 
-   Több dimenzió


## <a name="multiple-resources-multi-resource"></a>Több erőforrás (több erőforrás)

Egyetlen metrikai riasztási szabály több erőforrás figyelésére is használható, ha az erőforrások ugyanolyan típusúak, és ugyanabban az Azure-régióban vannak. Az ilyen típusú szabályok csökkentik a bonyolultságot, és a megtartani kívánt riasztási szabályok teljes számát. 

Ilyen riasztási szabály például:
-   Cél erőforrás: *myVM1, myVM2*
-   Metrika: *százalékos CPU*
-   Operátor: *nagyobb, mint*
-   Küszöbérték: *70*

Ebben a riasztási szabályban két metrikai idősorozat figyelése külön történik:
-   Százalékos CPU, ahol *Resource*= ' myVM1 ' > 70%
-   Százalékos CPU, ahol *Resource*= ' myVM2 ' > 70%

![Több erőforrásból álló riasztási szabály](media/alerts-metric-multiple-time-series-single-rule/multi-resource-alert-rule.png)
 
Egy több erőforrásból álló riasztási szabályban a feltételt **külön** kell kiértékelni az egyes erőforrások esetében (vagy pontosabban, minden egyes metrikai idősorozat esetében). Ez azt jelenti, hogy a riasztásokat az egyes erőforrásokhoz is külön kell eldobni.

Tegyük fel például, hogy a fenti riasztási szabályt a 70% feletti CPU figyelésére állította be. A kiértékelt időszakban (azaz az utolsó 5 percben)
-   A *MyVM1* *százalékos CPU* -értéke nagyobb, mint 70% 
-   A *MyVM2* *százalékos CPU* -értéke 50%

A riasztási szabály aktiválja a *myVM1*, de nem *myVM2*. Ezek az aktivált riasztások függetlenek. Különböző időpontokban is feloldhatók, az egyes virtuális gépek egyedi viselkedése függvényében.

További információ a többerőforrásos riasztási szabályokról és az ehhez a képességhez támogatott erőforrás-típusokról: a [Azure monitor metrikus riasztások használatával történő monitorozás](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor).

> [!NOTE] 
> A több erőforrást figyelő metrikai riasztási szabályban csak egyetlen feltétel engedélyezett.

## <a name="multiple-conditions-multi-condition"></a>Több feltétel (több feltétel)

Egyetlen metrikai riasztási szabály riasztási szabályként legfeljebb öt feltételt képes figyelni. 

Például:

- Cél erőforrás: *myVM1*
- Condition1
  - Metrika: *százalékos CPU*
  - Operátor: *nagyobb, mint*
  - Küszöbérték: *70*
- Condition2
  - Metrika: *teljes hálózat*
  - Operátor: *nagyobb, mint*
  - Küszöbérték: *20 MB*

Ebben a riasztási szabályban két metrikai idősorozat figyelhető meg:

- Százalékos CPU, ahol *Resource*= ' myVM1 ' > 70%
- A hálózat, ahol az *erőforrás*= "myVM1" > 20 MB

![Több feltétel riasztási szabálya](media/alerts-metric-multiple-time-series-single-rule/multi-condition-alert-rule.png)
 
A feltételek között egy "AND" operátort használ a rendszer. A riasztási szabály riasztást küld, ha az **összes** feltétel teljesül. A kilőtt riasztás akkor oldódik fel, ha legalább az egyik feltétel már nem teljesül. 

> [!NOTE]
> Ha több feltételt tartalmazó riasztási szabályban dimenziókat használ, korlátozásokat kell használni. További információkért lásd: [korlátozások egy metrikus riasztási szabályban több feltételt használó dimenziók használata esetén](alerts-troubleshoot-metric.md#restrictions-when-using-dimensions-in-a-metric-alert-rule-with-multiple-conditions).


## <a name="multiple-dimensions-multi-dimension"></a>Több dimenzió (több dimenzió)

Egyetlen metrikai riasztási szabály egy metrika több dimenzió értékének figyelésére is képes. A metrika méretei olyan név-érték párok, amelyek további adatokat hordoznak a metrika értékének leírásához. Például egy Storage-fiók **tranzakciós** metrikájának van egy **API-neve** nevű dimenziója, amely az egyes tranzakciók által meghívott API nevét írja le (például GetBlob, DeleteBlob, PutPage). A méretek használata nem kötelező, de lehetővé teszi a metrika szűrését, és csak a megadott idősorozatok figyelését, a metrikát a teljes dimenziós értékek összesítése helyett. 

Dönthet például úgy, hogy riasztást küld, ha a tranzakciók száma magas az összes API-névvel (amely az összesített adatokat), vagy ha a tranzakciók száma magas az adott API-nevek esetében, csak akkor válik riasztásra. 

Egy riasztási szabály például több dimenzió figyelése:

- Cél erőforrás: *myStorage1*
- Metrika: *tranzakciók*
- Dimenziók
  * API-név = *GetBlob, DeleteBlob, PutPage*
- Operátor: *nagyobb, mint*
- Küszöbérték: *70*

Ebben a riasztási szabályban három metrikai idősorozat figyelhető meg:

- Tranzakciók, ahol *Resource*= "myStorage1" és *API Name*= "GetBlob" > 70
- Tranzakciók, ahol *Resource*= "myStorage1" és *API Name*= "DeleteBlob" > 70
- Tranzakciók, ahol *Resource*= "myStorage1" és *API Name*= "PutPage" > 70

![Többdimenziós riasztási szabály egy dimenzió értékeivel](media/alerts-metric-multiple-time-series-single-rule/multi-dimension-1.png)

A többdimenziós metrika riasztási szabályai több dimenzió értékét is megfigyelheti egy metrika **különböző** méreteiből. Ebben az esetben a riasztási szabály **külön** figyeli a kiválasztott dimenzió értékek összes dimenzió értékének kombinációját.

Ilyen típusú riasztási szabály például:

- Cél erőforrás: *myStorage1*
- Metrika: *tranzakciók*
- Dimenziók
  * API-név = *GetBlob, DeleteBlob, PutPage*
  * Hitelesítés = *sas, AccountKey*
- Operátor: *nagyobb, mint*
- Küszöbérték: *70*

Ebben a riasztási szabályban a hat metrikus idősorozat figyelése külön történik:

- Tranzakciók, ahol *Resource*= ' myStorage1 ' és *API Name*= ' GetBlob ' és *Authentication*= ' sas ' > 70
- Tranzakciók, ahol *Resource*= "myStorage1" és *API Name*= "GetBlob" és *Authentication*= "AccountKey" > 70
- Tranzakciók, ahol *Resource*= ' myStorage1 ' és *API Name*= ' DeleteBlob ' és *Authentication*= ' sas ' > 70
- Tranzakciók, ahol *Resource*= "myStorage1" és *API Name*= "DeleteBlob" és *Authentication*= "AccountKey" > 70
- Tranzakciók, ahol *Resource*= ' myStorage1 ' és *API Name*= ' PutPage ' és *Authentication*= ' sas ' > 70
- Tranzakciók, ahol *Resource*= "myStorage1" és *API Name*= "PutPage" és *Authentication*= "AccountKey" > 70

![Többdimenziós riasztási szabály több dimenzióból származó értékekkel](media/alerts-metric-multiple-time-series-single-rule/multi-dimension-2.png)
 
### <a name="advanced-multi-dimension-features"></a>Speciális többdimenziós funkciók

1.  Az **összes jelenlegi és jövőbeli dimenzió kijelölése** – megadhatja, hogy a dimenzió összes lehetséges értékét figyelje, beleértve a jövőbeli értékeket is. Az ilyen riasztási szabályok automatikusan méretezhetők a dimenzió összes értékének figyeléséhez anélkül, hogy a rendszer minden alkalommal módosítania kellene a dimenzió értékét, amikor hozzáadják vagy eltávolítottak.
2.  Dimenziók **kizárása** – a dimenzió értékének "≠" (kizárás) operátorának kiválasztásával egyenértékű a dimenzió összes többi értékének kiválasztásával, beleértve a jövőbeli értékeket is.
3.  **Új és egyéni dimenziók** – a Azure Portalban megjelenő dimenzió értékek az elmúlt nap során összegyűjtött metrikus adatokon alapulnak. Ha a keresett dimenzióérték még nincs kibocsátva, hozzáadhat egy egyéni dimenzió értékét.
4. **Méretek egyeztetése előtaggal** – a "Start with" operátor kiválasztásával és az Egyéni előtag megadásával megfigyelheti az összes olyan dimenzió értékét, amely egy adott mintával kezdődik.

![Speciális többdimenziós funkciók](media/alerts-metric-multiple-time-series-single-rule/advanced-features.png)


## <a name="metric-alerts-pricing"></a>Metrikus riasztások díjszabása

A metrikus riasztási szabályok díjszabása a [Azure monitor díjszabási oldalán](https://azure.microsoft.com/pricing/details/monitor/)érhető el.

Metrikus riasztási szabály létrehozásakor a megadott ár becslése a kiválasztott szolgáltatások és a figyelt idősorozatok száma alapján történik, amelyet a szabály konfigurációja és az aktuális metrika értékei határoznak meg. A havi díj azonban az idősorozat tényleges értékelésén alapul, és ezért eltérhet az eredeti becsléstől, ha egyes idősorozatok nem rendelkeznek a kiértékeléshez szükséges adatokkal, vagy ha a riasztási szabály olyan szolgáltatásokat használ, amelyek dinamikusan méretezhetővé tehetik.

A riasztási szabályok például magas árat mutatnak, ha kihasználja a többdimenziós funkciót, és nagy számú dimenzióérték-kombináció van kiválasztva, ami számos idősorozat figyelését eredményezi. Az adott riasztási szabályhoz tartozó tényleges díj azonban alacsonyabb lehet, ha a Dimension Values kombinációból származó idősorozatok közül nem mindegyiknek kell kiértékelnie az adatokat.

## <a name="number-of-time-series-monitored-by-a-single-alert-rule"></a>Egyetlen riasztási szabály által figyelt idősorozatok száma

A felesleges költségek elkerülése érdekében az egyes riasztási szabályok alapértelmezés szerint legfeljebb 5000 idősorozatot tudnak figyelni. Ha ezt a korlátot az előfizetésből kívánja emelni, nyisson meg egy támogatási jegyet.


## <a name="next-steps"></a>Következő lépések

További információ a nagy léptékű figyelésről metrikus riasztások és [dinamikus küszöbértékek](../alerts/alerts-dynamic-thresholds.md)használatával.
