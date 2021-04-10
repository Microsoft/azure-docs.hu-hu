---
title: Mi az Azure Static Web Apps?
description: Az Azure statikus Web Apps legfontosabb funkciói és funkciói.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 04/01/2021
ms.author: cshoe
ms.openlocfilehash: e81f0a9e4fc50cf0d80f2905b9328af3c721865c
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106166401"
---
# <a name="what-is-azure-static-web-apps-preview"></a>Mi az Azure statikus Web Apps előzetes verzió?

Az Azure statikus Web Apps egy olyan szolgáltatás, amely automatikusan létrehozza és üzembe helyezi a teljes értékű webalkalmazásokat az Azure-ban egy adattárból.

:::image type="content" source="media/overview/azure-static-web-apps-overview.png" alt-text="Az Azure statikus Web Apps áttekintése diagramja":::

Az Azure statikus Web Apps munkafolyamata egy fejlesztői napi munkafolyamatra van szabva. Az alkalmazások a kód módosítása alapján vannak felépítve és telepítve.

Amikor létrehoz egy Azure statikus Web Apps-erőforrást, az Azure közvetlenül a GitHub vagy az Azure DevOps használatával felügyeli a választott ágakat. Minden alkalommal, amikor leküldi vagy elfogadja a pull-kérelmeket a figyelt ágra, a Build automatikusan elindul, és az alkalmazás és az API üzembe helyezése az Azure-ban történik.

A statikus webalkalmazások általában olyan könyvtárak és keretrendszerek használatával készültek, mint például a szögletes, a reakciós, a karcsú, a Vue vagy a Blazer, ahol a kiszolgálóoldali megjelenítés nem szükséges. Ezek az alkalmazások HTML-, CSS-, JavaScript- és képobjektumokat tartalmaznak, és ezek alkotják az alkalmazást. A hagyományos webkiszolgálókkal ezeket az eszközöket a szükséges API-végpontok mellett egyetlen kiszolgáló szolgálja ki.

A statikus Web Apps a statikus eszközöket egy hagyományos webkiszolgálótól választja el, és helyette a világ földrajzilag elosztott pontjairól szolgálnak. Ez az elosztás a fájlok sokkal gyorsabb szolgáltatását teszi lehetővé, mivel a fájlok fizikailag közelebb vannak a végfelhasználókhoz. Emellett az API-végpontok [kiszolgáló nélküli architektúrával](../azure-functions/functions-overview.md)vannak tárolva, így elkerülhető, hogy a teljes háttér-kiszolgálót egyszerre kelljen használni.

## <a name="key-features"></a>A legfontosabb jellemzők

- **Webes üzemeltetés** statikus tartalmakhoz, például HTML-, CSS-, JavaScript-és rendszerképekhez.
- A Azure Functions által biztosított **integrált API** -támogatás.
- **Az első osztályú GitHub és az Azure DevOps integrációja** , ahol a tárház megváltoztatja a trigger-és üzembe helyezési folyamatokat.
- **Globálisan elosztott** statikus tartalom, amely közelebb helyezi a tartalmat a felhasználókhoz.
- **Ingyenes SSL-tanúsítványok**, amelyek automatikusan megújulnak.
- **Egyéni tartományok** , amelyek márkás testreszabásokat biztosítanak az alkalmazás számára.
- **Zökkenőmentes biztonsági modell** fordított proxyval, ha olyan API-kat hív meg, amelyek nem igényelnek CORS-konfigurációt.
- A Azure Active Directory, a Facebook, a Google, a GitHub és a Twitter **szolgáltatást használó hitelesítésszolgáltató integrációja** .
- **Testreszabható engedélyezési szerepkör-definíció** és hozzárendelések.
- A **háttér-útválasztási szabályok** lehetővé teszik a teljes körű irányítást a kiszolgált tartalom és útvonalak felett.
- A lekéréses kérelmek által **generált átmeneti verziók** a közzététel előtt lehetővé teszik a hely előzetes verziójának kiadását.

## <a name="what-you-can-do-with-static-web-apps"></a>Mit tehet a statikus Web Apps

- Hozzon létre **modern webalkalmazásokat** JavaScript-keretrendszerekkel és-tárakkal, például a [szögletes](getting-started.md?tabs=angular), a [reagálási](getting-started.md?tabs=react), a [karcsú](/learn/modules/publish-app-service-static-web-app-api/), a [Vue](getting-started.md?tabs=react)vagy a [Blazer](https://dotnet.microsoft.com/apps/aspnet/web-apps/blazor) használatával webszerelvény-alkalmazások létrehozásához [Azure functions](apis.md) háttérrel.
- **Statikus helyeket tehet közzé** olyan keretrendszerekkel, mint például a [Gatsby](publish-gatsby.md), a [Hugo](publish-hugo.md), a [VuePress](publish-vuepress.md).
- **Webalkalmazások üzembe helyezése** olyan keretrendszerekkel, mint a [Next.js](deploy-nextjs.md) és a [Nuxt.js](deploy-nuxtjs.md).

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az első statikus alkalmazás létrehozása](getting-started.md)