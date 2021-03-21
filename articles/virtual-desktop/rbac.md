---
title: Beépített szerepkörök Windows rendszerű virtuális asztallal – Azure
description: Az Azure RBAC elérhető Windows rendszerű virtuális asztali szolgáltatások beépített szerepköreinek áttekintése.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 1120db685c54ff062f03aca9002bf77af549bc26
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104582119"
---
# <a name="built-in-roles-for-windows-virtual-desktop"></a>Beépített szerepkörök a Windows rendszerű virtuális asztalhoz

A Windows virtuális asztal Azure szerepköralapú hozzáférés-vezérlés (RBAC) használatával rendel szerepköröket a felhasználókhoz és a rendszergazdákhoz. Ezek a szerepkörök engedélyeket adnak a rendszergazdáknak bizonyos feladatok elvégzéséhez. Ha többet szeretne megtudni az Azure RBAC beépített szerepköreiről, tekintse meg az [Azure beépített szerepkörei](../role-based-access-control/built-in-roles.md)című témakört.

Az Azure-hoz készült standard beépített szerepkörök a következők: tulajdonos, közreműködő és olvasó. A Windows rendszerű virtuális asztali számítógépeken azonban további szerepkörök is megtalálhatók, amelyek lehetővé teszik a gazdagépek, az alkalmazások csoportjai és a munkaterületek felügyeleti szerepköreinek elkülönítését. Ez a szétválasztás lehetővé teszi a felügyeleti feladatok részletesebb szabályozását. Ezeket a szerepköröket az Azure standard szerepköreinek és a legalacsonyabb jogosultsági szintű módszereknek megfelelően nevezték el.

A Windows virtuális asztal nem rendelkezik konkrét tulajdonosi szerepkörrel. A szolgáltatási objektumok esetében azonban szabványos tulajdonosi szerepkört is használhat.

## <a name="desktop-virtualization-contributor"></a>Asztali virtualizálási közreműködő

Az asztali virtualizálási közreműködő szerepkör lehetővé teszi az üzemelő példány összes aspektusának kezelését. Azonban nem biztosít hozzáférést a számítási erőforrásokhoz. Emellett a felhasználói hozzáférés rendszergazdai szerepkörre is szüksége lesz az alkalmazások felhasználói vagy felhasználói csoportok számára való közzétételéhez.


- Microsoft. DesktopVirtualization/\* 
- Microsoft. Resources/Subscriptions/resourceGroups/READ
- Microsoft. Resources/üzemelő példány/\*
- Microsoft. Authorization/ \* /READ
- Microsoft. bepillantások/alertRules/\*
- Microsoft. support/\*

## <a name="desktop-virtualization-reader"></a>Asztali virtualizálási olvasó

Az asztali virtualizálási olvasó szerepkör lehetővé teszi, hogy megtekintse a telepítés összes adatát, de nem teszi lehetővé a módosítások elvégzését.

- Microsoft. DesktopVirtualization/ \* /READ
- Microsoft. Resources/Subscriptions/resourceGroups/READ
- Microsoft. Resources/központi telepítések/olvasás
- Microsoft. Authorization/ \* /READ
- Microsoft. bepillantások/alertRules/\*
- Microsoft. support/\*

## <a name="host-pool-contributor"></a>Alkalmazáskészlet közreműködője

A Host Pool közreműködő szerepkör lehetővé teszi a gazdagépek összes aspektusának kezelését, beleértve az erőforrásokhoz való hozzáférést is. Virtuális gépek létrehozásához szüksége lesz egy további közreműködő szerepkörre, a virtuális gép közreműködőire. Az AppGroup és a munkaterület közreműködői szerepköreinek a portál használatával történő létrehozásához vagy az asztali virtualizálási közreműködő szerepkör használatához szüksége lesz.

A következő lista ismerteti, hogy a szerepkör milyen engedélyeket tud elérni:

- Microsoft. DesktopVirtualization/hostpools/\*
- Microsoft. Resources/Subscriptions/resourceGroups/READ
- Microsoft. Resources/üzemelő példány/\*
- Microsoft. Authorization/ \* /READ
- Microsoft. bepillantások/alertRules/\*
- Microsoft. support/\*

## <a name="host-pool-reader"></a>Fogadó készlet olvasója

A gazdagép-olvasó szerepkör lehetővé teszi a gazdagép összes adatának megtekintését, de nem teszi lehetővé a módosítások elvégzését.

- Microsoft. DesktopVirtualization/hostpools/ \* /READ
- Microsoft. Resources/Subscriptions/resourceGroups/READ
- Microsoft. Resources/központi telepítések/olvasás
- Microsoft. Authorization/ \* /READ
- Microsoft. bepillantások/alertRules/\*
- Microsoft. support/\*

## <a name="application-group-contributor"></a>Alkalmazáscsoport közreműködői

Az Application Group közreműködő szerepkör lehetővé teszi az alkalmazások csoportjai összes aspektusának kezelését. Ha felhasználói vagy felhasználói csoportoknak szeretné közzétenni az alkalmazás-csoportokat, szüksége lesz a felhasználói hozzáférés rendszergazdai szerepkörre.

A következő lista ismerteti, hogy a szerepkör milyen engedélyeket tud elérni:

