---
title: Csoportok hozzáadása, eltávolítása és listázása egy felügyeleti egységben – Azure Active Directory | Microsoft Docs
description: A csoportok és a szerepkörük engedélyei a Azure Active Directory felügyeleti egységében kezelhetők.
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
ms.openlocfilehash: f88a0818d93c33b6265cc8c695479d2a42678ada
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103011034"
---
# <a name="add-and-manage-groups-in-an-administrative-unit-in-azure-active-directory"></a>Csoportok hozzáadása és kezelése egy felügyeleti egységben Azure Active Directory

A Azure Active Directory (Azure AD) szolgáltatásban hozzáadhat csoportokat egy felügyeleti egységhez a vezérlés részletesebb felügyeleti hatóköre érdekében.

A PowerShell és a Microsoft Graph használatának előkészítése a felügyeleti egység felügyeletéhez: első [lépések](admin-units-manage.md#get-started).

## <a name="add-groups-to-an-administrative-unit"></a>Csoportok hozzáadása egy felügyeleti egységhez

A felügyeleti egységekhez a Azure Portal, a PowerShell vagy a Microsoft Graph használatával adhat hozzá csoportokat.

### <a name="use-the-azure-portal"></a>Az Azure Portal használata

Csak az egyes csoportokat lehet hozzárendelni egy felügyeleti egységhez. Nincs lehetőség a csoportok tömeges műveletként való hozzárendelésére. A Azure Portal két módon rendelhet hozzá egy csoportot egy felügyeleti egységhez:

* A **csoportok** ablaktáblán:

  1. A Azure Portal nyissa meg az **Azure ad**-t.
  1. Válassza a **csoportok** lehetőséget, majd válassza ki azt a csoportot, amelyet hozzá szeretne rendelni a felügyeleti egységhez. 
  1. A bal oldali ablaktáblán válassza a **felügyeleti egységek** lehetőséget azon felügyeleti egységek listájának megjelenítéséhez, amelyekhez a csoport hozzá van rendelve. 

     ![Képernyőkép a "hozzárendelés felügyeleti egységhez" hivatkozásra a "felügyeleti egység" panelen.](./media/admin-units-add-manage-groups/assign-to-group-1.png)

  1. Válassza **a hozzárendelés felügyeleti egységhez** lehetőséget.
  1. A jobb oldali ablaktáblán válassza ki a felügyeleti egységet.

* A **felügyeleti egységek**  >  **minden csoport** paneljén:

  1. A Azure Portal nyissa meg az **Azure ad**-t.
  
  1. A bal oldali ablaktáblán válassza a **felügyeleti egységek**, majd az **összes csoport** lehetőséget. 
     A felügyeleti egységhez már hozzárendelt csoportok a jobb oldali ablaktáblán jelennek meg. 

  1. A **csoportok** ablaktáblán válassza a **Hozzáadás** lehetőséget.
    A jobb oldali ablaktábla az Azure AD-szervezet összes elérhető csoportját listázza. 

     ![Képernyőkép – a Hozzáadás gombra kattintva hozzáadhat egy csoportot egy felügyeleti egységhez.](./media/admin-units-add-manage-groups/assign-to-admin-unit.png)

  1. Válasszon ki egy vagy több olyan csoportot, amelyet hozzá szeretne rendelni a felügyeleti egységhez, majd kattintson a **kiválasztás** gombra.

### <a name="use-powershell"></a>A PowerShell használata

A következő példában a `Add-AzureADMSAdministrativeUnitMember` parancsmagot használva vegye fel a csoportot a felügyeleti egységbe. A felügyeleti egység és a hozzáadni kívánt csoport objektumazonosító-azonosítója argumentumként történik. Módosítsa a Kiemelt szakaszt az adott környezethez szükséges módon.


```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$GroupObj = Get-AzureADGroup -Filter "displayname eq 'TestGroup'"
Add-AzureADMSAdministrativeUnitMember -ObjectId $adminUnitObj.ObjectId -RefObjectId $GroupObj.ObjectId
```

### <a name="use-microsoft-graph"></a>Microsoft Graph használata

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

## <a name="view-a-list-of-groups-in-an-administrative-unit"></a>A csoportok listájának megtekintése egy felügyeleti egységben

### <a name="use-the-azure-portal"></a>Az Azure Portal használata

1. A Azure Portal nyissa meg az **Azure ad**-t.

1. A bal oldali ablaktáblán válassza a **felügyeleti egységek** lehetőséget, majd válassza ki azt a felügyeleti egységet, amelynek a csoportjait meg szeretné tekinteni. Alapértelmezés szerint az **összes felhasználó** ki van választva a bal oldali ablaktáblán. 

1. A bal oldali ablaktáblán válassza a **csoportok** lehetőséget. A jobb oldali ablaktábla megjeleníti azoknak a csoportoknak a listáját, amelyek a kiválasztott felügyeleti egység tagjai.

   ![Képernyőkép a "csoportok" panelről, amely egy felügyeleti egységben lévő csoportok listáját jeleníti meg.](./media/admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="use-powershell"></a>A PowerShell használata

A felügyeleti egység összes tagjának listájának megjelenítéséhez futtassa a következő parancsot: 

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
Get-AzureADMSAdministrativeUnitMember -ObjectId $adminUnitObj.ObjectId
```

Az összes olyan csoport megjelenítéséhez, amely tagja a felügyeleti egységnek, használja a következő kódrészletet:

```powershell
foreach ($member in (Get-AzureADMSAdministrativeUnitMember -ObjectId $adminUnitObj.ObjectId)) 
{
if($member.ObjectType -eq "Group")
{
Get-AzureADGroup -ObjectId $member.ObjectId
}
}
```

### <a name="use-microsoft-graph"></a>Microsoft Graph használata

Futtassa az alábbi parancsot:

Kérés

```http
GET /directory/administrativeUnits/{admin-unit-id}/members/$/microsoft.graph.group
```

Törzs

```http
{}
```

## <a name="view-a-list-of-administrative-units-for-a-group"></a>Egy csoport felügyeleti egységeinek listájának megtekintése

### <a name="use-the-azure-portal"></a>Az Azure Portal használata

1. A Azure Portal nyissa meg az **Azure ad**-t.

1. A bal oldali ablaktáblán válassza a **csoportok** lehetőséget a csoportok listájának megjelenítéséhez.

1. Válasszon ki egy csoportot a csoport profiljának megnyitásához. 

1. A bal oldali ablaktáblán válassza a **felügyeleti egységek** lehetőséget az összes olyan felügyeleti egység listázásához, ahol a csoport tagja.

   ![Képernyőkép a "felügyeleti egységek" panelről, amely megjeleníti a csoporthoz rendelt felügyeleti egységek listáját.](./media/admin-units-add-manage-groups/list-group-au.png)

### <a name="use-powershell"></a>A PowerShell használata

Futtassa az alábbi parancsot:

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $groupObjId} }
```

### <a name="use-microsoft-graph"></a>Microsoft Graph használata

Futtassa az alábbi parancsot:

```http
https://graph.microsoft.com/v1.0/groups/{group-id}/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-group-from-an-administrative-unit"></a>Csoport eltávolítása felügyeleti egységből

### <a name="use-the-azure-portal"></a>Az Azure Portal használata

A Azure Portal kétféleképpen távolíthatja el egy felügyeleti egységből:

- Eltávolítás egy csoportból – Áttekintés:

  1. A Azure Portal nyissa meg az **Azure ad**-t.
  1. A bal oldali ablaktáblán válassza a **csoportok** lehetőséget, majd nyissa meg annak a csoportnak a profilját, amelyet el szeretne távolítani egy felügyeleti egységből.
  1. A bal oldali ablaktáblán válassza a **felügyeleti egységek** lehetőséget az összes olyan felügyeleti egység listázásához, amelyhez a csoport hozzá van rendelve. 
  1. Válassza ki azt a felügyeleti egységet, amelyből el szeretné távolítani a csoportot, majd válassza az **Eltávolítás a felügyeleti egységből** lehetőséget.

     ![Képernyőkép a "felügyeleti egységek" panelről, amely a kiválasztott felügyeleti egységhez rendelt csoportok listáját jeleníti meg.](./media/admin-units-add-manage-groups/group-au-remove.png)

- Távolítsa el a felügyeleti egységből:

  1. A Azure Portal nyissa meg az **Azure ad**-t.
  1. A bal oldali ablaktáblán válassza a **felügyeleti egységek** lehetőséget, majd válassza ki azt a felügyeleti egységet, amelyhez a csoport hozzá van rendelve.
  1. A bal oldali ablaktáblán válassza a **csoportok** lehetőséget a felügyeleti egységhez rendelt összes csoport listázásához.
  1. Válassza ki az eltávolítani kívánt csoportot, majd válassza a **csoportok eltávolítása** lehetőséget.

    ![Képernyőfelvétel a "csoportok" panelről, amely egy felügyeleti egységben található csoportok listáját jeleníti meg.](./media/admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="use-powershell"></a>A PowerShell használata

Futtassa az alábbi parancsot:

```powershell
Remove-AzureADMSAdministrativeUnitMember -ObjectId $adminUnitId -MemberId $memberGroupObjId
```

### <a name="use-microsoft-graph"></a>Microsoft Graph használata

Futtassa az alábbi parancsot:

```http
https://graph.microsoft.com/v1.0/directory/administrativeUnits/{admin-unit-id}/members/{group-id}/$ref
```

## <a name="next-steps"></a>Következő lépések

- [Szerepkör társítása egy felügyeleti egységhez](admin-units-assign-roles.md)
- [Felhasználók kezelése egy felügyeleti egységben](admin-units-add-manage-users.md)
