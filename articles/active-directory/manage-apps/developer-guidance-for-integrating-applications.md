---
title: Az alkalmazás regisztrálása a Azure Active Directory használatára | Microsoft Docs
description: Ez a cikk az informatikai szakembereknek készült, az Azure-alkalmazások Active Directory segítségével történő integrálásához nyújt útmutatást.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: de16c947c59f5a0111b9325dbefe7daf1268fb40
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94649160"
---
# <a name="develop-line-of-business-apps-for-azure-active-directory"></a>Üzletági alkalmazások fejlesztése a Azure Active Directory
Ez az útmutató áttekintést nyújt az üzletági (LoB) alkalmazások Azure Active Directory (AD) való fejlesztéséről. A célközönség Active Directory/Microsoft 365 globális rendszergazdák.

## <a name="overview"></a>Áttekintés
Az Azure AD-vel integrált alkalmazások kiépítése lehetővé teszi a felhasználók számára, hogy Microsoft 365 segítségével egyszeri bejelentkezéssel jelentkezzenek be. Az alkalmazás az Azure AD-ben való használata lehetővé teszi az alkalmazás hitelesítési házirendjének szabályozását. További információ a feltételes hozzáférésről és az alkalmazások a többtényezős hitelesítéssel (MFA) való védelemmel kapcsolatban: [hozzáférési szabályok konfigurálása](../authentication/tutorial-enable-azure-mfa.md).

Regisztrálja az alkalmazást Azure Active Directory használatához. Az alkalmazás regisztrálása azt jelenti, hogy a fejlesztők az Azure AD használatával hitelesítik a felhasználókat, és felhasználói erőforrásokhoz, például e-mailekhez, naptárhoz és dokumentumokhoz férhetnek hozzá.

A címtár bármely tagja (nem pedig a vendégek) regisztrálhat egy alkalmazást, más néven *az alkalmazás-objektum létrehozását*. Ha nem tud regisztrálni egy alkalmazást, az azt jelenti, hogy a címtár globális rendszergazdája korlátozta ezt a funkciót, és előfordulhat, hogy kapcsolatba kell lépnie velük a [megfelelő jogosultságok beszerzése](../roles/delegate-app-roles.md#assign-built-in-application-admin-roles) érdekében, hogy regisztrálni tudja az alkalmazást. Ha többet szeretne megtudni arról, hogyan korlátozhatja a felhasználók számára [az alkalmazás-regisztrációs engedélyek delegálását Azure Active Directory](../roles/delegate-app-roles.md#restrict-who-can-create-applications).

Az alkalmazások regisztrálása lehetővé teszi, hogy a felhasználók a következőket tegye:

* Identitás beszerzése az Azure AD által felismert alkalmazásokhoz
* Szerezzen be egy vagy több titkot/kulcsot, amelyeket az alkalmazás használhat az AD-ben való hitelesítéshez
* Az alkalmazás a Azure Portalban egyéni névvel, emblémával stb.
* Az Azure AD-engedélyezési funkciók alkalmazása az alkalmazásra, beleértve a következőket:

  * Szerepköralapú hozzáférés-vezérlés (RBAC)
  * Azure Active Directory oAuth-engedélyezési kiszolgálóként (az alkalmazás által elérhetővé tett API biztonságossá tétele)
* Állapítsa meg a szükséges engedélyeket, amelyek szükségesek ahhoz, hogy az alkalmazás a várt módon működjön, beleértve a következőket:

     - Alkalmazás-engedélyek (csak globális rendszergazdák). Például: szerepkör-tagság egy másik Azure AD-alkalmazásban vagy szerepkör-tagság egy Azure-erőforráshoz, erőforráscsoporthoz vagy előfizetéshez képest
     - Delegált engedélyek (bármely felhasználó). Például: Azure AD, bejelentkezés és olvasási profil

> [!NOTE]
> Alapértelmezés szerint bármelyik tag regisztrálhat egy alkalmazást. Ha szeretné megtudni, hogyan korlátozhatja az alkalmazások adott tagokra való regisztrálásának engedélyeit, tekintse meg az [alkalmazások az Azure ad-be való hozzáadásának módját](../develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance).
>
>

A globális rendszergazdának a következőket kell tennie, hogy segítse a fejlesztőket az alkalmazások éles környezetben való használatra való előkészítésében:

* Hozzáférési szabályok konfigurálása (hozzáférési szabályzat/MFA)
* Az alkalmazás konfigurálása felhasználói hozzárendelés megköveteléséhez és felhasználók hozzárendeléséhez
* Az alapértelmezett felhasználói beleegyezett élmény mellőzése

## <a name="configure-access-rules"></a>Hozzáférési szabályok konfigurálása
Alkalmazáson belüli hozzáférési szabályok konfigurálása az SaaS-alkalmazásokhoz. Megkövetelheti például az MFA-t, vagy csak megbízható hálózatokon lévő felhasználók számára engedélyezze a hozzáférést. Ennek részletei a [hozzáférési szabályok konfigurálása című](../authentication/tutorial-enable-azure-mfa.md)dokumentumban találhatók.

## <a name="configure-the-app-to-require-user-assignment-and-assign-users"></a>Az alkalmazás konfigurálása felhasználói hozzárendelés megköveteléséhez és felhasználók hozzárendeléséhez
Alapértelmezés szerint a felhasználók hozzárendelése nélkül férhetnek hozzá az alkalmazásokhoz. Ha azonban az alkalmazás szerepköröket tesz elérhetővé, vagy ha azt szeretné, hogy az alkalmazás megjelenjen a felhasználó saját alkalmazásaiban, felhasználói hozzárendelés szükséges.

Ha Ön prémium szintű Azure AD vagy nagyvállalati mobilitási csomag (EMS) előfizető, javasoljuk, hogy használjon csoportokat. A csoportok az alkalmazáshoz való hozzárendelésével a folyamatos hozzáférés-kezelést delegálhatja a csoport tulajdonosának. Létrehozhatja a csoportot, vagy megkérheti a szervezet felelős személyét, hogy hozza létre a csoportot a csoport-felügyeleti létesítmény használatával.

[Felhasználók és csoportok társítása egy alkalmazáshoz](./assign-user-or-group-access-portal.md)  


## <a name="suppress-user-consent"></a>Felhasználói engedély mellőzése
Alapértelmezés szerint minden felhasználó beleegyezik a bejelentkezéshez szükséges engedélyekkel. A belefoglalási élmény, amely arra kéri a felhasználókat, hogy engedélyeket adjanak egy alkalmazásnak, olyan felhasználók számára is megoldhatók, akik nem ismerik az ilyen döntéseket.

A megbízható alkalmazások esetében egyszerűbbé teheti a felhasználói élményt azáltal, hogy a szervezet nevében hozzájárul az alkalmazáshoz.

További információ a felhasználói belefoglalásról és az Azure-beli belefoglalási feladatokról: [alkalmazások integrálása a Azure Active Directorysal](../develop/quickstart-register-app.md).

## <a name="related-articles"></a>Kapcsolódó cikkek
* [Helyszíni alkalmazások biztonságos távoli elérésének engedélyezése az Azure AD Application Proxy](application-proxy.md)
* [Az alkalmazásokhoz való hozzáférés kezelése az Azure AD-vel](what-is-access-management.md)