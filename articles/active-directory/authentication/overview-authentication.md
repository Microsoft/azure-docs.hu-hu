---
title: Azure Active Directory hitelesítés áttekintése
description: Ismerje meg a felhasználói bejelentkezések különböző hitelesítési módszereit és biztonsági funkcióit a Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: overview
ms.date: 01/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: sahenry, michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6f4659b9ee809cc1f1caeb1cb9c0d626b1b3a3b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98725654"
---
# <a name="what-is-azure-active-directory-authentication"></a>Mi az Azure Active Directory hitelesítés?

Az Identity platform egyik fő funkciója a hitelesítő adatok ellenőrzése vagy *hitelesítése*, amikor a felhasználó bejelentkezik egy eszközre, alkalmazásba vagy szolgáltatásba. Azure Active Directory (Azure AD) esetében a hitelesítés több, mint a Felhasználónév és a jelszó ellenőrzése. Az Azure AD-hitelesítés a következő összetevőket tartalmazza, így javíthatja a biztonságot, és csökkentheti az ügyfélszolgálati segítség szükségességét:

* Új jelszó önkiszolgáló kérése
* Azure AD többtényezős hitelesítés
* Hibrid integráció a jelszó-változtatások helyi környezetbe való visszaírásához
* Hibrid integráció egy helyi környezet jelszavas védelmi házirendjeinek betartatásához
* Jelszó nélküli hitelesítés

Tekintse meg a rövid videót, amely további információkat nyújt ezekről a hitelesítési összetevőkről.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4KVJA]

## <a name="improve-the-end-user-experience"></a>A végfelhasználói élmény javítása

Az Azure AD segítségével megvédheti a felhasználók identitását, és egyszerűsítheti a bejelentkezési élményt. Az önkiszolgáló jelszó-visszaállításhoz hasonló funkciók lehetővé teszik a felhasználók számára, hogy bármely eszközről webböngészővel frissítsenek vagy módosítsák a jelszavukat. Ez a funkció különösen akkor hasznos, ha a felhasználó elfelejtette a jelszavát, vagy a fiókja zárolva van. Anélkül, hogy az ügyfélszolgálatnak vagy a rendszergazdának támogatást kellene nyújtania, a felhasználók letilthatják magukat, és folytatják a munkát.

Az Azure AD Multi-Factor Authentication lehetővé teszi a felhasználók számára, hogy a bejelentkezés során további hitelesítési formát válasszák, például telefonhívást vagy Mobile apps-értesítést. Ez a képesség csökkenti a másodlagos hitelesítés (például a hardver jogkivonata) egyetlen, rögzített formáját. Ha a felhasználó jelenleg nem rendelkezik egy további hitelesítéssel, választhat másik módszert, és folytathatja a munkát.

![A bejelentkezési képernyőn használt hitelesítési módszerek](media/concept-authentication-methods/overview-login.png)

A jelszóval nem rendelkező hitelesítéssel nem szükséges, hogy a felhasználó a biztonságos jelszavakat egyáltalán ne hozzon létre és jegyezze fel. A Windows Hello for Business vagy a FIDO2 biztonsági kulcsokkal rendelkező funkciók lehetővé teszik a felhasználók számára, hogy jelszó nélkül jelentkezzenek be egy eszközre vagy alkalmazásba. Ez a képesség csökkentheti a jelszavak különböző környezetekben való kezelésének bonyolultságát.

## <a name="self-service-password-reset"></a>Új jelszó önkiszolgáló kérése

Az önkiszolgáló jelszó-visszaállítás lehetővé teszi a felhasználóknak a jelszavuk módosítását vagy alaphelyzetbe állítását, rendszergazdai vagy ügyfélszolgálati beavatkozás nélkül. Ha a felhasználó fiókja zárolva van, vagy elfelejti a jelszavát, akkor az utasításokat követve letilthatja önmagát, és visszatérhet a munkához. Ez a funkció csökkenti az ügyfélszolgálati hívásokat és a termelékenység elvesztését, ha a felhasználó nem tud bejelentkezni az eszközére vagy alkalmazására.

Az önkiszolgáló jelszó-visszaállítás a következő esetekben működik:

* **Jelszó módosítása –** ha egy felhasználó ismeri a jelszavát, de módosítani szeretné valami újat.
* **Jelszó alaphelyzetbe állítása –** ha a felhasználó nem tud bejelentkezni, például ha elfelejtette a jelszót, és szeretné alaphelyzetbe állítani a jelszavát.
* **Fiók zárolásának feloldása –** ha a felhasználó nem tud bejelentkezni, mert a fiókja zárolva van, és fel szeretné oldani a fiók zárolását.

Amikor egy felhasználó az önkiszolgáló jelszó-visszaállítási szolgáltatással frissíti vagy visszaállítja a jelszavát, a jelszó a helyszíni Active Directory környezetbe is visszaírható. A jelszó visszaírási gondoskodik arról, hogy a felhasználók azonnal használhassák a frissített hitelesítő adataikat a helyszíni eszközök és alkalmazások segítségével.

