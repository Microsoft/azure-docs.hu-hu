---
title: A Google-hitelesítés konfigurálása
description: Megtudhatja, hogyan konfigurálhatja a Google-hitelesítést identitás-szolgáltatóként a App Service vagy Azure Functions alkalmazáshoz.
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.topic: article
ms.date: 03/29/2021
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: f6bec32fa928e840569ed95c35a056db91ea9737
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077992"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-google-login"></a>A App Service vagy Azure Functions alkalmazás konfigurálása a Google bejelentkezés használatára

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Ebből a témakörből megtudhatja, hogyan konfigurálhatja Azure App Service vagy Azure Functions a Google hitelesítési szolgáltatóként való használatára.

A témakörben ismertetett eljárás végrehajtásához rendelkeznie kell egy ellenőrzött e-mail-címmel rendelkező Google-fiókkal. Új Google-fiók létrehozásához látogassa meg az [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302) webhelyet.

## <a name="register-your-application-with-google"></a><a name="register"> </a>Alkalmazás regisztrálása a Google-ban

1. Az ügyfél-azonosító és az ügyfél titkos kódjának létrehozásához kövesse a Google Sign-Inban található Google-dokumentációt a [kiszolgálóoldali alkalmazások számára](https://developers.google.com/identity/sign-in/web/server-side-flow) . A kód módosítása nem szükséges. Csak használja a következő információkat:
    - Az Ön által használt **JavaScript**-forrásokhoz használja az `https://<app-name>.azurewebsites.net` alkalmazás nevét a ben *\<app-name>* .
    - A **jóváhagyott átirányítási URI**-hoz használja a következőt: `https://<app-name>.azurewebsites.net/.auth/login/google/callback` .
1. Másolja az alkalmazás AZONOSÍTÓját és az alkalmazás titkos értékeit.

    > [!IMPORTANT]
    > Az alkalmazás titkos kulcsa egy fontos biztonsági hitelesítő adat. Ezt a titkos kódot Ne ossza meg senkivel, vagy Ossza szét egy ügyfélalkalmazás alkalmazásán belül.

## <a name="add-google-information-to-your-application"></a><a name="secrets"> </a>Google-információk hozzáadása az alkalmazáshoz

1. Jelentkezzen be a [Azure Portalba] , és navigáljon az alkalmazáshoz.
1. A bal oldali menüben válassza a **hitelesítés** lehetőséget. Kattintson a **személyazonosság-szolgáltató hozzáadása** elemre.
1. Válassza a **Google** elemet az identitás-szolgáltató legördülő menüben. Illessze be a korábban beszerzett alkalmazás-azonosító és az alkalmazás titkos értékeit.

    A titkos kódot a rendszer tárolóhely-Sticky [alkalmazásként](./configure-common.md#configure-app-settings) tárolja `GOOGLE_PROVIDER_AUTHENTICATION_SECRET` . Ezt a beállítást később is frissítheti [Key Vault hivatkozások](./app-service-key-vault-references.md) használatához, ha a titkos kulcsot szeretné kezelni a Azure Key Vaultban.

1. Ha ez az alkalmazáshoz konfigurált első identitás-szolgáltató, akkor a rendszer a **app Service hitelesítési beállítások** szakaszt is kéri. Ellenkező esetben a következő lépésre léphet.
    
    Ezek a beállítások határozzák meg, hogy az alkalmazás hogyan válaszol a nem hitelesített kérelmekre, és az alapértelmezett beállítások átirányítják az összes kérelmet az új szolgáltatóval való bejelentkezéshez. Módosíthatja a viselkedés testre szabását, vagy később is módosíthatja ezeket a beállításokat a  **hitelesítési beállítások** melletti **Szerkesztés** lehetőség választásával. További információ ezekről a lehetőségekről: [hitelesítési folyamat](overview-authentication-authorization.md#authentication-flow).

1. Választható Kattintson a **Tovább: hatókörök** elemre, és adja hozzá az alkalmazáshoz szükséges hatóköröket. Ezeket a rendszer a böngészőalapú folyamatok bejelentkezési idejére kéri.
1. Kattintson a **Hozzáadás** parancsra.

Most már készen áll a Google használatára a hitelesítéshez az alkalmazásban. A szolgáltató a **hitelesítés** képernyőn jelenik meg. Innen módosíthatja vagy törölheti a szolgáltatói konfigurációt.

## <a name="next-steps"></a><a name="related-content"> </a>Következő lépések

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure Portal]: https://portal.azure.com/

