---
title: Rugalmasság kialakítása a külső felhasználói hitelesítésben Azure Active Directory
description: Útmutató informatikai rendszergazdáknak és építészeknek a külső felhasználók rugalmas hitelesítésének kiépítéséhez
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 487efce1fe57413dda740c42a7fd3d5ea91cfa49
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98724959"
---
# <a name="build-resilience-in-external-user-authentication"></a>Rugalmasság létrehozása külső felhasználói hitelesítéssel

[Azure Active Directory B2B együttműködés](../external-identities/what-is-b2b.md) (Azure ad B2B) olyan [külső identitások](../external-identities/delegate-invitations.md) funkciója, amelyek lehetővé teszik más szervezetekkel és személyekkel való együttműködést. Lehetővé teszi a vendég felhasználók biztonságos bevezetését az Azure AD-bérlőbe anélkül, hogy a hitelesítő adataikat kellene kezelnie. A külső felhasználók identitását és hitelesítő adatait külső Identitáskezelő (identitásszolgáltató) használatával hozzák el, így nem kell megemlékezniük egy új hitelesítő adatra. 

## <a name="ways-to-authenticate-external-users"></a>Külső felhasználók hitelesítésének módjai

Kiválaszthatja a címtár külső felhasználói hitelesítésének módszereit. Használhatja a Microsoft IDP vagy más IDP.

Minden külső identitásszolgáltató függ attól, hogy az adott identitásszolgáltató rendelkezésre áll-e. A IDP-hez való csatlakozás bizonyos módszereivel a rugalmasság növelésére is van lehetőség.

> [!NOTE] 
> Az Azure AD B2B olyan beépített képességgel rendelkezik, amely bármely [Azure Active Directory](../index.yml) Bérlővel vagy személyes [Microsoft-fiókkal](https://account.microsoft.com/account)hitelesítheti a felhasználókat. Ezekhez a beépített beállításokhoz nem kell konfigurálnia a konfigurációt.

### <a name="considerations-for-resilience-with-other-idps"></a>A más IDP való rugalmasság szempontjai

Ha külső IDP használ a vendég felhasználói hitelesítéshez, bizonyos konfigurációk szükségesek ahhoz, hogy megakadályozza a fennakadások elkerülését.

| Hitelesítési módszer| Rugalmassági megfontolások |
| - | - |
| A közösségi IDP, például a [Facebook](../external-identities/facebook-federation.md) vagy a [Google](../external-identities/google-federation.md)összevonása.| Meg kell őriznie a fiókját a identitásszolgáltató, és konfigurálnia kell az ügyfél-azonosítót és az ügyfél titkos kulcsát. |
| [Közvetlen összevonás SAML-és WS-Federation identitás-szolgáltatókkal](../external-identities/direct-federation.md)| A identitásszolgáltató-tulajdonossal együtt kell működnie a végpontokhoz való hozzáféréshez, amikor Ön függ. <br>Meg kell őriznie a tanúsítványokat és a végpontokat tartalmazó metaadatokat. |
| [Egyszeri e-mail jelszó](../external-identities/one-time-passcode.md)| Ezzel a módszerrel a Microsoft e-mail-rendszere, a felhasználó e-mail-címe és a felhasználó e-mail-ügyfélprogramja függ. |


 

## <a name="self-service-sign-up-preview"></a>Önkiszolgáló regisztráció (előzetes verzió)

Meghívások vagy hivatkozások küldésének alternatívájaként engedélyezheti az [önkiszolgáló regisztrációt](../external-identities/self-service-sign-up-overview.md).  Ez lehetővé teszi, hogy a külső felhasználók hozzáférhessenek az alkalmazásokhoz. Létre kell hoznia egy [API-összekötőt](../external-identities/self-service-sign-up-add-api-connector.md) , és hozzá kell rendelnie egy felhasználói folyamathoz. Olyan felhasználói folyamatokat társít, amelyek egy vagy több alkalmazással határozzák meg a felhasználói élményt. 

Az [API-összekötők](../external-identities/api-connectors-overview.md) használatával integrálhatja önkiszolgáló bejelentkezési felhasználói folyamatát a külső rendszerek API-kkal. Ez az API-integráció használható az [Egyéni jóváhagyási munkafolyamatokhoz](../external-identities/self-service-sign-up-add-approvals.md), a [személyazonosság-ellenőrzés elvégzéséhez](../external-identities/code-samples-self-service-sign-up.md)és egyéb feladatokhoz, például a felhasználói attribútumok felülírásához. Az API-k használata megköveteli a következő függőségek kezelését.

* **API-összekötő hitelesítése**: az összekötő beállításához végponti URL-cím, Felhasználónév és jelszó szükséges. Állítson be egy folyamatot, amellyel a hitelesítő adatokat karbantartják, és az API-tulajdonossal együttműködve ellenőrizze, hogy van-e lejárati ütemterv.

* **API-összekötő válasza**: Tervezze meg az API-összekötőket a regisztrációs folyamaton, ha az API nem érhető el. Tekintse meg és adja meg az API-fejlesztőknek az [API-válaszokat](../external-identities/self-service-sign-up-add-api-connector.md) , valamint a [hibaelhárításhoz ajánlott eljárásokat](../external-identities/self-service-sign-up-add-api-connector.md). Működjön együtt az API fejlesztői csapatával az összes lehetséges reagálási forgatókönyv teszteléséhez, beleértve a folytatást, az érvényesítési hibát és a blokkoló válaszokat. 

## <a name="next-steps"></a>Következő lépések
Rugalmassági erőforrások rendszergazdák és építészek számára
 
* [Rugalmasság létrehozása a hitelesítőadat-kezeléssel](resilience-in-credentials.md)

* [Rugalmasság kiépítése az eszközök állapotával](resilience-with-device-states.md)

* [Rugalmasság kiépítése a folyamatos hozzáférés kiértékelésének (CAE) használatával](resilience-with-continuous-access-evaluation.md)

* [Hozzon létre rugalmasságot a hibrid hitelesítésben](resilience-in-hybrid.md)

* [Rugalmasság kialakítása az alkalmazás-hozzáférés alkalmazás-proxyval való használatához](resilience-on-premises-access.md)

Rugalmassági erőforrások fejlesztők számára

* [IAM-rugalmasság létrehozása az alkalmazásokban](resilience-app-development-overview.md)

* [Hozzon létre rugalmasságot a CIAM-rendszerekben](resilience-b2c.md)
