---
title: Szabályozás a Service Fabric fürterőforrás-kezelőben
description: Ismerje meg, hogyan konfigurálhatja a Service Fabric fürterőforrás-kezelő által biztosított szabályozásokat.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: b4d78b339bab02b5c44a31939e0da769dc21c3ec
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "75452160"
---
# <a name="throttling-the-service-fabric-cluster-resource-manager"></a>A Service Fabric fürterőforrás-kezelő szabályozása
Még ha helyesen konfigurálta a fürterőforrás-kezelőt is, a fürt megszakadhat. Előfordulhat például, hogy egyidejű csomópont-és meghibásodási tartománybeli hibák történtek – mi történne, ha a frissítés során bekövetkezett? A fürterőforrás-kezelő minden esetben megpróbál mindent kijavítani, és a fürt erőforrásait a fürt újbóli rendszerezése és kijavítása érdekében próbálja meg. A szabályozások segítenek biztosítani a megállít, hogy a fürt erőforrásokat használjon az erőforrások stabilizálásához – a csomópontok visszatérnek, a hálózati partíciók meggyógyítják, kijavítják a BITS üzembe helyezését.

Az ilyen típusú helyzetekben a Service Fabric fürterőforrás-kezelő több szabályozást is tartalmaz. Ezek a szabályozások meglehetősen nagy kalapácsok. Általában gondos tervezés és tesztelés nélkül nem változtathatók meg.

Ha módosítja a fürterőforrás-kezelő szabályozását, akkor azokat a várt tényleges terheléshez kell hangolnia. Meghatározhatja, hogy szükség van-e némi szabályozásra, még akkor is, ha ez azt jelenti, hogy a fürt bizonyos helyzetekben hosszabb időt vesz igénybe. A szabályozás helyes értékeinek megállapításához tesztelés szükséges. A szabályozásnak elég magasnak kell lennie ahhoz, hogy a fürt ésszerű időn belül reagáljon a változásokra, és elég alacsony legyen ahhoz, hogy ténylegesen megakadályozza a túl sok erőforrás-felhasználást. 

A legtöbb alkalommal, amikor láttuk, hogy ügyfeleink a szabályozást használják, már egy erőforrás által korlátozott környezetben voltak. Néhány példa korlátozott hálózati sávszélességet biztosít az egyes csomópontok számára, illetve olyan lemezeket, amelyek nem tudnak egyszerre sok állapot-nyilvántartó replikát létrehozni párhuzamosan, az átviteli korlátok miatt. A szabályozások nélkül a műveletek eljárhatják ezeket az erőforrásokat, így a műveletek meghiúsulnak vagy lassúak lesznek. Ezekben az esetekben az ügyfelek a szabályozást használták, és tudták, hogy kibővítették azt az időtartamot, ameddig a fürt stabil állapotba kerülne. Az ügyfelek azt is megértették, hogy az általános megbízhatósági szinten futnak a szabályozás során.


## <a name="configuring-the-throttles"></a>A szabályozások konfigurálása

A Service Fabric két mechanizmussal rendelkezik a replika-mozgások számának szabályozásához. A Service Fabric 5,7 előtt létezett alapértelmezett mechanizmus a maximálisan megengedett áthelyezések teljes számának szabályozását jelenti. Ez a beállítás nem működik a teljes méretű fürtök esetében. A nagyméretű fürtök esetében az alapértelmezett érték túl kicsi, jelentősen lelassíthatja a terheléselosztást, még akkor is, ha ez szükséges, miközben a kisebb fürtökön nincs hatása. Ezt az előző mechanizmust a százalék-alapú szabályozás váltotta fel, amely jobban méretezhető olyan dinamikus fürtökkel, amelyekben a szolgáltatások és a csomópontok száma rendszeresen változik.

A szabályozások a fürtök replikái számának százalékán alapulnak. A százalékos alapú szabályozások lehetővé teszik a következő szabály jelölését: "a replikák több mint 10%-át nem helyezhetők át 10 perces intervallumban", például.

