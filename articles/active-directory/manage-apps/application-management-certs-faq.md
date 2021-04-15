---
title: Azure Active Directory alkalmazáskezelési tanúsítványokra vonatkozó gyakori kérdések
description: A cikk tájékoztatást ad a tanúsítványok identitásszolgáltatóként (IDP) való használatával Azure Active Directory alkalmazások tanúsítványkezelésével kapcsolatos gyakori kérdésekre (GYIK).
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 03/19/2021
ms.author: iangithinji
ms.reviewer: secherka, mifarca, shchaur, shravank, sureshja
ms.openlocfilehash: 0868c942a023662a1a6d3053477d85b0245fef4b
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376239"
---
# <a name="azure-active-directory-azure-ad-application-management-certificates-frequently-asked-questions"></a>Azure Active Directory (Azure AD) alkalmazáskezelési tanúsítványokkal kapcsolatos gyakori kérdések

Ez az oldal az alkalmazások tanúsítványának identitásszolgáltatóként (IdP Azure Active Directory (Azure AD) való kezelésével kapcsolatos gyakori kérdésekre ad választ.

## <a name="is-there-a-way-to-generate-a-list-of-expiring-saml-signing-certificates"></a>Létre lehet hozni egy listát a lejáró SAML aláíró tanúsítványokról?

Egy CSV-fájlba exportálhatja az összes olyan alkalmazásregisztrációt, amely lejáró titkos [](app-management-powershell-samples.md)adatokat, tanúsítványokat és azok tulajdonosát a megadott alkalmazásokhoz használja egy CSV-fájlban. 

## <a name="where-can-i-find-the-information-about-soon-to-expire-certificates-renewal-steps"></a>Hol találom a hamarosan elévülni képes tanúsítványok megújítási lépéseit?

A lépéseket [itt](manage-certificates-for-federated-single-sign-on.md#renew-a-certificate-that-will-soon-expire) találhatja meg.

## <a name="how-can-i-customize-the-expiration-date-for-the-certificates-issued-by-azure-ad"></a>Hogyan szabhatom testre az Azure AD által kiadott tanúsítványok lejárati dátumát?

Alapértelmezés szerint az Azure AD úgy konfigurálja a tanúsítványt, hogy három év után lejárjon, amikor automatikusan létrejön az SAML egyszeri bejelentkezési konfigurációja során. Mivel a mentés után nem módosíthatja a tanúsítvány dátumát, létre kell hoznia egy új tanúsítványt. Az erre vonatkozó lépésekért tekintse meg az összevonási tanúsítvány lejárati dátumának testreszabását és egy új tanúsítványra való [átváltását.](manage-certificates-for-federated-single-sign-on.md#customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate)

## <a name="how-can-i-automate-the-certificates-expiration-notifications"></a>Hogyan automatizálhatóak a tanúsítványok lejárati értesítései?

Az Azure AD az SAML-tanúsítvány lejárta előtt 60, 30 és 7 nappal e-mailes értesítést küld. Az értesítések fogadására több e-mail-címet is hozzáadhat. 

> [!NOTE]
> Az Értesítési listához legfeljebb 5 e-mail-címet adhat hozzá (beleértve annak a rendszergazdának az e-mail-címét is, aki hozzáadta az alkalmazást). Ha további személyeket kell értesítenie, használja a terjesztési lista e-mailjeit. 

Azon e-mailek megadásához, amelyekre az értesítéseket szeretné küldeni, lásd: [E-mail-értesítési](manage-certificates-for-federated-single-sign-on.md#add-email-notification-addresses-for-certificate-expiration)címek hozzáadása a tanúsítvány lejáratához.

A szolgáltatástól kapott e-mail-értesítéseket nem lehet szerkeszteni vagy testre `aadnotification@microsoft.com` szabni. A lejárt titkos kulcsok és tanúsítványok használatával azonban exportálhatja az alkalmazásregisztrációkat [a PowerShell-szkriptekkel.](app-management-powershell-samples.md)

## <a name="who-can-update-the-certificates"></a>Ki frissítheti a tanúsítványokat?

Az alkalmazás tulajdonosa vagy globális rendszergazda vagy alkalmazás-rendszergazda frissítheti a tanúsítványokat Azure Portal felhasználói felületen, a PowerShellben vagy Microsoft Graph.

## <a name="i-need-more-details-about-certificate-signing-options"></a>További részletekre van szükségem a tanúsítvány-aláírási beállításokkal kapcsolatban.

Az Azure AD-ban beállíthatja a tanúsítvány-aláírási beállításokat és a tanúsítvány-aláíró algoritmust. További információ: [Advanced SAML token certificate signing options for Azure AD apps (Speciális SAML-jogkivonat-tanúsítvány-aláírási beállítások Azure AD-alkalmazásokhoz).](certificate-signing-options.md)

## <a name="i-need-to-replace-the-certificate-for-azure-ad-application-proxy-applications-and-need-more-instructions"></a>Le kell cserélnem az Azure AD-tanúsítványt alkalmazásproxy alkalmazásokhoz, és további utasításokra van szükségem.

Az Azure AD-alkalmazásproxy tanúsítványok cseréjéhez lásd: PowerShell-minta – Tanúsítvány cseréje alkalmazásproxy [alkalmazásokban.](scripts/powershell-get-custom-domain-replace-cert.md)

## <a name="how-do-i-manage-certificates-for-custom-domains-in-azure-ad-application-proxy"></a>Hogyan egyéni tartományok tanúsítványait az Azure AD-alkalmazásproxy?

Ahhoz, hogy egy helyszíni alkalmazást egyéni tartomány használatára konfigurálni tudja Azure Active Directory, egy ellenőrzött egyéni tartományra, egy PFX-tanúsítványra az egyéni tartományhoz és egy konfigurálni szükséges helyszíni alkalmazásra van szükség. További információ: Egyéni tartományok az [Azure AD-alkalmazásproxy.](application-proxy-configure-custom-domain.md) 

## <a name="i-need-to-update-the-token-signing-certificate-on-the-application-side-where-can-i-get-it-on-azure-ad-side"></a>Frissítenem kell a jogkivonat-aláíró tanúsítványt az alkalmazás oldalán. Hol kaphatom meg az Azure AD oldalán?

Az SAML X.509-tanúsítvány megújítását lásd: [SAML-aláíró tanúsítvány.](configure-saml-single-sign-on.md#saml-signing-certificate)

## <a name="what-is-azure-ad-signing-key-rollover"></a>Mi az az Azure AD aláírókulcs-visszaállítás?

További részleteket itt [talál.](../develop/active-directory-signing-key-rollover.md) 

## <a name="how-do-i-renew-application-token-encryption-certificate"></a>Hogyan az alkalmazás jogkivonat-titkosítási tanúsítványát?

Az alkalmazás jogkivonat-titkosítási tanúsítványának megújításához lásd: Jogkivonat-titkosítási tanúsítvány megújítása [vállalati alkalmazásokhoz.](howto-saml-token-encryption.md) 

## <a name="how-do-i-renew-application-token-signing-certificate"></a>Hogyan az alkalmazás jogkivonat-aláíró tanúsítványát?

Az alkalmazás jogkivonat-aláíró tanúsítványának megújításához lásd: How to renew a token signing certificate for an enterprise application (Jogkivonat-aláíró tanúsítvány megújítása vállalati [alkalmazáshoz).](manage-certificates-for-federated-single-sign-on.md)

## <a name="how-do-i-update-azure-ad-after-changing-my-federation-certificates"></a>Hogyan az Azure AD-t az összevonási tanúsítványok módosítása után?

Ha az összevonási tanúsítványok módosítása után frissítenie kell az Azure AD-t, tekintse meg a tanúsítvány-összevonási tanúsítványok megújítását Microsoft 365 [és Azure Active Directory.](../hybrid/how-to-connect-fed-o365-certs.md)
