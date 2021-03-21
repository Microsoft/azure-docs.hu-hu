---
title: 'Oktatóanyag: dinamikus konfiguráció használata a leküldéses frissítéssel egy .NET Core-alkalmazásban'
titleSuffix: Azure App Configuration
description: Ebből az oktatóanyagból megtudhatja, hogyan lehet dinamikusan frissíteni a .NET Core-alkalmazások konfigurációs adatai leküldéses frissítés használatával
services: azure-app-configuration
documentationcenter: ''
author: abarora
manager: zhenlan
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 07/25/2020
ms.author: abarora
ms.openlocfilehash: 977982bf1a36b4b85524df2513f2272fe4a8d1bf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101701518"
---
# <a name="tutorial-use-dynamic-configuration-using-push-refresh-in-a-net-core-app"></a>Oktatóanyag: dinamikus konfiguráció használata a leküldéses frissítéssel egy .NET Core-alkalmazásban

Az App Configuration .NET Core ügyféloldali függvénytár támogatja a konfiguráció igény szerinti frissítését anélkül, hogy újra kellene indítani az alkalmazást. Egy alkalmazás konfigurálható úgy, hogy az alábbi két módszer egyikével vagy mindkettővel azonosítsa az alkalmazás konfigurációjának változásait.

1. Lekérdezési modell: ez az alapértelmezett viselkedés, amely lekérdezést használ a konfiguráció változásainak észlelésére. Ha egy beállítás gyorsítótárazott értéke lejár, a következő hívást `TryRefreshAsync` `RefreshAsync` küld a kiszolgálónak, hogy ellenőrizze, hogy megváltozott-e a konfiguráció, és szükség esetén lekéri a frissített konfigurációt.

1. Leküldéses modell: ez az [alkalmazás konfigurációs eseményeivel](./concept-app-configuration-event.md) azonosítja a konfiguráció módosításait. Miután az alkalmazás konfigurációja beállítja a kulcs értékének változási eseményeinek Azure Event Gridre való küldését, az alkalmazás ezeket az eseményeket használhatja a konfiguráció frissítéséhez szükséges kérelmek teljes számának optimalizálásához. Az alkalmazások dönthetnek úgy, hogy előfizethetnek ezekre közvetlenül a Event Grid, vagy az egyik [támogatott eseménykezelőt](../event-grid/event-handlers.md) , például egy webhookot, egy Azure-függvényt vagy egy Service Bus témakört.

Az alkalmazások dönthetnek úgy, hogy előfizethetnek ezekre az eseményekre közvetlenül a Event Grid vagy egy webhookon keresztül, illetve az események Azure Service Busba való továbbításával is. A Azure Service Bus SDK egy API-t biztosít egy olyan üzenetkezelő regisztrálásához, amely leegyszerűsíti ezt a folyamatot olyan alkalmazások esetében, amelyek nem rendelkeznek HTTP-végponttal, vagy nem kívánják lekérdezni az Event gridet a változások folyamatos változásairól.

Ez az oktatóanyag bemutatja, hogyan valósítható meg a dinamikus konfigurációs frissítések a kódban a leküldéses frissítés használatával. Ez a gyors üzembe helyezési útmutatóban bemutatott alkalmazásra épül. A folytatás előtt fejezze be [a .net Core-alkalmazás létrehozása az alkalmazás konfigurációjával](./quickstart-dotnet-core-app.md) című részből.

