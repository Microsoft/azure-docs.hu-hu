---
title: Azure AD biztonságos hibrid hozzáférés | Microsoft Docs
description: Ez a cikk azokat a partneri megoldásokat ismerteti, amelyekkel az Azure AD-vel integrálhatja az örökölt helyszíni, nyilvános Felhőbeli és saját felhőalapú alkalmazásokat. Az alkalmazások kézbesítési vezérlőit vagy hálózatait az Azure AD-hez való csatlakozással védheti meg örökölt alkalmazásait.
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 2/16/2021
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: a793ebb6d2b58718a6ee42c69c38b9da1b124722
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104589395"
---
# <a name="secure-hybrid-access-secure-legacy-apps-with-azure-active-directory"></a>Biztonságos hibrid hozzáférés: az örökölt alkalmazások védelme Azure Active Directory

Mostantól a helyszíni és a Felhőbeli örökölt hitelesítési alkalmazásokat a következővel érheti el Azure Active Directory (AD) szolgáltatáshoz való csatlakoztatásával:

- [Azure-AD Application Proxy](#secure-hybrid-access-sha-through-azure-ad-application-proxy)

- [Meglévő alkalmazás-kézbesítési vezérlők és hálózatok](#sha-through-networking-and-delivery-controllers)

- [Virtuális magánhálózati (VPN) és Software-Defined peremhálózati (SDP) alkalmazások](#sha-through-vpn-and-sdp-applications)

Az Azure ad-funkciókkal, például az Azure AD [feltételes hozzáféréssel](../conditional-access/overview.md) és az Azure ad [Identity Protection](../identity-protection/overview-identity-protection.md)szolgáltatással áthidalhatja a szakadékot, és megerősítheti a biztonsági helyzeteket az összes alkalmazásban.

## <a name="secure-hybrid-access-sha-through-azure-ad-application-proxy"></a>Biztonságos hibrid hozzáférés (SHA) az Azure-AD Application Proxy
  
A [alkalmazásproxy](./what-is-application-proxy.md) használatával [biztonságos távoli hozzáférést](./application-proxy.md) biztosíthat a helyszíni webalkalmazásokhoz. A felhasználóknak nem kell VPN-t használniuk. A felhasználók az [egyszeri bejelentkezés](./add-application-portal-setup-sso.md)után egyszerűen csatlakozhatnak az alkalmazásaihoz bármely eszközről. Az alkalmazásproxy távoli hozzáférést biztosít a szolgáltatáshoz, és lehetővé teszi a [helyi alkalmazások egyszerű közzétételét](./application-proxy-add-on-premises-application.md) a vállalati hálózaton kívüli felhasználók számára. Segít a felhőalapú hozzáférés-kezelés méretezésében anélkül, hogy módosítania kellene a helyszíni alkalmazásokat. A következő lépésként [tervezze meg az Azure ad Application proxy üzembe helyezését](./application-proxy-deployment-plan.md) .

## <a name="azure-ad-partner-integrations"></a>Azure AD-partneri integrációk

### <a name="sha-through-networking-and-delivery-controllers"></a>SHA hálózati és kézbesítési vezérlőkön keresztül

Az [Azure ad Application Proxyon](./what-is-application-proxy.md)kívül, hogy lehetővé tegye a [zéró megbízhatósági keretrendszer](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/)használatát, a Microsoft partnerei harmadik féltől származó szolgáltatókkal. Használhatja a meglévő hálózatkezelési és kézbesítési vezérlőit, és egyszerűen biztosíthatja az üzleti folyamatai szempontjából kritikus régebbi alkalmazások, de az Azure AD-vel való védekezését. Valószínűleg már mindent megtalál, amire szüksége lehet az alkalmazások védelmének megkezdéséhez.

![A képek biztonságos hibrid hozzáférést mutatnak a hálózati partnerekkel és az alkalmazás-proxyval](./media/secure-hybrid-access/secure-hybrid-access.png)

A következő hálózati szolgáltatók előre elkészített megoldásokat és részletes útmutatást nyújtanak az Azure AD-vel való integráláshoz.

- [Akamai Enterprise Application Access (EAA)](../saas-apps/akamai-tutorial.md)

- [Citrix Application Delivery Controller (ADC)](../saas-apps/citrix-netscaler-tutorial.md)

- [F5 Big-IP APM](./f5-aad-integration.md)

- [Kemp](../saas-apps/kemp-tutorial.md)

- [Pulse Secure virtuális Traffic Manager (VTM)](../saas-apps/pulse-secure-virtual-traffic-manager-tutorial.md)

### <a name="sha-through-vpn-and-sdp-applications"></a>SHA VPN-és SDP-alkalmazásokon keresztül

A VPN-és SDP-megoldások használatával biztonságos hozzáférést biztosíthat a vállalati hálózathoz bármely eszközről, bármikor, bármilyen helyen, a szervezet adatainak védelme mellett. Azáltal, hogy az Azure AD-t identitás-szolgáltatóként (IDENTITÁSSZOLGÁLTATÓ) használja, modern hitelesítési és engedélyezési módszereket használhat, például az Azure AD [egyszeri bejelentkezést](./what-is-single-sign-on.md) és a [többtényezős hitelesítést](../authentication/concept-mfa-howitworks.md) a helyszíni örökölt alkalmazások biztonságossá tételéhez.  

![A képen a VPN-partnerek és az alkalmazásproxy biztonságos hibrid hozzáférése látható ](./media/secure-hybrid-access/app-proxy-vpn.png)

A következő VPN-szállítók előre elkészített megoldásokat és részletes útmutatást nyújtanak az Azure AD-vel való integráláshoz.

- [Cisco AnyConnect](../saas-apps/cisco-anyconnect.md)

- [Fortinet](../saas-apps/fortigate-ssl-vpn-tutorial.md)

- [F5 Big-IP APM](./f5-aad-password-less-vpn.md)

- [Palo Alto hálózatok – globális védelem](../saas-apps/paloaltoadmin-tutorial.md)

- [Pulse Secure Pulse Secure (számítógépek)](../saas-apps/pulse-secure-pcs-tutorial.md)

A következő SDP-gyártók előre elkészített megoldásokat és részletes útmutatást nyújtanak az Azure AD-vel való integráláshoz.

- [Datawiza hozzáférési közvetítő](./add-application-portal-setup-oidc-sso.md)

- [Perimeter 81](../saas-apps/perimeter-81-tutorial.md)


- [Silverfort hitelesítési platform](./add-application-portal-setup-oidc-sso.md)

- [Strata](../saas-apps/maverics-identity-orchestrator-saml-connector-tutorial.md)

- [Zscaler Private Access (ZPA)](../saas-apps/zscalerprivateaccess-tutorial.md)
