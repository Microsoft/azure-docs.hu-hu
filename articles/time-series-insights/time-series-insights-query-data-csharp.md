---
title: Adatok lekérdezése Gen1-környezetből C# Code-Azure Time Series Insights Gen1 használatával | Microsoft Docs
description: Megtudhatja, hogyan kérdezheti le az adatok egy Azure Time Series Insights Gen1-környezetből egy C# nyelven írt egyéni alkalmazás használatával.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: 724bc80f8887ff2c47db93ecfc4cd2e20be7e7f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95020044"
---
# <a name="query-data-from-the-azure-time-series-insights-gen1-environment-using-c-sharp"></a>Adatok lekérdezése a Azure Time Series Insights Gen1-környezetből C Sharp használatával

> [!CAUTION]
> Ez egy Gen1-cikk.

Ez a C# példa azt szemlélteti, hogyan használható a [Gen1 lekérdezési API](/rest/api/time-series-insights/gen1-query) -k Azure Time Series Insights Gen1-környezetből származó adatok lekérdezéséhez.

> [!TIP]
> A C#-Gen1 megtekintése a következő helyen: [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/gen1-sample/csharp-tsi-gen1-sample) .

## <a name="summary"></a>Összefoglalás

Az alábbi mintakód a következő funkciókat mutatja be:

* Hozzáférési token beszerzése Azure Active Directory a [Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)használatával.

* A beszerzett hozzáférési jogkivonat átadása a `Authorization` következő lekérdezési API-kérelmek fejlécében.

* A minta meghívja az egyes Gen1-lekérdezési API-kat, amelyek azt mutatják be, hogyan történik a HTTP-kérelmek elvégzése:
  * A [környezetek API beszerzése](/rest/api/time-series-insights/gen1-query-api#get-environments-api) azon környezetek visszaküldéséhez, amelyekhez a felhasználónak hozzáférése van
  * [Környezet rendelkezésre állási API-k beolvasása](/rest/api/time-series-insights/gen1-query-api#get-environment-availability-api)
  * [Környezeti metaadatok](/rest/api/time-series-insights/gen1-query-api#get-environment-metadata-api) beolvasása a környezeti metaadatok lekéréséhez
  * [Környezetek eseményeinek beolvasása API](/rest/api/time-series-insights/gen1-query-api#get-environment-events-api)
  * [Környezeti összesítések beolvasása API](/rest/api/time-series-insights/gen1-query-api#get-environment-aggregates-api)

* A Gen1 lekérdezési API-k használata a WSS használatával a következő üzenettel:

  * [Környezeti események beolvasása – adatfolyamként továbbított API](/rest/api/time-series-insights/gen1-query-api#get-environment-events-streamed-api)
  * [Környezet összesített adatfolyamának beolvasása](/rest/api/time-series-insights/gen1-query-api#get-environment-aggregates-streamed-api)

## <a name="prerequisites-and-setup"></a>Előfeltételek és beállítás

A mintakód fordítása és futtatása előtt végezze el a következő lépéseket:

1. [Gen1 Azure Time Series Insights-környezet kiépítése](./time-series-insights-get-started.md) .
1. Konfigurálja Azure Time Series Insights-környezetét Azure Active Directory a [hitelesítés és engedélyezés](time-series-insights-authentication-and-authorization.md)című témakörben leírtak szerint.
1. Telepítse a szükséges projekt-függőségeket.
1. Szerkessze az alábbi mintakód összes **#DUMMY #** helyére a megfelelő környezeti azonosítóval.
1. Futtassa a kódot a Visual Studióban.

## <a name="project-dependencies"></a>Projekt függőségei

Javasoljuk, hogy a Visual Studio legújabb verzióját használja:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) – 16.4.2 + verzió

A mintakód két kötelező függőséggel rendelkezik:

* [Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) -3.13.9 csomag.
* [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json) -9.0.1 csomag.

Töltse le a csomagokat a Visual Studio 2019-ban a **Build**  >  **Build megoldás** kiválasztásával.

Azt is megteheti, hogy hozzáadja a csomagokat a [NuGet 2.12 +](https://www.nuget.org/)használatával:

* `dotnet add package Newtonsoft.Json --version 9.0.1`
* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 3.13.9`

## <a name="c-sample-code"></a>C# mintakód

A C# mintakód eléréséhez tekintse meg a [Azure Time Series Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen1-sample/csharp-tsi-gen1-sample/Program.cs)] tárházat.

## <a name="next-steps"></a>Következő lépések

* A lekérdezéssel kapcsolatos további információkért olvassa el a [lekérdezési API-referenciát](/rest/api/time-series-insights/gen1-query-api).

* Olvassa el, hogyan [csatlakozhat egy JavaScript-alkalmazást az ügyfél-SDK](https://github.com/microsoft/tsiclient) -val Time Series Insights.
Azure-Samples/Azure-Time-Series-bepillantást/gen1-Sample/csharp-ÁME-gen1-Sample/program. cs