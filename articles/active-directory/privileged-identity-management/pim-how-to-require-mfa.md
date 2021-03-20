---
title: MFA vagy 2FA és Privileged Identity Management – Azure AD | Microsoft Docs
description: Ismerje meg, hogyan ellenőrzi a Azure AD Privileged Identity Management (PIM) a többtényezős hitelesítést (MFA).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4344e769cc8466287dab1e98e95cc3fbe705ffbd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94834999"
---
# <a name="multi-factor-authentication-and-privileged-identity-management"></a>Multi-Factor Authentication és Privileged Identity Management

Javasoljuk, hogy a többtényezős hitelesítés (MFA) megkövetelése az összes rendszergazda számára. Ez csökkenti a támadás kockázatát egy sérült jelszó miatt.

Megkövetelheti, hogy a felhasználók a bejelentkezéskor egy multi-Factor Authentication-kihívást végezzenek. Azt is megkövetelheti, hogy a felhasználók a Azure Active Directory (Azure AD) Privileged Identity Management (PIM) szerepkör aktiválásakor végezzenek el egy multi-Factor Authentication-kihívást. Így ha a felhasználó nem adott meg többtényezős hitelesítési kihívást, amikor bejelentkezett, a rendszer Privileged Identity Management fogja kérni őket.

> [!IMPORTANT]
> Jelenleg az Azure AD Multi-Factor Authentication csak munkahelyi vagy iskolai fiókokkal működik, nem a Microsoft személyes fiókjaival (általában egy személyes fiókkal, amely a Microsoft-szolgáltatásokba (például Skype, Xbox vagy Outlook.com) való bejelentkezéshez használatos. Emiatt a személyes fiókot használó felhasználók nem jogosultak a rendszergazda szerepkörre, mert nem használhatják a többtényezős hitelesítést a szerepköreik aktiválásához. Ha ezeknek a felhasználóknak továbbra is a munkaterhelések felügyeletét Microsoft-fiók segítségével kell megadniuk, akkor az állandó rendszergazdák számára is megemelheti őket.

## <a name="how-pim-validates-mfa"></a>Hogyan érvényesíti a PIM az MFA-t

A többtényezős hitelesítés érvényesítésére két lehetőség áll rendelkezésre, amikor egy felhasználó aktiválja a szerepkört.

A legegyszerűbb lehetőség az Azure AD-Multi-Factor Authentication használata a Kiemelt szerepkört aktiváló felhasználók számára. Ehhez először ellenőrizze, hogy a felhasználók jogosultak-e az Azure AD-Multi-Factor Authenticationra, ha szükségesek. Az Azure AD Multi-Factor Authentication telepítésével kapcsolatos további információkért lásd: [felhőalapú Azure ad-multi-Factor Authentication üzembe helyezése](../authentication/howto-mfa-getstarted.md). Ajánlott, de nem kötelező, hogy az Azure AD-t konfigurálja a többtényezős hitelesítés érvényesítéséhez ezen felhasználók számára a bejelentkezéskor. Ennek az az oka, hogy a multi-Factor Authentication-ellenőrzéseket Privileged Identity Management saját maga végzi.

Ha a felhasználók helyszíni hitelesítést végeznek, akkor a többtényezős hitelesítésért a személyazonossági szolgáltató felelős. Ha például úgy konfigurálta az Active Directory összevonási szolgáltatásokat, hogy az Azure AD-hez való hozzáférés előtt intelligenskártya-alapú hitelesítést igényeljen, a [felhőalapú erőforrásokat az Azure ad-vel multi-Factor Authentication és AD FS](../authentication/howto-mfa-adfs.md) tartalmazza a jogcímek az Azure ad-be való küldéséhez szükséges AD FS konfigurálására vonatkozó utasításokat. Amikor egy felhasználó megpróbál aktiválni egy szerepkört, Privileged Identity Management elfogadja, hogy a többtényezős hitelesítés már érvényesítve lett a felhasználó számára, amint megkapja a megfelelő jogcímeket.

## <a name="next-steps"></a>Következő lépések

- [Azure AD-szerepkör beállításainak konfigurálása Privileged Identity Management](pim-how-to-change-default-settings.md)
- [Az Azure erőforrás-szerepkör beállításainak konfigurálása Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
