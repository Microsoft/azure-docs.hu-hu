---
title: Vegye fel egy több-bérlős alkalmazást az Azure AD alkalmazáskatalógusában |} A Microsoft Docs
description: Ismerteti, hogyan listázhatja az egyénileg fejlesztett több-bérlős alkalmazást az Azure AD alkalmazáskatalógusában.
services: active-directory
documentationCenter: na
author: rwike77
manager: CelesteDG
ms.assetid: 92c1651a-675d-42c8-b337-f78e7dbcc40d
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ryanwi
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0434ec889a791079e2019bcb1d04f5ce0fceb731
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65545559"
---
# <a name="add-a-multitenant-application-to-the-azure-ad-application-gallery"></a>Vegye fel egy több-bérlős alkalmazást az Azure AD alkalmazáskatalógusában

## <a name="what-is-the-azure-ad-application-gallery"></a>Mi az az Azure AD alkalmazáskatalógusában?

Az Azure Active Directory (Azure AD) egy olyan felhőalapú identitásszolgáltatás. A [Azure AD alkalmazáskatalógusában](https://azure.microsoft.com/marketplace/active-directory/all/) az Azure Marketplace-en app Store-ban, ahol egyszeri bejelentkezést és a felhasználók átadásának közzétett összes alkalmazás-összekötő található. Ügyfeleink, akik az Azure AD Identitásszolgáltatóként, a különböző SaaS alkalmazás-összekötők közzétett itt találja. A rendszergazdák összekötők hozzáadása az app-galériából, konfigurálása és az összekötők használata egyszeri bejelentkezéshez, és üzembe helyezését. Az Azure AD összes fő összevonási protokollok, beleértve a SAML 2.0, OpenID Connect, OAuth és WS-Fed egyszeri bejelentkezést támogatja. 

## <a name="if-your-application-supports-saml-or-openidconnect"></a>Ha az alkalmazás támogatja az SAML- vagy OpenIDConnect
Ha egy több-bérlős alkalmazásban, az Azure AD alkalmazáskatalógusában szereplő kívánt rendelkezik, először győződjön meg arról, hogy az alkalmazás támogatja a következő egyszeri bejelentkezés technológiák valamelyikével:

- **OpenID Connect**: Ahhoz, hogy a felsorolt alkalmazások, a több-bérlős alkalmazás létrehozása az Azure ad-ben, és végrehajtja a [Azure ad-ben hozzájárulási keretrendszer](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) az alkalmazáshoz. A bejelentkezési kérés küldése egy közös végpontot, hogy minden ügyfél biztosíthat hozzájárulás az alkalmazáshoz. Szabályozhatja, hogy a felhasználók hozzáférése a bérlő Azonosítóját és a felhasználó egyszerű Felhasználónevét a jogkivonat érkezett alapján. Az alkalmazás beküldése leírt folyamatot követve [az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában ajánlati](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

- **SAML**: Ha az alkalmazás támogatja az SAML 2.0, az alkalmazás is szerepel a katalógusban. Kövesse a [az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában ajánlati](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>Ha az alkalmazás nem támogatja az SAML- vagy OpenIDConnect
Az alkalmazásokat, amelyek nem támogatják a SAML vagy OpenIDConnect továbbra is integrálhatók az alkalmazásgyűjtemény jelszó egyszeri bejelentkezéses technológiával.

Jelszavas egyszeri bejelentkezés, jelszótárolást, más néven lehetővé teszi felhasználói hozzáférés és a webes alkalmazásokhoz, amelyek nem támogatják az identitás-összevonási jelszavak kezelése. Emellett akkor is, amelyben több felhasználónak kell megosztani egyetlen fiókban, mint például a szervezet közösségi alkalmazás fiókok forgatókönyvek esetén hasznos. 

Ha azt szeretné, ez a technológia segítségével listázhatja:
1. Hozzon létre egy webalkalmazást, amely rendelkezik egy HTML-bejelentkezési lap konfigurálása [jelszavas egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis). 
2. A kérés elküldéséhez leírtak szerint [az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában ajánlati](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

## <a name="escalations"></a>Eszkalálást

Az összes eszkalálást e-mailben [Azure AD SSO integrációs csapat](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) , és felvesszük meg minél hamarabb.

## <a name="next-steps"></a>További lépések
Ismerje meg, hogyan [az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).
