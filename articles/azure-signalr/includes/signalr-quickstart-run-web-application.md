---
title: fájl belefoglalása
description: fájl belefoglalása
author: anthonychu
ms.service: signalr
ms.topic: include
ms.date: 03/04/2019
ms.author: antchu
ms.custom: include file
ms.openlocfilehash: 2af9c1dfd13d3aeafa7cf1ac76537117ecc15aff
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "84317781"
---
## <a name="run-the-web-application"></a>A webalkalmazás futtatása

1. Az ügyfelek tesztelésének egyszerűbbé tételéhez nyissa meg a böngészőt a mintául szolgáló egyoldalas webalkalmazásban [https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat-v2/](https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat-v2/) . 

    > [!NOTE]
    > A HTML-fájl forrása a következő helyen található: [/docs/demo/chat-v2/index.html](https://github.com/Azure-Samples/signalr-service-quickstart-serverless-chat/blob/master/docs/demo/chat-v2/index.html). Ha pedig saját maga is szeretné üzemeltetni a HTML-t, indítson el egy helyi HTTP-kiszolgálót, például a [http-Servert](https://www.npmjs.com/package/http-server) a */docs/demo/chat-v2* könyvtárban. Győződjön meg arról, hogy a forrás hozzá van adva a `CORS` *local.settings.js* a mintához hasonló beállításhoz.
    > 
    > ```javascript
    > "Host": {
    >  "LocalHttpPort": 7071,
    >  "CORS": "http://localhost:8080,https://azure-samples.github.io",
    >  "CORSCredentials": true
    > }
    >
    > ```

1. Ha a rendszer felkéri a függvényalkalmazás kiindulási URL-címének megadására, írja be a következőt: `http://localhost:7071`.

1. Amikor a rendszer erre kéri, adja meg a felhasználónevet.

1. A webalkalmazás meghívja a *GetSignalRInfo* függvényt a függvényalkalmazásban az Azure SignalR Service-hez való kapcsolódáshoz szükséges kapcsolatadatok lekéréséhez. Ha a kapcsolat létrejött, megjelenik a csevegőüzenet beviteli mezője.

1. Írja be az üzenetet, és nyomja le az Enter billentyűt. Az alkalmazás elküldi az üzenetet a *SendMessage* függvénynek az Azure Functions-alkalmazásban, amely ezt követően SignalR kimeneti kötés használatával továbbítja az üzenetet az összes csatlakoztatott ügyfélnek. Ha minden megfelelően működik, akkor az üzenetnek meg kell jelennie az alkalmazásban.

    ![Az alkalmazás futtatása](../media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-run-application.png)

1. Nyissa meg a webalkalmazás egy másik példányát egy másik böngészőablakban. Látni fogja, hogy az elküldött üzenet az alkalmazás összes példányában megjelenik.
