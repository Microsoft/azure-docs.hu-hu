---
title: Az Azure Service Fabric-fürt kiegyensúlyozása
description: Bevezetés a fürt kiegyensúlyozására a Service Fabric fürterőforrás-kezelővel.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 192aca589c3b1e660667dbe8377afe7802b56f17
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93146194"
---
# <a name="balancing-your-service-fabric-cluster"></a>A Service Fabric-fürt kiegyensúlyozása
A Service Fabric fürterőforrás-kezelő támogatja a dinamikus betöltési változásokat, a csomópontok vagy szolgáltatások hozzáadására és eltávolítására való reagálást. Emellett automatikusan kijavította a megkötések megsértését, és proaktív módon kiegyensúlyozza a fürtöt. Milyen gyakran történtek ezek a műveletek, és mi váltja ki őket?

A fürterőforrás-kezelő három különböző kategóriájú munkát hajt végre. Ezek a következők:

1. Elhelyezés – ez a szakasz a hiányzó állapotú replikák vagy állapot nélküli példányok elhelyezését tárgyalja. Az elhelyezés magában foglalja az új szolgáltatásokat és az állapot-nyilvántartó replikák vagy az állapot nélküli, sikertelenül kezelt példányok kezelését. A replikák vagy példányok törlését és eldobását itt kezeljük.
2. Korlátozási ellenőrzések – ebben a szakaszban a rendszeren belüli különböző elhelyezési megkötések (szabályok) megsértéseit ellenőrzi és kijavította. A szabályok példái olyan dolgok, mint például annak biztosítása, hogy a csomópontok ne legyenek túlterhelve, és hogy teljesülnek a szolgáltatások elhelyezésére vonatkozó korlátozások.
3. Egyensúly – ez a fázis ellenőrzi, hogy szükséges-e az egyensúly a különböző metrikák beállított kívánt szintje alapján. Ha így van, akkor megpróbál olyan elrendezést találni a fürtben, amely kiegyensúlyozottabb.

## <a name="configuring-cluster-resource-manager-timers"></a>A fürterőforrás-kezelő időzítő konfigurálása
Az egyensúly körüli vezérlők első készlete időzítők halmaza. Ezek az időzítők szabályozzák, hogy a fürterőforrás-kezelő milyen gyakran vizsgálja meg a fürtöt, és végrehajtja a megfelelő műveleteket.

A fürterőforrás-kezelő különböző típusú helyesbítéseit egy másik időzítő vezérli, amely a gyakoriságát szabályozza. Amikor az egyes időzítők bekövetkeznek, a feladat ütemezve lesz. Alapértelmezés szerint a Resource Manager:

* megvizsgálja az állapotát, és alkalmazza a frissítéseket (például egy csomópont leállását) a másodpercenként 1/10-én
* az elhelyezési ellenőrzési jelző másodpercenkénti beállítása
* a megkötés-ellenőrzési jelző másodpercenkénti beállítása
* öt másodpercenként beállítja az egyenlegező jelzőt

Az időzítők szabályozásának példái a következők:

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

