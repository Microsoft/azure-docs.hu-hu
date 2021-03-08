---
title: A metrikák töredezettségmentesítését az Azure Service Fabric
description: Ismerkedjen meg a töredezettségmentesítéssel vagy a csomagolással a Service Fabric mérőszámait tartalmazó stratégia segítségével. Ez a módszer nagyon nagy szolgáltatások esetén hasznos.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 6dee9a845df305b06153d99c2bafe6d74524a139
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2021
ms.locfileid: "102449203"
---
# <a name="defragmentation-of-metrics-and-load-in-service-fabric"></a>A metrikák és a betöltések töredezettségmentesítését Service Fabric
A Service Fabric fürterőforrás-kezelő alapértelmezett stratégiája a terhelési metrikák kezeléséhez a fürtben a terhelés elosztását végzi. Annak biztosítása, hogy a csomópontok egyenletesen legyenek kihasználva, így elkerülhetők a gyors és a ritka elérésű foltok is, amelyek mind a versengő, mind a kárba A munkaterhelések fürtben való elosztása szintén a legbiztonságosabb a túlélő meghibásodások szempontjából, mivel biztosítja, hogy a hiba nem veszi figyelembe az adott munkaterhelés nagy hányadát. 

A Service Fabric fürterőforrás-kezelője más stratégiát támogat a terhelés kezeléséhez, ami a töredezettségmentesítés. A töredezettségmentesítés azt jelenti, hogy ahelyett, hogy egy metrika kihasználtságát a fürtön keresztül kellene terjeszteni, a rendszer konszolidálja. Az összevonás csak az alapértelmezett kiegyensúlyozási stratégia inverziója – a metrikus terhelések átlagos szórásának minimalizálása helyett a fürterőforrás-kezelő megkísérli a növelést.

## <a name="when-to-use-defragmentation"></a>Mikor kell használni a töredezettségmentesítést
A fürt terhelésének elosztása az egyes csomópontokon lévő erőforrásokat használja fel. Bizonyos munkaterhelések olyan szolgáltatásokat hoznak létre, amelyek kivételesen nagy méretűek, és a legtöbb vagy az összes csomópontot használják. Ezekben az esetekben előfordulhat, hogy ha nagy méretű munkaterhelések jönnek létre, és nincs elég hely a csomópontokon a futtatásához. A nagyméretű munkaterhelések nem jelentenek problémát a Service Fabricban; Ezekben az esetekben a fürterőforrás-kezelő megállapítja, hogy át kell szerveznie a fürtöt, hogy helyet szabadítson fel ehhez a nagy számítási feladathoz. Addig azonban, amíg a munkaterhelésnek várnia kell a fürtben való ütemezésre.

Ha sok szolgáltatás és állapot mozog, akkor hosszú időt is igénybe vehet, amíg a nagy számítási feladatok a fürtbe kerülnek. Ez nagyobb valószínűséggel, ha a fürtben lévő más munkaterhelések is nagy méretűek, ezért az átrendezés hosszabb időt vesz igénybe. A Service Fabric csapat a forgatókönyv szimulálása során mérte a létrehozási időpontokat. Azt találtuk, hogy a nagyméretű szolgáltatások létrehozása sokkal több időt vett igénybe, amint a fürt kihasználtsága 30% és 50% között van. Ennek a forgatókönyvnek a kezeléséhez a töredezettségmentesítést terheléselosztási stratégiaként vezették be. Azt találtuk, hogy a nagy számítási feladatokhoz, különösen azokhoz, ahol a létrehozási idő fontos volt, a töredezettségmentesítés nagyon segítette a fürtben ütemezett új számítási feladatok beszerzését.

A Lemeztöredezettség-mentesítő metrikák konfigurálásával beállíthatja, hogy a fürterőforrás-kezelő proaktívan próbálja megismételni a szolgáltatások terhelését kevesebb csomópontra. Így biztosítható, hogy a fürt átrendezése nélkül szinte mindig legyen nagy a szolgáltatás. A fürt átrendezésének mellőzése lehetővé teszi a nagy méretű munkaterhelések gyors létrehozását.

A legtöbb embernek nincs szüksége töredezettségmentesítésre. A szolgáltatások általában kicsik, ezért nem nehéz megtalálni a helyet a fürtben. Ha az átszervezés lehetséges, gyorsan, újra bekerül, mivel a legtöbb szolgáltatás kicsi, és gyorsan és párhuzamosan helyezhető el. Ha azonban nagyméretű szolgáltatásokkal rendelkezik, és gyorsan kell őket létrehozni, akkor a töredezettségmentesítés stratégia az Ön számára. A Lemeztöredezettség-mentesítő használatának kompromisszumait a következő témakörben ismertetjük. 

