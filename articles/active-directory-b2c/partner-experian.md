---
title: Oktatóanyag a Azure Active Directory B2C konfigurálásához a Experian használatával
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan integrálhatja Azure AD B2C hitelesítését a Experian az azonosítás ellenőrzéséhez és a felhasználói attribútumok alapján történő ellenőrzéshez a csalások megelőzése érdekében.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/22/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 2058e5362a65b6cd5f3e5b7cb9c20ce32d020d30
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96928698"
---
# <a name="tutorial-for-configuring-experian-with-azure-active-directory-b2c"></a>Oktatóanyag a Experian konfigurálásához a Azure Active Directory B2C

Ebben a példában a Azure AD B2C és a [Experian](https://www.experian.com/decision-analytics/account-opening-fraud/microsoft-integration)integrálását ismertető útmutató nyújt útmutatást. A Experian számos megoldást kínál, amelyeket [itt](https://www.experian.com/)talál.

Ebben a mintában a Experian integrált digitális identitás-és adathamisítási platformjának **CrossCore** van használatban. A CrossCore egy azonosító-ellenőrző szolgáltatás, amely a felhasználó azonosításának ellenőrzésére szolgál. Ez a kockázat elemzése a felhasználó által a regisztrálási folyamat során biztosított számos adat alapján történik. A CrossCore segítségével határozható meg, hogy a felhasználó számára engedélyezett-e a bejelentkezés. A következő attribútumok használhatók a CrossCore kockázati elemzésében:

- E-mail
- IP-cím
- utónév;
- Középső név
- vezetéknév;
- Utca, házszám
- City
- Állam/megye
- Irányítószám
- Ország/régió
- Telefonszám

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következőkre lesz szüksége:

- Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.

- [Egy Azure ad B2C bérlő](./tutorial-create-tenant.md) , amely az Azure-előfizetéshez van csatolva.

## <a name="scenario-description"></a>Forgatókönyv leírása

A Experian-integráció a következő összetevőket tartalmazza:

- Azure AD B2C – az engedélyezési kiszolgáló, amely a felhasználó hitelesítő adatainak (más néven identitás-szolgáltató) ellenőrzéséhez felelős.

- Experian – a Experian szolgáltatás átveszi a felhasználó által megadott bemeneteket, és ellenőrzi a felhasználó identitását.

- Egyéni REST API – ez az API implementálja Azure AD B2C és a Experian szolgáltatás közötti integrációt.

A következő architektúra-diagram a megvalósítást mutatja be.

![képernyőkép a Experian-Architecture-diagramról](media/partner-experian/experian-architecture-diagram.png)

|Lépés | Description |
|:-----| :-----------|
| 1. | A felhasználó egy bejelentkezési oldalon érkezik. A felhasználó új fiók létrehozásához és az oldalba való belépéshez kiválasztja a regisztrációt. A Azure AD B2C a felhasználói attribútumokat gyűjti.
| 2. | Azure AD B2C meghívja a középső rétegbeli API-t, és átadja a felhasználói attribútumokat.
| 3. | A középső rétegbeli API gyűjti a felhasználói attribútumokat, és átalakítja a Experian API-t használó formátumba. Ezt követően küldje el a Experian.
| 4. | A Experian felhasználja az adatokat, és feldolgozza azokat a kockázatelemzés alapján a felhasználók azonosításának ellenőrzéséhez. Ezután visszaadja az eredményt a középső rétegbeli API-nak.
| 5. | A középső rétegbeli API feldolgozza az adatokat, és a megfelelő JSON formátumban küldi el a kapcsolódó információkat a Azure AD B2C.
| 6. | Azure AD B2C adatokat fogad a középső rétegbeli API-ból. Ha hibát jelez, hibaüzenet jelenik meg a felhasználó számára. Ha sikeres választ mutat, a rendszer hitelesíti a felhasználót, és beírja a könyvtárba.

## <a name="onboard-with-experian"></a>Experian

1. Experian-fiók létrehozásához forduljon a [Experian](https://www.experian.com/decision-analytics/account-opening-fraud/microsoft-integration)

2. A fiók létrehozása után megkapja az API-konfigurációhoz szükséges információkat. A következő szakaszok ismertetik a folyamatot.

## <a name="configure-azure-ad-b2c-with-experian"></a>Azure AD B2C konfigurálása a Experian

### <a name="part-1---deploy-the-api"></a>1. rész – az API üzembe helyezése

A megadott [API-kód](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Experian/CrossCoreIntegrationApi/CrossCoreIntegrationApi.sln) üzembe helyezése egy Azure-szolgáltatásban. A kód a Visual studióból is közzétehető, ezeket az [utasításokat](/visualstudio/deployment/quickstart-deploy-to-azure)követve.

>[!NOTE]
>Szüksége lesz az üzembe helyezett szolgáltatás URL-címére az Azure AD konfigurálásához a szükséges beállításokkal.

### <a name="part-2---deploy-the-client-certificate"></a>2. rész – az ügyféltanúsítvány üzembe helyezése

A Experian API-hívást egy ügyféltanúsítvány védi. Ezt az ügyféltanúsítványt a Experian biztosítja. A jelen [dokumentumban](../app-service/environment/certificates.md#private-client-certificate)említett utasításokat követve a tanúsítványt fel kell tölteni az Azure app Service-be. A minta házirend ezeket a kulcsokat a következő folyamat során használja:

- A tanúsítvány feltöltése

- Állítsa be a `WEBSITE_LOAD_ROOT_CERTIFICATES` kulcsot a tanúsítvány ujjlenyomatával.

### <a name="part-3---configure-the-api"></a>3. rész – az API konfigurálása

[Az Alkalmazásbeállítások konfigurálhatók az Azure app Service-ben](../app-service/configure-common.md#configure-app-settings). Ezzel a módszerrel a beállítások biztonságosan konfigurálhatók egy adattárba való ellenőrzés nélkül. A REST API-hoz a következő beállításokat kell megadnia:

| Alkalmazásbeállítások | Forrás | Jegyzetek |
| :-------- | :------------| :-----------|
|CrossCoreConfig: TenantId | Experian-fiók konfigurálása |     |
|CrossCoreConfig:OrgCode | Experian-fiók konfigurálása |     |
|CrossCore:ApiEndpoint |Experian-fiók konfigurálása|  |
|CrossCore:ClientReference | Experian-fiók konfigurálása | |
| CrossCore:ModelCode |Experian-fiók konfigurálása|
| CrossCore:OrgCode | Experian-fiók konfigurálása |
| CrossCore:SignatureKey  | Experian-fiók konfigurálása |
| CrossCore: TenantId  | Experian-fiók konfigurálása |
| CrossCore: CertificateThumbprint | Experian-tanúsítvány |
| Alapfokú hitelesítés: ApiUsername | Adja meg az API-hoz tartozó felhasználónevet | A ExtId-konfigurációban használatos |
| Alapfokú hitelesítés: ApiPassword | Adja meg az API-hoz tartozó jelszót | A ExtId-konfigurációban használatos

### <a name="part-4---create-api-policy-keys"></a>4. rész – API-szabályzatok kulcsainak létrehozása

Tekintse meg ezt a [dokumentumot](./secure-rest-api.md#add-rest-api-username-and-password-policy-keys) , és hozzon létre két házirend-kulcsot – egyet az API-felhasználónévhez, egyet pedig a fentiekben megadott API-jelszóhoz a http alapszintű hitelesítéshez.

>[!NOTE]
>A szabályzatok konfigurálásához később szükség lesz a kulcsokra.

### <a name="part-5---replace-the-configuration-values"></a>5. rész – a konfigurációs értékek cseréje

A megadott [Egyéni szabályzatokban](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Experian/policy)keresse meg a következő helyőrzőket, és cserélje le a elemet a példány megfelelő értékeire.

|                      Helyőrző                       |                                   Csere értékkel                                 |                   Példa                    |
| ------------------------------------------------------ | -------------------------------------------------------------------------------- | -------------------------------------------- |
| {your_tenant_name}                                     | A bérlő rövid neve                                                           | "yourtenant" a yourtenant.onmicrosoft.com |
| {your_trustframeworkbase_policy}                       | A TrustFrameworkBase szabályzat Azure AD B2C neve                  | B2C_1A_experian_TrustFrameworkBase           |
| {your_tenant_IdentityExperienceFramework_appid}        | Az Azure AD B2C-bérlőben konfigurált IdentityExperienceFramework-alkalmazás alkalmazás-azonosítója      | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_ ProxyIdentityExperienceFramework _appid} | Az Azure AD B2C-bérlőben konfigurált ProxyIdentityExperienceFramework-alkalmazás alkalmazás-azonosítója | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_extensions_appid}                         | A bérlő tárolási alkalmazásának alkalmazás-azonosítója                                      | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_extensions_app_objectid}                  | A bérlő Storage-alkalmazásának objektum-azonosítója                                   | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_api_username_key_name}                           | Az [itt](#part-4---create-api-policy-keys) létrehozott Felhasználónév-kulcs neve             | B2C \_ 1a \_ RestApiUsername                     |
| {your_api_password_key_name}                           | Az [itt](#part-4---create-api-policy-keys) létrehozott jelszó-kulcs neve             | B2C \_ 1a \_ RestApiPassword                     |
| {your_app_service_URL}                                 | A beállított app Service URL-címe                                             | `https://yourapp.azurewebsites.net`          |

### <a name="part-6---configure-the-azure-ad-b2c-policy"></a>6. rész – a Azure AD B2C házirend konfigurálása

Tekintse át ezt a [dokumentumot](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack) , amely útmutatást nyújt a Azure ad B2C bérlő beállításához és a házirendek konfigurálásához.

>[!NOTE]
>Ez a minta házirend a [helyi fiókok kezdő csomagjára](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)épül.

>[!NOTE]
> Ajánlott eljárásként Azt javasoljuk, hogy az ügyfelek az attribútumok gyűjteménye lapon vegyenek fel beleegyező értesítéseket. Értesítse a felhasználókat arról, hogy az adatok a harmadik féltől származó szolgáltatásoknak személyazonosság-ellenőrzés céljából lesznek elküldve.

## <a name="test-the-user-flow"></a>A felhasználói folyamat tesztelése

1. Nyissa meg a Azure AD B2C bérlőt, és a házirendek területen válassza a **felhasználói folyamatok** lehetőséget.

2. Válassza ki a korábban létrehozott **felhasználói folyamatot**.

3. Válassza a **felhasználói folyamat futtatása** lehetőséget, és válassza ki a beállításokat:

   a. **Alkalmazás**: válassza ki a regisztrált alkalmazást (minta a JWT)

   b. **Válasz URL-címe**: válassza ki az **átirányítási URL-címet**

   c. Válassza a **felhasználói folyamat futtatása** lehetőséget.

4. Ugorjon a regisztrációs folyamatra, és hozzon létre egy fiókot

5. Kijelentkezés

6. Bejelentkezési folyamat átugrása  

7. A **folytatáshoz** a CrossCore-puzzle jelenik meg.

## <a name="next-steps"></a>Következő lépések

További információkért tekintse át a következő cikkeket:

- [Egyéni szabályzatok az Azure AD B2C-ben](./custom-policy-overview.md)

- [Ismerkedés az egyéni szabályzatokkal Azure AD B2C](./custom-policy-get-started.md?tabs=applications)