- Microsoft. DesktopVirtualization/applicationgroups/\*
- Microsoft. DesktopVirtualization/hostpools/READ
- Microsoft. DesktopVirtualization/hostpools/sessionhosts/READ
- Microsoft. Resources/Subscriptions/resourceGroups/READ
- Microsoft. Resources/üzemelő példány/\*
- Microsoft. Authorization/ \* /READ
- Microsoft. bepillantások/alertRules/\*
- Microsoft. support/\*

## <a name="application-group-reader"></a>Alkalmazáscsoport-olvasó

Az alkalmazáscsoport-olvasó szerepkör lehetővé teszi, hogy megtekintse az App Group összes adatát, és nem teszi lehetővé a módosítások elvégzését.

A következő lista ismerteti, hogy a szerepkör milyen engedélyeket tud elérni:

- Microsoft. DesktopVirtualization/applicationgroups/ \* /READ
- Microsoft. DesktopVirtualization/applicationgroups/READ
- Microsoft. DesktopVirtualization/hostpools/READ
- Microsoft. DesktopVirtualization/hostpools/sessionhosts/READ
- Microsoft. Resources/Subscriptions/resourceGroups/READ
- Microsoft. Resources/központi telepítések/olvasás
- Microsoft. Authorization/ \* /READ
- Microsoft. bepillantások/alertRules/\*
- Microsoft. support/\*

## <a name="workspace-contributor"></a>Munkaterület közreműködője

A munkaterület-közreműködő szerepkör lehetővé teszi a munkaterületek összes aspektusának kezelését. Az alkalmazás-csoportokhoz hozzáadott alkalmazásokkal kapcsolatos információk lekéréséhez hozzá kell rendelnie az alkalmazáscsoport-olvasó szerepkört is.

A következő lista ismerteti, hogy a szerepkör milyen engedélyeket tud elérni:

- Microsoft. DesktopVirtualization/munkaterületek/\*
- Microsoft. DesktopVirtualization/applicationgroups/READ
- Microsoft. Resources/Subscriptions/resourceGroups/READ
- Microsoft. Resources/üzemelő példány/\*
- Microsoft. Authorization/ \* /READ
- Microsoft. bepillantások/alertRules/\*
- Microsoft. support/\*

## <a name="workspace-reader"></a>Munkaterület-olvasó

A munkaterület-olvasó szerepkör lehetővé teszi a munkaterület összes adatának megtekintését, de nem teszi lehetővé a módosítások elvégzését.

A következő lista ismerteti, hogy a szerepkör milyen engedélyeket tud elérni:

- Microsoft. DesktopVirtualization/munkaterületek/olvasás
- Microsoft. DesktopVirtualization/applicationgroups/READ
- Microsoft. Resources/Subscriptions/resourceGroups/READ
- Microsoft. Resources/központi telepítések/olvasás
- Microsoft. Authorization/ \* /READ
- Microsoft. bepillantások/alertRules/\*
- Microsoft. support/\*

## <a name="user-session-operator"></a>Felhasználói munkamenet operátora

A felhasználói munkamenet-kezelő szerepkör lehetővé teszi üzenetek küldését, a munkamenetek leválasztását és a "kijelentkezés" függvény használatát a munkamenetek kikapcsolásához a munkamenet-gazdagépről. Ez a szerepkör azonban nem teszi lehetővé a munkamenet-gazdagépek kezelését, például a munkamenet-gazdagép eltávolítását, a kiürítési mód módosítását stb. Ez a szerepkör a hozzárendeléseket láthatja, de nem tudja módosítani a rendszergazdákat. Azt javasoljuk, hogy ezt a szerepkört egy adott gazdagép-készlethez rendelje. Ha egy erőforráscsoport szintjén megadja ezt az engedélyt, a rendszergazda olvasási engedéllyel fog rendelkezni az erőforráscsoport alatti összes gazdagépen.

A következő lista ismerteti, hogy a szerepkör milyen engedélyeket tud elérni:

- Microsoft. DesktopVirtualization/hostpools/READ
- Microsoft. DesktopVirtualization/hostpools/sessionhosts/READ
- Microsoft. DesktopVirtualization/hostpools/sessionhosts/usersessions/\*
- Microsoft. Resources/Subscriptions/resourceGroups/READ
- Microsoft. Resources/központi telepítések/olvasás
- Microsoft. Authorization/ \* /READ
- Microsoft. bepillantások/alertRules/\*
- Microsoft. support/\*

## <a name="session-host-operator"></a>Munkamenet-gazda operátor

A munkamenet-gazdagép operátori szerepkör lehetővé teszi a munkamenet-gazdagépek megtekintését és eltávolítását, valamint a kiürítési mód módosítását. A Azure Portal használatával nem adhatnak hozzá munkamenet-gazdagépeket, mert nem rendelkeznek írási engedéllyel a gazdagép-objektumokhoz. Ha a regisztrációs jogkivonat érvényes (létrehozva és nem lejárt), akkor ezt a szerepkört használhatja a Azure Portalon kívüli gazdagéphez való hozzáadásához, ha a rendszergazda a virtuális gép közreműködői szerepkörén keresztül kiszámítja az engedélyeket.

A következő lista ismerteti, hogy a szerepkör milyen engedélyeket tud elérni:

- Microsoft. DesktopVirtualization/hostpools/READ
- Microsoft. DesktopVirtualization/hostpools/sessionhosts/\*
- Microsoft. Resources/Subscriptions/resourceGroups/READ
- Microsoft. Resources/központi telepítések/olvasás
- Microsoft. Authorization/ \* /READ
- Microsoft. bepillantások/alertRules/\*
- Microsoft. support/\*
