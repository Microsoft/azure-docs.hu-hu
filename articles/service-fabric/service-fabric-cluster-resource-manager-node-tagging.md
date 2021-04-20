---
title: Azure Service Fabric csomópontcímkék
description: Az Azure Service Fabric lehetővé teszi csomópontcímkék dinamikus hozzáadását és eltávolítását.
author: yu-supersonic
ms.topic: conceptual
ms.date: 04/05/2021
ms.author: branim
ms.custom: devx-track-csharp
ms.openlocfilehash: 712e6422060619e5567a74d6335320eff9ed8e66
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741744"
---
# <a name="introduction-to-dynamic-node-tags"></a>A dinamikus csomópontcímkék bemutatása
A csomópontcímkék lehetővé teszik a címkék dinamikus hozzáadását és eltávolítását a csomópontokon a szolgáltatások elhelyezésének befolyásolása érdekében. A csomópontcímkézés rendkívül rugalmas, és lehetővé teszi a szolgáltatáselhelyezés alkalmazás- vagy fürtfrissítések nélküli módosításait. A csomópontok címkéi bármikor hozzáadhatók vagy eltávolíthatók, és a szolgáltatások meghatároznak bizonyos címkékre vonatkozó követelményeket a létrehozásukkor. Egy szolgáltatás címkekövetelményei futás közben dinamikusan is frissíthetők.

A csomópontok címkézése hasonló [az](service-fabric-cluster-resource-manager-configure-services.md) elhelyezési korlátozásokhoz, és általában a szolgáltatás által futtatott csomópontok szabályozására használatos. Minden Service Fabric konfigurálható úgy, hogy a címkét el kell helyezni vagy tovább futtasa.

A csomópontok címkézése minden üzemeltetett Service Fabric (Reliable Services, futtatható vendég és tárolók) esetében támogatott. A csomópont-címkézéshez a futtatókörnyezet 8.0-s vagy újabb Service Fabric kell futnia.

A cikk további részében a csomópontok címkézésének engedélyezésére vagy letiltására, valamint a funkció használatára mutat be példákat.


## <a name="describing-dynamic-node-tags"></a>Dinamikus csomópontcímkék leírása
A szolgáltatások megadhatják a szükséges címkéket. A címkéknek két típusa van:
* **Az elhelyezéshez szükséges címkék** címkéket írnak le, amelyek csak szolgáltatáselhelyezéshez szükségesek. A replika elhelyezése után ezek a címkék a szolgáltatás megszakítása nélkül távolíthatók el. Ha ezen címkék bármelyikét eltávolítja a csomópontról, a szolgáltatásreplika továbbra is működni fog, és Service Fabric eltávolítja a szolgáltatást

* **A futtatáshoz szükséges** címkék címkéket írnak le, amelyek a szolgáltatás elhelyezéséhez és futtatásához is szükségesek. Ha a szükséges futó címkék bármelyikét eltávolítja, a Service Fabric áthelyezi a szolgáltatást egy másik csomópontra, ahol meg vannak adva ezek a címkék.

Példa: Az elhelyezési címkékhez szükséges akkor használható, ha valamilyen tárolóaktiváló szolgáltatást használ, és szükség van arra, hogy a tároló szolgáltatása el legyen helyezve, és a tároló aktiválása után már nincs szükség aktiválásra, és eltávolíthatja a hozzá társított címkét, de a tárolónak tovább kell futnia.
A címkék futtatásához szükséges akkor használható, ha számlázási szolgáltatással van, ami hasznos lehet, ha a felhasználó által használt szolgáltatással együtt kell lennie. Ha a számlázási szolgáltatás meghibásodik a csomóponton, eltávolítja a hozzá társított címkét, és a felhasználó által felé néző szolgáltatás egy másik csomópontra kerül, amely rendelkezik számlázási szolgáltatással és címkével.

Egy címke vagy címkekészlet hozzáadható, frissíthető vagy eltávolítható egyetlen csomópontról szabványos Service Fabric-felületi mechanizmusokkal, például C#-API-okkal, REST API-okkal vagy PowerShell-parancsokkal.

> [!NOTE]
> Service Fabric csomópontcímkék használata esetén nem tart fenn UD/FD-elosztásokat. Megfelelően kezelje a csomópontcímkéket, hogy a címkék tartományok közötti hibás elosztása miatt ne legyen szükség FD-/UD-szabálysértésekre.

## <a name="enabling-dynamic-node-tags"></a>Dinamikus csomópontcímkék engedélyezése
Ahhoz, hogy ez a funkció működjön, engedélyeznie kell a NodeTaggingEnabled konfigurációt a fürtjegyzék PlacementAndLoadBalancing szakaszában xml vagy JSON használatával:

