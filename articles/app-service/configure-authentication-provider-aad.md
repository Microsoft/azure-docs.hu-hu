---
title: Azure AD-hitelesítés konfigurálása
description: Megtudhatja, hogyan konfigurálhat Azure Active Directory hitelesítést identitás-szolgáltatóként a App Service vagy Azure Functions alkalmazáshoz.
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.topic: article
ms.date: 04/14/2020
ms.custom: seodec18, fasttrack-edit, has-adal-ref
ms.openlocfilehash: b1254e7db0e62d08ea2a3d6d30f2abd379675c55
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078315"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-azure-ad-login"></a>App Service vagy Azure Functions alkalmazás konfigurálása az Azure AD-bejelentkezés használatára

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Ebből a cikkből megtudhatja, hogyan konfigurálhatja Azure App Service vagy Azure Functions hitelesítését, hogy az alkalmazás a [Microsoft Identity platformmal](../active-directory/develop/v2-overview.md) (Azure ad) legyen aláírva a hitelesítési szolgáltatóként.

A App Service Authentication szolgáltatás automatikusan létrehozhatja az alkalmazások regisztrációját a Microsoft Identity platform használatával. Használhat olyan regisztrációt is, amelyet Ön vagy egy címtár-rendszergazda külön is létrehoz.

