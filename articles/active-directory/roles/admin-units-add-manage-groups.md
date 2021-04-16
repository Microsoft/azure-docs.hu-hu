---
title: Csoportokat adhat hozzá, távolíthat el és listából egy felügyeleti egységben – Azure Active Directory | Microsoft Docs
description: Csoportokat és azok szerepkör-engedélyeinek kezelését egy felügyeleti egységben a Azure Active Directory.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 03/10/2021
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ad8cce8375ecd670a481541a091e36aacb41240
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505292"
---
# <a name="add-and-manage-groups-in-an-administrative-unit-in-azure-active-directory"></a>Csoportok hozzáadása és kezelése felügyeleti egységben a Azure Active Directory

A Azure Active Directory (Azure AD) használatával csoportokat adhat hozzá egy felügyeleti egységhez a részletesebb felügyeleti felügyeleti hatókör érdekében.

A PowerShell és a felügyeleti Microsoft Graph előkészítéséhez lásd: [Első lépések.](admin-units-manage.md#get-started)

## <a name="add-groups-to-an-administrative-unit"></a>Csoportok hozzáadása felügyeleti egységhez

A felügyeleti egységekhez a következő parancsokkal adhat hozzá csoportokat: Azure Portal, PowerShell vagy Microsoft Graph.

### <a name="use-the-azure-portal"></a>Az Azure Portal használata

Csak egyes csoportokat rendelhet hozzá egy felügyeleti egységhez. Csoportok tömeges műveletként való hozzárendelése nem lehetséges. A Azure Portal kétféleképpen rendelhet hozzá egy csoportot egy felügyeleti egységhez:

* A Csoportok **panelen:**

  1. A Azure Portal az **Azure AD-hez.**
  1. Válassza **a Csoportok** lehetőséget, majd válassza ki a felügyeleti egységhez hozzárendelni kívánt csoportot. 
  1. A bal oldali panelen válassza a Felügyeleti **egységek** lehetőséget azon felügyeleti egységek listájának megjelenítéséhez, amelyekhez a csoport hozzá van rendelve. 

     ![A "Felügyeleti egységek" panel "Hozzárendelés felügyeleti egységhez" hivatkozásának képernyőképe.](./media/admin-units-add-manage-groups/assign-to-group-1.png)

  1. Válassza **a Hozzárendelés felügyeleti egységhez lehetőséget.**
  1. A jobb oldali panelen válassza ki a felügyeleti egységet.

* A Felügyeleti **egységek Minden**  >  **csoport panelen:**

  1. A Azure Portal az **Azure AD-hez.**
  
  1. A bal oldali panelen válassza a Felügyeleti **egységek,** majd a Minden **csoport lehetőséget.** 
     A felügyeleti egységhez már hozzárendelt csoportok a jobb oldali panelen jelennek meg. 

  1. A Csoportok **panelen** válassza a Hozzáadás **lehetőséget.**
    A jobb oldali panel az Azure AD-szervezetben elérhető összes csoportot felsorolja. 

     ![Képernyőkép a "Hozzáadás" gombról, amely egy csoportot ad hozzá egy felügyeleti egységhez.](./media/admin-units-add-manage-groups/assign-to-admin-unit.png)

  1. Válasszon ki egy vagy több, a felügyeleti egységhez hozzárendelni kívánt csoportot, majd kattintson a **Kijelölés gombra.**

### <a name="use-powershell"></a>A PowerShell használata

A következő példában a parancsmag használatával adja hozzá `Add-AzureADMSAdministrativeUnitMember` a csoportot a felügyeleti egységhez. A felügyeleti egység objektumazonosítója és a hozzáadni szükséges csoport objektumazonosítója argumentumként lesz felveve. Módosítsa a kiemelt szakaszt, ha szükséges az adott környezethez.


```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$GroupObj = Get-AzureADGroup -Filter "displayname eq 'TestGroup'"
Add-AzureADMSAdministrativeUnitMember -Id $adminUnitObj.Id -RefObjectId $GroupObj.ObjectId
```

### <a name="use-microsoft-graph"></a>A Microsoft Graph

Futtassa az alábbi parancsot:

Kérés

```http
POST /administrativeUnits/{admin-unit-id}/members/$ref
```

Törzs

```http
{
"@odata.id":"https://graph.microsoft.com/v1.0/groups/{group-id}"
}
```

Példa

```http
{
"@odata.id":"https://graph.microsoft.com/v1.0/groups/871d21ab-6b4e-4d56-b257-ba27827628f3"
}
```

## <a name="view-a-list-of-groups-in-an-administrative-unit"></a>Csoportlista megtekintése egy felügyeleti egységben

### <a name="use-the-azure-portal"></a>Az Azure Portal használata

1. A Azure Portal az **Azure AD-be.**

1. A bal oldali panelen válassza **a** Felügyeleti egységek lehetőséget, majd válassza ki azt a felügyeleti egységet, amelynek a csoportjait meg szeretné tekinteni. Alapértelmezés szerint a **Minden felhasználó beállítás** van kiválasztva a bal oldali panelen. 

1. A bal oldali panelen válassza a **Csoportok lehetőséget.** A jobb oldali panelen megjelenik azoknak a csoportoknak a listája, amelyek a kiválasztott felügyeleti egység tagjai.

   ![Képernyőkép a "Csoportok" panelről, amely egy felügyeleti egység csoportlistát jelenít meg.](./media/admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="use-powershell"></a>A PowerShell használata

A felügyeleti egység tagjainak listájának megjelenítéséhez futtassa a következő parancsot: 

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
Get-AzureADMSAdministrativeUnitMember -Id $adminUnitObj.Id
```

Az összes olyan csoport megjelenítéséhez, amely tagja a felügyeleti egységnek, használja az alábbi kódrészletet:

```powershell
foreach ($member in (Get-AzureADMSAdministrativeUnitMember -Id $adminUnitObj.Id)) 
{
if($member.ObjectType -eq "Group")
{
Get-AzureADGroup -ObjectId $member.ObjectId
}
}
```

### <a name="use-microsoft-graph"></a>A Microsoft Graph

Futtassa az alábbi parancsot:

Kérés

```http
GET /directory/administrativeUnits/{admin-unit-id}/members/$/microsoft.graph.group
```

Törzs

```http
{}
```

## <a name="view-a-list-of-administrative-units-for-a-group"></a>Csoport felügyeleti egységeinek listájának megtekintése

### <a name="use-the-azure-portal"></a>Az Azure Portal használata

1. A Azure Portal az **Azure AD-hez.**

1. A bal oldali panelen válassza a **Csoportok lehetőséget** a csoportok listájának megjelenítéséhez.

1. Válasszon ki egy csoportot a csoport profiljának megnyitásához. 

1. A bal oldali panelen válassza **a** Felügyeleti egységek lehetőséget az összes olyan felügyeleti egység listához, amelyben a csoport tag.

   ![A "Felügyeleti egységek" panel képernyőképe, amely azon felügyeleti egységek listáját jeleníti meg, amelyekhez egy csoport hozzá van rendelve.](./media/admin-units-add-manage-groups/list-group-au.png)

### <a name="use-powershell"></a>A PowerShell használata

Futtassa az alábbi parancsot:

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $groupObjId} }
```

### <a name="use-microsoft-graph"></a>A Microsoft Graph

Futtassa az alábbi parancsot:

```http
https://graph.microsoft.com/v1.0/groups/{group-id}/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-group-from-an-administrative-unit"></a>Csoport eltávolítása felügyeleti egységből

### <a name="use-the-azure-portal"></a>Az Azure Portal használata

A felügyeleti egységből kétféleképpen távolíthat el csoportot Azure Portal felügyeleti egységből:

- Távolítsa el a csoport áttekintésből:

  1. A Azure Portal az **Azure AD-hez.**
  1. A bal oldali panelen válassza a **Csoportok** lehetőséget, majd nyissa meg a felügyeleti egységből eltávolítani kívánt csoport profilját.
  1. A bal oldali panelen válassza **a** Felügyeleti egységek lehetőséget azon felügyeleti egységek listához, amelyekhez a csoport hozzá van rendelve. 
  1. Válassza ki azt a felügyeleti egységet, amelyből el szeretné távolítani a csoportot, majd válassza az Eltávolítás **a felügyeleti egységből lehetőséget.**

     ![A "Felügyeleti egységek" panel képernyőképe, amely a kiválasztott felügyeleti egységhez rendelt csoportok listáját jeleníti meg.](./media/admin-units-add-manage-groups/group-au-remove.png)

- Távolítsa el egy felügyeleti egységből:

  1. A Azure Portal az **Azure AD-hez.**
  1. A bal oldali panelen válassza a Felügyeleti **egységek** lehetőséget, majd válassza ki azt a felügyeleti egységet, amelyhez a csoport hozzá van rendelve.
  1. A bal oldali panelen válassza a **Csoportok lehetőséget** a felügyeleti egységhez rendelt összes csoport listához.
  1. Jelölje ki az eltávolítani kívánt csoportot, majd válassza a **Csoportok eltávolítása lehetőséget.**

    ![A "Csoportok" panel képernyőképe, amely egy felügyeleti egység csoportjainak listáját jeleníti meg.](./media/admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="use-powershell"></a>A PowerShell használata

Futtassa az alábbi parancsot:

```powershell
Remove-AzureADMSAdministrativeUnitMember -ObjectId $adminUnitId -MemberId $memberGroupObjId
```

### <a name="use-microsoft-graph"></a>A Microsoft Graph

Futtassa az alábbi parancsot:

```http
https://graph.microsoft.com/v1.0/directory/administrativeUnits/{admin-unit-id}/members/{group-id}/$ref
```

## <a name="next-steps"></a>Következő lépések

- [Szerepkör hozzárendelése felügyeleti egységhez](admin-units-assign-roles.md)
- [Felhasználók kezelése felügyeleti egységben](admin-units-add-manage-users.md)
