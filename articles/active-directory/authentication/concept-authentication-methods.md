---
title: Hitelesítési módszerek és szolgáltatások – Azure Active Directory
description: További információ a Azure Active Directoryban elérhető különböző hitelesítési módszerekről és szolgáltatásokról a bejelentkezési események javításához és biztonságossá tételéhez
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: ee10aa7c461aca65f385c735f6e9aaa28af7f9e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103471693"
---
# <a name="what-authentication-and-verification-methods-are-available-in-azure-active-directory"></a>Milyen hitelesítési és ellenőrzési módszerek érhetők el az Azure Active Directoryban?

A Azure Active Directory (Azure AD) fiókjaihoz tartozó bejelentkezési élmény részeként különböző módokon végezheti el a felhasználók hitelesítését. A Felhasználónév és a jelszó a leggyakoribb módszer a felhasználók számára a hitelesítő adatok megadására. Az Azure AD modern hitelesítési és biztonsági funkcióival az alapszintű jelszót a biztonságosabb hitelesítési módszerekkel kell kiegészíteni vagy helyettesíteni.

![Az Azure AD erősségeit és előnyben részesített hitelesítési módszereinek táblázata](media/concept-authentication-methods/authentication-methods.png)

A jelszó nélküli hitelesítési módszerek, például a Windows Hello, a FIDO2 biztonsági kulcsok és a Microsoft Authenticator alkalmazás biztosítja a legbiztonságosabb bejelentkezési eseményeket.

Az Azure AD Multi-Factor Authentication (MFA) a felhasználó bejelentkezésekor csak jelszó használatával ad további biztonságot. A felhasználónak további hitelesítési formákat kell megadnia, például egy leküldéses értesítésre való reagáláshoz, egy szoftver-vagy hardver-jogkivonat kódjának megadásához, illetve az SMS-vagy telefonhívások megválaszolásához.

Javasoljuk, hogy egyszerűsítse a felhasználói felületi élményt, és regisztráljon mind az MFA-, mind az önkiszolgáló jelszó-visszaállítás (SSPR) számára, javasoljuk, hogy [engedélyezze a kombinált biztonsági információk regisztrálását](howto-registration-mfa-sspr-combined.md). A rugalmasság érdekében javasoljuk, hogy a felhasználóknak több hitelesítési módszert kell regisztrálniuk. Ha egy felhasználó nem érhető el a bejelentkezés vagy a SSPR során, dönthet úgy, hogy egy másik metódussal végez hitelesítést. További információ: [rugalmas hozzáférés-vezérlési kezelési stratégia létrehozása az Azure ad-ben](concept-resilient-controls.md).

