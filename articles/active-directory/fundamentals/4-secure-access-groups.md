---
title: Külső hozzáférés biztonságossá tétele Azure Active Directory és Microsoft 365 csoportjaival
description: Azure Active Directory és Microsoft 365 csoportok használatával növelheti a biztonságot, ha a külső felhasználók hozzáférnek az erőforrásokhoz.
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
ms.openlocfilehash: 3e28714e2557027a3f8f5504f7052973a77720b6
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/23/2020
ms.locfileid: "97744060"
---
# <a name="securing-external-access-with-groups"></a>A külső hozzáférés biztonságossá tétele a csoportokkal 

A csoportok a hozzáférés-vezérlési stratégia lényeges részét képezik. A Azure Active Directory (Azure AD) biztonsági csoportok és Microsoft 365 (M365) csoportok használhatók az erőforrásokhoz való hozzáférés biztosításához.

A csoportok a legjobb választás a következő hozzáférés-vezérlési mechanizmusok alapjául:

* Feltételes hozzáférési szabályzatok

* Jogosultsági felügyeleti hozzáférési csomagok 

* Hozzáférés a M365-erőforrásokhoz, a Microsoft-csapatokhoz és a SharePoint-webhelyekhez

A csoportok a következő szerepkörökkel rendelkeznek:

* Tulajdonosok – a csoport tulajdonosai kezelik a csoport beállításait és a tagságát.

* Tagok – azok a tagok, akik öröklik a csoporthoz rendelt engedélyeket és hozzáférést.

* Vendégek – a vendégek a szervezeten kívüli tagok. 

## <a name="determine-your-group-strategy"></a>A csoport stratégiájának meghatározása

Ahogy fejleszti a csoport stratégiáját az erőforrásaihoz való külső hozzáférés biztonságossá tételéhez, gondolja át a [kívánt biztonsági](1-secure-access-posture.md) helyzetét az alábbi lehetőségek meghatározásához.