## <a name="azure-ad-multi-factor-authentication"></a>Azure AD többtényezős hitelesítés

A többtényezős hitelesítés egy olyan folyamat, amelyben a rendszer a bejelentkezési folyamat során felkéri a felhasználót egy másodlagos azonosításra, például hogy adjon meg egy kódot a mobiltelefonján, vagy végezzen ujjlenyomat-vizsgálatot.

Ha csak jelszó használatával hitelesíti a felhasználót, a támadás nem biztonságos vektort hagy. Ha a jelszó gyenge vagy máshol van kitéve, valóban a felhasználó bejelentkezik a felhasználónévvel és a jelszóval, vagy egy támadó? Ha a hitelesítés második formáját igényli, a biztonság megnövekszik, mivel ez a további tényező nem a támadók számára könnyen beszerezhető vagy duplikálható.

![A multi-Factor Authentication különböző formáinak fogalmi képe](./media/concept-mfa-howitworks/methods.png)

Az Azure AD Multi-Factor Authentication úgy működik, hogy a következő hitelesítési módszerek közül kettőt vagy többet igényel:

* Amit ismer, általában egy jelszó.
* Valami, például egy nem könnyen duplikált megbízható eszköz, például telefon vagy hardver kulcsa.
* A biometrikus adatok például ujjlenyomatok vagy Arcfelismerés.

A felhasználók az önkiszolgáló jelszó-visszaállítási és az Azure AD-Multi-Factor Authentication is regisztrálhatják, hogy leegyszerűsítsék a helyszíni élményt. A rendszergazdák meghatározhatják, hogy milyen típusú másodlagos hitelesítést lehet használni. Az Azure AD Multi-Factor Authentication akkor is megkövetelhető, ha a felhasználók önkiszolgáló jelszó-visszaállítást végeznek a folyamat további biztonságosabbá tételéhez.

## <a name="password-protection"></a>Jelszavas védelem

Alapértelmezés szerint az Azure AD blokkolja a gyenge jelszavakat, például a *jelszó1*. A rendszer automatikusan frissíti és kikényszeríti a globálisan tiltott jelszavak listáját, amely az ismert gyenge jelszavakat is tartalmazza. Ha egy Azure AD-felhasználó megpróbálja beállítani a jelszavukat a gyenge jelszavak egyikére, a rendszer értesítést küld a biztonságosabb jelszó kiválasztásáról.

A biztonság fokozása érdekében egyéni jelszavas védelmi házirendeket adhat meg. Ezek a házirendek szűrők használatával letilthatják az olyan jelszavak bármely változatát, mint például a *contoso* vagy egy olyan hely, mint például a *London*.

A hibrid biztonság érdekében a helyi Active Directory környezettel integrálhatja az Azure AD jelszavas védelmét. A helyszíni környezetben telepített összetevők megkapják a globálisan tiltott jelszavak listáját és az egyéni jelszavas védelmet az Azure AD-ből, a tartományvezérlők pedig a jelszó-módosítási események feldolgozására használják őket. Ez a hibrid megközelítés gondoskodik arról, hogy a felhasználók hogyan vagy hol változtatják meg a hitelesítő adataikat, így kényszeríti az erős jelszavak használatát.

## <a name="passwordless-authentication"></a>Jelszó nélküli hitelesítés

Számos környezet esetében a cél a jelszavak használatának megszüntetése a bejelentkezési események részeként. A szolgáltatások, például az Azure Password Protection vagy az Azure AD Multi-Factor Authentication segítenek a biztonság javításában, de a Felhasználónév és a jelszó a hitelesítés gyenge formája marad, amely megtámadható vagy találgatásos lehet.

![Biztonság és kényelem a jelszóval nem rendelkező hitelesítési folyamattal](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

Ha jelszó nélküli metódussal jelentkezik be, a hitelesítő adatok olyan metódusok használatával érhetők el, mint a biometria és a vállalati Windows Hello, vagy egy FIDO2 biztonsági kulcs. Ezeket a hitelesítési módszereket nem lehet könnyen duplikálni egy támadó.

Az Azure AD lehetővé teszi, hogy natív módon hitelesítse a jelszavak nélküli módszerekkel, így egyszerűbbé téve a felhasználók bejelentkezési élményét, és csökkentheti a támadások kockázatát.

## <a name="next-steps"></a>Következő lépések

Az első lépésekhez tekintse meg az [önkiszolgáló jelszó-visszaállítás (SSPR)][tutorial-sspr] és az [Azure ad multi-Factor Authentication][tutorial-azure-mfa]oktatóanyagát.

Az önkiszolgáló jelszó-visszaállítási fogalmakkal kapcsolatos további tudnivalókért tekintse meg az [Azure ad önkiszolgáló jelszó-visszaállítás működéséről][concept-sspr]szóló témakört.

Ha többet szeretne megtudni a többtényezős hitelesítési fogalmakról, tekintse meg az [Azure AD multi-Factor Authentication működését][concept-mfa]ismertető témakört.

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
