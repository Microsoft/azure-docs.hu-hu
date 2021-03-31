---
title: Alfürtözött mérőszámok kiegyensúlyozása
description: Az elhelyezési megkötések hatása a kiegyensúlyozásra és a kezelésének módjára
author: nipavlo
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: nipavlo
ms.openlocfilehash: 7f571a851e4da147240c524b742bcd652bc54181
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "82183116"
---
# <a name="balancing-of-subclustered-metrics"></a>Alfürtözött mérőszámok kiegyensúlyozása

## <a name="what-is-subclustering"></a>Mi az alfürtözés

Az alfürtözés akkor történik meg, ha a különböző elhelyezési korlátozásokkal rendelkező szolgáltatások közös metrikával rendelkeznek, és a jelentés betöltésére is sor kerül. Ha a szolgáltatások által jelentett terhelés jelentősen eltér, a csomópontok teljes terhelése nagy szórással fog rendelkezni, és úgy tűnik, mintha a fürt kiegyensúlyozatlan, még akkor is, ha a lehető legjobb egyenleggel rendelkezik.

## <a name="how-subclustering-affects-load-balancing"></a>Hogyan befolyásolja a fürtözés a terheléselosztást

Ha a szolgáltatások által a különböző csomópontokon jelentett terhelés jelentősen eltér, akkor előfordulhat, hogy egy nagy egyensúlyhiány van, ahol nincs. Továbbá, ha az alfürtözés okozta hamis egyensúlyhiány nagyobb, mint a tényleges egyensúlyhiány, akkor a erőforrás-kezelő kiegyensúlyozó algoritmusa is megzavarható, és a fürtben a legoptimálisabb egyensúly hozható létre.

Tegyük fel például, hogy négy szolgáltatásunk van, és mindegyiknek van egy terhelése a metrikus Metric1:

* Az A szolgáltatás – a "NodeType = =" előtér "elhelyezési korlátozással rendelkezik, amely 10-es terhelést jelez
* B szolgáltatás – elhelyezési korlátozás "NodeType = = frontend", a 10. terhelés jelentése
* Szolgáltatás C – a "NodeType = = háttér" elhelyezési korlátozással rendelkezik, amely a 100-es terhelést jelenti.
* A D szolgáltatás – "NodeType = = háttér" elhelyezési korlátozással rendelkezik, és 100-es terhelést jelez.
* Négy csomóponttal rendelkezik. Kettő közülük a "frontend" NodeType van beállítva, a másik kettő pedig "háttér".

A következő elhelyezéssel:

<center>

![Alfürtözött elhelyezési példa][Image1]
</center>

A fürt kiegyensúlyozatlan állapotba kerülhet, nagy terhelést biztosítunk a 3. és 4. csomóponton, de ez az elhelyezés a lehető legjobb egyensúlyt hozza létre ebben a helyzetben.

A Resource Manager képes felismerni az alfürtözési helyzeteket, és szinte minden esetben képes az optimális egyensúlyt létrehozni a megadott helyzetben.

Bizonyos kivételes helyzetekben, amikor a Resource Manager nem tud optimálisan egyensúlyt kiosztani egy alfürtözött metrikával, az továbbra is felismeri az alfürtözést, és állapotjelentést küld a probléma megoldásához.

## <a name="types-of-subclustering-and-how-they-are-handled"></a>Alfürtek típusai és azok kezelése

Az alfürtözési helyzetek három különböző kategóriába sorolhatók. Egy adott alfürtözési helyzet kategóriája határozza meg, hogy a Resource Manager hogyan fogja kezelni azt.

### <a name="first-category--flat-subclustering-with-disjoint-node-groups"></a>Első kategória – lapos alfürtözés a különálló csomópont-csoportokkal

Ez a kategória a legegyszerűbben olyan alfürtözést tartalmaz, ahol a csomópontok különböző csoportokba különíthetők el, és az egyes szolgáltatások csak az egyik csoport csomópontjaira helyezhetők el. Mindegyik csomópont csak egy csoporthoz és egy csoporthoz tartozik. A fentiekben ismertetett helyzetek ebben a kategóriában vannak, mint a legtöbb alfürtözési szituációban. 

Az ebben a kategóriában lévő helyzetekben a Resource Manager képes az optimális egyensúly kialakítására, és nincs szükség további beavatkozásra.

### <a name="second-category--subclustering-with-hierarchical-node-groups"></a>Második kategória – alfürtözés hierarchikus csomópont-csoportokkal

Ez akkor fordulhat elő, ha az egyik szolgáltatás számára engedélyezett csomópontok csoportja egy másik szolgáltatás számára engedélyezett csomópontok csoportjának egy részhalmaza. Ennek a helyzetnek a leggyakoribb példája, ha egyes szolgáltatásokban van definiálva elhelyezési korlátozás, és egy másik szolgáltatás nem rendelkezik elhelyezési korlátozással, és bármely csomópontra elhelyezhető.