* **Kik hozhatnak létre csoportokat?** Csak a rendszergazdák hozhatnak létre csoportokat, vagy ha szeretné, hogy az alkalmazottak és a külső felhasználók is létrehozzák ezeket a csoportokat.

   * *Alapértelmezés szerint minden bérlői tag létrehozhat Azure ad biztonsági csoportokat*. 

      * [A portálon nem rendszergazdák számára is korlátozhatja a hozzáférést](../develop/howto-restrict-your-app-to-a-set-of-users.md) , és letilthatja a csoport létrehozásának képességét a [PowerShellben.](../users-groups-roles/groups-troubleshooting.md) 

      * [Az önkiszolgáló csoportok felügyeletét Azure Active Directory is beállíthatja](../users-groups-roles/groups-self-service-management.md). 

   * *Alapértelmezés szerint minden felhasználó létrehozhat M365-csoportokat és-csoportokat a bérlőben lévő összes (belső és külső) felhasználóhoz való csatlakozáshoz*. 

      * Egy adott biztonsági csoport tagjaira [korlátozhatja Microsoft 365 csoportok létrehozását](https://docs.microsoft.com/microsoft-365/solutions/manage-creation-of-groups?view=o365-worldwide) . A beállítás konfigurálásához használja a Windows PowerShellt. 

* **Ki kell tudnia hívni a személyeket a csoportokra?** Lehet, hogy az összes csoporttagok hozzáadhatnak más tagokat, vagy csak a tulajdonosok tagokat adhatnak hozzá?

* **Kik is meghívhatók csoportokba?** Alapértelmezés szerint a külső felhasználók hozzáadhatók a csoportokhoz. 

### <a name="assign-users-to-groups"></a>Felhasználók hozzárendelése csoportokhoz

A felhasználók a felhasználói objektum felhasználói attribútumai vagy más feltételek alapján manuálisan is hozzárendelhetők a csoportokhoz. A felhasználók csak az attribútumaik alapján adhatók hozzá dinamikusan a csoportokhoz.

Például a következő módon rendelhet hozzá felhasználókat a csoportokhoz:

* megadott beosztás vagy részleg

* partner szervezet, amelyhez tartoznak (manuálisan vagy csatlakoztatott szervezeteken keresztül)

* felhasználó típusa (tag vagy vendég)

* részvétel egy adott projektben (manuálisan)

* location

A dinamikus csoportok akár felhasználókat, akár eszközöket is tartalmazhatnak, de nem mindkettőt. Felhasználói attribútumok alapján adhat hozzá lekérdezéseket a felhasználók a dinamikus csoportba való hozzárendeléséhez. Az alábbi példa azokat a lekérdezéseket mutatja be, amelyekkel felhasználókat vehet fel a csoportba, ha azok tagjai (nem a vendégek) és a pénzügyi részleg.

![Képernyőkép a dinamikus tagsági szabályok konfigurálásáról.](media/secure-external-access/4-dynamic-membership-rules.png)

További információ a dinamikus csoportokról: [dinamikus csoport létrehozása vagy frissítése Azure Active Directoryban.](../users-groups-roles/groups-create-rule.md)

### <a name="do-not-use-groups-for-multiple-purposes"></a>Csoportok ne legyenek több célra felhasználva

Ha biztonsági vagy erőforrás-hozzáférési célokra csoportokat használ, fontos, hogy egyetlen funkcióval rendelkezzenek. Ha egy csoport az erőforrásokhoz való hozzáférés biztosítására szolgál, semmilyen más célra nem használható. Ha egy csoport általános célokra szolgál, például a hely vagy a csoport tagságának meghatározásához, azt nem ajánlott a hozzáférés biztonságossá tételéhez is használni. 

Javasoljuk a biztonsági csoportok elnevezési konvencióját, amely a célt egyértelművé teszi. Például:

* *Secure_access_finance_apps*

* *Team_membership_finance_team*

* *Location_finance_building*



### <a name="types-of-groups"></a>Csoportok típusai

Az Azure ad-portálon vagy a M365 felügyeleti portálon is létrehozhat Azure AD-beli biztonsági csoportokat és Microsoft 365 csoportokat. Mindkét típus használható a külső hozzáférés biztonságossá tételéhez:

|Megfontolandó szempontok | Azure AD biztonsági csoportok (manuális és dinamikus)| Microsoft 365 csoportok |
| - | - | - |
| Mit tartalmaz a csoport?| Felhasználók<br>Csoportok<br>Szolgáltatásnevek<br>Eszközök| Csak felhasználók |
| Hol jön létre a csoport?| Azure AD-portál<br>M365-portál (ha engedélyezve van a levelezés)<br>PowerShell<br>Microsoft Graph<br>Végfelhasználói portál| M365-portál<br>Azure AD-portál<br>PowerShell<br>Microsoft Graph<br>Microsoft 365 alkalmazásokban |
| Kik jönnek létre alapértelmezés szerint?| Rendszergazdák <br>Végfelhasználók| Rendszergazdák<br>Végfelhasználók |
| Kik adhatók hozzá alapértelmezés szerint?| Belső felhasználók (tagok)| Bérlői tagok és vendégek bármely szervezetből |
| Mit biztosít a hozzáférés?| Csak azokat az erőforrásokat, amelyekhez hozzá van rendelve.| Az összes csoporttal kapcsolatos erőforrás:<br>(Postaláda, webhely, csoport, csevegés és egyéb M365-erőforrások)<br>Minden más erőforrás, amelyhez hozzá van adva a csoport |
| Használhatja a| Feltételes hozzáférés<br>Jogosultságok kezelése<br>Csoportos licencelés| Feltételes hozzáférés<br>Jogosultságok kezelése<br>Bizalmassági címkék |



Microsoft 365 csoportok használatával Microsoft 365 erőforrások, például egy csoport, valamint a hozzá tartozó webhelyek és tartalmak hozhatók létre és kezelhetők. Remek választás a projekten alapuló erőfeszítésekhez. 

 

## <a name="azure-ad-security-groups"></a>Azure AD biztonsági csoportok 

Az [Azure ad biztonsági csoportjai](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups) felhasználókat vagy eszközöket tartalmazhatnak, és használhatók a következőhöz való hozzáférés kezelésére 

* Azure-erőforrások, például Microsoft 365 alkalmazások, egyéni alkalmazások és szolgáltatott szoftverek (SaaS), például a Dropbox ServiceNow.

* Azure-beli adatszolgáltatások és-előfizetések.

* Azure-szolgáltatások.

Az Azure AD biztonsági csoportjai a következőket is felhasználhatják:

* rendeljen licenceket olyan szolgáltatásokhoz, mint például a M365, a Dynamics 365, valamint a nagyvállalati mobilitás és biztonság. További információ: [csoport alapú licencelés](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal).

* emelt szintű engedélyek kiosztása. További információ: [felhőalapú csoportok használata a szerepkör-hozzárendelések kezeléséhez (előzetes](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-groups-concept)verzió). 

Egy csoport létrehozásához [a Azure Portal](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal) navigáljon a Azure Active Directory, majd a csoportok elemre. A [PowerShell-parancsmagok](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-settings-v2-cmdlets)használatával Azure ad-beli biztonsági csoportokat is létrehozhat. 

> [!NOTE]
> Egy biztonsági csoport akár 1500 alkalmazás hozzárendelésére is használható, de még nem. 

![Képernyőkép a biztonsági csoport létrehozásáról.](media/secure-external-access/4-create-security-group.png)

> [!IMPORTANT]
> **Levelezési biztonsági csoport létrehozásához nyissa meg a [Microsoft 365 felügyeleti központot](https://admin.microsoft.com/)**. Nem lehet létrehozni az Azure AD-portálon. 
<br>A létrehozás időpontjában engedélyeznie kell egy biztonsági csoportot a levelezéshez. Később nem engedélyezhető.

### <a name="hybrid-organizations-and-azure-ad-security-groups"></a>Hibrid szervezetek és Azure AD-beli biztonsági csoportok

A hibrid szervezetek helyszíni infrastruktúrával és Azure AD-alapú felhőalapú infrastruktúrával is rendelkeznek. Számos, Active Directoryt használó hibrid szervezet hozza létre a helyszíni biztonsági csoportokat, és szinkronizálja őket a felhőbe. Ennek a módszernek a használatával csak a helyszíni környezetben lévő felhasználók adhatók hozzá a biztonsági csoportokhoz.

A helyszíni **infrastruktúrát a biztonsági rések elleni védelem érdekében felhasználhatja a Microsoft 365 bérlőhöz való hozzáféréshez**. Útmutatásért tekintse [meg a helyszíni támadások elleni Microsoft 365 védelmét](https://aka.ms/protectm365) ismertető témakört.

## <a name="microsoft-365-groups"></a>Microsoft 365 csoportok

[Microsoft 365 csoportok](https://docs.microsoft.com/microsoft-365/admin/create-groups/office-365-groups?view=o365-worldwide) a M365 összes hozzáférését biztosító alapszintű tagsági szolgáltatás. Ezek a [Azure Portal](https://portal.azure.com/)vagy a [M365-portálról](https://admin.microsoft.com/)hozhatók létre. Egy M365-csoport létrehozásakor hozzáférést biztosít az együttműködéshez használt erőforrások csoportjához. Az erőforrások teljes listájáért tekintse [meg a rendszergazdák Microsoft 365 csoportok áttekintése](https://docs.microsoft.com/microsoft-365/admin/create-groups/office-365-groups?view=o365-worldwide) című témakört.

A M365-csoportok a következő árnyalatokkal rendelkeznek a szerepköreik számára

* **Tulajdonosok** – a tulajdonosok tulajdonosai hozzáadhatnak vagy eltávolíthatnak tagokat, és egyedi engedélyekkel rendelkezhetnek, mint például a beszélgetések törlésének lehetősége a megosztott Beérkezett fájlok mappából vagy a csoport beállításainak módosítása. A csoport tulajdonosai átnevezhetik a csoportot, frissíthetik a leírást vagy a képet.

* **Tagok** – a tagok hozzáférhetnek a csoport összes tagjához, de nem módosíthatják a csoportok beállításait. Alapértelmezés szerint a csoporttagok meghívhatják a vendégeket a csoportba való csatlakozásra, de [ezt a beállítást is szabályozhatja](https://docs.microsoft.com/microsoft-365/admin/create-groups/manage-guest-access-in-groups?view=o365-worldwide).

* **Vendég** -csoportos vendégek a szervezeten kívüli tagok. A vendégek alapértelmezés szerint bizonyos korlátokkal rendelkeznek a Teams szolgáltatásban.

 

### <a name="m365-group-settings"></a>M365-csoport beállításai

Kiválaszthatja az e-mail aliast, az adatvédelmet, valamint azt, hogy engedélyezi-e a csoportot a csoportok számára a beállítás időpontjában. 

![Képernyőfelvétel Microsoft 365 csoport beállításainak szerkesztéséről](media/secure-external-access/4-edit-group-settings.png)

A telepítés után hozzáadhat tagokat, és konfigurálhatja az e-mailek használatának beállításait stb.

### <a name="next-steps"></a>További lépések

Tekintse meg a következő cikkeket az erőforrásokhoz való külső hozzáférés biztonságossá tételéhez. Javasoljuk, hogy a felsorolt sorrendben végezze el a műveleteket.

1. [A kívánt biztonsági helyzet meghatározása a külső hozzáféréshez](1-secure-access-posture.md)

2. [Aktuális állapot felderítése](2-secure-access-current-state.md)

3. [Irányítási terv létrehozása](3-secure-access-plan.md)

4. [Biztonsági csoportok használata](4-secure-access-groups.md) (itt van.)

5. [Áttérés az Azure AD B2B-re](5-secure-access-b2b.md)

6. [Biztonságos hozzáférés a jogosultságok kezelésével](6-secure-access-entitlement-managment.md)

7. [Biztonságos hozzáférés feltételes hozzáférési szabályzatokkal](7-secure-access-conditional-access.md)

8. [Biztonságos hozzáférés érzékenységi címkékkel](8-secure-access-sensitivity-labels.md)

9. [Biztonságos hozzáférés a Microsoft Teams, a OneDrive és a SharePoint rendszerhez](9-secure-access-teams-sharepoint.md)