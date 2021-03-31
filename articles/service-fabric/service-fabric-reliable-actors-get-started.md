---
title: Actor-alapú szolgáltatás létrehozása az Azure Service Fabric
description: Megtudhatja, hogyan hozhat létre, kereshet és helyezhet üzembe első Actor-alapú szolgáltatást a C#-ban Service Fabric Reliable Actors használatával.
ms.topic: conceptual
ms.date: 07/10/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 225ccb67153a33ed47af68ebb1549dce37426278
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96573461"
---
# <a name="getting-started-with-reliable-actors"></a>A Reliable Actors első lépései
> [!div class="op_single_selector"]
> * [C# Windowson](service-fabric-reliable-actors-get-started.md)
> * [Java Linuxon](./service-fabric-create-your-first-linux-application-with-java.md)

Ez a cikk végigvezeti egy egyszerű, megbízható szereplő alkalmazás létrehozásán és hibakeresésén a Visual Studióban. További információ a Reliable Actorsről: [Service Fabric Reliable Actors bemutatása](service-fabric-reliable-actors-introduction.md).

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik a Service Fabric fejlesztési környezettel, beleértve a Visual studiót, amely a gépen van beállítva. Részletekért lásd: [a fejlesztési környezet beállítása](service-fabric-get-started.md).

## <a name="create-a-new-project-in-visual-studio"></a>Új projekt létrehozása a Visual Studióban

Indítsa el a Visual Studio 2019-es vagy újabb verzióját rendszergazdaként, majd hozzon létre egy új **Service Fabric alkalmazás** -projektet:

![Service Fabric Tools for Visual Studio – új projekt][1]

A következő párbeszédpanelen válassza a **Actor Service** elemet a **.net Core 2,0** területen, és adja meg a szolgáltatás nevét.

![Service Fabric Project-sablonok][5]

A létrehozott projekt a következő struktúrát jeleníti meg:

![Service Fabric projekt szerkezete][2]

## <a name="examine-the-solution"></a>A megoldás vizsgálata

A megoldás három projektet tartalmaz:

* **Az alkalmazás projektje (MyApplication)**. Ez a projekt az összes szolgáltatást együtt az üzembe helyezéshez is csomagolja. Az alkalmazás kezelésére szolgáló *ApplicationManifest.xml* és PowerShell-parancsfájlokat tartalmazza.

* **Az interface projekt (HelloWorld. interfaces)**. Ez a projekt tartalmazza a színész illesztőfelület-definícióját. A színészi felületek bármilyen nevű projektben meghatározhatók.  A csatoló határozza meg a Actors-szerződést, amelyet a színészi implementáció és a színészet hívó ügyfelek is megosztanak.  Mivel az ügyfelek projektjei függenek tőle, általában logikus, hogy egy olyan szerelvényben definiálják, amely eltér a szereplő implementációtól.

* **A actoring Service projekt (HelloWorld)**. Ez a projekt azt a Service Fabric szolgáltatást határozza meg, amely a szereplőt fogja üzemeltetni. A *HelloWorld. cs* című színész megvalósítását tartalmazza. A Actors implementáció egy olyan osztály, amely az alaptípusból származik `Actor` , és megvalósítja a *MyActor. interfaces* projektben definiált illesztőfelületeket. A Actors osztálynak olyan konstruktort is végre kell hajtania, amely elfogadja a `ActorService` példányt és `ActorId` a-t, és átadja azokat az `Actor` alaposztálynak.
    
    Ez a projekt a *program. cs* is tartalmazza, amely a Service Fabric Runtime használatával regisztrálja a színészi osztályokat `ActorRuntime.RegisterActorAsync<T>()` . Az `HelloWorld` osztály már regisztrálva van. A projekthez hozzáadott további Actors implementációkat is regisztrálni kell a `Main()` metódusban.

## <a name="customize-the-helloworld-actor"></a>A HelloWorld Actor testreszabása

A projekt sablonja meghatároz néhány módszert a `IHelloWorld` felületen, és implementálja azokat a `HelloWorld` színészi implementációban.  Cserélje le ezeket a metódusokat, hogy a Actor szolgáltatás egy egyszerű "Hello World" karakterláncot ad vissza.

A *HelloWorld. interfaces* projekt *IHelloWorld. cs* fájljában cserélje le az interfész definícióját a következőképpen:

```csharp
public interface IHelloWorld : IActor
{
    Task<string> GetHelloWorldAsync();
}
```

A **HelloWorld** projektben a **HelloWorld. cs** alatt cserélje le a teljes osztály definícióját a következőképpen:

```csharp
[StatePersistence(StatePersistence.Persisted)]
internal class HelloWorld : Actor, IHelloWorld
{
    public HelloWorld(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<string> GetHelloWorldAsync()
    {
        return Task.FromResult("Hello from my reliable actor!");
    }
}
```

Nyomja le a **CTRL + SHIFT + B** billentyűkombinációt a projekt létrehozásához, és győződjön meg róla, hogy minden fordítás.

## <a name="add-a-client"></a>Ügyfél hozzáadása

Hozzon létre egy egyszerű konzolos alkalmazást a Actor szolgáltatás meghívásához.

1. Kattintson a jobb gombbal a megoldásra megoldáskezelő >   >  **új projekt hozzáadása..**. elemre.

2. A **.net Core** -projekt típusai területen válassza a **Console app (.net Core)** lehetőséget.  Nevezze el a projekt *ActorClient*.
    
    ![Új projekt hozzáadása párbeszédpanel][6]    
    
    > [!NOTE]
    > A konzolos alkalmazások nem az alkalmazás olyan típusa, amelyet általában a Service Fabric-ügyfélként használ, de a helyi Service Fabric-fürt használatával a hibakereséshez és teszteléshez használható.

3. A konzol alkalmazásának 64 bitesnek kell lennie az illesztőfelület-projekttel és egyéb függőségekkel való kompatibilitás fenntartásához.  Megoldáskezelő kattintson a jobb gombbal a **ActorClient** projektre, majd kattintson a **Tulajdonságok** elemre.  A **build (létrehozás** ) lapon állítsa be a **platform célt** az **x64** értékre.
    
    ![Build tulajdonságai][8]

4. Az ügyfél-projekthez szükség van a megbízható Actors NuGet-csomagra.  Kattintson az **eszközök**  >  **NuGet Package** Manager  >  **csomagkezelő konzol** elemre.  A Package Manager konzolon adja meg a következő parancsot:
    
    ```powershell
    Install-Package Microsoft.ServiceFabric.Actors -IncludePrerelease -ProjectName ActorClient
    ```

    A NuGet-csomag és annak összes függősége telepítve van a ActorClient projektben.

5. Az ügyfél projekthez az illesztőfelületek projektre mutató hivatkozás is szükséges.  A ActorClient projektben kattintson a jobb gombbal a **függőségek** elemre, majd kattintson a **projekt-hivatkozás hozzáadása** parancsra.  Válassza a **projektek > a megoldás** elemet (ha még nincs kiválasztva), majd jelölje be a **HelloWorld. interfaces** elem melletti jelölőnégyzetet.  Kattintson az **OK** gombra.
    
    ![Hivatkozás hozzáadása párbeszédpanel][7]

6. A ActorClient projektben cserélje le a *program. cs* teljes tartalmát a következő kódra:
    
    ```csharp
    using System;
    using System.Threading.Tasks;
    using Microsoft.ServiceFabric.Actors;
    using Microsoft.ServiceFabric.Actors.Client;
    using HelloWorld.Interfaces;
    
    namespace ActorClient
    {
        class Program
        {
            static void Main(string[] args)
            {
                IHelloWorld actor = ActorProxy.Create<IHelloWorld>(ActorId.CreateRandom(), new Uri("fabric:/MyApplication/HelloWorldActorService"));
                Task<string> retval = actor.GetHelloWorldAsync();
                Console.Write(retval.Result);
                Console.ReadLine();
            }
        }
    }
    ```

## <a name="running-and-debugging"></a>Futtatás és hibakeresés

Nyomja le az **F5** billentyűt az alkalmazás helyi létrehozásához, üzembe helyezéséhez és futtatásához a Service Fabric fejlesztői fürtben.  A telepítési folyamat során a **kimenet** ablakban láthatja a folyamatot.

![Service Fabric hibakeresési kimeneti ablak][3]

Ha a kimenet tartalmazza a szöveget, *az alkalmazás készen áll*, a ActorClient alkalmazással tesztelheti a szolgáltatást.  Megoldáskezelő kattintson a jobb gombbal a **ActorClient** projektre, **majd kattintson az**  >  **új példány indítása** parancsra.  A parancssori alkalmazásnak a Actor szolgáltatás kimenetét kell megjelenítenie.

![Alkalmazás kimenete][9]

> [!TIP]
> A Service Fabric Actors Runtime a [színészi módszerekhez kapcsolódó eseményeket és](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters)teljesítményszámlálókat bocsát ki. Ezek a diagnosztika és a teljesítmény monitorozása során hasznosak.

## <a name="next-steps"></a>Következő lépések
További információ arról, [hogy Reliable Actors hogyan használják a Service Fabric platformot](service-fabric-reliable-actors-platform.md).


[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG
[6]: ./media/service-fabric-reliable-actors-get-started/new-console-app.png
[7]: ./media/service-fabric-reliable-actors-get-started/add-reference.png
[8]: ./media/service-fabric-reliable-actors-get-started/build-props.png
[9]: ./media/service-fabric-reliable-actors-get-started/app-output.png
