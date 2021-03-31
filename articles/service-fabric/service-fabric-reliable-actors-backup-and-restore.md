---
title: Azure Service Fabric Actors biztonsági mentése és visszaállítása
description: Ismerje meg, hogyan implementálhatja a biztonsági mentést és a visszaállítást az Azure Service Fabric Actors szolgáltatásban.
ms.topic: conceptual
ms.date: 10/29/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 05dfb7d09e68373f52831a9e5233316df95ee552
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96571334"
---
# <a name="implement-reliable-actors-backup-and-restore"></a>Reliable Actors biztonsági mentés és visszaállítás megvalósítása

> [!NOTE]
> A Microsoft azt javasolja, hogy [rendszeres biztonsági mentést és visszaállítást](service-fabric-backuprestoreservice-quickstart-azurecluster.md) használjon a megbízható állapot-nyilvántartó szolgáltatások és Reliable Actors biztonsági mentésének konfigurálásához. 
> 

A következő példában egy egyéni Actor szolgáltatás egy metódust tesz elérhetővé a Actor-adatbiztonsági mentéshez, és kihasználja a már meglévő távelérés-figyelő előnyeit `ActorService` :

```csharp
public interface IMyActorService : IService
{
    Task BackupActorsAsync();
}

class MyActorService : ActorService, IMyActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }

    public Task BackupActorsAsync()
    {
        return this.BackupAsync(new BackupDescription(PerformBackupAsync));
    }

    private async Task<bool> PerformBackupAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
    {
        try
        {
           // store the contents of backupInfo.Directory
           return true;
        }
        finally
        {
           Directory.Delete(backupInfo.Directory, recursive: true);
        }
    }
}
```
```Java
public interface MyActorService extends Service
{
    CompletableFuture<?> backupActorsAsync();
}

class MyActorServiceImpl extends ActorService implements MyActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<FabricActorService, ActorId, ActorBase> newActor)
    {
       super(context, typeInfo, newActor);
    }

    public CompletableFuture backupActorsAsync()
    {
        return this.backupAsync(new BackupDescription((backupInfo, cancellationToken) -> performBackupAsync(backupInfo, cancellationToken)));
    }

    private CompletableFuture<Boolean> performBackupAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
    {
        try
        {
           // store the contents of backupInfo.Directory
           return true;
        }
        finally
        {
           deleteDirectory(backupInfo.Directory)
        }
    }

    void deleteDirectory(File file) {
        File[] contents = file.listFiles();
        if (contents != null) {
            for (File f : contents) {
               deleteDirectory(f);
             }
        }
        file.delete();
    }
}
```

Ebben a példában `IMyActorService` egy olyan távelérési szerződés `IService` (C#) és `Service` (Java), amelyet a implementál `MyActorService` . A távelérési szerződés hozzáadásával a metódusok `IMyActorService` mostantól elérhetők az ügyfelek számára a következő módon `ActorServiceProxy` :

```csharp
IMyActorService myActorServiceProxy = ActorServiceProxy.Create<IMyActorService>(
    new Uri("fabric:/MyApp/MyService"), ActorId.CreateRandom());

await myActorServiceProxy.BackupActorsAsync();
```
```Java
MyActorService myActorServiceProxy = ActorServiceProxy.create(MyActorService.class,
    new URI("fabric:/MyApp/MyService"), actorId);

myActorServiceProxy.backupActorsAsync();
```

A Reliable Actorsról a következő cikkekben olvashat bővebben:
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
