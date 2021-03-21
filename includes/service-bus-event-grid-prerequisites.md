---
title: fájl belefoglalása
description: fájl belefoglalása
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 10/15/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: d12df7197945a514ed8d3d0dca77271fb4bd0903
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96509537"
---
## <a name="prerequisites"></a>Előfeltételek
Ha nem rendelkezik Azure- [előfizetéssel](../articles/guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) .

## <a name="create-a-service-bus-namespace"></a>Service Bus-névtér létrehozása
Kövesse az oktatóanyag lépéseit: gyors útmutató [: a Azure Portal használatával hozzon létre egy Service Bus témakört és előfizetéseket a témakörbe](../articles/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md) a következő feladatok elvégzéséhez:

- Hozzon létre egy **prémium** szintű Service Bus névteret. 
- A kapcsolatok karakterláncának beolvasása. 
- Hozzon létre egy Service Bus témakört.
- Hozzon létre egy előfizetést a témakörbe. Ebben az oktatóanyagban csak egy előfizetésre van szükség, ezért nem kell létrehoznia az S2 és az S3 előfizetést. 

## <a name="send-messages-to-the-service-bus-topic"></a>Üzenetek küldése a Service Bus témakörnek
Ebben a lépésben egy mintául szolgáló alkalmazás használatával küld üzeneteket az előző lépésben létrehozott Service Bus témakörnek. 

1. A [GitHub Azure-Service-Bus adattár](https://github.com/Azure/azure-service-bus/)klónozása.
2. A Visual Studióban lépjen a *\samples\DotNet\Azure.Messaging.ServiceBus\ServiceBusEventGridIntegration* mappába, majd nyissa meg a *SBEventGridIntegration. SLN* fájlt.
3. A Megoldáskezelő ablakban bontsa ki a **MessageSender** projektet, és válassza a **program. cs** lehetőséget.
4. Cserélje le a `<SERVICE BUS NAMESPACE - CONNECTION STRING>` karakterláncot a Service Bus névterére és a `<TOPIC NAME>` témakör nevére. 

    ```csharp
    const string ServiceBusConnectionString = "<SERVICE BUS NAMESPACE - CONNECTION STRING>";
    const string TopicName = "<TOPIC NAME>";
    ```
5. Hozza létre és futtassa a programot az 5 tesztüzenet ( `const int numberOfMessages = 5;` ) Service Bus témakörbe való küldéséhez. 

    :::image type="content" source="./media/service-bus-event-grid-prerequisites/console-app-output.png" alt-text="Konzolos alkalmazás kimenete":::
