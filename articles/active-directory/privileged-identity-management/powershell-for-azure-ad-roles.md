---
title: PowerShell Azure AD-szerepkörökhöz a PIM-ben – Azure AD | Microsoft Docs
description: Azure AD-szerepkörök kezelése a Azure AD Privileged Identity Management (PIM) PowerShell-parancsmagjai használatával.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2774c63d34de3de951aa8076b56d203b976158ec
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102548750"
---
# <a name="powershell-for-azure-ad-roles-in-privileged-identity-management"></a>PowerShell Azure AD-szerepkörökhöz Privileged Identity Management

Ez a cikk a Azure Active Directory (Azure AD) PowerShell-parancsmagok használatát ismerteti az Azure AD-szerepkörök Privileged Identity Management (PIM) szolgáltatásban való kezeléséhez. Emellett azt is bemutatja, hogyan lehet beállítani az Azure AD PowerShell-modult.

> [!Note]
> A hivatalos PowerShell csak akkor támogatott, ha a Azure AD Privileged Identity Management új verzióját támogatja. Lépjen a Privileged Identity Managementra, és győződjön meg róla, hogy a következő szalagcím szerepel a gyors üzembe helyezés panelen.
> [![tekintse át a Privileged Identity Management telepített verzióját](media/pim-how-to-add-role-to-user/pim-new-version.png "Válassza az Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox) Ha nem rendelkezik ezzel a bannerrel, várjon, amíg jelenleg a következő heteken belül bevezetjük a frissített élményt.
> Az Privileged Identity Management PowerShell-parancsmagok az Azure AD előzetes verziójának moduljában támogatottak. Ha egy másik modult használ, és a modul mostantól hibaüzenetet ad vissza, kezdje el használni ezt az új modult. Ha olyan éles rendszerek vannak, amelyek egy másik modulra épülnek, lépjen kapcsolatba a következővel: [pim_preview@microsoft.com](mailto:pim_preview@microsoft.com) .

## <a name="installation-and-setup"></a>Telepítés és beállítás

1. Az Azure AD Preview-modul telepítése

    ```powershell
    Install-module AzureADPreview
    ```

1. A folytatás előtt győződjön meg arról, hogy rendelkezik a szükséges szerepkör-engedélyekkel. Ha olyan felügyeleti feladatokat próbál végrehajtani, mint a szerepkör-hozzárendelés vagy a szerepkör-beállítás frissítése, győződjön meg arról, hogy rendelkezik a globális rendszergazda vagy a Kiemelt szerepkörű rendszergazda szerepkörrel. Ha csak a saját hozzárendelését próbálja aktiválni, az alapértelmezett felhasználói engedélyeken túli engedélyek megadása nem kötelező.

1. Kapcsolódjon az Azure AD-hez.

    ```powershell
    $AzureAdCred = Get-Credential  
    Connect-AzureAD -Credential $AzureAdCred
    ```

1. Keresse meg az Azure ad-szervezet bérlői azonosítóját **Azure Active Directory**  >  **Tulajdonságok**  >  **könyvtár-azonosítójának** megkeresésével. A parancsmagok szakaszban ezt az azonosítót kell használnia, amikor meg kell adnia a resourceId.

    ![A szervezet AZONOSÍTÓjának megkeresése az Azure AD-szervezet tulajdonságainál](./media/powershell-for-azure-ad-roles/tenant-id-for-Azure-ad-org.png)

> [!Note]
> A következő fejezetekben egyszerű példákat talál, amelyek segítségével megkezdheti a működést. A következő parancsmagokkal kapcsolatos részletesebb dokumentációt itt talál: [https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#privileged_role_management](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#privileged_role_management) . A providerID paraméterben azonban a "aadRoles" értékkel kell helyettesíteni a "azureResources" kifejezést. Arra is emlékeznie kell, hogy az Azure AD-szervezet bérlői AZONOSÍTÓját resourceId paraméterként használja.

## <a name="retrieving-role-definitions"></a>Szerepkör-definíciók beolvasása

Az alábbi parancsmaggal kérheti le az Azure AD-szervezet összes beépített és egyéni Azure AD-szerepkörét. Ez a fontos lépés a szerepkör neve és a roleDefinitionId közötti leképezést biztosítja. A roleDefinitionId a parancsmagok egészében használják egy adott szerepkörre való hivatkozáshoz.

A roleDefinitionId az Azure AD-szervezetre jellemző, és eltér a szerepkör-felügyeleti API által visszaadott roleDefinitionId.

```powershell
Get-AzureADMSPrivilegedRoleDefinition -ProviderId aadRoles -ResourceId 926d99e7-117c-4a6a-8031-0cc481e9da26
```

Eredmény:

![Az Azure AD-szervezet összes szerepkörének beolvasása](./media/powershell-for-azure-ad-roles/get-all-roles-result.png)

## <a name="retrieving-role-assignments"></a>Szerepkör-hozzárendelések beolvasása

Az alábbi parancsmaggal kérheti le az összes szerepkör-hozzárendelést az Azure AD-szervezetben.

```powershell
Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26"
```

A következő parancsmaggal kérhet le egy adott felhasználó összes szerepkör-hozzárendelését. Ezt a listát az Azure AD portál "saját szerepkörök" néven is ismert. Az egyetlen különbség az, hogy hozzáadott egy szűrőt a tulajdonos AZONOSÍTÓhoz. Ebben a kontextusban a tulajdonos azonosítója a felhasználóazonosító vagy a csoport azonosítója.

```powershell
Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "subjectId eq 'f7d1887c-7777-4ba3-ba3d-974488524a9d'" 
```

A következő parancsmaggal kérhet le egy adott szerepkör összes szerepkör-hozzárendelését. Az roleDefinitionId itt látható az előző parancsmag által visszaadott azonosító.

```powershell
Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "roleDefinitionId eq '0bb54a22-a3df-4592-9dc7-9e1418f0f61c'"
```

A parancsmagok az alább látható szerepkör-hozzárendelési objektumok listáját eredményezik. A tulajdonos azonosítója annak a felhasználónak a felhasználói azonosítója, akihez a szerepkör hozzá van rendelve. A hozzárendelési állapot lehet aktív vagy jogosult. Ha a felhasználó aktív, és a LinkedEligibleRoleAssignmentId mezőben van egy azonosító, az azt jelenti, hogy a szerepkör jelenleg aktiválva van.

Eredmény:

![Az Azure AD-szervezet összes szerepkör-hozzárendelésének beolvasása](./media/powershell-for-azure-ad-roles/get-all-role-assignments-result.png)

## <a name="assign-a-role"></a>Szerepkör kiosztása

Egy jogosult hozzárendelés létrehozásához használja a következő parancsmagot.

```powershell
Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'ff690580-d1c6-42b1-8272-c029ded94dec' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'adminAdd' -AssignmentState 'Eligible' -schedule $schedule -reason "dsasdsas" 
```

A hozzárendelés kezdési és befejezési időpontját meghatározó ütemterv egy olyan objektum, amely az alábbi példához hasonló módon hozható létre:

```powershell
$schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
$schedule.Type = "Once"
$schedule.StartDateTime = (Get-Date).ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ss.fffZ")
$schedule.endDateTime = "2020-07-25T20:49:11.770Z"
```
> [!Note]
> Ha a Befejeződátumidő értéke NULL értékre van állítva, állandó hozzárendelést jelez.

## <a name="activate-a-role-assignment"></a>Szerepkör-hozzárendelés aktiválása

A következő parancsmag használatával aktiválhat egy jogosult hozzárendelést.

```powershell
Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'f55a9a68-f424-41b7-8bee-cee6a442d418' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'UserAdd' -AssignmentState 'Active' -schedule $schedule -reason "dsasdsas"
``` 

Ez a parancsmag majdnem azonos a szerepkör-hozzárendelés létrehozásához használt parancsmaggal. A parancsmagok közötti fő különbség az, hogy a – Type paraméter esetében az aktiválás "hozzáadási" helyett "adminAdd". A másik különbség az, hogy a – AssignmentState paraméter "aktív" a "jogosult" helyett.

> [!Note]
> Két korlátozási forgatókönyv van a szerepkör-aktiváláshoz a PowerShell használatával.
> 1. Ha a szerepkör-beállításban jegyrendszer/jegy száma szükséges, akkor nem lehet paraméterként megadni ezeket. Így nem lehet aktiválni a szerepkört a Azure Portalon túl. A szolgáltatás a következő néhány hónap során a PowerShellbe van bevezetve.
> 1. Ha a szerepkör-aktiváláshoz többtényezős hitelesítésre van szükség, a PowerShell számára jelenleg nincs lehetőség arra, hogy megtámadja a felhasználót, amikor aktiválja a szerepkört. Ehelyett a felhasználóknak aktiválnia kell az MFA-kihívást az Azure AD-hez való kapcsolódáskor, ha [ezt a blogbejegyzést](http://www.anujchaudhary.com/2020/02/connect-to-azure-ad-powershell-with-mfa.html) követik az egyik mérnöktől. Ha egy PIM-alkalmazást fejleszt, az egyik lehetséges megvalósítás a felhasználók megtámadása és a modulhoz való Újrakapcsolódás, miután "MfaRule" hibaüzenetet kapnak.

## <a name="retrieving-and-updating-role-settings"></a>Szerepkör-beállítások beolvasása és frissítése

Az alábbi parancsmaggal kérheti le az összes szerepkör-beállítást az Azure AD-szervezetben.

```powershell
Get-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Filter "ResourceId eq '926d99e7-117c-4a6a-8031-0cc481e9da26'"
```

A beállításban négy fő objektum található. A PIM jelenleg csak három objektumot használ. A UserMemberSettings az aktiválási beállítások, a AdminEligibleSettings a jogosult hozzárendelések hozzárendelési beállításai, a AdminmemberSettings pedig az aktív hozzárendelések hozzárendelési beállításai.

[![Szerepkör-beállítások beolvasása és frissítése.](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png)](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png#lightbox)

A szerepkör-beállítás frissítéséhez be kell szereznie egy adott szerepkör meglévő beállítási objektumát, és módosítania kell azt:

```powershell
Get-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Filter "ResourceId eq 'tenant id' and RoleDefinitionId eq 'role id'"
$settinga = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedRuleSetting
$settinga.RuleIdentifier = "JustificationRule"
$settinga.Setting = '{"required":false}'
```

Ezután előre elvégezheti a beállítást, és alkalmazhatja egy adott szerepkör egyik objektumára az alábbi ábrán látható módon. Az azonosító itt a szerepkör-beállítási azonosító, amely a lista szerepkör-beállítások parancsmagjának eredményéről kérhető le.

```powershell
Set-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Id 'ff518d09-47f5-45a9-bb32-71916d9aeadf' -ResourceId '3f5887ed-dd6e-4821-8bde-c813ec508cf9' -RoleDefinitionId '2387ced3-4e95-4c36-a915-73d803f93702' -UserMemberSettings $settinga 
```

## <a name="next-steps"></a>Következő lépések

- [Egyéni Azure AD-szerepkör kiosztása](azure-ad-custom-roles-assign.md)
- [Egyéni Azure AD-szerepkör-hozzárendelés eltávolítása vagy frissítése](azure-ad-custom-roles-update-remove.md)
- [Egyéni Azure AD-szerepkör-hozzárendelés konfigurálása](azure-ad-custom-roles-configure.md)
- [Szerepkör-definíciók az Azure AD-ben](../roles/permissions-reference.md)
