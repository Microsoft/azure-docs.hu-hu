---
title: Azure Service Bus entitások programozott létrehozása | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan lehet dinamikusan vagy programozott módon kiépíteni Service Bus névtereket és entitásokat.
ms.devlang: dotnet
ms.topic: article
ms.date: 01/13/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 57192ab2ee1624cb18de832ac91c95290da727df
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98539870"
---
# <a name="dynamically-provision-service-bus-namespaces-and-entities"></a>Service Bus névterek és entitások dinamikus kiépítése 
A Azure Service Bus felügyeleti kódtárak dinamikusan tudnak kiépíteni Service Bus névtereket és entitásokat. Ez összetett központi telepítéseket és üzenetkezelési forgatókönyveket tesz lehetővé, és lehetővé teszi, hogy programozott módon határozza meg, hogy milyen entitásokat kell kiépíteni. Ezek a kódtárak jelenleg a .NET-hez érhetők el.

## <a name="overview"></a>Áttekintés
A Service Bus entitások létrehozásához és kezeléséhez három felügyeleti függvénytár érhető el. Ezek a következők:

- [Azure. Messaging. ServiceBus. Administration](#azuremessagingservicebusadministration)
- [Microsoft. Azure. ServiceBus. Management](#microsoftazureservicebusmanagement)
- [Microsoft.Azure.Management.ServiceBus](#microsoftazuremanagementservicebus)

Ezen csomagok mindegyike támogatja a **várólistákon, témakörökön és előfizetéseken** végrehajtott létrehozási, lekérési, listázási, törlési, frissítési, törlési és frissítési műveleteket. Azonban csak a [Microsoft. Azure. Management. ServiceBus](#microsoftazuremanagementservicebus) támogatja a **névterek** létrehozási, frissítési, listázási, lekérési és törlési műveleteit, valamint a sas-kulcsok listázását és újragenerálását stb. 

A Microsoft. Azure. Management. ServiceBus függvénytár csak Azure Active Directory (Azure AD) hitelesítéssel működik, és nem támogatja a kapcsolatok karakterláncának használatát. Míg a másik két függvénytár (Azure. Messaging. ServiceBus és a Microsoft. Azure. ServiceBus) támogatja a szolgáltatással való hitelesítéshez szükséges kapcsolódási karakterláncot, és könnyebben használható. Ezen könyvtárak között az Azure. Messaging. ServiceBus a legújabb, és ez az, amit ajánlott használni.

A következő szakaszokban további részleteket talál a könyvtárakról. 

## <a name="azuremessagingservicebusadministration"></a>Azure. Messaging. ServiceBus. Administration
A névterek, várólisták, témakörök és előfizetések kezeléséhez használhatja az [Azure. Messaging. ServiceBus. felügyeleti](/dotnet/api/azure.messaging.servicebus.administration) névtér [ServiceBusAdministrationClient](/dotnet/api/azure.messaging.servicebus.administration.servicebusadministrationclient) osztályát. Itt látható a mintakód. A teljes példa: a [szifilisz példája](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/servicebus/Azure.Messaging.ServiceBus/tests/Samples/Sample07_CrudOperations.cs).

```csharp
using System;
using System.Threading.Tasks;

using Azure.Messaging.ServiceBus.Administration;

namespace adminClientTrack2
{
    class Program
    {
        public static void Main()
        {
            MainAsync().GetAwaiter().GetResult();
        }

        private static async Task MainAsync()
        {
            string connectionString = "SERVICE BUS NAMESPACE CONNECTION STRING";
            string QueueName = "QUEUE NAME";
            string TopicName = "TOPIC NAME";
            string SubscriptionName = "SUBSCRIPTION NAME";

            var adminClient = new ServiceBusAdministrationClient(connectionString);
            bool queueExists = await adminClient.QueueExistsAsync(QueueName);
            if (!queueExists)
            {
                var options = new CreateQueueOptions(QueueName)
                {
                    MaxDeliveryCount = 3                    
                };
                await adminClient.CreateQueueAsync(options);
            }


            bool topicExists = await adminClient.TopicExistsAsync(TopicName);
            if (!topicExists)
            {
                var options = new CreateTopicOptions(TopicName)
                {
                    MaxSizeInMegabytes = 1024
                };
                await adminClient.CreateTopicAsync(options);
            }

            bool subscriptionExists = await adminClient.SubscriptionExistsAsync(TopicName, SubscriptionName);
            if (!subscriptionExists)
            {
                var options = new CreateSubscriptionOptions(TopicName, SubscriptionName)
                {
                    DefaultMessageTimeToLive = new TimeSpan(2, 0, 0, 0)
                };
                await adminClient.CreateSubscriptionAsync(options);
            }
        }
    }
}

```


## <a name="microsoftazureservicebusmanagement"></a>Microsoft. Azure. ServiceBus. Management 
A [ManagementClient](/dotnet/api/microsoft.azure.servicebus.management.managementclient) osztályt a [Microsoft. Azure. ServiceBus. Management](/dotnet/api/microsoft.azure.servicebus.management) névtérben használhatja a névterek, várólisták, témakörök és előfizetések kezeléséhez. A mintakód a következő: 

> [!NOTE]
> Javasoljuk, hogy az `ServiceBusAdministrationClient` osztályt a `Azure.Messaging.ServiceBus.Administration` könyvtárból használja, amely a legújabb SDK. Részletekért tekintse meg az [első szakaszt](#azuremessagingservicebusadministration). 

```csharp
using System;
using System.Threading.Tasks;

using Microsoft.Azure.ServiceBus.Management;

namespace SBusManagementClient
{
    class Program
    {
        public static void Main()
        {
            MainAsync().GetAwaiter().GetResult();
        }

        private static async Task MainAsync()
        {
            string connectionString = "SERVICE BUS NAMESPACE CONNECTION STRING";
            string QueueName = "QUEUE NAME";
            string TopicName = "TOPIC NAME";
            string SubscriptionName = "SUBSCRIPTION NAME";

            var managementClient = new ManagementClient(connectionString);
            bool queueExists = await managementClient.QueueExistsAsync(QueueName);
            if (!queueExists)
            {
                QueueDescription qd = new QueueDescription(QueueName);
                qd.MaxSizeInMB = 1024;
                qd.MaxDeliveryCount = 3;
                await managementClient.CreateQueueAsync(qd);
            }


            bool topicExists = await managementClient.TopicExistsAsync(TopicName);
            if (!topicExists)
            {
                TopicDescription td = new TopicDescription(TopicName);
                td.MaxSizeInMB = 1024;
                td.DefaultMessageTimeToLive = new TimeSpan(2, 0, 0, 0);
                await managementClient.CreateTopicAsync(td);
            }

            bool subscriptionExists = await managementClient.SubscriptionExistsAsync(TopicName, SubscriptionName);
            if (!subscriptionExists)
            {
                SubscriptionDescription sd = new SubscriptionDescription(TopicName, SubscriptionName);
                sd.DefaultMessageTimeToLive = new TimeSpan(2, 0, 0, 0);
                sd.MaxDeliveryCount = 3;
                await managementClient.CreateSubscriptionAsync(sd);
            }
        }
    }
}
```


## <a name="microsoftazuremanagementservicebus"></a>Microsoft.Azure.Management.ServiceBus 
Ez a könyvtár a Azure Resource Manager-alapú Control Plane SDK része. 

### <a name="prerequisites"></a>Előfeltételek

A könyvtár használatának megkezdéséhez hitelesítenie kell magát a Azure Active Directory (Azure AD) szolgáltatással. Az Azure AD-ben az Azure-erőforrásokhoz való hozzáférést biztosító egyszerű szolgáltatásként kell hitelesítenie magát. Az egyszerű szolgáltatásnév létrehozásával kapcsolatos információkért tekintse meg a következő cikkek egyikét:  

* [A Azure Portal használatával hozzon létre Active Directory alkalmazást és egyszerű szolgáltatást, amely hozzáférhet az erőforrásokhoz](../active-directory/develop/howto-create-service-principal-portal.md)
* [Szolgáltatásnév létrehozása erőforrások eléréséhez az Azure PowerShell használatával](../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* [Szolgáltatásnév létrehozása erőforrások eléréséhez az Azure CLI használatával](/cli/azure/create-an-azure-service-principal-azure-cli)

Ezek az oktatóanyagok egy `AppId` (ügyfél-azonosító), `TenantId` és `ClientSecret` (hitelesítési kulcs) használatát teszik lehetővé, amelyek mindegyike a felügyeleti kódtárak általi hitelesítéshez használatos. Legalább [**Azure Service Bus adattulajdonosi**](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner) vagy [**közreműködői**](../role-based-access-control/built-in-roles.md#contributor) engedélyekkel kell rendelkeznie ahhoz az erőforráscsoporthoz, amelyen futtatni szeretné a szolgáltatást.

### <a name="programming-pattern"></a>Programozási minta

A Service Bus-erőforrások kezelésére szolgáló minta egy közös protokollt követ:

1. Szerezze be a tokent az Azure AD-ből a **Microsoft. IdentityModel. clients. ActiveDirectory** könyvtár használatával:
   ```csharp
   var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

   var result = await context.AcquireTokenAsync("https://management.azure.com/", new ClientCredential(clientId, clientSecret));
   ```
2. Hozza létre az `ServiceBusManagementClient` objektumot:

   ```csharp
   var creds = new TokenCredentials(token);
   var sbClient = new ServiceBusManagementClient(creds)
   {
       SubscriptionId = SettingsCache["SubscriptionId"]
   };
   ```
3. Állítsa be a `CreateOrUpdate` paramétereket a megadott értékekre:

   ```csharp
   var queueParams = new QueueCreateOrUpdateParameters()
   {
       Location = SettingsCache["DataCenterLocation"],
       EnablePartitioning = true
   };
   ```
4. A hívás végrehajtása:

   ```csharp
   await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, QueueName, queueParams);
   ```

### <a name="complete-code-to-create-a-queue"></a>A várólista létrehozásához szükséges kód végrehajtása
Service Bus üzenetsor létrehozásához az alábbi mintakód vonatkozik. Teljes példaként tekintse meg a [.net-felügyeleti mintát a githubon](https://github.com/Azure-Samples/service-bus-dotnet-management/). 

```csharp
using System;
using System.Threading.Tasks;

using Microsoft.Azure.Management.ServiceBus;
using Microsoft.Azure.Management.ServiceBus.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;

namespace SBusADApp
{
    class Program
    {
        static void Main(string[] args)
        {
            CreateQueue().GetAwaiter().GetResult();
        }

        private static async Task CreateQueue()
        {
            try
            {
                var subscriptionID = "<SUBSCRIPTION ID>";
                var resourceGroupName = "<RESOURCE GROUP NAME>";
                var namespaceName = "<SERVICE BUS NAMESPACE NAME>";
                var queueName = "<NAME OF QUEUE YOU WANT TO CREATE>";

                var token = await GetToken();

                var creds = new TokenCredentials(token);
                var sbClient = new ServiceBusManagementClient(creds)
                {
                    SubscriptionId = subscriptionID,
                };

                var queueParams = new SBQueue();

                Console.WriteLine("Creating queue...");
                await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, queueName, queueParams);
                Console.WriteLine("Created queue successfully.");
            }
            catch (Exception e)
            {
                Console.WriteLine("Could not create a queue...");
                Console.WriteLine(e.Message);
                throw e;
            }
        }

        private static async Task<string> GetToken()
        {
            try
            {
                var tenantId = "<AZURE AD TENANT ID>";
                var clientId = "<APPLICATION/CLIENT ID>";
                var clientSecret = "<CLIENT SECRET>";

                var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

                var result = await context.AcquireTokenAsync(
                    "https://management.azure.com/",
                    new ClientCredential(clientId, clientSecret)
                );

                // If the token isn't a valid string, throw an error.
                if (string.IsNullOrEmpty(result.AccessToken))
                {
                    throw new Exception("Token result is empty!");
                }

                return result.AccessToken;
            }
            catch (Exception e)
            {
                Console.WriteLine("Could not get a token...");
                Console.WriteLine(e.Message);
                throw e;
            }
        }

    }
}
```

## <a name="fluent-library"></a>Fluent könyvtár
Az Service Bus entitások felügyeletére szolgáló Fluent könyvtár használatával kapcsolatban tekintse meg [ezt](https://github.com/Azure/azure-libraries-for-net/tree/master/Samples/ServiceBus)a példát. 

## <a name="next-steps"></a>Következő lépések
Tekintse meg a következő témaköröket: 

- [Azure. Messaging. ServiceBus. Administration](/dotnet/api/azure.messaging.servicebus.administration.servicebusadministrationclient)
- [Microsoft. Azure. ServiceBus. Management](/dotnet/api/microsoft.azure.servicebus.management.managementclient)
- [Microsoft.Azure.Management.ServiceBus](/dotnet/api/microsoft.azure.management.servicebus.servicebusmanagementclient)
- [Fluent](/dotnet/api/microsoft.azure.management.servicebus.fluent)