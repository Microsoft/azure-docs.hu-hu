---
title: Vendégfelhasználók hozzáférési engedélyeinek korlátozása – Azure Active Directory | Microsoft Docs
description: A vendégfelhasználók hozzáférési engedélyeinek korlátozása a Azure Portal, a PowerShell vagy Microsoft Graph használatával Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 01/14/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: df4cb32720d80dd23289be7e760c9934e9a8db8a
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107501501"
---
# <a name="restrict-guest-access-permissions-preview-in-azure-active-directory"></a>Vendég-hozzáférési engedélyek korlátozása (előzetes verzió) a Azure Active Directory

Azure Active Directory (Azure AD) lehetővé teszi annak korlátozását, hogy a külső vendégfelhasználók mit láthatnak a szervezetben az Azure AD-ban. A vendégfelhasználók alapértelmezés szerint korlátozott engedélyszintre vannak beállítva az Azure AD-ban, míg a tagfelhasználók alapértelmezett beállítása az alapértelmezett felhasználói engedélyek teljes készlete. Ez az Azure AD-szervezet külső együttműködési beállításainak új vendégfelhasználói engedélyszintje előzetes verziója, amely még korlátozottabb hozzáférést biztosít, így a vendégelérési lehetőségek mostantól a következőek:

Engedélyszint         | Hozzáférési szint | Érték
----------------         | ------------ | -----
Ugyanaz, mint a tagfelhasználók     | A vendégek ugyanolyan hozzáféréssel férnek hozzá az Azure AD-erőforrásokhoz, mint a tagfelhasználók | a0b1b346-4d3e-4e8b-98f8-753987be4970
Korlátozott hozzáférés (alapértelmezett) | A vendégek láthatják az összes nem rejtett csoport tagságát | 10dae51f-b6af-4016-8d66-8c2a99b929b3
**Korlátozott hozzáférés (új)**  | **A vendégek nem láthatják egyik csoport tagságát sem** | **2af84b1e-32c8-42b7-82bc-daa82404023b**

Ha a vendég hozzáférése korlátozott, a vendégek csak a saját felhasználói profiljukat megtekinthetik. Más felhasználók megtekintésére vonatkozó engedély akkor sem engedélyezett, ha a vendég egyszerű felhasználónév vagy objectId alapján keres. A korlátozott hozzáférés azt is korlátozza, hogy a vendégfelhasználók ne látják az őket használó csoportok tagságát. További információ az általános alapértelmezett felhasználói engedélyekről, beleértve a vendégfelhasználói engedélyeket: Mik az alapértelmezett felhasználói engedélyek a [Azure Active Directory.](../fundamentals/users-default-permissions.md)

## <a name="permissions-and-licenses"></a>Engedélyek és licencek

A külső együttműködési beállítások konfiguráláshoz globális rendszergazdai szerepkörben kell lennie. A vendég hozzáférésének korlátozására nincsenek további licencelési követelmények.

## <a name="update-in-the-azure-portal"></a>Frissítés a Azure Portal

Módosításokat végrehajtottunk a vendégfelhasználói engedélyek Azure Portal vezérlőiben.

