---
title: Kétlépéses ellenőrzés – Azure AD MFA és ADFS – Azure Active Directory
description: Ez az Azure AD Multi-Factor Authentication oldal, amely leírja, hogyan kezdheti el az Azure AD MFA és a AD FS használatát.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: d0023d40fdc26fa1c42a67ce78a9259643098abb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96741405"
---
# <a name="getting-started-with-azure-ad-multi-factor-authentication-and-active-directory-federation-services"></a>Első lépések az Azure AD Multi-Factor Authentication és Active Directory összevonási szolgáltatások (AD FS)

<center>

![Azure AD MFA és ADFS – első lépések](./media/multi-factor-authentication-get-started-adfs/adfs.png)</center>

Ha a szervezete a helyszíni Active Directory összevonta a AD FS használatával Azure Active Directoryekkel, az Azure AD Multi-Factor Authentication használatának két lehetősége van.

* Biztonságos felhőalapú erőforrások az Azure AD Multi-Factor Authentication vagy Active Directory összevonási szolgáltatások (AD FS) használatával
* A felhő és a helyszíni erőforrások védelme az Azure Multi-Factor Authentication-kiszolgáló használatával

Az alábbi táblázat összefoglalja az Azure AD Multi-Factor Authentication és AD FS által biztosított erőforrások biztonságossá tételének ellenőrzési élményét.

| Ellenőrzés – Böngészőalapú alkalmazások | Ellenőrzés – Nem böngészőalapú alkalmazások |
|:--- |:--- |
| Azure AD-erőforrások biztonságossá tétele az Azure AD-vel Multi-Factor Authentication |<li>Az ellenőrzés első lépése a helyszínen történik az AD FS használatával.</li> <li>A második lépés felhőalapú hitelesítést használó telefonalapú módszer.</li> |
| Az Azure AD-erőforrások védelme az Active Directory összevonási szolgáltatásokkal |<li>Az ellenőrzés első lépése a helyszínen történik az AD FS használatával.</li><li>A második lépés a helyszínen történik a jogcím betartásával.</li> |

Összevont felhasználók alkalmazásjelszavaival kapcsolatos figyelmeztetések:

* Az alkalmazásjelszavak ellenőrzése felhőalapú hitelesítéssel történik, így mellőzik az összevonásokat. Az összevonás csak alkalmazásjelszó beállításakor van aktív használatban.
* Az alkalmazásjelszavak nem tartják be a helyszíni ügyfél hozzáférés-vezérlési beállításait.
* Az alkalmazásjelszavak használata esetén nem érhető el a helyszíni hitelesítésnaplózás.
* A fiókok letiltása/törlése akár 3 óráig is eltarthat a címtár-szinkronizálással, ami késlelteti az alkalmazásjelszó letiltását/törlését a felhőalapú identitásban.

Az Azure AD Multi-Factor Authentication vagy az Azure Multi-Factor Authentication-kiszolgáló AD FS-vel való beállításával kapcsolatos információkért tekintse meg a következő cikkeket:

* [Biztonságos felhőalapú erőforrások az Azure AD Multi-Factor Authentication és AD FS használatával](howto-mfa-adfs.md)
* [A felhő és a helyszíni erőforrások védelme az Azure Multi-Factor Authentication-kiszolgáló és a Windows Server 2012 R2 AD FS használatával](howto-mfaserver-adfs-2012.md)
* [A felhő és a helyszíni erőforrások védelme az Azure Multi-Factor Authentication-kiszolgáló és az AD FS 2.0-s verziójának használatával](howto-mfaserver-adfs-2.md)
