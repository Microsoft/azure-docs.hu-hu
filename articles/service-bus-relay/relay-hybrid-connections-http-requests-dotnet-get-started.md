---
title: Ismerkedés a hibrid Azure Relay-kapcsolatok HTTP-kéréseivel a .NET-ben | Microsoft Docs
description: C#-konzolalkalmazást hozhat létre a hibrid Azure Relay-kapcsolatok HTTP-kéréseihez a .NET-ben.
services: service-bus-relay
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: d1386900-b942-4abf-acfc-38d2ef826253
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 08/16/2018
ms.author: spelluru
ms.openlocfilehash: e66a1651a46cfaeb7fb8b232eeb7cf6a2fb8044d
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47451222"
---
# <a name="get-started-with-relay-hybrid-connections-http-requests-in-net"></a>Ismerkedés a hibrid Relay-kapcsolatok HTTP-kéréseivel a .NET-ben
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

Ez az oktatóanyag bevezetést nyújt az [Azure Relay Hibrid-kapcsolatok](relay-what-is-it.md#hybrid-connections) használatába. Megismerheti egy olyan ügyfélalkalmazás Microsoft .NET-tel való létrehozását, amely kéréseket küld egy kapcsolódó figyelőalkalmazásnak. 

## <a name="what-will-be-accomplished"></a>Az oktatóanyag célja
A hibrid kapcsolatokhoz szükség van egy ügyfél- és egy kiszolgáló-összetevőre is. Ebben az oktatóanyagban a lépéseket követve két konzolalkalmazást hoz létre:

1. Relay-névtér létrehozása az Azure Portal használatával.
2. Hibrid kapcsolat létrehozása ezen a névtéren az Azure Portal használatával.
3. Kiszolgálói (figyelő) konzolalkalmazás írása kérésfogadási céllal.
4. Ügyfél-konzolalkalmazás (küldő) írása kérésküldési céllal.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következő előfeltételekre lesz szüksége:

* [Visual Studio 2015 vagy újabb](http://www.visualstudio.com). A jelen oktatóanyag példái a Visual Studio 2017-et használják.
* Azure-előfizetés.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-by-using-the-azure-portal"></a>1. Névtér létrehozása az Azure Portal használatával
Ha már létrehozta a Relay-névteret, folytassa a [hibrid kapcsolat az Azure Portal használatával történő létrehozását](#2-create-a-hybrid-connection-using-the-azure-portal) ismertető szakasszal.

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="2-create-a-hybrid-connection-by-using-the-azure-portal"></a>2. Hibrid kapcsolat létrehozása az Azure Portal használatával
Ha már létrehozott egy hibrid kapcsolatot, folytassa a [kiszolgálói alkalmazás létrehozását](#3-create-a-server-application-listener) ismertető szakasszal.

[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="3-create-a-server-application-listener"></a>3. Kiszolgálói alkalmazás (figyelő) létrehozása
A Visual Studióban egy C# nyelven íródott konzolalkalmazást hozunk létre az üzenetek figyeléséhez és a Relay-től való fogadásához.

[!INCLUDE [relay-hybrid-connections-http-requests-dotnet-get-started-server](../../includes/relay-hybrid-connections-http-requests-dotnet-get-started-server.md)]

## <a name="4-create-a-client-application-sender"></a>4. Ügyfélalkalmazás létrehozása (küldő)
A Visual Studióban egy C# nyelven íródott konzolalkalmazást hozunk létre az üzenetek Relay-be való küldéséhez.

[!INCLUDE [relay-hybrid-connections-http-requests-dotnet-get-started-client](../../includes/relay-hybrid-connections-http-requests-dotnet-get-started-client.md)]

## <a name="5-run-the-applications"></a>5. Az alkalmazások futtatása
1. Futtassa a kiszolgálóalkalmazást. A konzolon a következő szöveget fogja látni:

    ```
    Online
    Server listening
    ```
1. Futtassa az ügyfélalkalmazást. Megjelenik a `hello!` üzenet az ügyfélablakban. Az ügyfél egy HTTP-kérést küldött a kiszolgálónak, amely `hello!` üzenettel válaszolt. 
3. A konzolablakok bezárásához nyomja le az **ENTER** billentyűt mindkét konzolablakban. 

Gratulálunk, végpontok közötti hibrid kapcsolati alkalmazást hozott létre!

## <a name="next-steps"></a>További lépések

* [Relay – gyakori kérdések](relay-faq.md)
* [Névtér létrehozása](relay-create-namespace-portal.md)
* [Bevezetés a Node használatába](relay-hybrid-connections-node-get-started.md)

