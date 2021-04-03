---
title: 'Service Fabric fürterőforrás-kezelő: szállítási díj'
description: Ismerje meg a Service Fabric-szolgáltatások szállítási költségeit, valamint azt, hogy hogyan lehet megadni az építészeti igényeknek megfelelően, beleértve a dinamikus konfigurációt is.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.custom: devx-track-csharp
ms.openlocfilehash: 0fdcfb02851d56ed996ae4bf32671ab545782733
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89005343"
---
# <a name="service-movement-cost"></a>Szolgáltatás-áthelyezési díj
A Service Fabric fürterőforrás-kezelő tényezője azt vizsgálja, hogy a fürtön végrehajtott módosítások Milyen költségekkel járnak. A "Cost" fogalma ki van kapcsolva a fürt mennyiségének javítására. A költségek a szolgáltatások kiegyensúlyozásra, töredezettségmentesítésre és egyéb követelményekre való áthelyezésével kapcsolatosak. A cél az, hogy a legkevésbé zavaró vagy költséges módon teljesítse a követelményeket.

A szolgáltatásoknak a minimális CPU-időt és a hálózati sávszélességet kell áthelyezniük. Az állapot-nyilvántartó szolgáltatások esetében szükség van a szolgáltatások állapotának másolására, így további memóriát és lemezt igényel. Az Azure Service Fabric fürterőforrás-kezelőhöz tartozó megoldások díjszabásának minimalizálása révén biztosítható, hogy a fürt erőforrásai ne legyenek feleslegesen kitöltöttek. Azonban nem szeretné figyelmen kívül hagyni azokat a megoldásokat, amelyek jelentősen javítják a fürt erőforrásainak kiosztását.

A fürterőforrás-kezelő két módon kezeli a költségeket, és korlátozza azokat, miközben megpróbálja kezelni a fürtöt. Az első mechanizmus egyszerűen csak az összes áthelyezést számítja fel. Ha két megoldás jön létre ugyanazzal az egyenleggel (pontszám), akkor a fürterőforrás-kezelő előnyben részesíti a legalacsonyabb költségeket (a mozgatások teljes száma).

Ez a stratégia jól működik. Az alapértelmezett vagy a statikus terhelések esetében azonban nem valószínű, hogy minden olyan összetett rendszer, amely minden mozdulat egyenlő. Egyesek valószínűleg sokkal drágábbak.

## <a name="setting-move-costs"></a>Áthelyezési költségek beállítása 
A szolgáltatáshoz tartozó alapértelmezett áthelyezési költségeket a létrehozáskor adhatja meg:

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -DefaultMoveCost Medium
```

C# 

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
//set up the rest of the ServiceDescription
serviceDescription.DefaultMoveCost = MoveCost.Medium;
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

A szolgáltatás létrehozása után dinamikusan is megadható vagy frissíthető a MoveCost: 

PowerShell: 

```posh
Update-ServiceFabricService -Stateful -ServiceName "fabric:/AppName/ServiceName" -DefaultMoveCost High
```

C#

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.DefaultMoveCost = MoveCost.High;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/AppName/ServiceName"), updateDescription);
```

## <a name="dynamically-specifying-move-cost-on-a-per-replica-basis"></a>Az áthelyezési költségeket dinamikusan megadnia egy replika alapján

Az előző kódrészletek mind a teljes szolgáltatás MoveCost megadására szolgálnak egyszerre a szolgáltatáson kívülről. Az áthelyezési költségeket azonban akkor érdemes használni, ha egy adott szolgáltatási objektum mozgatási díja az élettartama alatt változik. Mivel a szolgáltatásoknak valószínűleg az a legjobb ötlete, hogy mennyire költségesek egy adott idő, van egy API a szolgáltatások számára, hogy a saját egyéni áthelyezési költségeit jelentse a futtatókörnyezet során. 

C#

```csharp
this.Partition.ReportMoveCost(MoveCost.Medium);
```

## <a name="impact-of-move-cost"></a>Az áthelyezési költségeket érintő hatás
A MoveCost öt szinttel rendelkezik: nulla, alacsony, közepes, magas és VeryHigh. A következő szabályok érvényesek:

