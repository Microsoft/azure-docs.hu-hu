---
title: 'Az első Service Fabric-alkalmazás létrehozása a C-ben #'
description: Bevezetés az állapot nélküli és állapot-nyilvántartó szolgáltatásokkal Microsoft Azure Service Fabric alkalmazás létrehozásához.
ms.topic: conceptual
ms.date: 07/10/2019
ms.custom: sfrev, devx-track-csharp
ms.openlocfilehash: 45341c98a40cbcabfa8b96f2016f02f1755fe2b3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98791527"
---
# <a name="get-started-with-reliable-services"></a>Ismerkedés a Reliable Services használatával

> [!div class="op_single_selector"]
> * [C# Windowson](service-fabric-reliable-services-quick-start.md)
> * [Java Linuxon](service-fabric-reliable-services-quick-start-java.md)

Egy Azure Service Fabric-alkalmazás egy vagy több olyan szolgáltatást tartalmaz, amely a kódot futtatja. Ebből az útmutatóból megtudhatja, hogyan hozhat létre állapot nélküli és állapot-nyilvántartó Service Fabric alkalmazásokat [Reliable Services](service-fabric-reliable-services-introduction.md)használatával.  

## <a name="basic-concepts"></a>Alapfogalmak

A Reliable Services megkezdéséhez csak néhány alapvető fogalmat kell megismernie:

* **Szolgáltatás típusa**: Ez a szolgáltatás implementálása. A nevet a megjelenő osztály határozza meg, amely kiterjeszti, `StatelessService` valamint az ott használt egyéb kódokat vagy függőségeket, valamint a nevét és verziószámát.
* **Nevesített Service-példány**: a szolgáltatás futtatásához a szolgáltatás típusának elnevezett példányait kell létrehoznia, hasonlóan ahhoz, mint egy adott típusú objektum példányának létrehozásakor. A szolgáltatási példány neve URI formátumú, a "Fabric:/" használatával séma, például "Fabric:/SajátPr/MyService".
* **Service Host**: a létrehozott elnevezett szolgáltatás-példányokat a gazdagépen belül kell futtatni. A szolgáltatás gazdagépe csak egy folyamat, ahol a szolgáltatás példányai futhatnak.
* **Szolgáltatás regisztrálása**: a regisztráció minden együtt jár. A szolgáltatási típust regisztrálni kell a Service Host Service Fabric futtatókörnyezetében, hogy a Service Fabric példányokat hozzon létre a futtatásához.  

## <a name="create-a-stateless-service"></a>Állapot nélküli szolgáltatás létrehozása

Az állapot nélküli szolgáltatás olyan típusú szolgáltatás, amely jelenleg a Felhőbeli alkalmazásokhoz tartozó szabvány. A rendszer állapot nélkülinek tekinti, mivel maga a szolgáltatás nem tartalmaz megbízhatóan vagy nagyon elérhetővé tenni kívánt adattárolást. Ha egy állapot nélküli szolgáltatás egy példánya leáll, az összes belső állapota elvész. Ebben a típusú szolgáltatásban az állapotot egy külső tárolóban (például Azure-táblákban vagy SQL Database) kell megőrizni ahhoz, hogy az informatikai szervezet számára elérhető és megbízható legyen.

Indítsa el a Visual Studio 2017 vagy a Visual Studio 2019 alkalmazást rendszergazdaként, és hozzon létre egy új Service Fabric *HelloWorld* nevű alkalmazás-projektet:

![Új Service Fabric-alkalmazás létrehozása az új projekt párbeszédpanel használatával](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject.png)

Ezután hozzon létre egy állapot nélküli szolgáltatási projektet a **.net Core 2,0** nevű *HelloWorldStateless*:

![A második párbeszédpanelen hozzon létre egy állapot nélküli szolgáltatási projektet](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject2.png)

A megoldás mostantól két projektet tartalmaz:

* *HelloWorld*. Ez a *szolgáltatásait* tartalmazó *alkalmazás* -projekt. Tartalmazza továbbá az alkalmazást leíró alkalmazás-jegyzékfájlt, valamint számos PowerShell-szkriptet, amelyek segítenek az alkalmazás üzembe helyezésében.
* *HelloWorldStateless*. Ez a szolgáltatási projekt. Az állapot nélküli szolgáltatás megvalósítását tartalmazza.

## <a name="implement-the-service"></a>A szolgáltatás implementálása

Nyissa meg a **HelloWorldStateless. cs** fájlt a szolgáltatási projektben. Service Fabric a szolgáltatás bármilyen üzleti logikát futtathat. A Service API két belépési pontot biztosít a kódhoz:

* Egy *RunAsync* nevű, nyílt végű belépési pont metódus, amelyben megkezdheti a munkaterhelések végrehajtását, beleértve a hosszú ideig futó számítási feladatokat is.

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    ...
}
```

* Egy kommunikációs belépési pont, ahol lehetőség van a kommunikációs verem (például ASP.NET Core) csatlakoztatására. Itt kezdheti meg a felhasználóktól és más szolgáltatásokból érkező kérések fogadását.

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}
```

