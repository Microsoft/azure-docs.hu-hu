---
title: Windows-licenc alkalmazása munkamenetgazda virtuális gépekre – Azure
description: Ismerteti, hogyan alkalmazhatja a Windows-licencet Windows Virtual Desktop virtuális gépekre.
author: Heidilohr
ms.topic: how-to
ms.date: 08/14/2019
ms.author: helohr
ms.openlocfilehash: fa3c9f82e99536b07a27656e0143d6b2fcc89a44
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833867"
---
# <a name="apply-windows-license-to-session-host-virtual-machines"></a>Windows-licenc alkalmazása munkamenetgazda virtuális gépekre

Azok az ügyfelek, akik megfelelő licenccel Windows Virtual Desktop számítási feladatok futtatásához, Windows-licencet alkalmazhatnak a munkamenetgazda virtuális gépeikre, és futtathatnak egy másik licencért való fizetés nélkül. További információ: díjszabás [Windows Virtual Desktop.](https://azure.microsoft.com/pricing/details/virtual-desktop/)

## <a name="ways-to-use-your-windows-virtual-desktop-license"></a>A licenc használatának Windows Virtual Desktop módjai
Windows Virtual Desktop licencelés lehetővé teszi licencek alkalmazását bármely Windows vagy Windows Server rendszerű virtuális gépre, amely munkamenet-gazdagépként van regisztrálva egy gazdagépkészletben, és felhasználói kapcsolatokat fogad. Ez a licenc nem vonatkozik a fájlmegosztási kiszolgálóként, tartományvezérlőként stb. futó virtuális gépekre.

A licenc több módon is Windows Virtual Desktop használható:
- A gazdagépkészletet és annak munkamenetgazda virtuális gépeit a következő ajánlat [Azure Marketplace létrehozhatja:](./create-host-pools-azure-marketplace.md). Az így létrehozott virtuális gépekre automatikusan alkalmazva lesz a licenc.
- Gazdagépkészletet és annak munkamenetgazda virtuális gépeit a [GitHub Azure Resource Manager hozhat létre.](./virtual-desktop-fall-2019/create-host-pools-arm-template.md) Az így létrehozott virtuális gépekre automatikusan alkalmazva lesz a licenc.
- A licencet meglévő munkamenetgazda virtuális gépre is alkalmazhatja. Ehhez először kövesse a Gazdagépkészlet létrehozása [a PowerShell](./create-host-pools-powershell.md) használatával cikkben található utasításokat egy gazdagépkészlet és a társított virtuális gépek létrehozásához, majd térjen vissza ehhez a cikkhez, és ismerje meg, hogyan alkalmazhatja a licencet.

## <a name="apply-a-windows-license-to-a-session-host-vm"></a>Windows-licenc alkalmazása munkamenetgazda virtuális gépre
Győződjön meg arról, hogy telepítette és [konfigurálta a legújabb Azure PowerShell.](/powershell/azure/) Futtassa a következő PowerShell-parancsmagot a Windows-licenc alkalmazáshoz:

```powershell
$vm = Get-AzVM -ResourceGroup <resourceGroupName> -Name <vmName>
$vm.LicenseType = "Windows_Client"
Update-AzVM -ResourceGroupName <resourceGroupName> -VM $vm
```

## <a name="verify-your-session-host-vm-is-utilizing-the-licensing-benefit"></a>Annak ellenőrzése, hogy a munkamenetgazda virtuális gépe használja-e a licencelési előnyt
A virtuális gép üzembe helyezése után futtassa ezt a parancsmagot a licenctípus ellenőrzéséhez:
```powershell
Get-AzVM -ResourceGroupName <resourceGroupName> -Name <vmName>
```

Az alkalmazott Windows-licenccel rendelkező munkamenetgazda virtuális gép a következőt fogja mutatni:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

Az alkalmazott Windows-licenccel nem rendelkező virtuális gépek a következőt fogják mutatni:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

Futtassa a következő parancsmagot az összes olyan munkamenetgazda virtuális gép listájának a listájának a listájához, amelyekre az Azure-előfizetésében windowsos licenc van alkalmazva:

```powershell
$vms = Get-AzVM
$vms | Where-Object {$_.LicenseType -like "Windows_Client"} | Select-Object ResourceGroupName, Name, LicenseType
```

## <a name="requirements-for-deploying-windows-server-remote-desktop-services"></a>A Windows Server-kiszolgálók telepítésének Távoli asztali szolgáltatások

Ha a telepítésben Windows Server 2019, 2016 vagy 2012 R2 rendszert telepít Windows Virtual Desktop-gazdagépként, egy Távoli asztali szolgáltatások-licenckiszolgálónak elérhetőnek kell lennie a virtuális gépekről. A Távoli asztali szolgáltatások-licenckiszolgáló a helyszínen vagy az Azure-ban is lehet. További információ: Activate the Távoli asztali szolgáltatások license server (Licenckiszolgáló [aktiválása).](/windows-server/remote/remote-desktop-services/rds-activate-license-server)
