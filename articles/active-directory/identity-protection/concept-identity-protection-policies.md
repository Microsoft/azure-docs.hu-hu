---
title: Azure AD Identity Protection házirendek
description: Az Identity Protection által engedélyezett három szabályzat azonosítása
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28a9080ce878e262573adf0b3c79394079c09ca2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94835968"
---
# <a name="identity-protection-policies"></a>Identity Protection-szabályzatok

A Azure Active Directory Identity Protection három alapértelmezett szabályzatot tartalmaz, amelyeket a rendszergazdák engedélyeznek. Ezek a szabályzatok korlátozott testreszabást tartalmaznak, de a legtöbb szervezetre alkalmazhatók. Az összes házirend lehetővé teszi a felhasználók kizárását, például a [vészhelyzeti vagy a break-Glass rendszergazdai fiókjait](../roles/security-emergency-access.md).

![Identity Protection-szabályzatok](./media/concept-identity-protection-policies/identity-protection-policies.png)

## <a name="azure-ad-mfa-registration-policy"></a>Azure AD MFA regisztrációs szabályzat

Az Identity Protection lehetővé teszi a szervezetek számára az Azure AD Multi-Factor Authentication (MFA) használatát a bejelentkezést igénylő feltételes hozzáférési szabályzattal. Ennek a szabályzatnak a engedélyezése nagyszerű módja annak, hogy a szervezet új felhasználói az első napon regisztrálták az MFA-t. A többtényezős hitelesítés a kockázati események egyik önszervizelési módszere az Identity Protectionben. Az önszervizelés lehetővé teszi, hogy a felhasználók saját maguk végezzék el az ügyfélszolgálati hívások mennyiségének csökkentését.

Az Azure AD Multi-Factor Authentication kapcsolatos további információkért tekintse meg a következő cikket [: az Azure ad multi-Factor Authentication működése](../authentication/concept-mfa-howitworks.md).

## <a name="sign-in-risk-policy"></a>Bejelentkezési kockázati szabályzat

Az Identity Protection elemzi az egyes bejelentkezések, valós idejű és offline állapotú jeleket, és kiszámítja a kockázati pontszámot azon valószínűség alapján, hogy a felhasználó nem végezte el a bejelentkezést. A rendszergazdák a kockázati pontszám alapján hozhatnak döntéseket a szervezeti követelmények betartatása érdekében. A rendszergazdák dönthetnek úgy, hogy letilthatják a hozzáférést, engedélyezik a hozzáférést, vagy engedélyezik a hozzáférést, de többtényezős hitelesítést igényelnek.

Ha a rendszer veszélyezteti a kockázatot, a felhasználók a többtényezős hitelesítést a saját kiszolgálására, valamint a kockázatos bejelentkezési esemény bezárására is használhatják a rendszergazdák számára szükségtelen zaj megelőzése érdekében.

> [!NOTE] 
> A bejelentkezési kockázati házirend elindítása előtt a felhasználóknak előzőleg regisztrálniuk kell az Azure AD-Multi-Factor Authentication.

### <a name="custom-conditional-access-policy"></a>Egyéni feltételes hozzáférési szabályzat

A rendszergazdák dönthetnek úgy is, hogy egyéni feltételes hozzáférési szabályzatot hoznak létre, beleértve a bejelentkezési kockázatokat hozzárendelési feltételként. A feltételes hozzáférési szabályzatokban feltételként feltett feltételekkel kapcsolatos további információkért tekintse meg a következő cikket [: feltételes hozzáférés: feltételek](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk)

![Egyéni feltételes hozzáférés bejelentkezési kockázati szabályzata](./media/concept-identity-protection-policies/identity-protection-custom-sign-in-policy.png)

## <a name="user-risk-policy"></a>Felhasználói kockázati szabályzat

Az Identity Protection kiszámíthatja, hogy mit gondol a felhasználó viselkedése, és hogyan használhatja azt a kockázatokra alapozva. A felhasználói kockázat a valószínűsége annak, hogy az identitást feltörték. A rendszergazdák a kockázati pontszám alapján hozhatnak döntéseket a szervezeti követelmények betartatása érdekében. A rendszergazdák dönthetnek úgy, hogy letiltják a hozzáférést, engedélyezik vagy engedélyezik a hozzáférést, de jelszó-módosítást igényelnek az [Azure ad önkiszolgáló jelszó-visszaállítás](../authentication/howto-sspr-deployment.md)használatával.

Ha a rendszer veszélyezteti a kockázatot, a felhasználók önkiszolgáló jelszó-visszaállítást végezhetnek, és lezárhatók a felhasználói kockázati esemény a rendszergazdák számára szükségtelen zaj megelőzése érdekében.

> [!NOTE] 
> A felhasználói kockázati házirend elindítása előtt a felhasználóknak előzőleg regisztrálniuk kell magukat az önkiszolgáló jelszó-visszaállításhoz.

## <a name="next-steps"></a>Következő lépések

- [Az Azure AD önkiszolgáló jelszó-visszaállítás engedélyezése](../authentication/howto-sspr-deployment.md)

- [Az Azure AD többtényezős hitelesítés engedélyezése](../authentication/howto-mfa-getstarted.md)

- [Az Azure AD Multi-Factor Authentication regisztrációs szabályzatának engedélyezése](howto-identity-protection-configure-mfa-policy.md)

- [Bejelentkezési és felhasználói kockázati házirendek engedélyezése](howto-identity-protection-configure-risk-policies.md)
