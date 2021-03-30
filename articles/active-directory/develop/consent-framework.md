---
title: Azure AD-beli engedélyezési keretrendszer
titleSuffix: Microsoft identity platform
description: Ismerje meg a Azure Active Directory engedélyezési keretrendszerét, és azt, hogy miként könnyíti meg a több-bérlős webes és natív ügyfélalkalmazások fejlesztését.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/21/2020
ms.author: ryanwi
ms.reviewer: zachowd, lenalepa, jesakowi
ms.openlocfilehash: 74321bc75fa760727e7896f47cdfc5b2929047e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92366088"
---
# <a name="azure-active-directory-consent-framework"></a>Az Azure Active Directory jóváhagyási keretrendszere

A Azure Active Directory (Azure AD) engedélyezési keretrendszere megkönnyíti a több-bérlős webes és natív ügyfélalkalmazások fejlesztését. Ezek az alkalmazások lehetővé teszik a felhasználói fiókok általi bejelentkezést olyan Azure AD-bérlőtől, amely eltér az alkalmazás regisztrálásának helyétől. Emellett a saját webes API-jai mellett olyan webes API-kat is el kell érniük, mint a Microsoft Graph API (az Azure AD, az Intune és a szolgáltatások eléréséhez a Microsoft 365) és más Microsoft-szolgáltatások API-jait.

A keretrendszer egy olyan felhasználó vagy rendszergazda számára, aki beleegyezik egy olyan alkalmazásba, amely megkéri a címtárban való regisztrálást, ami a címtáradatok elérését is magában foglalja. Ha például egy webalkalmazásnak a Microsoft 365 felhasználóhoz tartozó naptári adatokat kell beolvasnia, akkor először a felhasználónak kell megadnia az ügyfélalkalmazás belefoglalását. A belefoglalást követően az ügyfélalkalmazás meghívja a Microsoft Graph API-t a felhasználó nevében, és igény szerint használhatja a naptári adatokat. A [Microsoft Graph API](https://developer.microsoft.com/graph) hozzáférést biztosít a Microsoft 365 (például a SharePoint, a OneDrive, a OneNote által küldött, a plannertől származó feladatok és az Excelből származó munkafüzetek), valamint az Azure ad-től származó és más Microsoft Cloud Services-adatobjektumokból származó adatokhoz.

A hozzájárulási keretrendszer a OAuth 2,0-ra és annak különböző folyamataira épül, például az engedélyezési kód engedélyezésére és az ügyfél hitelesítő adataira a nyilvános vagy bizalmas ügyfelek használatával. Az OAuth 2,0 használatával az Azure AD lehetővé teszi számos különböző típusú ügyfélalkalmazás (például telefon, tábla, kiszolgáló vagy webalkalmazás) kiépítését, és hozzáférést biztosít a szükséges erőforrásokhoz.

További információ a OAuth 2.0 engedélyezési támogatással rendelkező hozzájárulási keretrendszer használatáról: [hozzáférés engedélyezése webalkalmazásokhoz a OAuth 2,0 és](v2-oauth2-auth-code-flow.md) az Azure ad és [Az Azure ad hitelesítési forgatókönyvei](./authentication-vs-authorization.md)használatával. További információ az Microsoft 365 Microsoft Graph használatával való jogosult hozzáférésről: az [alkalmazás hitelesítése Microsoft Graphsal](/graph/).

## <a name="consent-experience---an-example"></a>Beleegyező felhasználói élmény – példa

A következő lépések bemutatják, hogyan működik az alkalmazás-fejlesztő és a felhasználó beleegyezik a felhasználói felülettel.

1. Tegyük fel, hogy van egy olyan webes ügyfélalkalmazás, amelynek adott engedélyekre van szüksége az erőforrás/API eléréséhez. Megtudhatja, hogyan hajthatja végre ezt a konfigurációt a következő szakaszban, de lényegében a Azure Portal a rendszer a konfigurációs időpontban megjelenő engedélyezési kérelmeket használja. Más konfigurációs beállításokhoz hasonlóan az alkalmazás Azure AD-regisztrációja is részévé válik:

    ![Engedélyek más alkalmazásokhoz](./media/consent-framework/permissions.png)

1. Vegye figyelembe, hogy az alkalmazásra vonatkozó engedélyek frissítve lettek, az alkalmazás fut, és a felhasználó első alkalommal hamarosan használatba veszi azt. Először az alkalmazásnak meg kell szereznie egy engedélyezési kódot az Azure AD `/authorize` végpontján. Az engedélyezési kód ezután új hozzáférési és frissítési jogkivonat beszerzésére használható.

1. Ha a felhasználó még nincs hitelesítve, az Azure AD- `/authorize` végpont felszólítja a felhasználót, hogy jelentkezzen be.

    ![Felhasználói vagy rendszergazdai bejelentkezés az Azure AD-be](./media/consent-framework/usersignin.png)

1. Miután a felhasználó bejelentkezett, az Azure AD megállapítja, hogy a felhasználónak meg kell-e jelenítenie egy beleegyező lapot. Ez a meghatározás azon alapul, hogy a felhasználó (vagy a szervezet rendszergazdája) már megadta-e az alkalmazáshoz való hozzájárulásukat. Ha még nem adta meg a jóváhagyást, az Azure AD belekéri a felhasználót, és megjeleníti a működéséhez szükséges engedélyeket. A beleegyezési párbeszédpanelen megjelenő engedélyek halmaza megegyezik a Azure Portal **delegált engedélyeiben** kiválasztott engedélyekkel.

    ![A belefoglalt hozzáférési párbeszédpanelen megjelenő engedélyek példáját jeleníti meg](./media/consent-framework/consent.png)

1. Miután a felhasználó beleegyezett a hozzájárulásba, a rendszer egy engedélyezési kódot ad vissza az alkalmazásnak, amely a hozzáférési jogkivonat és a frissítési jogkivonat beszerzésére lett beváltva. További információ erről a folyamatról: [OAuth 2,0 engedélyezési kód folyamata](v2-oauth2-auth-code-flow.md).

1. Rendszergazdaként a bérlő összes felhasználója nevében jóváhagyhatja az alkalmazás delegált engedélyeit is. A rendszergazdai jogosultság meggátolja, hogy a rendszer a bérlő összes felhasználója számára megjelenjen a belefoglalt hozzáférés párbeszédablakban, és a rendszergazda szerepkörrel rendelkező felhasználók [Azure Portal](https://portal.azure.com) is elvégezhető. Ha meg szeretné tudni, hogy mely rendszergazdai szerepkörök jogosultak a delegált engedélyekre, tekintse meg az [Azure ad-beli rendszergazdai szerepkörre vonatkozó engedélyeket](../roles/permissions-reference.md).

    **Az alkalmazás delegált engedélyeinek beleegyezett**

   1. Ugrás az alkalmazás **API-engedélyeinek** oldalára
   1. Kattintson a **rendszergazdai jóváhagyás megadása** gombra.

      ![Engedélyek megadása explicit rendszergazdai jóváhagyáshoz](./media/consent-framework/grant-consent.png)

   > [!IMPORTANT]
   > A explicit beleegyezés megadása az **engedélyek megadása** gomb használata jelenleg szükséges a ADAL.js használó egyoldalas alkalmazásokhoz (Spa). Ellenkező esetben az alkalmazás a hozzáférési jogkivonat kérése esetén meghiúsul.

## <a name="next-steps"></a>Következő lépések

Ismerje [meg, hogyan alakíthat át egy alkalmazást több-bérlővé](howto-convert-app-to-be-multi-tenant.md)
