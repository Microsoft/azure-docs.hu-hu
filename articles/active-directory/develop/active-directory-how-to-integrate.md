---
title: Integráció a Microsoft Identity platformmal | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg az alkalmazások Microsoft Identity platformmal való integrálásának előnyeit, és szerezzen forrásokat olyan funkciókhoz, mint az egyszerűsített bejelentkezés, az Identitáskezelés, a többtényezős hitelesítés és a hozzáférés-vezérlés.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/01/2020
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, seoapril2019
ROBOTS: NOINDEX
ms.openlocfilehash: b7ee283ff61753a060e49a3340cd0a795b04faf9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98755934"
---
# <a name="integrating-with-the-microsoft-identity-platform"></a>Integráció a Microsoft Identity platformmal

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Ebből a cikkből megtudhatja, milyen előnyökkel jár az alkalmazás integrálása a Microsoft Identity platformmal, és hogyan szerezhet be erőforrásokat az integrációhoz. A Microsoft Identity platform és a Azure Active Directory (AD) nagyvállalati szintű identitáskezelést biztosít a felhőalapú alkalmazásokhoz. A Microsoft Identity platform integrációja lehetővé teszi a felhasználók számára, hogy zökkenőmentes bejelentkezési élményt biztosítson, és segít az alkalmazásnak megfelelni az informatikai szabályzatoknak.

## <a name="how-to-integrate"></a>Az integrálás menete

Az alkalmazás számos módon integrálható a Microsoft Identity platformmal. Használja ki az alkalmazásának megfelelő számos vagy több ilyen forgatókönyvet.

### <a name="support-the-microsoft-identity-platform-as-a-way-to-sign-in-to-your-application"></a>A Microsoft Identity platform támogatása az alkalmazásba való bejelentkezéshez

**Csökkentse a bejelentkezés súrlódását, és csökkentse a támogatási költségeket.** Ha a Microsoft Identity platformot használja az alkalmazásba való bejelentkezéshez, a felhasználóknak még egy nevet és jelszót sem kell megemlékezniük. Fejlesztőként egy kevesebb jelszót fog tárolni és védelemmel ellátni. Az elfelejtett jelszó-visszaállítások kezelésének mellőzése önmagában jelentős megtakarítás lehet. A Microsoft Identity platform a világ egyik legnépszerűbb felhőalapú alkalmazásához jelentkezik be, beleértve a Microsoft 365 és a Microsoft Azure. A felhasználók milliói több száz millió felhasználót érintenek, így a felhasználó már be van jelentkezve a Microsoft Identity platformba. További információ [a Microsoft Identity platform-bejelentkezés támogatásának hozzáadásáról](./authentication-vs-authorization.md).

**Egyszerűsítse az alkalmazás regisztrálását.**  Az alkalmazásra való regisztráció során a Microsoft Identity platform alapvető információkat küldhet a felhasználóról, így előre kitöltheti a regisztrációs űrlapot, vagy teljesen megszüntetheti a regisztrációt. A felhasználók az Azure AD-fiókjával regisztrálhatnak az alkalmazásra, mint a közösségi médiában és a mobil alkalmazásokban található, ismerős hozzájárulással. Bármely felhasználó regisztrálhat, és bejelentkezhet egy olyan alkalmazásba, amely integrálva van a Microsoft Identity platformmal anélkül, hogy ez bevonása lenne. További információ az [alkalmazás Azure ad-fiókba való bejelentkezésének regisztrálásáról](../../app-service/configure-authentication-provider-aad.md).

### <a name="browse-for-users-manage-user-provisioning-and-control-access-to-your-application"></a>Felhasználók tallózása, felhasználók üzembe helyezésének kezelése és az alkalmazáshoz való hozzáférés szabályozása

**Tallózással keresse meg a címtárban lévő felhasználókat.**  A Microsoft Graph API segítségével a felhasználók megkereshetik és böngészhetik más személyeket a szervezeten belül, amikor meghívja másokat vagy hozzáférést biztosítanak, ahelyett, hogy e-mail-címeket kellene beírniuk. A felhasználók megkereshetik az ismerős címjegyzék stílusának felületét, beleértve a szervezeti hierarchia részletes adatainak megtekintését is. További információ a [Microsoft Graph API](/graph/overview)-ról.

**Active Directory csoportok és terjesztési listák újbóli használata az ügyfél már felügyelet alatt áll.**  Az Azure AD azokat a csoportokat tartalmazza, amelyeket az ügyfél már használ az e-mailek terjesztéséhez és a hozzáférések kezeléséhez. A Microsoft Graph API használatával használja újra ezeket a csoportokat ahelyett, hogy az ügyfélnek külön csoportokat kellene létrehoznia és kezelnie az alkalmazásban. A csoport adatai a bejelentkezési jogkivonatokban is elküldhetők az alkalmazásnak. További információ a [Microsoft Graph API](/graph/overview)-ról.