``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="NodeTaggingEnabled" Value="true" />
</Section>
```

az ClusterConfig.jsüzemelő példányok vagy az Azure-Template.jsüzemelő fürtök esetén a on (be) kapcsolaton keresztül:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": " NodeTaggingEnabled ",
          "value": "true"
      }
    ]
  }
]
```

## <a name="setting-dynamic-node-tags"></a>Dinamikus csomópontcímkék beállítása

### <a name="using-powershell"></a>A PowerShell használata

Csomópontcímkék hozzáadása a csomóponthoz:

```posh
Add-ServiceFabricNodeTags -NodeName "DB.1" -NodeTags @("SampleTag1", "SampleTag2")
```
Ez a parancs hozzáadja a "SampleTag1" és a "SampleTag2" címkéket a DB.1 csomóponton.

Csomópontcímkék eltávolítása a csomópontból:

```posh
Remove-ServiceFabricNodeTags -NodeName "DB.1" -NodeTags @("SampleTag1", "SampleTag2")
```
Ez a parancs eltávolítja a "SampleTag1" és "SampleTag2" címkéket a DB.1 csomóponton.

### <a name="using-c-apis"></a>C#-API-k használata

Csomópontcímkék hozzáadása a csomóponthoz:

```csharp
FabricClient fabricClient = new FabricClient();
List<string> nodeTagsList = new List<string>();
nodeTagsList.Add("SampleTag1");
nodeTagsList.Add("SampleTag2");
await fabricClient.ClusterManager.AddNodeTagsAsync("DB.1", nodeTagsList);
```

Csomópontcímkék eltávolítása a csomópontból:

```csharp
FabricClient fabricClient = new FabricClient();
List<string> nodeTagsList = new List<string>();
nodeTagsList.Add("SampleTag1");
nodeTagsList.Add("SampleTag2");
await fabricClient.ClusterManager.RemoveNodeTagsAsync("DB.1", nodeTagsList);
```

## <a name="setting-required-tags-for-services"></a>A szolgáltatásokhoz szükséges címkék beállítása

### <a name="using-powershell"></a>A PowerShell használata

Új szolgáltatás létrehozása:

```posh
New-ServiceFabricService -ApplicationName fabric:/HelloWorld -ServiceName fabric:/HelloWorld/svc1 -ServiceTypeName HelloWorldStateful -Stateful -PartitionSchemeSingleton -TargetReplicaSetSize 5 -MinReplicaSetSize 3 -TagsRequiredToRun @("SampleTag1") - TagsRequiredToPlace @("SampleTag2")
```
Ez a parancs létrehoz egy szolgáltatást, amely megköveteli, hogy a "SampleTag2" jelen legyen egy csomóponton ahhoz, hogy a szolgáltatás ott legyen, és a "SampleTag1" is jelen legyen ahhoz, hogy a szolgáltatás tovább futjon ezen a csomóponton.

Meglévő szolgáltatás frissítése:

```posh
Update-ServiceFabricService -Stateful -ServiceName fabric:/myapp/test -TagsRequiredToRun @("SampleTag1") -TagsRequiredToPlace @("SampleTag2")
```
Ez a parancs frissíti a szolgáltatást, amelyhez a "SampleTag2" parancsnak jelen kell lennie egy csomóponton ahhoz, hogy a szolgáltatás ott legyen elhelyezve, és a "SampleTag1" is jelen legyen ahhoz, hogy a szolgáltatás tovább futjon ezen a csomóponton.

### <a name="using-c-apis"></a>C#-API-k használata

Új szolgáltatás létrehozása:

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
//set up the rest of the ServiceDescription
ServiceTags serviceTags = new ServiceTags();
serviceTags.TagsRequiredToPlace.Add("SampleTag1");
serviceTags.TagsRequiredToRun.Add("SampleTag2");
serviceDescription.ServiceTags = serviceTags;
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Meglévő szolgáltatás frissítése:

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceUpdateDescription serviceUpdate = new StatefulServiceUpdateDescription();
ServiceTags serviceTags = new ServiceTags();
serviceTags.TagsRequiredToPlace.Add("SampleTag1");
serviceTags.TagsRequiredToRun.Add("SampleTag2");
serviceUpdate.ServiceTags = serviceTags;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/AppName/ServiceName"), serviceUpdate);
```

Ezek a parancsok mind egyformán érvényesek az állapot nélküli szolgáltatásokra.

## <a name="next-steps"></a>Következő lépések
További információ az [elhelyezési korlátozásokról](service-fabric-cluster-resource-manager-configure-services.md)
