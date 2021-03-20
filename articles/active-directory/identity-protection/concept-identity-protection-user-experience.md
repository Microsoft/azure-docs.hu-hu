---
title: Felhasználói élmény Azure AD Identity Protection
description: Azure AD Identity Protection felhasználói élmény
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d4897b9187caab50be4db75bbc0af03e3d35aa4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94835985"
---
# <a name="user-experiences-with-azure-ad-identity-protection"></a>Felhasználói élmény Azure AD Identity Protection

A Azure Active Directory Identity Protection a következőket teheti:

* Az Azure AD Multi-Factor Authentication (MFA) regisztrálásának megkövetelése a felhasználóktól
* A kockázatos bejelentkezések és a feltört felhasználók szervizelésének automatizálása

Az összes Identity Protection-házirend hatással van a felhasználók bejelentkezési élményére. A felhasználók regisztrálhatnak és használhatnak olyan eszközöket, mint az Azure AD MFA és az önkiszolgáló jelszó-visszaállítás csökkentheti a hatást. Ezek az eszközök a megfelelő házirend-választásokkal együtt lehetővé teszik a felhasználók számára, hogy szükség esetén önszervizelési lehetőséget adjanak.

## <a name="multi-factor-authentication-registration"></a>Multi-Factor Authentication-regisztráció

A többtényezős hitelesítés regisztrációját és az összes felhasználó megcélzását igénylő Identity Protection-házirend engedélyezésével biztosíthatja, hogy az Azure AD MFA-t a jövőben is használhatja önkiszolgálásra. A házirend konfigurálásakor a felhasználók 14 napos időszakot kapnak, ahol regisztrálhatnak, és a végén a regisztrációra kényszerülnek. A felhasználók felhasználói élményét az alábbiakban ismertetjük. További információ a cikk végfelhasználói dokumentációjában, a [kétfaktoros ellenőrzés és a munkahelyi vagy iskolai fiók áttekintése](../user-help/multi-factor-authentication-end-user-first-time.md)című cikkben található.

### <a name="registration-interrupt"></a>Regisztráció megszakítása

1. Ha bejelentkezik bármely Azure AD-integrált alkalmazásba, a felhasználó értesítést kap arról, hogy a fiók hogyan állítható be a többtényezős hitelesítéshez. Ezt a házirendet a Windows 10 beépített felhasználói felületén is aktiváljuk új eszközökhöz.
   
    ![További információ szükséges](./media/concept-identity-protection-user-experience/identity-protection-experience-more-info-mfa.png)

1. Az Azure AD-Multi-Factor Authentication regisztrálásához és a bejelentkezés befejezéséhez hajtsa végre az útmutató lépéseit.

## <a name="risky-sign-in-remediation"></a>Kockázatos bejelentkezés szervizelése

Ha egy rendszergazda szabályzatot konfigurált a bejelentkezési kockázatokhoz, az érintett felhasználók értesítést kapnak, amikor megpróbálnak bejelentkezni, és elindítják a házirendek kockázati szintjét. 

### <a name="risky-sign-in-self-remediation"></a>Kockázatos bejelentkezés önszervizelése

1. A felhasználó értesítést kap arról, hogy valami szokatlant észlelt a bejelentkezéssel kapcsolatban, például egy új helyről, eszközről vagy alkalmazásból való bejelentkezést.
   
    ![Valami szokatlan kérdés](./media/concept-identity-protection-user-experience/120.png)

1. A felhasználónak bizonyítania kell az identitását, ha az Azure AD MFA-t a korábban regisztrált módszerekkel végzi. 

### <a name="risky-sign-in-administrator-unblock"></a>Kockázatos bejelentkezési rendszergazda feloldása

A rendszergazdák dönthetnek úgy, hogy a kockázati szintjétől függően letiltják a felhasználókat a bejelentkezéskor. A zárolás feloldásához a végfelhasználóknak kapcsolatba kell lépniük az informatikai munkatársakkal, vagy megpróbálnak bejelentkezni ismerős helyről vagy eszközről. Ebben az esetben a többtényezős hitelesítés végrehajtásával önszervizelés nem lehetséges.

![A bejelentkezési kockázati házirend tiltja](./media/concept-identity-protection-user-experience/200.png)

Az informatikai munkatársak a [felhasználók letiltásának feloldása](howto-identity-protection-remediate-unblock.md#unblocking-based-on-sign-in-risk) című szakaszban szereplő utasításokat követve engedélyezhetik a felhasználóknak a bejelentkezést.

## <a name="risky-user-remediation"></a>Kockázatos felhasználói szervizelés

Ha egy felhasználói kockázati házirendet konfiguráltak, a felhasználói kockázati szintet teljesítő felhasználóknak a bejelentkezés előtt át kell esniük a felhasználói kompromisszumos helyreállítási folyamaton. 

### <a name="risky-user-self-remediation"></a>Kockázatos felhasználó önszervizelése

1. A rendszer értesíti a felhasználót, hogy a fiók biztonsága gyanús tevékenység vagy kiszivárgott hitelesítő adatok miatt veszélyben van.
   
    ![Szervizelés](./media/concept-identity-protection-user-experience/101.png)

1. A felhasználónak bizonyítania kell az identitását, ha az Azure AD MFA-t a korábban regisztrált módszerekkel végzi. 
1. Végül a felhasználónak meg kell változtatnia a jelszavát az önkiszolgáló jelszó-visszaállítással, mert valaki más is hozzáférhet a fiókjához.

## <a name="risky-sign-in-administrator-unblock"></a>Kockázatos bejelentkezési rendszergazda feloldása

A rendszergazdák dönthetnek úgy, hogy a kockázati szintjétől függően letiltják a felhasználókat a bejelentkezéskor. A zárolás feloldásához a végfelhasználóknak kapcsolatba kell lépniük az informatikai részleggel. Az önszervizelés a többtényezős hitelesítés és az önkiszolgáló jelszó-visszaállítás használatával nem lehetséges ebben az esetben.

![Felhasználói kockázati házirend tiltja](./media/concept-identity-protection-user-experience/104.png)

Az informatikai munkatársak a [felhasználók letiltásának feloldása](howto-identity-protection-remediate-unblock.md#unblocking-based-on-user-risk) című szakaszban szereplő utasításokat követve engedélyezhetik a felhasználóknak a bejelentkezést.

## <a name="see-also"></a>Lásd még

- [Kockázatok szervizelése és a felhasználók blokkolásának feloldása](howto-identity-protection-remediate-unblock.md)

- [Azure Active Directory Identity Protection](./overview-identity-protection.md)