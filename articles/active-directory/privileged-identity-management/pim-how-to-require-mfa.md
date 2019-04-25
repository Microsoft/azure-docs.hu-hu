---
title: Többtényezős hitelesítés (MFA) és a PIM - Azure Active Directory |} A Microsoft Docs
description: Ismerje meg, miként ellenőrzi az Azure AD Privileged Identity Management (PIM) a többtényezős hitelesítés (MFA).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 08/31/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8831a838a403d58d8673c6400e0dda06c03dc69f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60442675"
---
# <a name="multi-factor-authentication-mfa-and-pim"></a>Többtényezős hitelesítés (MFA) és a PIM

Azt javasoljuk, hogy szükséges-e többtényezős hitelesítés (MFA) az összes rendszergazda számára. Ez csökkenti a biztonsági kockázatot jelentő jelszó miatt a támadás kockázatát.

Megkövetelheti, hogy a felhasználók bejelentkezéskor befejezéséhez az MFA-hitelesítést. Is megkövetelheti, hogy felhasználók végezze el az MFA-hitelesítést amikor aktiválnak egy szerepkörhöz az Azure Active Directory (Azure AD) Privileged Identity Management (PIM). Ezzel a módszerrel a felhasználó nem fejeződött be az MFA-hitelesítést, ha azok jelentkezett be, ha kéri erre a PIM által.

> [!IMPORTANT]
> Kattintson a jobb most az Azure MFA csak akkor működik a munkahelyi vagy iskolai fiókokhoz, nem Microsoft-fiókok (általában egy személyes fiók, amellyel jelentkezzen be a Microsoft szolgáltatásaihoz, például a Skype, Xbox, Outlook.com, stb.). Ez az oka bárki Microsoft-fiókkal nem lehet a jogosult rendszergazda, mert a szerepkörök aktiválására nem használhatják a többtényezős hitelesítés. Ha ezek a felhasználók továbbra is a Microsoft-fiókkal számítási feladatok kezelése, megszereznie azokat állandó rendszergazdák most.

## <a name="how-pim-validates-mfa"></a>Hogyan PIM érvényesíti a többtényezős hitelesítés

A többtényezős hitelesítés érvényesítése, amikor egy felhasználó aktiválja a szerepkörét két lehetőség van.

A legegyszerűbb lehetőség, hogy a felhasználók számára, akik egy rendszerjogosultságú szerepkör aktiválása az Azure MFA támaszkodnak. Ehhez először ellenőrizze, hogy ezek a felhasználók licencét, ha szükséges, és az Azure MFA-kiszolgáló regisztrálva van. Az Azure MFA üzembe helyezésével kapcsolatos további információkért lásd: [üzembe helyezése a felhőalapú Azure multi-factor Authentication](../authentication/howto-mfa-getstarted.md). Azt javasoljuk, de nem szükséges, hogy konfigurálja-e az Azure AD MFA kényszerítése ezeknek a felhasználóknak, amikor bejelentkeznek a. Ennek az oka az MFA-ellenőrzések magát a PIM szerint történik.

Azt is megteheti Ha a felhasználók végeznek helyszíni hitelesítést használhat az identitásszolgáltató feladata a multi-factor Authentication. Például, ha már konfigurálta a AD összevonási szolgáltatások az Azure AD-ben elérése előtt intelligenskártya-alapú hitelesítés kötelezővé tétele [felhőerőforrások védelme Azure multi-factor Authentication és az AD FS](../authentication/howto-mfa-adfs.md) utasításokat tartalmaz AD FS-t az Azure AD-jogcímek küldése konfigurálásához. Amikor egy felhasználó megpróbál egy szerepkör aktiválásához, a PIM fogja fogadni, hogy az MFA már ellenőrzése megtörtént a felhasználó után, megkapja a megfelelő jogcímeket.

## <a name="next-steps"></a>További lépések

- [A PIM az Azure AD szerepkör-beállítások konfigurálása](pim-how-to-change-default-settings.md)
- [A PIM Azure szerepkör-beállítások konfigurálása](pim-resource-roles-configure-role-settings.md)
