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
ms.openlocfilehash: 9bc641d680d927c44814f6814ebf6a6dde958c9e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98900975"
---
Azure Functions lehetővé teszi olyan konfigurációs replikációs feladatok létrehozását, amelyek egy előre elkészített belépési pontra támaszkodnak. A [Azure functions konfiguráció-alapú replikációs mintái](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config) bemutatják, hogyan használhatja fel az [előre elkészített segítőket](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication) a saját kódjában, vagy elkerülheti a kód teljes kezelését, és csak a konfigurációt használja.

## <a name="create-a-replication-task"></a>Replikációs feladat létrehozása
Egy ilyen replikációs feladat létrehozásához először hozzon létre egy új mappát a Project (projekt) mappa alatt. Az új mappa neve a függvény neve, például `europeToAsia` vagy `telemetry` . A név nem rendelkezik közvetlen korrelációval a használt üzenetküldési entitásokkal, és csak az Ön azonosítására szolgál. Ugyanabban a projektben több tucat függvényt is létrehozhat.

Ezután hozzon létre egy `function.json` fájlt a mappában. A fájl konfigurálja a függvényt. Kezdje a következő tartalommal:

``` JSON
{
    "configurationSource": "config",
    "bindings" : [
        {
            "direction": "in",
            "name": "input" 
        },
        {
            "direction": "out",
            "name": "output"
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": -1,
        "minimumInterval": "00:00:05",
        "maximumInterval": "00:05:00"
    },
    "disabled": false,
    "scriptFile": "../bin/Azure.Messaging.Replication.dll",
    "entryPoint": "Azure.Messaging.Replication.*"
}
```

Ebben a fájlban három konfigurációs lépést kell végrehajtania, amelyek attól függnek, hogy mely entitásokat kívánja összekapcsolni:

1. [A bemeneti irány konfigurálása](#configure-the-input-direction)
2. [A kimeneti irány konfigurálása](#configure-the-output-direction)
3. [Belépési pont konfigurálása](#configure-the-entry-point)

### <a name="configure-the-input-direction"></a>A bemeneti irány konfigurálása

#### <a name="event-hub-input"></a>Event hub-bemenet

Ha eseményt szeretne kapni az esemény-központból, adja hozzá a konfigurációs adatokat a "kötések" beállításban található felső szakaszhoz.

* **típus** – a "eventHubTrigger" típus.
* **kapcsolat** – az Event hub kapcsolati karakterláncához tartozó Alkalmazásbeállítások értékének neve. 
* **eventHubName** – az Event hub neve a kapcsolati karakterlánc által azonosított névtérben.
* **consumerGroup** – a fogyasztói csoport neve. Vegye figyelembe, hogy a név százalékos jelekkel van elfoglalva, ezért az Alkalmazásbeállítások értékére is hivatkozik.

```JSON
    ...
    "bindings" : [
        {
            "direction": "in",
            "type": "eventHubTrigger",
            "connection": "functionname-source-connection",
            "eventHubName": "eventHubA",
            "consumerGroup" : "%functionname-source-consumergroup%",
            "name": "input" 
        }
    ...
```

#### <a name="service-bus-queue-input"></a>Service Bus üzenetsor bemenete

Ha Service Bus-várólistáról szeretne eseményeket fogadni, adja hozzá a konfigurációs adatokat a "kötések" alatt található felső szakaszhoz.

* **típus** – a "serviceBusTrigger" típus.
* **kapcsolatok** – a Service Busi kapcsolatok karakterláncához tartozó Alkalmazásbeállítások értékének neve.
* **queueName** – a névtéren belüli Service Bus üzenetsor neve, amelyet a (z) a kapcsolatok karakterlánca azonosít.

```JSON
    ...
    "bindings" : [
        {
            "direction": "in",
            "type": "serviceBusTrigger",
            "connection": "functionname-source-connection",
            "queueName": "queue-a",
            "name": "input" 
        }
    ...
```

#### <a name="service-bus-topic-input"></a>Service Bus témakör bemenete

Ha egy Service Bus témakörben szeretne eseményeket kapni, adja hozzá a konfigurációs adatokat a "kötések" alatt található felső szakaszhoz.

* **típus** – a "serviceBusTrigger" típus.
* **kapcsolatok** – a Service Busi kapcsolatok karakterláncához tartozó Alkalmazásbeállítások értékének neve. Az értéknek akkor kell lennie, `{FunctionName}-source-connection` Ha a megadott parancsfájlokat kívánja használni.
* **topicName** – a névtéren belüli Service Bus témakör neve, amelyet a (z) a kapcsolatok karakterlánca azonosít.
* **subscriptionName** – a megadott témakörben a Service Bus-előfizetés neve, amely a névtéren belül azonosítva van.

```JSON
    ...
    "bindings" : [
        {
            "direction": "in",
            "type": "serviceBusTrigger",
            "connection": "functionname-source-connection",
            "topicName": "topic-x",
            "subscriptionName" : "sub-y",
            "name": "input" 
        }
    ...
```

### <a name="configure-the-output-direction"></a>A kimeneti irány konfigurálása

#### <a name="event-hub-output"></a>Event hub-kimenet

Ha az eseményeket egy Event hubhoz szeretné továbbítani, adja hozzá a konfigurációs adatokat a "kötések" mezőben található alsó szakaszhoz.

* **típus** – a "eventHub" típus.
* **kapcsolat** – az Event hub kapcsolati karakterláncához tartozó Alkalmazásbeállítások értékének neve. 
* **eventHubName** – az Event hub neve a kapcsolati karakterlánc által azonosított névtérben.

```JSON
    ...
    "bindings" : [
        {
            ...
        },
        {
            "direction": "out",
            "type": "eventHub",
            "connection": "functionname-target-connection",
            "eventHubName": "eventHubB",
            "name": "output" 
        }
    ...
```

#### <a name="service-bus-queue-output"></a>Service Bus üzenetsor kimenete

Ha az eseményeket egy Service Bus-várólistára kívánja továbbítani, adja hozzá a konfigurációs adatokat a "kötések" mezőben található alsó szakaszhoz.

* **típus** – a "serviceBus" típus.
* **kapcsolatok** – a Service Busi kapcsolatok karakterláncához tartozó Alkalmazásbeállítások értékének neve. 
* **queueName** – a névtéren belüli Service Bus üzenetsor neve, amelyet a (z) a kapcsolatok karakterlánca azonosít.

```JSON
    ...
    "bindings" : [
        {
            ...
        },
        {
            "direction": "out",
            "type": "serviceBus",
            "connection": "functionname-target-connection",
            "queueName": "queue-b",
            "name": "output" 
        }
    ...
```

#### <a name="service-bus-topic-output"></a>Service Bus témakör kimenete

Ha az eseményeket egy Service Bus témakörbe szeretné továbbítani, adja hozzá a konfigurációs adatokat az alsó szakaszhoz a "kötések" alatt, amely meghatározza a következőt:

* **típus** – a "serviceBus" típus.
* **kapcsolatok** – a Service Busi kapcsolatok karakterláncához tartozó Alkalmazásbeállítások értékének neve. 
* **topicName** – a névtéren belüli Service Bus témakör neve, amelyet a (z) a kapcsolatok karakterlánca azonosít.

```JSON
    ...
    "bindings" : [
        {
            ...
        },
        {
            "direction": "out",
            "type": "serviceBus",
            "connection": "functionname-target-connection",
            "topicName": "topic-b",
            "name": "output" 
        }
    ...
```

### <a name="configure-the-entry-point"></a>Belépési pont konfigurálása

A belépési pont konfigurálása a szabványos replikációs feladatok egyikét választja. Ha módosítja a `Azure.Messaging.Replication` projektet, hozzáadhat feladatokat is, és itt tekintheti meg őket. Ilyenek például a következők:

```JSON
    ...
    "scriptFile": "../dotnet/bin/Azure.Messaging.Replication.dll",
    "entryPoint": "Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToEventHub"
    ...
```

A következő táblázat a források és a célok kombinációinak helyes értékeit adja meg:

| Forrás      | Cél      | Belépési pont 
|-------------|-------------|------------------------------------------------------------------------
| Eseményközpont   | Eseményközpont   | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToEventHub`
| Eseményközpont   | Service Bus | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToServiceBus`
| Service Bus | Eseményközpont   | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToEventHub`
| Service Bus | Service Bus | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToServiceBus`

### <a name="retry-policy"></a>Újrapróbálkozási szabályzat

Az újrapróbálkozási szabályzat konfigurálásához tekintse meg az újrapróbálkozások [Azure functions dokumentációját](../articles/azure-functions/functions-bindings-error-pages.md) . Az ebben a tárházban lévő projektekben kiválasztott házirend-beállítások egy exponenciális leállítási stratégiát állítanak be az újrapróbálkozási időközökkel 5 másodperc és 5 perc között, és az adatvesztés elkerülése érdekében az újrapróbálkozások száma végtelen.

Service Bus esetében tekintse át az eseményindítók [használatának az eseményindító rugalmassága](../articles/azure-functions/functions-bindings-error-pages.md#using-retry-support-on-top-of-trigger-resilience) érdekében című szakaszt az eseményindítók interakciójának és a várólista számára meghatározott maximális kézbesítések számának megismeréséhez.

### <a name="build-deploy-and-configure"></a>Létrehozás, üzembe helyezés és konfigurálás

Amíg a konfigurációra koncentrál, a feladatokhoz szükség van egy telepíthető alkalmazás létrehozására és a Azure Functions gazdagépek konfigurálására úgy, hogy az tartalmazza az összes szükséges információt az adott végpontokhoz való kapcsolódáshoz. 

Ez az újrafelhasználható parancsfájlokkal együtt a [Azure functions konfigurációs alapú replikációs mintáit](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config)mutatja be.