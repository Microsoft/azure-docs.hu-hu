---
title: Leltár frissítése Azure Portal témakörök/előfizetések használatával
description: Ez az oktatóanyag bemutatja, hogyan küldhet és fogadhat témakörökhöz és előfizetésekhez tartozó üzeneteket, és hogyan adhat hozzá szűrési szabályokat a .NET használatával
author: spelluru
ms.author: spelluru
ms.date: 10/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: a7b03727c574dcee1cd56144a521f36de6dc48c6
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861372"
---
# <a name="tutorial-update-inventory-using-azure-portal-and-topicssubscriptions"></a>Oktatóanyag: Leltár frissítése az Azure Portal és témakörök/előfizetések használatával

A Microsoft Azure Service Bus egy több-bérlős felhőalapú üzenetkezelési szolgáltatás, amely információkat küld alkalmazások és szolgáltatók között. Az aszinkron műveletek rugalmas, közvetítőalapú üzenettovábbítást, valamint strukturált, érkezési sorrendben történő üzenetkiküldést tesznek lehetővé. Emellett közzétételi vagy előfizetési lehetőségeket is biztosítanak. Ebből az oktatóanyagból megtudhatja, hogyan használhatók a Service Bus-témakörök és -előfizetések kereskedelmi leltárazási forgatókönyvekben az Azure Portalt és a .NET-et használó közzétételi/feliratkozási csatornákkal.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Service Bus-témakör és egy vagy több hozzá tartozó előfizetés létrehozása az Azure Portal használatával
> * Témakörszűrők hozzáadása .NET-kód használatával
> * Két különböző tartalmú üzenet létrehozása
> * Az üzenetek elküldése és annak ellenőrzése, hogy megérkeztek-e a megfelelő előfizetésekbe
> * Üzenet fogadása az előfizetésektől

Erre a forgatókönyvre egy példa, amikor több kiskereskedelmi üzletben frissül a leltárválaszték. Ebben az esetben minden egyes üzlet vagy üzletcsoport megkapja a neki szánt üzeneteket a választéka frissítéséről. Ez az oktatóanyag bemutatja, hogyan valósítható meg a forgatókönyv előfizetések és szűrők használatával. Először hozzon létre egy témakört 3 előfizetéssel, adjon hozzá szabályokat és szűrőket, majd küldjön és fogadjon üzeneteket a témaköröktől és az előfizetésektől.

![témakör](./media/service-bus-tutorial-topics-subscriptions-portal/about-service-bus-topic.png)

Ha nem rendelkezik Azure-előfizetéssel, a kezdés előtt létrehozhat egy [ingyenes][] fiókot.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez győződjön meg arról, hogy telepítette a következőket:

