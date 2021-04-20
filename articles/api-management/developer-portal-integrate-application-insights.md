---
title: Integráció Application Insights fejlesztői portállal
titleSuffix: Azure API Management
description: Megtudhatja, hogyan integrálhatja Application Insights a felügyelt vagy saját fejlesztésű fejlesztői portálba.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: 5e1c9caa55d0b3b7820f766a30c878fdc01f5137
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741720"
---
# <a name="integrate-application-insights-to-developer-portal"></a>Integráció Application Insights fejlesztői portállal

A szolgáltatás egyik Azure Monitor a Application Insights. Ez egy fejlesztők és DevOps-szakemberek számára készült, extensible Application Performance Management (APM) szolgáltatás. A használatával monitorozható a fejlesztői portál, és észlelhetők a teljesítménybeli anomáliák. Application Insights hatékony elemzőeszközöket tartalmaz, amelyek segítségével megtudhatja, mit is csinálnak a felhasználók a fejlesztői portálon.

## <a name="add-application-insights-to-your-portal"></a>Új Application Insights hozzáadása a portálhoz

Kövesse az alábbi lépéseket a Application Insights vagy saját fejlesztésű fejlesztői portálhoz való csatlakoztatáshoz.

> [!IMPORTANT]
> A felügyelt portálok esetén nem szükséges az 1. és a 2. lépés. Ha már van felügyelt portálja, ugorjon a 4. lépésre.

1. Állítson be [egy helyi környezetet](developer-portal-self-host.md#step-1-set-up-local-environment) a fejlesztői portál legújabb kiadásának.

1. Telepítse az **npm-csomagot** a [Paperbitek az Azure-hoz való hozzáadásához:](https://github.com/paperbits/paperbits-azure)

    ```console
    npm install @paperbits/azure --save
    ```

1. A `startup.publish.ts` mappában található `src` fájlban importálja és regisztrálja a Application Insights modult:

    ```typescript
    import { AppInsightsPublishModule } from "@paperbits/azure";
    ...
    injector.bindModule(new AppInsightsPublishModule());
    ```

1. A portál konfigurációjának lekérése:

    ```http
    GET /contentTypes/document/contentItems/configuration
    ```

    ```json
    {
        "nodes": [
            {
                "site": {
                    "title": "Microsoft Azure API Management - developer portal",
                    "description": "Discover APIs, learn how to use them, try them out interactively, and sign up to acquire keys.",
                    "keywords": "Azure, API Management, API, developer",
                    "faviconSourceId": null,
                    "author": "Microsoft Azure API Management"
                }
            }
        ]
    }
    ```

1. Terjessze ki az előző lépésben lekért helykonfigurációt Application Insights konfigurációval:

    ```http
    PUT /contentTypes/document/contentItems/configuration
    ```

    ```json
    {
        "nodes": [
            {
                "site": { ... },
                "integration": {
                    "appInsights": {
                        "instrumentationKey": "xxxxxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxxx"
                    }
                }
            }
        ]
    }
    ```

## <a name="next-steps"></a>Következő lépések

További információ a fejlesztői portálról:

- [Az Azure API Management fejlesztői portál áttekintése](api-management-howto-developer-portal.md)
- [Portál üzembe helyezésének automatizálása](automate-portal-deployments.md)
- [A fejlesztői portál önálló gazdagépe](developer-portal-self-host.md)
