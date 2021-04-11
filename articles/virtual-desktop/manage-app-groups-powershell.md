---
title: Windows rendszerű virtuális asztali PowerShell – Azure-beli alkalmazás-csoportok kezelése
description: Windows rendszerű virtuális asztali alkalmazás-csoportok kezelése a PowerShell-lel.
author: Heidilohr
ms.topic: how-to
ms.date: 04/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 1652048da8084007bc86a6793f14f10b4a1f2e0f
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106446043"
---
# <a name="manage-app-groups-using-powershell"></a>Alkalmazás-csoportok kezelése a PowerShell-lel

>[!IMPORTANT]
>Ez a tartalom a Windows rendszerű virtuális asztali Azure Resource Manager Windows virtuális asztali objektumokkal vonatkozik. Ha a Windowsos virtuális asztalt (klasszikus) Azure Resource Manager objektumok nélkül használja, tekintse meg [ezt a cikket](./virtual-desktop-fall-2019/manage-app-groups-2019.md).

Az új Windows rendszerű virtuális asztali készlethez létrehozott alapértelmezett alkalmazáscsoport a teljes asztalt is közzéteszi. Emellett létrehozhat egy vagy több RemoteApp-alkalmazáscsoport is a gazdagéphez. Ezt az oktatóanyagot követve hozzon létre egy RemoteApp-alkalmazáscsoport alkalmazást, és tegye közzé az egyes **Start** menüket.

Az oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Hozzon létre egy RemoteApp-csoportot.
> * Hozzáférés biztosítása RemoteApp-programokhoz.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy követte a PowerShell- [modul beállítása](powershell-module.md) a PowerShell-modul beállításához és az Azure-fiókba való bejelentkezéshez című témakör utasításait.

## <a name="create-a-remoteapp-group"></a>RemoteApp-csoport létrehozása

RemoteApp-csoport létrehozása a PowerShell-lel:

1. A következő PowerShell-parancsmag futtatásával hozzon létre egy új, üres RemoteApp-alkalmazás csoportot.

   ```powershell
   New-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname> -ApplicationGroupType "RemoteApp" -HostPoolArmPath '/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName/providers/Microsoft.DesktopVirtualization/hostPools/HostPoolName'-Location <azureregion>
   ```

2. Választható Annak ellenőrzéséhez, hogy az alkalmazáscsoport létrejött-e, a következő parancsmag futtatásával megtekintheti az alkalmazáskészlethez tartozó összes alkalmazás-csoportot.

   ```powershell
   Get-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname>
   ```

3. Futtassa a következő parancsmagot a **Start** menü alkalmazások listájának lekéréséhez a gazdaszámítógép virtuálisgép-rendszerképén. Jegyezze fel a közzétenni kívánt alkalmazás **filepath**, **IconPath**, **IconIndex** és egyéb fontos információinak értékeit.

   ```powershell
   Get-AzWvdStartMenuItem -ApplicationGroupName <appgroupname> -ResourceGroupName <resourcegroupname> | Format-List | more
   ```

   A kimenetnek az alábbihoz hasonló formátumban kell megjelenítenie az összes Start menü elemet:

   ```powershell
   AppAlias            : access
   CommandLineArgument :
   FilePath            : C:\Program Files\Microsoft Office\root\Office16\MSACCESS.EXE
   FriendlyName        :
   IconIndex           : 0
   IconPath            : C:\Program Files\Microsoft Office\Root\VFS\Windows\Installer\{90160000-000F-0000-1000-0000000FF1CE}\accicons.exe
   Id                  : /subscriptions/resourcegroups/providers/Microsoft.DesktopVirtualization/applicationgroups/startmenuitems/Access
   Name                : 0301RAG/Access
   Type                : Microsoft.DesktopVirtualization/applicationgroups/startmenuitems

   AppAlias            : charactermap
   CommandLineArgument :
   FilePath            : C:\windows\system32\charmap.exe
   FriendlyName        :
   IconIndex           : 0
   IconPath            : C:\windows\system32\charmap.exe
   Id                  : /subscriptions/resourcegroups/providers/Microsoft.DesktopVirtualization/applicationgroups/startmenuitems/Character Map
   Name                : 0301RAG/Character Map
   Type                : Microsoft.DesktopVirtualization/applicationgroups/startmenuitems
   ```

4. A következő parancsmag futtatásával telepítheti az alkalmazást a alapján `AppAlias` . `AppAlias` a 3. lépés kimenetének futtatásakor láthatóvá válik.

   ```powershell
   New-AzWvdApplication -AppAlias <appalias> -GroupName <appgroupname> -Name <remoteappname> -ResourceGroupName <resourcegroupname> -CommandLineSetting <DoNotAllow|Allow|Require>
   ```

5. Választható Futtassa a következő parancsmagot egy új RemoteApp-program közzétételéhez az 1. lépésben létrehozott alkalmazáscsoport számára.

   ```powershell
   New-AzWvdApplication -GroupName <appgroupname> -Name <remoteappname> -ResourceGroupName <resourcegroupname> -Filepath <filepath> -IconPath <iconpath> -IconIndex <iconindex> -CommandLineSetting <DoNotAllow|Allow|Require>
   ```

6. Az alkalmazás közzétételének ellenőrzéséhez futtassa a következő parancsmagot.

   ```powershell
   Get-AzWvdApplication -GroupName <appgroupname> -ResourceGroupName <resourcegroupname>
   ```

7. Ismételje meg az 1 – 5. lépést minden olyan alkalmazás esetében, amelyet közzé szeretne tenni ehhez az alkalmazási csoporthoz.
8. Futtassa a következő parancsmagot, hogy hozzáférést biztosítson a felhasználóknak a RemoteApp-programokhoz az alkalmazás csoportban.

   ```powershell
   New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
   ```

## <a name="next-steps"></a>Következő lépések

Ha ezt az útmutatót az oktatóanyagokból olvassa be, tekintse meg [a gazdagépek létrehozása a szolgáltatás frissítéseinek](create-validation-host-pool.md)ellenőrzéséhez című témakört. Az ellenőrzési gazdagépek segítségével figyelheti a szolgáltatás frissítéseit, mielőtt az éles környezetbe helyezné őket.