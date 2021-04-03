---
title: Új Alkalmazásregisztrációk-élmény Azure AD B2C
description: Bevezetés a Azure AD B2C új alkalmazás-regisztrációs felületének használatába.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 05/25/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 430da23986fc36a0e94c049512ef716aff1fed5c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98660250"
---
# <a name="the-new-app-registrations-experience-for-azure-active-directory-b2c"></a>A Azure Active Directory B2C új Alkalmazásregisztrációk felülete

A Azure Active Directory B2C (Azure AD B2C) új **[Alkalmazásregisztrációk](https://aka.ms/b2cappregistrations)** -felülete már általánosan elérhető. Ha többet szeretne megtudni az **alkalmazások** Azure ad B2Cre való regisztrálásához, amelyet a "régi felhasználói élmény" című témakörben talál, akkor ez az útmutató az új felület használatának első lépéseit mutatja be.

## <a name="overview"></a>Áttekintés
Korábban a régi felhasználói felülettel felügyelheti a Azure AD B2C a felhasználó által megjelenő alkalmazásokat a többi alkalmazástól függetlenül. Ez különböző alkalmazások létrehozását jelentette az Azure különböző helyein.

Az új felhasználói élmény egy helyen jeleníti meg az összes Azure AD B2C alkalmazás regisztrációját és az Azure AD-alkalmazások regisztrációját, és egységes módon kezeli őket. Egy olyan alkalmazás létrehozásához, amely Microsoft Graph engedélyekkel rendelkezik az erőforrás-kezeléshez, csak az egyik módszert kell megtanulnia.

Az új élményt úgy érheti el, ha a Azure Portal **Azure ad B2C** vagy a **Azure Active Directory** szolgáltatásainak egyik Azure AD B2C-bérlője **Alkalmazásregisztrációk** navigál.

A Azure AD B2C Alkalmazásregisztrációk felhasználói élmény az Azure AD-bérlők általános [alkalmazás-regisztrációs felületén](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) alapul, de Azure ad B2C bérlők számára van kialakítva.

## <a name="whats-not-changing"></a>Mi nem változik?
- Az alkalmazások és a kapcsolódó konfigurációk az új felületén is megtalálhatók. Nem kell újból regisztrálnia az alkalmazásokat, és az alkalmazások felhasználóinak újra be kell jelentkezniük.

> [!NOTE]
> Az összes korábban létrehozott alkalmazás megtekintéséhez navigáljon a **Alkalmazásregisztrációk** panelre, és válassza a **minden alkalmazás** lapot. Ez megjeleníti a régi felhasználói élményben, az új felhasználói élményben és az Azure AD szolgáltatásban létrehozott alkalmazásokat.

## <a name="key-new-features"></a>Legfontosabb új funkciók

-   Az **egyesített alkalmazások listája** az összes olyan alkalmazást megjeleníti, amely a Azure ad B2C és az Azure ad-vel való hitelesítés egyetlen kényelmes helyen történik. Emellett kihasználhatja az Azure AD-alkalmazásokhoz már elérhető szolgáltatások előnyeit, beleértve a **Létrehozás** dátuma, a **tanúsítványok & a titkok** állapotát, a keresősáv és sok más egyéb funkciót is.

-   Az **alkalmazások regisztrálásával** gyorsan regisztrálhat egy alkalmazást, függetlenül attól, hogy az ügyfél vagy az alkalmazás fér hozzá Microsoft Graphhoz.

- A **végpontok** panelen gyorsan azonosíthatja a forgatókönyvhöz tartozó végpontokat, beleértve az OpenID Connect konfigurációját, az SAML-metaadatokat, az Microsoft Graph API-t és a [OAuth 2,0 felhasználói folyamat végpontját](tokens-overview.md#endpoints).

- Az **API-engedélyek** és **az API** -k közzététele átfogóbb hatókör-, engedély-és beleegyezési felügyeletet biztosít. Mostantól az MS Graph és az Azure AD Graph engedélyeket is hozzárendelhet egy alkalmazáshoz.

-   A **tulajdonosok** és a **jegyzékfájl** mostantól olyan alkalmazásokhoz érhető el, amelyek hitelesítése Azure ad B2C. Hozzáadhat tulajdonosokat a regisztrációhoz, és közvetlenül szerkesztheti az alkalmazás tulajdonságait [a jegyzékfájl-szerkesztő használatával](../active-directory/develop/reference-app-manifest.md).


## <a name="new-supported-account-types"></a>Új támogatott fióktípus

Az új felhasználói élményben válasszon ki egy támogatási fiókot a következő lehetőségek közül:
- Csak az ebben a szervezeti címtárban található fiókok
- Fiókok bármely szervezeti címtárban (bármely Azure AD-címtár – több-bérlős)
- Fiókok bármely identitás-szolgáltató vagy szervezeti címtárban (a felhasználók felhasználói folyamatokkal történő hitelesítéséhez)

A különböző fióktípus megismeréséhez válassza a létrehozási **élmény lehetőséget.**

A régi élményben az alkalmazások létrehozása mindig ügyfél-alkalmazásként történt. Ezekhez az alkalmazásokhoz a fiók típusa **bármely identitás-szolgáltató vagy szervezeti címtár fiókjaira van beállítva (a felhasználók felhasználói folyamatokkal történő hitelesítéséhez)**.
> [!NOTE]
> Ez a beállítás ahhoz szükséges, hogy Azure AD B2C felhasználói folyamatokat futtasson az alkalmazás felhasználóinak hitelesítéséhez. Megtudhatja [, hogyan regisztrálhat egy alkalmazást felhasználói folyamatokkal való használatra.](tutorial-register-applications.md)

Ezt a lehetőséget használhatja a Azure AD B2C SAML-szolgáltatóként való használatára is. [További információk](identity-provider-adfs.md).

## <a name="applications-for-devops-scenarios"></a>Alkalmazások DevOps-forgatókönyvekhez

A más fióktípus használatával létrehozhat egy alkalmazást a DevOps-forgatókönyvek kezeléséhez, például a Microsoft Graph használata az identitási élményhez tartozó szabályzatok feltöltéséhez vagy a felhasználók kiépítéséhez. Megtudhatja [, hogyan regisztrálhat egy Microsoft Graph alkalmazást Azure ad B2C erőforrások kezeléséhez](microsoft-graph-get-started.md).

Előfordulhat, hogy nem látja az összes Microsoft Graph engedélyt, mert az engedélyek közül sok nem vonatkozik az Azure B2C fogyasztói felhasználókra. [További információ a felhasználók Microsoft Graph használatával történő kezeléséről](microsoft-graph-operations.md).

## <a name="admin-consent-and-offline_accessopenid-scopes"></a>Rendszergazdai engedély és offline_access + OpenID-hatókörök
<!-- Azure AD B2C doesn't support user consent. That is, when a user signs into an application, the user doesn't see a screen requesting consent for the application permissions. All permissions have to be granted through admin consent.  -->

Az **OpenID** hatókörre azért van szükség, hogy Azure ad B2C be tudja jelentkezni a felhasználókat egy alkalmazásba. A **offline_access** hatókörre van szükség egy felhasználó frissítési jogkivonatának kibocsátásához. Ezek a hatókörök korábban lettek hozzáadva, és a rendszergazdai beleegyező értékkel lettek megadva. A létrehozási folyamat során könnyedén hozzáadhat engedélyeket ezekhez a hatókörökhöz azáltal, hogy a **rendszergazdai jóváhagyás megadása OpenID-és offline_access engedélyek beállítás be** van jelölve. Más esetben a Microsoft Graph engedélyek a meglévő alkalmazások **API-engedélyeinek** beállításaiban adhatók hozzá rendszergazdai jogosultsággal.

További információ az [engedélyekről és a beleegyezik](../active-directory/develop/v2-permissions-and-consent.md).

## <a name="platformsauthentication-reply-urlsredirect-uris"></a>Platformok/hitelesítés: válasz URL-címek/átirányítási URI-k
A régi felhasználói felületen a különböző platformok típusait a **Tulajdonságok** szakaszban kezeltük a Web Apps/API-k és a natív ügyfelek ÁTirányítási URI-ja alapján. A "natív ügyfelek" a "nyilvános ügyfelek" néven is ismertek, és az iOS-, macOS-, Android-és egyéb mobil-és asztali alkalmazás-típusok alkalmazásai is lehetnek.

Az új felhasználói felületen a válasz URL-címei és az átirányítási URI-k is hivatkoznak átirányítási URI-ként, és megtalálhatók az alkalmazás **hitelesítési** szakaszában. Alkalmazásregisztrációk nem kizárólag webalkalmazás vagy natív alkalmazás lehet. A megfelelő átirányítási URI-k regisztrálásával ugyanazt az alkalmazást használhatja a platform összes típusához.

Az átirányítási URI-k szükségesek a web vagy a Public (Mobile és Desktop) típusú alkalmazásokhoz való társításhoz. [További információ az átirányítási URI-k használatáról](../active-directory/develop/quickstart-register-app.md#add-a-redirect-uri)

<!-- Whether an application should be treated as a public client is inferred at run-time from the Redirect URI platform type, if possible. The **Treat application as a public client** setting should be set to **Yes** for flows that might not use a redirect URI, such as ROPC flows. -->

Az **iOS-, MacOS** -és **Android** -platformok nyilvános ügyfél típusúak. Egyszerű módszert biztosítanak az iOS/macOS vagy Android rendszerű alkalmazások konfigurálásához a megfelelő átirányítási URI-k használatával a MSAL-hez való használathoz. További információ az [alkalmazás konfigurációs beállításairól](../active-directory/develop/msal-client-applications.md).


## <a name="application-certificates--secrets"></a>Alkalmazás-tanúsítványok & Secrets

A **kulcsok** helyett az új felhasználói élményben a tanúsítványok **& Secrets** panelen kezelheti a tanúsítványokat és a titkos kulcsokat. A tanúsítványok & titkos kódok lehetővé teszik az alkalmazások számára, hogy azonosítsák magukat a hitelesítési szolgáltatásban, amikor jogkivonatokat fogadnak a webes címezhető helyen (HTTPS-séma használatával). Javasoljuk, hogy az ügyfél hitelesítő adatai helyett egy tanúsítványt használjon az Azure AD-vel való hitelesítéshez. A tanúsítványok nem használhatók a Azure AD B2C való hitelesítéshez.


## <a name="features-not-applicable-in-azure-ad-b2c-tenants"></a>Azure AD B2C bérlők nem alkalmazható szolgáltatásai
A következő Azure AD-alkalmazás-regisztrációs képességek nem alkalmazhatók Azure AD B2C bérlők számára, illetve nem érhetők el:
- **Szerepkörök és rendszergazdák** – jelenleg nem érhető el Azure ad B2Choz.
- **Branding** – a felhasználói felület/UX testreszabása a **vállalati védjegyezési** felületen vagy felhasználói folyamat részeként van konfigurálva. Ismerkedjen meg [a Azure Active Directory B2C felhasználói felületének testreszabásával](customize-ui-with-html.md).
- **Közzétevő tartományának ellenőrzése** – az alkalmazás regisztrálva van a *. onmicrosoft.com*, amely nem ellenőrzött tartomány. Emellett a közzétevői tartományt elsődlegesen a felhasználói jóváhagyás biztosítására használják, amely nem vonatkozik Azure AD B2C alkalmazásokra a felhasználói hitelesítéshez. [További információ a közzétevő tartományáról](../active-directory/develop/howto-configure-publisher-domain.md).
- **Jogkivonat konfigurációja** – a jogkivonat a felhasználói folyamat részeként van konfigurálva, nem pedig alkalmazás.
- A gyors üzembe helyezési **élmény jelenleg** nem érhető el Azure ad B2C bérlők számára.
<!-- - The **Integration assistant** blade is currently not available for Azure AD B2C tenants. -->

## <a name="limitations"></a>Korlátozások
Az új felhasználói élmény a következő korlátozásokkal jár:
- Jelenleg Azure AD B2C nem tesz különbséget a hozzáférés vagy az azonosító jogkivonatok kibocsátása között az implicit folyamatokhoz. Ha a **hitelesítés** panelen az **azonosító tokenek** lehetőség van kiválasztva, mindkét típusú token elérhető az implicit engedélyezési folyamathoz.
<!-- - Azure AD B2C doesn't currently support the single-page application "SPA" app type.  -->
- A támogatott fiókok értékének módosítása nem támogatott a felhasználói felületen. Az alkalmazás jegyzékfájlját kell használnia, kivéve, ha az Azure AD egybérlős és a több-bérlő közötti váltást végzi.

## <a name="next-steps"></a>Következő lépések

Az új alkalmazás regisztrációs élményének megkezdéséhez:
* Ismerje meg [, hogyan regisztrálhat egy webalkalmazást](tutorial-register-applications.md).
* Ismerje meg [, hogyan regisztrálhat webes API-t](add-web-api-application.md).
* Ismerje meg [, hogyan regisztrálhat egy natív ügyfélalkalmazás](add-native-application.md).
* Megtudhatja [, hogyan regisztrálhat egy Microsoft Graph alkalmazást Azure ad B2C erőforrások kezeléséhez](microsoft-graph-get-started.md).
* Ismerje meg [, hogyan használhatja a Azure ad B2C SAML-](identity-provider-adfs.md) szolgáltatóként.
* További információ az [alkalmazások típusairól](application-types.md).