1. Jelentkezzen be az [Azure AD](https://aad.portal.azure.com) felügyeleti központba globális rendszergazda engedélyekkel.
1. A **Azure Active Directory** áttekintési lapján válassza a Felhasználói **beállítások lehetőséget.**
1. A **Külső felhasználók alatt** válassza a Külső együttműködési beállítások kezelése **lehetőséget.**
1. A Külső **együttműködési beállítások lapon** válassza a **Vendégfelhasználói** hozzáférés a saját címtárobjektumaik tulajdonságaira és tagságára van korlátozva lehetőséget.

    ![Az Azure AD külső együttműködési beállításainak oldala](./media/users-restrict-guest-permissions/external-collaboration-settings.png)

1. Kattintson a **Mentés** gombra. A módosítások a vendégfelhasználók számára 15 percet is igénybe vehatnak.

## <a name="update-with-the-microsoft-graph-api"></a>Frissítés a Microsoft Graph API-val

Hozzáadtunk egy új Microsoft Graph API-t, amely konfigurálja a vendégengedélyeket az Azure AD-szervezetben. A következő API-hívások bármilyen engedélyszint hozzárendelésére használhatók. A guestUserRoleId itt használt értéke a legnagyobb korlátozott vendégfelhasználói beállítás szemléltetésére használható. A vendégengedélyek beállítására Microsoft Graph további információért lásd: [authorizationPolicy erőforrástípus.](/graph/api/resources/authorizationpolicy)

### <a name="configuring-for-the-first-time"></a>Konfigurálás első alkalommal

````PowerShell
POST https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy

{
  "guestUserRoleId": "2af84b1e-32c8-42b7-82bc-daa82404023b"
}
````

A válasznak 204-es sikernek kell lennie.

### <a name="updating-the-existing-value"></a>A meglévő érték frissítése

````PowerShell
PATCH https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy

{
  "guestUserRoleId": "2af84b1e-32c8-42b7-82bc-daa82404023b"
}
````

A válasznak 204-es sikernek kell lennie.

### <a name="view-the-current-value"></a>Az aktuális érték megtekintése

````PowerShell
GET https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy
````

Példaválasz:

````PowerShell
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#policies/authorizationPolicy/$entity",
    "id": "authorizationPolicy",
    "displayName": "Authorization Policy",
    "description": "Used to manage authorization related settings across the company.",
    "enabledPreviewFeatures": [],
    "guestUserRoleId": "10dae51f-b6af-4016-8d66-8c2a99b929b3",
    "permissionGrantPolicyIdsAssignedToDefaultUserRole": [
        "user-default-legacy"
    ]
}
````

## <a name="update-with-powershell-cmdlets"></a>Frissítés PowerShell-parancsmagokkal

Ezzel a funkcióval a korlátozott engedélyek PowerShell v2-parancsmagokkal konfigurálhatóak. A Get és Set PowerShell-parancsmagok a 2.0.2.85-ös verzióban vannak közzétéve.

### <a name="get-command-get-azureadmsauthorizationpolicy"></a>Get parancs: Get-AzureADMSAuthorizationPolicy

Példa:

````PowerShell
PS C:\WINDOWS\system32> Get-AzureADMSAuthorizationPolicy

Id                                                : authorizationPolicy
OdataType                                         :
Description                                       : Used to manage authorization related settings across the company.
DisplayName                                       : Authorization Policy
EnabledPreviewFeatures                            : {}
GuestUserRoleId                                   : 10dae51f-b6af-4016-8d66-8c2a99b929b3
PermissionGrantPolicyIdsAssignedToDefaultUserRole : {user-default-legacy}
````

### <a name="set-command-set-azureadmsauthorizationpolicy"></a>Parancs beállítása: Set-AzureADMSAuthorizationPolicy

Példa:

````PowerShell
PS C:\WINDOWS\system32> Set-AzureADMSAuthorizationPolicy -GuestUserRoleId '2af84b1e-32c8-42b7-82bc-daa82404023b'
````

> [!NOTE]
> Ha a rendszer kéri, adja meg az authorizationPolicy azonosítót.

## <a name="supported-microsoft-365-services"></a>Támogatott Microsoft 365 szolgáltatások

### <a name="supported-services"></a>Támogatott szolgáltatások

A támogatottak alatt azt jelenti, hogy a felhasználói élmény a vártnak megfelelő; pontosabban, hogy ugyanaz, mint a jelenlegi vendégélmény.

- Teams
- Outlook (OWA)
- SharePoint
- Planner a Teamsben
- Planner-webalkalmazás

### <a name="services-currently-not-supported"></a>A szolgáltatások jelenleg nem támogatottak

A jelenlegi támogatás nélküli szolgáltatás kompatibilitási problémákat okozhat az új vendégkorlátozási beállítással.

- Űrlapok
- Planner mobilalkalmazás
- Project
- Yammer

## <a name="frequently-asked-questions-faq"></a>Gyakori kérdések (GYIK)

