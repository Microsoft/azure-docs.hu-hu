---
title: Rugalmas végfelhasználói élmény a Azure AD B2C használatával | Microsoft Docs
description: Módszerek a rugalmasság kiépítésére a végfelhasználói élményben Azure AD B2C használatával
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 53cdbfb3ba531330c37f86233cbed990da4efaf4
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107258384"
---
# <a name="resilient-end-user-experience"></a>Rugalmas végfelhasználói élmény

A regisztrációs és bejelentkezési végfelhasználói élmény a következő elemekből áll:

- A felhasználó által kommunikáló felületek – például CSS, HTML és JavaScript

- A felhasználói folyamatok és az Ön által létrehozott egyéni házirendek – például a regisztráció, a bejelentkezés és a profil szerkesztése

- Az alkalmazás identitás-szolgáltatói (IDP) – például a helyi fiók felhasználóneve/jelszava, az Outlook, a Facebook és a Google

![A képen a végfelhasználói élmény összetevői láthatók](media/resilient-end-user-experiences/end-user-experience-architecture.png)

## <a name="choose-between-user-flow-and-custom-policy"></a>Választás a felhasználói folyamat és az egyéni házirend között  

A leggyakoribb identitás-feladatok beállításának elősegítése érdekében Azure AD B2C beépített konfigurálható [felhasználói folyamatokat](../../active-directory-b2c/user-flow-overview.md)biztosít. Saját [Egyéni szabályzatokat](../../active-directory-b2c/custom-policy-overview.md)is létrehozhat, amelyek maximális rugalmasságot biztosítanak. Azonban ajánlott egyéni házirendeket használni az összetett forgatókönyvek kezeléséhez.

### <a name="how-to-decide-between-user-flow-and-custom-policy"></a>Döntés a felhasználói folyamat és az egyéni házirend között

Ha az üzleti követelményei teljesülnek, válassza a beépített felhasználói folyamatok lehetőséget. Mivel a Microsoft széles körben teszteli, az identitás felhasználói folyamatainak megfelelő házirend-szintű működés, teljesítmény vagy skálázás ellenőrzéséhez szükséges tesztelést csökkentheti. Továbbra is tesztelni kell az alkalmazásokat a funkciók, a teljesítmény és a skálázás érdekében.

Ha az üzleti követelmények miatt [Egyéni szabályzatokat választ](../../active-directory-b2c/user-flow-overview.md) , ügyeljen arra, hogy az alkalmazás szintű tesztelésen kívül a működési, a teljesítményre vagy a skálázásra vonatkozó házirend szintű tesztelést végezzen.

