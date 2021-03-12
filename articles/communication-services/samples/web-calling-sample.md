---
title: Azure kommunikációs szolgáltatások – webes hívás mintája
titleSuffix: An Azure Communication Services sample
description: A kommunikációs szolgáltatások webes hívási mintájának ismertetése
author: chriswhilar
manager: mariusu-msft
services: azure-communication-services
ms.author: mariusu
ms.date: 10/15/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: c3ceb866d9ca817299929b9a811915b82ccfbd48
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102614744"
---
# <a name="get-started-with-the-web-calling-sample"></a>Ismerkedés a webes hívási mintával

A web Calling Sample egy webalkalmazás, amely a kommunikációs szolgáltatások webes hívási ügyféloldali kódtár által nyújtott különböző képességek lépésenkénti bemutatója.

Ez a minta fejlesztők számára készült, és nagyon egyszerűvé teszi a kommunikációs szolgáltatások megkezdését. A felhasználói felülete több szakaszra oszlik, amelyek mindegyike egy "show code" (kód megjelenítése) gombot tartalmaz, amely lehetővé teszi, hogy közvetlenül a böngészőjéből másoljon kódot a saját kommunikációs szolgáltatások alkalmazásba.

## <a name="get-started-with-the-web-calling-sample"></a>Ismerkedés a webes hívási mintával

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]


> [!IMPORTANT]
> [Ez a minta a githubon érhető el.](https://github.com/Azure-Samples/communication-services-web-calling-tutorial/)

Kövesse a/Project/readme.MD a projekt beállításához és a számítógép helyi futtatásához.
Miután a [webes hívás mintája](https://github.com/Azure-Samples/communication-services-web-calling-tutorial) fut a gépen, a következő Kezdőlap jelenik meg:

:::image type="content" source="./media/web-calling-tutorial-page-1.png" alt-text="Webes hívási oktatóanyag 1" lightbox="./media/web-calling-tutorial-page-1.png":::

:::image type="content" source="./media/web-calling-tutorial-page-2.png" alt-text="Webes hívási oktatóanyag 2" lightbox="./media/web-calling-tutorial-page-2.png":::

## <a name="user-provisioning-and-sdk-initialization"></a>A felhasználók üzembe helyezése és az SDK inicializálása

Kattintson a "kiépítési felhasználó és az SDK inicializálása" elemre az SDK inicializálásához a háttér-jogkivonat kiépítési szolgáltatása által kiépített jogkivonat használatával. Ez a háttér-szolgáltatás a-ben érhető el `/project/webpack.config.js` .

Kattintson a "kód megjelenítése" gombra a saját megoldásában használható mintakód megtekintéséhez.

Az SDK inicializálása után a következőket kell látnia:

:::image type="content" source="./media/user-provisioning.png" alt-text="Felhasználók regisztrálása" lightbox="./media/user-provisioning.png":::

Most már készen áll arra, hogy megkezdje a hívások küldését a kommunikációs szolgáltatások erőforrásaival!

## <a name="placing-and-receiving-calls"></a>Hívások elhelyezése és fogadása

A kommunikációs szolgáltatások web Calling SDK lehetővé teszi a **1:1**, **1: N** és a **csoportos** hívás használatát.

1:1 vagy 1: N kimenő hívások esetén több kommunikációs szolgáltatás felhasználói identitást is megadhat a vesszővel tagolt értékek használatával történő híváshoz. Megadhat hagyományos (PSTN-) telefonszámokat is, amelyeket vesszővel tagolt értékek használatával hívhat meg.

A PSTN-telefonszámok hívásakor adja meg az alternatív hívó AZONOSÍTÓját. A kimenő hívások elhelyezéséhez kattintson a hívás elhelyezése gombra:

:::image type="content" source="./media/place-a-call.png" alt-text="Hívás elhelyezése" lightbox="./media/place-a-call.png":::

Egy csoportos híváshoz való csatlakozáshoz adja meg a hívást azonosító GUID azonosítót, és kattintson a "csatlakozás csoport" gombra:

:::image type="content" source="./media/join-a-group-call.png" alt-text="Csatlakozás csoportos híváshoz" lightbox="./media/join-a-group-call.png":::

A "kód megjelenítése" gombra kattintva megtekintheti a hívások elhelyezésére, fogadására és a csoportos hívások csatlakoztatására szolgáló mintakód.

Egy aktív hívás a következőképpen néz ki:

:::image type="content" source="./media/group-call.png" alt-text="Csoportos hívás" lightbox="./media/group-call.png":::

Ez a minta kódrészleteket is biztosít a következő képességekhez:

  - A videó ikonra kattintva be-és kikapcsolhatja a videokamerát
  - A mikrofon ikonra kattintva be-és kikapcsolhatja a mikrofont
  - A lejátszás ikonra kattintva megtarthatja/megtarthatja a hívást
  - Kattintson a képernyő ikonjára a képernyő megosztásának elindításához/leállításához
  - A személy ikonra kattintva vehet fel egy résztvevőt a hívásba
  - Ha egy adott résztvevőt szeretne eltávolítani a hívásból, kattintson a résztvevő eltávolítása elemre a résztvevő névsorában


## <a name="next-steps"></a>Következő lépések

>[!div class="nextstepaction"]
>[A minta letöltése a GitHubról](https://github.com/Azure-Samples/communication-services-web-calling-tutorial/)

További információért tekintse át a következő cikkeket:

- Ismerkedjen meg [a hívó ügyféloldali kódtár használatával](../quickstarts/voice-video-calling/calling-client-samples.md)
- További információ a [hívás működéséről](../concepts/voice-video-calling/about-call-types.md)
- Az [API-referenciák dokumentációjának](/javascript/api/azure-communication-services/@azure/communication-calling/) áttekintése
- A [contoso Med-alkalmazás](https://github.com/Azure-Samples/communication-services-contoso-med-app) mintájának áttekintése

## <a name="additional-reading"></a>További információ

- [Azure Communication GitHub](https://github.com/Azure/communication) – további példákat és információkat talál a hivatalos GitHub-oldalon
- [Redux](https://redux.js.org/) – ügyféloldali állapot kezelése
- [FluentUI](https://aka.ms/fluent-ui) – Microsoft powered UI Library
- [Reagálás](https://reactjs.org/) – függvénytár a felhasználói felületek létrehozásához
- [ASP.net Core](/aspnet/core/introduction-to-aspnet-core?preserve-view=true&view=aspnetcore-3.1) -keretrendszer webalkalmazások létrehozásához
