---
title: Átfedés létrehozása a Media Encoder Standard használatával
description: Megtudhatja, hogyan hozhat létre átfedést Media Encoder Standard használatával.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 08/31/2020
ms.openlocfilehash: 6d68f35cd7eff745a3d17009b65b1ed73a190173
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490250"
---
# <a name="how-to-create-an-overlay-with-media-encoder-standard"></a>Átfedés létrehozása a Media Encoder Standard használatával

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

A Media Encoder Standard a képek, hangfájlok vagy más videók egy másik videóba való átfedését teszi lehetővé. A bemenetnek pontosan egy fájlt kell megadnia. A képfájlt JPG, PNG, GIF vagy BMP formátumban, vagy egy hangfájl (például WAV, MP3, WMA vagy M4A fájl) vagy videofájl formájában is megadhatja.


## <a name="prerequisites"></a>Előfeltételek

* Gyűjtsön olyan fiókadatokat, amelyekre szükség van a *appsettings.js* konfigurálásához a minta fájljában. Ha nem tudja, hogyan teheti meg, tekintse meg a rövid útmutató [: alkalmazás regisztrálása a Microsoft Identity platformon](../../active-directory/develop/quickstart-register-app.md)című témakört. A következő értékeket kell megvárni a fájl *appsettings.js* .

    ```json
    {
    "AadClientId": "",
    "AadEndpoint": "https://login.microsoftonline.com",
    "AadSecret": "",
    "AadTenantId": "",
    "AccountName": "",
    "ArmAadAudience": "https://management.core.windows.net/",
    "ArmEndpoint": "https://management.azure.com/",
    "Location": "",
    "ResourceGroup": "",
    "SubscriptionId": ""
    }
    ```

Ha még nem ismeri az átalakításokat, javasoljuk, hogy hajtsa végre a következő tevékenységeket:

* [Videó és hang kódolásának](encode-concept.md) olvasása Media Services
* Olvassa el, [Hogyan kódolhat egyéni átalakító-.net-](transform-custom-presets-how-to.md)tel. A cikk lépéseit követve állítsa be, hogy az átalakításokkal való együttműködéshez szükség van-e a .NET-re, majd térjen vissza ide az átfedések előre beállított mintájának kipróbálásához.
* Lásd az [átalakítások hivatkozási dokumentumát](/rest/api/media/transforms).

Ha már ismeri az átalakításokat, töltse le az átfedések mintáját.

## <a name="overlays-preset-sample"></a>Átfedések előre definiált mintája

Töltse le a [Media-Services-overlay mintát](https://github.com/Azure-Samples/media-services-overlays) az átfedések megkezdéséhez.

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [transforms next steps](./includes/transforms-next-steps.md)]
