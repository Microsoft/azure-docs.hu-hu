---
title: Az Azure AD használata az App Galleryben nem szereplő alkalmazásokhoz
description: Ismerje meg, hogyan integrálhatja az Azure AD-katalógusban nem szereplő alkalmazásokat.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 07/27/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: 9721679938517e38f669f78ee0f5f9f3a80e05a7
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258269"
---
# <a name="using-azure-ad-for-applications-not-listed-in-the-app-gallery"></a>Az Azure AD használata az App Galleryben nem szereplő alkalmazásokhoz

Az [alkalmazás hozzáadása](add-application-portal.md) rövid útmutatóból megtudhatja, hogyan adhat hozzá egy alkalmazást az Azure ad-bérlőhöz.

Az [Azure ad Application Galleryben](../saas-apps/tutorial-list.md)választható lehetőségek mellett lehetősége van egy nem katalógusbeli **alkalmazás** hozzáadására is. 

## <a name="capabilities-for-apps-not-listed-in-the-azure-ad-gallery"></a>Az Azure AD-katalógusban nem szereplő alkalmazások képességei

Hozzáadhat bármely olyan alkalmazást, amely már létezik a szervezetében, vagy bármely harmadik féltől származó alkalmazás olyan szállítótól, amely még nem része az Azure AD-katalógusnak. A [licencszerződéstől](https://azure.microsoft.com/pricing/details/active-directory/)függően a következő lehetőségek érhetők el:

- Bármely olyan alkalmazás önkiszolgáló integrációja, amely támogatja az [Security Assertion Markup Language (SAML) 2,0-](https://wikipedia.org/wiki/SAML_2.0) es identitás-szolgáltatót (SP által kezdeményezett vagy identitásszolgáltató)
- A HTML-alapú bejelentkezési oldallal rendelkező webalkalmazások önkiszolgáló integrációja [jelszó-alapú egyszeri bejelentkezés](sso-options.md#password-based-sso) használatával
- Az olyan alkalmazások önkiszolgáló kapcsolata, amelyek a [rendszert a tartományok közötti Identitáskezelés-felügyeleti (scim) protokoll használatával használják a felhasználók üzembe](../app-provisioning/use-scim-to-provision-users-and-groups.md) helyezéséhez
- Hivatkozás hozzáadása bármely alkalmazáshoz az [Office 365 app launcherben](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/) vagy [a saját alkalmazásokban](sso-options.md#linked-sign-on)

Ha az egyéni alkalmazások Azure AD-val való integrálásával kapcsolatos fejlesztői útmutatást keres, tekintse meg az [Azure ad hitelesítési forgatókönyveit](../develop/authentication-vs-authorization.md). Ha olyan alkalmazást fejleszt ki, amely egy modern protokollt használ, például az [OpenId Connect/OAuth](../develop/active-directory-v2-protocols.md) -t a felhasználók hitelesítéséhez, akkor a Azure Portal [Alkalmazásregisztrációk](../develop/quickstart-register-app.md) felületének használatával regisztrálhatja azt a Microsoft Identity platformon.

## <a name="next-steps"></a>Következő lépések

- [Gyors üzembe helyezési sorozat az App Management szolgáltatásban](view-applications-portal.md)