Ebben az oktatóanyagban a `RunAsync()` belépési pont módszerére fogunk összpontosítani. Itt azonnal megkezdheti a kód futtatását.
A projekt sablonja tartalmaz egy olyan minta-implementációt, `RunAsync()` amely egy gördülő szám.

> [!NOTE]
> A kommunikációs verem használatával kapcsolatos további információkért lásd: [a szolgáltatással folytatott kommunikáció a ASP.net Core](service-fabric-reliable-services-communication-aspnetcore.md)

### <a name="runasync"></a>RunAsync

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following sample code with your own logic
    //       or remove this RunAsync override if it's not needed in your service.

    long iterations = 0;

    while (true)
    {
        cancellationToken.ThrowIfCancellationRequested();

        ServiceEventSource.Current.ServiceMessage(this.Context, "Working-{0}", ++iterations);

        await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
    }
}
```

A platform meghívja ezt a módszert, ha a szolgáltatás egy példánya el van helyezve, és készen áll a végrehajtásra. Állapot nélküli szolgáltatás esetén, amely egyszerűen azt jelenti, hogy mikor nyílik meg a szolgáltatás példánya. A rendszer lemondási tokent biztosít annak koordinálására, hogy mikor kell lezárni a szolgáltatási példányt. Service Fabric a szolgáltatási példányok nyitott/záró ciklusa többször is megjelenhet a szolgáltatás teljes élettartama során. Ez különböző okok miatt fordulhat elő, többek között:

* A rendszer áthelyezi a szolgáltatási példányokat az erőforrás-kiegyensúlyozáshoz.
* Hibák fordulnak elő a kódban.
* Az alkalmazás vagy a rendszer frissítve van.
* A mögöttes hardver kiesést tapasztal.

Ezt a koordinálást a rendszer felügyeli, hogy a szolgáltatás elérhető legyen, és megfelelően kiegyensúlyozott legyen.

`RunAsync()` ne tiltsa le a szinkron módon. A RunAsync implementációjának egy feladatot kell visszaadnia, vagy várnia kell a hosszan futó vagy a blokkoló műveletekre, hogy a futtatókörnyezet továbbra is folytatódjon. Vegye figyelembe, hogy az `while(true)` előző példában szereplő hurokban egy feladat-visszaküldési művelet `await Task.Delay()` van használatban. Ha a számítási feladatnak szinkronban kell lennie, egy új feladatot kell ütemeznie a `Task.Run()` `RunAsync` megvalósításában.

A számítási feladatok megszakítása a megadott lemondási token által koordinált együttműködési erőfeszítés. A rendszer megvárja a feladat befejezését (sikeres befejezéssel, megszakítással vagy hibával), mielőtt továbblép. Fontos, hogy tiszteletben tartsák a lemondási jogkivonatot, fejezze be a munkát, és `RunAsync()` a lehető leggyorsabban lépjen ki, amikor a rendszer a lemondást kéri.

Ebben az állapot nélküli szolgáltatási példában a Count egy helyi változóban van tárolva. Azonban mivel ez egy állapot nélküli szolgáltatás, a tárolt érték csak a szolgáltatási példányának aktuális életciklusában létezik. A szolgáltatás áthelyezése vagy újraindítása után az érték elvész.

## <a name="create-a-stateful-service"></a>Állapot-nyilvántartó szolgáltatás létrehozása

Service Fabric bevezet egy új, állapot-nyilvántartó jellegű szolgáltatást. Egy állapot-nyilvántartó szolgáltatás a szolgáltatáson belül megbízhatóan kezelheti az állapotot, és az azt használó kóddal együtt. Az állapotot Service Fabric a rendszer, anélkül, hogy az állapotot külső tárolóra kell megőrizni.

Ha a számláló értékét állapot nélkül szeretné átalakítani a nagyfokú rendelkezésre állásra és a tartósra, még akkor is, ha a szolgáltatás áthelyezi vagy újraindul, szüksége van egy állapot-nyilvántartó szolgáltatásra.

Ugyanebben a *HelloWorld* -alkalmazásban hozzáadhat egy új szolgáltatást úgy, hogy a jobb gombbal az alkalmazás-projektben található szolgáltatások hivatkozásaira kattint, és kiválasztja a **Hozzáadás – > új Service Fabric szolgáltatás** elemet.

![Szolgáltatás hozzáadása a Service Fabric alkalmazáshoz](media/service-fabric-reliable-services-quick-start/hello-stateful-NewService.png)

Válassza a **.net Core 2,0 – > állapot-nyilvántartó szolgáltatást** , és nevezze el *HelloWorldStateful*. Kattintson az **OK** gombra.

![Új Service Fabric állapot-nyilvántartó szolgáltatás létrehozásához használja az új projekt párbeszédpanelt](media/service-fabric-reliable-services-quick-start/hello-stateful-NewProject.png)

Az alkalmazásnak most két szolgáltatással kell rendelkeznie: az állapot nélküli szolgáltatás *HelloWorldStateless* és az állapot-nyilvántartó szolgáltatás *HelloWorldStateful*.

Az állapot-nyilvántartó szolgáltatás ugyanolyan belépési pontokkal rendelkezik, mint az állapot nélküli szolgáltatás. A fő különbség egy olyan *szolgáltató* elérhetősége, amely megbízhatóan képes tárolni az állapotot. Service Fabric tartalmaz egy [megbízható gyűjtemények](service-fabric-reliable-services-reliable-collections.md)nevű állami szolgáltatói implementációt, amely lehetővé teszi a replikált adatstruktúrák létrehozását a megbízható állami kezelőn keresztül. Az állapot-nyilvántartó megbízható szolgáltatás alapértelmezés szerint ezt az állapotot használja.

Nyissa meg a **HelloWorldStateful. cs** a *HelloWorldStateful*, amely a következő RunAsync módszert tartalmazza:

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following sample code with your own logic
    //       or remove this RunAsync override if it's not needed in your service.

    var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");

    while (true)
    {
        cancellationToken.ThrowIfCancellationRequested();

        using (var tx = this.StateManager.CreateTransaction())
        {
            var result = await myDictionary.TryGetValueAsync(tx, "Counter");

            ServiceEventSource.Current.ServiceMessage(this.Context, "Current Counter Value: {0}",
                result.HasValue ? result.Value.ToString() : "Value does not exist.");

            await myDictionary.AddOrUpdateAsync(tx, "Counter", 0, (key, value) => ++value);

            // If an exception is thrown before calling CommitAsync, the transaction aborts, all changes are
            // discarded, and nothing is saved to the secondary replicas.
            await tx.CommitAsync();
        }

        await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
    }
```

