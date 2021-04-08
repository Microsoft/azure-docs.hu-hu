---
title: Rugalmasság kiépítése a hitelesítő adatok kezelése Azure Active Directory
description: Útmutató az építészek és a rendszergazdák számára a rugalmas hitelesítőadat-stratégiák létrehozásához.
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
ms.openlocfilehash: 399d2f71fa20d63dce89cf3be5c12ffd63264895
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724710"
---
# <a name="build-resilience-with-credential-management"></a>Rugalmasság létrehozása a hitelesítőadat-kezeléssel

Ha a hitelesítő adatok egy jogkivonat-kérelemben jelennek meg az Azure AD-ben, több függőség is rendelkezésre áll, amelyeknek elérhetőnek kell lenniük az ellenőrzéshez. Az első hitelesítési tényező az Azure AD-hitelesítésre, és bizonyos esetekben a helyszíni infrastruktúrára támaszkodik. A hibrid hitelesítési architektúrákkal kapcsolatos további információkért lásd: [rugalmasság kialakítása a hibrid infrastruktúrában](resilience-in-hybrid.md). 

Ha második tényezőt valósít meg, a második tényező függőségei hozzáadódnak az elsőhöz. Ha például az első tényező a PTA ESP-n keresztül történik, és a második tényező az SMS, a függőségek a következők:

* Azure AD-hitelesítési szolgáltatások

* Azure MFA szolgáltatás

* Helyszíni infrastruktúra

* Telefonos szolgáltató

* A felhasználó eszköze (nem a képen látható)

 
![Hitelesítési módszerek és függőségek képe](./media/resilience-in-credentials/admin-resilience-credentials.png)

A hitelesítő adatokkal kapcsolatos stratégiájának figyelembe kell vennie az egyes hitelesítési típusok függőségeit, és olyan módszereket kell kiépíteni, amelyek elkerülik a meghibásodást 

Mivel a hitelesítési módszerek eltérő függőségekkel rendelkeznek, célszerű engedélyezni a felhasználók számára, hogy a lehető legtöbb második tényezős beállítást regisztrálják. Ha lehetséges, ügyeljen arra, hogy a különböző függőségekkel rendelkező második tényezőket is tartalmazza. Például a hanghívás és az SMS mint második tényező ugyanazokat a függőségeket használja, így az egyetlen lehetőség nem csökkenti a kockázatot.

A legrugalmasabb hitelesítési stratégia a jelszóval nem rendelkező hitelesítés használata. A Windows Hello for Business és az 2,0 biztonsági kulcsok kevesebb függőséggel rendelkeznek, mint az erős hitelesítés két külön tényezővel. A Microsoft Authenticator alkalmazás, a vállalati Windows Hello és a 2,0 biztonsági kulcsok a legbiztonságosabbak. 

A második tényező esetében a Microsoft Authenticator alkalmazás vagy más hitelesítő alkalmazás időalapú egyszeri jelszót (TOTP) vagy ESKÜt használó hardver-tokeneket használ a legkevesebb függőséggel, ezért rugalmasabb.

## <a name="how-do-multiple-credentials-help-resilience"></a>Hogyan segít a több hitelesítő adat rugalmassága?

A több hitelesítőadat-típus kiépítés lehetővé teszi a felhasználók számára, hogy a beállítások és a környezeti kényszerek megfeleljenek. Ennek eredményeképpen az interaktív hitelesítés, amelyben a felhasználók a többtényezős hitelesítésre kérik, a rendszer rugalmasabban fogja tudni, hogy a kérés időpontjában nem érhetők el bizonyos függőségek. [Optimalizálhatja a többtényezős hitelesítéshez szükséges újrahitelesítési kéréseket](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md).

A fent ismertetett egyéni felhasználói rugalmasság mellett a vállalatoknak olyan nagy mennyiségű fennakadást kell tervezniük, mint például az olyan működési hibák, amelyek helytelen konfigurációt, természeti katasztrófát vagy vállalati szintű erőforrás-kimaradást vezetnek be a helyszíni összevonási szolgáltatásba (különösen a többtényezős hitelesítés használata esetén). 

## <a name="how-do-i-implement-resilient-credentials"></a>Hogyan rugalmas hitelesítő adatokat implementál?

* A függőségek csökkentése érdekében olyan [jelszóval nem rendelkező hitelesítő adatokat](../authentication/howto-authentication-passwordless-deployment.md) helyezzen üzembe, mint például a vállalati Windows Hello, a telefonos hitelesítés és a FIDO2 biztonsági kulcsa.

* Telepítse a [Microsoft Authenticator alkalmazást](../user-help/user-help-auth-app-overview.md) második tényezőként.

* Kapcsolja be a Windows Server Active Directoryról szinkronizált hibrid fiókok [jelszavas kivonatának szinkronizálását](../hybrid/whatis-phs.md) . Ez a beállítás az összevonási szolgáltatások (például a AD FS) mellett engedélyezhető, és az összevonási szolgáltatás meghibásodása esetén visszaesést biztosít.

* A [többtényezős hitelesítési módszerek használatának elemzése](/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/) a felhasználói élmény javítása érdekében.

* [Rugalmas hozzáférés-vezérlési stratégia implementálása](../authentication/concept-resilient-controls.md)

## <a name="next-steps"></a>Következő lépések
Rugalmassági erőforrások rendszergazdák és építészek számára
 
* [Rugalmasság kiépítése az eszközök állapotával](resilience-with-device-states.md)

* [Rugalmasság kiépítése a folyamatos hozzáférés kiértékelésének (CAE) használatával](resilience-with-continuous-access-evaluation.md)

* [Rugalmasság létrehozása külső felhasználói hitelesítéssel](resilience-b2b-authentication.md)

* [Hozzon létre rugalmasságot a hibrid hitelesítésben](resilience-in-hybrid.md)

* [Rugalmasság kialakítása az alkalmazás-hozzáférés alkalmazás-proxyval való használatához](resilience-on-premises-access.md)

Rugalmassági erőforrások fejlesztők számára

* [IAM-rugalmasság létrehozása az alkalmazásokban](resilience-app-development-overview.md)

* [Hozzon létre rugalmasságot a CIAM-rendszerekben](resilience-b2c.md)