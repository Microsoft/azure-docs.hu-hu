---
title: Szolgáltatások megvalósítása az Azure Service Fabric Actors szolgáltatásban
description: Leírja, hogyan írhat saját Actor-szolgáltatást, amely ugyanúgy valósítja meg a szolgáltatási szintű szolgáltatásokat, mint amikor a StatefulService örökli.
ms.topic: conceptual
ms.date: 03/19/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: d39ec93e0ad03d6c860bae9d0790e860c95457a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96575567"
---
# <a name="implement-service-level-features-in-your-actor-service"></a>A szolgáltatás szintű szolgáltatások implementálása a Actor Service-ben

A [szolgáltatási rétegben](service-fabric-reliable-actors-platform.md#service-layering)leírtak szerint a Actor szolgáltatás maga is megbízható szolgáltatás. A ból származó saját szolgáltatást írhat `ActorService` . A szolgáltatási szintű szolgáltatásokat ugyanúgy is megvalósíthatja, mint az állapot-nyilvántartó szolgáltatás öröklése esetén, például:

- Szolgáltatás biztonsági mentése és visszaállítása.
- Megosztott funkciók minden résztvevő számára, például egy áramkör-megszakító.
- A távoli eljárás magára a színészi szolgáltatásra, és minden egyes színészre hívja fel a hívást.

## <a name="use-the-actor-service"></a>A Actors szolgáltatás használata

A színészi példányok hozzáférnek a Actors szolgáltatáshoz, amelyben futnak. A Actors szolgáltatással a színészi példányok programozott módon szerezhetik be a szolgáltatási környezetet. A szolgáltatási környezet rendelkezik a partíció-AZONOSÍTÓval, a szolgáltatás nevével, az alkalmazás nevével és más Azure Service Fabric platform-specifikus információkkal.

```csharp
Task MyActorMethod()
{
    Guid partitionId = this.ActorService.Context.PartitionId;
    string serviceTypeName = this.ActorService.Context.ServiceTypeName;
    Uri serviceInstanceName = this.ActorService.Context.ServiceName;
    string applicationInstanceName = this.ActorService.Context.CodePackageActivationContext.ApplicationName;
}
```
```Java
CompletableFuture<?> MyActorMethod()
{
    UUID partitionId = this.getActorService().getServiceContext().getPartitionId();
    String serviceTypeName = this.getActorService().getServiceContext().getServiceTypeName();
    URI serviceInstanceName = this.getActorService().getServiceContext().getServiceName();
    String applicationInstanceName = this.getActorService().getServiceContext().getCodePackageActivationContext().getApplicationName();
}
```

Mint minden Reliable Services, a Actor szolgáltatást a Service Fabric Runtime szolgáltatásban kell regisztrálni. A Actors-példányok futtatásához a színészi típust is regisztrálni kell a Actor szolgáltatásban. Az aktorok esetében ezt a feladatot az `ActorRuntime` regisztrációs metódus végzi el. A legegyszerűbb esetben regisztrálhatja a színész típusát, a Actor szolgáltatás pedig az alapértelmezett beállításokat használja.

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>().GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```

Azt is megteheti, hogy a regisztrációs módszer által biztosított lambda használatával saját maga is létrehozza a Actors szolgáltatást. Ezután konfigurálhatja a Actors szolgáltatást, és explicit módon létrehozhatja a színészi példányokat. A függőségeket a konstruktorán keresztül adhatja be a színésznek.

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new ActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```
```Java
static class Program
{
    private static void Main()
    {
      ActorRuntime.registerActorAsync(
              MyActor.class,
              (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
              timeout);

        Thread.sleep(Long.MAX_VALUE);
    }
}
```

## <a name="actor-service-methods"></a>Actor Service-metódusok

A actoring szolgáltatás implementálja `IActorService` (c#) vagy `ActorService` (Java), ami implementálja a ( `IService` c#) vagy a `Service` (Java) szolgáltatást. Ezt a felületet a Reliable Services távelérési szolgáltatás használja, amely lehetővé teszi a távoli eljáráshívási hívásokat a szolgáltatási módszerekhez. Olyan szolgáltatási szintű metódusokat tartalmaz, amelyek távolról is meghívhatók a szolgáltatás távelérésén keresztül. Használhatja a szereplők [enumerálására](service-fabric-reliable-actors-enumerate.md) és [törlésére](service-fabric-reliable-actors-delete-actors.md) .


## <a name="custom-actor-service"></a>Egyéni Actor szolgáltatás

A Actor-regisztráció lambda használatával regisztrálhatja a saját, `ActorService` C# és a (Java) rendszerből származtatott egyéni Actor-szolgáltatást `FabricActorService` . Ezután implementálhatja a saját szolgáltatási szintű funkcióit úgy, hogy az örökölt `ActorService` (C#) vagy a `FabricActorService` (Java) szolgáltatási osztályt ír. Az egyéni Actor szolgáltatás örökli az összes Actor Runtime funkciót a `ActorService` (C#) vagy a `FabricActorService` (Java) használatával. Saját szolgáltatási módszerek megvalósítására is használható.

```csharp
class MyActorService : ActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }
}
```
```Java
class MyActorService extends FabricActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, BiFunction<FabricActorService, ActorId, ActorBase> newActor)
    {
         super(context, typeInfo, newActor);
    }
}
```

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new MyActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```
```Java
public class Program
{
    public static void main(String[] args)
    {
        ActorRuntime.registerActorAsync(
                MyActor.class,
                (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
                timeout);
        Thread.sleep(Long.MAX_VALUE);
    }
}
```

## <a name="implement-actor-backup-and-restore"></a>A színész biztonsági mentésének és visszaállításának implementálása

Az egyéni Actors szolgáltatás lehetővé teszi, hogy a-ben már meglévő távelérési figyelő előnyeit kihasználva egy metódust nyújtson a Actor-adatbiztonsági mentéshez `ActorService` . Példát a következő témakörben talál: [biztonsági másolatok és visszaállítási szereplők](service-fabric-reliable-actors-backup-and-restore.md).

## <a name="actor-that-uses-a-remoting-v2-interface-compatible-stack"></a>Egy, a távelérési szolgáltatással (kompatibilis csatolóval) elhasználó

A távelérési szolgáltatás (V2_1) verem a v2 távelérési verem összes funkcióját tartalmazza. A felülete kompatibilis a távelérés v1-es verziójával, de nem kompatibilis a v2 és a v1 verzióval. Ha a v1-ről V2_1ra szeretne frissíteni a szolgáltatás rendelkezésre állásának hatása nélkül, kövesse a következő szakaszban ismertetett lépéseket.

A következő módosítások szükségesek a távelérési V2_1 verem használatához:

1. Adja hozzá a következő Assembly attribútumot a Actor Interfaces-hez.
  
   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
   ```

2. A v2-verem használatának megkezdéséhez hozzon létre és frissítse a Actor Service-t és a színészi ügyfél-projekteket.

### <a name="actor-service-upgrade-to-remoting-v2-interface-compatible-stack-without-affecting-service-availability"></a>A Actors szolgáltatás a szolgáltatás rendelkezésre állásának befolyásolása nélkül frissítheti a távelérési szolgáltatást (a csatolóval kompatibilis)

Ez a változás egy kétlépéses frissítés. Kövesse a jelen szakasz lépéseit.

1. Adja hozzá a következő Assembly attribútumot a Actor Interfaces-hez. Ez az attribútum két figyelőt indít el a Actor szolgáltatáshoz, a v1 (meglévő) és a V2_1 figyelőhöz. Frissítse a Actor szolgáltatást ezzel a módosítással.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
   ```

2. A korábbi frissítés befejezése után frissítse a Actor-ügyfeleket.
   Ez a lépés biztosítja, hogy a Actor proxy a távelérési V2_1 veremét használja.

3. Ez a lépés nem kötelező. Módosítsa az előző attribútumot a v1-figyelő eltávolításához.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
    ```

## <a name="actor-that-uses-the-remoting-v2-stack"></a>A távelérési v2-veremet használó színész

A 2,8-es verzió NuGet csomaggal a felhasználók mostantól a távelérés v2-veremét is használhatják, amely jobban teljesít, és olyan funkciókat biztosít, mint például az egyéni szerializálás. A távelérési v2 nem kompatibilis visszafelé a meglévő távelérési veremmel (mostantól v1 távelérési veremként).

Az alábbi módosítások szükségesek a távelérési v2-verem használatához.

1. Adja hozzá a következő Assembly attribútumot a Actor Interfaces-hez.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
   ```

2. Felépítheti és frissítheti a Actors szolgáltatást és a színészi ügyfél-projekteket a v2-verem használatának megkezdéséhez.

### <a name="upgrade-the-actor-service-to-the-remoting-v2-stack-without-affecting-service-availability"></a>A szolgáltatás rendelkezésre állásának befolyásolása nélkül frissítse a Actors szolgáltatást a távoli eljáráshívás v2-verembe

Ez a változás egy kétlépéses frissítés. Kövesse a jelen szakasz lépéseit.

1. Adja hozzá a következő Assembly attribútumot a Actor Interfaces-hez. Ez az attribútum két figyelőt indít el a Actor szolgáltatáshoz, a v1 (meglévő) és a v2-figyelőhöz. Frissítse a Actor szolgáltatást ezzel a módosítással.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
   ```

2. A korábbi frissítés befejezése után frissítse a Actor-ügyfeleket.
   Ez a lépés gondoskodik arról, hogy a Actor proxy a távelérési v2-veremet használja.

3. Ez a lépés nem kötelező. Módosítsa az előző attribútumot a v1-figyelő eltávolításához.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
    ```

## <a name="next-steps"></a>Következő lépések

* [Színészi állapot kezelése](service-fabric-reliable-actors-state-management.md)
* [A Actor életciklusa és a szemét gyűjtése](service-fabric-reliable-actors-lifecycle.md)
* [A Actors API-dokumentációja](/previous-versions/azure/dn971626(v=azure.100))
* [.NET-mintakód](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java-mintakód](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
