---
title: Azure Active Directory alkalmazás-felügyeleti tanúsítványok – gyakori kérdések
description: A Azure Active Directory identitás-szolgáltatóként (identitásszolgáltató) használó alkalmazások tanúsítványainak kezelésével kapcsolatos gyakori kérdések (GYIK) című témakörben talál válaszokat.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 03/19/2021
ms.author: kenwith
ms.reviewer: secherka, mifarca, shchaur, shravank, sureshja
ms.openlocfilehash: 928bf02e2d628379738483b40631e36f0f929176
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104803718"
---
# <a name="azure-active-directory-azure-ad-application-management-certificates-frequently-asked-questions"></a>Azure Active Directory (Azure AD) alkalmazás-felügyeleti tanúsítványok – gyakori kérdések

Ez a lap a Azure Active Directory (Azure AD) identitás-szolgáltatóként (identitásszolgáltató) használó alkalmazások tanúsítványainak kezelésével kapcsolatos gyakori kérdésekre ad választ.

## <a name="is-there-a-way-to-generate-a-list-of-expiring-saml-signing-certificates"></a>Van lehetőség a lejáró SAML-aláíró tanúsítványok listájának előállítására?

Az összes alkalmazás-regisztrációt exportálhatja egy CSV-fájlban lévő címtárból a megadott alkalmazásokhoz tartozó lejáró titkokkal, tanúsítványokkal és tulajdonosokkal a [PowerShell-parancsfájlok](app-management-powershell-samples.md)használatával. 

## <a name="where-can-i-find-the-information-about-soon-to-expire-certificates-renewal-steps"></a>Hol találhatom meg a tanúsítvány-megújítási lépések lejáratának hamarosan megjelenő információkat?

