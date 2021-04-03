---
title: OpenID Connect-hitelesítés Azure Active Directory
description: Építészeti útmutató az OpenID Connect hitelesítés megvalósításához Azure Active Directorysal.
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
ms.openlocfilehash: 0e5bf7e51de38d42e64f6737e687c5946a464160
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96168661"
---
# <a name="openid-connect-authentication-with-azure-active-directory"></a>OpenID Connect-hitelesítés Azure Active Directory

Az OpenID Connect (OIDC) a OAuth2 protokollon alapuló hitelesítési protokoll (amely az engedélyezéshez használatos). A OIDC a OAuth2 szabványosított üzeneteit használja az Identity Services biztosításához. 

A OIDC tervezési célja, hogy egyszerű és bonyolult dolgok legyenek. A OIDC lehetővé teszi a fejlesztők számára, hogy a jelszavakat a felhasználók és a fájlok kezelése nélkül hitelesítsék a webhelyeken és az alkalmazásokban. Ez biztosítja az App Builder számára, hogy biztonságos módon ellenőrizze annak a személynek az identitását, aki jelenleg az alkalmazáshoz kapcsolódó böngészőt vagy natív alkalmazást használja.

A felhasználó hitelesítését olyan identitás-szolgáltatón kell végrehajtani, ahol a felhasználó munkamenetét vagy hitelesítő adatait ellenőrizni fogja. Ehhez megbízható ügynökre van szükség. A natív alkalmazások általában erre a célra indítják a rendszerböngészőt. A beágyazott nézetek nem tekinthetők megbízhatónak, mivel nem lehet megakadályozni, hogy az alkalmazás a felhasználói jelszó megadását meggátolja. 

A hitelesítésen kívül a felhasználót is megkérheti a felhasználótól. A hozzájárulás a felhasználó explicit engedélye, amely lehetővé teszi az alkalmazások számára a védett erőforrások elérését. A beleegyezett hozzáférés nem azonos a hitelesítéssel, mert az adott erőforráshoz csak egyszer kell megadnia az engedélyt. A jóváhagyás addig érvényes marad, amíg a felhasználó vagy a rendszergazda manuálisan vissza nem vonja a jogosultságot. 

## <a name="use-when"></a>A következő esetekben használja

Felhasználói hozzáférésre és webes bejelentkezésre van szükség.

![építészeti diagram](./media/authentication-patterns/oidc-auth.png)

## <a name="components-of-system"></a>A System összetevői

* **Felhasználó**: szolgáltatás kérése az alkalmazástól.

* **Megbízható ügynök**: az az összetevő, amellyel a felhasználó kommunikál. Ez a megbízható ügynök általában egy webböngésző.

* **Application**(alkalmazás): az alkalmazás vagy az erőforrás-kiszolgáló, ahol az erőforrás vagy az adat található. Megbízhatónak tartja az identitás-szolgáltatót a megbízható ügynök biztonságos hitelesítéséhez és engedélyezéséhez. 

* **Azure ad**: a OIDC-szolgáltató, más néven az identitás-szolgáltató, biztonságosan felügyeli a felhasználó adatait, hozzáférését, valamint a folyamat részes felei közötti megbízhatósági kapcsolatokat. Hitelesíti a felhasználó identitását, engedélyezi és visszavonja az erőforrásokhoz való hozzáférést, és jogkivonatokat állít ki. 

## <a name="implement-oidc-with-azure-ad"></a>A OIDC megvalósítása az Azure AD-vel

* [Alkalmazások integrálása az Azure AD segítségével](../saas-apps/tutorial-list.md) 

* [OAuth 2,0 és OpenID Connect protokollok a Microsoft Identity platformon](../develop/active-directory-v2-protocols.md) 

* [Microsoft Identity platform és OpenID Connect protokoll](../develop/v2-protocols-oidc.md) 

* [Webes bejelentkezés OpenID-kapcsolattal Azure Active Directory B2C](../../active-directory-b2c/openid-connect.md) 

* [Alkalmazás biztonságossá tétele az OpenID Connect és az Azure AD használatával](/learn/modules/secure-app-with-oidc-and-azure-ad/) 