Önálló üzemelő példányokhoz vagy az Azure által üzemeltetett fürtökhöz Template.jsClusterConfig.json keresztül:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "PLBRefreshGap",
          "value": "0.10"
      },
      {
          "name": "MinPlacementInterval",
          "value": "1.0"
      },
      {
          "name": "MinConstraintCheckInterval",
          "value": "1.0"
      },
      {
          "name": "MinLoadBalancingInterval",
          "value": "5.0"
      }
    ]
  }
]
```

A fürterőforrás-kezelő jelenleg csak az egyik műveletet hajtja végre egyszerre, egymás után. Ezért ezekre az időpontokra a "minimális intervallumok", valamint az időzítők "beállítás jelzők"ként való kilépésének lépésein kerül sor. A fürterőforrás-kezelő például gondoskodik a szolgáltatások létrehozására vonatkozó függőben lévő kérelmekről a fürt kiegyensúlyozása előtt. Ahogy az alapértelmezett időintervallumok szerint is látható, a fürterőforrás-kezelő megkeresi a gyakran szükséges műveleteket. Ez általában azt jelenti, hogy az egyes lépések során végzett módosítások halmaza kicsi. A kisméretű módosítások gyakran lehetővé teszik, hogy a fürterőforrás-kezelő reagáljon a fürtben előforduló dolgokra. Az alapértelmezett időzítők biztosítják a kötegelt feldolgozást, mivel a több azonos típusú esemény általában egyszerre fordul elő. 

Ha például a csomópontok meghibásodnak, akkor a teljes tartalék tartományok egyszerre is megadhatók. Ezeket a hibákat a rendszer a *PLBRefreshGap* utáni következő állapot frissítése során rögzíti. A javítások meghatározása a következő elhelyezés, a korlátozás-ellenőrzés és a kiegyenlítés futtatása során történik. Alapértelmezés szerint a fürterőforrás-kezelő nem ellenőrzi a fürt változásainak időpontját, és az összes módosítást egyszerre próbálja meg kezelni. Ennek eredményeképpen a forgalom feltört.

A fürterőforrás-kezelőnek további információra van szüksége annak megállapításához, hogy a fürt kiegyensúlyozva van-e. A következő két konfigurációval rendelkezünk: *BalancingThresholds* és *ActivityThresholds*.

## <a name="balancing-thresholds"></a>Kiegyensúlyozási küszöbértékek
A kiegyensúlyozás kiváltásának fő vezérlője a kiegyensúlyozó küszöbérték. A metrika kiegyensúlyozási küszöbértéke egy _arány_. Ha a legtöbbet betöltött csomóponton lévő metrika terhelése a legkevésbé betöltött csomópont terhelésének mértékével meghaladja a mérőszám *BalancingThreshold*, a fürt kiegyensúlyozatlan. Ennek eredményeképpen a rendszer a fürterőforrás-kezelő legközelebb ellenőrzi a terheléselosztást. A *MinLoadBalancingInterval* időzítő határozza meg, hogy a fürterőforrás-kezelő milyen gyakran ellenőrizze, hogy szükség van-e a kiegyensúlyozásra. Az ellenőrzés nem jelenti azt, hogy bármi történik. 

Az egyensúlyi küszöbértékek meghatározása a fürt definíciójának részeként, metrikus alapon történik. A metrikákkal kapcsolatos további információkért tekintse meg [ezt a cikket](service-fabric-cluster-resource-manager-metrics.md).

ClusterManifest.xml

```xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

Önálló üzemelő példányokhoz vagy az Azure által üzemeltetett fürtökhöz Template.jsClusterConfig.json keresztül:

```json
"fabricSettings": [
  {
    "name": "MetricBalancingThresholds",
    "parameters": [
      {
          "name": "MetricName1",
          "value": "2"
      },
      {
          "name": "MetricName2",
          "value": "3.5"
      }
    ]
  }
]
```

<center>

![Példa kiegyensúlyozó küszöbértékre][Image1]
</center>

Ebben a példában az egyes szolgáltatások egy adott metrika egy egységét veszik igénybe. A fenti példában a csomópontok maximális terhelése öt, a minimum pedig kettő. Tegyük fel, hogy a metrika kiegyensúlyozási küszöbértéke három. Mivel a fürt aránya 5/2 = 2,5, és a megadott egyenlegező küszöbértéknél kevesebb, mint három, a fürt egyensúlyban van. A fürterőforrás-kezelő ellenőrzésekor nem aktiválódik az egyensúly.

Az alsó példában a csomópontok maximális terhelése 10, míg a minimum kettő, ami öt arányt eredményez. Az öt érték nagyobb, mint az adott metrika esetében három kijelölt egyenlegező küszöbérték. Ennek eredményeképpen a rendszer az egyenlegező időzítőt a következő időpontra ütemezi. Ilyen esetben a terhelést általában a Csomópont3 terjesztik. Mivel a Service Fabric fürterőforrás-kezelő nem használ kapzsi megközelítést, néhány terhelést is el lehet osztani a Csomópont2. 

<center>

![Kiegyenlítő küszöbértékek – példa műveletek][Image2]
</center>

> [!NOTE]
> A "kiegyensúlyozás" két különböző stratégiát kezel a fürt terhelésének kezeléséhez. A fürterőforrás-kezelő által használt alapértelmezett stratégia a terhelés elosztása a fürt csomópontjai között. A másik stratégia a [Töredezettségmentesítés](service-fabric-cluster-resource-manager-defragmentation-metrics.md). A Lemeztöredezettség-mentesítést ugyanabban az egyenlegező futtatásban hajtja végre a rendszer. Az egyenlegező és a töredezettségmentesítés stratégiák különböző mérőszámokhoz használhatók ugyanazon a fürtön belül. A szolgáltatások mind az egyenlegező, mind a Lemeztöredezettség-mentesítő metrikával rendelkezhetnek. A Lemeztöredezettség-mentesítő metrikák esetében a fürtben betöltött terhelések aránya akkor aktiválódik, ha a terheléselosztási küszöbérték _alá_ esik. 
>

