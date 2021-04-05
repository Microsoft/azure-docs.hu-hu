---
title: Reliable Actors on Service Fabric
description: Leírja, hogy a Reliable Actors hogyan vannak rétegben Reliable Services és hogyan használják a Service Fabric platform funkcióit.
ms.topic: conceptual
ms.date: 3/9/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: e204b47b7fa9f528341507c315c926159d524e13
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98789582"
---
# <a name="how-reliable-actors-use-the-service-fabric-platform"></a>Hogyan Reliable Actors használni a Service Fabric platformot?
Ez a cikk azt ismerteti, hogy Reliable Actors hogyan működik az Azure Service Fabric platformon. A Reliable Actors egy olyan keretrendszerben fut, amely egy, a *Actors szolgáltatásnak* nevezett, állapot-nyilvántartó megbízható szolgáltatás implementációjában található. A Actors szolgáltatás tartalmazza az életciklus és az üzenetek a szereplőkkel való elküldésének kezeléséhez szükséges összes összetevőt:

* A színész futtatókörnyezete kezeli az életciklusokat, a Garbage gyűjteményt, és kikényszeríti az egyszálas hozzáférést.
* A Actors szolgáltatás távelérési figyelője fogadja a résztvevők számára a távelérési hívásokat, és elküldi őket egy diszpécsernek a megfelelő Actor-példányra való átirányításhoz.
* A szereplők állami szolgáltatója becsomagolja az állami szolgáltatókat (például a megbízható gyűjtemények állami szolgáltatóját), és egy adaptert biztosít a színészi állapot kezeléséhez.

Ezek az összetevők együtt alkotják a megbízható színészi keretrendszert.

## <a name="service-layering"></a>Szolgáltatás-réteg
Mivel a Actors szolgáltatás maga egy megbízható szolgáltatás, az összes [alkalmazás modellje](service-fabric-application-model.md), életciklusa, [csomagolása](service-fabric-package-apps.md), [üzembe helyezése](service-fabric-deploy-remove-applications.md), frissítése és méretezési fogalma ugyanúgy alkalmazza a Reliable Services a Actor Servicesre.

![Színészi szolgáltatás rétegződése][1]

Az előző ábrán látható a Service Fabric alkalmazás-keretrendszerek és a felhasználói kód közötti kapcsolat. A kék elemek a Reliable Services alkalmazás-keretrendszer, a narancssárga a megbízható Actors keretrendszert képviselik, a zöld pedig a felhasználói kódot jelöli.

Reliable Services a szolgáltatás örökli az `StatefulService` osztályt. Ez az osztály a `StatefulServiceBase` (vagy `StatelessService` az állapot nélküli szolgáltatások esetében) származik. Reliable Actors a Actors szolgáltatást használja. A színészi szolgáltatás az osztály egy másik implementációja `StatefulServiceBase` , amely megvalósítja a színészi mintát, amelyben a szereplők futnak. Mivel maga a színészi szolgáltatás maga a implementációja `StatefulServiceBase` , a saját szolgáltatását is megírhatja, amely `ActorService` a szolgáltatás szintjének származtatása és megvalósítása ugyanúgy történik, mint az örökléskor `StatefulService` , például:

* Szolgáltatás biztonsági mentése és visszaállítása.
* Megosztott funkciók minden résztvevő számára, például egy áramkör-megszakító.
* A távoli eljárás magára a színészi szolgáltatásra, és minden egyes színészre hívja fel a hívást.

További információ: [a szolgáltatás szintű szolgáltatások megvalósítása a Actor Service-ben](service-fabric-reliable-actors-using.md).

## <a name="application-model"></a>Alkalmazásmodell
A Actor Services Reliable Services, így az alkalmazás modellje ugyanaz. A Actor Framework-build eszközei azonban létrehozzák az alkalmazás-modell fájljait.

### <a name="service-manifest"></a>Szolgáltatás jegyzékfájlja
A Actor Framework build eszközei automatikusan létrehozzák a Actors szolgáltatás ServiceManifest.xml fájljának tartalmát. Ez a fájl a következőket tartalmazza:

