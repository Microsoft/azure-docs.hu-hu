---
title: Az Azure AD Multi-Factor Authentication áttekintése
description: Ismerje meg, hogyan segít az Azure AD Multi-Factor Authentication az adatkezeléshez és az alkalmazásokhoz való hozzáférés biztosításában, miközben egy egyszerű bejelentkezési folyamatra vonatkozó felhasználói igényeket is kielégít.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1cc5227cb4b5de02cba65a60dd469da93d3767e5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96744057"
---
# <a name="how-it-works-azure-ad-multi-factor-authentication"></a>A működés módja: Azure AD Multi-Factor Authentication

A többtényezős hitelesítés egy olyan folyamat, amelyben a rendszer a bejelentkezési folyamat során felkéri a felhasználót egy másodlagos azonosításra, például hogy adjon meg egy kódot a mobiltelefonján, vagy végezzen ujjlenyomat-vizsgálatot.

Ha csak jelszó használatával hitelesíti a felhasználót, a támadás nem biztonságos vektort hagy. Ha a jelszó gyenge vagy máshol van kitéve, valóban a felhasználó bejelentkezik a felhasználónévvel és a jelszóval, vagy egy támadó? Ha a hitelesítés második formáját igényli, a biztonság megnövekszik, mivel ez a további tényező nem a támadók számára könnyen beszerezhető vagy duplikálható.

![A multi-Factor Authentication különböző formáinak fogalmi képe](./media/concept-mfa-howitworks/methods.png)

Az Azure AD Multi-Factor Authentication úgy működik, hogy a következő hitelesítési módszerek közül kettőt vagy többet igényel:

* Amit ismer, általában egy jelszó.
* Valami, például egy nem könnyen duplikált megbízható eszköz, például telefon vagy hardver kulcsa.
* A biometrikus adatok például ujjlenyomatok vagy Arcfelismerés.

A felhasználók az önkiszolgáló jelszó-visszaállítási és az Azure AD-Multi-Factor Authentication is regisztrálhatják, hogy leegyszerűsítsék a helyszíni élményt. A rendszergazdák meghatározhatják, hogy milyen típusú másodlagos hitelesítést lehet használni. Az Azure AD Multi-Factor Authentication akkor is megkövetelhető, ha a felhasználók önkiszolgáló jelszó-visszaállítást végeznek a folyamat további biztonságosabbá tételéhez.

![A bejelentkezési képernyőn használt hitelesítési módszerek](media/concept-authentication-methods/overview-login.png)

Az Azure AD Multi-Factor Authentication segít megőrizni az adathozzáférést és az alkalmazásokat, miközben a felhasználók számára egyszerűséget biztosít. További biztonságot nyújt a hitelesítés második formáját igényli, és erős hitelesítést tesz lehetővé számos könnyen használható [hitelesítési módszer](concept-authentication-methods.md)segítségével. A rendszergazda által létrehozott konfigurációs döntések alapján előfordulhat, hogy a felhasználók nem tudják feltámadni az MFA-t.

Az alkalmazásoknak vagy szolgáltatásoknak nem kell módosítaniuk az Azure AD-Multi-Factor Authentication használatát. Az ellenőrzési kérések az Azure AD bejelentkezési eseményének részét képezik, amely szükség esetén automatikusan kéri és dolgozza fel az MFA-feladatot.

## <a name="available-verification-methods"></a>Rendelkezésre álló ellenőrzési módszerek

Amikor egy felhasználó bejelentkezik egy alkalmazásba vagy szolgáltatásba, és egy MFA-kérést kap, a további ellenőrzésük egyik regisztrált formája közül választhat. A rendszergazda megkövetelheti ezeknek az Azure AD Multi-Factor Authentication ellenőrzési módszereknek a regisztrálását, vagy a felhasználó elérheti a saját [profilját](https://myprofile.microsoft.com) az ellenőrzési módszerek szerkesztéséhez vagy hozzáadásához.

Az Azure AD Multi-Factor Authentication a következő további ellenőrzési formákat használhatja:

* A Microsoft Authenticator alkalmazás
* A hardver-token ESKÜje
* SMS
* Hanghívás

## <a name="how-to-enable-and-use-azure-ad-multi-factor-authentication"></a>Az Azure AD-Multi-Factor Authentication engedélyezése és használata

A felhasználók és csoportok engedélyezhető az Azure AD Multi-Factor Authentication számára, hogy a bejelentkezési esemény során további ellenőrzésre is rákérdezzenek. A [biztonsági beállítások](../fundamentals/concept-fundamentals-security-defaults.md) minden Azure ad-bérlő számára elérhetők, hogy gyorsan engedélyezzék a Microsoft Authenticator alkalmazás használatát az összes felhasználó számára.

Részletesebb szabályozáshoz a [feltételes hozzáférési](../conditional-access/overview.md) szabályzatok az MFA-t igénylő események és alkalmazások definiálására használhatók. Ezek a házirendek lehetővé teszik a rendszeres bejelentkezési eseményeket, ha a felhasználó a vállalati hálózaton vagy egy regisztrált eszközön van, de a távoli vagy a személyes eszközön további ellenőrzési tényezőket kér.

![Áttekintő diagram a feltételes hozzáférés működéséről a bejelentkezési folyamat biztonságossá tételéhez](media/tutorial-enable-azure-mfa/conditional-access-overview.png)

## <a name="next-steps"></a>Következő lépések

A licenceléssel kapcsolatos további tudnivalókért tekintse meg [Az Azure AD multi-Factor Authentication funkcióit és licenceit](concept-mfa-licensing.md).

Az MFA működés közbeni megjelenítéséhez engedélyezze az Azure AD Multi-Factor Authentication a következő oktatóanyagban a tesztelési felhasználók csoportjának:

> [!div class="nextstepaction"]
> [Az Azure AD többtényezős hitelesítés engedélyezése](./tutorial-enable-azure-mfa.md)