Az oktatóanyag lépéseihez bármilyen Kódszerkesztő használható. A [Visual Studio Code](https://code.visualstudio.com/) egy kiváló lehetőség, amely a Windows, MacOS és Linux platformokon érhető el.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Előfizetés beállítása a konfigurációs változási események elküldéséhez az alkalmazás konfigurációjától egy Service Bus témakörbe
> * Állítsa be a .NET Core-alkalmazást úgy, hogy az alkalmazás konfigurációjában megjelenő változásokra reagálva frissítse a konfigurációját.
> * Használja fel a legújabb konfigurációt az alkalmazásban.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez telepítse a [.net Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-azure-service-bus-topic-and-subscription"></a>Azure Service Bus témakör és előfizetés beállítása

Ez az oktatóanyag a Event Grid Service Bus integrációját használja, hogy leegyszerűsítse a konfigurációs változások észlelését azon alkalmazások esetében, amelyek folyamatosan nem kívánják lekérdezni az alkalmazás konfigurációját. A Azure Service Bus SDK egy API-t biztosít egy olyan üzenetkezelő regisztrálásához, amely a konfiguráció frissítésére használható, ha az alkalmazás konfigurációjában változások észlelhetők. Kövesse a rövid útmutató lépéseit [: a Azure Portal használatával hozzon létre egy Service Bus témakört és előfizetést](../service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md) a Service Bus-névtér,-témakör és-előfizetés létrehozásához.

Az erőforrások létrehozása után adja hozzá a következő környezeti változókat. Ezeket a rendszer a konfigurációs változásokhoz tartozó eseménykezelő regisztrálására használja az alkalmazás kódjában.

| Kulcs | Érték |
|---|---|
| ServiceBusConnectionString | A Service Bus-névtérhez tartozó kapcsolatok karakterlánca |
| ServiceBusTopic | A Service Bus témakör neve |
| ServiceBusSubscription | A Service Bus-előfizetés neve |

## <a name="set-up-event-subscription"></a>Esemény-előfizetés beállítása

1. Nyissa meg az alkalmazás konfigurációs erőforrását a Azure Portalban, majd kattintson a be gombra `+ Event Subscription` a `Events` panelen.

    ![Alkalmazás-konfigurációs események](./media/events-pane.png)

1. Adja meg a `Event Subscription` és a nevét `System Topic` .

    ![Esemény-előfizetés létrehozása](./media/create-event-subscription.png)

1. Jelölje ki a `Endpoint Type` t, válassza ki `Service Bus Topic` a Service Bus témakört, majd kattintson a be gombra `Confirm Selection` .

    ![Esemény-előfizetés Service Bus-végpont](./media/event-subscription-servicebus-endpoint.png)

1. `Create`Az esemény-előfizetés létrehozásához kattintson a be gombra.

1. Kattintson a `Event Subscriptions` `Events` panelre, és ellenőrizze, hogy sikeresen létrejött-e az előfizetés.

    ![Alkalmazás-konfigurációs esemény-előfizetések](./media/event-subscription-view.png)

> [!NOTE]
> A konfigurációs módosítások előfizetése során a rendszer egy vagy több szűrőt is használhat az alkalmazásnak eljuttatott események számának csökkentésére. Ezek [Event Grid előfizetési szűrők](../event-grid/event-filtering.md) vagy [Service Bus előfizetési szűrők](../service-bus-messaging/topic-filters.md)segítségével konfigurálhatók. Az előfizetési szűrő például arra használható, hogy csak az eseményekre fizessen elő olyan változásokra, amelyek egy adott karakterlánccal kezdődnek.

## <a name="register-event-handler-to-reload-data-from-app-configuration"></a>Az eseménykezelő regisztrálása az alkalmazások konfigurációjának betöltéséhez

Nyissa meg a *program. cs* fájlt, és frissítse a fájlt a következő kóddal.

```csharp
using Microsoft.Azure.ServiceBus;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Configuration.AzureAppConfiguration;
using System;
using System.Diagnostics;
using System.Text;
using System.Text.Json;
using System.Threading.Tasks;

namespace TestConsole
{
    class Program
    {
        private const string AppConfigurationConnectionStringEnvVarName = "AppConfigurationConnectionString"; // e.g. Endpoint=https://{store_name}.azconfig.io;Id={id};Secret={secret}
        private const string ServiceBusConnectionStringEnvVarName = "ServiceBusConnectionString"; // e.g. Endpoint=sb://{service_bus_name}.servicebus.windows.net/;SharedAccessKeyName={key_name};SharedAccessKey={key}
        private const string ServiceBusTopicEnvVarName = "ServiceBusTopic";
        private const string ServiceBusSubscriptionEnvVarName = "ServiceBusSubscription";

        private static IConfigurationRefresher _refresher = null;

        static async Task Main(string[] args)
        {
            string appConfigurationConnectionString = Environment.GetEnvironmentVariable(AppConfigurationConnectionStringEnvVarName);

            IConfiguration configuration = new ConfigurationBuilder()
                .AddAzureAppConfiguration(options =>
                {
                    options.Connect(appConfigurationConnectionString);
                    options.ConfigureRefresh(refresh =>
                        refresh
                            .Register("TestApp:Settings:Message")
                            .SetCacheExpiration(TimeSpan.FromDays(30))  // Important: Reduce poll frequency
                    );

                    _refresher = options.GetRefresher();
                }).Build();

            RegisterRefreshEventHandler();
            var message = configuration["TestApp:Settings:Message"];
            Console.WriteLine($"Initial value: {configuration["TestApp:Settings:Message"]}");

            while (true)
            {
                await _refresher.TryRefreshAsync();

                if (configuration["TestApp:Settings:Message"] != message)
                {
                    Console.WriteLine($"New value: {configuration["TestApp:Settings:Message"]}");
                    message = configuration["TestApp:Settings:Message"];
                }
                
                await Task.Delay(TimeSpan.FromSeconds(1));
            }
        }

        private static void RegisterRefreshEventHandler()
        {
            string serviceBusConnectionString = Environment.GetEnvironmentVariable(ServiceBusConnectionStringEnvVarName);
            string serviceBusTopic = Environment.GetEnvironmentVariable(ServiceBusTopicEnvVarName);
            string serviceBusSubscription = Environment.GetEnvironmentVariable(ServiceBusSubscriptionEnvVarName);
            SubscriptionClient serviceBusClient = new SubscriptionClient(serviceBusConnectionString, serviceBusTopic, serviceBusSubscription);

            serviceBusClient.RegisterMessageHandler(
                handler: (message, cancellationToken) =>
               {
                   string messageText = Encoding.UTF8.GetString(message.Body);
                   JsonElement messageData = JsonDocument.Parse(messageText).RootElement.GetProperty("data");
                   string key = messageData.GetProperty("key").GetString();
                   Console.WriteLine($"Event received for Key = {key}");

                   _refresher.SetDirty();
                   return Task.CompletedTask;
               },
                exceptionReceivedHandler: (exceptionargs) =>
                {
                    Console.WriteLine($"{exceptionargs.Exception}");
                    return Task.CompletedTask;
                });
        }
    }
}
```

A [SetDirty](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.iconfigurationrefresher.setdirty) metódussal lehet beállítani a gyorsítótárazott értéket a frissítésre regisztrált kulcs-értékekhez. Ezzel biztosíthatja, hogy a következő hívással `RefreshAsync` vagy `TryRefreshAsync` újból érvényesítse a gyorsítótárazott értékeket az alkalmazás konfigurációjával, és szükség esetén frissítse őket.

A rendszer véletlenszerű késleltetést ad hozzá, mielőtt a gyorsítótárazott érték inkonzisztensként van megjelölve, hogy csökkentse a lehetséges szabályozást, ha egyszerre több példány frissül. A gyorsítótárazott érték alapértelmezett maximális késleltetése 30 másodperc, de felülbírálható egy opcionális `TimeSpan` paraméternek a metódusba való átadásával `SetDirty` .

> [!NOTE]
> Ha csökkenteni szeretné az alkalmazás-konfigurációra irányuló kérések számát a leküldéses frissítés használatakor, fontos, hogy a `SetCacheExpiration(TimeSpan cacheExpiration)` megfelelő értékű `cacheExpiration` paramétert hívja meg. Ez vezérli a gyorsítótár lejárati idejét a lekéréses frissítéshez, és biztonsági hálóként használható arra az esetre, ha az esemény-előfizetéssel vagy a Service Bus előfizetéssel kapcsolatos probléma merül fel. A javasolt érték: `TimeSpan.FromDays(30)` .

## <a name="build-and-run-the-app-locally"></a>Az alkalmazás helyi létrehozása és futtatása

1. Állítson be egy **AppConfigurationConnectionString** nevű környezeti változót, és állítsa azt a hozzáférési kulcsra az alkalmazás konfigurációs tárolójához. Ha a Windows-parancssort használja, futtassa a következő parancsot, és indítsa újra a parancssort, hogy a módosítás érvénybe lépjen:

    ```console
     setx AppConfigurationConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Ha a Windows PowerShellt használja, futtassa a következő parancsot:

    ```powershell
     $Env:AppConfigurationConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Ha macOS vagy Linux rendszert használ, futtassa a következő parancsot:

    ```console
     export AppConfigurationConnectionString='connection-string-of-your-app-configuration-store'
    ```

1. Futtassa a következő parancsot a konzol alkalmazás létrehozásához:

    ```console
     dotnet build
    ```

1. A létrehozás sikeres befejezése után futtassa a következő parancsot az alkalmazás helyi futtatásához:

    ```console
     dotnet run
    ```

    ![A leküldéses frissítés futtatása a frissítés előtt](./media/dotnet-core-app-pushrefresh-initial.png)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **minden erőforrás** lehetőséget, majd válassza ki a gyors útmutatóban létrehozott app Configuration Store-példányt.

1. Válassza a **Configuration Explorer** lehetőséget, és frissítse a következő kulcsok értékeit:

    | Kulcs | Érték |
    |---|---|
    | TestApp: beállítások: üzenet | Adatok az Azure-alkalmazás konfigurációjában – frissítve |

1. Várjon 30 másodpercet, hogy az eseményt feldolgozzák, és a konfigurációt frissíteni lehessen.

    ![A leküldéses frissítés futtatása a frissítés után](./media/dotnet-core-app-pushrefresh-final.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban engedélyezte a .NET Core-alkalmazást, hogy dinamikusan frissítse a konfigurációs beállításokat az alkalmazás konfigurációjában. Ha meg szeretné tudni, hogyan használható az Azure felügyelt identitása az alkalmazás-konfigurációhoz való hozzáférés egyszerűsítéséhez, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Felügyelt identitások integrációja](./howto-integrate-azure-managed-service-identity.md)