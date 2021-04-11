---
title: Facebook-hitelesítés konfigurálása
description: Megtudhatja, hogyan konfigurálhatja a Facebook-hitelesítést identitás-szolgáltatóként a App Service vagy Azure Functions alkalmazáshoz.
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.topic: article
ms.date: 03/29/2021
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: bc639eaea76b3309d6ed047e73c726040da19639
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078009"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-facebook-login"></a>A App Service vagy Azure Functions alkalmazás konfigurálása Facebook-Bejelentkezés használatára

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Ez a cikk bemutatja, hogyan konfigurálhatja Azure App Service vagy Azure Functions a Facebook hitelesítési szolgáltatóként való használatára.

A cikkben ismertetett eljárás végrehajtásához olyan Facebook-fiókra van szükség, amely ellenőrzött e-mail-címmel és mobiltelefon-számmal rendelkezik. Új Facebook-fiók létrehozásához nyissa meg a [Facebook.com].

## <a name="register-your-application-with-facebook"></a><a name="register"> </a>Alkalmazás regisztrálása a Facebook-ban

1. Nyissa meg a [Facebook Developers] webhelyet, és jelentkezzen be Facebook-fiókjának hitelesítő adataival.

   Ha nem rendelkezik Facebook for Developers-fiókkal, kattintson az első **lépések** lehetőségre, és kövesse a regisztrációs lépéseket.
1. Válassza **a saját alkalmazások**  >  **új alkalmazás hozzáadása** lehetőséget.
1. A **megjelenítendő név** mezőben:
   1. Adjon egyedi nevet az alkalmazásnak.
   1. Adja meg a **kapcsolattartási E-mail címét**.
   1. Válassza az **alkalmazás-azonosító létrehozása** lehetőséget.
   1. Fejezze be a biztonsági ellenőrzését.

   Megnyílik az új Facebook-alkalmazás fejlesztői irányítópultja.
1. Válassza az **irányítópult**  >  **Facebook-Bejelentkezés**  >    >  **webes** beállítása lehetőséget.
1. A **Facebook-Bejelentkezés** alatt a bal oldali navigációs sávon válassza a **Beállítások** lehetőséget.
1. Az **érvényes OAuth átirányítási URI** -k mezőben adja meg a értéket `https://<app-name>.azurewebsites.net/.auth/login/facebook/callback` . Ne felejtse el helyettesíteni a `<app-name>` Azure app Service alkalmazás nevét.
1. Válassza a **módosítások mentése** lehetőséget.
1. A bal oldali ablaktáblán válassza a **Beállítások**  >  **alapszintű** lehetőséget. 
1. Az **alkalmazás titkos kulcsa** mezőben válassza a **Megjelenítés** lehetőséget. Másolja az alkalmazás- **azonosító** és az **alkalmazás titkos kulcsának** értékeit. Később a App Service alkalmazást az Azure-ban konfigurálhatja.

   > [!IMPORTANT]
   > Az alkalmazás titkos kulcsa egy fontos biztonsági hitelesítő adat. Ezt a titkos kódot Ne ossza meg senkivel, vagy Ossza szét egy ügyfélalkalmazás alkalmazásán belül.
   >

1. Az alkalmazás regisztrálásához használt Facebook-fiók az alkalmazás rendszergazdája. Ezen a ponton csak a rendszergazdák jelentkezhetnek be ebbe az alkalmazásba.

   Más Facebook-fiókok hitelesítéséhez válassza az **alkalmazás felülvizsgálata** lehetőséget, és engedélyezze, hogy a **\<your-app-name> nyilvános** felhasználók a Facebook-hitelesítés használatával hozzáférjenek az alkalmazáshoz.

## <a name="add-facebook-information-to-your-application"></a><a name="secrets"> </a>Facebook-információk hozzáadása az alkalmazáshoz

1. Jelentkezzen be a [Azure Portalba] , és navigáljon az alkalmazáshoz.
1. A bal oldali menüben válassza a **hitelesítés** lehetőséget. Kattintson a **személyazonosság-szolgáltató hozzáadása** elemre.
1. Válassza a **Facebook** lehetőséget az identitás-szolgáltató legördülő menüben. Illessze be a korábban beszerzett alkalmazás-azonosító és az alkalmazás titkos értékeit.

    A titkos kódot a rendszer tárolóhely-Sticky [alkalmazásként](./configure-common.md#configure-app-settings) tárolja `FACEBOOK_PROVIDER_AUTHENTICATION_SECRET` . Ezt a beállítást később is frissítheti [Key Vault hivatkozások](./app-service-key-vault-references.md) használatához, ha a titkos kulcsot szeretné kezelni a Azure Key Vaultban.

1. Ha ez az alkalmazáshoz konfigurált első identitás-szolgáltató, akkor a rendszer a **app Service hitelesítési beállítások** szakaszt is kéri. Ellenkező esetben a következő lépésre léphet.
    
    Ezek a beállítások határozzák meg, hogy az alkalmazás hogyan válaszol a nem hitelesített kérelmekre, és az alapértelmezett beállítások átirányítják az összes kérelmet az új szolgáltatóval való bejelentkezéshez. Módosíthatja a viselkedés testre szabását, vagy később is módosíthatja ezeket a beállításokat a  **hitelesítési beállítások** melletti **Szerkesztés** lehetőség választásával. További információ ezekről a lehetőségekről: [hitelesítési folyamat](overview-authentication-authorization.md#authentication-flow).

1. Választható Kattintson a **Tovább: hatókörök** elemre, és adja hozzá az alkalmazáshoz szükséges hatóköröket. Ezeket a rendszer a böngészőalapú folyamatok bejelentkezési idejére kéri.
1. Kattintson a **Hozzáadás** parancsra.

Most már készen áll a Facebook használatára a hitelesítéshez az alkalmazásban. A szolgáltató a **hitelesítés** képernyőn jelenik meg. Innen módosíthatja vagy törölheti a szolgáltatói konfigurációt.

## <a name="next-steps"></a><a name="related-content"> </a>Következő lépések

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->
[Facebook-fejlesztők]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure Portal]: https://portal.azure.com/