* Actor szolgáltatás típusa. A típus nevét a színész projekt neve alapján hozza létre a rendszer. A színész adatmegőrzési attribútuma alapján a HasPersistedState jelző is ennek megfelelően van beállítva.
* Kód csomag.
* Konfigurációs csomag.
* Erőforrások és végpontok.

### <a name="application-manifest"></a>Alkalmazásjegyzék
A színészi keretrendszer build eszközei automatikusan létrehozzák a Actor szolgáltatás alapértelmezett szolgáltatási definícióját. A Build Tools feltölti az alapértelmezett szolgáltatás tulajdonságait:

* A kópiakészlet darabszámát a színész adatmegőrzési attribútuma határozza meg. Minden alkalommal, amikor megváltoznak a színész adatmegőrzési attribútuma, a rendszer az alapértelmezett szolgáltatási definícióban lévő replikakészlet-készletet ennek megfelelően alaphelyzetbe állítja.
* A partíciós séma és a tartomány egységes Int64 van beállítva a teljes Int64.

## <a name="service-fabric-partition-concepts-for-actors"></a>Service Fabric a résztvevők partíciós fogalmait
A Actor Services particionált állapot-nyilvántartó szolgáltatásokat nyújt. A Actors szolgáltatás minden partíciója tartalmaz Actors készletet. A szolgáltatási partíciók automatikusan a Service Fabric több csomópontján vannak elosztva. A Actor instances eloszlása ennek eredményeképpen történik.

![Színészek particionálása és elosztása][5]

A Reliable Services különböző partíciós sémákkal és partíciós kulcs-tartományokkal hozhatók létre. A Actors szolgáltatás a Int64 particionálási sémát használja a teljes Int64, amely a szereplők partíciók közötti leképezésére szolgál.

### <a name="actor-id"></a>Színész azonosítója
A szolgáltatásban létrehozott minden egyes szereplőhöz egyedi azonosító tartozik, amelyet az `ActorId` osztály képvisel. `ActorId` egy átlátszatlan azonosító érték, amely véletlenszerű azonosítók létrehozásával használható a szolgáltatások partícióinak egységes elosztására:

```csharp
ActorProxy.Create<IMyActor>(ActorId.CreateRandom());
```
```Java
ActorProxyBase.create<MyActor>(MyActor.class, ActorId.newId());
```


Minden `ActorId` kivonat egy Int64. Ezért a Actor szolgáltatásnak egy Int64 particionálási sémát kell használnia a teljes Int64-tartományhoz. Az egyéni azonosító értékek azonban használhatók egy-hoz `ActorID` , beleértve a GUID-ket, az UUID-ket, a karakterláncokat és a Int64s is.

```csharp
ActorProxy.Create<IMyActor>(new ActorId(Guid.NewGuid()));
ActorProxy.Create<IMyActor>(new ActorId("myActorId"));
ActorProxy.Create<IMyActor>(new ActorId(1234));
```
```Java
ActorProxyBase.create(MyActor.class, new ActorId(UUID.randomUUID()));
ActorProxyBase.create(MyActor.class, new ActorId("myActorId"));
ActorProxyBase.create(MyActor.class, new ActorId(1234));
```

GUID-azonosítók/UUID-kódok és karakterláncok használatakor az értékek kivonatolása egy Int64 történik. Ha azonban explicit módon ad meg egy Int64 `ActorId` , a Int64 további kivonatolás nélkül leképezi közvetlenül a partícióra. Ezzel a technikával szabályozhatja, hogy a szereplők milyen partíciót helyeznek el.


## <a name="next-steps"></a>Következő lépések
* [Színészi állapot kezelése](service-fabric-reliable-actors-state-management.md)
* [A Actor életciklusa és a szemét gyűjtése](service-fabric-reliable-actors-lifecycle.md)
* [A Actors API-dokumentációja](/dotnet/api/microsoft.servicefabric.actors)
* [.NET-mintakód](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java-mintakód](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