**A Microsoft Identity platform segítségével szabályozhatja, hogy ki férhet hozzá az alkalmazáshoz.**  Az Azure AD-rendszergazdák és az alkalmazások tulajdonosai az adott felhasználókhoz és csoportokhoz is hozzárendelhet hozzáférést az alkalmazásokhoz. A Microsoft Graph API használatával elolvashatja ezt a listát, és használhatja az erőforrások kiépítés, valamint az alkalmazáson belüli hozzáférés kiépítés szabályozására.

**Használja a Microsoft Identity platformot a szerepkörökön alapuló Access Control.**  A rendszergazdák és az alkalmazások tulajdonosai felhasználókat és csoportokat rendelhetnek hozzá az alkalmazás Microsoft Identity platformon való regisztrálása során meghatározott szerepkörökhöz. A szerepkör-információkat a rendszer a bejelentkezési jogkivonatokban elküldi az alkalmazásnak, és a Microsoft Graph API használatával is olvasható. További információ [a Microsoft Identity platform használatáról az engedélyezéshez](https://cloudblogs.microsoft.com/enterprisemobility/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles/).

### <a name="get-access-to-users-profile-calendar-email-contacts-files-and-more"></a>Hozzáférés a felhasználók profiljához, a naptárhoz, az e-mailekhez, a névjegyekhez és a fájlokhoz

**A Microsoft Identity platform a Microsoft 365 és más Microsoft üzleti szolgáltatások engedélyezési kiszolgálója.**  Ha támogatja a Microsoft Identity platformot, hogy bejelentkezzen az alkalmazásba, vagy támogassa a jelenlegi felhasználói fiókjainak az Azure AD felhasználói fiókjaihoz való összekapcsolását a OAuth 2,0 használatával, olvasási és írási hozzáférést kérhet a felhasználó profiljához, naptárához, e-mailekhez, névjegyekhez, fájlokhoz és egyéb információkhoz. Az eseményeket zökkenőmentesen is megírhatja a felhasználó naptárába, és fájlokat olvashat vagy írhat a OneDrive. További információ [a Microsoft 365 API](/graph/overview)-król.

### <a name="promote-your-application-in-the-azure-and-microsoft-365-marketplaces"></a>Népszerűsítse alkalmazását az Azure-ban és Microsoft 365 piactéren

**Népszerűsítse alkalmazását az Azure AD-t már használó több millió szervezet számára.**  Azok a felhasználók, akik ezeket a piactéreket keresik és böngészhetik, már egy vagy több felhőalapú szolgáltatást használnak, így azok a felhőalapú szolgáltatás ügyfelei számára is elérhetővé válnak. További információ az alkalmazás előléptetéséről [Az Azure Marketplace](https://azure.microsoft.com/marketplace/partner-program/)-en.

**Amikor a felhasználók regisztrálnak az alkalmazásra, az megjelenik az Azure AD hozzáférési paneljén, és Microsoft 365 az App launcherben.**  A felhasználók a későbbiekben gyorsan és egyszerűen visszatérhetnek az alkalmazáshoz, ami javítja a felhasználói szerepvállalást. További információ az [Azure ad hozzáférési paneljéről](../user-help/my-apps-portal-end-user-access.md).

### <a name="secure-device-to-service-and-service-to-service-communication"></a>Biztonságos eszköz-szolgáltatás és szolgáltatások közötti kommunikáció

**A Microsoft Identity platform használata a szolgáltatások és eszközök személyazonosságának kezeléséhez csökkenti az íráshoz szükséges kódot, és lehetővé teszi a hozzáférés kezelését.**  A szolgáltatások és eszközök megkapják a Microsoft Identity platform jogkivonatait az OAuth használatával, és ezeket a jogkivonatokat használják a webes API-k eléréséhez. A Microsoft Identity platform használatával elkerülhető az összetett hitelesítési kód írása. Mivel a szolgáltatások és az eszközök identitása az Azure AD-ben van tárolva, a kulcsokat és a visszavonást egy helyen kezelheti ahelyett, hogy ezt külön kellene elvégeznie az alkalmazásban.

## <a name="benefits-of-integration"></a>Az integráció előnyei

A Microsoft Identity platformmal való integráció olyan előnyökkel jár, amelyek nem igénylik további kódok megírását.

### <a name="integration-with-enterprise-identity-management"></a>Integráció a vállalati identitások kezelésével

**Segítsen az alkalmazásnak az informatikai szabályzatoknak való megfelelésben.**  A szervezetek a Microsoft Identity platformmal integrálják a vállalati identitás-felügyeleti rendszereiket, így ha egy személy elhagyja a szervezetet, automatikusan elveszíti az alkalmazáshoz való hozzáférést anélkül, hogy további lépéseket kellene elvégeznie. Felügyelheti, hogy ki férhet hozzá az alkalmazáshoz, és meghatározhatja, hogy milyen hozzáférési házirendekre van szükség – például a többtényezős hitelesítésnél –, amely csökkenti az összetett vállalati házirendeknek való megfeleléshez szükséges kód írását. Az Azure AD részletes naplót biztosít a rendszergazdáknak, hogy kik bejelentkezett az alkalmazásba, hogy nyomon kövessék a használatot.

**Az Azure AD kiterjeszti Active Directory a felhőbe, hogy az alkalmazás integrálható legyen az AD-vel.**  A világ számos szervezete a Active Directory elsődleges bejelentkezési és Identitáskezelés-felügyeleti rendszereként használja, és megköveteli, hogy alkalmazásaikat az AD-vel működjenek. Az Azure AD-val való integráció az alkalmazást Active Directorytel integrálja.

### <a name="advanced-security-features"></a>Speciális biztonsági funkciók

**Multi-Factor Authentication.**  A Microsoft Identity platform natív többtényezős hitelesítést biztosít. A rendszergazdák megkövetelhetik a többtényezős hitelesítés használatát az alkalmazáshoz való hozzáféréshez, így Önnek nem kell ezt a támogatást leírnia. További információ a [multi-Factor Authenticationról](https://azure.microsoft.com/documentation/services/multi-factor-authentication/).

**Rendellenes bejelentkezési észlelés.**  A Microsoft Identity platform naponta több mint egy milliárd bejelentkezést dolgoz fel, a gépi tanulási algoritmusok használatával pedig észlelheti a gyanús tevékenységeket, és értesíti a rendszergazdákat a lehetséges problémákról. A Microsoft Identity platform bejelentkezésének támogatásával az alkalmazás megkapja a védelem előnyeit. További információ a [Azure Active Directory Access-jelentés megtekintéséről](../reports-monitoring/overview-reports.md).

**Feltételes hozzáférés.**  A többtényezős hitelesítésen kívül a rendszergazdáknak bizonyos feltételek teljesülése szükséges ahhoz, hogy a felhasználók bejelentkezhetnek az alkalmazásba. A beállítható feltételek közé tartozik az ügyféleszközök IP-címtartomány, a megadott csoportok tagsága és a hozzáféréshez használt eszköz állapota. További információ a [Azure Active Directory feltételes hozzáférésről](../conditional-access/overview.md).

### <a name="easy-development"></a>Könnyű fejlesztés

**Iparági szabványnak megfelelő protokollok.**  A Microsoft elkötelezett az iparági szabványok támogatásában. A Microsoft Identity platform támogatja az iparági szabványnak megfelelő OAuth 2,0 és az OpenID Connect 1,0 protokollokat. További információ a [Microsoft Identity platform hitelesítési protokolljairól](active-directory-v2-protocols.md).

**Nyílt forráskódú kódtárak.**  A Microsoft teljes körűen támogatott nyílt forráskódú kódtárakat biztosít népszerű nyelvekhez és platformokhoz a gyorsabb fejlesztés érdekében. A forráskód az Apache 2,0 alatt van, és Ön szabadon elvégezheti az elágazást, és hozzájárulhat a projektekhez. További információ a [Microsoft Authentication Library (MSAL)](reference-v2-libraries.md)szolgáltatásról.

### <a name="worldwide-presence-and-high-availability"></a>Globális jelenlét és magas rendelkezésre állás

**Az Azure AD a világ különböző pontjain üzemelő adatközpontokban van üzembe helyezve, és az óra körül felügyelhető és figyelhető.**  Az Azure AD a Microsoft Azure és a Microsoft 365 Identity Management rendszer, amely a világ 28 adatközpontjában üzemel. A címtáradatok legalább három adatközpontba való replikálásra vannak garantálva. A globális Load Balancer biztosítja, hogy a felhasználók hozzáférjenek az Azure AD legközelebbi példányához, amely tartalmazza az adatmennyiséget, és ha problémát észlel, automatikusan átirányítja a kéréseket más adatközpontokhoz.

## <a name="next-steps"></a>Következő lépések

A [kód írásának megkezdése](v2-overview.md#getting-started).

[Felhasználók aláírása a Microsoft Identity platform használatával](./authentication-vs-authorization.md)