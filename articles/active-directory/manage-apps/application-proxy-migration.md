---
title: Frissítés az Azure AD Application Proxyra | Microsoft Docs
description: Válassza ki, hogy melyik proxy-megoldás a legjobb, ha a Microsoft Forefront-vagy Unified Access-átjáróról frissít.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: kenwith
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 52cb4480e8b7820d656b8ab66f12486c2062dca5
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443491"
---
# <a name="compare-remote-access-solutions"></a>Távelérési megoldások összehasonlítása

Azure Active Directory Application Proxy a Microsoft által kínált két távelérési megoldás egyike. A másik a webalkalmazás-proxy, a helyszíni verzió. Ez a két megoldás helyettesíti a Microsoft által kínált korábbi termékeket: Microsoft Forefront Threat Management Gateway (TMG) és Unified Access Gateway (UAG). Ebből a cikkből megtudhatja, hogyan hasonlítják össze ezeket a négy megoldást egymással. Azok számára, akik továbbra is az elavult TMG-vagy UAG-megoldásokat használják, a cikk segítségével megtervezheti az áttelepítést az egyik alkalmazásproxy használatával. 


## <a name="feature-comparison"></a>Szolgáltatások összehasonlítása

Ebből a táblázatból megtudhatja, hogyan hasonlíthatja össze a veszélyforrások kezelési átjáróját (TMG), az egységes hozzáférési átjárót (UAG), a webalkalmazás-proxyt (WAP) és az Azure AD Application Proxy (AP) egymással.

| Szolgáltatás | TMG | UAG | WAP | AP |
| ------- | --- | --- | --- | --- |
| Tanúsítványhitelesítés | Igen | Igen | - | - |
| Böngészőalapú alkalmazások szelektív közzététele | Igen | Igen | Igen | Igen |
| Előhitelesítés és egyszeri bejelentkezés | Igen | Igen | Igen | Igen | 
| Réteg 2/3 tűzfal | Igen | Igen | - | - |
| Proxy-képességek továbbítása | Igen | - | - | - |
| VPN-képességek | Igen | Igen | - | - |
| Sokoldalú protokollok támogatása | - | Igen | Igen, ha HTTP-n keresztül fut | Igen, ha HTTP-n vagy Távoli asztali átjáróon keresztül fut |
| ADFS-proxy kiszolgálóként szolgál | - | Igen | Igen | - |
| Egy portál alkalmazás-hozzáféréshez | - | Igen | - | Igen |
| Válasz törzséhez tartozó hivatkozás fordítása | Igen | Igen | - | Igen | 
| Hitelesítés fejlécekkel | - | Igen | - | Igen, a PingAccess | 
| Felhőbeli biztonság | - | - | - | Igen | 
| Feltételes hozzáférés | - | Igen | - | Igen |
| Nincsenek összetevők a vagy demilitarizált zónában (DMZ) | - | - | - | Igen |
| Nincsenek bejövő kapcsolatok | - | - | - | Igen |

A legtöbb esetben ajánlott az Azure AD Application Proxyt a modern megoldásként ajánlani. A webalkalmazás-proxyt csak olyan helyzetekben érdemes használni, amelyekhez proxykiszolgáló szükséges a AD FShoz, és nem használhat egyéni tartományokat a Azure Active Directoryban. 

Az Azure AD Application Proxy a hasonló termékekhez képest egyedi előnyöket biztosít, többek között:

- Az Azure AD kiterjesztése helyszíni erőforrásokra
   - Felhőbeli biztonság és védelem
   - Az olyan funkciók, mint a feltételes hozzáférés és a Multi-Factor Authentication egyszerűen engedélyezhetők
- Nincsenek összetevők a vagy demilitarizált zónában
- Nincs szükség bejövő kapcsolatra
- Az egyik My apps oldal, amellyel a felhasználók az összes alkalmazáshoz hozzáférhetnek, többek között a Microsoft 365, az Azure AD integrált SaaS-alkalmazásaihoz és a helyszíni webalkalmazásokhoz. 


## <a name="next-steps"></a>Következő lépések

- [Az Azure AD Application Proxy használata biztonságos távoli hozzáférés biztosításához a helyszíni alkalmazásokhoz](application-proxy.md)
