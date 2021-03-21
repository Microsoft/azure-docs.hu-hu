---
title: Több-bérlős alkalmazás hozzáadása az Azure AD Application Galleryhez
description: Ez a cikk azt ismerteti, hogy miként listázható a saját fejlesztésű több-bérlős alkalmazás az Azure AD Application Galleryben.
services: active-directory
documentationCenter: na
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ryanwi
ms.reviewer: jeedes
ROBOTS: NOINDEX
ms.openlocfilehash: de0231a49c4f806660ea0cb305fdc1a70e2b36d3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98063127"
---
# <a name="add-a-multitenant-application-to-the-azure-ad-application-gallery"></a>Több-bérlős alkalmazás hozzáadása az Azure AD alkalmazáskatalógusához

## <a name="what-is-the-azure-ad-application-gallery"></a>Mi az Azure AD Application Gallery?

A Azure Active Directory (Azure AD) egy felhőalapú identitás-szolgáltatás. Az [Azure ad-alkalmazás](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActiveDirectory) katalógusa az Azure Marketplace App Store-ban található, ahol az összes alkalmazás-összekötő közzé van téve az egyszeri bejelentkezéshez és a felhasználók üzembe helyezéséhez. Az Azure AD-t identitás-szolgáltatóként használó ügyfeleink megtalálják az itt közzétett különböző SaaS-alkalmazás-összekötőket. A rendszergazdák hozzáadhatnak összekötőket az alkalmazás-katalógusból, majd konfigurálhatják és használhatják az összekötőket az egyszeri bejelentkezéshez és az üzembe helyezéshez. Az Azure AD az összes jelentősebb összevonási protokollt támogatja, beleértve az SAML 2,0, az OpenID Connect, a OAuth és az WS-Fed az egyszeri bejelentkezéshez. 

## <a name="if-your-application-supports-saml-or-openidconnect"></a>Ha az alkalmazás támogatja az SAML-t vagy a OpenIDConnect-t
Ha rendelkezik egy több-bérlős alkalmazással, amelyet az Azure AD-alkalmazás-katalógusban szeretne felvenni, először győződjön meg arról, hogy az alkalmazás támogatja a következő egyszeri bejelentkezési technológiák egyikét:

- **OpenID Connect**: az alkalmazás listájának létrehozásához hozza létre a több-bérlős alkalmazást az Azure ad-ben, és implementálja az [Azure ad-beli engedélyezési keretrendszert](./consent-framework.md) az alkalmazáshoz. Küldje el a bejelentkezési kérést egy közös végpontra, hogy bármely ügyfél beleegyezik az alkalmazásba. A felhasználó hozzáférését a bérlő azonosítója és a jogkivonatban kapott felhasználó egyszerű felhasználóneve alapján szabályozhatja. Küldje el az alkalmazást az [alkalmazás listázása az Azure Active Directory Application Galleryben](./v2-howto-app-gallery-listing.md)című részében ismertetett eljárással.

- **SAML**: Ha az alkalmazása támogatja az SAML 2,0-et, az alkalmazás a katalógusban is szerepelhet. Kövesse az [alkalmazás listázása a Azure Active Directory Application Galleryben](./v2-howto-app-gallery-listing.md)című témakör utasításait.

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>Ha az alkalmazás nem támogatja az SAML-t vagy a OpenIDConnect-t
Az SAML-vagy OpenIDConnect-t nem támogató alkalmazások továbbra is integrálhatók az alkalmazás-katalógusba a jelszó egyszeri bejelentkezési technológiájának használatával.

A jelszavas egyszeri bejelentkezés, más néven a jelszó-tároló, lehetővé teszi a felhasználói hozzáférés és jelszavak kezelését az identitás-összevonást nem támogató webalkalmazásokhoz. Olyan helyzetekben is hasznos, amikor több felhasználónak egyetlen fiókot kell megosztania, például a szervezete közösségi Media app-fiókjaival. 

Ha az alkalmazást a következő technológiával szeretné listázni:
1. Hozzon létre egy webalkalmazást, amely tartalmaz egy HTML-bejelentkezési oldalt a [jelszó-egyszeri bejelentkezés](../manage-apps/what-is-single-sign-on.md)konfigurálásához. 
2. Küldje el a kérelmet az [alkalmazás listázása a Azure Active Directory Application Galleryben](./v2-howto-app-gallery-listing.md)című témakörben leírtak szerint.

## <a name="escalations"></a>Azok következményeinek

Bármilyen eszkaláció esetén küldjön e-mailt az [Azure ad SSO integrációs csapatának](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) , és a lehető leghamarabb visszakerülünk Önnek.

## <a name="next-steps"></a>Következő lépések
Megtudhatja, hogyan [listázhatja az alkalmazást az Azure Active Directory alkalmazás-](./v2-howto-app-gallery-listing.md)katalógusban.