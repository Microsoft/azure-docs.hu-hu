---
title: Felhasználói folyamatok és egyéni házirendek a Azure Active Directory B2Cban | Microsoft Docs
titleSuffix: Azure AD B2C
description: További információ a beépített felhasználói folyamatokról és a Azure Active Directory B2C egyéni házirend-bővíthető házirend-keretrendszeréről.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/08/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2e4dbc5178bec3a5b1f0931267465879f604f36f
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226008"
---
# <a name="user-flows-and-custom-policies-overview"></a>Felhasználói folyamatok és egyéni házirendek áttekintése

Azure AD B2Cban megadhatja azt az üzleti logikát, amelyet a felhasználók követnek, hogy hozzáférjenek az alkalmazáshoz. Megadhatja például, hogy a felhasználók milyen lépéseket követnek a bejelentkezéskor, a regisztráció, a profil szerkesztése vagy a jelszó alaphelyzetbe állítása során. A folyamat befejezése után a felhasználó jogkivonatot szerez be, és hozzáférést szerez az alkalmazáshoz. 

Azure AD B2C kétféleképpen biztosíthat identitás-felhasználói élményt:

* A **felhasználói folyamatok** előre definiált, beépített, konfigurálható szabályzatokat biztosítanak, így percek alatt létrehozhatók a regisztráció, a bejelentkezés és a házirend-szerkesztési élmény.

* Az **Egyéni szabályzatok** lehetővé teszik, hogy saját felhasználói utazásokat hozzon létre az összetett identitási élményhez.

Az alábbi képernyőfelvételen a felhasználói folyamat beállításai felhasználói felület, illetve az egyéni házirend-konfigurációs fájlok láthatók.

![Képernyőfelvétel: a felhasználói folyamat beállításainak KEZELŐFELÜLETe, illetve az egyéni házirend-konfigurációs fájlok.](media/user-flow-overview/user-flow-vs-custom-policy.png)

Ez a cikk röviden áttekinti a felhasználói folyamatokat és az egyéni szabályzatokat, és segít eldönteni, hogy melyik módszer fog működni a legjobban az üzleti igényei szerint.

## <a name="user-flows"></a>Felhasználói folyamatok

A leggyakoribb identitási feladatok beállításához a Azure Portal számos előre definiált és konfigurálható szabályzatot tartalmaz, amelyeket *felhasználói folyamatoknak* nevezünk.

A felhasználói folyamatok beállításait, például az alábbi beállításokkal konfigurálhatja az identitások viselkedését az alkalmazásokban:

* A bejelentkezéshez használt fióktípus, például olyan közösségi fiókok, mint például a Facebook vagy a bejelentkezéshez e-mail-címet és jelszót használó helyi fiókok
* A fogyasztótól begyűjtött attribútumok, például Utónév, postai kód vagy lakhely szerinti ország/régió
* Azure AD-Multi-Factor Authentication (MFA)
* A felhasználói felület testreszabása
* Jogcímek készlete egy jogkivonatban, amelyet az alkalmazás akkor kap, miután a felhasználó befejezte a felhasználói folyamatot
* Munkamenet-kezelés
* ... és még sok minden más

Az alkalmazások általános identitási forgatókönyvek többsége a felhasználói folyamatokkal hatékonyan definiálható és valósítható meg. Javasoljuk, hogy a beépített felhasználói folyamatokat használja, hacsak nem rendelkezik olyan összetett felhasználói utazási forgatókönyvekkel, amelyek az egyéni házirendek teljes rugalmasságát igénylik.

## <a name="custom-policies"></a>Egyéni szabályzatok

Az egyéni házirendek olyan konfigurációs fájlok, amelyek meghatározzák a Azure AD B2C bérlő felhasználói élményének viselkedését. Habár a felhasználói folyamatok előre vannak meghatározva a Azure AD B2C-portálon a leggyakoribb identitási feladatok elvégzéséhez, az egyéni szabályzatok teljes mértékben szerkeszthetők egy identitás-fejlesztővel, és számos különböző feladatot végezhetnek el.

