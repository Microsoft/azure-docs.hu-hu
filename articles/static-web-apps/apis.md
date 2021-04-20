---
title: API-támogatás Azure Static Web Apps Azure Functions
description: A támogatott API-Azure Static Web Apps megismeri
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 1fc5e1e6982686e7042e5b8ad55d72a4560b6aee
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107737477"
---
# <a name="api-support-in-azure-static-web-apps-preview-with-azure-functions"></a>API-támogatás Azure Static Web Apps előzetes verzióban Azure Functions

Azure Static Web Apps kiszolgáló nélküli API-végpontokat biztosít a [Azure Functions.](../azure-functions/functions-overview.md) Az API-k Azure Functions igény szerint dinamikusan skáláznak, és a következő funkciókat tartalmazzák:

- **Integrált biztonság** a felhasználói hitelesítéshez és a [szerepköralapú engedélyezési adatokhoz való közvetlen hozzáféréssel.](user-information.md)
- **Zökkenőmentes útválasztás,** amely egyéni CORS-szabályok nélkül teszi biztonságossá az _API-útvonalat_ a webalkalmazás számára.
- **HTTP-eseményindítók** és bemeneti/kimeneti kötések.

## <a name="configuration"></a>Konfiguráció

Az API-végpontok az API-útvonalon keresztül érhetők el a _webalkalmazás_ számára. Bár ez az útvonal ki van javítva, ön szabályozhatja azt a mappát és projektet, ahol megkeresi a Azure Functions alkalmazást. Ezt a helyet az adattár _.github/workflows_ mappájában található [munkafolyamat YAML-fájl](github-actions-workflow.md#build-and-deploy) szerkesztésével módosíthatja.

## <a name="constraints"></a>Korlátozások

Azure Static Web Apps API-t biztosít a Azure Functions. A Azure Functions funkciói olyan funkciókra összpontosítanak, amelyek lehetővé teszik api-k webalkalmazáshoz való létrehozásához, valamint a webalkalmazás biztonságos csatlakozását az API-hoz. Ezek a funkciók bizonyos korlátozásokat tartalmaznak, például:

- Az API útvonalelőtagjának api-nak _kell lennie._
- Az API-nak egy 12Node.js, .NET Core 3.1 vagy Python 3.8 Azure Functions kell lennie.
- Az API-függvények útválasztási szabályai csak az [átirányításokat és](routes.md#redirects) az útvonalak [szerepkörökkel való biztonságossá tételeit támogatják.](routes.md#securing-routes-with-roles)
- Az eseményindítók [](../azure-functions/functions-bindings-http-webhook.md)http-alapúak.
  - A bemeneti és [kimeneti kötések](../azure-functions/functions-triggers-bindings.md#supported-bindings) támogatottak.
- A naplók csak akkor érhetők el, ha Application Insights [hozzáadja](../azure-functions/functions-monitoring.md) a függvényalkalmazáshoz.
- Egyes alkalmazásbeállításokat a szolgáltatás kezeli. Nem konfigurálhat olyan alkalmazásbeállításokat, amelyek a következő előtagokkal kezdődnek: `APPSETTING_` , `AZUREBLOBSTORAGE_` , `AZUREFILESSTORAGE_` `AZURE_FUNCTION_` `CONTAINER_` `DIAGNOSTICS_` `DOCKER_` `FUNCTIONS_` `IDENTITY_` `MACHINEKEY_` `MAINSITE_` `MSDEPLOY_` `SCMSITE_` `SCM_` `WEBSITES_` `WEBSITE_` `WEBSOCKET_` `AzureWeb` .

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [API hozzáadása](add-api.md)
