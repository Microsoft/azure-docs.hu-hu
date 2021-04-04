---
title: OAUTH 2,0-hitelesítés Azure Active Directory
description: Építészeti útmutató a OAUTH 2,0 hitelesítés megvalósításához Azure Active Directory használatával.
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
ms.openlocfilehash: c1604d79ce5eb9949028cd677b340bf3d4b09f6c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96172839"
---
# <a name="oauth-20-authentication-with-azure-active-directory"></a>OAuth 2,0-hitelesítés Azure Active Directory

A OAuth 2,0 az engedélyezési iparági protokoll. Lehetővé teszi, hogy a felhasználó korlátozott hozzáférést biztosítson a védett erőforrásaihoz. A kifejezetten Hypertext Transfer Protocol (HTTP)-vel való munkavégzésre tervezték, a OAuth elválasztja az ügyfél szerepkörét az erőforrás-tulajdonostól. Az ügyfél hozzáférést kér az erőforrás-tulajdonos által vezérelt és az erőforrás-kiszolgáló által üzemeltetett erőforrásokhoz. Az erőforrás-kiszolgáló az erőforrás tulajdonosának jóváhagyásával kiállítja a hozzáférési jogkivonatokat. Az ügyfél a hozzáférési jogkivonatok használatával fér hozzá az erőforrás-kiszolgáló által üzemeltetett védett erőforrásokhoz. 

A OAuth 2,0 közvetlenül kapcsolódik az OpenID Connecthez (OIDC). Mivel a OIDC egy, a OAuth 2,0-re épülő hitelesítési és engedélyezési réteg, nem kompatibilis visszafelé a OAuth 1,0-mel. Azure Active Directory (Azure AD) az összes OAuth 2,0 folyamatot támogatja. 

## <a name="use-when"></a>A következő esetekben használja:

Gazdag ügyfél & modern alkalmazások és REST-alapú webes API-hozzáférés.

![Architektúra ábrája](./media/authentication-patterns/oauth.png)

## <a name="components-of-system"></a>A System összetevői

* **Felhasználó**: szolgáltatás kérése a webalkalmazástól (alkalmazás). A felhasználó általában az az erőforrás-tulajdonos, aki az adott adattal rendelkezik, és lehetővé teszi, hogy az ügyfelek hozzáférjenek az adatforráshoz vagy az erőforráshoz. 

* **Webböngésző**: az a webböngésző, amelyet a felhasználó a OAuth-ügyfélhez kommunikál. 

* **Webalkalmazás**: a webalkalmazás, illetve az erőforrás-kiszolgáló, ahol az erőforrás vagy az adat található. Megbízik az engedélyezési kiszolgálón a OAuth-ügyfél biztonságos hitelesítéséhez és engedélyezéséhez. 

* **Azure ad**: az Azure ad az engedélyezési kiszolgáló, más néven az identitás-szolgáltató (identitásszolgáltató). Biztonságos módon kezeli a felhasználó adatait, hozzáférését és megbízhatósági kapcsolatát. Az erőforrásokhoz való hozzáférés engedélyezését és visszavonását végző jogkivonatok kiadásáért felelősek.

## <a name="implement-oauth-20-with-azure-ad"></a>A OAuth 2,0 megvalósítása az Azure AD-vel

* [Alkalmazások integrálása az Azure AD segítségével](../saas-apps/tutorial-list.md) 

* [OAuth 2,0 és OpenID Connect protokollok a Microsoft Identity platformon](../develop/active-directory-v2-protocols.md) 

* [Alkalmazások típusai és OAuth2](../develop/v2-app-types.md) 

