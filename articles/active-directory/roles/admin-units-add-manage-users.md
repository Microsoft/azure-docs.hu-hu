---
title: Felhasználók hozzáadása, eltávolítása és listázása egy felügyeleti egységben – Azure Active Directory | Microsoft Docs
description: A felhasználók és a szerepkörük engedélyeinek kezelése egy felügyeleti egységben Azure Active Directory
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
ms.openlocfilehash: 7a9d80344a31023d174935e7f785e36102e99eba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103011546"
---
# <a name="add-and-manage-users-in-an-administrative-unit-in-azure-active-directory"></a>Felhasználók hozzáadása és kezelése egy felügyeleti egységben Azure Active Directory

A Azure Active Directory (Azure AD) szolgáltatásban hozzáadhat felhasználókat egy felügyeleti egységhez a vezérlés részletesebb felügyeleti hatóköre érdekében.

A PowerShell és a Microsoft Graph használatának előkészítése a felügyeleti egység felügyeletéhez: első [lépések](admin-units-manage.md#get-started).

## <a name="add-users-to-an-administrative-unit"></a>Felhasználók hozzáadása egy felügyeleti egységhez

### <a name="use-the-azure-portal"></a>Az Azure Portal használata

A felhasználókat a felügyeleti egységekhez egyenként vagy tömeges műveletként is hozzárendelheti.

- Egyéni felhasználók társítása felhasználói profilból:

   1. Jelentkezzen be az [Azure ad felügyeleti központba](https://portal.azure.com) a Kiemelt szerepkörű rendszergazdai engedélyekkel.

   1. Válassza a **felhasználók** lehetőséget, majd a felhasználó profiljának megnyitásához válassza ki a felügyeleti egységhez hozzárendelendő felhasználót.
   
   1. Válassza a **felügyeleti egységek** lehetőséget. 
   
   1. Ha a felhasználót egy vagy több felügyeleti egységhez szeretné rendelni, válassza a **hozzárendelés felügyeleti egységhez** lehetőséget, majd a jobb oldali ablaktáblán válassza ki azokat a felügyeleti egységeket, amelyekhez hozzá szeretné rendelni a felhasználót.

       ![Képernyőkép a "felügyeleti egységekről" panelről, amely a felhasználót egy felügyeleti egységhez rendeli hozzá.](./media/admin-units-add-manage-users/assign-users-individually.png)

- Egyéni felhasználók társítása egy felügyeleti egységből:

   1. Jelentkezzen be az [Azure ad felügyeleti központba](https://portal.azure.com) a Kiemelt szerepkörű rendszergazdai engedélyekkel.
   1. Válassza a **felügyeleti egységek** lehetőséget, majd válassza ki azt a felügyeleti egységet, amelyben a felhasználót hozzá szeretné rendelni.
   1. Válassza a **minden felhasználó** lehetőséget, válassza a **tag hozzáadása** lehetőséget, majd a **tag hozzáadása** panelen válasszon ki egy vagy több olyan felhasználót, amelyet hozzá szeretne rendelni a felügyeleti egységhez.

        ![Képernyőkép a "felhasználók" panelről, amely a felhasználó felügyeleti egységhez való hozzárendelésére használható.](./media/admin-units-add-manage-users/assign-to-admin-unit.png)

- Felhasználók kiosztása tömeges műveletként:

   1. Jelentkezzen be az [Azure ad felügyeleti központba](https://portal.azure.com) a Kiemelt szerepkörű rendszergazdai engedélyekkel.

   1. Válassza a **felügyeleti egységek** lehetőséget.

   1. Válassza ki azt a felügyeleti egységet, amelyhez felhasználókat szeretne felvenni.

   1. Válassza a **felhasználók**  >  **tömeges tevékenységek**  >  **tömeges Hozzáadás tagok** elemet. Ezután letöltheti a vesszővel tagolt értékeket tartalmazó (CSV) sablont, és szerkesztheti a fájlt. A formátum egyszerű, és az egyes sorokban hozzá kell adni egyetlen egyszerű felhasználónevet. Miután a fájl elkészült, mentse azt egy megfelelő helyre, majd töltse fel a lépés részeként.

      ![Képernyőfelvétel a "felhasználók" panelről, amely a felhasználókat egy felügyeleti egységhez tömeges műveletként rendeli hozzá.](./media/admin-units-add-manage-users/bulk-assign-to-admin-unit.png)

### <a name="use-powershell"></a>A PowerShell használata

A PowerShellben a `Add-AzureADAdministrativeUnitMember` következő példában szereplő parancsmag használatával adja hozzá a felhasználót a felügyeleti egységhez. Annak a felügyeleti egységnek az azonosítója, amelyhez hozzá szeretné adni a felhasználót és a hozzáadni kívánt felhasználó objektumazonosítóát argumentumként. Módosítsa a Kiemelt szakaszt az adott környezethez szükséges módon.

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$userObj = Get-AzureADUser -Filter "UserPrincipalName eq 'bill@example.onmicrosoft.com'"
Add-AzureADMSAdministrativeUnitMember -Id $adminUnitObj.ObjectId -RefObjectId $userObj.ObjectId
```


### <a name="use-microsoft-graph"></a>Microsoft Graph használata

Cserélje le a helyőrzőt a tesztelési adatokra, és futtassa a következő parancsot:

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

## <a name="view-a-list-of-administrative-units-for-a-user"></a>A felhasználó felügyeleti egységeinek listájának megtekintése

### <a name="use-the-azure-portal"></a>Az Azure Portal használata

A Azure Portal a következő módon nyithatja meg a felhasználó profilját:

1. Nyissa meg az **Azure ad**-t, majd válassza a **felhasználók** lehetőséget.

1. Válassza ki azt a felhasználót, akinek a profilját meg szeretné tekinteni.

1. Válassza ki a **felügyeleti egységeket** azon felügyeleti egységek listájának megjelenítéséhez, amelyekhez a felhasználó hozzá lett rendelve.

   ![Képernyőfelvétel azon felügyeleti egységekről, amelyekhez a felhasználó hozzá van rendelve.](./media/admin-units-add-manage-users/list-user-admin-units.png)

### <a name="use-powershell"></a>A PowerShell használata

Futtassa az alábbi parancsot:

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -Id $_.ObjectId | where {$_.RefObjectId -eq $userObjId} }
```

> [!NOTE]
> Alapértelmezés szerint a `Get-AzureADAdministrativeUnitMember` csak a felügyeleti egység 100 tagjait adja vissza. További tagok beolvasásához hozzáadhatja a következőt: `"-All $true"` .

### <a name="use-microsoft-graph"></a>Microsoft Graph használata

Cserélje le a helyőrzőt a tesztelési adatokra, és futtassa a következő parancsot:

```http
https://graph.microsoft.com/v1.0/users/{user-id}/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-single-user-from-an-administrative-unit"></a>Egyetlen felhasználó eltávolítása egy felügyeleti egységből

### <a name="use-the-azure-portal"></a>Az Azure Portal használata

A felhasználókat kétféleképpen távolíthatja el egy felügyeleti egységből: 

* A Azure Portal nyissa meg az **Azure ad**-t, majd válassza a **felhasználók** lehetőséget. 
  1. Válassza ki a felhasználót a felhasználó profiljának megnyitásához. 
  1. Válassza ki azt a felügyeleti egységet, amelyből el szeretné távolítani a felhasználót, majd válassza az **Eltávolítás a felügyeleti egységből** lehetőséget.

     ![Képernyőfelvétel: a felhasználó eltávolítása egy felügyeleti egységből a felhasználó profil paneljéről.](./media/admin-units-add-manage-users/user-remove-admin-units.png)

* A Azure Portal nyissa meg az **Azure ad**-t, majd válassza a **felügyeleti egységek** elemet.
  1. Válassza ki azt a felügyeleti egységet, amelyből el szeretné távolítani a felhasználót. 
  1. Válassza ki a felhasználót, majd válassza a **tag eltávolítása** lehetőséget.
  
     ![A felhasználó felügyeleti egység szintjén való eltávolítását bemutató képernyőkép.](./media/admin-units-add-manage-users/admin-units-remove-user.png)

### <a name="use-powershell"></a>A PowerShell használata

Futtassa az alábbi parancsot:

```powershell
Remove-AzureADMSAdministrativeUnitMember -Id $adminUnitId -MemberId $memberUserObjId
```

### <a name="use-microsoft-graph"></a>Microsoft Graph használata

Cserélje le a helyőrzőket a tesztelési adatokkal, és futtassa a következő parancsot:

```http
https://graph.microsoft.com/v1.0/directory/administrativeUnits/{admin-unit-id}/members/{user-id}/$ref
```

## <a name="remove-multiple-users-as-a-bulk-operation"></a>Több felhasználó eltávolítása tömeges műveletként

Ha több felhasználót szeretne eltávolítani egy felügyeleti egységből, tegye a következőket:

1. A Azure Portal nyissa meg az **Azure ad**-t.

1. Válassza a **felügyeleti egységek** lehetőséget, majd válassza ki azt a felügyeleti egységet, amelyből el szeretné távolítani a felhasználókat. 

1. Válassza a **tömeges eltávolítás tagok** lehetőséget, majd töltse le a használni kívánt CSV-sablont az eltávolítandó felhasználók listázásához.

   ![A "felhasználók" panel "tagok eltávolítása" hivatkozását bemutató képernyőkép.](./media/admin-units-add-manage-users/bulk-user-remove.png)

1. Szerkessze a letöltött CSV-sablont a megfelelő felhasználói bejegyzésekkel. Ne távolítsa el a sablon első két sorát. Adjon hozzá egy egyszerű felhasználónevet (UPN) az egyes sorokban.

   ![Képernyőkép egy szerkesztett CSV-fájlról, amely tömegesen távolítja el a felhasználókat egy felügyeleti egységből.](./media/admin-units-add-manage-users/bulk-user-entries.png)

1. Mentse a módosításokat, töltse fel a fájlt, majd válassza a **Submit (Küldés**) lehetőséget.

## <a name="next-steps"></a>Következő lépések

- [Szerepkör társítása egy felügyeleti egységhez](admin-units-assign-roles.md)
- [Csoportok hozzáadása egy felügyeleti egységhez](admin-units-add-manage-groups.md)
