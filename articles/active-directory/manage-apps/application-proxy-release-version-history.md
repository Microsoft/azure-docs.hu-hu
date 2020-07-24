---
title: 'Azure AD Application Proxy: verziók kiadásának előzményei | Microsoft Docs'
description: Ez a cikk felsorolja az Azure AD Application Proxy összes kiadását, és ismerteti az új funkciókat és a rögzített problémákat.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/22/2020
ms.subservice: app-mgmt
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 042509240eb2b88446d3ac1956d9056d5c39dfc8
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87019386"
---
# <a name="azure-ad-application-proxy-version-release-history"></a>Azure AD Application Proxy: verziók kiadásának előzményei
Ez a cikk a kiadott Azure Active Directory (Azure AD) alkalmazásproxy verzióit és funkcióit sorolja fel. Az Azure AD csapata rendszeresen frissíti az alkalmazásproxy új szolgáltatásait és funkcióit. Az alkalmazásproxy-összekötők automatikusan frissülnek, amikor megjelent egy új verzió. 

Javasoljuk, hogy az automatikus frissítések engedélyezve legyenek az összekötők számára a legújabb funkciók és hibajavítások biztosításához. A Microsoft közvetlen támogatást biztosít a legújabb összekötő-verzióhoz és egy korábbi verzióhoz.

A kapcsolódó erőforrások listája:

