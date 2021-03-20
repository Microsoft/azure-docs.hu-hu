---
title: Az oldalon található hivatkozások nem működnek alkalmazásproxy-alkalmazásokban
description: Az Azure AD-vel integrált alkalmazásproxy-hivatkozásokkal kapcsolatos hibák elhárítása
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/10/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a8ecb908063a40da32c1397fd4249811f9c2712
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99254614"
---
# <a name="links-on-the-page-dont-work-for-an-application-proxy-application"></a>Az oldalon található hivatkozások nem működnek alkalmazásproxy-alkalmazásokban

Ebből a cikkből megtudhatja, miért nem működik megfelelően a Azure Active Directory Application Proxy alkalmazás hivatkozásai.

## <a name="overview"></a>Áttekintés 
Az alkalmazásproxy-alkalmazás közzététele után az alkalmazásban alapértelmezés szerint működő hivatkozások a közzétett gyökér URL-címében található célhelyekre mutatnak. Az alkalmazáson belüli hivatkozások nem működnek, az alkalmazás belső URL-címe valószínűleg nem tartalmazza az alkalmazásban található hivatkozások összes célhelyét.

**Miért történik ez?** Ha egy alkalmazás hivatkozására kattint, az alkalmazásproxy megkísérli feloldani az URL-címet az adott alkalmazáson belüli belső URL-címként vagy külsőleg elérhető URL-címként. Ha a hivatkozás olyan belső URL-címre mutat, amely nem ugyanazon az alkalmazáson belül található, akkor nem tartozik ezekhez a gyűjtőhöz, és nem talált hibát eredményez.

## <a name="ways-you-can-resolve-broken-links"></a>A hibás hivatkozások feloldásának módjai

A probléma három módon oldható meg. Az alábbi lehetőségek a növekvő komplexitásban szerepelnek.

1.  Győződjön meg arról, hogy a belső URL-cím olyan gyökér, amely az alkalmazáshoz kapcsolódó összes hivatkozást tartalmazza. Ez lehetővé teszi, hogy az összes hivatkozás feloldható legyen egy alkalmazáson belül közzétett tartalomként.

    Ha módosítja a belső URL-címet, de nem szeretné módosítani a felhasználók kezdőlapját, módosítsa a Kezdőlap URL-címét a korábban közzétett belső URL-címre. Ezt a "Azure Active Directory" – alkalmazás-regisztrációk lehetőséggel teheti meg – &gt; &gt; válassza ki az Application- &gt; branding elemet. A branding (védjegyezés) szakaszban megjelenik a "Kezdőlap URL-címe" mező, amelyet a kívánt kezdőlapra lehet beállítani. Ha továbbra is használja az örökölt Alkalmazásregisztrációk élményt, a Tulajdonságok lap a "Kezdőlap URL-címe" részleteit jeleníti meg. 
    
    > [!IMPORTANT]
    > A fenti módosítások elvégzéséhez az alkalmazás-objektumok az Azure AD-ben való módosításához jogosultság szükséges. A felhasználónak hozzá kell rendelnie egy [alkalmazás-rendszergazdai](../roles/delegate-app-roles.md#assign-built-in-application-admin-roles) szerepkört, amely az Azure ad-ben engedélyezi az alkalmazás modificaion jogosultságait a felhasználónak.
    >

2.  Ha az alkalmazásai teljes tartományneveket (FQDN) használnak, [Egyéni tartományokat](application-proxy-configure-custom-domain.md) használhat az alkalmazások közzétételéhez. Ez a funkció lehetővé teszi, hogy ugyanazt az URL-címet belsőleg és külsőleg is használni lehessen.

    Ezzel a beállítással biztosíthatja, hogy az alkalmazásban lévő hivatkozások külsőleg is elérhetők legyenek az Application proxyn keresztül, mivel az alkalmazáson belüli, belső URL-címekre mutató hivatkozások kívül is felismerhetők. A hivatkozásoknak továbbra is egy közzétett alkalmazáshoz kell tartoznia. Ezzel a beállítással azonban a hivatkozásoknak nem kell ugyanahhoz az alkalmazáshoz tartoznia, és több alkalmazáshoz is tartozhatnak.

3.  Ha ezek közül egyik lehetőség sem valósítható meg, több lehetőség is van a beágyazott hivatkozások fordításának engedélyezésére. Ezek a lehetőségek közé tartoznak a Intune Managed Browser, a saját alkalmazások bővítmény vagy az alkalmazáson keresztüli fordítási beállítás használata. Ha többet szeretne megtudni ezekről a lehetőségekről és azok engedélyezéséről, tekintse meg az [Azure ad Application proxy közzétett alkalmazásokra mutató hardcoded-hivatkozások átirányítása](application-proxy-configure-hard-coded-link-translation.md)című témakört.

## <a name="next-steps"></a>Következő lépések
[Meglévő helyszíni proxykiszolgálók használata](application-proxy-configure-connectors-with-proxy-servers.md)

