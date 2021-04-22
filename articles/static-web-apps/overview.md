---
title: Mi az Azure Static Web Apps?
description: A szolgáltatások legfontosabb funkciói Azure Static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 04/01/2021
ms.author: cshoe
ms.openlocfilehash: 945979daa3c766b737e5b312a6c14e60204a6a55
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874062"
---
# <a name="what-is-azure-static-web-apps-preview"></a>Mi az Azure Static Web Apps előzetes verzió?

Azure Static Web Apps egy olyan szolgáltatás, amely automatikusan hoz létre és helyez üzembe teljes körű webalkalmazásokat az Azure-ban egy kódtárból.

:::image type="content" source="media/overview/azure-static-web-apps-overview.png" alt-text="Azure Static Web Apps áttekintő diagram":::

A Azure Static Web Apps munkafolyamata a fejlesztők napi munkafolyamatához van szabva. Az alkalmazások a kódváltozások alapján vannak felépítve és üzembe építve.

Amikor létrehoz egy Azure Static Web Apps erőforrást, az Azure közvetlenül a GitHubbal vagy az Azure DevOps használatával figyeli a választott ágat. Minden alkalommal, amikor leküldi a véglegesítéseket, vagy lekéréses kérelmeket fogad a figyelt ágba, a rendszer automatikusan futtat egy buildet, és üzembe helyez egy alkalmazást és egy API-t az Azure-ban.

A statikus webalkalmazások gyakran olyan kódtárak és keretrendszerek használatával készültek, mint az Angular, a React, a Svelte, a Vue vagy a Blazor, ahol a kiszolgálóoldali megjelenítés nem szükséges. Ezek az alkalmazások HTML-, CSS-, JavaScript- és képobjektumokat tartalmaznak, és ezek alkotják az alkalmazást. A hagyományos webkiszolgálók esetén ezeket az eszközöket egyetlen kiszolgáló szolgálja ki a szükséges API-végpontok mellett.

A Static Web Apps a statikus eszközök elkülönülnek a hagyományos webkiszolgálótól, és ehelyett a világ földrajzilag elosztott pontjairól szolgálják ki őket. Ez az elosztás a fájlok sokkal gyorsabb szolgáltatását teszi lehetővé, mivel a fájlok fizikailag közelebb vannak a végfelhasználókhoz. Emellett az API-végpontok kiszolgáló [](../azure-functions/functions-overview.md)nélküli architektúrával vannak üzemeltetve, így nincs szükség teljes háttérkiszolgálóra.

## <a name="key-features"></a>A legfontosabb jellemzők

- **Webes üzemeltetés,** például HTML-, CSS-, JavaScript- és képtartalmakhoz használható.
- **Integrált API-támogatás** a Azure Functions.
- **Első osztályú GitHub- és Azure DevOps-integráció,** ahol az adattár változásai buildeket és üzembe helyezéseket aktiválnak.
- **Globálisan elosztott statikus** tartalom, a tartalom közelebb kerül a felhasználókhoz.
- **Ingyenes SSL-tanúsítványok,** amelyek automatikusan megújulnak.
- **Egyéni tartományok** az alkalmazás márkajelzéses testreszabásához.
- **Zökkenőmentes biztonsági modell** fordított proxyval AZ API-k hívhatóak, amelyekhez nincs szükség CORS-konfigurációra.
- **A hitelesítésszolgáltató integrációja** a Azure Active Directory, a Facebookkal, a Google-sel, a GitHubbal és a Twittersel.
- **Testre szabható engedélyezési szerepkör-definíciók** és -hozzárendelések.
- **A háttér-útválasztási szabályok** lehetővé teszik a kiszolgált tartalom és útvonalak teljes vezérlését.
- **Lekéréses kérelmeken** keresztül létrehozott átmeneti verziók, amelyek lehetővé teszik a webhely előzetes verzióit a közzététel előtt.

## <a name="what-you-can-do-with-static-web-apps"></a>Mire használhatja a Static Web Apps

- **Modern webalkalmazásokat** hozhat létre JavaScript-keretrendszerekkel és kódtárakkal, például [az Angular,](getting-started.md?tabs=angular) [a React,](getting-started.md?tabs=react)a [](apis.md) [Svelte,](/learn/modules/publish-app-service-static-web-app-api/)a [Vue](getting-started.md?tabs=vue)vagy a [Blazor](https://dotnet.microsoft.com/apps/aspnet/web-apps/blazor) használatával WebAssembly-alkalmazások Azure Functions háttéralkalmazásokkal.
- **Statikus webhelyeket tehet** közzé olyan keretrendszerekkel, mint a [Gatsby,](publish-gatsby.md) [a Mert,](publish-hugo.md)a [VuePress.](publish-vuepress.md)
- **Webalkalmazások üzembe** helyezése olyan keretrendszerekkel, mintNext.js [és](deploy-nextjs.md) [Nuxt.js.](deploy-nuxtjs.md)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az első statikus alkalmazás összeállítása](getting-started.md)
