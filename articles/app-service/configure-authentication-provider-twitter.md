---
title: Twitter-hitelesítés konfigurálása
description: Megtudhatja, hogyan konfigurálhatja a Twitter-hitelesítést identitás-szolgáltatóként a App Service vagy Azure Functions alkalmazáshoz.
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.topic: article
ms.date: 03/29/2021
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: 6ecce954991d9f3901c54a6f87fc803b32469862
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077975"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-twitter-login"></a>A App Service vagy Azure Functions alkalmazás konfigurálása a Twitter-bejelentkezés használatára

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Ez a cikk bemutatja, hogyan konfigurálható Azure App Service vagy Azure Functions a Twitter hitelesítési szolgáltatóként való használatához.

A cikkben ismertetett eljárás végrehajtásához olyan Twitter-fiókra van szükség, amely ellenőrzött e-mail-címmel és telefonszámmal rendelkezik. Új Twitter-fiók létrehozásához nyissa meg a [Twitter.com].

## <a name="register-your-application-with-twitter"></a><a name="register"> </a>Alkalmazás regisztrálása a Twitteren

1. Jelentkezzen be a [Azure Portalba] , és lépjen az alkalmazáshoz. Másolja az **URL-címet**. Ezt fogja használni a Twitter-alkalmazás konfigurálásához.
1. Lépjen a [Twitter-fejlesztők] webhelyére, jelentkezzen be a Twitter-fiókja hitelesítő adataival, és válassza az **alkalmazás létrehozása** lehetőséget.
1. Adja meg az alkalmazás **nevét** és **leírását** az új alkalmazáshoz. Illessze be az alkalmazás **URL-címét** a **webhely URL-** címe mezőbe. A **visszahívási URL-címek** szakaszban adja meg app Service alkalmazásának HTTPS URL-címét, és fűzze hozzá az elérési utat `/.auth/login/twitter/callback` . Például: `https://contoso.azurewebsites.net/.auth/login/twitter/callback`.
1. A lap alján írja be a legalább 100 karaktert a **mondja el, hogyan használja ezt az alkalmazást**, majd válassza a **Létrehozás** lehetőséget. Az előugró ablakban kattintson az újbóli **Létrehozás** gombra. Megjelenik az alkalmazás részletei.
1. Válassza a **kulcsok és hozzáférési tokenek** fület.

   Jegyezze fel ezeket az értékeket:
   - API-kulcs
   - API titkos kulcsa

   > [!IMPORTANT]
   > Az API titkos kulcsa fontos biztonsági hitelesítő adat. Ezt a titkos kódot Ne ossza meg senkivel, vagy ossza meg az alkalmazásával.

## <a name="add-twitter-information-to-your-application"></a><a name="secrets"> </a>Twitter-információk hozzáadása az alkalmazáshoz

1. Jelentkezzen be a [Azure Portalba] , és navigáljon az alkalmazáshoz.
1. A bal oldali menüben válassza a **hitelesítés** lehetőséget. Kattintson a **személyazonosság-szolgáltató hozzáadása** elemre.
1. Válassza a **Twitter** lehetőséget az identitás-szolgáltató legördülő menüben. Illessze be a `API key` `API secret key` korábban beszerzett értékeket.

    A titkos kódot a rendszer tárolóhely-Sticky [alkalmazásként](./configure-common.md#configure-app-settings) tárolja `TWITTER_PROVIDER_AUTHENTICATION_SECRET` . Ezt a beállítást később is frissítheti [Key Vault hivatkozások](./app-service-key-vault-references.md) használatához, ha a titkos kulcsot szeretné kezelni a Azure Key Vaultban.

1. Ha ez az alkalmazáshoz konfigurált első identitás-szolgáltató, akkor a rendszer a **app Service hitelesítési beállítások** szakaszt is kéri. Ellenkező esetben a következő lépésre léphet.
    
    Ezek a beállítások határozzák meg, hogy az alkalmazás hogyan válaszol a nem hitelesített kérelmekre, és az alapértelmezett beállítások átirányítják az összes kérelmet az új szolgáltatóval való bejelentkezéshez. Módosíthatja a viselkedés testre szabását, vagy később is módosíthatja ezeket a beállításokat a  **hitelesítési beállítások** melletti **Szerkesztés** lehetőség választásával. További információ ezekről a lehetőségekről: [hitelesítési folyamat](overview-authentication-authorization.md#authentication-flow).

1. Kattintson a **Hozzáadás** parancsra.

Most már készen áll a Twitter használatára a hitelesítéshez az alkalmazásban. A szolgáltató a **hitelesítés** képernyőn jelenik meg. Innen módosíthatja vagy törölheti a szolgáltatói konfigurációt.

## <a name="next-steps"></a><a name="related-content"> </a>Következő lépések

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[Twitter-fejlesztők]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[twitter.com]: https://go.microsoft.com/fwlink/p/?LinkID=268287
[Azure Portal]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
