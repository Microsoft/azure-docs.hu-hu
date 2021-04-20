---
title: Integráció Google Címkekezelő fejlesztői portállal
titleSuffix: Azure API Management
description: Megtudhatja, hogyan csatlakoztatható Google Címkekezelő a felügyelt vagy saját fejlesztésű fejlesztői portálhoz az Azure API Management.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: c209eb782787146d947b4684d41c5d1e9bb6364e
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741725"
---
# <a name="integrate-google-tag-manager-to-api-management-developer-portal"></a>Integráció Google Címkekezelő fejlesztői API Management portállal

[Google Címkekezelő](https://developers.google.com/tag-manager) a Google által létrehozott címkekezelő rendszer. Segítségével kezelheti a webhelyek nyomon követéséhez és elemzéséhez használt JavaScript- és HTML-címkéket. Használhatja például a Google Címkekezelő Google Analytics, hőtérképek vagy csevegőrobotok, például a LiveChat integrálására.

Az ebben a cikkben található lépéseket követve csatlakoztassa Google Címkekezelő azure-beli felügyelt vagy saját fejlesztésű fejlesztői portálhoz API Management.

## <a name="add-google-tag-manager-to-your-portal"></a>Új Google Címkekezelő hozzáadása a portálhoz

Az alábbi lépéseket követve csatlakoztassa Google Címkekezelő a felügyelt vagy saját fejlesztésű fejlesztői portálhoz.

> [!IMPORTANT]
> A felügyelt portálok esetén nem szükséges az 1. és a 2. lépés. Ha már van felügyelt portálja, ugorjon a 4. lépésre.

1. Állítson be [egy helyi környezetet](developer-portal-self-host.md#step-1-set-up-local-environment) a fejlesztői portál legújabb kiadásában.

1. Telepítse az **npm-csomagot** [paperbitek hozzáadásához a Google Címkekezelő:](https://github.com/paperbits/paperbits-gtm)

    ```sh
    npm install @paperbits/gtm --save
    ```

1. A `startup.publish.ts` mappában található `src` fájlban importálja és regisztrálja a GTM-modult:

    ```typescript
    import { GoogleTagManagerPublishModule } from "@paperbits/gtm/gtm.publish.module";
    ...
    injector.bindModule(new GoogleTagManagerPublishModule());
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

1. Terjessze ki az előző lépés helykonfigurációját az Google Címkekezelő beállítással:

    ```http
    PUT /contentTypes/document/contentItems/configuration
    ```

    ```json
    {
        "nodes": [
            {
                "site": { ... },
                "integration": {
                    "googleTagManager": {
                        "containerId": "GTM-..."
                    }
                }
            }
        ]
    }
    ```

## <a name="next-steps"></a>Következő lépések

- [Portál üzembe helyezésének automatizálása](automate-portal-deployments.md)
- [A fejlesztői portál saját gazdagépe](developer-portal-self-host.md)