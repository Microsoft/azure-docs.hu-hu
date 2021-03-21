---
title: fájl belefoglalása
description: fájl belefoglalása
services: service-bus-messaging, event-hubs
author: spelluru
ms.service: service-bus-messaging, event-hubs
ms.topic: include
ms.date: 12/12/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 1ce983ee739a4a124a93c7913f092b23dfec3cbd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98900976"
---
## <a name="what-is-a-replication-task"></a>Mi az a replikációs feladat?

Egy replikációs feladat eseményeket fogad egy forrásból, és továbbítja azokat a célhelyre.
A legtöbb replikációs feladat változatlanul továbbítja az eseményeket, és a metaadatokat a legtöbb esetben a metaadat-struktúrák között végzi el, ha a forrás-és a célként megadott protokollok eltérnek. 

A replikációs feladatok általában állapot nélküliek, ami azt jelenti, hogy nem osztják meg az állapotot vagy más mellékhatásokat a feladatok szekvenciális vagy párhuzamos végrehajtása során. Ez a kötegek és a láncolás esetében is igaz, ami a stream meglévő állapotának tetején is megvalósítható. 

Így a replikációs feladatok eltérnek az összesítési feladatoktól, amelyek általában állapottal rendelkeznek, és az elemzési keretrendszerek és szolgáltatások (például a [Azure stream Analytics](../articles/stream-analytics/stream-analytics-introduction.md)) tartománya.

## <a name="replication-applications-and-tasks-in-azure-functions"></a>A Azure Functions replikációs alkalmazásai és feladatai