Kérdés | Válasz
-------- | ------
Hol érvényesek ezek az engedélyek? | Ezek a címtárszintű engedélyek minden Azure AD-szolgáltatásban és -portálon érvényesítve vannak, beleértve a Microsoft Graph, a PowerShell v2, a Azure Portal és Saját alkalmazások portálon. Microsoft 365 csoportok együttműködési forgatókönyvekben való használatát Microsoft 365 szolgáltatásokra, különösen az Outlookra, a Microsoft Teamsre és a SharePointra is hatással van.
Hogyan befolyásolják a korlátozott engedélyek, hogy mely csoportokat láthatják a vendégek? | Az alapértelmezett vagy korlátozott vendégengedélytől függetlenül a vendégek nem számba semeheti a csoportok vagy felhasználók listáját. A vendégek az engedélyektől függően a Azure Portal és a Saját alkalmazások is láthatják a csoportokat, amelyek tagjai:<li>**Alapértelmezett engedélyek:** A vendégnek meg kell keresnie az objektumazonosítóját a Minden felhasználó  listában, majd a Csoportok lehetőséget választva meg kell keresnie azokat a csoportokat, amelyek tagjai a Azure Portal. Itt láthatják azon csoportok listáját, amelyek tagjai, beleértve a csoport összes részletét, beleértve a nevet, az e-mailt és így tovább. A Saját alkalmazások a felhasználók láthatják a tulajdonában és a csoportjukban található csoportokat.</li><li>**Korlátozott vendégengedélyek:** A Azure Portal továbbra is megkeresik a csoportlistát, ha rákeresnek az objektumazonosítójukra a Minden felhasználó listában, majd kiválasztják a Csoportok lehetőséget. Csak nagyon korlátozott részleteket láthatnak a csoportról, különösen az objektumazonosítóról. A Név és az E-mail oszlop alapértelmezés szerint üres, a Csoport típusa pedig Ismeretlen. A Saját alkalmazások portálon nem tudják elérni a tulajdonában vagy a csoportjukban található csoportokat.</li><br>A címtárakból származó címtárengedélyek részletesebb összehasonlításához lásd Graph API alapértelmezett felhasználói [engedélyeket.](../fundamentals/users-default-permissions.md#member-and-guest-users)
A portál mely részeit Saját alkalmazások ez a funkció? | A csoportfunkciók a Saját alkalmazások az új engedélyeket. Ebbe beletartozik az összes elérési út a csoportlista és a csoporttagság megtekintéséhez a Saját alkalmazások. A csoportcsempe rendelkezésre állása nem módosult. A csoportcsempe elérhetőségét továbbra is a meglévő csoportbeállítás vezérli a Azure Portal.
Felülbírálják ezek az engedélyek a SharePoint- vagy Microsoft Teams-vendégbeállításokat? | Nem. Ezek a meglévő beállítások továbbra is az alkalmazások felhasználói élményét és hozzáférését irányítják. Ha például problémákat lát a SharePointban, ellenőrizze a külső megosztási beállításokat.
Melyek a Planner és a Yammer ismert kompatibilitási problémái? | <li>Ha az engedélyek beállítása "korlátozott", a Planner mobilalkalmazásba bejelentkezett vendégek nem férhetnek hozzá a csomagjaikhoz vagy a feladataikhoz.<li>Ha az engedélyek beállítása "korlátozott", a Yammerbe bejelentkezett vendégek nem hagyhatják el a csoportot.
Módosulnak a meglévő vendégengedélyek a bérlőmben? | Az aktuális beállítások nem módosultak. Fenntartjuk a visszamenőleges kompatibilitást a meglévő beállításokkal. Ön dönti el, hogy mikor szeretne módosításokat.
Alapértelmezés szerint be lesznek állítva ezek az engedélyek? | Nem. A meglévő alapértelmezett engedélyek változatlanok maradnak. Az engedélyeket szigorúbbra is beállíthatja.
Vonatkoznak licenckövetelmények ehhez a funkcióhoz? | Nem, ezzel a funkcióval nincsenek új licencelési követelmények.

## <a name="next-steps"></a>Következő lépések

- Az Azure AD-beli meglévő vendégengedélyekkel kapcsolatos további információkért lásd: Mik az alapértelmezett felhasználói engedélyek a [Azure Active Directory?](../fundamentals/users-default-permissions.md)
- A vendégelérés Microsoft Graph API-metódusokkal kapcsolatos további információkért lásd: [authorizationPolicy erőforrástípus](/graph/api/resources/authorizationpolicy)
- Egy felhasználó összes hozzáférésének visszavonása: Felhasználói hozzáférés visszavonása az [Azure AD-ban](users-revoke-access.md)
