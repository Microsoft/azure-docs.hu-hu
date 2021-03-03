---
title: Alkalmazás egyszeri bejelentkezésének konfigurálása
description: Az egyszeri bejelentkezés konfigurálása egy olyan egyéni alkalmazáshoz, amelyet Ön fejleszt és regisztrál az Azure AD-ben.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: ryanwi
ROBOTS: NOINDEX
ms.openlocfilehash: 60a07145e38c19f577ad9864efb599e7b244aa83
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101653711"
---
# <a name="how-to-configure-single-sign-on-for-an-application"></a>Egyszeri bejelentkezés konfigurálása alkalmazáshoz

Az összevont egyszeri bejelentkezés (SSO) engedélyezése az alkalmazásban automatikusan engedélyezve van, ha az egyesítő az OpenID Connect, az SAML 2,0 vagy a WS-fed használatával végzi az Azure AD-t. Ha a végfelhasználóknak be kell jelentkezniük annak ellenére, hogy már rendelkezik egy meglévő munkamenettel az Azure AD-vel, előfordulhat, hogy az alkalmazás hibásan van konfigurálva.

* Ha ADAL/MSAL használ, győződjön meg arról, hogy a **PromptBehavior** beállítása **automatikus** , és nem **mindig**.

* Ha Mobile-alkalmazást készít, további konfigurációkra lehet szükség a közvetített vagy nem felügyelt egyszeri bejelentkezés engedélyezéséhez.

Android esetén lásd: a [Cross app SSO engedélyezése Androidon](../azuread-dev/howto-v1-enable-sso-android.md).<br>

IOS esetén lásd: a [Cross app SSO engedélyezése iOS-ben](../azuread-dev/howto-v1-enable-sso-ios.md).

## <a name="next-steps"></a>Következő lépések

[Azure AD SSO](../manage-apps/what-is-single-sign-on.md)<br>

[A Cross app SSO engedélyezése Androidon](../azuread-dev/howto-v1-enable-sso-android.md)<br>

[A Cross app SSO engedélyezése iOS-ben](../azuread-dev/howto-v1-enable-sso-ios.md)<br>

[Alkalmazások integrálása a AzureAD-be](./quickstart-register-app.md)<br>

[Engedélyek és beleegyezett a Microsoft Identity platform](./v2-permissions-and-consent.md)<br>

[AzureAD Microsoft Q&A](/answers/topics/azure-active-directory.html)