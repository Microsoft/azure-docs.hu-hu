---
title: Delegált hozzáférés a Windows rendszerű virtuális asztalon – Azure
description: Felügyeleti képességek delegálása Windows rendszerű virtuális asztali környezetben, például példákkal.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 899e42e3b592ce1bc92d7d47578e10355b1c6de7
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2020
ms.locfileid: "82614433"
---
# <a name="delegated-access-in-windows-virtual-desktop"></a>Delegált hozzáférés a Windows Virtual Desktopon

>[!IMPORTANT]
>Ez a tartalom a Fall 2019 kiadásra vonatkozik, amely nem támogatja a Windows rendszerű virtuális asztali objektumokat Azure Resource Manager. Ha a Spring 2020 Update szolgáltatásban bevezetett Azure Resource Manager Windows rendszerű virtuális asztali objektumokat szeretne felügyelni, tekintse meg [ezt a cikket](../delegated-access-virtual-desktop.md).

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

## <a name="next-steps"></a>További lépések

Az egyes szerepkörök által használható PowerShell-parancsmagok teljes listájáért tekintse meg a [PowerShell-referenciát](/powershell/windows-virtual-desktop/overview).

A Windows rendszerű virtuális asztali környezet beállításával kapcsolatos útmutatásért lásd: [Windows rendszerű virtuális asztali környezet](environment-setup-2019.md).
