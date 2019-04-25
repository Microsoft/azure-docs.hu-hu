---
title: A kétlépéses ellenőrzés az Azure MFA és AD FS – Azure Active Directory
description: Ez az Azure Multi-Factor Authentication-oldal leírja, hogyan kezdheti el az Azure MFA és az AD FS használatát.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58cfac56af9074e065431f6adbd44496adc7c7a6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60414508"
---
# <a name="getting-started-with-azure-multi-factor-authentication-and-active-directory-federation-services"></a>Bevezetés az Azure Multi-Factor Authentication és az Active Directory összevonási szolgáltatások használatába

<center>

![Az Azure MFA és AD FS első lépések](./media/multi-factor-authentication-get-started-adfs/adfs.png)</center>

Ha a szervezete az AD FS használatával vonta össze a helyszíni Active Directoryt az Azure Active Directoryval, az Azure Multi-Factor Authentication két módon használható .

* A felhőerőforrások védelme az Azure Multi-Factor Authentication vagy az Active Directory összevonási szolgáltatások használatával
* A felhő és a helyszíni erőforrások védelme az Azure Multi-Factor Authentication-kiszolgáló használatával

A következő táblázat foglalja össze az ellenőrzésbeli különbségeket az erőforrásoknak az Azure Multi-Factor Authentication használatával, illetve az AD FS használatával való védelme esetén

| Ellenőrzés – Böngészőalapú alkalmazások | Ellenőrzés – Nem böngészőalapú alkalmazások |
|:--- |:--- |
| Az Azure AD-erőforrások védelme az Azure Multi-Factor Authentication használatával |<li>Az ellenőrzés első lépése a helyszínen történik az AD FS használatával.</li> <li>A második lépés felhőalapú hitelesítést használó telefonalapú módszer.</li> |
| Az Azure AD-erőforrások védelme az Active Directory összevonási szolgáltatásokkal |<li>Az ellenőrzés első lépése a helyszínen történik az AD FS használatával.</li><li>A második lépés a helyszínen történik a jogcím betartásával.</li> |

Összevont felhasználók alkalmazásjelszavaival kapcsolatos figyelmeztetések:

* Az alkalmazásjelszavak ellenőrzése felhőalapú hitelesítéssel történik, így mellőzik az összevonásokat. Az összevonás csak alkalmazásjelszó beállításakor van aktív használatban.
* Az alkalmazásjelszavak nem tartják be a helyszíni ügyfél hozzáférés-vezérlési beállításait.
* Az alkalmazásjelszavak használata esetén nem érhető el a helyszíni hitelesítésnaplózás.
* A fiókok letiltása/törlése akár 3 óráig is eltarthat a címtár-szinkronizálással, ami késlelteti az alkalmazásjelszó letiltását/törlését a felhőalapú identitásban.

Az Azure Multi-Factor Authentication vagy az Azure Multi-Factor Authentication-kiszolgáló AD FS-sel való beállításával kapcsolatosan további információt a következő cikkekben talál:

* [A felhőerőforrások védelme az Azure Multi-Factor Authentication és az AD FS használatával](howto-mfa-adfs.md)
* [A felhő és a helyszíni erőforrások védelme az Azure Multi-Factor Authentication-kiszolgáló és a Windows Server 2012 R2 AD FS használatával](howto-mfaserver-adfs-2012.md)
* [A felhő és a helyszíni erőforrások védelme az Azure Multi-Factor Authentication-kiszolgáló és az AD FS 2.0-s verziójának használatával](howto-mfaserver-adfs-2.md)