Erőforrás |  Részletek
--------- | --------- |
Alkalmazásproxy engedélyezése | Ebben az [oktatóanyagban](application-proxy-add-on-premises-application.md)az alkalmazásproxy engedélyezéséhez, valamint az összekötők telepítéséhez és regisztrálásához szükséges előfeltételeket ismertetjük.
Az Azure AD Application Proxy-összekötők ismertetése | További információ az [Összekötők felügyeletéről](application-proxy-connectors.md) és az összekötők [automatikus frissítéséről](application-proxy-connectors.md#automatic-updates).
Azure AD Application Proxy-összekötő letöltése |  [Töltse le a legújabb összekötőt](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download).

## <a name="1519750"></a>1.5.1975.0

### <a name="release-status"></a>Kiadás állapota

Július 22.2020: Ez a verzió csak a letöltési oldalon keresztül telepíthető. Ennek a verziónak az automatikus frissítési kiadása később jelenik meg.

### <a name="new-features-and-improvements"></a>Új funkciók és Újdonságok
-   Továbbfejlesztett Azure Government felhőalapú környezetek támogatása. A Azure Government-felhőhöz tartozó összekötő megfelelő telepítésének lépéseiért tekintse át az [Előfeltételek](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-government-cloud#allow-access-to-urls) és a [telepítés lépéseit](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-government-cloud#install-the-agent-for-the-azure-government-cloud).
- Az Távoli asztali szolgáltatások webes ügyfél és az alkalmazásproxy használatának támogatása. További részletekért tekintse meg az [Azure AD Application Proxy távoli asztal közzétételével foglalkozó](application-proxy-integrate-with-remote-desktop-services.md) témakört.
- Továbbfejlesztett WebSocket-bővítmények egyeztetése. 

### <a name="fixed-issues"></a>Megoldott problémák
- Kijavítva a kisbetűs karakterláncokat kényszerítő WebSocket-probléma.
- Kijavított egy problémát, amely miatt az összekötők alkalmanként nem válaszolnak.

## <a name="1516260"></a>1.5.1626.0

### <a name="release-status"></a>Kiadás állapota

Július 17., 2020: megjelent a letöltéshez. Ez a verzió csak a letöltési oldalon keresztül telepíthető. Ennek a verziónak az automatikus frissítési kiadása később jelenik meg.

### <a name="fixed-issues"></a>Megoldott problémák
- Az előző verzióban található memóriavesztés feloldása probléma
- A WebSocket-támogatás általános fejlesztése

## <a name="1515260"></a>1.5.1526.0

### <a name="release-status"></a>Kiadás állapota

Április 07., 2020: kiadva a letöltéshez

### <a name="new-features-and-improvements"></a>Új funkciók és Újdonságok
-   Az összekötők csak a TLS 1,2-et használják az összes kapcsolathoz. További részletekért lásd az [összekötő előfeltételei](application-proxy-add-on-premises-application.md#before-you-begin) című témakört.
- Továbbfejlesztett jelzés az összekötő és az Azure-szolgáltatások között. Ez magában foglalja az összekötő és az Azure-szolgáltatások közötti WCF-kommunikációt, valamint a WebSocket-kommunikáció DNS-gyorsítótárazási funkcióinak támogatását.
- Proxy konfigurálásának támogatása az összekötő és a háttérbeli alkalmazás között. További információ: a [meglévő helyszíni proxykiszolgálók használata](application-proxy-configure-connectors-with-proxy-servers.md).

### <a name="fixed-issues"></a>Megoldott problémák
- Eltávolítva a 8080-es portra az összekötőről az Azure-szolgáltatásokba való kommunikációhoz.
- Hibakeresési Nyomkövetések hozzáadva a WebSocket-kommunikációhoz. 
- Megoldotta a SameSite attribútum megőrzését a háttérbeli alkalmazás cookie-jai beállításakor.

## <a name="156120"></a>1.5.612.0

### <a name="release-status"></a>Kiadás állapota

Szeptember 20., 2018: kiadva a letöltéshez

### <a name="new-features-and-improvements"></a>Új funkciók és Újdonságok

- A WebSocket-támogatás hozzáadva a QlikSense alkalmazáshoz. A QlikSense és az alkalmazásproxy integrálásával kapcsolatos további tudnivalókért tekintse [meg ezt a](application-proxy-qlik.md)bemutatót. 
- Továbbfejlesztettük a telepítővarázsló segítségével, hogy könnyebben konfiguráljon egy kimenő proxyt. 
- A TLS 1,2 beállítása az összekötők alapértelmezett protokollja. 
- Új végfelhasználói licencszerződés (EULA) lett hozzáadva.  

### <a name="fixed-issues"></a>Megoldott problémák

- Kijavított egy hibát, amely némi memóriavesztés okozta az összekötőt.
- Frissült a Azure Service Bus verziója, amely tartalmazza az összekötő időtúllépési hibáinak elhárítását.

## <a name="154020"></a>1.5.402.0

### <a name="release-status"></a>Kiadás állapota

Január 19., 2018: kiadva letöltésre

### <a name="fixed-issues"></a>Megoldott problémák

- A cookie-ban tartományi fordítást igénylő egyéni tartományok támogatása.

## <a name="151320"></a>1.5.132.0

### <a name="release-status"></a>Kiadás állapota 

2017. május 25., letöltésre kiadva 

### <a name="new-features-and-improvements"></a>Új funkciók és Újdonságok 

Az összekötők kimenő kapcsolati korlátainak jobb szabályozása. 

## <a name="15360"></a>1.5.36.0

### <a name="release-status"></a>Kiadás állapota

Április 15., 2017: kiadva letöltésre

### <a name="new-features-and-improvements"></a>Új funkciók és Újdonságok

- Egyszerűbb előkészítés és felügyelet kevesebb szükséges porttal. Az alkalmazásproxy mostantól csak két szabványos kimenő portot nyit meg: 443 és 80. Az alkalmazásproxy továbbra is csak a kimenő kapcsolatokat használja, így még nincs szüksége a DMZ egyik összetevőjére sem. Részletekért tekintse meg a [konfigurációs dokumentációt](application-proxy-add-on-premises-application.md).  
- Ha a külső proxy vagy tűzfal támogatja, a hálózatot az IP-címtartomány helyett DNS-sel nyithatja meg. Az alkalmazásproxy-szolgáltatások csak a *. msappproxy.net és a *. servicebus.windows.net kapcsolatait igénylik.


## <a name="earlier-versions"></a>Korábbi verziók

Ha a 1.5.36.0-nál korábbi verziójú alkalmazásproxy-összekötőt használ, frissítsen a legújabb verzióra, és győződjön meg arról, hogy a legújabb teljes körűen támogatott funkciókkal rendelkezik.

## <a name="next-steps"></a>További lépések
- További információ [a helyszíni alkalmazások távoli eléréséről az Azure ad Application Proxyon keresztül](application-proxy.md).
- Az alkalmazásproxy használatának megkezdéséhez lásd [: oktatóanyag: helyszíni alkalmazás hozzáadása a táveléréshez alkalmazásproxy](application-proxy-add-on-premises-application.md)használatával.
