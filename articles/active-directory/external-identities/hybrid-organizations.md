---
title: B2B-együttműködés hibrid szervezetek számára – Azure AD
description: A partnerek a helyszíni és a Felhőbeli erőforrásokhoz is hozzáférést biztosíthatnak az Azure AD B2B együttműködéssel.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 427f7ad4d6a1b9839b1197ef9f7ca15400ea0f59
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87909395"
---
# <a name="azure-active-directory-b2b-collaboration-for-hybrid-organizations"></a>Azure Active Directory B2B-együttműködés hibrid szervezeteknél

A Azure Active Directory (Azure AD) B2B-együttműködés megkönnyíti a külső partnerek számára, hogy hozzáférjenek a szervezet alkalmazásaihoz és erőforrásaihoz. Ez akkor is igaz, ha a hibrid konfigurációban mind a helyszíni, mind a felhőalapú erőforrásokkal rendelkezik. Nem számít, ha a külső partneri fiókokat helyileg felügyeli a helyszíni identitási rendszerben, vagy ha a felhőben lévő külső fiókokat Azure AD B2B-felhasználóként kezeli. Mostantól megadhatja, hogy ezek a felhasználók mindkét helyen azonos bejelentkezési hitelesítő adatok használatával férhessenek hozzá az erőforrásokhoz.

## <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-apps"></a>B2B-felhasználók engedélyezése az Azure AD-ben a helyszíni alkalmazásokhoz való hozzáféréshez

Ha a szervezete az Azure AD B2B együttműködési képességeket használja, hogy meghívják vendégeit a partner szervezeteitől az Azure AD-be, mostantól elérhetővé teheti a B2B-felhasználók számára a helyszíni alkalmazásokhoz való hozzáférést.

Az SAML-alapú hitelesítést használó alkalmazások számára elérhetővé teheti ezeket az alkalmazásokat a B2B-felhasználók számára a Azure Portal keresztül az Azure AD Application Proxy használatával a hitelesítéshez.

Az integrált Windows-hitelesítést (IWA) használó alkalmazások számára a Kerberos által korlátozott delegálás (KCD) használatával az Azure AD proxyt is használhatja a hitelesítéshez. Az engedélyezéshez azonban felhasználói objektumra van szükség a helyszíni Windows Server Active Directoryban. Kétféle módszerrel hozhatók létre helyi felhasználói objektumok, amelyek a B2B vendég felhasználóit jelölik.

- A Microsoft Graphhoz Microsoft Identity Manager (a felügyeleti csomaggal) 2016 SP1 és a fakezelési felügyeleti ügynököt használhatja.
- PowerShell-parancsfájlt is használhat. (Ehhez a megoldáshoz nem szükséges a webalkalmazás.)

A megoldások megvalósításával kapcsolatos további információkért lásd: [B2B-felhasználók engedélyezése az Azure ad-ben a helyszíni alkalmazásokhoz](hybrid-cloud-to-on-premises.md).

## <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources"></a>Helyileg felügyelt partneri fiókok hozzáférésének biztosítása a felhőalapú erőforrásokhoz

Az Azure AD előtt a helyszíni identitási rendszerekkel rendelkező szervezeteknek hagyományosan felügyelt partneri fiókokkal kell rendelkezniük a helyszíni címtárban. Ha Ön egy ilyen szervezet, akkor győződjön meg arról, hogy partnerei továbbra is hozzáférhetnek az alkalmazásokhoz és más erőforrásokhoz a felhőbe való áthelyezéshez. Ideális esetben azt szeretné, hogy ezek a felhasználók ugyanazt a hitelesítő adatokat használják a Felhőbeli és a helyszíni erőforrások eléréséhez. 

Mostantól olyan metódusokat is kínálunk, amelyekkel a Azure AD Connect használatával szinkronizálhatja ezeket a helyi fiókokat a felhőbe "vendég felhasználóként", ahol a fiókok ugyanúgy működnek, mint az Azure AD B2B-felhasználók.

A vállalati adatok védelme érdekében szabályozhatja a hozzáférést a megfelelő erőforrásokhoz, és konfigurálhatja azokat az engedélyezési házirendeket, amelyek az alkalmazottaktól eltérően kezelik ezeket a vendég felhasználókat.

A megvalósítás részleteiért lásd: a [helyileg felügyelt partneri fiókok hozzáférésének biztosítása a felhőalapú erőforrásokhoz az Azure ad B2B együttműködés használatával](hybrid-on-premises-to-cloud.md).
 
## <a name="next-steps"></a>Következő lépések

- [Hozzáférés biztosítása az Azure AD-beli B2B-felhasználóknak a helyszíni alkalmazásokhoz](hybrid-cloud-to-on-premises.md)
- [Hozzáférés biztosítása helyben kezelt partnerfiókoknak felhőbeli erőforrásokhoz az Azure AD vállalatközi együttműködéssel](hybrid-on-premises-to-cloud.md)


