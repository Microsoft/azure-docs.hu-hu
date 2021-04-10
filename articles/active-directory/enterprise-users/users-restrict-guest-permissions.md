---
title: Vendég felhasználói hozzáférési engedélyek korlátozása – Azure Active Directory | Microsoft Docs
description: A vendég felhasználói hozzáférési engedélyek korlátozása a Azure Portal, a PowerShell vagy a Microsoft Graph használatával Azure Active Directory
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
ms.openlocfilehash: bf2d0d3335468147575eb53a99940866baa18375
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98222521"
---
# <a name="restrict-guest-access-permissions-preview-in-azure-active-directory"></a>A vendég hozzáférési engedélyei (előzetes verzió) korlátozása Azure Active Directory

Azure Active Directory (Azure AD) segítségével korlátozhatja, hogy a külső vendég felhasználók hogyan láthatják a szervezetében az Azure AD-ben. Az Azure AD alapértelmezés szerint korlátozott jogosultsági szintre állítja a vendég felhasználókat, míg a felhasználók alapértelmezés szerint az alapértelmezett felhasználói engedélyek teljes készletét használják. Ez az Azure AD-szervezet külső együttműködési beállításaiban található új vendég felhasználói jogosultsági szint előzetes verziója, amely még korlátozottabb hozzáférést biztosít, így a vendég hozzáférési lehetőségei mostantól a következők:

Engedélyszint         | Hozzáférési szint
----------------         | ------------
Ugyanaz, mint a tag felhasználói     | A vendégek ugyanolyan hozzáféréssel rendelkeznek az Azure AD-erőforrásokhoz, mint a tag felhasználói
Korlátozott hozzáférés (alapértelmezett) | A vendégek az összes nem rejtett csoport tagságát láthatják
**Korlátozott hozzáférés (új)**  | **A vendégek nem láthatják a csoportok tagságát**

Ha a vendég hozzáférése korlátozott, a vendégek csak a saját felhasználói profilt tekinthetik meg. A többi felhasználó megtekintésére vonatkozó engedély nem engedélyezett, még akkor sem, ha a vendég egyszerű felhasználónevet vagy objectId keres. A korlátozott hozzáférés azt is korlátozza, hogy a vendég felhasználók láthassák a csoportok tagságát. További információ az alapértelmezett felhasználói engedélyekről, beleértve a vendég felhasználói engedélyeket is: [Mik az alapértelmezett felhasználói engedélyek a Azure Active Directory-ben?](../fundamentals/users-default-permissions.md).

## <a name="permissions-and-licenses"></a>Engedélyek és licencek

A külső együttműködési beállítások konfigurálásához globális rendszergazdai szerepkörrel kell rendelkeznie. A vendég-hozzáférés korlátozására nincsenek további licencelési követelmények.

## <a name="update-in-the-azure-portal"></a>Frissítés a Azure Portal

Módosítottuk a vendég felhasználói engedélyeinek meglévő Azure Portal vezérlőit.

