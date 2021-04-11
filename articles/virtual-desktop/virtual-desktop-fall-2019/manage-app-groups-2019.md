---
title: Windows rendszerű virtuális asztali (klasszikus) alkalmazások csoportjainak kezelése – Azure
description: Megtudhatja, hogyan állíthatja be a Windows rendszerű virtuális asztali (klasszikus) bérlőket Azure Active Directory (AD) szolgáltatásban.
author: Heidilohr
ms.topic: tutorial
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 0b95c87ad447b92532bc6c456c06eb85162c7247
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444411"
---
# <a name="tutorial-manage-app-groups-for-windows-virtual-desktop-classic"></a>Oktatóanyag: alkalmazás-csoportok kezelése a Windows rendszerű virtuális asztali gépeken (klasszikus)

>[!IMPORTANT]
>Ez a tartalom a Windows rendszerű virtuális asztali (klasszikus) verzióra vonatkozik, amely nem támogatja Azure Resource Manager Windows rendszerű virtuális asztali objektumokat. Ha Azure Resource Manager Windows rendszerű virtuális asztali objektumokat szeretne felügyelni, tekintse meg [ezt a cikket](../manage-app-groups.md).

Az új Windows rendszerű virtuális asztali készlethez létrehozott alapértelmezett alkalmazáscsoport a teljes asztalt is közzéteszi. Emellett létrehozhat egy vagy több RemoteApp-alkalmazáscsoport is a gazdagéphez. Ezt az oktatóanyagot követve hozzon létre egy RemoteApp-alkalmazáscsoport alkalmazást, és tegye közzé az egyes **Start** menüket.

Az oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Hozzon létre egy RemoteApp-csoportot.
> * Hozzáférés biztosítása RemoteApp-programokhoz.

Mielőtt elkezdené, [töltse le és importálja a](/powershell/windows-virtual-desktop/overview/) PowerShell-munkamenetben használni kívánt Windows virtuális asztali PowerShell-modult, ha még nem tette meg. Ezután futtassa a következő parancsmagot a fiókjába való bejelentkezéshez:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-a-remoteapp-group"></a>RemoteApp-csoport létrehozása

1. A következő PowerShell-parancsmag futtatásával hozzon létre egy új, üres RemoteApp-alkalmazás csoportot.

   ```powershell
   New-RdsAppGroup <tenantname> <hostpoolname> <appgroupname> -ResourceType "RemoteApp"
   ```

2. Választható Annak ellenőrzéséhez, hogy az alkalmazáscsoport létrejött-e, a következő parancsmag futtatásával megtekintheti az alkalmazáskészlethez tartozó összes alkalmazás-csoportot.

   ```powershell
   Get-RdsAppGroup <tenantname> <hostpoolname>
   ```

3. Futtassa a következő parancsmagot a **Start** menü alkalmazások listájának lekéréséhez a gazdaszámítógép virtuálisgép-rendszerképén. Jegyezze fel a közzétenni kívánt alkalmazás **filepath**, **IconPath**, **IconIndex** és egyéb fontos információinak értékeit.

   ```powershell
   Get-RdsStartMenuApp <tenantname> <hostpoolname> <appgroupname>
   ```

4. A következő parancsmag futtatásával telepítheti az alkalmazást a alapján `AppAlias` . `AppAlias` a 3. lépés kimenetének futtatásakor láthatóvá válik.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -AppAlias <appalias>
   ```

5. Választható Futtassa a következő parancsmagot egy új RemoteApp-program közzétételéhez az 1. lépésben létrehozott alkalmazáscsoport számára.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -Filepath <filepath>  -IconPath <iconpath> -IconIndex <iconindex>
   ```

6. Az alkalmazás közzétételének ellenőrzéséhez futtassa a következő parancsmagot.

   ```powershell
   Get-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname>
   ```

7. Ismételje meg az 1 – 5. lépést minden olyan alkalmazás esetében, amelyet közzé szeretne tenni ehhez az alkalmazási csoporthoz.
8. Futtassa a következő parancsmagot, hogy hozzáférést biztosítson a felhasználóknak a RemoteApp-programokhoz az alkalmazás csoportban.

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname> -UserPrincipalName <userupn>
   ```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan hozhat létre egy alkalmazáscsoport, hogyan tölthető be a RemoteApp-programokkal, és hogyan rendelhet hozzá felhasználókat az alkalmazás-csoporthoz. Az érvényesítési gazdagépek létrehozásáról a következő oktatóanyagban olvashat bővebben. Az ellenőrzési gazdagépek segítségével figyelheti a szolgáltatás frissítéseit, mielőtt az éles környezetbe helyezné őket.

> [!div class="nextstepaction"]
> [Gazdagépcsoport létrehozása a szolgáltatásfrissítések érvényesítéséhez](create-validation-host-pool-2019.md)
