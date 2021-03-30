---
title: Azure Relay Hibrid kapcsolatok-HTTP-kérelmek a .NET-ben
description: C#-konzolalkalmazást hozhat létre a hibrid Azure Relay-kapcsolatok HTTP-kéréseihez a .NET-ben.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 06/23/2020
ms.openlocfilehash: 7a11abb984da3601a4d6aa921224e01f94d0871c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88922582"
---
# <a name="get-started-with-relay-hybrid-connections-http-requests-in-net"></a>Ismerkedés a hibrid Relay-kapcsolatok HTTP-kéréseivel a .NET-ben
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

Ebben a rövid útmutatóban olyan .NET-küldő és fogadó alkalmazásokat hoz létre, amelyek üzeneteket küldenek és fogadnak a HTTP protokoll használatával. Az alkalmazások a Azure Relay Hibrid kapcsolatok funkcióját használják. Az általános Azure Relayről a [Azure Relay](relay-what-is-it.md)című témakörben olvashat bővebben. 

Ebben a rövid útmutatóban a következő lépéseket hajtja végre:

1. Relay-névtér létrehozása az Azure Portal használatával.
2. Hibrid kapcsolat létrehozása ezen a névtéren az Azure Portal használatával.
3. Kiszolgálói (figyelő) konzolalkalmazás írása üzenetfogadási céllal.
4. Ügyfél-konzolalkalmazás (küldő) írása üzenetküldési céllal.
5. Alkalmazások futtatása. 

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következő előfeltételekre lesz szüksége:

* [Visual Studio 2015 vagy újabb verzió](https://www.visualstudio.com). A jelen oktatóanyag példái a Visual Studio 2017-et használják.
* Azure-előfizetés. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/), mielőtt hozzákezd.

## <a name="create-a-namespace"></a>Névtér létrehozása
[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-a-hybrid-connection"></a>Hibrid kapcsolat létrehozása
[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="create-a-server-application-listener"></a>Kiszolgálói alkalmazás (figyelő) létrehozása
A Visual Studióban egy C# nyelven íródott konzolalkalmazást hozunk létre az üzenetek figyeléséhez és a Relay-től való fogadásához.

[!INCLUDE [relay-hybrid-connections-http-requests-dotnet-get-started-server](../../includes/relay-hybrid-connections-http-requests-dotnet-get-started-server.md)]

## <a name="create-a-client-application-sender"></a>Ügyfélalkalmazás létrehozása (küldő)
A Visual Studióban egy C# nyelven íródott konzolalkalmazást hozunk létre az üzenetek Relay-be való küldéséhez.

[!INCLUDE [relay-hybrid-connections-http-requests-dotnet-get-started-client](../../includes/relay-hybrid-connections-http-requests-dotnet-get-started-client.md)]

## <a name="run-the-applications"></a>Az alkalmazások futtatása
1. Futtassa a kiszolgálóalkalmazást. A konzolon a következő szöveget fogja látni:

    ```
    Online
    Server listening
    ```
1. Futtassa az ügyfélalkalmazást. Megjelenik a `hello!` üzenet az ügyfélablakban. Az ügyfél HTTP-kérelmet küld a kiszolgálónak, és a kiszolgáló a következővel válaszolt: `hello!` . 
3. A konzolablakok bezárásához nyomja le az **ENTER** billentyűt mindkét konzolablakban. 

Gratulálunk, létrehozott egy teljes Hibrid kapcsolatok alkalmazást!

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban olyan .NET-ügyfél-és kiszolgálói alkalmazásokat hozott létre, amelyek HTTP-t használnak az üzenetek küldéséhez és fogadásához. A Azure Relay Hibrid kapcsolatok szolgáltatása szintén támogatja a websocketek használatát az üzenetek küldéséhez és fogadásához. Ha szeretné megismerni, hogyan használhatók a websocketek Azure Relay Hibrid kapcsolatoksal, tekintse meg a [WebSockets](relay-hybrid-connections-dotnet-get-started.md)rövid útmutatót.

Ebben a rövid útmutatóban a .NET-keretrendszert használta az ügyfél-és kiszolgálói alkalmazások létrehozásához. Az ügyfél-és kiszolgálói alkalmazások Node.js használatával történő írásához tekintse meg a [Node.js WebSockets](relay-hybrid-connections-node-get-started.md) rövid útmutatóját vagy a [Node.js http](relay-hybrid-connections-http-requests-dotnet-get-started.md)-gyors útmutatót.
