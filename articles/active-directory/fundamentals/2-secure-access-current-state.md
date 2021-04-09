---
title: Ismerje meg a külső együttműködés aktuális állapotát Azure Active Directory
description: Az együttműködés aktuális állapotának felderítésére szolgáló módszerek megismerése.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 30858e9978f7e8857c5f8a2dcdfd7455f6e97b60
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102553425"
---
# <a name="discover-the-current-state-of-external-collaboration-in-your-organization"></a>A szervezeten belüli külső együttműködés aktuális állapotának felderítése 

A külső együttműködés aktuális állapotának feltárása előtt meg kell [határoznia a kívánt biztonsági](1-secure-access-posture.md)helyzeteket. A szervezet igényeinek megfelelően központosított és delegált vezérléssel, valamint a vonatkozó irányítási, szabályozási és megfelelőségi célokkal kell rendelkeznie. 

A szervezeten belüli személyek valószínűleg már együttműködnek más szervezetek felhasználóival. Az együttműködés a hatékonyságnövelő alkalmazások, például a Microsoft 365, e-mail-küldés vagy más, külső felhasználókkal való adatmegosztással rendelkező szolgáltatásokkal végezhető el. Az irányítási terv pillérei a felderítés során lesznek megalkotva 
*   a külső együttműködést kezdeményező felhasználók.
*   azok a külső felhasználók és szervezetek, amelyekkel együttműködik.
*   a külső felhasználók számára biztosított hozzáférés.


## <a name="users-initiating-external-collaboration"></a>Külső együttműködést kezdeményező felhasználók

A külső együttműködést kezdeményező felhasználók jobban megismerhetik a külső együttműködéshez leginkább megfelelő alkalmazásokat, és ha a hozzáférés véget ért. A felhasználók megismerése segíthet megállapítani, hogy ki delegált jogosultság a külső felhasználók meghívásához, a hozzáférési csomagok létrehozásához és a hozzáférési felülvizsgálatok befejezéséhez.

A jelenleg együttműködő felhasználók megkereséséhez tekintse át a [Microsoft 365 naplót a megosztási és hozzáférési kérelmekre vonatkozó tevékenységekhez](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance#sharing-and-access-request-activities). Tekintse át az [Azure ad-naplózási naplót is, amely részletesen ismerteti](../external-identities/auditing-and-reporting.md) , hogy ki és mikor hívott meg a B2B-felhasználók címtára

## <a name="find-current-collaboration-partners"></a>Az aktuális együttműködési partnerek keresése

A külső felhasználók lehetnek az [Azure ad B2B-felhasználók](../external-identities/what-is-b2b.md) (lehetőleg) partner által felügyelt hitelesítő adatokkal, illetve helyileg kiosztott hitelesítő adatokkal rendelkező külső felhasználókkal. Ezek a felhasználók jellemzően (de nem mindig) a vendég UserType vannak megjelölve. A vendég felhasználókat a [Microsoft Graph API](/graph/api/user-list?tabs=http), a [PowerShell](/graph/api/user-list?tabs=http)vagy a [Azure Portal](../enterprise-users/users-bulk-download.md)használatával lehet enumerálni.

### <a name="use-email-domains-and-companyname-property"></a>E-mail-tartományok és cégnév-tulajdonság használata

A külső szervezeteket a külső felhasználók e-mail-címeinek tartománynevei határozzák meg. Ha a felhasználói identitás szolgáltatók, például a Google is támogatottak, ez nem lehetséges. Ebben az esetben javasoljuk, hogy a cégnév attribútumot úgy írja be, hogy egyértelműen azonosítsa a felhasználó külső szervezetét.

### <a name="use-allow-or-deny-lists"></a>Engedélyezési vagy megtagadási listák használata

Gondolja át, hogy a szervezete csak bizonyos szervezetekkel kíván-e együttműködést engedélyezni. Azt is megteheti, hogy a szervezet szeretné letiltani az adott szervezetekkel való együttműködést.  A bérlői szinten van egy [engedélyezési vagy megtagadási lista](../external-identities/allow-deny-list.md), amely a forrástól (például a csapatoktól, a sharepointtól és az Azure portaltól függetlenül) teljes B2B-meghívások és beváltások vezérlésére használható.
Ha jogosultság-kezelést használ, a hozzáférési csomagokat a partnerek egy részhalmazához is elérheti az alább látható módon megadott csatlakoztatott szervezetek beállítással.


![Képernyőkép a Megtagadás engedélyezése listáról új hozzáférési csomag létrehozásához.](media/secure-external-access/2-new-access-package.png)


## <a name="find-access-being-granted-to-external-users"></a>A külső felhasználók számára biztosított hozzáférés megkeresése

A külső felhasználók és szervezetek leltározása után a Microsoft Graph API-val meghatározhatja, hogy az Azure AD-csoporttagság vagy a [közvetlen alkalmazás-hozzárendelés](/graph/api/resources/approleassignment) az [Azure ad-](/graph/api/resources/groups-overview) ben milyen hozzáférést kap a felhasználóknak.


### <a name="enumerate-application-specific-permissions"></a>Alkalmazásspecifikus engedélyek számbavétele

Előfordulhat, hogy az alkalmazásspecifikus engedélyek enumerálását is el tudja végezni. Létrehozhat például egy engedélyezési jelentést a SharePoint Online-hoz a [szkript](https://gallery.technet.microsoft.com/office/SharePoint-Online-c9ec4f64)használatával.

Kifejezetten vizsgálja meg az üzleti szempontból bizalmas és az üzleti szempontból kritikus fontosságú alkalmazások hozzáférését, hogy teljes mértékben tisztában legyenek a külső hozzáféréssel.

### <a name="detect-ad-hoc-sharing"></a>Ad hoc megosztás észlelése
Ha e-mail-címe és hálózati csomagjai lehetővé teszik, a tartalmat megvizsgálhatja e-mailben vagy jogosulatlan szoftveres (SaaS) alkalmazásokon keresztül. [Microsoft 365 adatvesztés elleni védelem](/microsoft-365/compliance/data-loss-prevention-policies) segítségével azonosíthatja, megakadályozhatja és figyelheti a bizalmas információk véletlen megosztását a Microsoft 365-infrastruktúrában. A [Microsoft Cloud app Security](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/cloud-app-security) segítségével azonosíthatja a jogosulatlan SaaS-alkalmazások használatát a környezetben.

## <a name="next-steps"></a>Következő lépések

Tekintse meg a következő cikkeket az erőforrásokhoz való külső hozzáférés biztonságossá tételéhez. Javasoljuk, hogy a felsorolt sorrendben végezze el a műveleteket.

1. [A külső hozzáférés biztonsági helyzetének meghatározása](1-secure-access-posture.md)

2. [Fedezze fel aktuális állapotát](2-secure-access-current-state.md) (itt van.)

3. [Irányítási terv létrehozása](3-secure-access-plan.md)

4. [Biztonsági csoportok használata](4-secure-access-groups.md)

5. [Áttérés az Azure AD B2B-re](5-secure-access-b2b.md)

6. [Biztonságos hozzáférés a jogosultságok kezelésével](6-secure-access-entitlement-managment.md)

7. [Biztonságos hozzáférés feltételes hozzáférési szabályzatokkal](7-secure-access-conditional-access.md)

8. [Biztonságos hozzáférés érzékenységi címkékkel](8-secure-access-sensitivity-labels.md)

9. [Biztonságos hozzáférés a Microsoft Teams, a OneDrive és a SharePoint rendszerhez](9-secure-access-teams-sharepoint.md)