Itt talál egy [videót](https://www.youtube.com/watch?v=LB2yj4HSptc&feature=youtu.be) , amely segít kiválasztani a legmegfelelőbb hitelesítési módszert a szervezet biztonságának megőrzéséhez.

## <a name="authentication-method-strength-and-security"></a>Hitelesítési módszer erőssége és biztonsága

Ha olyan szolgáltatásokat telepít, mint például az Azure AD Multi-Factor Authentication a szervezetben, tekintse át az elérhető hitelesítési módszereket. Válassza ki azokat a módszereket, amelyek megfelelnek a követelményeknek, vagy meghaladják a biztonságot, a használhatóságot és a rendelkezésre állást. Ha lehetséges, használja a legmagasabb szintű biztonságú hitelesítési módszereket.

Az alábbi táblázat az elérhető hitelesítési módszerek biztonsági szempontjait ismerteti. A rendelkezésre állás azt jelzi, hogy a felhasználó használhatja-e a hitelesítési módszert, nem a szolgáltatás rendelkezésre állását az Azure AD-ben:

| Hitelesítési módszer          | Biztonság | Használhatóság | Rendelkezésre állás |
|--------------------------------|:--------:|:---------:|:------------:|
| Vállalati Windows Hello     | Magas     | Magas      | Magas         |
| A Microsoft Authenticator alkalmazás    | Magas     | Magas      | Magas         |
| FIDO2 biztonsági kulcs             | Magas     | Magas      | Magas         |
| A hardver-tokenek ESKÜje (előzetes verzió) | Közepes   | Közepes    | Magas         |
| Az eskü szoftver jogkivonatai           | Közepes   | Közepes    | Magas         |
| SMS                            | Közepes   | Magas      | Közepes       |
| Hang                          | Közepes   | Közepes    | Közepes       |
| Jelszó                       | Alacsony      | Magas      | Magas         |

A biztonsággal kapcsolatos legújabb információkért tekintse meg a blogbejegyzéseket:

- [Itt az ideje, hogy letegye a telefonos átvitelt a hitelesítéshez](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/it-s-time-to-hang-up-on-phone-transports-for-authentication/ba-p/1751752)
- [Hitelesítési sebezhetőségek és támadási vektorok](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124)

> [!TIP]
> A rugalmasság és a használhatóság érdekében javasoljuk, hogy használja a Microsoft Authenticator alkalmazást. Ez a hitelesítési módszer a legjobb felhasználói élményt és több módot kínál, például a jelszavakat, az MFA leküldéses értesítéseit és az eskü-kódokat.

## <a name="how-each-authentication-method-works"></a>Az egyes hitelesítési módszerek működése

Egyes hitelesítési módszerek elsődleges tényezőként használhatók egy alkalmazásba vagy eszközre való bejelentkezéskor, például FIDO2 biztonsági kulcs vagy jelszó használatával. Más hitelesítési módszerek csak másodlagos tényezőként érhetők el az Azure AD Multi-Factor Authentication vagy SSPR használata esetén.

A következő táblázat azt ismerteti, hogy mikor lehet hitelesítési módszert használni a bejelentkezési esemény során:

| Metódus                         | Elsődleges hitelesítés | Másodlagos hitelesítés  |
|--------------------------------|:----------------------:|:-------------------------:|
| Vállalati Windows Hello     | Yes                    | MFA                       |
| A Microsoft Authenticator alkalmazás    | Yes                    | MFA és SSPR              |
| FIDO2 biztonsági kulcs             | Yes                    | MFA                       |
| A hardver-tokenek ESKÜje (előzetes verzió) | No                     | MFA                       |
| Az eskü szoftver jogkivonatai           | No                     | MFA                       |
| SMS                            | Yes                    | MFA és SSPR              |
| Hanghívás                     | No                     | MFA és SSPR              |
| Jelszó                       | Yes                    |                           |

Az összes hitelesítési módszer konfigurálható a Azure Portalban, és egyre inkább a [Microsoft Graph REST API Beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta)használatával.

Ha többet szeretne megtudni az egyes hitelesítési módszerek működéséről, tekintse meg a következő külön fogalmi cikkeket:

* [Vállalati Windows Hello](/windows/security/identity-protection/hello-for-business/hello-overview)
* [A Microsoft Authenticator alkalmazás](concept-authentication-authenticator-app.md)
* [FIDO2 biztonsági kulcs](concept-authentication-passwordless.md#fido2-security-keys)
* [A hardver-tokenek ESKÜje (előzetes verzió)](concept-authentication-oath-tokens.md#oath-hardware-tokens-preview)
* [Az eskü szoftver jogkivonatai](concept-authentication-oath-tokens.md#oath-software-tokens)
* [SMS-bejelentkezés](howto-authentication-sms-signin.md) és- [ellenőrzés](concept-authentication-phone-options.md#mobile-phone-verification)
* [Telefonhívás ellenőrzése](concept-authentication-phone-options.md)
* Jelszó

> [!NOTE]
> Az Azure AD-ben a jelszó gyakran az egyik elsődleges hitelesítési módszer. A jelszó-hitelesítési módszer nem tiltható le. Ha az elsődleges hitelesítési tényezőként jelszót használ, növelje a bejelentkezési események biztonságát az Azure AD Multi-Factor Authentication használatával.

A következő további ellenőrzési módszerek használhatók bizonyos helyzetekben:

* [Alkalmazás jelszavai](howto-mfa-app-passwords.md) – olyan régi alkalmazásokhoz használható, amelyek nem támogatják a modern hitelesítést, és konfigurálhatók FELHASZNÁLÓNKÉNTI Azure AD-multi-Factor Authentication.
* [Biztonsági kérdések](concept-authentication-security-questions.md) – csak a SSPR esetében használatos
* [E-mail-cím](concept-sspr-howitworks.md#authentication-methods) – csak a SSPR esetében használatos

## <a name="next-steps"></a>Következő lépések

Az első lépésekhez tekintse meg az [önkiszolgáló jelszó-visszaállítás (SSPR)][tutorial-sspr] és az [Azure ad multi-Factor Authentication][tutorial-azure-mfa]oktatóanyagát.

További információ a SSPR fogalmakról: az [Azure ad önkiszolgáló jelszó-visszaállításának működése][concept-sspr].

További információ az MFA-fogalmakról: [Hogyan működik az Azure AD multi-Factor Authentication][concept-mfa].

További információ a hitelesítési módszerek konfigurálásáról a [Microsoft Graph REST API Beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta)használatával.

A használt hitelesítési módszerek áttekintését lásd: [Azure AD multi-Factor Authentication hitelesítési módszer elemzése a PowerShell](/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/)-lel.

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