## <a name="defragmentation-tradeoffs"></a>Töredezettségmentesítési kompromisszumok
A Lemeztöredezettség-mentesítő növelheti a hibák impactfulness, mivel további szolgáltatások futnak a sikertelen csomópontokon. A töredezettségmentesítés is növelheti a költségeket, mivel a fürtben lévő erőforrásokat tartalékban kell tárolni, és a nagy számítási feladatok létrehozására kell várni.

Az alábbi ábrán két fürt vizuális ábrázolása látható, amelyek közül az egyik a töredezettségmentesítése, a másik pedig nem. 

<center>

![A kiegyensúlyozott és a detöredezett fürtök összehasonlítása][Image1]
</center>

A kiegyensúlyozott esetben vegye figyelembe, hogy hány mozgásra lenne szükség a legnagyobb szolgáltatási objektumok egyikének elhelyezéséhez. A detöredezett fürtben a nagy munkaterhelés a négy vagy öt csomópontra helyezhető anélkül, hogy várnia kellene bármely más szolgáltatás áthelyezésére.

## <a name="defragmentation-pros-and-cons"></a>Töredezettségmentesítés előnyei és hátrányai
Mi is az egyéb fogalmi kompromisszumok? Íme egy gyors táblázat, amely a következőkre gondol:

| Lemeztöredezettség-mentesítő profik | Töredezettségmentesítés hátrányai |
| --- | --- |
| Lehetővé teszi a nagyméretű szolgáltatások gyorsabb létrehozását |A terhelést kevesebb csomópontra koncentrálja, ezzel növelve a tartalmat |
| Csökkenti az adatáthelyezést a létrehozás során |A hibák nagyobb mértékben befolyásolhatják a szolgáltatásokat, és nagyobb adatforgalomhoz vezethetnek |
| Lehetővé teszi a követelmények részletes leírását és a hely visszanyerését |Összetettebb általános erőforrás-kezelési konfiguráció |

A detöredezett és a normál metrikákat is összekeverheti ugyanabban a fürtben. A fürterőforrás-kezelő a lehető legnagyobb mértékben megpróbálja összevonni a Lemeztöredezettség-mentesítési metrikákat, miközben kiterjeszti a többiet. A töredezettségmentesítés és az elosztási stratégiák keverésének eredményei több tényezőtől függnek, többek között:
  - a kiegyenlítő metrikák száma és a Lemeztöredezettség-mentesítő metrikák száma
  - Azt határozza meg, hogy bármely szolgáltatás mindkét típusú metrikát használja-e 
  - a metrikák súlyozása
  - aktuális metrika betöltése
  
A pontos konfiguráció meghatározásához kísérletezésre van szükség. Javasoljuk a számítási feladatok alapos mérését, mielőtt engedélyezi a töredezettségmentesítés-metrikákat az éles környezetben. Ez különösen akkor igaz, ha a töredezettségmentesítést és a kiegyensúlyozott mérőszámokat ugyanazon a szolgáltatáson belül keverik. 

## <a name="configuring-defragmentation-metrics"></a>A Lemeztöredezettség-mentesítő metrikáinak konfigurálása
A Lemeztöredezettség-mentesítő metrikák konfigurálása globális döntés a fürtben, és egyéni metrikák is kiválaszthatók a töredezettségmentesítéshez. A következő konfigurációs kódrészletek bemutatják, hogyan konfigurálhatja a töredezettségmentesítés mérőszámait. Ebben az esetben a "Metric1" a Lemeztöredezettség-mentesítési metrikaként van konfigurálva, míg a "Metric2" általában továbbra is egyensúlyban marad. 

ClusterManifest.xml:

```xml
<Section Name="DefragmentationMetrics">
    <Parameter Name="Metric1" Value="true" />
    <Parameter Name="Metric2" Value="false" />
</Section>
```

Önálló üzemelő példányokhoz vagy az Azure által üzemeltetett fürtökhöz Template.jsClusterConfig.json keresztül:

```json
"fabricSettings": [
  {
    "name": "DefragmentationMetrics",
    "parameters": [
      {
          "name": "Metric1",
          "value": "true"
      },
      {
          "name": "Metric2",
          "value": "false"
      }
    ]
  }
]
```


## <a name="next-steps"></a>Következő lépések
- A fürterőforrás-kezelő számos lehetőséget kínál a fürt leírására. Ha többet szeretne megtudni róluk, tekintse meg ezt a cikket a [Service Fabric-fürt leírását ismertető](service-fabric-cluster-resource-manager-cluster-description.md) cikkben.
- A metrikák azt jelentik, hogyan kezeli a Service Fabric fürterőforrás-kezelő a fürtben a felhasználást és a kapacitást. A metrikákkal és azok konfigurálásával kapcsolatos további tudnivalókért tekintse meg [ezt a cikket](service-fabric-cluster-resource-manager-metrics.md)

[Image1]:./media/service-fabric-cluster-resource-manager-defragmentation-metrics/balancing-defrag-compared.png
