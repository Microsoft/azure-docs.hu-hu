---
title: A Azure Portal témakörök és előfizetések Service Bus létrehozásához
description: 'Rövid útmutató: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre egy Service Bus-témakört és -előfizetést ehhez a témakörhez a Azure Portal.'
author: spelluru
ms.author: spelluru
ms.date: 06/23/2020
ms.topic: quickstart
ms.custom:
- mode-portal
ms.openlocfilehash: 25b0579f05c1f7669a8dfc1df02e9a1f575ea066
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537154"
---
# <a name="use-the-azure-portal-to-create-a-service-bus-topic-and-subscriptions-to-the-topic"></a>Service Bus-témakör és -előfizetések létrehozása a témakörhöz az Azure Portal használatával
Ebben a rövid útmutatóban a Azure Portal létrehoz egy Service Bus, majd előfizetéseket hoz létre ehhez a témakörhez. 

## <a name="what-are-service-bus-topics-and-subscriptions"></a>Mik azok a Service Bus-üzenettémák és -előfizetések?
A Service Bus-üzenettémák és -előfizetések *közzétételi/előfizetési* modellt biztosítanak az üzenettovábbításhoz. Üzenettémák és előfizetések használata esetén az elosztott alkalmazások összetevői nem közvetlenül egymással kommunikálnak, hanem egy közvetítőként szolgáló üzenettémakörön keresztül.

![Az üzenettémakörök alapfogalmai](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

A Service Bus üzenetsorokkal ellentétben, amelyekben az egyes üzeneteket egyetlen fogyasztó feldolgozta, a témakörök és előfizetések egy-a-több hez típusú kommunikációt biztosítanak közzétételi/feliratkozási minta használatával. Egy üzenettémakörhöz több előfizetést is lehet regisztrálni. Ha egy üzenetet elküldenek egy témakörbe, az összes előfizetés számára elérhetővé válik, amelyek egymástól függetlenül kezelhetik és dolgozhatják fel az üzenetet. Az egyes témakörökre való előfizetés egy virtuális üzenetsorra hasonlít, amely minden, a témakörnek elküldött üzenetről kap egy másolatot. Igény szerint előfizetésenként is regisztrálhat szűrőszabályokat egy témakörre, így szűrheti vagy korlátozhatja, hogy egy témakör mely üzenetei mely témakörök előfizetéseit kapják meg.

Service Bus témakörök és előfizetések lehetővé teszik nagy számú üzenet feldolgozását nagy számú felhasználóra és alkalmazásra.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-topics-three-subscriptions-portal](../../includes/service-bus-create-topics-three-subscriptions-portal.md)]

> [!NOTE]
> Az erőforrásokat Service Bus kezelheti a [Service Bus Explorer.](https://github.com/paolosalvatori/ServiceBusExplorer/) A Service Bus Explorer lehetővé teszi a felhasználók számára, hogy egy Service Bus névtérhez csatlakozzon, és könnyen felügyelik az üzenetküldési entitásokat. Az eszköz olyan speciális funkciókat biztosít, mint az importálási/exportálási funkciók, vagy a témakör, az üzenetsorok, az előfizetések, a továbbítási szolgáltatások, az értesítési központok és az eseményközpontok tesztelése. 

## <a name="next-steps"></a>Következő lépések
Ebben a cikkben létrehozott egy új Service Bus, egy témakört a névtérben, és három előfizetést a témakörre. Ha meg szeretne ismerkedni az üzeneteknek a témakörben való közzétételével és az előfizetésből származó üzenetekre való feliratkozás menetével, tekintse meg az üzenetek közzétételével és feliratkozástal kapcsolatos szakaszának alábbi rövid **útmutatóit.** 

- [.NET](service-bus-dotnet-how-to-use-topics-subscriptions.md)
- [Java](service-bus-java-how-to-use-topics-subscriptions.md)
- [JavaScript](service-bus-nodejs-how-to-use-topics-subscriptions.md)
- [Python](service-bus-python-how-to-use-topics-subscriptions.md)
- [PHP](service-bus-php-how-to-use-topics-subscriptions.md)
- [Ruby](service-bus-ruby-how-to-use-topics-subscriptions.md)
