---
title: Azure-beli számítási feladatok optimalizálása az Advisor score használatával
description: Azure Advisor pontszám használatával a legtöbbet hozhatja ki az Azure-ban.
ms.topic: article
ms.date: 09/09/2020
ms.openlocfilehash: 11b20bc3b4d604d3a7ff4608cd1c21f129c1cb6d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97630122"
---
# <a name="optimize-azure-workloads-by-using-advisor-score"></a>Azure-beli számítási feladatok optimalizálása az Advisor score használatával

## <a name="introduction-to-advisor-score"></a>Az Advisor pontszám bemutatása

Azure Advisor a számítási feladatokra vonatkozó ajánlott eljárásokat nyújt. Ezek a javaslatok személyre szabottak, és a következőkhöz nyújtanak segítséget:

* Javítsa ki a számítási feladatok testtartását, és optimalizálja az Azure-beli üzemelő példányait.
* Az ajánlott eljárások követésével proaktív módon megelőzheti a leggyakoribb problémákat.
* Mérje fel Azure-beli számítási feladatait a [Microsoft Azure Well-Architected-keretrendszer](/azure/architecture/framework/)öt pillérére.

Az Advisor alapszolgáltatása az Advisor pontszáma révén hatékonyan és hatékonyan érheti el ezeket a célokat.

Ahhoz, hogy a legtöbbet hozza ki az Azure-ból, fontos tisztában lennie azzal, hogy hol található a számítási feladatok optimalizálási útja. Tudnia kell, hogy mely szolgáltatásokat és erőforrásokat kell jól felhasználni, és melyek nem. Emellett érdemes tudnia, hogyan rangsorolhatja a műveleteit a javaslatok alapján, hogy maximalizálja a végeredményt.

Fontos, hogy nyomon kövesse és jelentse a folyamat előrehaladását ebben az optimalizálási úton. Az Advisor pontszám használatával egyszerűen elvégezheti ezeket a dolgokat az új gamification-felülettel.

Személyre szabott felhőalapú tanácsadóként Azure Advisor folyamatosan vizsgálja a használat telemetria és az erőforrás-konfigurációt az iparági ajánlott eljárások ellenőrzése érdekében. Az Advisor ezután egyetlen pontszámban összesíti az eredményeket. Ebben a pontszámban egy pillantással megtudhatja, ha a szükséges lépéseket a megbízható, biztonságos és költséghatékony megoldások létrehozásához használja.

Az Advisor pontszáma egy összesített pontszámból áll, amely tovább bontható öt kategória pontszámával. Az Advisor minden kategóriájának egyik pontszáma a Well-Architected keretrendszer öt pillérét jelöli.

A napi, heti és havi trendeket megtekintve nyomon követheti a folyamat előrehaladását. A célkitűzések eléréséhez a teljesítménytesztek is megadhatók.

 ![Képernyőkép az Advisor score oldaláról.](./media/advisor-score-1.png)

## <a name="interpret-an-advisor-score"></a>Advisor-pontszám értelmezése

Az Advisor a teljes Advisor pontszámát és az Advisor-kategóriák részletezését jeleníti meg százalékban kifejezve. Bármely kategória 100%-os pontszáma azt jelenti, hogy az Advisor által értékelt összes erőforrás az Advisor által javasolt ajánlott eljárásokat követi. A spektrum másik végén a 0%-os pontszám azt jelenti, hogy az Advisor által értékelt egyik erőforrás sem követi az Advisor javaslatait. Ezeknek a pontszámoknak a használatával könnyedén elérheti a következő folyamatot:

