---
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: erhopf
ms.openlocfilehash: d5a483cb239893d04d2c2581fb378f411878f4ba
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102620073"
---
## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services)
* A Azure Active Directory hitelesítéshez konfigurált, magával ragadó olvasó erőforrás. A beállításhoz kövesse az [alábbi utasításokat](../../how-to-create-immersive-reader.md) .  A környezeti tulajdonságok konfigurálásakor itt létrehozott értékek némelyikére szüksége lesz. Mentse a munkamenet kimenetét szövegfájlba későbbi használatra.
* [Git](https://git-scm.com/).
* [Magával ragadó olvasói SDK](https://github.com/microsoft/immersive-reader-sdk).
* [Android Studio](https://developer.android.com/studio).

## <a name="configure-authentication-credentials"></a>Hitelesítő adatok konfigurálása

1. Indítsa el a Android Studiot, és nyissa meg a projektet a- **olvasó-SDK/js/Samples/Gyorsindítás-Java-Android** címtárból (Java) vagy a- **olvasó-SDK/js/Samples/Gyorsindítás-Kotlin** könyvtárából (Kotlin).

1. Hozzon létre egy **env** nevű fájlt a **/assets** mappában. Adja hozzá a következő neveket és értékeket, és adja meg a megfelelő értékeket. Ne véglegesítse ezt a fájlt a verziókövetés során, mert olyan titkos kulcsokat tartalmaz, amelyeket nem szabad nyilvánosságra hozni.
    
    ```text
    TENANT_ID=<YOUR_TENANT_ID>
    CLIENT_ID=<YOUR_CLIENT_ID>
    CLIENT_SECRET=<YOUR_CLIENT_SECRET>
    SUBDOMAIN=<YOUR_SUBDOMAIN>
    ```

## <a name="start-the-immersive-reader-with-sample-content"></a>A részletes olvasó a minta tartalmának elindítása

Válasszon egy eszköz-emulátort a AVD-kezelőből, és futtassa a projektet.

## <a name="next-steps"></a>Következő lépések

* Fedezze fel az [olvasói SDK](https://github.com/microsoft/immersive-reader-sdk) -t és a [magára ejtő olvasó SDK-referenciát](../../reference.md).
* Kód mintáinak megtekintése a [githubon](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/).