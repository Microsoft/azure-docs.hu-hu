---
title: Hitelesítési módszerek és szolgáltatások – Azure Active Directory
description: A bejelentkezési események javításának és biztonságossá Azure Active Directory hitelesítési módszereinek és funkcióinak megismerése
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
ms.openlocfilehash: b4d69157f4544daad962cca15e53802e7b912399
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530429"
---
# <a name="what-authentication-and-verification-methods-are-available-in-azure-active-directory"></a>Milyen hitelesítési és ellenőrzési módszerek érhetők el az Azure Active Directoryban?

A felhasználói fiókok (Azure AD) bejelentkezési élményének Azure Active Directory különböző módokon hitelesítheti magát a felhasználó. A felhasználó a leggyakrabban felhasználónévvel és jelszóval adja meg a hitelesítő adatokat. Az Azure AD modern hitelesítési és biztonsági funkcióival az egyszerű jelszót ki kell egészíteni vagy biztonságosabb hitelesítési módszerekkel kell helyettesíteni.

![Az Azure AD erősségeinek és előnyben részesített hitelesítési módszereinek táblázata](media/concept-authentication-methods/authentication-methods.png)

A jelszó nélküli hitelesítési módszerek Windows Hello a Windows Hello, a FIDO2 biztonsági kulcsok és a Microsoft Authenticator az alkalmazás biztosítja a legbiztonságosabb bejelentkezési eseményeket.

Az Azure AD Multi-Factor Authentication (MFA) további biztonságot biztosít a jelszó használata felett, amikor egy felhasználó bejelentkezik. A felhasználótól további hitelesítési formákat is be lehet kérni, például hogy válaszoljon egy leküldéses értesítésre, adjon meg egy kódot egy szoftverből vagy hardvertokenből, vagy válaszoljon egy SMS-t vagy telefonhívást.

A felhasználói regisztrációs élmény egyszerűsítése és az MFA és az önkiszolgáló jelszó-visszaállítás (SSPR) regisztrálása érdekében javasoljuk, hogy engedélyezze a kombinált biztonsági információk [regisztrációját.](howto-registration-mfa-sspr-combined.md) A rugalmasság érdekében javasoljuk, hogy a felhasználóknak több hitelesítési módszert is regisztrálnia kell. Ha az egyik módszer nem érhető el a felhasználó számára a bejelentkezés vagy az SSPR során, dönthet úgy, hogy egy másik módszerrel hitelesíti magát. További információ: [Rugalmas hozzáférés-vezérlési stratégia létrehozása az Azure AD-ben.](concept-resilient-controls.md)

