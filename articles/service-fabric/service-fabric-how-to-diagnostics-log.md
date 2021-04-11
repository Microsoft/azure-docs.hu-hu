---
title: Naplózási események létrehozása .NET-alkalmazásokból
description: Ismerje meg, hogyan adhat hozzá naplózást az Azure-fürtön vagy önálló fürtön üzemeltetett .NET Service Fabric-alkalmazáshoz.
ms.topic: conceptual
ms.date: 03/27/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: dd3afed85dc3eff81cbe60f252cdc16b0d4018c8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105628118"
---
# <a name="add-logging-to-your-service-fabric-application"></a>Naplózás hozzáadása Service Fabric-alkalmazáshoz

Az alkalmazásnak elegendő információt kell biztosítania a kriminalisztikai hibakereséshez, ha problémák merülnek fel. A naplózás a Service Fabric alkalmazáshoz felvehető legfontosabb dolgok egyike. Hiba esetén a megfelelő naplózás lehetővé teszi a hibák kivizsgálását. A naplók elemzésével az alkalmazás teljesítményének vagy kialakításának javítására is lehetőség van. Ez a dokumentum néhány különböző naplózási lehetőséget mutat be.

## <a name="eventflow"></a>EventFlow segítségével

A [EventFlow segítségével Library](https://github.com/Azure/diagnostics-eventflow) Suite lehetővé teszi az alkalmazások számára, hogy meghatározzák a gyűjteni kívánt diagnosztikai adatokat, és hogy hol legyenek leválasztva. A diagnosztikai adatok bármilyen típusúak lehetnek a teljesítményszámlálóktól az alkalmazás-nyomkövetésig. Ugyanabban a folyamatban fut, mint az alkalmazás, így a kommunikációs terhelés kisméretű. További információ a EventFlow segítségével és a Service Fabricről: [Azure Service Fabric Event összesítés a EventFlow segítségével](service-fabric-diagnostics-event-aggregation-eventflow.md).

### <a name="using-structured-eventsource-events"></a>Strukturált EventSource-események használata

Az üzenetküldés a használati eset alapján lehetővé teszi az eseményre vonatkozó adatcsomagolást az esemény kontextusában. A megadott esemény tulajdonságainak neve vagy értékei alapján könnyebben kereshet és szűrheti a keresést. A rendszerállapot-kimenet strukturálása megkönnyíti az olvasást, de több gondolatot és időt igényel az egyes használati esetekhez tartozó események definiálásához. 

Egyes események definíciói megoszthatók a teljes alkalmazásban. Egy metódus indítási vagy leállítási eseménye például egy alkalmazás számos szolgáltatásán újra felhasználható. A tartományi specifikus szolgáltatások, például a megrendelési rendszerek rendelkezhetnek egy **CreateOrder** -eseménnyel, amely a saját egyedi eseménnyel rendelkezik. Ez a megközelítés számos eseményt eredményezhet, és lehetséges, hogy az azonosítók koordinálására van szükség a projekt csapatok között. 

```csharp
[EventSource(Name = "MyCompany-VotingState-VotingStateService")]
internal sealed class ServiceEventSource : EventSource
{
    public static readonly ServiceEventSource Current = new ServiceEventSource();

    // The instance constructor is private to enforce singleton semantics.
    private ServiceEventSource() : base() { }

    ...

    // The ServiceTypeRegistered event contains a unique identifier, an event attribute that defined the event, and the code implementation of the event.
    private const int ServiceTypeRegisteredEventId = 3;
    [Event(ServiceTypeRegisteredEventId, Level = EventLevel.Informational, Message = "Service host process {0} registered service type {1}", Keywords = Keywords.ServiceInitialization)]
    public void ServiceTypeRegistered(int hostProcessId, string serviceType)
    {
        WriteEvent(ServiceTypeRegisteredEventId, hostProcessId, serviceType);
    }

    // The ServiceHostInitializationFailed event contains a unique identifier, an event attribute that defined the event, and the code implementation of the event.
    private const int ServiceHostInitializationFailedEventId = 4;
    [Event(ServiceHostInitializationFailedEventId, Level = EventLevel.Error, Message = "Service host initialization failed", Keywords = Keywords.ServiceInitialization)]
    public void ServiceHostInitializationFailed(string exception)
    {
        WriteEvent(ServiceHostInitializationFailedEventId, exception);
    }

    ...

```

### <a name="using-eventsource-generically"></a>A EventSource általános használata

Mivel a konkrét események meghatározása nehéz lehet, sok ember definiál néhány eseményt egy közös paraméterrel, amely általában az adatokat karakterláncként adja ki. A strukturált aspektus nagy része elvész, és nehezebb megkeresni és szűrni az eredményeket. Ebben a megközelítésben néhány olyan esemény van meghatározva, amely általában megfelel a naplózási szinteknek. A következő kódrészlet egy hibakeresési és hibaüzenetet definiál:

```csharp
[EventSource(Name = "MyCompany-VotingState-VotingStateService")]
internal sealed class ServiceEventSource : EventSource
{
    public static readonly ServiceEventSource Current = new ServiceEventSource();

    // The Instance constructor is private, to enforce singleton semantics.
    private ServiceEventSource() : base() { }

    ...

    private const int DebugEventId = 10;
    [Event(DebugEventId, Level = EventLevel.Verbose, Message = "{0}")]
    public void Debug(string msg)
    {
        WriteEvent(DebugEventId, msg);
    }

    private const int ErrorEventId = 11;
    [Event(ErrorEventId, Level = EventLevel.Error, Message = "Error: {0} - {1}")]
    public void Error(string error, string msg)
    {
        WriteEvent(ErrorEventId, error, msg);
    }

    ...

```

A strukturált és általános rendszerállapot hibrid használata is jól használható. A strukturált rendszerállapot-kimutatási hibák és mérőszámok jelentésére szolgál. Általános események a mérnökök által a hibaelhárításhoz felhasznált részletes naplózáshoz használhatók.

## <a name="microsoftextensionslogging"></a>Microsoft. Extensions. Logging

A ASP.NET Core naplózás ([Microsoft. Extensions. Logging NuGet csomag](https://www.nuget.org/packages/Microsoft.Extensions.Logging)) egy naplózási keretrendszer, amely szabványos naplózási API-t biztosít az alkalmazás számára. A többi naplózási háttér támogatása csatlakoztatható ASP.NET Core naplózáshoz. Ez számos támogatást biztosít az alkalmazásban való bejelentkezéshez, anélkül, hogy sok kódot kellene módosítania.

1. Adja hozzá a **Microsoft. Extensions. Logging** NuGet-csomagot a felvenni kívánt projekthez. Adja hozzá a szolgáltatói csomagokat is. További információ: [bejelentkezés ASP.net Core](/aspnet/core/fundamentals/logging).
2. Adja hozzá a **Microsoft. Extensions. Logging** fájlhoz tartozó **using** direktívát a szolgáltatás fájljához.
3. Definiáljon egy privát változót a szolgáltatási osztályon belül.

   ```csharp
   private ILogger _logger = null;
   ```

4. A szolgáltatási osztály konstruktorában adja hozzá a következő kódot:

   ```csharp
   _logger = new LoggerFactory().CreateLogger<Stateless>();
   ```

5. Megkezdheti a kód üzembe helyezését a metódusokban. Íme néhány példa:

   ```csharp
   _logger.LogDebug("Debug-level event from Microsoft.Logging");
   _logger.LogInformation("Informational-level event from Microsoft.Logging");

   // In this variant, we're adding structured properties RequestName and Duration, which have values MyRequest and the duration of the request.
   // Later in the article, we discuss why this step is useful.
   _logger.LogInformation("{RequestName} {Duration}", "MyRequest", requestDuration);
   ```

### <a name="using-other-logging-providers"></a>Más naplózási szolgáltatók használata

Egyes harmadik féltől származó szolgáltatók az előző szakaszban leírt módszert használják, beleértve a [Serilog](https://serilog.net/), a [NLog](https://nlog-project.org/)és a [Loggr](https://github.com/imobile3/Loggr.Extensions.Logging). Mindegyiket csatlakoztathatja ASP.NET Core naplózáshoz, vagy külön is használhatja őket. A Serilog olyan funkcióval rendelkezik, amely a naplózó összes üzenetet gazdagítja. Ez a szolgáltatás hasznos lehet a szolgáltatásnév, a típus és a partíció adatainak kimenete. Ha ezt a funkciót a ASP.NET Core-infrastruktúrában szeretné használni, hajtsa végre a következő lépéseket:

1. Adja hozzá a **Serilog**, a **Serilog. Extensions. Logging**, a **Serilog. mosogatós. írástudó** és a **Serilog. mosogató. megfigyelhető** NuGet-csomagokat a projekthez. 
2. Hozzon létre egy `LoggerConfiguration` és a Logger-példányt.

   ```csharp
   Log.Logger = new LoggerConfiguration().WriteTo.LiterateConsole().CreateLogger();
   ```

3. Adjon hozzá egy `Serilog.ILogger` argumentumot a szolgáltatás konstruktorához, és adja át az újonnan létrehozott naplózó.

   ```csharp
   ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
   ```

4. A szolgáltatás konstruktorában a **ServiceTypeName**, **szolgáltatásnév**, **PartitionID** és **InstanceId** tulajdonság-gazdagítók jönnek létre.

   ```csharp
   public Stateless(StatelessServiceContext context, Serilog.ILogger serilog)
       : base(context)
   {
       PropertyEnricher[] properties = new PropertyEnricher[]
       {
           new PropertyEnricher("ServiceTypeName", context.ServiceTypeName),
           new PropertyEnricher("ServiceName", context.ServiceName),
           new PropertyEnricher("PartitionId", context.PartitionId),
           new PropertyEnricher("InstanceId", context.ReplicaOrInstanceId),
       };

       serilog.ForContext(properties);

       _logger = new LoggerFactory().AddSerilog(serilog.ForContext(properties)).CreateLogger<Stateless>();
   }
   ```

5. A kód megegyeznek azzal, mintha Serilog nélkül használta ASP.NET Core.

   >[!NOTE]
   >Azt javasoljuk, hogy *ne* használja a statikust `Log.Logger` az előző példával. Service Fabric ugyanazon szolgáltatástípus több példányát is üzemeltetheti egyetlen folyamaton belül. Ha a statikus szolgáltatást használja `Log.Logger` , a tulajdonság-gazdagítók utolsó írója megjeleníti a-t futtató összes példány értékeit. Ez az egyik oka annak, hogy a _logger változó a Service osztály egy privát tagja változó. Emellett elérhetővé kell tennie a `_logger` közös programkódot is, amely a szolgáltatásokon keresztül is felhasználható.

## <a name="next-steps"></a>Következő lépések

- További információ az [alkalmazások figyeléséről Service Fabricban](service-fabric-diagnostics-event-generation-app.md).
- További információ a [EventFlow segítségével](service-fabric-diagnostics-event-aggregation-eventflow.md) és a [Windows Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md)való naplózásról.
