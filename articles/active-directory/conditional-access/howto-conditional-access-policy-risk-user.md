---
title: Felhasználói kockázat alapú feltételes hozzáférés – Azure Active Directory
description: Feltételes hozzáférési szabályzatok létrehozása az Identity Protection felhasználói kockázatával
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 07/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: d5423a61c8febee72f32935f3dee4e9f9e868630
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89049077"
---
# <a name="conditional-access-user-risk-based-conditional-access"></a>Feltételes hozzáférés: felhasználói kockázat alapú feltételes hozzáférés

A Microsoft együttműködik a kutatókkal, a bűnüldözéssel, a Microsoft különböző biztonsági csoportjaival és más megbízható forrásokkal a kiszivárgott Felhasználónév és jelszó párok megtalálásához. A prémium szintű Azure AD P2 licenccel rendelkező szervezetek létrehozhatnak [Azure ad Identity Protection felhasználói kockázati észleléseket](../identity-protection/concept-identity-protection-risks.md#user-risk)tartalmazó feltételes hozzáférési szabályzatokat. 

A házirendet két helyen lehet hozzárendelni. A szervezeteknek a következő lehetőségek egyikét kell választaniuk ahhoz, hogy egy, a biztonságos jelszó megváltoztatását igénylő, felhasználói kockázatalapú feltételes hozzáférési szabályzatot engedélyezzenek.

## <a name="enable-with-conditional-access-policy"></a>Engedélyezés feltételes hozzáférési házirenddel

1. Jelentkezzen be a **Azure Portal** globális rendszergazdaként, biztonsági rendszergazdaként vagy feltételes hozzáférést biztosító rendszergazdaként.
1. Keresse meg **Azure Active Directory**  >  **biztonsági**  >  **feltételes hozzáférését**.
1. Válassza az **új szabályzat** lehetőséget.
1. Adjon nevet a szabályzatnak. Javasoljuk, hogy a szervezetek értelmes szabványt hozzanak létre a szabályzatok nevében. További információ: [a szabályzatok elnevezési szabályainak beállítása](./plan-conditional-access.md#set-naming-standards-for-your-policies).
1. A **Hozzárendelések** alatt válassza a **Felhasználók és csoportok** lehetőséget.
   1. A **Belefoglalás** területen válassza a **minden felhasználó** lehetőséget.
   1. A **kizárás** területen válassza a **felhasználók és csoportok** lehetőséget, majd válassza ki a szervezet vészhelyzeti hozzáférését vagy az adatbontási fiókokat. 
   1. Válassza a **Kész** lehetőséget.
1. A **Cloud apps vagy a műveletek** területen válassza a  >   **minden felhőalapú alkalmazás** lehetőséget.
1. A   >  **felhasználói kockázat** beállítás alatt állítsa az **Igen** értékre a **configure (Konfigurálás** ) beállítást. A **házirend érvényesítéséhez szükséges felhasználói kockázati szintek konfigurálása** területen válassza a **magas** lehetőséget, majd kattintson a **kész** gombra.
1. A **hozzáférés-vezérlés**  >  **megadása** területen válassza a **hozzáférés biztosítása**, a **jelszó megkövetelése**, majd a **kiválasztás** lehetőséget.
1. Erősítse meg a beállításokat, és állítsa **be az** **Engedélyezés házirendet** be értékre.
1. Válassza a **Létrehozás** lehetőséget a szabályzat engedélyezéséhez.

## <a name="enable-through-identity-protection"></a>Engedélyezés az Identity Protection használatával

1. Jelentkezzen be az **Azure Portalra**.
1. Válassza a **minden szolgáltatás** lehetőséget, majd keresse meg **Azure ad Identity Protection**.
1. Válassza a **felhasználói kockázati házirend** elemet.
1. A **hozzárendelések** alatt válassza a **felhasználók** lehetőséget.
   1. A **Belefoglalás** területen válassza a **minden felhasználó** lehetőséget.
   1. A **kizárás** területen válassza a **kizárt felhasználók kiválasztása** lehetőséget, válassza ki a szervezet vészhelyzeti hozzáférését vagy az adatbontási fiókokat, és válassza a **kiválasztás** lehetőséget.
   1. Válassza a **Kész** lehetőséget.
1. A **feltételek** területen válassza a **felhasználói kockázat** lehetőséget, majd válassza a **magas** lehetőséget.
   1. Válassza a **kiválasztás**, majd a **kész** lehetőséget.
1. A   >  **hozzáférés**-vezérlés területen válassza a **hozzáférés engedélyezése** lehetőséget, majd jelölje be a **jelszó módosításának megkövetelése** jelölőnégyzetet.
   1. Válassza a **Kiválasztás** lehetőséget.
1. **Házirend kényszerített** értékének beállítása **a** következőre:.
1. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>Következő lépések

[Feltételes hozzáférés – közös szabályzatok](concept-conditional-access-policy-common.md)

[Bejelentkezési kockázatalapú feltételes hozzáférés](howto-conditional-access-policy-risk.md)

[A hatás meghatározása a feltételes hozzáférésről szóló jelentés módban](howto-conditional-access-insights-reporting.md)

[Bejelentkezési viselkedés szimulálása a feltételes hozzáférési What If eszköz használatával](troubleshoot-conditional-access-what-if.md)

[Mi az az Azure Active Directory Identity Protection?](../identity-protection/overview-identity-protection.md)