- [Új alkalmazás regisztrációjának automatikus létrehozása](#express)
- [Külön létrehozott meglévő regisztráció használata](#advanced)

> [!NOTE]
> Az új regisztráció létrehozásának lehetősége kormányzati felhők esetén nem érhető el. Ehelyett [külön kell megadnia a regisztrációt](#advanced).

## <a name="create-a-new-app-registration-automatically"></a><a name="express"> </a>Új alkalmazás regisztrációjának automatikus létrehozása

Ez a beállítás úgy van kialakítva, hogy a hitelesítés egyszerű legyen, és csak néhány kattintást igényel.

1. Jelentkezzen be a [Azure Portalba] , és navigáljon az alkalmazáshoz.
1. A bal oldali menüben válassza a **hitelesítés** lehetőséget. Kattintson a **személyazonosság-szolgáltató hozzáadása** elemre.
1. Válassza a **Microsoft** lehetőséget az identitás-szolgáltató legördülő menüben. Alapértelmezés szerint az új regisztráció létrehozása lehetőség van kiválasztva. Módosíthatja a regisztráció nevét vagy a támogatott fióktípus típusát.

    A rendszer létrehozza és tárolja az ügyfél titkos kulcsát, amely tárolóhely-Sticky- [alkalmazási beállításként](./configure-common.md#configure-app-settings) lesz elnevezve `MICROSOFT_PROVIDER_AUTHENTICATION_SECRET` . Ezt a beállítást később is frissítheti [Key Vault hivatkozások](./app-service-key-vault-references.md) használatához, ha a titkos kulcsot szeretné kezelni a Azure Key Vaultban.

1. Ha ez az alkalmazáshoz konfigurált első identitás-szolgáltató, akkor a rendszer a **app Service hitelesítési beállítások** szakaszt is kéri. Ellenkező esetben a következő lépésre léphet.
    
    Ezek a beállítások határozzák meg, hogy az alkalmazás hogyan válaszol a nem hitelesített kérelmekre, és az alapértelmezett beállítások átirányítják az összes kérelmet az új szolgáltatóval való bejelentkezéshez. Módosíthatja a viselkedés testre szabását, vagy később is módosíthatja ezeket a beállításokat a  **hitelesítési beállítások** melletti **Szerkesztés** lehetőség választásával. További információ ezekről a lehetőségekről: [hitelesítési folyamat](overview-authentication-authorization.md#authentication-flow).

1. Választható Kattintson a **Tovább gombra: engedélyek** és az alkalmazáshoz szükséges hatókörök hozzáadása. Ezeket a rendszer hozzáadja az alkalmazás regisztrálásához, de később is módosíthatja őket.
1. Kattintson a **Hozzáadás** parancsra.

Most már készen áll a Microsoft Identity platform használatára a hitelesítéshez az alkalmazásban. A szolgáltató a **hitelesítés** képernyőn jelenik meg. Innen módosíthatja vagy törölheti a szolgáltatói konfigurációt.

Az Azure AD-bejelentkezés az Azure Storage-hoz és Microsoft Graphhoz hozzáférő webalkalmazáshoz való konfigurálását [bemutató példát ebben az oktatóanyagban](scenario-secure-app-authentication-app-service.md)talál.

## <a name="use-an-existing-registration-created-separately"></a><a name="advanced"> </a>Külön létrehozott meglévő regisztráció használata

Az alkalmazást manuálisan is regisztrálhatja a Microsoft Identity platformon, testreszabhatja a regisztrációt, és konfigurálhatja App Service hitelesítését a regisztrációs adatokkal. Ez akkor hasznos, ha például egy másik Azure AD-bérlőből származó alkalmazás-regisztrációt szeretne használni, mint amely az alkalmazásában van.

### <a name="create-an-app-registration-in-azure-ad-for-your-app-service-app"></a><a name="register"> </a>Alkalmazás-regisztráció létrehozása az Azure ad-ben a app Service-alkalmazáshoz

Először létre kell hoznia az alkalmazás regisztrációját. Ennek megfelelően Gyűjtse össze a következő információkat, amelyekre később szüksége lesz, amikor konfigurálja a hitelesítést a App Service alkalmazásban:

- Ügyfél-azonosító
- Bérlőazonosító
- Ügyfél titka (nem kötelező)
- Alkalmazás-azonosító URI-ja

Az alkalmazás regisztrálásához hajtsa végre a következő lépéseket:

1. Jelentkezzen be a [Azure Portalba], keresse meg és válassza ki a **app Services**, majd válassza ki az alkalmazást. Jegyezze fel az alkalmazás **URL-címét**. Ezzel konfigurálja a Azure Active Directory alkalmazás regisztrációját.
1. A portál menüjében válassza a **Azure Active Directory** lehetőséget, majd nyissa meg a **Alkalmazásregisztrációk** lapot, és válassza az **új regisztráció** lehetőséget.
1. Az **alkalmazás regisztrálása** lapon adja meg az alkalmazás regisztrációjának **nevét** .
1. Az **átirányítási URI**-ban válassza a **webes** és típus lehetőséget `<app-url>/.auth/login/aad/callback` . Például: `https://contoso.azurewebsites.net/.auth/login/aad/callback`.
1. Válassza a **Regisztráció** lehetőséget.
1. Az alkalmazás regisztrációjának létrehozása után másolja az **alkalmazás (ügyfél) azonosítóját** és a **címtár (bérlő) azonosítóját** később.
1. Válassza a **Hitelesítés** lehetőséget. Az **implicit támogatás** lehetőségnél engedélyezze az **azonosító jogkivonatokat** az OpenID Connect felhasználói bejelentkezések app Serviceból való engedélyezéséhez.
1. Választható Válassza a **branding** elemet. A **Kezdőlap URL-címe** mezőben adja meg app Service alkalmazásának URL-címét, majd válassza a **Mentés** lehetőséget.
1. Válasszon ki **egy API**-  >  **készletet**. Egybérlős alkalmazás esetén illessze be a App Service alkalmazás URL-címét, majd válassza a **Mentés** és a több-bérlős alkalmazás lehetőséget, illessze be az URL-címet, amely a bérlő által ellenőrzött tartományok egyikén alapul, majd válassza a **Mentés** lehetőséget.

   > [!NOTE]
   > Ez az érték az alkalmazás regisztrációs **azonosítójának URI** azonosítója. Ha a webalkalmazásnak hozzáférést kell biztosítania egy Felhőbeli API-hoz, akkor a felhő App Service erőforrásának konfigurálásakor a webalkalmazás **alkalmazás-azonosító URI-ja** szükséges. Ezt például akkor használhatja, ha azt szeretné, hogy a Cloud Service explicit módon engedélyezze a hozzáférést a webalkalmazáshoz.

1. Válassza a **Hatókör hozzáadása** lehetőséget.
   1. A **hatókör neve** mezőbe írja be a *user_impersonation*.
   1. A szövegmezőben adja meg a belefoglalni kívánt hatókör nevét és leírását, amelyet a felhasználók a beleegyező lapon láthatnak. Írja be például a következőt: *hozzáférés az alkalmazáshoz*.
   1. Válassza a **hatókör hozzáadása** elemet.
1. Választható Az ügyfél titkos kulcsának létrehozásához válassza a **tanúsítványok & Secrets**  >  **új ügyfél titkos**  >  **hozzáadása** elemet. Másolja a lapon látható ügyfél titkos kulcs értékét. Nem jelenik meg újra.
1. Választható Több **Válasz URL-cím** hozzáadásához válassza a **hitelesítés** lehetőséget.

### <a name="enable-azure-active-directory-in-your-app-service-app"></a><a name="secrets"> </a>Azure Active Directory engedélyezése a app Service alkalmazásban

1. Jelentkezzen be a [Azure Portalba] , és navigáljon az alkalmazáshoz.
1. A bal oldali menüben válassza a **hitelesítés** lehetőséget. Kattintson a **személyazonosság-szolgáltató hozzáadása** elemre.
1. Válassza a **Microsoft** lehetőséget az identitás-szolgáltató legördülő menüben.
1. Az **alkalmazás regisztrálása** beállításnál választhat, hogy kiválaszt **egy meglévő alkalmazást ebben a címtárban** , amely automatikusan összegyűjti a szükséges alkalmazás-információkat. Ha a regisztráció egy másik bérlőtől származik, vagy Önnek nincs engedélye a regisztrációs objektum megtekintésére, válassza **a meglévő alkalmazás-regisztráció részleteinek megadása** lehetőséget. Ehhez a beállításhoz ki kell töltenie a következő konfigurációs adatokat:

    |Mező|Leírás|
    |-|-|
    |Alkalmazás (ügyfél) azonosítója| Használja az **alkalmazás regisztrációjának alkalmazás-(ügyfél-) azonosítóját** . |
    |Ügyfél titka (nem kötelező)| Használja az alkalmazás regisztrációjában létrehozott ügyfél-titkos kulcsot. Az ügyfél titka, a hibrid folyamat használatos, a App Service pedig visszaküldi a hozzáférési és frissítési jogkivonatokat. Ha nincs beállítva az ügyfél titkos kulcsa, a rendszer implicit folyamatot használ, és csak egy azonosító jogkivonatot ad vissza. Ezeket a jogkivonatokat a szolgáltató küldte el, és az EasyAuth jogkivonat-tárolóban tárolja.|
    |Kiállító URL-címe| A `<authentication-endpoint>/<tenant-id>/v2.0` és a helyett használja a *\<authentication-endpoint>* felhőalapú környezet (például "" globális Azure-hoz) [hitelesítési végpontját](../active-directory/develop/authentication-national-cloud.md#azure-ad-authentication-endpoints) is, és cserélje le https://login.microsoftonline.com *\<tenant-id>* azt a **címtár-(bérlői) azonosítóra** , amelyben az alkalmazás regisztrálása létrejött. Ez az érték a felhasználók megfelelő Azure AD-bérlőre való átirányítására, valamint a megfelelő metaadatok letöltésére szolgál a megfelelő jogkivonat-aláíró kulcsok és jogkivonat-kiállítói jogcím értékének meghatározásához. Az Azure AD v1-t és Azure Functions alkalmazásokat használó alkalmazások esetében hagyja `/v2.0` ki az URL-címet.|
    |Engedélyezett jogkivonat-célközönségek| Ha ez egy Felhőbeli vagy kiszolgálóalkalmazás-alkalmazás, és engedélyezni szeretné a hitelesítési jogkivonatokat egy webalkalmazásból, adja hozzá a webalkalmazás **alkalmazás-azonosító URI-ját** itt. A konfigurált **ügyfél** -azonosító *mindig* implicit módon engedélyezett célközönségnek tekintendő.|

    Az ügyfél titkos kulcsát a rendszer tárolóhelyként rögzített [alkalmazás-beállításként](./configure-common.md#configure-app-settings) tárolja `MICROSOFT_PROVIDER_AUTHENTICATION_SECRET` . Ezt a beállítást később is frissítheti [Key Vault hivatkozások](./app-service-key-vault-references.md) használatához, ha a titkos kulcsot szeretné kezelni a Azure Key Vaultban.

1. Ha ez az alkalmazáshoz konfigurált első identitás-szolgáltató, akkor a rendszer a **app Service hitelesítési beállítások** szakaszt is kéri. Ellenkező esetben a következő lépésre léphet.
    
    Ezek a beállítások határozzák meg, hogy az alkalmazás hogyan válaszol a nem hitelesített kérelmekre, és az alapértelmezett beállítások átirányítják az összes kérelmet az új szolgáltatóval való bejelentkezéshez. Módosíthatja a viselkedés testre szabását, vagy később is módosíthatja ezeket a beállításokat a  **hitelesítési beállítások** melletti **Szerkesztés** lehetőség választásával. További információ ezekről a lehetőségekről: [hitelesítési folyamat](overview-authentication-authorization.md#authentication-flow).

1. Kattintson a **Hozzáadás** parancsra.

Most már készen áll a Microsoft Identity platform használatára a hitelesítéshez az alkalmazásban. A szolgáltató a **hitelesítés** képernyőn jelenik meg. Innen módosíthatja vagy törölheti a szolgáltatói konfigurációt.

## <a name="configure-client-apps-to-access-your-app-service"></a>Ügyfélalkalmazások konfigurálása a App Service eléréséhez

Az előző szakaszban regisztrálta a App Service vagy az Azure-függvényt a felhasználók hitelesítéséhez. Ez a szakasz azt ismerteti, hogyan regisztrálhat natív ügyfél-vagy démon-alkalmazásokat, hogy a felhasználók nevében vagy önmagukban is hozzáférhessenek a App Service által elérhető API-khoz. A szakasz lépéseinek elvégzése nem kötelező, ha csak a felhasználókat kívánja hitelesíteni.

### <a name="native-client-application"></a>Natív ügyfélalkalmazás

A natív ügyfeleket regisztrálhatja úgy, hogy a bejelentkezett felhasználó nevében hozzáférést Kérjen a App Service alkalmazás API-jai számára.

1. A [Azure Portal]válassza a **Active Directory**  >  **Alkalmazásregisztrációk**  >  **új regisztráció** lehetőséget.
1. Az **alkalmazás regisztrálása** lapon adja meg az alkalmazás regisztrációjának **nevét** .
1. Az **átirányítási URI**-ban válassza a **nyilvános ügyfél (mobil & asztal)** lehetőséget, és írja be az URL-címet `<app-url>/.auth/login/aad/callback` . Például: `https://contoso.azurewebsites.net/.auth/login/aad/callback`.

    > [!NOTE]
    > Microsoft Store alkalmazás esetén használja a [csomag biztonsági azonosítóját](/previous-versions/azure/app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library#package-sid) URI-ként.
1. Válassza a **Létrehozás** lehetőséget.
1. Az alkalmazás regisztrációjának létrehozása után másolja az **Application (ügyfél) azonosító** értékét.
1. Válassza az **API-engedélyek**  >  **Hozzáadás engedély**  >  **saját API**-k lehetőséget.
1. Válassza ki a korábban létrehozott App Service alkalmazás regisztrációját. Ha nem látja az alkalmazás regisztrációját, győződjön meg arról, hogy felvette a **user_impersonation** hatókört az [alkalmazás regisztrációjának létrehozása az Azure ad-ben a app Service alkalmazáshoz](#register).
1. A **delegált engedélyek** területen válassza a **user_impersonation** lehetőséget, majd válassza az **engedélyek hozzáadása** elemet.

Ezzel konfigurált egy natív ügyfélalkalmazás-alkalmazást, amely egy felhasználó nevében hozzáférést kérhet a App Service alkalmazáshoz.

### <a name="daemon-client-application-service-to-service-calls"></a>Daemon Client Application (szolgáltatások közötti hívások)

Az alkalmazás képes jogkivonatot beszerezni egy olyan webes API meghívásához, amelyet saját maga (nem a felhasználó nevében) üzemeltet a App Service vagy a Function alkalmazásban. Ez a forgatókönyv olyan nem interaktív Daemon-alkalmazások esetében hasznos, amelyek bejelentkezett felhasználó nélkül végeznek feladatokat. A standard OAuth 2,0 ügyfél- [hitelesítő adatok](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md) engedélyezését használja.

1. A [Azure Portal]válassza a **Active Directory**  >  **Alkalmazásregisztrációk**  >  **új regisztráció** lehetőséget.
1. Az **alkalmazás regisztrálása** lapon adja meg a démon-alkalmazás regisztrációjának **nevét** .
1. Egy démon alkalmazás esetében nincs szükség átirányítási URI-ra, hogy az üres legyen.
1. Válassza a **Létrehozás** lehetőséget.
1. Az alkalmazás regisztrációjának létrehozása után másolja az **Application (ügyfél) azonosító** értékét.
1. Válassza a **tanúsítványok & Secrets**  >  **új ügyfél titkos**  >  **hozzáadása** elemet. Másolja a lapon látható ügyfél titkos kulcs értékét. Nem jelenik meg újra.

Mostantól [kérhet hozzáférési tokent az ügyfél-azonosító és az ügyfél titkos kulcsa](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#first-case-access-token-request-with-a-shared-secret) alapján, ha a `resource` paramétert a célalkalmazás **alkalmazás-azonosító URI azonosítójával** állítja be. Az eredményül kapott hozzáférési jogkivonatot ezután a standard [OAuth 2,0 engedélyezési fejléc](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#use-the-access-token-to-access-the-secured-resource)használatával lehet megtekinteni a célalkalmazás számára, és app Service hitelesítés/engedélyezés ellenőrzi, majd a tokent a szokásos módon fogja használni, hogy a hívó (ebben az esetben az alkalmazás nem felhasználó) legyen hitelesítve.

Ez lehetővé teszi, hogy az Azure AD-bérlő _bármely_ ügyfélalkalmazás egy hozzáférési jogkivonatot kérjen, és hitelesítse magát a célalkalmazás számára. Ha azt is szeretné kényszeríteni, hogy csak bizonyos ügyfélalkalmazások engedélyezzék az _engedélyezést_ , néhány további konfigurációt is végre kell hajtania.

1. [Definiáljon egy alkalmazás-szerepkört](../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md) a védelemmel ellátni kívánt app Service vagy Function alkalmazást jelölő alkalmazás-regisztráció jegyzékfájljában.
1. Az engedélyezni kívánt ügyfelet képviselő alkalmazás-regisztrációnál válassza az API- **engedélyek**  >  **Hozzáadás engedély**  >  **saját API**-k lehetőséget.
1. Válassza ki a korábban létrehozott alkalmazás-regisztrációt. Ha nem látja az alkalmazás regisztrációját, győződjön meg róla, hogy [hozzáadta az alkalmazás-szerepkört](../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md).
1. Az **alkalmazás engedélyei** területen válassza ki a korábban létrehozott alkalmazás-szerepkört, majd kattintson az **engedélyek hozzáadása** lehetőségre.
1. Győződjön meg arról, hogy a **rendszergazdai jóváhagyás megadása** lehetőségre kattintva engedélyezi az ügyfélalkalmazás számára az engedély kérését.
1. Az előző forgatókönyvhöz hasonlóan (a Szerepkörök hozzáadása előtt) mostantól [kérhet hozzáférési jogkivonatot](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#first-case-access-token-request-with-a-shared-secret) ugyanarra a célra `resource` , és a hozzáférési jogkivonat tartalmazni fogja `roles` az ügyfélalkalmazás számára jóváhagyott alkalmazás-szerepköröket tartalmazó jogcímet.
1. A cél App Service vagy a Function app Code-ban ellenőrizheti, hogy a várt szerepkörök szerepelnek-e a jogkivonatban (ezt az App Service hitelesítés/engedélyezés nem végzi el). További információ: hozzáférés a [felhasználói jogcímekhez](app-service-authentication-how-to.md#access-user-claims).

Ezzel konfigurált egy Daemon ügyfélalkalmazás-alkalmazást, amely a saját identitásával fér hozzá a App Service alkalmazáshoz.

## <a name="best-practices"></a>Ajánlott eljárások

A hitelesítés beállításához használt konfigurációtól függetlenül a következő ajánlott eljárások biztosítják a bérlő és az alkalmazások biztonságát:

- Adja meg az egyes App Service alkalmazások saját engedélyeit és hozzájárulásukat.
- Konfigurálja az egyes App Service alkalmazásait a saját regisztrálásával.
- Kerülje a környezetek közötti engedélyek megosztását külön alkalmazás-regisztrációk használatával külön üzembe helyezési pontokhoz. Az új kód tesztelésekor ez a gyakorlat segít megakadályozni az éles alkalmazást érintő problémákat.

## <a name="next-steps"></a><a name="related-content"> </a>Következő lépések

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]
* [Oktatóanyag: felhasználók hitelesítése és engedélyezése egy olyan webalkalmazásban, amely hozzáfér az Azure Storage szolgáltatáshoz és Microsoft Graph](scenario-secure-app-authentication-app-service.md)
* [Oktatóanyag: Felhasználók hitelesítése és engedélyezése végpontok között az Azure App Service-ben](tutorial-auth-aad.md)
<!-- URLs. -->

[Azure Portal]: https://portal.azure.com/