A lépéseket [itt](manage-certificates-for-federated-single-sign-on.md#renew-a-certificate-that-will-soon-expire) találhatja meg.

## <a name="how-can-i-customize-the-expiration-date-for-the-certificates-issued-by-azure-ad"></a>Hogyan lehet testreszabni az Azure AD által kiadott tanúsítványok lejárati dátumát?

Alapértelmezés szerint az Azure AD úgy konfigurálja a tanúsítványt, hogy három év után lejárjon, amikor az SAML egyszeri bejelentkezés konfigurálása során automatikusan létrejön. Mivel a mentés után nem lehet módosítani a tanúsítvány dátumát, létre kell hoznia egy új tanúsítványt. Ennek módjával kapcsolatban tekintse meg az [összevonási tanúsítvány lejárati idejének testreszabását és az új tanúsítványra való átadását](manage-certificates-for-federated-single-sign-on.md#customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate)ismertető témakört.

## <a name="how-can-i-automate-the-certificates-expiration-notifications"></a>Hogyan automatizálható a tanúsítványok lejárati értesítései?

Az Azure AD e-mailben értesítést küld a 60, 30 és 7 nappal az SAML-tanúsítvány lejárta előtt. Az értesítések fogadásához több e-mail-címet is hozzáadhat. 

> [!NOTE]
> Akár 5 e-mail-címet is hozzáadhat az értesítési listához (beleértve az alkalmazást hozzáadó rendszergazda e-mail-címét). Ha több személyt kell értesítenie, használja a terjesztési lista e-mailjeit. 

Az értesítések küldéséhez használni kívánt e-mailek megadásához tekintse meg az [e-mail értesítési címek hozzáadása a tanúsítvány lejáratához](manage-certificates-for-federated-single-sign-on.md#add-email-notification-addresses-for-certificate-expiration)című témakört.

A szolgáltatástól kapott e-mail-értesítések szerkesztésére és testreszabására nincs lehetőség `aadnotification@microsoft.com` . Azonban a [PowerShell-parancsfájlok](app-management-powershell-samples.md)segítségével exportálhatja az alkalmazások regisztrációját, és megtekintheti a titkos kulcsokat és tanúsítványokat.

## <a name="who-can-update-the-certificates"></a>Kik frissíthetik a tanúsítványokat?

Az alkalmazás vagy a globális rendszergazda vagy az alkalmazás rendszergazdája Azure Portal felhasználói felületen, a PowerShellben vagy a Microsoft Graphon keresztül frissítheti a tanúsítványokat.

## <a name="i-need-more-details-about-certificate-signing-options"></a>További részletekre van szükségem a tanúsítvány-aláírási beállításokkal kapcsolatban.

Az Azure AD-ben beállíthat tanúsítvány-aláírási beállításokat és a tanúsítvány-aláírási algoritmust. További információ: [Advanced SAML-jogkivonat tanúsítvány-aláírási lehetőségei az Azure ad-alkalmazásokhoz](certificate-signing-options.md).

## <a name="i-need-to-replace-the-certificate-for-azure-ad-application-proxy-applications-and-need-more-instructions"></a>Le kell cserélni az Azure AD Application Proxy-alkalmazások tanúsítványát, és további útmutatásra van szükség.

Az Azure AD Application Proxy-alkalmazások tanúsítványainak cseréjéhez lásd: [PowerShell-minta – a tanúsítvány cseréje az Application proxy-alkalmazásokban](scripts/powershell-get-custom-domain-replace-cert.md).

## <a name="how-do-i-manage-certificates-for-custom-domains-in-azure-ad-application-proxy"></a>Hogyan az Azure AD Application Proxy egyéni tartományokhoz tartozó tanúsítványok kezelése?

Ha a helyszíni alkalmazást egyéni tartomány használatára szeretné konfigurálni, egy ellenőrzött Azure Active Directory egyéni tartományra, egy, az egyéni tartomány PFX-tanúsítványára, valamint egy, a konfigurálandó helyszíni alkalmazásra van szükség. További információ: [Egyéni tartományok az Azure ad Application proxyban](application-proxy-configure-custom-domain.md). 

## <a name="i-need-to-update-the-token-signing-certificate-on-the-application-side-where-can-i-get-it-on-azure-ad-side"></a>Frissíteni kell a jogkivonat-aláíró tanúsítványt az alkalmazás oldalán. Honnan szerezhetem be az Azure AD-t?

Az SAML X. 509 tanúsítvány megújítása: [SAML aláíró tanúsítvány](configure-saml-single-sign-on.md#saml-signing-certificate).

## <a name="what-is-azure-ad-signing-key-rollover"></a>Mi az az Azure AD-aláírási kulcs rollover?

További részletek [itt](../develop/active-directory-signing-key-rollover.md)találhatók. 

## <a name="how-do-i-renew-application-token-encryption-certificate"></a>Hogyan az alkalmazás-jogkivonat titkosítási tanúsítványának megújítása?

Az alkalmazás-jogkivonat titkosítási tanúsítványának megújítását lásd: [jogkivonat-titkosítási tanúsítvány megújítása vállalati alkalmazásokhoz](howto-saml-token-encryption.md). 

## <a name="how-do-i-renew-application-token-signing-certificate"></a>Hogyan az alkalmazás-jogkivonat aláíró tanúsítványának megújítása?

Az alkalmazás-jogkivonat aláíró tanúsítványának megújítását lásd: [jogkivonat-aláíró tanúsítvány megújítása vállalati alkalmazáshoz](manage-certificates-for-federated-single-sign-on.md).

## <a name="how-do-i-update-azure-ad-after-changing-my-federation-certificates"></a>Az összevonási tanúsítványok módosítása után Hogyan frissíteni az Azure AD-t?

Ha az összevonási tanúsítványok módosítása után szeretné frissíteni az Azure AD-t, tekintse meg a [Microsoft 365 és Azure Active Directory összevonási tanúsítványainak megújítása](../hybrid/how-to-connect-fed-o365-certs.md)című témakört.
