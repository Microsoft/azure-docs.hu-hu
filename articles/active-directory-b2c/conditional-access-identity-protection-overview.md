---
title: Identity Protection és feltételes hozzáférés Azure AD B2C
description: Ismerje meg, hogyan biztosítja az Identity Protection a kockázatos bejelentkezéseket és a kockázatok észlelését. Megtudhatja, hogyan és a feltételes hozzáférés lehetővé teszi, hogy a Azure AD B2C bérlők kockázati eseményein alapuló szervezeti házirendeket kényszerítse ki.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 09/01/2020
ms.author: mimart
author: msmimart
manager: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: b17706f908613336714bb7daf08fdf3bfd5eaf64
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2020
ms.locfileid: "96928392"
---
# <a name="identity-protection-and-conditional-access-for-azure-ad-b2c"></a>Identity Protection és feltételes hozzáférés Azure AD B2C

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Azure Active Directory B2C (Azure AD B2C) biztonságának növelése Azure AD Identity Protection és feltételes hozzáféréssel. Az Identity Protection kockázatkezelési funkciói, beleértve a kockázatos felhasználókat és a kockázatos bejelentkezéseket, a rendszer automatikusan észleli és megjeleníti a Azure AD B2C bérlőn. Létrehozhat olyan feltételes hozzáférési szabályzatokat, amelyek ezeket a kockázati észleléseket használják a műveletek meghatározására és a szervezeti házirendek betartatására. Ezek a képességek együtt biztosítanak Azure AD B2C az alkalmazások tulajdonosainak nagyobb mértékben szabályozzák a kockázatos hitelesítést és a hozzáférési házirendeket.
  
Ha már ismeri az [Identity Protectiont](../active-directory/identity-protection/overview-identity-protection.md) és a [feltételes hozzáférést](../active-directory/conditional-access/overview.md) az Azure ad-ben, akkor ezekkel a lehetőségekkel a Azure ad B2C ismerős élmény lesz, és a jelen cikkben tárgyalt kisebb különbségek is megtalálhatók.

![Feltételes hozzáférés a B2C-bérlőben](media/conditional-access-identity-protection-overview/conditional-access-b2c.png)

> [!NOTE]
> A feltételes hozzáférés használatához Azure AD B2C prémium P2 szükséges.

## <a name="benefits-of-identity-protection-and-conditional-access-for-azure-ad-b2c"></a>Az Identity Protection és a feltételes hozzáférés előnyei Azure AD B2C  

Ha a feltételes hozzáférési szabályzatokat az Identity Protection kockázati észlelésével párosítja, a megfelelő házirend-művelettel válaszolhat a kockázatos hitelesítésekre.

- **Új szintű láthatóságot nyerhet az alkalmazásai és ügyfélköre hitelesítési kockázataival**. Ha az Azure AD-ben és a Microsoft-fiókban több milliárd havi hitelesítéssel rendelkezik, a kockázati észlelési algoritmusok mostantól alacsony, közepes vagy magas kockázatot jelentenek a hitelesítő adatok megjelölésére a helyi fogyasztó vagy a polgárok hitelesítése során.
- **A saját adaptív hitelesítés konfigurálásával automatikusan kezelheti a kockázatokat**. A megadott alkalmazások esetében megkövetelheti, hogy egy adott felhasználó egy második hitelesítési tényezőt adjon meg, a többtényezős hitelesítés (MFA) esetében. Vagy letilthatja a hozzáférést az észlelt kockázati szint alapján. Más Azure AD B2C-funkciókhoz hasonlóan az eredményül kapott végfelhasználói élményt is testreszabhatja a szervezet hangja, stílusa és márkája alapján. A kockázatcsökkentő alternatívákat is megjelenítheti, ha a felhasználó nem tud hozzáférni.
- **Hozzáférés vezérlése a hely, a csoportok és az alkalmazások alapján**.A feltételes hozzáférés a nem kockázati alapú helyzetek szabályozására is használható. Megkövetelheti például az MFA használatát egy adott alkalmazáshoz hozzáférő ügyfelek számára, vagy letilthatják a hozzáférést a megadott földrajzi helyekről.
- **Integráció a Azure ad B2C felhasználói folyamatok és az identitási élmény keretrendszere egyéni szabályzatokkal**. Használja meglévő testreszabott élményeit, és adja hozzá azokat a vezérlőket, amelyekre a feltételes hozzáféréssel való kapcsolódáshoz szüksége van. Emellett speciális forgatókönyveket is alkalmazhat a hozzáférés biztosításához, például a tudásalapú hozzáféréshez vagy a saját előnyben részesített MFA-szolgáltatóhoz.

## <a name="feature-differences-and-limitations"></a>Szolgáltatások közötti különbségek és korlátozások

Az Identitáskezelés és a feltételes hozzáférés Azure AD B2C általában ugyanúgy működnek, mint az Azure AD-ben, a következő kivételekkel:

- A Security Center nem érhető el Azure AD B2Cban.

- Az Identity Protection és a feltételes hozzáférés nem támogatott a kiszolgálók közötti ROPC Azure AD B2C bérlők esetében.

- Azure AD B2C bérlők esetében az Identity Protection kockázati észlelése csak a helyi B2C-fiókok esetében érhető el, nem pedig a közösségi identitásokhoz, például a Google-hoz vagy a Facebookhoz.

- Azure AD B2C bérlők esetében az Identity Protection kockázati észlelésének egy részhalmaza érhető el. Lásd: [Identity Protection beállítása](conditional-access-identity-protection-setup.md#set-up-identity-protection).

- A feltételes hozzáférésű eszköz megfelelőségi funkciója Azure AD B2C bérlők esetében nem érhető el.


## <a name="integrate-conditional-access-with-user-flows-and-custom-policies"></a>Feltételes hozzáférés integrálása felhasználói folyamatokkal és egyéni szabályzatokkal

Azure AD B2C a feltételes hozzáférési feltételeket a beépített felhasználói folyamatokból is aktiválhatja. A feltételes hozzáférést egyéni szabályzatokhoz is beépítheti. A B2C felhasználói folyamat más szempontjaihoz hasonlóan a végfelhasználói üzenetkezelés is testreszabható a szervezet hang-, márka-és enyhítő alternatívái alapján. Lásd: [feltételes hozzáférés technikai profiljának definiálása](conditional-access-technical-profile.md).

## <a name="microsoft-graph-api"></a>Microsoft Graph API

A feltételes hozzáférési szabályzatokat a Azure AD B2C Microsoft Graph API-val is kezelheti. Részletekért tekintse meg a [feltételes hozzáférési dokumentációt](../active-directory/conditional-access/overview.md) és a [Microsoft Graph referenciát](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta).

## <a name="next-steps"></a>Következő lépések

- [Az Identity Protection és a feltételes hozzáférés beállítása Azure AD B2Choz](conditional-access-identity-protection-setup.md)
- [További információ az Azure AD Identity Protection szolgáltatásáról](../active-directory/identity-protection/overview-identity-protection.md)
- [Tudnivalók a feltételes hozzáférésről](../active-directory/conditional-access/overview.md)