Példa:

* A szolgáltatás: nincs elhelyezési megkötés
* B szolgáltatás: elhelyezési megkötés "NodeType = = frontend"
* Szolgáltatás C: elhelyezési megkötés "NodeType = = háttér"

Ez a konfiguráció egy részhalmazt hoz létre a különböző szolgáltatásokhoz tartozó csomópont-csoportok között.

<center>

![Részhalmazt felülbíráló alfürt][Image2]
</center>

Ebben az esetben fennáll a valószínűsége, hogy a rendszer az optimálisnál rosszabb egyensúlyt hoz létre.

A Resource Manager felismeri ezt a helyzetet, és előkészíti az "A" szolgáltatást két szolgáltatásba – az A1-es szolgáltatásba, amelyek a háttér-csomópontokon helyezhetők el Ez az első kategóriába tartozó, optimálisan kiegyensúlyozott állapotra vált.

### <a name="third-category--subclustering-with-partial-overlap-between-node-sets"></a>Harmadik kategória – részleges átfedés a csomópont-készletek között

Ez a helyzet akkor fordul elő, ha a csomópontok csoportjai között részleges átfedés van, amelyre egyes szolgáltatások elhelyezhetők.

Ha például van egy NodeColor nevű Node tulajdonság, és három csomóponttal rendelkezik:

* 1. csomópont: NodeColor = piros
* 2. csomópont: NodeColor = kék
* 2. csomópont: NodeColor = zöld

És két szolgáltatásunk van:

* A szolgáltatás: elhelyezési korlátozással "Color = = Red | | Szín = = kék "
* B szolgáltatás: elhelyezési korlátozással "Color = = Blue | | Szín = = zöld "

Ezért az A szolgáltatás az 1. és a 2. csomóponton helyezhető el, a B szolgáltatás pedig a 2. és 3. csomóponton helyezhető el.

Ebben az esetben fennáll a valószínűsége, hogy a rendszer az optimálisnál rosszabb egyensúlyt hoz létre.

A Resource Manager felismeri ezt a helyzetet, és létrehoz egy állapotjelentést, amely tanácsot ad a szolgáltatások felosztásához.

Ebben az esetben a Resource Manager nem tud javaslatot tenni a szolgáltatások felosztására, mivel több felosztás is végezhető, és nem lehet megbecsülni, hogy melyik módszer lenne az optimális megoldás a szolgáltatások felosztására.

## <a name="configuring-subclustering"></a>Alfürtözés konfigurálása

A Resource Manager alfürtözéssel kapcsolatos viselkedését az alábbi konfigurációs paraméterek módosításával módosíthatja:
* SubclusteringEnabled – a paraméter azt határozza meg, hogy a Resource Manager a terheléselosztás során figyelembe veszi-e az alfürtözést. Ha ez a paraméter ki van kapcsolva, az erőforrás-kezelő figyelmen kívül hagyja az alfürtözést, és megpróbál optimális egyensúlyt elérni globális szinten. A paraméter alapértelmezett értéke hamis.
* SubclusteringReportingPolicy – azt határozza meg, hogy a Resource Manager hogyan fogja kibocsátani a hierarchikus és a részlegesen átfedésben lévő alfürthöz tartozó állapotjelentést. A nulla érték azt jelenti, hogy az alfürttel kapcsolatos állapot-jelentések ki vannak kapcsolva, az "1" érték azt jelenti, hogy a rendszer figyelmeztetési állapotra vonatkozó jelentéseket készít az alrendszerek optimális alfürtözési helyzetéhez, és a "2" értéke "OK" állapotú jelentéseket fog készíteni. A paraméter alapértelmezett értéke "1".

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="SubclusteringEnabled" Value="true" />
            <Parameter Name="SubclusteringReportingPolicy" Value="1" />
        </Section>
```

Önálló üzemelő példányokhoz vagy az Azure által üzemeltetett fürtökhöz Template.jsClusterConfig.json keresztül:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "SubclusteringEnabled",
          "value": "true"
      },
      {
          "name": "SubclusteringReportingPolicy",
          "value": "1"
      },
    ]
  }
]
```

## <a name="next-steps"></a>Következő lépések
* Ha szeretné megtudni, hogy a fürterőforrás-kezelő hogyan kezeli és kiegyenlíti a fürt terhelését, tekintse meg a [terhelés kiegyensúlyozásáról](service-fabric-cluster-resource-manager-balancing.md) szóló cikket.
* Ha szeretné megtudni, hogyan korlátozhatja a szolgáltatásait, hogy csak bizonyos csomópontokon legyenek elhelyezve, lásd: [csomópont-tulajdonságok és elhelyezési megkötések](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)

[Image1]:./media/cluster-resource-manager-subclustering/subclustered-placement.png
[Image2]:./media/cluster-resource-manager-subclustering/subset-superset-nodes.png
