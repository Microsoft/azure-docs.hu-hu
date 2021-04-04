---
title: A kiadói ellenőrzés áttekintése – Microsoft Identity platform | Azure
description: Áttekintést nyújt a Microsoft Identity platform közzétevő-ellenőrzési programjáról. Felsorolja az előnyeit, a program követelményeit és a gyakran ismételt kérdéseket. Ha az alkalmazás közzétevőként van megjelölve, az azt jelenti, hogy a közzétevő ellenőrizte az identitását egy olyan Microsoft Partner Network-fiókkal, amely befejezte az ellenőrzési folyamatot, és társította ezt az MPN-fiókot az alkalmazás regisztrálásával.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/19/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: 1e913e3a5356ad7f49d8b3066f5bd3da7eddd2c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93308777"
---
# <a name="publisher-verification"></a>Közzétevő ellenőrzése

A kiadói ellenőrzés segítségével a rendszergazdák és a végfelhasználók megismerhetik az alkalmazások fejlesztőinek a Microsoft Identity platformmal való integrálásának eredetiségét. 

> [!VIDEO https://www.youtube.com/embed/IYRN2jDl5dc]

Ha az alkalmazás közzétevőként van megjelölve, az azt jelenti, hogy a közzétevő ellenőrizte az identitását egy olyan [Microsoft Partner Network](https://partner.microsoft.com/membership) -fiókkal, amely befejezte az [ellenőrzési](/partner-center/verification-responses) folyamatot, és társította ezt az MPN-fiókot az alkalmazás regisztrálásával. 

Egy kék "ellenőrzött" jelvény jelenik meg az Azure AD-hozzájárulási kérdésben és a többi képernyőn: ![ beleegyezés kérése](./media/publisher-verification-overview/consent-prompt.png)

Ez a funkció elsősorban olyan fejlesztők számára készült, akik több-bérlős alkalmazásokat fejlesztenek, amelyek a [OAuth 2,0-es és az OpenID-](active-directory-v2-protocols.md) t használják a [Microsoft Identity platformmal](v2-overview.md). Ezek az alkalmazások az OpenID Connect használatával írják alá a felhasználókat, vagy az OAuth 2,0-et használhatják az olyan API-k használatával való hozzáféréshez, mint a [Microsoft Graph](https://developer.microsoft.com/graph/).

## <a name="benefits"></a>Előnyök
A kiadói ellenőrzés a következő előnyöket biztosítja:
- **Megnövekedett átláthatóság és a kockázatok csökkentése az ügyfelek számára**– ez a funkció segíti az ügyfeleket abban, hogy a szervezetében használt alkalmazásokat az általuk megbízhatónak tartott fejlesztők teszik közzé. 

- **Továbbfejlesztett védjegyezés**– az "ellenőrzött" jelvény jelenik meg az Azure ad- [hozzájárulási kérdés](application-consent-experience.md), a vállalati alkalmazások lapon, valamint a végfelhasználók és a rendszergazdák által használt további UX-felületek. 

- **Gördülékeny nagyvállalati elfogadás**– a rendszergazdák a [felhasználói jóváhagyási házirendeket](../manage-apps/configure-user-consent.md)konfigurálhatják, a közzétevő ellenőrzési állapotát pedig az elsődleges házirend feltételei közé.

> [!NOTE]
> November 2020-től kezdve a végfelhasználók nem tudnak jóváhagyást adni a legtöbb újonnan regisztrált több-bérlős alkalmazásnak ellenőrzött közzétevők nélkül. Ez a november 8th 2020 után regisztrált alkalmazásokra vonatkozik, és a OAuth 2.0-s verzióját használja az alapszintű bejelentkezés és a felhasználói profil olvasása után, valamint a különböző bérlők felhasználói számára történő kérésének megadására, ha az alkalmazás regisztrálva van. Egy figyelmeztetés jelenik meg a jóváhagyás képernyőn, amely tájékoztatja a felhasználókat arról, hogy ezek az alkalmazások kockázatos, és nem ellenőrzött közzétevők.    

## <a name="requirements"></a>Követelmények
A kiadói ellenőrzéshez néhány előfeltétel van, amelyek némelyikét már számos Microsoft-partner végezte el. Ezek a következők: 

-  Egy olyan érvényes [Microsoft Partner Network](https://partner.microsoft.com/membership) -fiók MPN-azonosítója, amely befejezte az [ellenőrzési](/partner-center/verification-responses) folyamatot. Az MPN-fióknak a szervezete [partner globális fiókjának (PGA)](/partner-center/account-structure#the-top-level-is-the-partner-global-account-pga) kell lennie. 

-  Egy Azure AD-bérlőben regisztrált, [közzétevő tartománnyal](howto-configure-publisher-domain.md) konfigurált alkalmazás.

-  Az MPN-fiók ellenőrzése során használt e-mail-cím tartományának meg kell egyeznie az alkalmazásban konfigurált közzétevő tartománnyal vagy egy, az Azure AD-bérlőhöz hozzáadott, DNS által ellenőrzött [Egyéni tartománnyal](../fundamentals/add-custom-domain.md) . 

-  Az ellenőrzést végző felhasználó számára engedélyezni kell, hogy az Azure AD-ben és az MPN-fiókban is módosíthassa az alkalmazások regisztrációját. 

    -  Az Azure AD-ben ehhez a felhasználóhoz a következő [szerepkörök](../roles/permissions-reference.md)egyikének kell tartoznia: Application admin, Cloud Application admin vagy globális rendszergazda. 

    -  A partner Centerben ennek a felhasználónak a következő [szerepkörökkel](/partner-center/permissions-overview)kell rendelkeznie: MPN-rendszergazda, fiókok rendszergazdája vagy globális rendszergazda (ez az Azure ad-ben elsajátított megosztott szerepkör).
    
-  Az ellenőrzést végző felhasználónak a [többtényezős hitelesítés](../authentication/howto-mfa-getstarted.md)használatával kell bejelentkeznie.

-  A közzétevő elfogadja a [Microsoft Identity platformot a fejlesztők használati feltételeinek](/legal/microsoft-identity-platform/terms-of-use).

Azok a fejlesztők, akik már teljesítik ezeket az előfeltételeket, percek alatt ellenőrizhetik. Ha a követelmények nem teljesülnek, a beállítás ingyenes. 

## <a name="frequently-asked-questions"></a>Gyakori kérdések 
Az alábbiakban néhány gyakori kérdést talál a közzétevő ellenőrzési programjával kapcsolatban. A követelményekkel és a folyamattal kapcsolatos gyakori kérdésekért lásd: [alkalmazás megjelölése közzétevőként – ellenőrzött](mark-app-as-publisher-verified.md).

- **Milyen __információkat biztosít a__ közzétevői ellenőrzés?**  Ha egy alkalmazás meg van jelölve, a közzétevő ellenőrizte, ez nem jelenti azt, hogy az alkalmazás vagy a közzétevője elvégezte-e az adott minősítést, megfelel-e az iparági szabványoknak, betartja az ajánlott eljárásokat stb. Más Microsoft-programok ezeket az információkat biztosítják, beleértve az [Microsoft 365 alkalmazás minősítését](/microsoft-365-app-certification/overview).

- **Mennyibe kerül ez a díj? Igényel licencet?** A Microsoft nem számítja fel a fejlesztők számára a közzétevők ellenőrzését, és nem igényel semmilyen konkrét licencet. 

- **Hogyan kapcsolódik a Microsoft 365 közzétevői igazoláshoz? Mi a helyzet Microsoft 365 alkalmazás-minősítéssel?** Ezek olyan kiegészítő programok, amelyek segítségével a fejlesztők megbízható alkalmazásokat hozhatnak létre, amelyeket az ügyfelek magabiztosan tudnak elfogadni. A folyamat első lépése a közzétevő ellenőrzése, és az összes fejlesztőnek el kell végeznie a fenti feltételeknek megfelelő alkalmazások létrehozását. 

  A Microsoft 365t is integráló fejlesztők további előnyöket kaphatnak a programoktól. További információkért tekintse meg [Microsoft 365 közzétevői igazolást](/microsoft-365-app-certification/docs/attestation) és [Microsoft 365 alkalmazás minősítését](/microsoft-365-app-certification/docs/certification). 

- **Ugyanaz, mint az Azure AD Application Gallery?** Nem – a közzétevő ellenőrzése egy kiegészítő, de különálló program a [Azure Active Directory alkalmazás-gyűjteményben](v2-howto-app-gallery-listing.md). A fenti feltételeknek megfelelő fejlesztőknek a programban való részvételtől függetlenül kell befejezniük a közzétevő ellenőrzési folyamatát. 

## <a name="next-steps"></a>Következő lépések
* Megtudhatja, hogyan [jelölheti meg az alkalmazást közzétevőként](mark-app-as-publisher-verified.md).
* A közzétevő ellenőrzésének [hibakeresése](troubleshoot-publisher-verification.md) .
