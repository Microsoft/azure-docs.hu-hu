---
title: Azure Event Hubs – események küldése/fogadása a .NET-keretrendszer használatával
description: 'Gyors útmutató: Ez a cikk egy olyan .NET-keretrendszerbeli alkalmazás létrehozásához nyújt útmutatást, amely eseményeket küld az Azure Event Hubsnak.'
ms.topic: quickstart
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 3c2ce58f86fa27544641c0917e96fa3172017232
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107309356"
---
# <a name="quickstart-send-events-to-or-receive-events-from-azure-event-hubs-using-net-framework"></a>Gyors útmutató: események küldése vagy fogadása az Azure Event Hubs a .NET-keretrendszerrel
Az Azure Event Hubs egy Big Data streamplatform és eseményfeldolgozó szolgáltatás, amely másodpercenként több millió esemény fogadására és feldolgozására képes. Az Event Hubs képes az elosztott szoftverek és eszközök által generált események, adatok vagy telemetria feldolgozására és tárolására. Az eseményközpontokba elküldött adatok bármilyen valós idejű elemzési szolgáltató vagy kötegelési/tárolóadapter segítségével átalakíthatók és tárolhatók. Az Event Hubs részletes áttekintéséért lásd az [Event Hubs áttekintését](event-hubs-about.md) és az [Event Hubs-szolgáltatásokat](event-hubs-features.md) ismertető cikket.

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre a .NET-keretrendszer konzoljának alkalmazásait a C#-ban egy eventhub események küldéséhez vagy fogadásához. 

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag teljesítéséhez a következő előfeltételekre lesz szüksége:

- [Microsoft Visual Studio 2019](https://visualstudio.com).
- **Hozzon létre egy Event Hubs névteret és egy Event hubot**. Első lépésként a [Azure Portal](https://portal.azure.com) használatával hozzon létre egy Event Hubs típusú névteret, és szerezze be azokat a felügyeleti hitelesítő adatokat, amelyekre az alkalmazásnak szüksége van az Event hub-vel való kommunikációhoz. A névtér és az Event hub létrehozásához kövesse az [ebben a cikkben](event-hubs-create.md)ismertetett eljárást. Ezután szerezze be a **kapcsolati karakterláncot az Event hub-névtérhez** a következő cikk utasításait követve: [kapcsolati karakterlánc beolvasása](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Az oktatóanyag későbbi részében a kapcsolatok karakterláncát kell használnia.

## <a name="send-events"></a>Események küldése 
Ebből a szakaszból megtudhatja, hogyan hozhat létre egy .NET-keretrendszerbeli Console-alkalmazást, hogy eseményeket küldjön az Event hub-nak. 

### <a name="create-a-console-application"></a>Konzolalkalmazás létrehozása

A Visual Studióban hozzon létre egy új Visual C# asztali alkalmazás projektet a **Console Application** Project sablon használatával. Adja a projektnek a **Sender** (Küldő) nevet.
   
![Képernyőfelvétel: az "új projekt" ablak a "Console app (.NET-keretrendszer)" beállítással van kiválasztva, a "feladó" és az "OK" gomb pedig kiemelten jelenik meg.](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp1.png)

### <a name="add-the-event-hubs-nuget-package"></a>Az Event Hubs NuGet-csomag hozzáadása

1. A Solution Explorerben (Megoldáskezelőben) kattintson a jobb gombbal a **Sender** (Küldő) projektre, majd kattintson a **Manage NuGet Packages for Solution** (NuGet-csomagok kezelése megoldáshoz) parancsra. 
2. Kattintson a **Browse** (Tallózás) lapra, és keressen a következőre: `WindowsAzure.ServiceBus`. Kattintson az **Install** (Telepítés) gombra, és fogadja el a használati feltételeket. 
   
    ![Service Bus NuGet-csomag telepítése](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp2.png)
   
    A Visual Studio letölti és telepíti az [Azure Service Bus library NuGet package](https://www.nuget.org/packages/WindowsAzure.ServiceBus) (Azure szolgáltatásbusz-könyvtár NuGet-csomag) elemet, és hozzáad egy rá mutató hivatkozást is.

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Kód írása az üzenetek eseményközpontba való küldésére

1. Adja hozzá a következő `using`utasításokat a **Program.cs** fájl elejéhez:
   
    ```csharp
    using System.Threading;
    using Microsoft.ServiceBus.Messaging;
    ```
2. Adja hozzá a következő mezőket a **Program** osztályhoz, lecserélve a helyőrző értékeket az előző szakaszban létrehozott eseményközpont nevével, valamint a korábban elmentett névtérszintű kapcsolati sztringgel. Az Event hub kapcsolati karakterláncát az Azure Portal Event hub lapján található **RootManageSharedAccessKey** alatt található **kapcsolati karakterlánc – elsődleges** kulcs lehetőséggel másolhatja. A részletes lépésekért lásd: a [kapcsolatok karakterláncának beolvasása](event-hubs-get-connection-string.md#get-connection-string-from-the-portal).
   
    ```csharp
    static string eventHubName = "Your Event Hub name";
    static string connectionString = "namespace connection string";
    ```
3. Adja hozzá a **Program** osztályhoz a következő módszert:
   
      ```csharp
      static void SendingRandomMessages()
      {
          var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
          while (true)
          {
              try
              {
                  var message = Guid.NewGuid().ToString();
                  Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, message);
                  eventHubClient.Send(new EventData(Encoding.UTF8.GetBytes(message)));
              }
              catch (Exception exception)
              {
                  Console.ForegroundColor = ConsoleColor.Red;
                  Console.WriteLine("{0} > Exception: {1}", DateTime.Now, exception.Message);
                  Console.ResetColor();
              }
       
              Thread.Sleep(200);
          }
      }
      ```
   
      Ez a metódus folyamatosan küldi az eseményeket az eseményközpontjának, 200 ezredmásodperces késleltetéssel.
4. Végül adja a következő sorokat a **Main** metódushoz:
   
      ```csharp
      Console.WriteLine("Press Ctrl-C to stop the sender process");
      Console.WriteLine("Press Enter to start now");
      Console.ReadLine();
      SendingRandomMessages();
      ```
5. Futtassa a programot, és ellenőrizze, hogy nincsenek-e hibák.
  
## <a name="receive-events"></a>Események fogadása
Ebben a szakaszban olyan .NET-keretrendszerbeli konzol-alkalmazást ír, amely az Event hub-ból származó üzeneteket fogad az [Event Processor Host](event-hubs-event-processor-host.md)használatával. Az [Event Processor Host](event-hubs-event-processor-host.md) egy .net-osztály, amely leegyszerűsíti az események fogadását az Event hubokból az állandó ellenőrzőpontok és a párhuzamos fogadások kezelésével az adott esemény hubokból. Az Event Processor Host használatával több fogadó között oszthatja el az eseményeket, még akkor is, ha ezek különböző csomópontokon üzemelnek. 

[!INCLUDE [event-hubs-create-storage](../../includes/event-hubs-create-storage.md)]

### <a name="create-a-console-application"></a>Konzolalkalmazás létrehozása

Hozzon létre egy új Visual C# asztalialkalmazás-projektet a **Console Application** (Konzolalkalmazás) projektsablonnal. Adja a projektnek a **Receiver** (Fogadó) nevet.
   
![Konzolalkalmazás létrehozása](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp1.png)

### <a name="add-the-event-hubs-nuget-package"></a>Az Event Hubs NuGet-csomag hozzáadása

1. A Megoldáskezelőben kattintson a jobb gombbal a **Receiver** (Fogadó) projektre, majd kattintson a **Manage NuGet Packages for Solution** (Megoldás NuGet-csomagjainak kezelése) parancsra.
2. Kattintson a **Browse** (Tallózás) lapra, és keressen a következőre: `Microsoft Azure Service Bus Event Hub - EventProcessorHost`. Kattintson az **Install** (Telepítés) gombra, és fogadja el a használati feltételeket.
   
    ![Az Event Processor Host NuGet-csomag keresése](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-eph-csharp1.png)
   
    A Visual Studio letölti és telepíti az [Azure Service Bus Event Hub - EventProcessorHost NuGet csomagot](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost) minden függőségével együtt, és hozzáad egy rá mutató hivatkozást is.

### <a name="implement-the-ieventprocessor-interface"></a>Az IEvent Processor felület implementálása

1. Kattintson a jobb gombbal a **Receiver** (Fogadó) projektre, kattintson az **Add** (Hozzáadás) lehetőségre, majd a **Class** (Osztály) elemre. Nevezze el az új osztályt **SimpleEventProcessor** névre, és kattintson az **Add** (Hozzáadás) gombra az osztály létrehozásához.
   
    ![SimpleEventProcessor osztály hozzáadása](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp2.png)
2. Adja hozzá a következő utasításokat a SimpleEventProcessor.cs fájl elejéhez:
    
      ```csharp
      using Microsoft.ServiceBus.Messaging;
      using System.Diagnostics;
      ```
    
3. Helyettesítse be a következő kódot a osztály törzséhez:
    
      ```csharp
      class SimpleEventProcessor : IEventProcessor
      {
        Stopwatch checkpointStopWatch;
        
        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
            if (reason == CloseReason.Shutdown)
            {
                await context.CheckpointAsync();
            }
        }
        
        Task IEventProcessor.OpenAsync(PartitionContext context)
        {
            Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
            this.checkpointStopWatch = new Stopwatch();
            this.checkpointStopWatch.Start();
            return Task.FromResult<object>(null);
        }
        
        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (EventData eventData in messages)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
        
                Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                    context.Lease.PartitionId, data));
            }
        
            //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
            {
                await context.CheckpointAsync();
                this.checkpointStopWatch.Restart();
            }
        }
      }
      ```
    
      Az **EventProcessorHost** ezt az osztályt hívja meg az eseményközpontból fogadott események feldolgozásához. A `SimpleEventProcessor` osztály stoppert használ az ellenőrzőpont-metódus **EventProcessorHost** környezetben való rendszeres időközönkénti hívásához. Ez az eljárás biztosítja, hogy ha a fogadó újraindul, ne vesszen el öt percnél több feldolgozási munka.

### <a name="update-the-main-method-to-use-simpleeventprocessor"></a>A fő metódus frissítése a SimpleEventProcessor használatára

1. A **Program** osztályban adja hozzá a következő `using` utasítást a fájl elejéhez:
    
      ```csharp
      using Microsoft.ServiceBus.Messaging;
      ```
    
2. Cserélje le a `Main` metódust a `Program` osztályban a következő kódra, az Event hub nevét és a korábban mentett névtér-szintű kapcsolati sztringet, valamint az előző részekben másolt Storage-fiókot és-kulcsot. 
    
      ```csharp
      static void Main(string[] args)
      {
        string eventHubConnectionString = "{Event Hubs namespace connection string}";
        string eventHubName = "{Event Hub name}";
        string storageAccountName = "{storage account name}";
        string storageAccountKey = "{storage account key}";
        string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);
        
        string eventProcessorHostName = Guid.NewGuid().ToString();
        EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
        Console.WriteLine("Registering EventProcessor...");
        var options = new EventProcessorOptions();
        options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
        eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();
        
        Console.WriteLine("Receiving. Press enter key to stop worker.");
        Console.ReadLine();
        eventProcessorHost.UnregisterEventProcessorAsync().Wait();
      }
      ```
    
3. Futtassa a programot, és ellenőrizze, hogy nincsenek-e hibák.
  
## <a name="next-steps"></a>Következő lépések
Olvassa el a következő cikkeket: 

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Az Azure Event Hubs szolgáltatásai és terminológiája](event-hubs-features.md).
- [Event Hubs – gyakori kérdések](event-hubs-faq.yml)


<!-- Links -->
[EventProcessorHost]: /dotnet/api/microsoft.servicebus.messaging.eventprocessorhost
[Event Hubs overview]: event-hubs-about.md
[Event Hubs Programming Guide]: event-hubs-programming-guide.md
[Azure Storage account]:../storage/common/storage-create-storage-account.md
[Event Processor Host]: event-hubs-event-processor-host.md
[Azure portal]: https://portal.azure.com