Az alábbi videó [segít](https://www.youtube.com/watch?v=LB2yj4HSptc&feature=youtu.be) kiválasztani a legjobb hitelesítési módszert a szervezet biztonságának érdekében.

## <a name="authentication-method-strength-and-security"></a>Hitelesítési módszer erőssége és biztonsága

Amikor olyan szolgáltatásokat helyez üzembe a szervezetében, mint az Azure AD Multi-Factor Authentication, tekintse át az elérhető hitelesítési módszereket. Válassza ki a biztonsági, használhatósági és rendelkezésre állási szempontból a követelményeknek megfelelő vagy azt meghaladó módszereket. Ahol lehetséges, használjon a legmagasabb szintű biztonsággal rendelkező hitelesítési módszereket.

Az alábbi táblázat az elérhető hitelesítési módszerek biztonsági szempontjait ismerteti. A rendelkezésre állás azt jelzi, hogy a felhasználó a hitelesítési módszert használhatja, nem pedig az Azure AD-szolgáltatás rendelkezésre állását:

| Hitelesítési módszer          | Biztonság | Használhatóság | Rendelkezésre állás |
|--------------------------------|:--------:|:---------:|:------------:|
| Vállalati Windows Hello     | Magas     | Magas      | Magas         |
| A Microsoft Authenticator alkalmazás    | Magas     | Magas      | Magas         |
| FIDO2 biztonsági kulcs             | Magas     | Magas      | Magas         |
| OATH hardveres jogkivonatok (előzetes verzió) | Közepes   | Közepes    | Magas         |
| OATH szoftvertokenek           | Közepes   | Közepes    | Magas         |
| SMS                            | Közepes   | Magas      | Közepes       |
| Hang                          | Közepes   | Közepes    | Közepes       |
| Jelszó                       | Alacsony      | Magas      | Magas         |

A biztonsággal kapcsolatos legfrissebb információkért tekintse meg blogbejegyzésünket:

- [Itt az ideje, hogy lefagyasztsa a telefonos szállítást a hitelesítéshez](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/it-s-time-to-hang-up-on-phone-transports-for-authentication/ba-p/1751752)
- [Hitelesítési biztonsági rések és támadási vektorok](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124)

> [!TIP]
> A rugalmasság és a használhatóság érdekében javasoljuk, hogy használja a Microsoft Authenticator alkalmazást. Ez a hitelesítési módszer biztosítja a legjobb felhasználói élményt és többféle módot, például jelszó nélküli, MFA leküldéses értesítéseket és OATH-kódokat.

## <a name="how-each-authentication-method-works"></a>Az egyes hitelesítési módszerek működése

Egyes hitelesítési módszerek elsődleges tényezőként használhatók az alkalmazásokba vagy eszközökre való bejelentkezéskor, például EGY FIDO2 biztonsági kulcs vagy jelszó használatával. Más hitelesítési módszerek csak másodlagos tényezőként érhetők el az Azure AD Multi-Factor Authentication vagy az SSPR használata esetén.

Az alábbi táblázat ismerteti, hogy mikor használható hitelesítési módszer a bejelentkezési események során:

| Metódus                         | Elsődleges hitelesítés | Másodlagos hitelesítés  |
|--------------------------------|:----------------------:|:-------------------------:|
| Vállalati Windows Hello     | Igen                    | MFA                       |
| A Microsoft Authenticator alkalmazás    | Igen                    | MFA és SSPR              |
| FIDO2 biztonsági kulcs             | Igen                    | MFA                       |
| OATH hardveres jogkivonatok (előzetes verzió) | Nem                     | MFA                       |
| OATH szoftvertokenek           | Nem                     | MFA                       |
| SMS                            | Igen                    | MFA és SSPR              |
| Hanghívás                     | Nem                     | MFA és SSPR              |
| Jelszó                       | Igen                    |                           |

Ezek a hitelesítési módszerek mind konfigurálhatóak a Azure Portal, és egyre inkább a [Microsoft Graph REST API.](/graph/api/resources/authenticationmethods-overview)

Ha többet szeretne megtudni az egyes hitelesítési módszerekről, tekintse meg a következő különálló elméleti cikkeket:

* [Vállalati Windows Hello](/windows/security/identity-protection/hello-for-business/hello-overview)
* [A Microsoft Authenticator alkalmazás](concept-authentication-authenticator-app.md)
* [FIDO2 biztonsági kulcs](concept-authentication-passwordless.md#fido2-security-keys)
* [OATH hardvertokenek (előzetes verzió)](concept-authentication-oath-tokens.md#oath-hardware-tokens-preview)
* [OATH szoftvertokenek](concept-authentication-oath-tokens.md#oath-software-tokens)
* [SMS-bejelentkezés és](howto-authentication-sms-signin.md) [ellenőrzés](concept-authentication-phone-options.md#mobile-phone-verification)
* [Hanghívások ellenőrzése](concept-authentication-phone-options.md)
* Jelszó

> [!NOTE]
> Az Azure AD-ban a jelszó gyakran az egyik elsődleges hitelesítési módszer. A jelszó-hitelesítési módszer nem tiltható le. Ha jelszót használ elsődleges hitelesítési tényezőként, növelje a bejelentkezési események biztonságát az Azure AD Multi-Factor Authentication használatával.

Bizonyos esetekben a következő további ellenőrzési módszerek használhatók:

* [Alkalmazásjelszavak](howto-mfa-app-passwords.md) – olyan régi alkalmazásokhoz használatos, amelyek nem támogatják a modern hitelesítést, és felhasználónkénti Azure AD Multi-Factor Authenticationre konfigurálhatóak.
* [Biztonsági kérdések](concept-authentication-security-questions.md) – csak SSPR-hez használatos
* [E-mail-cím](concept-sspr-howitworks.md#authentication-methods) – csak az SSPR-hez használatos

## <a name="next-steps"></a>Következő lépések

Első lépésekért tekintse meg az önkiszolgáló [jelszó-visszaállítás (SSPR)][tutorial-sspr] és az [Azure AD Multi-Factor Authentication oktatóanyagát.][tutorial-azure-mfa]

Az SSPR-fogalmakkal kapcsolatos további információkért lásd: [Az Azure AD önkiszolgáló jelszóát állításának működése.][concept-sspr]

További információ az MFA-fogalmakról: [Az Azure AD Multi-Factor Authentication működése.][concept-mfa]

További információ a hitelesítési módszerek konfigurálásról a [Microsoft Graph REST API.](/graph/api/resources/authenticationmethods-overview)

A használt hitelesítési módszerek áttekintéséhez lásd: [Azure AD Multi-Factor Authentication](/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/)hitelesítési módszer elemzése a PowerShell használatával.

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
