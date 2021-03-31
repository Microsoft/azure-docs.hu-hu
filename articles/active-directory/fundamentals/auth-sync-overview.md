---
title: A hitelesítési és szinkronizálási protokoll Azure Active Directory áttekintése
description: Építészeti útmutató az Azure AD örökölt hitelesítési protokollok és szinkronizálási minták integrálásához
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: ab63bc5bd2819a239741da525eebb2404a47bbf9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92441198"
---
# <a name="azure-active-directory-integrations-with-authentication-and-synchronization-protocols"></a>Azure Active Directory integráció a hitelesítési és szinkronizálási protokollokkal

Microsoft Azure Active Directory (Azure AD) számos hitelesítési és szinkronizálási protokollal való integrációt tesz lehetővé. A hitelesítési integrációk lehetővé teszik, hogy az Azure AD-t és annak biztonsági és felügyeleti funkcióit az örökölt hitelesítési módszereket használó alkalmazások kis vagy semmilyen módosításával használja. A szinkronizálási integrációk lehetővé teszik a felhasználók és csoportok adatainak szinkronizálását az Azure AD-be, majd a felhasználó Azure AD-kezelési képességeit. Egyes szinkronizálási minták lehetővé teszik az automatizált üzembe helyezést is.

## <a name="legacy-authentication-protocols"></a>Örökölt hitelesítési protokollok

Az alábbi táblázat a hitelesítés Azure AD-integrációját mutatja be a régi hitelesítési protokollokkal és azok képességeivel. Válassza ki a megtekinteni kívánt hitelesítési protokoll nevét.

* Részletes leírás

* Mikor lehet használni

* Építészeti diagram

* A rendszerösszetevők magyarázata

* Hivatkozások az integráció megvalósításához

 

| Hitelesítési protokoll| Hitelesítés| Engedélyezés| Multi-Factor Authentication| Feltételes hozzáférés |
| - |- | - | - | - |
| [Fejlécalapú hitelesítés](auth-header-based.md)|![Pipa jel](./media/authentication-patterns/check.png)| ![Pipa jel](./media/authentication-patterns/check.png)| ![Pipa jel](./media/authentication-patterns/check.png)| ![Pipa jel](./media/authentication-patterns/check.png) |
| [LDAP-hitelesítés](auth-ldap.md)| ![Pipa jel](./media/authentication-patterns/check.png)| | |  |
| [OAuth 2.0 típusú hitelesítés](auth-oauth2.md)| ![Pipa jel](./media/authentication-patterns/check.png)| ![Pipa jel](./media/authentication-patterns/check.png)| ![Pipa jel](./media/authentication-patterns/check.png)| ![Pipa jel](./media/authentication-patterns/check.png) |
| [OIDC-hitelesítés](auth-oidc.md)| ![Pipa jel](./media/authentication-patterns/check.png)| ![Pipa jel](./media/authentication-patterns/check.png)| ![Pipa jel](./media/authentication-patterns/check.png)| ![Pipa jel](./media/authentication-patterns/check.png) |
| [Jelszó alapú egyszeri bejelentkezéses hitelesítés](auth-password-based-sso.md )| ![Pipa jel](./media/authentication-patterns/check.png)| ![Pipa jel](./media/authentication-patterns/check.png)| ![Pipa jel](./media/authentication-patterns/check.png)| ![Pipa jel](./media/authentication-patterns/check.png) |
| [RADIUS-hitelesítés]( auth-radius.md)| ![Pipa jel](./media/authentication-patterns/check.png)| | ![Pipa jel](./media/authentication-patterns/check.png)| ![Pipa jel](./media/authentication-patterns/check.png) |
| [Távoli asztali átjáró szolgáltatások](auth-remote-desktop-gateway.md)| ![Pipa jel](./media/authentication-patterns/check.png)| ![Pipa jel](./media/authentication-patterns/check.png)| ![Pipa jel](./media/authentication-patterns/check.png)| ![Pipa jel](./media/authentication-patterns/check.png) |
| [Secure Shell (SSH)](auth-ssh.md) |  ![Pipa jel](./media/authentication-patterns/check.png)| | ![Pipa jel](./media/authentication-patterns/check.png)| ![Pipa jel](./media/authentication-patterns/check.png) |
| [SAML-hitelesítés](auth-saml.md)| ![Pipa jel](./media/authentication-patterns/check.png)| ![Pipa jel](./media/authentication-patterns/check.png)| ![Pipa jel](./media/authentication-patterns/check.png)| ![Pipa jel](./media/authentication-patterns/check.png) |
| [Windows-hitelesítés – Kerberos által korlátozott delegálás](auth-kcd.md)| ![Pipa jel](./media/authentication-patterns/check.png)| ![Pipa jel](./media/authentication-patterns/check.png)| ![Pipa jel](./media/authentication-patterns/check.png)| ![Pipa jel](./media/authentication-patterns/check.png) |


 
## <a name="synchronization-patterns"></a>Szinkronizálási minták

A következő táblázat bemutatja az Azure AD-integrációt a szinkronizálási mintákkal és azok képességeivel. Válassza ki a megtekinteni kívánt minta nevét

* Részletes leírás

* Mikor lehet használni

* Építészeti diagram

* A rendszerösszetevők magyarázata

* Hivatkozások az integráció megvalósításához



| Szinkronizálási minta| Címtár-szinkronizálás| Felhasználók átadása |
| - | - | - |
| [Címtár-szinkronizálás](sync-directory.md)| ![Pipa jel](./media/authentication-patterns/check.png)|  |
| [LDAP-szinkronizálás](sync-ldap.md)| ![Pipa jel](./media/authentication-patterns/check.png)|  |
| [SCIM-szinkronizálás](sync-scim.md)| ![Pipa jel](./media/authentication-patterns/check.png)| ![Pipa jel](./media/authentication-patterns/check.png) |

