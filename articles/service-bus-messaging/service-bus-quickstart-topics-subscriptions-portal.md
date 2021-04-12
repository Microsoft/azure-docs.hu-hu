---
title: Service Bus témakörök és előfizetések létrehozása a Azure Portal használatával
description: 'Gyors útmutató: ebben a rövid útmutatóban megtudhatja, hogyan hozhat létre Service Bus témakört és előfizetéseket az adott témakörhöz a Azure Portal használatával.'
author: spelluru
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: spelluru
ms.openlocfilehash: 749ba3d2947dfd7defdf2549305f2a61c16ba713
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95803291"
---
# <a name="use-the-azure-portal-to-create-a-service-bus-topic-and-subscriptions-to-the-topic"></a>Service Bus-témakör és -előfizetések létrehozása a témakörhöz az Azure Portal használatával
Ebben a rövid útmutatóban a Azure Portal használatával hozhat létre egy Service Bus témakört, majd előfizetéseket hozhat létre ehhez a témakörhöz. 

## <a name="what-are-service-bus-topics-and-subscriptions"></a>Mik azok a Service Bus-üzenettémák és -előfizetések?
A Service Bus-üzenettémák és -előfizetések *közzétételi/előfizetési* modellt biztosítanak az üzenettovábbításhoz. Üzenettémák és előfizetések használata esetén az elosztott alkalmazások összetevői nem közvetlenül egymással kommunikálnak, hanem egy közvetítőként szolgáló üzenettémakörön keresztül.

![Az üzenettémakörök alapfogalmai](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

Service Bus várólistákkal ellentétben, amelyekben az egyes üzeneteket egyetlen fogyasztó dolgozza fel, a témakörök és az előfizetések egy-a-több kommunikációt biztosítanak a közzétételi/előfizetési minta használatával. Egy üzenettémakörhöz több előfizetést is lehet regisztrálni. Ha egy üzenetet elküldenek egy témakörbe, az összes előfizetés számára elérhetővé válik, amelyek egymástól függetlenül kezelhetik és dolgozhatják fel az üzenetet. Az egyes témakörökre való előfizetés egy virtuális üzenetsorra hasonlít, amely minden, a témakörnek elküldött üzenetről kap egy másolatot. Igény szerint regisztrálhat egy témakörhöz tartozó szűrési szabályokat, amelyekkel szűrheti vagy korlátozhatja, hogy mely üzeneteket kapjanak a témakör előfizetései.

Service Bus témakörök és előfizetések lehetővé teszik nagy mennyiségű üzenet feldolgozását nagy számú felhasználó és alkalmazás között.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-topics-three-subscriptions-portal](../../includes/service-bus-create-topics-three-subscriptions-portal.md)]

> [!NOTE]
> [Service Bus Explorerrel](https://github.com/paolosalvatori/ServiceBusExplorer/)kezelheti Service Bus erőforrásait. A Service Bus Explorer lehetővé teszi a felhasználók számára, hogy egy Service Bus névtérhez kapcsolódjanak, és egyszerű módon felügyelhetik az üzenetkezelési entitásokat. Az eszköz olyan speciális funkciókat biztosít, mint az importálási/exportálási funkció, illetve a témakör, a várólisták, az előfizetések, a Relay-szolgáltatások, az értesítési központok és az események hubok. 

## <a name="next-steps"></a>Következő lépések
Ebben a cikkben létrehozott egy Service Bus névteret, egy témakört a névtérben, és három előfizetést a témakörbe. Ha szeretné megtudni, hogyan tehet közzé üzeneteket a témakörben, és hogyan fizethet elő az előfizetésből érkező üzenetekre, tekintse meg az alábbi rövid útmutatók egyikét a **Közzététel és az előfizetés üzenetekben** című részben. 

- [.NET](service-bus-dotnet-how-to-use-topics-subscriptions.md)
- [Java](service-bus-java-how-to-use-topics-subscriptions.md)
- [JavaScript](service-bus-nodejs-how-to-use-topics-subscriptions.md)
- [Python](service-bus-python-how-to-use-topics-subscriptions.md)
- [PHP](service-bus-php-how-to-use-topics-subscriptions.md)
- [Ruby](service-bus-ruby-how-to-use-topics-subscriptions.md)