* A MoveCosts egymáshoz viszonyítva, a nulla és a VeryHigh kivételével. 
* A nulla áthelyezési ár azt jelenti, hogy a mozgás ingyenes, és nem számít bele a megoldás pontszámára.
* Az áthelyezési díj magasra vagy VeryHigh való beállítása *nem* garantálja, hogy a replika *soha* nem lesz áthelyezve.
* A VeryHigh-áthelyezési költségeket tartalmazó replikák csak akkor lesznek áthelyezve, ha a fürtben nem lehet megerősíteni a korlátozás megsértését (még akkor is, ha a jogsértés kijavításához sok más replika áthelyezésére van szükség)



<center>

![A költségek mozgatása tényezőként a replikák mozgáshoz való kiválasztásakor][Image1]
</center>

A MoveCost segítségével megtalálhatja azokat a megoldásokat, amelyek a lehető legkevesebb megszakítást okozzák, és a legkönnyebb elérni, miközben továbbra is egyenértékű egyenlegre érkeznek. A szolgáltatásokra vonatkozó költségeket számos dologhoz lehet viszonyítani. Az áthelyezési díj kiszámításának leggyakoribb tényezője a következők:

- A szolgáltatás által áthelyezendő állapot vagy adatmennyiség.
- Az ügyfelek leválasztásának díja. Az elsődleges replika áthelyezése általában drágább, mint a másodlagos replika áthelyezésének díja.
- A repülés közbeni művelet megszakításának költségeit. Az adattároló szintjén vagy az ügyfél hívására adott válaszokban végrehajtott műveletek költségesek. Egy adott pont után nem kell leállítania őket, ha nem kell. Így amíg a művelet folyamatban van, megnövelheti ennek a szolgáltatási objektumnak a mozgatási költségeit, hogy csökkentse a mozgatásának valószínűségét. A művelet elvégzése után állítsa vissza a költségeket a normál értékre.

> [!IMPORTANT]
> A VeryHigh áthelyezési költségeit körültekintően kell figyelembe venni, mivel jelentős mértékben korlátozza a fürterőforrás-kezelő képességét arra, hogy globálisan optimális elhelyezési megoldást találjon a fürtben. A VeryHigh-áthelyezési költségeket tartalmazó replikák csak akkor lesznek áthelyezve, ha a fürtben nem lehet megerősíteni a korlátozás megsértését (még akkor is, ha a jogsértés kijavításához sok más replika áthelyezésére van szükség)

## <a name="enabling-move-cost-in-your-cluster"></a>Áthelyezési díjak engedélyezése a fürtben
Ahhoz, hogy a részletesebb MoveCosts figyelembe vegyék, engedélyezni kell a MoveCost a fürtben. Ezen beállítás nélkül a MoveCost kiszámításakor a rendszer az alapértelmezett leltározási módot használja, és a MoveCost-jelentéseket figyelmen kívül hagyja.


ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="UseMoveCostReports" Value="true" />
        </Section>
```

Önálló üzemelő példányokhoz vagy az Azure által üzemeltetett fürtökhöz Template.jsClusterConfig.json keresztül:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "UseMoveCostReports",
          "value": "true"
      }
    ]
  }
]
```

## <a name="next-steps"></a>Következő lépések
- Service Fabric fürterőforrás-kezelő metrikákat használ a fürt felhasználásának és kapacitásának kezeléséhez. Ha többet szeretne megtudni a metrikákkal és azok konfigurálásával kapcsolatban, tekintse meg az [erőforrás-felhasználás és a betöltés Service Fabric a metrikákkal](service-fabric-cluster-resource-manager-metrics.md)című témakört.
- Ha szeretné megtudni, hogy a fürterőforrás-kezelő hogyan kezeli és kiegyenlíti a fürt terhelését, tekintse meg a [Service Fabric-fürt kiegyensúlyozását](service-fabric-cluster-resource-manager-balancing.md)ismertető témakört.

[Image1]:./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png
