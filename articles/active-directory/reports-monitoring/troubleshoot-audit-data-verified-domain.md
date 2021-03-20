---
title: Az ellenőrzött tartományi változások naplózási hibáinak megoldása | Microsoft Docs
description: A Azure Active Directory tevékenység naplófájljaiban megjelenő információkat nyújt a felhasználók ellenőrzött tartományának módosításakor.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/22/2020
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3c9ec3b1e96e47dbf46c6acb2c81147b614d069
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87117429"
---
# <a name="troubleshoot-audit-data-on-verified-domain-change"></a>Hibakeresés: az ellenőrzött tartomány változásának naplózása 


## <a name="i-have-a-lot-of-changes-to-my-users-and-i-am-not-sure-what-the-cause-of-it-is"></a>Sok változásom van a felhasználók számára, és nem vagyok biztos benne, hogy mi az oka.

### <a name="symptoms"></a>Hibajelenségek

Megnézem az Azure AD-naplókat, és az Azure AD-bérlőben több felhasználói frissítés is megjelenik. Ezek a **frissítési felhasználói** események nem jelenítik meg a **szereplők** információit, ami bizonytalanságot okoz a felhasználókra vonatkozó tömeges változások elindításával. 

### <a name="cause"></a>Ok

 A tömeges objektumok változásainak gyakori oka egy **ProxyCalc** nevű, nem szinkron háttérbeli művelet.  A **ProxyCalc** az Azure ad-felhasználók,-csoportok vagy-névjegyek által frissített megfelelő **userPrincipalName** -és **proxy-címeket** meghatározó logika. A **ProxyCalc** mögötti kialakítás azt biztosítja, hogy a **userPrincipalName** és a **proxy-címek** bármikor konzisztensek legyenek az Azure ad-ben. A **ProxyCalc** olyan explicit módosítással kell elindítani, mint az ellenőrzött tartomány változása, és a háttérben nem fut le állandóan tevékenységként. 

  

#### <a name="what-does-userprincipalname-consistency-mean"></a>Mit jelent a UserPrincipalName konzisztencia? 

A csak felhőalapú felhasználók esetében a konzisztencia azt jelenti, hogy a **userPrincipalName** egy ellenőrzött tartományi utótagra van beállítva. Inkonzisztens **userPrincipalName** feldolgozásakor a **ProxyCalc** az alapértelmezett onmicrosoft.com-utótagra konvertálja, például: username@Contoso.onmicrosoft.com 

A szinkronizált felhasználók esetében a konzisztencia azt jelenti, hogy a **userPrincipalName** egy ellenőrzött tartományi utótagra van beállítva, és megfelel a helyszíni **userPrincipalName** értéknek (ShadowUserPrincipalName). Inkonzisztens **userPrincipalName** feldolgozásakor a **ProxyCalc** a **ShadowUserPrincipalName** azonos értékre vált, vagy abban az esetben, ha a tartományi utótag el lett távolítva a bérlőből, a rendszer az alapértelmezett *. onmicrosoft.com tartományi utótagra konvertálja. 

  

#### <a name="what-does-proxy-address-consistency-mean"></a>Mit jelent a proxy-címek konzisztenciája? 

A csak felhőalapú felhasználók esetében a konzisztencia azt jelenti, hogy a proxy címei egy ellenőrzött tartományi utótagnak felelnek meg. Inkonzisztens proxy-címek feldolgozása esetén a **ProxyCalc** az alapértelmezett *. onmicrosoft.com tartományi utótagra konvertálja, például: SMTP:username@Contoso.onmicrosoft.com 

A szinkronizált felhasználók esetében a konzisztencia azt jelenti, hogy a proxy címei megfelelnek a helyszíni proxy címe (i) értékének (azaz ShadowProxyAddresses). A **ProxyAddresses** a **ShadowProxyAddresses** szinkronizálásával várható. Ha a szinkronizált felhasználóhoz Exchange-licenc van hozzárendelve, akkor a proxy címének meg kell egyeznie a helyszíni proxy-cím (ek) értékével, és meg kell egyeznie egy ellenőrzött tartományi utótaggal is. Ebben az esetben a **ProxyCalc** megtisztítja az inkonzisztens proxy-címeket egy nem ellenőrzött tartományi utótaggal, és a rendszer eltávolítja az Azure ad-ből származó objektumból. Ha a nem ellenőrzött tartomány ellenőrzése később megtörtént, a **ProxyCalc** újraszámítja és hozzáadja a **SHADOWPROXYADDRESSES** az Azure ad-ben lévő objektumhoz.  

> [!NOTE]
> A szinkronizált objektumok esetében a nem várt eredmények kiszámításának elkerüléséhez érdemes a **ProxyCalc** beállítani egy Azure ad által ellenőrzött tartományhoz a helyszíni objektumon.  

  
A **ProxyCalc** -t kiváltó rendszergazdai feladatok egyike, amikor ellenőrzött tartományi változás van. Ez a feladat minden alkalommal megtörténik, amikor egy ellenőrzött tartományt hozzáadnak/eltávolítanak egy Azure AD-bérlőből, amely belsőleg elindítja a **ProxyCalc**.  

Ha például egy ellenőrzött tartományi Fabrikam.com ad hozzá a Contoso.onmicrosoft.com-bérlőhöz, ez a művelet egy ProxyCalc műveletet indít el a bérlő összes objektumán. Ezt az eseményt a rendszer az Azure AD-naplókba rögzíti, ha az **ellenőrzött tartomány hozzáadása** esemény előtt **frissíti a felhasználói** eseményeket. Másfelől, ha a Fabrikam.com el lett távolítva a Contoso.onmicrosoft.com-bérlőből, akkor az összes **frissítési felhasználói** eseményt egy, az **ellenőrzött tartomány eltávolítása** esemény előzi meg.   

#### <a name="additional-notes"></a>További megjegyzések:

A ProxyCalc nem okoz változást bizonyos objektumokon: 

- nem rendelkezik aktív Exchange-licenccel 
- NULL értékre van állítva a **MSExchRemoteRecipientType** 
- nem minősül megosztott erőforrásnak. A megosztott erőforrás az, amikor a **CloudMSExchRecipientDisplayType** a következő értékek egyikét tartalmazza: **MailboxUser (Shared)**, **PublicFolder**, **ConferenceRoomMailbox**, **EquipmentMailbox**, **ArbitrationMailbox**, **RoomList,** **TeamMailboxUser**, **csoport postaláda**, **ütemezési postaláda**, **ACLableMailboxUser**, **ACLableTeamMailboxUser** 
  
 A két különálló esemény közötti korreláció kiépítéséhez a Microsoft dolgozik, hogy a naplókban a **szereplők** információinak frissítésével azonosítsa azokat a módosításokat, amelyeket egy ellenőrzött tartományi változás váltott ki. Ez a művelet segít ellenőrizni, hogy az ellenőrzött tartomány változási eseménye lezajlott-e, és elkezdte-e a bérlő objektumainak frissítését. 

Emellett a legtöbb esetben a felhasználók nem módosítják a **userPrincipalName** , és a **proxy címeik** konzisztensek, ezért a naplókban csak azok a frissítések jelennek meg, amelyek az objektum tényleges módosítását okozták. Ez a művelet megakadályozza a naplók zajszintjét, és segít a rendszergazdáknak összekapcsolni a megmaradt módosításokat az ellenőrzött tartományi változási eseményeken a fentiekben leírtak szerint. 

## <a name="next-steps"></a>Következő lépések

[Azure AD Connect Sync szolgáltatás árnyék-attribútumai](../hybrid/how-to-connect-syncservice-shadow-attributes.md)
