---
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: erhopf
ms.openlocfilehash: a3345ff9a6cc1d5f8e2fbc78c2a76ba6f183aeb6
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102620085"
---
## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services)
* A Azure Active Directory hitelesítéshez konfigurált, magával ragadó olvasó erőforrás. A beállításhoz kövesse az [alábbi utasításokat](../../how-to-create-immersive-reader.md) .  A környezeti tulajdonságok konfigurálásakor itt létrehozott értékek némelyikére szüksége lesz. Mentse a munkamenet kimenetét szövegfájlba későbbi használatra.
* [MacOS](https://www.apple.com/macos).
* [Git](https://git-scm.com/).
* [Magával ragadó olvasói SDK](https://github.com/microsoft/immersive-reader-sdk).
* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12).

## <a name="configure-authentication-credentials"></a>Hitelesítő adatok konfigurálása

1. Nyissa meg a Xcode, és nyissa meg a **immersive-Reader-SDK/js/Samples/iOS/Quickstart-Swift/Quickstart-Swift. xcodeproj** fájlt.
1. A felső menüben válassza a **termék**  >  **sémájának**  >  **szerkesztési sémája** lehetőséget.
1. A **Futtatás** nézetben válassza az **argumentumok** fület.
1. A **környezeti változók** szakaszban adja hozzá a következő neveket és értékeket. Adja meg azokat az értékeket, amelyeket a magától elolvasó erőforrás létrehozásakor adott meg.

    ```text
    TENANT_ID=<YOUR_TENANT_ID>
    CLIENT_ID=<YOUR_CLIENT_ID>
    CLIENT_SECRET<YOUR_CLIENT_SECRET>
    SUBDOMAIN=<YOUR_SUBDOMAIN>
    ```

Ne véglegesítse ezt a változást a verziókövetés során, mert olyan titkos kulcsokat tartalmaz, amelyeket nem szabad nyilvánosságra hozni.

## <a name="start-the-immersive-reader-with-sample-content"></a>A részletes olvasó a minta tartalmának elindítása

A Xcode válassza a **CTRL + R** billentyűkombinációt a projekt futtatásához.

## <a name="next-steps"></a>Következő lépések

* Fedezze fel az [olvasói SDK](https://github.com/microsoft/immersive-reader-sdk) -t és a [magára ejtő olvasó SDK-referenciát](../../reference.md).
* Kód mintáinak megtekintése a [githubon](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/).