Tekintse meg a [felhasználói folyamatokat és az egyéni házirendeket összehasonlító](../../active-directory-b2c/user-flow-overview.md#comparing-user-flows-and-custom-policies) cikket, amely segítséget nyújt a döntéshez.

## <a name="choose-multiple-idps"></a>Több IDP kiválasztása

[Külső identitás-szolgáltató](../../active-directory-b2c/technical-overview.md#external-identity-providers) , például a Facebook használata esetén mindenképpen legyen tartalék terv arra az esetre, ha a külső szolgáltató elérhetetlenné válik.

### <a name="how-to-set-up-multiple-idps"></a>Több IDP beállítása

A külső identitás-szolgáltató regisztrációs folyamatának részeként tartalmaznia kell egy ellenőrzött identitási jogcímet, például a felhasználó mobiltelefonszámát vagy e-mail-címét. Véglegesítse az ellenőrzött jogcímeket a mögöttes Azure AD B2C Directory-példánnyal. Ha a külső szolgáltató nem érhető el, térjen vissza az ellenőrzött identitás jogcímere, és térjen vissza a telefonszámra hitelesítési módszerként. Egy másik lehetőség, hogy egy egyszeri jelszót küld a felhasználónak, amely lehetővé teszi a felhasználó számára a bejelentkezést.

 [Alternatív hitelesítési útvonalak létrehozásához](https://github.com/azure-ad-b2c/samples/tree/master/policies/idps-filter)kövesse az alábbi lépéseket:

 1. A regisztrálási szabályzat konfigurálásával engedélyezheti a helyi fiók és a külső IDP regisztrációját.

 2. Konfiguráljon egy profilt, amely lehetővé teszi a felhasználók számára, hogy a bejelentkezés után [összekapcsolják a másik identitást a fiókjával](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/account-linking) .

 3. Értesítés küldése és engedélyezése a felhasználóknak egy [másik identitásszolgáltató való váltásra](../../active-directory-b2c/customize-ui-with-html.md#configure-dynamic-custom-page-content-uri) leállás közben.

## <a name="availability-of-multi-factor-authentication"></a>A többtényezős hitelesítés rendelkezésre állása

Ha [telefonos szolgáltatást használ a többtényezős hitelesítéshez (MFA)](../../active-directory-b2c/phone-authentication-user-flows.md), ügyeljen arra, hogy alternatív szolgáltatót használjon. A helyi távközlési vagy telefonos szolgáltató fennakadást okozhat a szolgáltatásban.

### <a name="how-to-choose-an-alternate-mfa"></a>Alternatív MFA kiválasztása  

A Azure AD B2C szolgáltatás egy beépített, telefonos MFA-szolgáltatót használ az idő-alapú egyszeri PIN-kód (OTPs) kézbesítéséhez. A felhasználó előzetesen regisztrált telefonszámára hanghívás és SMS-üzenet formájában kerül sor. A következő alternatív módszerek különböző forgatókönyvekhez érhetők el:

**Felhasználói folyamatok** használata esetén két módszer áll rendelkezésre a rugalmasság létrehozásához:

- **Felhasználói folyamat konfigurációjának módosítása**: a telefon alapú egyszeri jelszavas kézbesítés megszakadásának észlelése után módosítsa az egyszeri jelszavas kézbesítési módszert a telefonról e-mail-címre, és telepítse újra a felhasználói folyamatot, így az alkalmazások változatlanok maradnak.

![képernyőfelvétel: bejelentkezési regisztráció](media/resilient-end-user-experiences/create-sign-in.png)

- **Alkalmazások módosítása**: az egyes identitási feladatokhoz, például a regisztráláshoz és a bejelentkezéshez a felhasználói folyamatok két készletét határozza meg. Konfigurálja úgy az elsőt, hogy telefonon alapuló egyszeri jelszavas és a második e-mail-alapú egyszeri jelszavas hitelesítésre legyen beállítva. A telefon alapú egyszeri jelszavas kézbesítés megszakadásának észlelése után módosítsa és telepítse újra az alkalmazásokat úgy, hogy az első felhasználói folyamattól a másodikig váltson, így a felhasználói folyamatok változatlanok maradnak.  

**Egyéni szabályzatok** használatakor négyféle módszer áll rendelkezésre a rugalmasság létrehozásához. Az alábbi lista összetettségi sorrendben van, és újra kell telepítenie a frissített házirendeket.

- A **telefonos egyszeri jelszavas vagy e-mail-alapú egyszeri jelszavas jelszó felhasználó általi kiválasztásának engedélyezése**: mindkét lehetőséget elérhetővé teheti a felhasználók számára, és engedélyezheti a felhasználók számára az egyik lehetőség kiválasztását. Nem kell módosítania a szabályzatokat vagy az alkalmazásokat.

- **Dinamikusan válthat a telefon-alapú egyszeri jelszavas és e-mail-alapú egyszeri jelszavas kapcsolat között**: a regisztráláskor a telefon és az e-mail-adatok összegyűjtése. Előre definiálja az egyéni szabályzatot a telefon megszakadásakor felmerülő feltételes váltáshoz, az e-mail-alapú egyszeri jelszavas kézbesítéstől függően. Nem kell módosítania a szabályzatokat vagy az alkalmazásokat.

- **Hitelesítő alkalmazás használata**: egyéni házirend frissítése a [hitelesítő alkalmazás](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-mfa-totp)használatára. Ha a szokásos MFA telefon-vagy e-mail-alapú egyszeri jelszavas, akkor telepítse újra az egyéni szabályzatokat a hitelesítő alkalmazás használatára való áttéréshez.

>[!Note]
>A regisztráció során a felhasználóknak konfigurálniuk kell a hitelesítő alkalmazás-integrációt.

- **Biztonsági kérdések használata**: Ha a fenti módszerek egyike sem alkalmazható, biztonsági kérdéseket is megvalósíthat biztonsági mentésként. A felhasználók biztonsági kérdéseinek beállítása az előkészítés vagy a profil szerkesztése során, valamint a válaszok tárolása a címtártól eltérő különálló adatbázisban. Ez a módszer nem felel meg a "valami Ön számára" nevű MFA-követelménynek (például telefon), de egy másodlagos "valamit, amit ismer".

## <a name="use-a-content-delivery-network"></a>Content Delivery Network használata

A Content Delivery Networks (CDNs) jobb teljesítményű és olcsóbb, mint a blob-tárolók az egyéni felhasználói folyamat felhasználói FELÜLETének tárolásához. A weblap tartalmát a rendszer gyorsabban továbbítja a nagy rendelkezésre állású kiszolgálók földrajzilag elosztott hálózatáról.  

Rendszeresen tesztelje a CDN rendelkezésre állását és a tartalom terjesztésének teljesítményét a végpontok közötti forgatókönyv és a terheléses tesztelés segítségével. Ha az előléptetés vagy az üdülési forgalom miatt várhatóan nagy lendületet szeretne, módosítsa a becsléseket a terheléses teszteléshez.
  
## <a name="next-steps"></a>Következő lépések

- [Rugalmassági erőforrások Azure AD B2C-fejlesztőknek](resilience-b2c.md)
  
  - [Rugalmas felületek külső folyamatokkal](resilient-external-processes.md)
  - [Rugalmasság a fejlesztőknek ajánlott eljárások révén](resilience-b2c-developer-best-practices.md)
  - [Rugalmasság a monitorozás és az elemzés révén](resilience-with-monitoring-alerting.md)
- [Rugalmasság kialakítása a hitelesítési infrastruktúrában](resilience-in-infrastructure.md)
- [A hitelesítés és az engedélyezés rugalmasságának fokozása az alkalmazásokban](resilience-app-development-overview.md)