A százalék alapú szabályozás konfigurációs beállításai a következők:

  - GlobalMovementThrottleThresholdPercentage – a fürtben egyszerre engedélyezett mozgások maximális száma a fürtben lévő replikák teljes számának százalékában kifejezve. a 0 érték nem korlátozza a korlátot. Az alapértelmezett érték a 0. Ha a beállítás és a GlobalMovementThrottleThreshold is meg van adva, akkor a rendszer a konzervatív korlátot használja.
  - GlobalMovementThrottleThresholdPercentageForPlacement – az elhelyezési fázisban engedélyezett mozgások maximális száma a fürtben lévő replikák teljes számának százalékában kifejezve. a 0 érték nem korlátozza a korlátot. Az alapértelmezett érték a 0. Ha a beállítás és a GlobalMovementThrottleThresholdForPlacement is meg van adva, akkor a rendszer a konzervatív korlátot használja.
  - GlobalMovementThrottleThresholdPercentageForBalancing – az elosztási fázisban engedélyezett mozgások maximális száma a fürtben lévő replikák teljes számának százalékában kifejezve. a 0 érték nem korlátozza a korlátot. Az alapértelmezett érték a 0. Ha a beállítás és a GlobalMovementThrottleThresholdForBalancing is meg van adva, akkor a rendszer a konzervatív korlátot használja.

A szabályozás százalékos arányának megadásakor a 0,05-as 5%-ot kell megadnia. A szabályozás hatálya alá eső intervallum a GlobalMovementThrottleCountingInterval, amely másodpercben van megadva.


``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThresholdPercentage" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForPlacement" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForBalancing" Value="0" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
</Section>
```

Önálló üzemelő példányokhoz vagy az Azure által üzemeltetett fürtökhöz Template.jsClusterConfig.json keresztül:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "GlobalMovementThrottleThresholdPercentage",
          "value": "0.0"
      },
      {
          "name": "GlobalMovementThrottleThresholdPercentageForPlacement",
          "value": "0.0"
      },
      {
          "name": "GlobalMovementThrottleThresholdPercentageForBalancing",
          "value": "0.0"
      },
      {
          "name": "GlobalMovementThrottleCountingInterval",
          "value": "600"
      }
    ]
  }
]
```

### <a name="default-count-based-throttles"></a>Alapértelmezett Count-alapú szabályozások
Ezek az információk abban az esetben vannak megadva, ha régebbi fürtöket használ, vagy továbbra is megőrzi ezeket a konfigurációkat az azóta frissített fürtökön. Általánosságban azt javasoljuk, hogy ezeket a rendszer a fenti százalék-alapú szabályozással helyettesítse. Mivel a százalékos sávszélesség-szabályozás alapértelmezés szerint le van tiltva, ezek a szabályozások a fürt alapértelmezett szabályozási értékei maradnak mindaddig, amíg le nem állnak, és a százalékos alapú szabályozások le lesznek cserélve. 

  - GlobalMovementThrottleThreshold – ezzel a beállítással szabályozható, hogy a fürtben lévő mozgások teljes száma egy időben történjen. A GlobalMovementThrottleCountingInterval másodpercben megadott időtartam. A GlobalMovementThrottleThreshold alapértelmezett értéke 1000, a GlobalMovementThrottleCountingInterval alapértelmezett értéke pedig 600.
  - MovementPerPartitionThrottleThreshold – ezzel a beállítással szabályozható, hogy az egyes szolgáltatások partícióinak hány mozgása legyen egy ideig. A MovementPerPartitionThrottleCountingInterval másodpercben megadott időtartam. A MovementPerPartitionThrottleThreshold alapértelmezett értéke 50, a MovementPerPartitionThrottleCountingInterval alapértelmezett értéke pedig 600.

Ezeknek a szabályozásoknak a konfigurációja ugyanazt a mintát követi, mint a százalék-alapú szabályozás.

## <a name="next-steps"></a>Következő lépések
- Ha szeretné megtudni, hogy a fürterőforrás-kezelő hogyan kezeli és kiegyenlíti a fürt terhelését, tekintse meg a [terhelés kiegyensúlyozásáról](service-fabric-cluster-resource-manager-balancing.md) szóló cikket.
- A fürterőforrás-kezelő számos lehetőséget kínál a fürt leírására. Ha többet szeretne megtudni róluk, tekintse meg ezt a cikket a [Service Fabric-fürt leírását ismertető](service-fabric-cluster-resource-manager-cluster-description.md) cikkben.