1. Jelentkezzen be az [Azure ad felügyeleti központba](https://aad.portal.azure.com) globális rendszergazdai engedélyekkel.
1. A szervezet **Azure Active Directory** áttekintés lapján válassza a **felhasználói beállítások** lehetőséget.
1. A **külső felhasználók** területen válassza a **külső együttműködési beállítások kezelése** lehetőséget.
1. A **külső együttműködési beállítások** lapon válassza a **vendég felhasználói hozzáférés lehetőséget a saját címtár-objektumok tulajdonságai és tagsága** lehetőségre.

    ![Az Azure AD külső együttműködési beállítások lapja](./media/users-restrict-guest-permissions/external-collaboration-settings.png)

1. Kattintson a **Mentés** gombra. A módosítások akár 15 percet is igénybe vehetnek a vendég felhasználóinak érvénybe léptetéséhez.

## <a name="update-with-the-microsoft-graph-api"></a>Frissítés a Microsoft Graph API-val

Új Microsoft Graph API-t adtunk hozzá a vendég engedélyeinek konfigurálásához az Azure AD-szervezetben. A következő API-hívások bármelyik jogosultsági szint hozzárendelésére használhatók. Az itt használt guestUserRoleId értéke a legtöbb korlátozott vendég felhasználói beállítás szemléltetése. További információ a Microsoft Graph használatáról a vendég engedélyeinek beállításához: [authorizationPolicy erőforrástípus](/graph/api/resources/authorizationpolicy).

### <a name="configuring-for-the-first-time"></a>Konfigurálás első alkalommal

````PowerShell
POST https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy

{
  "guestUserRoleId": "2af84b1e-32c8-42b7-82bc-daa82404023b"
}
````

A válasznak 204 sikeresnek kell lennie.

### <a name="updating-the-existing-value"></a>A meglévő érték frissítése

````PowerShell
PATCH https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy

{
  "guestUserRoleId": "2af84b1e-32c8-42b7-82bc-daa82404023b"
}
````

A válasznak 204 sikeresnek kell lennie.

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

Ezzel a funkcióval lehetőség van a korlátozott engedélyek konfigurálására a PowerShell V2-parancsmagok használatával. A PowerShell-parancsmagok beolvasása és beállítása a 2.0.2.85 verzióban történt.

### <a name="get-command-get-azureadmsauthorizationpolicy"></a>Parancs beolvasása: Get-AzureADMSAuthorizationPolicy

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

### <a name="set-command-set-azureadmsauthorizationpolicy"></a>Set parancs: Set-AzureADMSAuthorizationPolicy

Példa:

````PowerShell
PS C:\WINDOWS\system32> Set-AzureADMSAuthorizationPolicy -GuestUserRoleId '2af84b1e-32c8-42b7-82bc-daa82404023b'
````

> [!NOTE]
> A authorizationPolicy megadásakor AZONOSÍTÓként kell megadnia a kérést.

## <a name="supported-microsoft-365-services"></a>Támogatott Microsoft 365 szolgáltatások

### <a name="supported-services"></a>Támogatott szolgáltatások

Támogatott módon azt értjük, hogy a tapasztalatok a várt módon működnek. pontosabban, hogy ugyanaz, mint az aktuális vendég élmény.

- Teams
- Outlook (OWA)
- SharePoint
- Planner a Teams szolgáltatásban
- Planner webalkalmazás

### <a name="services-currently-not-supported"></a>A szolgáltatások jelenleg nem támogatottak

Az aktuális támogatás nélküli szolgáltatás kompatibilitási problémákba ütközhet az új vendég korlátozási beállítással.

- Űrlapok
- Planner Mobile-alkalmazás
- Project
- Yammer

## <a name="frequently-asked-questions-faq"></a>Gyakori kérdések (GYIK)

Kérdés | Válasz
-------- | ------
Hová kell alkalmazni ezeket az engedélyeket? | Ezek a címtár-szintű engedélyek az Azure AD-szolgáltatások és-portálok esetében érvényesek, beleértve a Microsoft Graph, a PowerShell V2, a Azure Portal és a saját alkalmazások portált. Az együttműködési forgatókönyvekhez Microsoft 365 csoportokat kihasználó Microsoft 365-szolgáltatások is érintettek, különösen az Outlook, a Microsoft Teams és a SharePoint.
Hogyan befolyásolják a korlátozott engedélyek, hogy mely csoportok láthatják a vendégeket? | Az alapértelmezett vagy a korlátozott vendég engedélyeitől függetlenül a vendégek nem tudják enumerálni a csoportok vagy felhasználók listáját. Az engedélyektől függően a vendégek a Azure Portal és a saját alkalmazások portálon is megtekinthetik a csoportok tagjait:<li>**Alapértelmezett engedélyek**: Ha meg szeretné keresni azokat a csoportokat, amelyek tagjai a Azure Portalnak, a vendégnek a **minden felhasználó** listában meg kell KERESNIe az objektum azonosítóját, majd ki kell választania a **csoportokat**. Itt láthatják azoknak a csoportoknak a listáját, amelyeknek tagjai, beleértve az összes csoport részleteit, beleértve a nevet, az e-maileket stb. A saját alkalmazások portálon megtekinthetik azoknak a csoportoknak a listáját, amelyeknek a tulajdonosai és csoportjaik tagjai.</li><li>**Korlátozott vendég engedélyei**: a Azure Portalban továbbra is megtalálják azoknak a csoportoknak a listáját, amelyeknek a tagjai a minden felhasználó listában megkeresik a hozzájuk tartozó objektumazonosítók keresését, majd kiválasztja a csoportok lehetőséget. A csoportnak csak nagyon kevés részlete látható, különösen az objektumazonosító. A tervezés szerint a név és az e-mail-oszlopok üresek, és a csoport típusa nem ismerhető fel. A saját alkalmazások portálon nem férhetnek hozzá azok a csoportok, amelyeknek a tulajdonosai vagy csoportjaik tagjai.</li><br>A Graph APIből származó címtár-engedélyek részletesebb összehasonlítását lásd: [alapértelmezett felhasználói engedélyek](../fundamentals/users-default-permissions.md#member-and-guest-users).
A My apps portál mely részeit érinti ez a funkció? | A saját alkalmazások portál csoportok funkciója tiszteletben tartja ezeket az új engedélyeket. Ide tartozik a csoportok listájának és a csoporttagságok a saját alkalmazásokban való megtekintéséhez szükséges összes elérési út. Nem történt változás a csoport csempe rendelkezésre állása során. A csoport csempe rendelkezésre állását továbbra is a Azure Portal meglévő csoportjának beállításai vezérlik.
Felülbírálják ezeket az engedélyeket a SharePoint vagy a Microsoft Teams vendég beállításainak? | Nem. Ezek a meglévő beállítások továbbra is szabályozzák az alkalmazások élményét és elérését. Ha például problémát tapasztal a SharePointban, ellenőrizze a külső megosztási beállításokat.
Mik a Planner és a Yammer ismert kompatibilitási problémái? | <li>Ha a "korlátozott" értékre van állítva, a Planner Mobile alkalmazásba bejelentkezett vendégek nem fognak tudni hozzáférni a csomagokhoz vagy a feladatokhoz.<li>Ha az engedélyek "korlátozott" értékre vannak beállítva, a Yammer-be bejelentkezett vendégek nem hagyhatják el a csoportot.
Módosulnak a meglévő vendég engedélyei a bérlőn? | Nem történt változás az aktuális beállításokban. A meglévő beállításokkal visszamenőleges kompatibilitást biztosítunk. Ön dönti el, hogy szeretne-e módosításokat végezni.
A rendszer alapértelmezés szerint beállítja ezeket az engedélyeket? | Nem. A meglévő alapértelmezett engedélyek változatlanok maradnak. Igény szerint beállíthatja, hogy az engedélyek szigorúbbak legyenek.
Vannak a szolgáltatásra vonatkozó licencfeltételek? | Nem, a szolgáltatáshoz nem tartozik új licencelési követelmény.

## <a name="next-steps"></a>Következő lépések

- Ha többet szeretne megtudni az Azure AD-ben meglévő vendég engedélyekről, tekintse meg [a mi az alapértelmezett felhasználói engedélyek a Azure Active Directory?](../fundamentals/users-default-permissions.md)
- A vendég hozzáférésének korlátozására szolgáló Microsoft Graph API-metódusok megtekintéséhez lásd: [authorizationPolicy-erőforrás típusa](/graph/api/resources/authorizationpolicy)
- A felhasználók összes hozzáférésének visszavonásáról lásd: [felhasználói hozzáférés visszavonása az Azure ad-ben](users-revoke-access.md)