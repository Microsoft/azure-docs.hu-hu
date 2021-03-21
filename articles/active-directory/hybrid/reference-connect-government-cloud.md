---
title: 'Azure AD Connect: a hibrid identitás szempontjai Azure Government felhőhöz'
description: A Azure AD Connect Azure Government felhővel való üzembe helyezésének különleges szempontjai.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: bbb4298d0d9a9d7589c4a2055b4d55a0b852f951
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97504355"
---
# <a name="hybrid-identity-considerations-for-the-azure-government-cloud"></a>Hibrid identitási megfontolások a Azure Government felhőhöz

Ez a cikk a hibrid környezetek Microsoft Azure Government felhővel való integrálásával kapcsolatos szempontokat ismerteti. Ez az információ a Azure Government felhővel dolgozó rendszergazdák és építészek számára készült referenciául szolgál.

> [!NOTE]
> Ahhoz, hogy integrálni lehessen a Microsoft Active Directory környezetét (akár helyszíni, akár egy olyan IaaS, amely ugyanabban a felhőalapú példányban található Azure Government), frissítenie kell a [Azure ad Connect](https://www.microsoft.com/download/details.aspx?id=47594)legújabb kiadására.

Egyesült Államok kormányzati védelmi végpontok teljes listáját a [dokumentációban](/office365/enterprise/office-365-u-s-government-dod-endpoints)találja.

## <a name="azure-ad-pass-through-authentication"></a>Azure AD átmenő hitelesítés

Az alábbi információk az átmenő hitelesítés és a Azure Government felhő megvalósítását ismertetik.

### <a name="allow-access-to-urls"></a>URL-címek elérésének engedélyezése

Az átmenő hitelesítési ügynök üzembe helyezése előtt ellenőrizze, hogy létezik-e tűzfal a kiszolgálók és az Azure AD között. Ha a tűzfal vagy a proxy lehetővé teszi a DNS-blokkolt vagy biztonságos programok használatát, adja hozzá a következő kapcsolatokat.

> [!NOTE]
> Az alábbi útmutató az [Azure ad Application proxy-összekötő](../manage-apps/what-is-application-proxy.md) Azure Government környezetekben történő telepítésére is vonatkozik.

|URL-cím |Használat célja|
|-----|-----|
|&#42;. msappproxy.us</br>&#42;.servicebus.usgovcloudapi.net|Az ügynök ezeket az URL-címeket használja az Azure AD Cloud Service-vel való kommunikációhoz. |
|`mscrl.microsoft.us:80` </br>`crl.microsoft.us:80` </br>`ocsp.msocsp.us:80` </br>`www.microsoft.us:80`| Az ügynök ezeket az URL-címeket használja a tanúsítványok ellenőrzéséhez.|
|login.windows.us </br>secure.aadcdn.microsoftonline-p.com </br>&#42;. microsoftonline.us </br>&#42;. microsoftonline-p.us </br>&#42;. msauth.net </br>&#42;. msauthimages.net </br>&#42;. msecnd.net</br>&#42;. msftauth.net </br>&#42;. msftauthimages.net</br>&#42;. phonefactor.net </br>enterpriseregistration.windows.net</br>management.azure.com </br>policykeyservice.dc.ad.msft.net</br>ctldl.windowsupdate.us:80| Az ügynök ezeket az URL-címeket használja a regisztrációs folyamat során.

### <a name="install-the-agent-for-the-azure-government-cloud"></a>A Azure Government-felhő ügynökének telepítése

Kövesse az alábbi lépéseket a Azure Government-felhőhöz tartozó ügynök telepítéséhez:

1. A parancssori terminálban nyissa meg azt a mappát, amely az ügynököt telepítő végrehajtható fájlt tartalmazza.
1. Futtassa a következő parancsokat, amelyek meghatározzák, hogy a telepítés Azure Government.

   Átmenő hitelesítés esetén:

   ```
   AADConnectAuthAgentSetup.exe ENVIRONMENTNAME="AzureUSGovernment"
   ```

   Alkalmazásproxy esetén:

   ```
   AADApplicationProxyConnectorInstaller.exe ENVIRONMENTNAME="AzureUSGovernment" 
   ```

## <a name="single-sign-on"></a>Egyszeri bejelentkezés

### <a name="set-up-your-azure-ad-connect-server"></a>A Azure AD Connect-kiszolgáló beállítása

Ha a bejelentkezési módszere átmenő hitelesítést használ, nincs szükség további előfeltétel-ellenőrzésre. Ha bejelentkezési módszerként használja a jelszó-kivonatoló szinkronizálást, és a Azure AD Connect és az Azure AD között tűzfal található, ügyeljen a következőkre:

- Azure AD Connect 1.1.644.0 vagy újabb verziót használ.
- Ha a tűzfal vagy a proxy engedélyezi a DNS által letiltott vagy biztonságos programok használatát, vegye fel a kapcsolatot a &#42;. msappproxy.us URL-címekkel a 443-es porton keresztül.

  Ha nem, engedélyezze a hozzáférést az Azure Datacenter IP-tartományokhoz, amelyek hetente frissülnek. Ez az előfeltétel csak akkor érvényes, ha engedélyezi a szolgáltatást. A tényleges felhasználói bejelentkezésekhez nem szükséges.

### <a name="roll-out-seamless-single-sign-on"></a>Zökkenőmentes egyszeri Sign-On

Az alábbi utasítások segítségével fokozatosan kiderítheti az Azure AD zökkenőmentes egyszeri Sign-On a felhasználók számára. Első lépésként adja hozzá az Azure AD URL-címét az `https://autologon.microsoft.us` összes vagy a kiválasztott felhasználó intranetes zónájának beállításaihoz a Active Directory csoportházirend használatával.

Az intranetes zóna házirend-beállításának engedélyezéséhez **parancsfájlon keresztül is engedélyeznie kell a frissítések állapotjelző sávján keresztül csoportházirend**.

## <a name="browser-considerations"></a>A böngésző szempontjai

### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox (minden platform)

A Mozilla Firefox nem használ automatikusan Kerberos-hitelesítést. Az alábbi lépéseket követve minden felhasználónak manuálisan hozzá kell adnia az Azure AD URL-címét a Firefox-beállításaihoz:

1. Futtassa a Firefox programot, és írja be a **about: config** parancsot   a címsorba. Zárja be az esetlegesen megjelenő értesítéseket.
1. Keressen rá a **Network. Negotiate – auth. megbízható – URI-** k   beállításra. Ez a beállítás felsorolja a Firefox által a Kerberos-hitelesítéshez megbízhatóként használt helyeket.
1. Kattintson a jobb gombbal a preferencia nevére, majd válassza a **módosítás** menüpontot.
1. Írja be `https://autologon.microsoft.us` a mezőt a mezőbe.
1. Kattintson **az OK gombra**   , majd nyissa meg újra a böngészőt.

### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Microsoft Edge a Chromium alapján (minden platform)

Ha felülbírálta a környezetben lévő  `AuthNegotiateDelegateAllowlist`   vagy `AuthServerAllowlist`   házirend-beállításokat, ügyeljen arra, hogy hozzáadja az Azure ad URL-címét `https://autologon.microsoft.us` .

### <a name="google-chrome-all-platforms"></a>Google Chrome (minden platform)

Ha felülbírálta a környezetben lévő  `AuthNegotiateDelegateWhitelist`   vagy `AuthServerWhitelist`   házirend-beállításokat, ügyeljen arra, hogy hozzáadja az Azure ad URL-címét `https://autologon.microsoft.us` .

## <a name="next-steps"></a>Következő lépések

- [Átmenő hitelesítés](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)
- [Egyszeri bejelentkezés](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites)