Az elosztási küszöbérték alá való beszerzés nem kifejezett cél. Az egyenlegező küszöbértékek csak *triggerek*. A kiegyenlítő futtatásakor a fürterőforrás-kezelő meghatározza, hogy milyen módosításokat végezhet, ha van ilyen. Csak azért, mert egy kiegyenlítő keresés kiindul, nem jelent semmi lépést. Előfordulhat, hogy a fürt kiegyensúlyozatlan, de túl van korlátozva. Alternatív megoldásként a tökéletesítésekhez túl [költséges](service-fabric-cluster-resource-manager-movement-cost.md)mozgások szükségesek.

## <a name="activity-thresholds"></a>Tevékenység küszöbértékei
Időnként, bár a csomópontok viszonylag kiegyensúlyozva vannak, a fürt terhelésének *teljes* mennyisége alacsony. A terhelés hiánya átmeneti dip lehet, vagy mert a fürt új, és csak a bootstrapped. Mindkét esetben előfordulhat, hogy nem kíván időt fordítani a fürtre, mert kevés a kinyerhető. Ha a fürt egyensúlyba került, a hálózat és a számítási erőforrások elköltésével bármilyen nagy *abszolút* különbség nélkül mozgathatja a dolgokat. A szükségtelen mozgatások elkerülése érdekében egy másik, a tevékenység küszöbértékének nevezett vezérlőelem van. A tevékenységi küszöbértékek lehetővé teszik a tevékenységhez tartozó abszolút alsó határ megadását. Ha egyetlen csomópont sem haladja meg a küszöbértéket, akkor a rendszer nem aktiválja a kiegyenlítést, még akkor sem, ha a kiegyenlítési küszöbérték teljesül.

Tegyük fel, hogy megtartjuk a jelen metrika három értékének kiegyensúlyozási küszöbértékét. Tegyük fel, hogy a 1536-es tevékenység küszöbértéke is. Az első esetben, amíg a fürt kiegyensúlyozva van, a terheléselosztási küszöbértéknél nincs csomópont, amely nem felel meg a tevékenység küszöbértékének, így semmi nem történik. Az alsó példában a Csomópont1 a tevékenység küszöbértéke felett van. Mivel az egyenlegező küszöbérték és a metrika tevékenységi küszöbértéke is túllépve, a rendszer ütemezi a kiegyenlítést. Példaként tekintse meg a következő ábrát: 

<center>

![Példa a tevékenység küszöbértékére][Image3]
</center>

Az elosztási küszöbértékekhez hasonlóan a tevékenység küszöbértékei a fürt definíciója szerint vannak definiálva a metrikán keresztül:

ClusterManifest.xml

``` xml
    <Section Name="MetricActivityThresholds">
      <Parameter Name="Memory" Value="1536"/>
    </Section>
```

Önálló üzemelő példányokhoz vagy az Azure által üzemeltetett fürtökhöz Template.jsClusterConfig.json keresztül:

```json
"fabricSettings": [
  {
    "name": "MetricActivityThresholds",
    "parameters": [
      {
          "name": "Memory",
          "value": "1536"
      }
    ]
  }
]
```

A kiegyensúlyozási és a tevékenységi küszöbértékek is egy adott metrika esetében vannak kötve, csak akkor aktiválódik, ha az adott metrika esetében az elosztási küszöbérték és a tevékenység küszöbértéke is túllépve.

> [!NOTE]
> Ha nincs megadva, a metrika kiegyensúlyozási küszöbértéke 1, a tevékenység küszöbértéke pedig 0. Ez azt jelenti, hogy a fürterőforrás-kezelő megkísérli a metrika teljes mértékben kiegyensúlyozottan megtartani az adott terhelést. Ha egyéni metrikákat használ, javasoljuk, hogy explicit módon határozza meg a metrikák saját kiegyensúlyozási és tevékenységi küszöbértékeit. 
>