Az egyéni szabályzatok teljes mértékben konfigurálhatók és házirend-vezéreltek. Összehangolja az entitások közötti megbízhatóságot a szabványos protokollokban. Például az OpenID Connect, a OAuth, az SAML és néhány nem standard, például REST API-alapú rendszer-rendszerbeli jogcímek cseréje. A keretrendszer felhasználóbarát, fehér címkével ellátott tapasztalatokat hoz létre.

Az egyéni házirend lehetővé teszi a felhasználói útvonalak összeállítását a lépések tetszőleges kombinációjával. Például:

* Összevonása más identitás-szolgáltatókkal
* Az első és a harmadik féltől származó többtényezős hitelesítés (MFA) kihívásai
* Bármilyen felhasználói bevitel összegyűjtése
* Integráció külső rendszerekkel REST API kommunikáció használatával

Az egyes felhasználói útvonalakat egy házirend határozza meg. Tetszőleges számú szabályzatot építhet ki, mint amennyire szüksége lehet a szervezete legjobb felhasználói élményének engedélyezéséhez.

![A IEF által engedélyezett összetett felhasználói út példáját bemutató ábra](media/user-flow-overview/custom-policy-diagram.png)

Az egyéni házirendeket több XML-fájl határozza meg, amelyek egy hierarchikus láncban egymásra hivatkoznak. Az XML-elemek meghatározzák a jogcímek sémáját, a jogcímek átalakítását, a tartalmi definíciókat, a jogcímeket, a technikai profilokat, a felhasználói útvonalak előkészítésének lépéseit és az identitással kapcsolatos egyéb szempontokat

Az egyéni szabályzatok nagy rugalmassága a legmegfelelőbb, ha összetett identitás-forgatókönyveket kell létrehoznia. Az egyéni házirendeket konfiguráló fejlesztőknek alapos részletességgel kell megadniuk a megbízható kapcsolatokat, hogy tartalmazzák a metaadatok végpontját, a pontos jogcím-definíciókat, valamint a titkok, kulcsok és tanúsítványok konfigurálását az egyes identitás-szolgáltatók igényei szerint.

További információ az egyéni házirendekről [Azure Active Directory B2Cban](custom-policy-overview.md).

## <a name="comparing-user-flows-and-custom-policies"></a>Felhasználói folyamatok és egyéni házirendek összehasonlítása

Az alábbi táblázat részletesen összehasonlítja az Azure AD B2C felhasználói folyamatokkal és az egyéni házirenddel kapcsolatos forgatókönyveket.

