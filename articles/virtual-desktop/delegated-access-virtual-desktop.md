---
title: Delegált hozzáférés Windows virtuális asztal előzetes verziója – Azure
description: Hogyan kell delegálni a felügyeleti funkciókat biztosít a Windows virtuális asztal előzetes központi telepítés, beleértve a példákat.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 250aea52de63a6397ce00e9cadcadf3a8ba39858
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60870506"
---
# <a name="delegated-access-in-windows-virtual-desktop-preview"></a>Windows virtuális asztal előzetes verzióban elérhető delegált hozzáférés

Windows virtuális asztal előzetes rendelkezik delegált hozzáférés modell, amely lehetővé teszi olyan mértékű hozzáférést egy adott felhasználó jogosult szerepkör hozzárendelése szerint határozza meg. Szerepkör-hozzárendelés három részből áll: szolgáltatásnév, szerepkör-definíció és hatókör. A Windows virtuális asztal delegált hozzáférés modell alapján az Azure RBAC-modellben. Az adott szerepkör-hozzárendelések és azok összetevőiből kapcsolatos további információkért lásd: [az Azure szerepköralapú hozzáférés-vezérlés áttekintése](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles).

Windows virtuális asztal delegált hozzáférés által támogatott összes eleme a szerepkör-hozzárendelést a következő értékeket:

* Rendszerbiztonsági tag
    * Felhasználók
    * Az egyszerű szolgáltatások
* Szerepkör-definíció
    * Beépített szerepkörök
* Scope
    * Bérlő csoportok
    * Bérlők
    * Gazdagép-készletek
    * Alkalmazáscsoportok

## <a name="built-in-roles"></a>Beépített szerepkörök

A Windows virtuális asztal delegált hozzáférés hozzárendelheti a felhasználókhoz és az egyszerű szolgáltatások számos beépített szerepkör-definíciók rendelkezik.

* A távoli asztali szolgáltatások tulajdonosa mindent felügyelhetnek, beleértve az erőforrásokhoz való hozzáférést.
* Egy távoli asztali szolgáltatások közreműködői erőforrásokhoz való hozzáférés kivételével mindent felügyelhetnek.
* Egy távoli asztali szolgáltatások olvasó, aki mindent megtekinthet, de nem módosíthatja.
* A távoli asztali szolgáltatások az operátornak diagnosztikai tevékenységek megtekintéséhez.

## <a name="powershell-cmdlets-for-role-assignments"></a>PowerShell-parancsmagok a szerepkör-hozzárendelések

Futtassa a következő parancsmagokat létrehozása, megtekintése és szerkesztése a szerepkör-hozzárendelések:

* **Get-RdsRoleAssignment** szerepkör-hozzárendelések listáját jeleníti meg.
* **Új RdsRoleAssignment** hoz létre egy új szerepkör-hozzárendelést.
* **Set-RdsRoleAssignment** szerkeszti a szerepkör-hozzárendeléseket.

### <a name="accepted-parameters"></a>Elfogadott paramétereinek

Módosíthatja a alapszintű három parancsmag a következő paraméterekkel:

* **AadTenantId**: Itt adhatja meg, amely tagja az egyszerű szolgáltatás Azure Active Directory bérlői azonosító.
* **AppGroupName**: a távoli asztal alkalmazás csoport neve.
* **Diagnosztikai**: diagnosztika hatókörét jelzi. (Akár kell megfeleltetni a **infrastruktúra** vagy **bérlői** paraméterek.)
* **HostPoolName**: a távoli asztali állomás készlet nevét.
* **Infrastruktúra**: azt jelzi, hogy az infrastruktúra hatókör.
* **RoleDefinitionName**: a felhasználó, csoport vagy alkalmazás rendelt távoli asztali szolgáltatások szerepköralapú hozzáférés-vezérlési szerepkör nevét. (Például távoli asztali szolgáltatások tulajdonos, távoli asztali szolgáltatások olvasó és így tovább.)
* **ServerPrincipleName**: az Azure Active Directory-alkalmazás neve.
* **SignInName**: a felhasználó e-mail címét vagy egyszerű felhasználónév.
* **TenantName**: a távoli asztal bérlő neve.

## <a name="next-steps"></a>További lépések

Minden szerepkör által használható PowerShell-parancsmagok teljes listájáért tekintse meg a [PowerShell-referencia](/powershell/windows-virtual-desktop/overview).

Egy Windows virtuális asztali környezet beállításához útmutatást, lásd: [Windows virtuális asztal előzetes verziójú környezet](environment-setup.md).