### <a name="runasync"></a>RunAsync

`RunAsync()` hasonló állapotú és állapot nélküli szolgáltatásokban működik. Egy állapot-nyilvántartó szolgáltatásban azonban a platform további feladatokat hajt végre az Ön nevében a végrehajtás előtt `RunAsync()` . Ez a munka magában foglalja annak biztosítását, hogy a megbízható állapot-kezelő és a megbízható gyűjtemények használatra készek legyenek.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Megbízható gyűjtemények és a megbízható State Manager

```csharp
var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
```

A [IReliableDictionary](/dotnet/api/microsoft.servicefabric.data.collections.ireliabledictionary-2#microsoft_servicefabric_data_collections_ireliabledictionary_2) egy olyan szótári implementáció, amellyel megbízhatóan tárolhatja az állapotot a szolgáltatásban. A Service Fabric és megbízható gyűjtemények révén közvetlenül a szolgáltatásban tárolhatja az adatait anélkül, hogy külső állandó tárolóra lenne szükség. A megbízható gyűjtemények kiválóan elérhetővé teszik az adataikat. A Service Fabric a szolgáltatás több *replikájának* létrehozásával és kezelésével hajtja végre. Emellett olyan API-t is biztosít, amely ellátja a replikák kezelésének bonyolultságát és az állapotukat.

A megbízható gyűjtemények bármilyen .NET-típust tárolhatnak, beleértve az egyéni típusokat is, néhány figyelmeztetéssel:

* A Service Fabric az állapotot a csomópontok közötti *replikálással* , a megbízható gyűjtemények pedig az összes replikán tárolják az adataikat a helyi lemezen. Ez azt jelenti, hogy a megbízható gyűjteményekben tárolt összes elemnek *szerializálható* kell lennie. Alapértelmezés szerint a megbízható gyűjtemények [DataContract](/dotnet/api/system.runtime.serialization.datacontractattribute) használ a szerializáláshoz, ezért fontos, hogy az [adategyezmény-szerializáló](/dotnet/framework/wcf/feature-details/types-supported-by-the-data-contract-serializer) a típusait az alapértelmezett szerializáló használata esetén is támogatja.
* Az objektumok a magas rendelkezésre állás érdekében replikálódnak, amikor megbízható gyűjteményekre vonatkozó tranzakciókat véglegesít. A megbízható gyűjteményekben tárolt objektumokat a szolgáltatás helyi memóriájában tárolja. Ez azt jelenti, hogy van egy helyi hivatkozása az objektumra.
  
   Fontos, hogy ne módosítsa az objektumok helyi példányait anélkül, hogy frissítési műveletet hajt végre a tranzakcióban található megbízható gyűjteményen. Ennek az az oka, hogy az objektumok helyi példányainak módosításait a rendszer nem replikálja automatikusan. Újra be kell szúrni az objektumot a szótárba, vagy a *frissítési* módszerek egyikét kell használnia a szótárban.

A megbízható State Manager megbízható gyűjteményeket kezel Önnek. Egyszerűen megkérheti a megbízható állapot kezelőjét, hogy név szerint bármikor és a szolgáltatás bármely pontjára felkérje a megbízható begyűjtést. A megbízható State Manager gondoskodik arról, hogy a hivatkozás visszakerüljön. Nem javasoljuk, hogy mentse a megbízható gyűjteményi példányokra mutató hivatkozásokat az osztály tag változói vagy tulajdonságai között. Különös figyelmet kell fordítani annak biztosítására, hogy a hivatkozás a szolgáltatás életciklusa során mindig egy példányra legyen beállítva. A megbízható állapot-kezelő kezeli ezt a munkát, és ismétlődő látogatásokra van optimalizálva.

### <a name="transactional-and-asynchronous-operations"></a>Tranzakciós és aszinkron műveletek

```csharp
using (ITransaction tx = this.StateManager.CreateTransaction())
{
    var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

    await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

    await tx.CommitAsync();
}
```

A megbízható gyűjtemények több azonos művelettel rendelkeznek, mint a saját `System.Collections.Generic` és a `System.Collections.Concurrent` társaik, a Language Integrated Query (LINQ) kivételével. A megbízható gyűjtemények műveletei aszinkron módon vannak elvégezve. Ennek az az oka, hogy az írási műveletek megbízható gyűjteményekkel végeznek I/O-műveleteket a lemezre való replikáláshoz és az adatok megőrzéséhez.

A megbízható gyűjtési műveletek *tranzakciós* tevékenységek, így az állapot konzisztens marad több megbízható gyűjtemény és művelet között. Megadhat például egy munkaelemet egy megbízható várólistából, végrehajthat egy műveletet, és az eredményt egy megbízható szótárban mentheti, mindezt egyetlen tranzakción belül. Ezt atomi műveletként kezeli a rendszer, és garantálja, hogy a teljes művelet sikeres lesz, vagy a teljes művelet visszaállítja a műveletet. Ha hiba lép fel az elem kivonása után, de az eredmény mentése előtt, a teljes tranzakció vissza lesz állítva, és az elem a várólistán marad a feldolgozáshoz.

## <a name="run-the-application"></a>Az alkalmazás futtatása
Most visszatérünk a *HelloWorld* alkalmazáshoz. Most már létrehozhatja és üzembe helyezheti szolgáltatásait. Ha lenyomja az **F5** billentyűt, az alkalmazás a helyi fürtön lesz felépítve és telepítve.

A szolgáltatások elindítása után megtekintheti a generált Windows esemény-nyomkövetés (ETW) eseményeit egy **diagnosztikai események** ablakban. Vegye figyelembe, hogy a megjelenített események az állapot nélküli szolgáltatásból és az alkalmazás állapot-nyilvántartó szolgáltatásból származnak. A **szüneteltetés** gombra kattintva szüneteltetheti az adatfolyamot. Ezután megvizsgálhatja az üzenet részleteit az üzenet kibontásával.

> [!NOTE]
> Az alkalmazás futtatása előtt győződjön meg arról, hogy a helyi fejlesztési fürt fut. A helyi környezet beállításával kapcsolatos információkért tekintse meg az [első lépéseket ismertető útmutatót](service-fabric-get-started.md) .
> 
> 

![Diagnosztikai események megtekintése a Visual Studióban](media/service-fabric-reliable-services-quick-start/hello-stateful-Output.png)

## <a name="next-steps"></a>Következő lépések
[A Service Fabric-alkalmazás hibakeresése a Visual Studióban](service-fabric-debugging-your-application.md)

[Első lépések: Service Fabric webes API-szolgáltatások OWIN-alapú önkiszolgáló használatával](./service-fabric-reliable-services-communication-aspnetcore.md)

[További információ a megbízható gyűjteményekről](service-fabric-reliable-services-reliable-collections.md)

[Alkalmazás üzembe helyezése](service-fabric-deploy-remove-applications.md)

[Alkalmazás frissítése](service-fabric-application-upgrade.md)

[Reliable Services fejlesztői referenciája](/previous-versions/azure/dn706529(v=azure.100))