## <a name="balancing-services-together"></a>Szolgáltatások kiegyensúlyozása együtt
Azt jelzi, hogy a fürt kiegyensúlyozva van-e, vagy sem a fürtre kiterjedő döntés. A kijavításának módja azonban az egyes szolgáltatás-replikák és-példányok áthelyezése. Ez logikus, igaz? Ha a memória fel van halmozva egy csomóponton, több replika vagy példány is hozzájárulhat ahhoz. Az egyensúlyhiány kijavítása szükségessé teheti az olyan állapot-nyilvántartó replikák vagy állapot nélküli példányok áthelyezését, amelyek a kiegyensúlyozatlan metrikát használják.

Időnként előfordulhat azonban, hogy egy olyan szolgáltatás, amely még nem volt kiegyensúlyozva, áthelyezte a szolgáltatást (ne feledje, hogy korábban a helyi és a globális súlyok megvitatását) Miért mozdulnak el egy szolgáltatás, ha az összes ilyen szolgáltatás mérőszáma kiegyensúlyozott volt? Lássunk egy példát:

- Tegyük fel, hogy négy szolgáltatás, a service1, a service2, a Service3 és a Service4 van. 
- A service1-jelentések mérőszámai Metric1 és Metric2. 
- A service2-jelentések mérőszámai Metric2 és Metric3. 
- A Service3-jelentések mérőszámai Metric3 és Metric4.
- Service4 jelentések metrika Metric99. 

Bizonyára láthatja, hová megyünk itt: van egy lánc! Négy független szolgáltatásunk nem érhető el, három olyan szolgáltatásunk van, amely kapcsolódik egymáshoz, és az egyik a saját tulajdonában van.

<center>

![Diagram, amely bemutatja, hogyan egyenlítheti össze a szolgáltatásokat.][Image4]
</center>

Ennek a láncnak a miatt előfordulhat, hogy a 1-4-es metrikai egyensúlyhiány a szolgáltatások 1-3-hez tartozó replikákat vagy példányokat eredményezhet. Azt is tudjuk, hogy az 1., 2. vagy 3. mérőszámok egyensúlyhiánya nem okozhat mozgást a Service4. A Service4-hoz tartozó replikák vagy példányok áthelyezése óta nem lenne pont semmi, ami hatással lehet a 1-3 mérőszámok egyenlegére.

A fürterőforrás-kezelő automatikusan kiszámítja, hogy mely szolgáltatások kapcsolódnak egymáshoz. A szolgáltatások metrikáinak hozzáadása, eltávolítása vagy módosítása hatással lehet a kapcsolataira. Előfordulhat például, hogy a service2 két futtatása is frissült a Metric2 eltávolításához. Ez megszakítja a láncot a service1 és a service2 között. Mostantól a kapcsolódó szolgáltatások két csoportja helyett három:

<center>

![Diagram, amely azt mutatja, hogy a fürterőforrás-kezelő határozza meg, hogy mely szolgáltatások kapcsolódnak egymáshoz.][Image5]
</center>

## <a name="next-steps"></a>Következő lépések
* A metrikák azt jelentik, hogyan kezeli a Service Fabric fürterőforrás-kezelő a fürtben a felhasználást és a kapacitást. A metrikákkal és azok konfigurálásával kapcsolatos további tudnivalókért tekintse meg [ezt a cikket](service-fabric-cluster-resource-manager-metrics.md)
* A szállítási költség az egyik módja annak, hogy a fürterőforrás-kezelőnek jelezze, hogy bizonyos szolgáltatások drágábbak a többinél. További információ a mozgási díjakról: [ebben a cikkben](service-fabric-cluster-resource-manager-movement-cost.md)
* A fürterőforrás-kezelő több szabályozást is tartalmaz, amelyekkel lelassíthatja a forgalom változását a fürtben. Általában nem szükségesek, de ha [szüksége van rájuk, megismerheti őket](service-fabric-cluster-resource-manager-advanced-throttling.md)
* A fürterőforrás-kezelő felismeri és kezeli az alfürtözést (ez a helyzet akkor fordul elő, ha elhelyezési megkötéseket és kiegyensúlyozást használ). Ha szeretné megtudni, hogyan befolyásolhatja az alfürtözést, és hogyan kezelheti azt, tekintse meg [a következőt](cluster-resource-manager-subclustering.md) :

[Image1]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png
