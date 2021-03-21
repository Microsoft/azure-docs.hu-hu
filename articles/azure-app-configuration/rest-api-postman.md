---
title: Azure Active Directory REST API – tesztelés a Poster használatával
description: Az Azure-alkalmazás konfigurációjának tesztelése a Poster használatával REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 0b4444b049d181c2f66f8b02a5202dd17a3f4b6b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96932489"
---
# <a name="test-the-azure-app-configuration-rest-api-using-postman"></a>Az Azure-alkalmazás konfigurációjának tesztelése a Poster használatával REST API

A REST API a [Poster](https://www.getpostman.com/)használatával történő teszteléséhez meg kell adnia a kérések [hitelesítéséhez](./rest-api-authentication-hmac.md) szükséges HTTP-fejléceket. A következő lépésekkel konfigurálhatja a Poster-t a REST API teszteléséhez, a hitelesítési fejlécek automatikus létrehozásával:

1. Új [kérelem](https://learning.getpostman.com/docs/postman/sending_api_requests/requests/) létrehozása
1. Adja hozzá a `signRequest` függvényt a [JavaScript hitelesítési mintából](./rest-api-authentication-hmac.md#javascript) a kérelem [előkérési parancsfájljában](https://learning.getpostman.com/docs/postman/scripts/pre_request_scripts/)
1. Adja hozzá a következő kódot az előkérési parancsfájl végéhez. A hozzáférési kulcs frissítése a TODO-Megjegyzés által jelzett módon

    ```js
    // TODO: Replace the following placeholders with your access key
    var credential = "<Credential>"; // Id
    var secret = "<Secret>"; // Value

    var isBodyEmpty = pm.request.body === null || pm.request.body === undefined || pm.request.body.isEmpty();

    var headers = signRequest(
        pm.request.url.getHost(),
        pm.request.method,
        pm.request.url.getPathWithQuery(),
        isBodyEmpty ? undefined : pm.request.body.toString(),
        credential,
        secret);

    // Add headers to the request
    headers.forEach(header => {
        pm.request.headers.upsert({key: header.name, value: header.value});
    })
    ```

1. A kérelem elküldése
