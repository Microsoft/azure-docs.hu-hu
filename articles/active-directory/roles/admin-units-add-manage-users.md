---
title: Felhasználók hozzáadása, eltávolítása és listába való felvétele egy felügyeleti egységben – Azure Active Directory | Microsoft Docs
description: Felhasználók és szerepkör-engedélyek kezelése egy felügyeleti egységben a Azure Active Directory
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 101f1452f547f195288e2d22bc516880100c7735
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107496826"
---
# <a name="add-and-manage-users-in-an-administrative-unit-in-azure-active-directory"></a>Felhasználók hozzáadása és kezelése egy felügyeleti egységben a Azure Active Directory

A Azure Active Directory (Azure AD) használatával felhasználókat adhat hozzá egy felügyeleti egységhez a részletesebb felügyeleti felügyeleti hatókör érdekében.

A PowerShell és a felügyeleti Microsoft Graph használatának előkészítéséhez lásd: [Első lépések.](admin-units-manage.md#get-started)

## <a name="add-users-to-an-administrative-unit"></a>Felhasználók hozzáadása felügyeleti egységhez

### <a name="use-the-azure-portal"></a>Az Azure Portal használata

A felhasználókat egyenként vagy tömeges műveletként rendelheti hozzá a felügyeleti egységekhez.

- Egyéni felhasználók hozzárendelése felhasználói profilból:

   1. Jelentkezzen be az [Azure AD](https://portal.azure.com) felügyeleti központba kiemelt szerepkör-rendszergazdai engedélyekkel.

   1. Válassza **a Felhasználók** lehetőséget, majd a felhasználó profiljának megnyitásához válassza ki a felügyeleti egységhez hozzárendelni kívánt felhasználót.
   
   1. Válassza **a Felügyeleti egységek lehetőséget.** 
   
   1. Ha a felhasználót egy vagy több  felügyeleti egységhez szeretné hozzárendelni, válassza a Hozzárendelés felügyeleti egységhez lehetőséget, majd a jobb oldali panelen válassza ki azokat a felügyeleti egységeket, amelyekhez hozzá szeretné rendelni a felhasználót.

       ![Képernyőkép a "Felügyeleti egységek" panelről, amely a felhasználók felügyeleti egységhez való hozzárendelését mutatja be.](./media/admin-units-add-manage-users/assign-users-individually.png)

- Egyéni felhasználók hozzárendelése felügyeleti egységből:

   1. Jelentkezzen be az [Azure AD](https://portal.azure.com) felügyeleti központba kiemelt szerepkör-rendszergazdai engedélyekkel.
   1. Válassza **a Felügyeleti egységek** lehetőséget, majd válassza ki azt a felügyeleti egységet, amelyben a felhasználó hozzá lesz rendelve.
   1. Válassza **a Minden felhasználó** lehetőséget, válassza  a **Tag** hozzáadása lehetőséget, majd a Tag hozzáadása panelen válasszon ki egy vagy több felhasználót, akikhez hozzá szeretné rendelni a felügyeleti egységet.

        ![Képernyőkép a "Felhasználók" felügyeleti egységről a felhasználók felügyeleti egységhez való hozzárendelésére.](./media/admin-units-add-manage-users/assign-to-admin-unit.png)

- Felhasználók hozzárendelése tömeges műveletként:

   1. Jelentkezzen be az [Azure AD](https://portal.azure.com) felügyeleti központba kiemelt szerepkör-rendszergazdai engedélyekkel.

   1. Válassza **a Felügyeleti egységek lehetőséget.**

   1. Válassza ki azt a felügyeleti egységet, amelyhez felhasználókat szeretne hozzáadni.

   1. Válassza **a Felhasználók tömeges**  >  **tevékenységei** Tagok tömeges hozzáadása  >  **lehetőséget.** Ezután letöltheti a vesszővel elválasztott értékeket (CSV) sablont, és szerkesztheti a fájlt. A formátum egyszerű, és minden sorban egyetlen egyszerű felhasználónevet kell hozzáadni. Miután a fájl elkészült, mentse egy megfelelő helyre, majd töltse fel a lépés részeként.

      ![Képernyőkép a "Felhasználók" panelről, amely lehetővé teszi a felhasználók tömeges műveletként való hozzárendelését egy felügyeleti egységhez.](./media/admin-units-add-manage-users/bulk-assign-to-admin-unit.png)

### <a name="use-powershell"></a>A PowerShell használata

A PowerShellben a következő példában található parancsmag használatával adja hozzá a felhasználót `Add-AzureADAdministrativeUnitMember` a felügyeleti egységhez. Annak a felügyeleti egységnek az objektumazonosítója, amelyhez a felhasználót és a hozzáadni kívánt felhasználó objektumazonosítóját hozzá szeretné adni, argumentumként lesz felveve. Módosítsa a kiemelt szakaszt, ha szükséges az adott környezethez.

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$userObj = Get-AzureADUser -Filter "UserPrincipalName eq 'bill@example.onmicrosoft.com'"
Add-AzureADMSAdministrativeUnitMember -Id $adminUnitObj.Id -RefObjectId $userObj.ObjectId
```


### <a name="use-microsoft-graph"></a>A Microsoft Graph

Cserélje le a helyőrzőt a tesztinformációkra, és futtassa a következő parancsot:

Kérés

```http
POST /administrativeUnits/{admin-unit-id}/members/$ref
```

Törzs

```http
{
  "@odata.id":"https://graph.microsoft.com/v1.0/users/{user-id}"
}
```

Példa

```http
{
  "@odata.id":"https://graph.microsoft.com/v1.0/users/john@example.com"
}
```

## <a name="view-a-list-of-administrative-units-for-a-user"></a>Felhasználó felügyeleti egységeinek listájának megtekintése

### <a name="use-the-azure-portal"></a>Az Azure Portal használata

A Azure Portal a következővel nyithatja meg a felhasználói profilt:

1. Válassza az **Azure AD lehetőséget,** majd válassza a **Felhasználók lehetőséget.**

1. Válassza ki azt a felhasználót, akinek a profilját meg szeretné tekinteni.

1. Válassza **a Felügyeleti egységek** lehetőséget azon felügyeleti egységek listájának megjelenítéséhez, amelyekhez a felhasználó hozzá lett rendelve.

   ![Képernyőkép a felügyeleti egységekről, amelyekhez a felhasználó hozzá lett rendelve.](./media/admin-units-add-manage-users/list-user-admin-units.png)

### <a name="use-powershell"></a>A PowerShell használata

Futtassa az alábbi parancsot:

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -Id $_.ObjectId | where {$_.RefObjectId -eq $userObjId} }
```

> [!NOTE]
> Alapértelmezés szerint a `Get-AzureADAdministrativeUnitMember` egy felügyeleti egységnek csak 100 tagját adja vissza. További tagok lekéréshez hozzáadhatja a `"-All $true"` tagot.

### <a name="use-microsoft-graph"></a>A Microsoft Graph

Cserélje le a helyőrzőt a tesztinformációkra, és futtassa a következő parancsot:

```http
https://graph.microsoft.com/v1.0/users/{user-id}/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-single-user-from-an-administrative-unit"></a>Egyetlen felhasználó eltávolítása egy felügyeleti egységből

### <a name="use-the-azure-portal"></a>Az Azure Portal használata

Egy felhasználót kétféleképpen távolíthat el egy felügyeleti egységből: 

* A Azure Portal válassza az **Azure AD** lehetőséget, majd válassza a **Felhasználók lehetőséget.** 
  1. Válassza ki a felhasználót a felhasználói profil megnyitásához. 
  1. Válassza ki azt a felügyeleti egységet, amelyből el szeretné távolítani a felhasználót, majd válassza az **Eltávolítás a felügyeleti egységből lehetőséget.**

     ![Képernyőkép a felhasználó felügyeleti egységből való eltávolításáról a felhasználói profil panelről.](./media/admin-units-add-manage-users/user-remove-admin-units.png)

* A Azure Portal válassza az **Azure AD** lehetőséget, majd válassza a **Felügyeleti egységek lehetőséget.**
  1. Válassza ki azt a felügyeleti egységet, amelyből el szeretné távolítani a felhasználót. 
  1. Jelölje ki a felhasználót, majd válassza a **Tag eltávolítása lehetőséget.**
  
     ![Képernyőkép a felhasználó felügyeleti egység szintjén való eltávolításáról.](./media/admin-units-add-manage-users/admin-units-remove-user.png)

### <a name="use-powershell"></a>A PowerShell használata

Futtassa az alábbi parancsot:

```powershell
Remove-AzureADMSAdministrativeUnitMember -Id $adminUnitId -MemberId $memberUserObjId
```

### <a name="use-microsoft-graph"></a>A Microsoft Graph

Cserélje le a helyőrzőket a tesztinformációkra, és futtassa a következő parancsot:

```http
https://graph.microsoft.com/v1.0/directory/administrativeUnits/{admin-unit-id}/members/{user-id}/$ref
```

## <a name="remove-multiple-users-as-a-bulk-operation"></a>Több felhasználó eltávolítása tömeges műveletként

Ha több felhasználót szeretne eltávolítani egy felügyeleti egységből, tegye a következőket:

1. A Azure Portal az **Azure AD-be.**

1. Válassza **a Felügyeleti egységek** lehetőséget, majd válassza ki azt a felügyeleti egységet, amelyből felhasználókat szeretne eltávolítani. 

1. Válassza **a Tagok tömeges eltávolítása** lehetőséget, majd töltse le az eltávolítani kívánt felhasználók listához használni kívánt CSV-sablont.

   ![Képernyőkép a "Tagok tömeges eltávolítása" hivatkozásról a "Felhasználók" panelen.](./media/admin-units-add-manage-users/bulk-user-remove.png)

1. Szerkessze a letöltött CSV-sablont a megfelelő felhasználói bejegyzésekkel. Ne távolítsa el a sablon első két sorát. Minden sorban adjon hozzá egy egyszerű felhasználónevet (UPN).

   ![Képernyőkép egy szerkesztett CSV-fájlról, amely tömegesen távolít el felhasználókat egy felügyeleti egységből.](./media/admin-units-add-manage-users/bulk-user-entries.png)

1. Mentse a módosításokat, töltse fel a fájlt, majd válassza a **Küldés lehetőséget.**

## <a name="next-steps"></a>Következő lépések

- [Szerepkör hozzárendelése felügyeleti egységhez](admin-units-assign-roles.md)
- [Csoportok hozzáadása felügyeleti egységhez](admin-units-add-manage-groups.md)
