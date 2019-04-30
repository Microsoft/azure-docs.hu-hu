---
title: Az Azure Service Fabricban aktorok enumerálása |} A Microsoft Docs
description: További tudnivalók a Reliable Actors és azok metaadatainak számbavétele.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: amanbha
ms.assetid: 45839a7f-0536-46f1-ae2b-8ba3556407fb
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 04e2c32b18e6897d6443fea68587aba9ae294be5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60729137"
---
# <a name="enumerate-service-fabric-reliable-actors"></a>Service Fabric Reliable Actors számbavétele
A Reliable Actors-szolgáltatás lehetővé teszi, hogy egy ügyfél metaadatait, a szereplők a szolgáltatást üzemeltető számbavétele. Mivel az aktorszolgáltatás particionált az állapotalapú szolgáltatások, a enumerálás partíciónként történik. Mindegyik partíció tartalmazhat számos actors, mert az enumerálás sorozataként lapokra bontott eredményeket adja vissza. A lapok vannak beállításpanelen keresztül, addig, amíg az összes olvasható. Az alábbi példa bemutatja, hogyan hozhat létre az összes aktív szereplők listáját egyik partíciójában, az aktorszolgáltatás:

```csharp
IActorService actorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), partitionKey);

ContinuationToken continuationToken = null;
List<ActorInformation> activeActors = new List<ActorInformation>();

do
{
    PagedResult<ActorInformation> page = await actorServiceProxy.GetActorsAsync(continuationToken, cancellationToken);

    activeActors.AddRange(page.Items.Where(x => x.IsActive));

    continuationToken = page.ContinuationToken;
}
while (continuationToken != null);
```

```Java
ActorService actorServiceProxy = ActorServiceProxy.create(
    new URI("fabric:/MyApp/MyService"), partitionKey);

ContinuationToken continuationToken = null;
List<ActorInformation> activeActors = new ArrayList<ActorInformation>();

do
{
    PagedResult<ActorInformation> page = actorServiceProxy.getActorsAsync(continuationToken);

    while(ActorInformation x: page.getItems())
    {
         if(x.isActive()){
              activeActors.add(x);
         }
    }

    continuationToken = page.getContinuationToken();
}
while (continuationToken != null);
```



## <a name="next-steps"></a>További lépések
* [Aktor állapotkezelés](service-fabric-reliable-actors-state-management.md)
* [Actors-életciklus-kezelés és szemétgyűjtés gyűjtemény](service-fabric-reliable-actors-lifecycle.md)
* [Aktorok API dokumentációja](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [.NET mintakód](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java-mintakód](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
