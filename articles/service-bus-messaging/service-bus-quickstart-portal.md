---
title: Üzenetsor Azure Portal létrehozása a Service Bus használatával
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre Service Bus névteret és egy üzenetsort a névtérben a Azure Portal.
author: spelluru
ms.author: spelluru
ms.date: 08/12/2020
ms.topic: quickstart
ms.custom:
- mode-portal
ms.openlocfilehash: e38d32c93453737060f654add58f09902b05ee45
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537191"
---
# <a name="use-azure-portal-to-create-a-service-bus-namespace-and-a-queue"></a>A Azure Portal használatával hozzon létre egy Service Bus-névteret és egy üzenetsort
Ez a rövid útmutató bemutatja, hogyan hozhat létre egy Service Bus névteret és egy üzenetsort a [Azure Portal.][Azure portal] Azt is bemutatja, hogyan kaphatja meg azokat az engedélyezési hitelesítő adatokat, amelyek használatával az ügyfélalkalmazás üzeneteket küldhet és fogadhat az üzenetsorból. 

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató befejezéséhez győződjön meg arról, hogy rendelkezik Azure-előfizetéssel. Ha nem rendelkezik Azure-előfizetéssel, kezdés előtt létrehozhat egy [ingyenes][] fiókot.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## <a name="next-steps"></a>Következő lépések
Ebben a cikkben létrehozott egy új Service Bus és egy üzenetsort a névtérben. Az üzenetek üzenetsorba vagy üzenetsorból való küldésének/fogadásának elsajátításért tekintse meg az Üzenetek küldése és fogadása szakasz egyik alábbi rövid **útmutatóját.** 

- [.NET](service-bus-dotnet-get-started-with-queues.md)
- [Java](service-bus-java-how-to-use-queues.md)
- [JavaScript](service-bus-nodejs-how-to-use-queues.md)
- [Python](service-bus-python-how-to-use-queues.md)
- [PHP](service-bus-php-how-to-use-queues.md)
- [Ruby](service-bus-ruby-how-to-use-queues.md)

[ingyenes fiók]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Azure portal]: https://portal.azure.com/

[service-bus-flow]: ./media/service-bus-quickstart-portal/service-bus-flow.png
