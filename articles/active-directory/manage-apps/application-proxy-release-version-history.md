---
title: 'Azure AD Application Proxy: Verziókiadások |} A Microsoft Docs'
description: Ez a cikk az Azure AD-alkalmazásproxy összes kiadása, és ismerteti az új szolgáltatásokat és a rögzített kapcsolatos problémák
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/05/2019
ms.subservice: app-mgmt
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97b54d57a13f0f2b47633a25f1a44efdfa17d11a
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595062"
---
# <a name="azure-ad-application-proxy-version-release-history"></a>Azure AD Application Proxy: Verziókiadások előzményei
Ez a cikk felsorolja és funkciókról az Azure Active Directory (Azure AD) Application Proxy kiadott. Az Azure AD csapata rendszeresen frissíti Application Proxy új szolgáltatásait és funkcióit. Alkalmazásproxy-összekötők automatikusan frissülnek, amikor egy új verziója.

Kapcsolódó erőforrások listáját a következő:

Resource |  Részletek
--------- | --------- |
Alkalmazásproxy engedélyezése | Alkalmazásproxy engedélyezése, telepítése és egy összekötő regisztrálása Előfeltételek leírását a jelen [oktatóanyag](application-proxy-add-on-premises-application.md).
Az Azure AD-alkalmazásproxy-összekötők ismertetése | Tudjon meg többet [összekötő felügyeleti](application-proxy-connectors.md) , és hogyan összekötők [automatikus frissítési](application-proxy-connectors.md#automatic-updates).
Az Azure AD Application Proxy Connector letöltése |  [Töltse le a legújabb összekötőt](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download).

## <a name="156120"></a>1.5.612.0

### <a name="release-status"></a>Kiadási állapot

2018. szeptember 20.: Kiadás dátuma: letöltés

### <a name="new-features-and-improvements"></a>Új funkciók és fejlesztések

- WebSocket támogatása a QlikSense alkalmazás. QlikSense integrálása a proxyval kapcsolatos további információkért lásd: Ez [forgatókönyv](application-proxy-qlik.md). 
- Javult a telepítővarázsló, hogy egyszerűbb legyen a kimenő proxy konfigurálása. 
- A TLS 1.2 állítja be az alapértelmezett protokoll összekötők. 
- Hozzá egy új végfelhasználói licencszerződés (EULA).  

### <a name="fixed-issues"></a>Hibák kijavítva:

- Kijavítva a hiba, által okozott bizonyos memóriavesztés az összekötőben.
- Az Azure Service Bus-verziót, amely magában foglalja a hibajavítás integrálását az összekötő időtúllépési problémák frissítése.

## <a name="154020"></a>1.5.402.0

### <a name="release-status"></a>Kiadási állapot

2018. január 19. óta: Kiadás dátuma: letöltés

### <a name="fixed-issues"></a>Hibák kijavítva:

- Egyéni tartományok, amelyek a cookie-t a tartomány fordítási kell támogatása.

## <a name="151320"></a>1.5.132.0

### <a name="release-status"></a>Kiadási állapot 

2017. május 25.: Kiadás dátuma: letöltés 

### <a name="new-features-and-improvements"></a>Új funkciók és fejlesztések 

Összekötők kimenő kapcsolat korlátai továbbfejlesztett felett. 

## <a name="15360"></a>1.5.36.0

### <a name="release-status"></a>Kiadási állapot

2017. április 15.: Kiadás dátuma: letöltés

### <a name="new-features-and-improvements"></a>Új funkciók és fejlesztések

- Egyszerűsített bevezetése és kezelése a kevesebb szükséges portokat. Alkalmazásproxy most már csak két standard szintű kimenő portok megnyitása van szükség: 443-as és a 80-as. Alkalmazásproxy használata csak kimenő kapcsolatokat, így még mindig nem szükséges a Szegélyhálózaton lévő összetevők továbbra is. További információkért lásd: a [konfigurációs dokumentációt](application-proxy-add-on-premises-application.md).  
- Ha támogatja a külső proxy vagy tűzfal, mostantól megnyithatja a hálózat DNS-helyett IP-címtartomány. Application Proxy services-kapcsolatok szükségesek *. msappproxy.net és *. servicebus.windows.net csak.


## <a name="earlier-versions"></a>Korábbi verziók

Ha egy alkalmazásproxy-összekötő verziójának rendszernél korábbi 1.5.36.0 használ, frissítse a a legújabb verzióra, ellenőrizze, hogy a legújabb funkciók teljes mértékben támogatottak.

## <a name="next-steps"></a>További lépések
- Tudjon meg többet [távoli hozzáférést a helyszíni alkalmazások az Azure AD-alkalmazásproxyn keresztül](application-proxy.md).
- Az alkalmazásproxy használatának megkezdéséhez lásd [oktatóanyag: A távoli hozzáféréshez alkalmazásproxyn keresztül a helyszíni alkalmazás hozzáadása](application-proxy-add-on-premises-application.md).