- [Visual Studio 2017 3-as frissítés (verziószám: 15.3, 26730.01)](https://www.visualstudio.com/vs) vagy újabb.
- [NET Core SDK](https://dotnet.microsoft.com/download), 2.0-s vagy újabb verzió.

## <a name="service-bus-topics-and-subscriptions"></a>Service Bus-témakörök és -előfizetések

Minden egyes [témakörre való előfizetés](service-bus-messaging-overview.md#topics) másolatot kaphat az összes üzenetről. A témakörök teljes protokoll és szemantika szempontjából is teljesen kompatibilisek a Service Bus-üzenetsorokkal. A Service Bus-témakörök számos különféle kiválasztási szabályt támogatnak szűrőfeltételekkel, illetve az üzenet tulajdonságait beállító vagy módosító választható műveletekkel. Minden szabályegyezéskor létrejön egy üzenet. A szabályokról, szűrőkről és műveletekről ezen a [hivatkozáson](topic-filters.md) talál további információt.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-topics-three-subscriptions-portal](../../includes/service-bus-create-topics-three-subscriptions-portal.md)]



## <a name="create-filter-rules-on-subscriptions"></a>Szűrési szabályok létrehozása előfizetésekhez

Miután kiépítette a névteret és a témakört/előfizetéseket, valamint beszerezte a szükséges hitelesítő adatokat, készen áll az előfizetések szűrési szabályainak létrehozására, majd az üzenetek küldésére és fogadására. A kódot [ebben a GitHub-mintamappában](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/TopicFilters) vizsgálhatja meg.

## <a name="send-and-receive-messages"></a>Üzenetek küldése és fogadása

A kód futtatásához tegye a következőt:

1. Egy parancssorban vagy PowerShell-parancssorban klónozza a [Service Bus GitHub-adattárát](https://github.com/Azure/azure-service-bus/) a következő paranccsal:

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

2. Lépjen a következő mintamappához: `azure-service-bus\samples\DotNet\Azure.Messaging.ServiceBus\BasicSendReceiveTutorialwithFilters`.

3. Szerezze be a kapcsolati sztringet, amelyet a jelen oktatóanyag A felügyeleti hitelesítő adatok beszerzése című szakaszában írtaknak megfelelően a Jegyzettömbbe másolt. Szüksége lesz továbbá az előző szakaszban létrehozott témakör nevére.

4. Írja be a következő parancsot a parancssorba:

   ```shell
   dotnet build
   ```

5. Lépjen a `BasicSendReceiveTutorialwithFilters\bin\Debug\netcoreapp3.1` mappába.

6. Írja be a következő parancsot a program futtatásához. Ne felejtse el kicserélni a `myConnectionString` kifejezést az előzőleg beszerzett értékre, a `myTopicName` kifejezést pedig az imént létrehozott témakör nevére:

   ```shell
   dotnet BasicSendReceiveTutorialwithFilters.dll -ConnectionString "myConnectionString" -TopicName "myTopicName"
   ``` 
7. Kövesse az utasításokat a konzolban, és először válassza ki a szűrő létrehozását. A szűrők létrehozásához az alapértelmezett szűrők eltávolítása is hozzátartozik. Ha PowerShellt vagy parancssori felületet használ, nem kell eltávolítania az alapértelmezett szűrőket, de ha kódot használ, ez mindenképpen szükséges. Az 1. és a 3. konzolparancs segítségével kezelheti a korábban létrehozott előfizetésekhez tartozó szűrőket:

   - Végrehajtás 1: az alapértelmezett szűrők eltávolítása.
   - Végrehajtás 2: saját szűrők hozzáadása.
   - Végrehajtás 3: igény szerint a saját szűrők eltávolítása. Vegye figyelembe, hogy az alapértelmezett szűrők így nem jönnek létre újra.

     ![2 kimenetének megjelenítése](./media/service-bus-tutorial-topics-subscriptions-portal/create-rules.png)

8. A szűrők létrehozása után üzeneteket küldhet. Nyomja le a 4-es billentyűt, és figyelje meg 10 üzenetet küldését a témakörnek:

    ![Kimenet küldése](./media/service-bus-tutorial-topics-subscriptions-portal/send-output.png)

9. Nyomja le az 5-ös billentyűt, és figyelje meg az üzenetek fogadását. Ha nem kapott 10 db üzenetet, nyomja le az „m” billentyűt a menü megjelenítéséhez, majd nyomja újból az 5-ös billentyűt.

    ![Kimenet fogadása](./media/service-bus-tutorial-topics-subscriptions-portal/receive-output.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a névteret és a témakört. Ehhez válassza ki az erőforrásokat a portálon, majd kattintson a **Törlés** elemre.

## <a name="understand-the-sample-code"></a>A mintakód értelmezése

Ez a szakasz a mintakód működésének további részleteit ismerteti.

### <a name="get-connection-string-and-topic"></a>Kapcsolati sztring és témakör lekérése

A kód először meghatározza azokat a változókat, amelyek a program további végrehajtását vezérlik.

```csharp
string ServiceBusConnectionString;
string TopicName;

static string[] Subscriptions = { "S1", "S2", "S3" };
static IDictionary<string, string[]> SubscriptionFilters = new Dictionary<string, string[]> {
    { "S1", new[] { "StoreId IN('Store1', 'Store2', 'Store3')", "StoreId = 'Store4'"} },
    { "S2", new[] { "sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'" } },
    { "S3", new[] { "sys.To NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8')" } }
};
// You can have only have one action per rule and this sample code supports only one action for the first filter, which is used to create the first rule. 
static IDictionary<string, string> SubscriptionAction = new Dictionary<string, string> {
    { "S1", "" },
    { "S2", "" },
    { "S3", "SET sys.Label = 'SalesEvent'"  }
};
static string[] Store = { "Store1", "Store2", "Store3", "Store4", "Store5", "Store6", "Store7", "Store8", "Store9", "Store10" };
static string SysField = "sys.To";
static string CustomField = "StoreId";
static int NrOfMessagesPerStore = 1; // Send at least 1.
```

A kapcsolati sztring és a témakör nevét a rendszer az alább látható módon parancssori paraméterekkel továbbítja, majd beolvassa a `Main()` metódusba:

```csharp
static void Main(string[] args)
{
    string ServiceBusConnectionString = "";
    string TopicName = "";

    for (int i = 0; i < args.Length; i++)
    {
        if (args[i] == "-ConnectionString")
        {
            Console.WriteLine($"ConnectionString: {args[i + 1]}");
            ServiceBusConnectionString = args[i + 1]; // Alternatively enter your connection string here.
        }
        else if (args[i] == "-TopicName")
        {
            Console.WriteLine($"TopicName: {args[i + 1]}");
            TopicName = args[i + 1]; // Alternatively enter your queue name here.
        }
    }

    if (ServiceBusConnectionString != "" && TopicName != "")
    {
        Program P = StartProgram(ServiceBusConnectionString, TopicName);
        P.PresentMenu().GetAwaiter().GetResult();
    }
    else
    {
        Console.WriteLine("Specify -Connectionstring and -TopicName to execute the example.");
        Console.ReadKey();
    }
}
```

### <a name="remove-default-filters"></a>Alapértelmezett szűrők eltávolítása

Amikor előfizetést hoz létre, a Service Bus létrehozza a hozzá tartozó alapértelmezett szűrőt. Ez a szűrő lehetővé teszi a témakörbe küldött összes üzenet fogadását. Ha egyéni szűrőket kíván használni, az alábbi kódban látható módon eltávolíthatja az alapértelmezett szűrőt:

```csharp
private async Task RemoveDefaultFilters()
{
    Console.WriteLine($"Starting to remove default filters.");

    try
    {
        var client = new ServiceBusAdministrationClient(ServiceBusConnectionString);
        foreach (var subscription in Subscriptions)
        {
            await client.DeleteRuleAsync(TopicName, subscription, CreateRuleOptions.DefaultRuleName);
            Console.WriteLine($"Default filter for {subscription} has been removed.");
        }

        Console.WriteLine("All default Rules have been removed.\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }

    await PresentMenu();
}
```

### <a name="create-filters"></a>Szűrők létrehozása

Az alábbi kóddal a jelen oktatóanyagban definiált egyéni szűrőket adhatja hozzá:

```csharp
private async Task CreateCustomFilters()
{
    try
    {
        for (int i = 0; i < Subscriptions.Length; i++)
        {
            var client = new ServiceBusAdministrationClient(ServiceBusConnectionString);
            string[] filters = SubscriptionFilters[Subscriptions[i]];
            if (filters[0] != "")
            {
                int count = 0;
                foreach (var myFilter in filters)
                {
                    count++;

                    string action = SubscriptionAction[Subscriptions[i]];
                    if (action != "")
                    {
                        await client.CreateRuleAsync(TopicName, Subscriptions[i], new CreateRuleOptions
                        {
                            Filter = new SqlRuleFilter(myFilter),
                            Action = new SqlRuleAction(action),
                            Name = $"MyRule{count}"
                        });
                    }
                    else
                    {
                        await client.CreateRuleAsync(TopicName, Subscriptions[i], new CreateRuleOptions
                        {
                            Filter = new SqlRuleFilter(myFilter),
                            Name = $"MyRule{count}"
                        });
                    }
                }
            }

            Console.WriteLine($"Filters and actions for {Subscriptions[i]} have been created.");
        }

        Console.WriteLine("All filters and actions have been created.\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }

    await PresentMenu();
}
```

### <a name="remove-your-custom-created-filters"></a>Egyénileg létrehozott szűrők eltávolítása

Ha az előfizetéséhez tartozó összes szűrőt el kívánja távolítani, ezt a következő kóddal teheti meg:

```csharp
private async Task CleanUpCustomFilters()
{
    foreach (var subscription in Subscriptions)
    {
        try
        {
            var client = new ServiceBusAdministrationClient(ServiceBusConnectionString);
            IAsyncEnumerator<RuleProperties> rules = client.GetRulesAsync(TopicName, subscription).GetAsyncEnumerator();
            while (await rules.MoveNextAsync())
            {
                await client.DeleteRuleAsync(TopicName, subscription, rules.Current.Name);
                Console.WriteLine($"Rule {rules.Current.Name} has been removed.");
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.ToString());
        }
    }
    Console.WriteLine("All default filters have been removed.\n");

    await PresentMenu();
}
```

### <a name="send-messages"></a>Üzenetek küldése

A témakörbe történő üzenetküldés hasonló az üzenetsorba való üzenetküldéshez. Ez a példa azt mutatja be, hogyan lehet üzeneteket küldeni egy feladatlista és aszinkron feldolgozás használatával:

```csharp
public async Task SendMessages()
{
    try
    {
        await using var client = new ServiceBusClient(ServiceBusConnectionString);
        var taskList = new List<Task>();
        for (int i = 0; i < Store.Length; i++)
        {
            taskList.Add(SendItems(client, Store[i]));
        }

        await Task.WhenAll(taskList);
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }
    Console.WriteLine("\nAll messages sent.\n");
}

private async Task SendItems(ServiceBusClient client, string store)
{
    // create the sender
    ServiceBusSender tc = client.CreateSender(TopicName);

    for (int i = 0; i < NrOfMessagesPerStore; i++)
    {
        Random r = new Random();
        Item item = new Item(r.Next(5), r.Next(5), r.Next(5));

        // Note the extension class which is serializing an deserializing messages
        ServiceBusMessage message = item.AsMessage();
        message.To = store;
        message.ApplicationProperties.Add("StoreId", store);
        message.ApplicationProperties.Add("Price", item.GetPrice().ToString());
        message.ApplicationProperties.Add("Color", item.GetColor());
        message.ApplicationProperties.Add("Category", item.GetItemCategory());

        await tc.SendMessageAsync(message);
        Console.WriteLine($"Sent item to Store {store}. Price={item.GetPrice()}, Color={item.GetColor()}, Category={item.GetItemCategory()}"); ;
    }
}
```

### <a name="receive-messages"></a>Üzenetek fogadása

Az üzenetek fogadása szintén feladatlistával történik, és a kód kötegelést használ. Kötegeléssel küldhet és fogadhat is üzeneteket, de ez a példa csak a kötegelt üzenetfogadást mutatja be. A valóságban azonban a megszakítás helyett érdemes megtartani a ciklust, és megadni egy nagyobb időtartamot, például egy percet. A közvetítő fogadási meghívása ennyi ideig marad nyitva. Ha üzenet érkezik, a rendszer azonnal visszaadja, és új fogadási hívást kezdeményez. Ezt a koncepciót *hosszú lekérdezésnek* nevezik. A fogadópumpa, amely a [rövid útmutatóban](service-bus-quickstart-portal.md) és az adattár számos további mintájában is látható, jellemzőbb megoldás.

```csharp
public async Task Receive()
{
    var taskList = new List<Task>();
    for (var i = 0; i < Subscriptions.Length; i++)
    {
        taskList.Add(this.ReceiveMessages(Subscriptions[i]));
    }

    await Task.WhenAll(taskList);
}

private async Task ReceiveMessages(string subscription)
{
    await using var client = new ServiceBusClient(ServiceBusConnectionString);
    ServiceBusReceiver receiver = client.CreateReceiver(TopicName, subscription);

    // In reality you would not break out of the loop like in this example but would keep looping. The receiver keeps the connection open
    // to the broker for the specified amount of seconds and the broker returns messages as soon as they arrive. The client then initiates
    // a new connection. So in reality you would not want to break out of the loop. 
    // Also note that the code shows how to batch receive, which you would do for performance reasons. For convenience you can also always
    // use the regular receive pump which we show in our Quick Start and in other github samples.
    while (true)
    {
        try
        {
            //IList<Message> messages = await receiver.ReceiveAsync(10, TimeSpan.FromSeconds(2));
            // Note the extension class which is serializing an deserializing messages and testing messages is null or 0.
            // If you think you did not receive all messages, just press M and receive again via the menu.
            IReadOnlyList<ServiceBusReceivedMessage> messages = await receiver.ReceiveMessagesAsync(maxMessages: 100);

            if (messages.Any())
            {
                foreach (ServiceBusReceivedMessage message in messages)
                {
                    lock (Console.Out)
                    {
                        Item item = message.As<Item>();
                        IReadOnlyDictionary<string, object> myApplicationProperties = message.ApplicationProperties;
                        Console.WriteLine($"StoreId={myApplicationProperties["StoreId"]}");
                        if (message.Subject != null)
                        {
                            Console.WriteLine($"Subject={message.Subject}");
                        }
                        Console.WriteLine(
                            $"Item data: Price={item.GetPrice()}, Color={item.GetColor()}, Category={item.GetItemCategory()}");
                    }

                    await receiver.CompleteMessageAsync(message);
                }
            }
            else
            {
                break;
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.ToString());
        }
    }
}
```

> [!NOTE]
> Az erőforrás-Service Bus a [Service Bus Explorer.](https://github.com/paolosalvatori/ServiceBusExplorer/) A Service Bus Explorer lehetővé teszi a felhasználók számára, hogy egy Service Bus névtérhez csatlakozzon, és könnyen felügyelik az üzenetküldési entitásokat. Az eszköz speciális funkciókat biztosít, például az importálási/exportálási funkciókat, valamint a témakörök, üzenetsorok, előfizetések, továbbítási szolgáltatások, értesítési központok és eseményközpontok tesztelésének képességét. 

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban erőforrásokat osztott ki az Azure Portal használatával, majd üzeneteket küldött és fogadott egy Service Bus-témakörtől és a hozzá tartozó előfizetésektől. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
> * Service Bus-témakör és egy vagy több hozzá tartozó előfizetés létrehozása az Azure Portal használatával
> * Témakörszűrők hozzáadása .NET-kód használatával
> * Két különböző tartalmú üzenet létrehozása
> * Az üzenetek elküldése és annak ellenőrzése, hogy megérkeztek-e a megfelelő előfizetésekbe
> * Üzenet fogadása az előfizetésektől

Az üzenetküldéssel és -fogadással kapcsolatos további példákért tekintse meg a [Service Bus-mintákat a GitHubon](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted).

Folytassa a következő oktatóanyaggal, ha szeretne többet megtudni a Service Bus közzétételi/előfizetési funkcióiról.

> [!div class="nextstepaction"]
> [Reagálás eseményekre az Event Griden keresztül](service-bus-to-event-grid-integration-example.md)

[ingyenes fiók]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Azure portal]: https://portal.azure.com/

[connection-string]: ./media/service-bus-tutorial-topics-subscriptions-portal/connection-string.png
[service-bus-flow]: ./media/service-bus-tutorial-topics-subscriptions-portal/about-service-bus-topic.png