* Az **Advisor score** segítségével elsajátíthatja, hogy a számítási feladatok vagy előfizetések hogyan futnak az Advisor pontszáma alapján. Megtekintheti a korábbi trendeket is, hogy megtudja, mi a trend.
* Az egyes javaslatok **kategóriánkénti pontszáma** azt mutatja be, hogy mely Kiemelt javaslatok javítják a pontszámokat. Ezek az értékek mind a javaslat súlyozását, mind pedig az előre jelzett egyszerű megvalósítást tükrözik. Ezek a tényezők segítenek meggyőződni arról, hogy a legtöbb értéket lekérheti az idejével. Emellett a rangsorolással is segítenek.
* Az egyes javaslatokra vonatkozó **Kategória pontszáma** az egyes kategóriákhoz tartozó szervizelési műveletek rangsorolását segíti.

Az egyes javaslatok kategóriához való hozzájárulása egyértelműen látható az Azure Portal **Advisor pontszám** lapján. Az egyes kategóriák pontszámát a **potenciális pontszám növekedése** oszlopban szereplő százalékpontok alapján növelheti. Ez az érték a kategórián belüli javaslat súlyát és a várható egyszerű megvalósítást tükrözi a lehetséges legkönnyebb feladatok megoldásához. A legjobb pontszámot érintő javaslatokra koncentrálva a lehető leghatékonyabban hozhatja ki az időt.

![Az Advisor pontszámának hatását bemutató képernyőkép.](./media/advisor-score-2.png)

Ha az Advisor egyik javaslata sem vonatkozik egy adott erőforrásra, elhalaszthatja vagy elvetheti ezeket az ajánlásokat. A következő frissítéssel kizárjuk a pontszám számítását. Az Advisor további Visszajelzésként is ezt a bemenetet fogja használni a modell tökéletesítéséhez.

## <a name="how-is-an-advisor-score-calculated"></a>Hogyan számítják ki az Advisor pontszámát?

Az Advisor megjeleníti a kategória pontszámait és a teljes tanácsadói pontszámot százalékban kifejezve. Bármely kategóriában a 100%-os pontszám azt jelenti, hogy az *Advisor által értékelt* összes erőforrást az Advisor által javasolt ajánlott eljárások követik. A spektrum másik végén a 0%-os pontszám azt jelenti, hogy az Advisor által értékelt források egyike sem követi az Advisor javaslatait.

**Az öt kategória közül mind a legmagasabb lehetséges pontszáma 100.** Az Advisor összesített pontszámát a rendszer az összes vonatkozó kategória pontszáma alapján számítja ki, és az összes vonatkozó kategória közül a legmagasabb lehetséges pontszám összege alapján oszlik meg. A legtöbb előfizetés esetében az Advisor hozzáadja a pontszámot az egyes kategóriákhoz, és a 500-as megosztást. A *Kategóriák pontszámát azonban csak akkor számítjuk ki, ha az Advisor által értékelt erőforrásokat használ*.

### <a name="advisor-score-calculation-example"></a>Példa az Advisor pontszámának számítására

* **Egyszeri előfizetés pontszáma:** Ez a példa az előfizetéshez tartozó összes Advisor kategória pontszámának egyszerű jelentése. Ha az Advisor kategória pontszámai: **Cost** = 73, **megbízhatóság** = 85, **működési kiválóság** = 77 és **teljesítmény** = 100, az Advisor pontszáma a következő: (73 + 85 + 77 + 100)/(4x100) = 0,84% vagy 84%.
* **Több előfizetés pontszáma:** Ha több előfizetés van kiválasztva, a teljes Advisor-pontszámok súlyozott összesített kategóriába tartoznak. Itt minden Advisor-kategória pontszáma az előfizetések által felhasznált erőforrások alapján összesítve történik. Miután az Advisor a súlyozott összesített kategória pontszámait, az Advisor egy egyszerű számítást végez, amely az előfizetések teljes pontszámát adja meg.

### <a name="scoring-methodology"></a>Pontozási módszertan

Az Advisor pontszámának kiszámítása négy lépésben végezhető el:

1. Az Advisor kiszámítja az érintett *erőforrások kiskereskedelmi költségeit*. Ezek az erőforrások azok az előfizetésekben találhatóak, amelyek legalább egy ajánlással rendelkeznek az Advisorban.
1. Az Advisor kiszámítja a *kiértékelt erőforrások kiskereskedelmi árát*. Ezeket az erőforrásokat az Advisor figyeli, függetlenül attól, hogy vannak-e javaslatai vagy sem.
1. Az Advisor minden egyes ajánlási típus esetében kiszámítja az *erőforrás-arányt*. Ez az arány az érintett erőforrások kiskereskedelmi díja, osztva a kiértékelt erőforrások kiskereskedelmi díjainak.
1. Az Advisor három további súlyozást alkalmaz az összes kategória kifogástalan erőforrás-arányára:

   * A nagyobb hatású javaslatok súlyosabbak a kisebb hatású javaslatoknál.
   * A hosszú távú javaslatokat tartalmazó erőforrások többek között a pontszám alapján lesznek kiszámítva.
   * A tanácsadóban elhalasztott vagy elhagyott erőforrások teljes egészében törlődnek a pontszám kiszámítása során.

Az Advisor az Advisor kategóriájának szintjén alkalmazza ezt a modellt, hogy minden kategóriához tanácsadói pontszámot adjon. A **Biztonság** egy [biztonságos pontszám](../security-center/secure-score-security-controls.md#introduction-to-secure-score) modellt használ. Egy egyszerű átlag a végső tanácsadói pontszámot eredményezi.

## <a name="advisor-score-faqs"></a>Advisor-pontszám – gyakori kérdések

### <a name="how-often-is-my-score-refreshed"></a>Milyen gyakran frissül a pontszám?

A pontszám naponta legalább egyszer frissül.

### <a name="why-do-some-recommendations-have-the-empty---value-in-the-category-score-impact-column"></a>Miért van néhány javaslat üres "-" értékkel a kategória pontszám Impact oszlopában?

Az Advisor nem tartalmaz azonnal új ajánlásokat vagy javaslatokat a pontozási modell legutóbbi változásaival. Egy rövid próbaidőszak után, amely általában néhány hét, a pontszámban szerepel.

### <a name="why-is-the-cost-score-impact-greater-for-some-recommendations-even-if-they-have-lower-potential-savings"></a>Miért nagyobb a költséghatékonyság hatása bizonyos javaslatok esetében, még akkor is, ha alacsonyabb a lehetséges megtakarítás?

A **díjszabás** a kihasználatlan erőforrások potenciális megtakarítását, valamint a javaslatok megvalósításának várható egyszerűségét tükrözi. A további súlyozás például olyan érintett erőforrásokra vonatkozik, amelyek hosszú ideje inaktívak, még akkor is, ha a lehetséges megtakarítások alacsonyabbak.

### <a name="why-dont-i-have-a-score-for-one-or-more-categories-or-subscriptions"></a>Miért nincs pontszám egy vagy több kategóriához vagy előfizetéshez?

Az Advisor csak az Advisor által értékelt erőforrásokkal rendelkező kategóriákra és előfizetésekre mutató pontszámot hoz létre.

### <a name="what-if-a-recommendation-isnt-relevant"></a>Mi a teendő, ha egy javaslat nem releváns?

Ha elveti az Advisor javaslatát, az a pontszám kiszámítása után nem lesz kihagyva. A nem hiányzó javaslatok segítségével az Advisor a javaslatok minőségének javítását is segíti.

### <a name="why-did-my-score-change"></a>Miért változott a pontszám?

A pontszám változhat, ha az Advisor által javasolt ajánlott eljárásokat bevezetve kijavítja az érintett erőforrásokat. Ha Ön vagy bárki, aki az előfizetésre vonatkozó engedélyekkel rendelkezik, vagy új erőforrásokat hozott létre, akkor a pontszám ingadozásai is megjelenhetnek. A pontszám a költségeket befolyásoló erőforrások arányán alapul, az összes erőforrás teljes költsége alapján.

### <a name="how-does-advisor-calculate-the-retail-cost-of-resources-on-a-subscription"></a>Hogyan számítja ki az Advisor az előfizetéshez tartozó erőforrások kiskereskedelmi költségeit?

Az Advisor az [Azure díjszabásában](https://azure.microsoft.com/pricing/)közzétett utólagos elszámolású díjszabást használja. Ezek a díjak nem tükrözik a vonatkozó kedvezményeket. A díjszabást ezután megszorozza az erőforrás utolsó napján történő használat mennyiségétől. Az erőforrás-díj kiszámításával járó kedvezmények kihagyása az Advisor által az előfizetések, a bérlők és a beléptetések esetében összehasonlítható, ha a kedvezmények eltérőek lehetnek.

### <a name="do-i-need-to-view-the-recommendations-in-advisor-to-get-points-for-my-score"></a>Meg kell-e tekinteni az Advisorban szereplő ajánlásokat, hogy pontokat kapjak a pontszámhoz?

Nem. A pontszám azt mutatja be, hogy az Advisor által ajánlott eljárásokat kell-e alkalmazni, még akkor is, ha az ajánlott eljárásokat proaktív módon fogadja el, és soha nem tekinti meg az Advisor javaslatait.

### <a name="does-the-scoring-methodology-differentiate-between-production-and-dev-test-workloads"></a>Különbséget tesz a pontozási módszer a termelési és fejlesztési-tesztelési feladatok között?

Nem, most nem. Az egyes erőforrásokra vonatkozó ajánlásokat azonban elhagyhatja, ha ezeket az erőforrásokat fejlesztési és tesztelési célokra használják, és a javaslatok nem érvényesek.

### <a name="can-i-compare-scores-between-a-subscription-with-100-resources-and-a-subscription-with-100000-resources"></a>Összehasonlítható-e az előfizetések közötti pontszámok egy 100-erőforrással és egy 100 000-erőforrással rendelkező előfizetéssel?

A pontozási módszer az előfizetés és a szolgáltatás kombinációjában lévő erőforrások számának szabályozására szolgál. A kevesebb erőforrással rendelkező előfizetések nagyobb vagy alacsonyabb pontszámot kapnak, mint az előfizetések több erőforrással.

### <a name="what-does-it-mean-when-i-see-coming-soon-in-the-score-impact-column"></a>Mit jelent, ha a pontszám hatásának oszlopában a "hamarosan elérhető" érték jelenik meg?

Ez az üzenet azt jelenti, hogy a javaslat új, és dolgozunk az Advisor pontszám modelljén. Miután ez az új javaslat egy pontszám-számításban szerepel, a pontszám befolyásolhatja a javaslat értékét.

### <a name="does-my-score-depend-on-how-much-i-spend-on-azure"></a>Függ a pontszám attól, hogy mennyit költek az Azure-ban?

Nem. A pontszám nem feltétlenül tükrözi, hogy mennyit költ. A szükségtelen kiadások alacsonyabb **költségekkel** járnak.

## <a name="access-advisor-score"></a>Hozzáférési tanácsadói pontszám

Az Advisor pontszám nyilvános előzetes verzióban érhető el a Azure Portal. A bal oldali ablaktábla **Advisor** szakasza alatt tekintse meg az **Advisor pontszámát**.

![Képernyőkép, amely az Advisor score belépési pontját jeleníti meg.](./media/advisor-score-3.png)

## <a name="next-steps"></a>Következő lépések

Az Advisor ajánlásaival kapcsolatos további információkért lásd:

* [Az Advisor bemutatása](advisor-overview.md)
* [Bevezetés az Advisor használatába](advisor-get-started.md)
* [Az Advisor költségekkel kapcsolatos javaslatai](advisor-cost-recommendations.md)
* [Az Advisor teljesítményével kapcsolatos javaslatok](advisor-performance-recommendations.md)
* [Advisor biztonsági javaslatok](advisor-security-recommendations.md)
* [Advisor működési kiválósági javaslatok](advisor-operational-excellence-recommendations.md)
