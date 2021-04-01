---
title: Delegált hozzáférés a Windows Virtual Desktopban (klasszikus) – Azure
description: Felügyeleti képességek delegálása Windows rendszerű virtuális asztali (klasszikus) környezetben, példákat is beleértve.
author: Heidilohr
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0152dc5102fd3f77418448234cab1234d25b97c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88008151"
---
# <a name="delegated-access-in-windows-virtual-desktop-classic"></a>Delegált hozzáférés a Windows Virtual Desktopban (klasszikus)

>[!IMPORTANT]
>Ez a tartalom a Windows rendszerű virtuális asztali (klasszikus) verzióra vonatkozik, amely nem támogatja Azure Resource Manager Windows rendszerű virtuális asztali objektumokat. Ha Azure Resource Manager Windows rendszerű virtuális asztali objektumokat szeretne felügyelni, tekintse meg [ezt a cikket](../delegated-access-virtual-desktop.md).

A Windows virtuális asztal olyan delegált hozzáférési modellel rendelkezik, amely lehetővé teszi, hogy egy adott felhasználó számára engedélyezett mennyiségű hozzáférést rendeljen hozzá egy szerepkörhöz. A szerepkör-hozzárendelés három összetevőből áll: rendszerbiztonsági tag, szerepkör-definíció és hatókör. A Windows rendszerű virtuális asztali delegált hozzáférési modell az Azure RBAC-modellen alapul. Ha többet szeretne megtudni az adott szerepkör-hozzárendelésekről és azok összetevőiről, tekintse meg [Az Azure szerepköralapú hozzáférés-vezérlés áttekintését](../../role-based-access-control/built-in-roles.md).

A Windows virtuális asztal delegált hozzáférése a következő értékeket támogatja a szerepkör-hozzárendelés minden eleméhez:

* Rendszerbiztonsági tag
    * Felhasználók
    * Szolgáltatásnevek
* Szerepkör-definíció
    * Beépített szerepkörök
* Hatókör
    * Bérlői csoportok
    * Bérlők
    * Gazdagépek készletei
    * Alkalmazáscsoportok

## <a name="built-in-roles"></a>Beépített szerepkörök

A Windows rendszerű virtuális asztal delegált hozzáférése számos beépített szerepkör-definícióval rendelkezik, amelyeket hozzárendelhet a felhasználókhoz és az egyszerű szolgáltatásokhoz.

* Az RDS-tulajdonosok mindent kezelhetnek, beleértve az erőforrásokhoz való hozzáférést is.
* Az RDS-közreműködők mindent kezelhetnek, de nem férhetnek hozzá az erőforrásokhoz.
* Az RDS-olvasó mindent megtekinthet, de nem végezhet módosításokat.
* Az RDS-operátorok megtekinthetik a diagnosztikai tevékenységeket.

## <a name="powershell-cmdlets-for-role-assignments"></a>A szerepkör-hozzárendelések PowerShell-parancsmagjai

A következő parancsmagok futtatásával hozhatja létre, tekintheti meg és távolíthatja el a szerepkör-hozzárendeléseket:

* A **Get-RdsRoleAssignment** megjeleníti a szerepkör-hozzárendelések listáját.
* A **New-RdsRoleAssignment** új szerepkör-hozzárendelést hoz létre.
* A **Remove-RdsRoleAssignment** törli a szerepkör-hozzárendeléseket.

### <a name="accepted-parameters"></a>Elfogadott paraméterek

Az alapszintű három parancsmagot a következő paraméterekkel módosíthatja:

* **AadTenantId**: azt a Azure Active Directory bérlői azonosítót adja meg, amelyből az egyszerű szolgáltatásnév tagja.
* **AppGroupName**: a távoli asztal alkalmazás csoportjának neve.
* **Diagnosztika**: a diagnosztika hatókörét jelzi. (Párosítva kell lennie az **infrastruktúra** vagy a **bérlő** paramétereinek.)
* **HostPoolName**: a távoli asztal-címkészlet neve.
* **Infrastruktúra**: az infrastruktúra hatókörét jelzi.
* **RoleDefinitionName**: a felhasználóhoz, csoporthoz vagy alkalmazáshoz rendelt, szerepkörön alapuló hozzáférés-vezérlési szerepkör távoli asztali szolgáltatások neve. (Például Távoli asztali szolgáltatások tulajdonos, Távoli asztali szolgáltatások olvasó stb.)
* **ServerPrincipleName**: a Azure Active Directory alkalmazás neve.
* **SignInName**: a felhasználó e-mail-címe vagy egyszerű felhasználóneve.
* **TenantName**: a távoli asztal bérlő neve.

## <a name="next-steps"></a>Következő lépések

Az egyes szerepkörök által használható PowerShell-parancsmagok teljes listájáért tekintse meg a [PowerShell-referenciát](/powershell/windows-virtual-desktop/overview).

A Windows rendszerű virtuális asztali környezet beállításával kapcsolatos útmutatásért lásd: [Windows rendszerű virtuális asztali környezet](environment-setup-2019.md).