Azure Functions a replikációs feladat egy olyan [trigger](../articles/azure-functions/functions-triggers-bindings.md) használatával valósul meg, amely egy vagy több bemeneti üzenetet szerzi be egy konfigurált forrásból, valamint egy [kimeneti kötést](../articles/azure-functions/functions-triggers-bindings.md#binding-direction) , amely továbbítja a forrásból a konfigurált célra küldött üzeneteket. 

| Eseményindító  | Kimenet |
|----------|--------|
| [Azure Event Hubs trigger](../articles/azure-functions/functions-bindings-event-hubs-trigger.md?tabs=csharp) | [Az Azure Event hub kimeneti kötése](../articles/azure-functions/functions-bindings-event-hubs-output.md?tabs=csharp) |
| [Azure Service Bus trigger](../articles/azure-functions/functions-bindings-service-bus-trigger.md?tabs=csharp) | [Azure Service Bus kimeneti kötés](../articles/azure-functions/functions-bindings-service-bus-output.md?tabs=csharp)
| [Azure IoT Hub trigger](../articles/azure-functions/functions-bindings-event-iot-trigger.md?tabs=csharp) | [Azure IoT Hub kimeneti kötés](../articles/azure-functions/functions-bindings-event-iot-output.md?tabs=csharp)
| [Azure Event Grid trigger](../articles/azure-functions/functions-bindings-event-grid-trigger.md?tabs=csharp) | [Azure Event Grid kimeneti kötés](../articles/azure-functions/functions-bindings-event-grid-output.md?tabs=csharp)
| [Azure Queue Storage-eseményindító](../articles/azure-functions/functions-bindings-storage-queue-trigger.md?tabs=csharp) | [Azure Queue Storage kimeneti kötés](../articles/azure-functions/functions-bindings-storage-queue-output.md?tabs=csharp)
| [Apache Kafka trigger](https://github.com/azure/azure-functions-kafka-extension) | [Apache Kafka kimeneti kötés](https://github.com/azure/azure-functions-kafka-extension)
| [RabbitMQ-trigger](https://github.com/azure/azure-functions-rabbitmq-extension) | [RabbitMQ kimeneti kötés](https://github.com/azure/azure-functions-rabbitmq-extension) 
| | [Azure Notification Hubs kimeneti kötés](../articles/azure-functions/functions-bindings-notification-hubs.md)
||[Az Azure Signaler szolgáltatás kimeneti kötése](../articles/azure-functions/functions-bindings-signalr-service-output.md?tabs=csharp)
||[Twilio SendGrid kimeneti kötés](../articles/azure-functions/functions-bindings-sendgrid.md?tabs=csharp)

A replikációs feladatok a replikálási alkalmazásban ugyanúgy telepíthetők, mint bármely más Azure Functions alkalmazás. Több feladatot is beállíthat ugyanahhoz az alkalmazáshoz. 

A Azure Functions Premium esetében több replikációs alkalmazás is megoszthatja ugyanazt az alapul szolgáló erőforráskészletet, amelyet egy App Service tervnek nevezünk. Ez azt jelenti, hogy a .NET-ben írt replikációs feladatokat egyszerűen rézvezetékes végezhet a Java-ban írt replikációs feladatokkal, például a következő módon:. Ez akkor számít, ha ki szeretné használni az olyan könyvtárak előnyeit, mint például az Apache Camel, amely csak a Java számára érhető el, és ha az adott integrációs útvonalhoz a legjobb megoldás, akkor is, ha a többi replikációs feladathoz általában más nyelvet és futtatókörnyezetet szeretne használni. 

Ha elérhető, érdemes a Batch-orientált eseményindítókat előnyben részesíteni az egyes eseményeket vagy üzeneteket kézbesítő eseményindítókkal szemben, és mindig a teljes esemény vagy üzenet struktúráját kell megszereznie ahelyett, hogy az Azure Function [paraméterének kötési kifejezéseit](../articles/azure-functions/functions-bindings-expressions-patterns.md)kellene használnia.

A függvény nevének tükröznie kell a csatlakoztatni kívánt forrás-és célhelyet, és előtaggal kell rendelkeznie az alkalmazás konfigurációs fájljaiban található kapcsolati karakterláncokra vagy más konfigurációs elemekre az adott névvel. 

### <a name="data-and-metadata-mapping"></a>Adatok és metaadatok megfeleltetése

Ha úgy döntött, hogy a bemeneti eseményindítók és a kimeneti kötések párosítását választotta, néhány leképezést kell végrehajtania a különböző esemény-vagy üzenetkezelési típusok között, kivéve, ha az eseményindító típusa és a kimenete azonos.

Az Event Hubs és Service Bus közötti üzeneteket másoló egyszerű replikációs feladatokhoz nem kell saját kódot írnia, de a replikálási mintákhoz [megadott segédprogram-könyvtárra](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication) támaszkodhat.

### <a name="retry-policy"></a>Újrapróbálkozási szabályzat

Ha el szeretné kerülni az adatvesztést egy replikációs függvény egyik oldalán, az újrapróbálkozási házirendet robusztus értékre kell állítania. Az újrapróbálkozási szabályzat konfigurálásához tekintse meg az újrapróbálkozások [Azure functions dokumentációját](../articles/azure-functions/functions-bindings-error-pages.md) . 

A példában szereplő projektekhez kiválasztott házirend-beállítások egy exponenciális leállítási [stratégiát állítanak be az](https://github.com/Azure-Samples/azure-messaging-replication-dotnet) újrapróbálkozási időközökkel 5 másodperctől 15 percre, az adatvesztés elkerülése érdekében pedig végtelen újrapróbálkozást végeznek. 

Service Bus esetében tekintse át az eseményindítók [használatának az eseményindító rugalmassága](../articles/azure-functions/functions-bindings-error-pages.md#using-retry-support-on-top-of-trigger-resilience) érdekében című szakaszt az eseményindítók interakciójának és a várólista számára meghatározott maximális kézbesítések számának megismeréséhez.

### <a name="setting-up-a-replication-application-host"></a>Replikációs alkalmazás gazdagépének beállítása

Egy replikációs alkalmazás egy vagy több replikációs feladat végrehajtási gazdagépe. 

Ez egy Azure Functions alkalmazás, amely a (z) vagy a (z) Azure Functions Premium csomagon történő futtatásra van konfigurálva. Minden replikációs alkalmazásnak [rendszer vagy felhasználó által hozzárendelt felügyelt identitás](../articles/app-service/overview-managed-identity.md)alatt kell futnia. 

A csatolt Azure Resource Manager (ARM-) Sablonok replikációs alkalmazást hoznak létre és konfigurálnak a következővel:

* egy Azure Storage-fiók a replikálási folyamat és a naplók nyomon követéséhez,
* egy rendszerhez rendelt felügyelt identitás és 
* Az Azure monitorozása és Application Insights integrációja a figyeléshez.

Az Azure-beli virtuális hálózathoz (VNet) kötött Event Hubs eléréséhez szükséges replikációs alkalmazásoknak a Azure Functions Premium csomagot kell használniuk, és úgy kell konfigurálni, hogy ugyanahhoz a VNet csatolva legyenek, amely egyben az elérhető lehetőségek egyike is.


|       | Üzembe helyezés | Vizualizáció  
|-------|------------------|--------------|---------------|
| **Azure Functions használati terv** | [![Üzembe helyezés az Azure-ban](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2FAconsumption%2Fazuredeploy.json)|[![Vizualizáció](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fconsumption%2Fazuredeploy.json)
| **Prémium csomag Azure Functions** |[![Üzembe helyezés az Azure-ban](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium%2Fazuredeploy.json) | [![Vizualizáció](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium%2Fazuredeploy.json)
| **Prémium csomag Azure Functions a VNet** | [![Üzembe helyezés az Azure-ban](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium-vnet%2Fazuredeploy.json)|[![Vizualizáció](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium-vnet%2Fazuredeploy.json)


### <a name="examples"></a>Példák

A [minták tárháza](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/) több példát is tartalmaz a replikálási feladatokra, amelyek Event Hubs és/vagy Service Bus entitások között másolnak eseményeket.

Az esemény Event Hubs közötti másolásához egy Event hub-eseményindítót kell használnia az Event hub kimeneti kötésével:

```csharp
[FunctionName("telemetry")]
[ExponentialBackoffRetry(-1, "00:00:05", "00:05:00")]
public static Task Telemetry(
    [EventHubTrigger("telemetry", ConsumerGroup = "$USER_FUNCTIONS_APP_NAME.telemetry", Connection = "telemetry-source-connection")] EventData[] input,
    [EventHub("telemetry-copy", Connection = "telemetry-target-connection")] EventHubClient outputClient,
    ILogger log)
{
    return EventHubReplicationTasks.ForwardToEventHub(input, outputClient, log);
}
```

Service Bus entitások közötti másoláshoz használja a Service Bus triggert és a kimeneti kötést:

```csharp
[FunctionName("jobs-transfer")]
[ExponentialBackoffRetry(-1, "00:00:05", "00:05:00")]
public static Task JobsTransfer(
    [ServiceBusTrigger("jobs-transfer", Connection = "jobs-transfer-source-connection")] Message[] input,
    [ServiceBus("jobs", Connection = "jobs-target-connection")] IAsyncCollector<Message> output,
    ILogger log)
{
    return ServiceBusReplicationTasks.ForwardToServiceBus(input, output, log);
}
```

A segítő módszerek megkönnyítik a replikálást Event Hubs és Service Bus között:

| Forrás      | Cél      | Belépési pont 
|-------------|-------------|------------------------------------------------------------------------
| Eseményközpont   | Eseményközpont   | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToEventHub`
| Eseményközpont   | Service Bus | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToServiceBus`
| Service Bus | Eseményközpont   | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToEventHub`
| Service Bus | Service Bus | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToServiceBus`


### <a name="monitoring"></a>Figyelés

Ha szeretné megtudni, hogyan figyelheti meg a replikációs alkalmazást, tekintse meg a Azure Functions dokumentációjának [figyelés szakaszát](../articles/azure-functions/configure-monitoring.md) .

A replikációs feladatok figyelésének különösen hasznos eszköze a Application Insights alkalmazás- [hozzárendelés](../articles/azure-monitor/app/app-map.md), amely automatikusan létrejön a rögzített figyelési adatokból, és lehetővé teszi a replikációs feladat forrásának és a célzott átvitelek megbízhatóságának és teljesítményének a felderítését.

Az azonnali diagnosztikai információkhoz használhatja az [élő metrika](../articles/azure-monitor/app/live-stream.md) portál eszközt, amely kis késleltetésű vizualizációt biztosít a naplózási adatokhoz.

## <a name="next-steps"></a>Következő lépések

* [Azure Functions üzemelő példányok](../articles/azure-functions/functions-deployment-technologies.md)
* [Azure Functions diagnosztika](../articles/azure-functions/functions-diagnostics.md)
* [Azure Functions hálózati beállítások](../articles/azure-functions/functions-networking-options.md)
* [Azure-Application Insights](../articles/azure-monitor/app/app-insights-overview.md)