| Környezet | Felhasználói folyamatok | Egyéni szabályzatok |
|-|-------------------|-----------------|
| Megcélzott felhasználók | Az összes alkalmazás-fejlesztő személyazonossági szakértelemmel vagy anélkül. | Identitás-szakemberek, rendszerintegrátorok, tanácsadók és belső identitású csapatok. Ezek kényelmesek az OpenID Connect-folyamatokkal, valamint az identitás-szolgáltatók és a jogcímek hitelesítésének megismerésére. |
| Konfigurációs módszer | Azure Portal egy felhasználóbarát felhasználói felülettel (UI). | Közvetlenül szerkesztheti az XML-fájlokat, majd feltöltheti őket a Azure Portalba. |
| Felhasználói felület testreszabása | A [felhasználói felület teljes testreszabása](customize-ui-with-html.md) HTML-, CSS-és [JavaScript](javascript-and-page-layout.md)-beállításokkal.<br><br>[Többnyelvű támogatás](language-customization.md) egyéni karakterláncokkal. | Ugyanaz |
| Attribútumok testreszabása | Standard és egyéni attribútumok. | Ugyanaz |
| Jogkivonat-és munkamenet-kezelés | A [jogkivonatok](configure-tokens.md) és a [munkamenetek viselkedésének](session-behavior.md)testreszabása. | Ugyanaz |
| Identitásszolgáltatók | [Előre definiált helyi](identity-provider-local.md) vagy [közösségi szolgáltató](add-identity-provider.md), például Azure Active Directory bérlők közötti összevonás. | Szabványokon alapuló OIDC, OAUTH és SAML.  A REST API-kkal való integráció használatával a hitelesítés is lehetséges. |
| Identitással kapcsolatos feladatok | [Regisztráció vagy bejelentkezés](add-sign-up-and-sign-in-policy.md) helyi vagy számos közösségi fiókkal.<br><br>Önkiszolgáló [jelszó-visszaállítás](add-password-reset-policy.md).<br><br>[Profil szerkesztése](add-profile-editing-policy.md).<br><br>Multi-Factor Authentication.<br><br>Hozzáférési jogkivonat folyamatai. | Hajtsa végre ugyanazokat a feladatokat, mint a felhasználói folyamatok egyéni identitás-szolgáltatók használatával vagy egyéni hatókörök használata.<br><br>Hozzon létre egy felhasználói fiókot egy másik rendszeren a regisztráció időpontjában.<br><br>Üdvözlő e-mail küldése a saját e-mail szolgáltatójának használatával.<br><br>Használjon Azure AD B2Con kívüli felhasználói tárolót.<br><br>A felhasználó által megadott adatok érvényesítése egy megbízható rendszerrel egy API használatával. |

## <a name="application-integration"></a>Alkalmazásintegráció

A bérlőben számos felhasználói folyamatot vagy különböző típusú egyéni szabályzatokat hozhat létre, és igény szerint használhatja azokat az alkalmazásokban. A felhasználói folyamatok és az egyéni házirendek is újra felhasználhatók az alkalmazások között. Ez a rugalmasság lehetővé teszi az identitások megadását és módosítását a kód minimális vagy semmilyen módosításával. 

Amikor egy felhasználó be kíván jelentkezni az alkalmazásba, az alkalmazás egy felhasználói folyamathoz vagy egyéni házirend által megadott végponthoz kezdeményezi az engedélyezési kérelmet. A felhasználói folyamat vagy az egyéni házirend határozza meg és szabályozza a felhasználó élményét. Amikor elvégeznek egy felhasználói folyamatot, Azure AD B2C generál egy jogkivonatot, majd visszairányítja a felhasználót az alkalmazáshoz.

![Mobil alkalmazás Azure AD B2C bejelentkezési oldal közötti folyamattal](media/user-flow-overview/app-integration.png)

Több alkalmazás is használhatja ugyanazt a felhasználói folyamatot vagy egyéni házirendet. Egyetlen alkalmazás több felhasználói folyamatot vagy egyéni szabályzatot is használhat.

Ha például be szeretne jelentkezni egy alkalmazásba, az alkalmazás a *regisztrációs vagy bejelentkezési* felhasználói folyamatot használja. Miután a felhasználó bejelentkezett, érdemes lehet szerkeszteni a profilt. A profil szerkesztéséhez az alkalmazás egy másik engedélyezési kérelmet indít el, ezúttal a felhasználói folyamat *szerkesztése* lehetőséget használva.

Az alkalmazás egy szabványos HTTP-hitelesítési kérelem használatával indítja el a felhasználói folyamatot, amely tartalmazza a felhasználói folyamatot vagy az egyéni szabályzat nevét. Egy testreszabott [jogkivonat](tokens-overview.md) érkezik válaszként.


## <a name="next-steps"></a>Következő lépések

- Az ajánlott felhasználói folyamatok létrehozásához kövesse az [oktatóanyag: felhasználói folyamat létrehozása](tutorial-create-user-flows.md)című témakör utasításait.
- Tudnivalók a [Azure ad B2C felhasználói folyamatának verzióiról](user-flow-versions.md).
- További információ az [Azure ad B2C egyéni szabályzatokról](custom-policy-overview.md).
