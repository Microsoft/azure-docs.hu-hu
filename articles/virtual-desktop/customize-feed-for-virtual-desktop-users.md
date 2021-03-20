---
title: A Windows rendszerű virtuális asztali felhasználók hírcsatornáinak testreszabása – Azure
description: A Windows rendszerű virtuális asztali felhasználók hírcsatornáinak testreszabása PowerShell-parancsmagokkal.
author: Heidilohr
ms.topic: how-to
ms.date: 09/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: e47486f29537cb948aaae7cf17e97bae14b60700
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90084294"
---
# <a name="customize-the-feed-for-windows-virtual-desktop-users"></a>A Windows rendszerű virtuális asztali felhasználók hírcsatornájának testreszabása

>[!IMPORTANT]
>Ez a tartalom a Windows rendszerű virtuális asztali Azure Resource Manager Windows virtuális asztali objektumokkal vonatkozik. Ha a Windowsos virtuális asztalt (klasszikus) Azure Resource Manager objektumok nélkül használja, tekintse meg [ezt a cikket](./virtual-desktop-fall-2019/customize-feed-virtual-desktop-users-2019.md).

Testreszabhatja a hírcsatornát, hogy a RemoteApp-és távoli asztali erőforrások felismerhető módon jelenjenek meg a felhasználók számára.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy már letöltötte és telepítette a Windows rendszerű virtuális asztali PowerShell-modult. Ha még nem tette meg, kövesse a [PowerShell-modul beállítása](powershell-module.md)című témakör utasításait.

## <a name="customize-the-display-name-for-a-remoteapp"></a>A RemoteApp megjelenítendő nevének testreszabása

A közzétett RemoteApp megjelenített nevét megváltoztathatja a felhasználóbarát név beállításával. Alapértelmezés szerint a felhasználóbarát név megegyezik a RemoteApp program nevével.

Az alkalmazáscsoport közzétett RemoteAppinak listájának lekéréséhez futtassa a következő PowerShell-parancsmagot:

```powershell
Get-AzWvdApplication -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname>
```

Ha rövid nevet szeretne hozzárendelni egy RemoteApp-hoz, futtassa a következő parancsmagot a szükséges paraméterekkel:

```powershell
Update-AzWvdApplication -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname> -FriendlyName <newfriendlyname>
```

Tegyük fel például, hogy az aktuális alkalmazásokat a következő példa parancsmaggal kérdezte le:

```powershell
Get-AzWvdApplication -ResourceGroupName 0301RG -ApplicationGroupName 0301RAG | format-list
```

A kimenet így néz ki:

```powershell
CommandLineArgument :
CommandLineSetting  : DoNotAllow
Description         :
FilePath            : C:\Program Files\Windows NT\Accessories\wordpad.exe
FriendlyName        : Microsoft Word
IconContent         : {0, 0, 1, 0…}
IconHash            : --iom0PS6XLu-EMMlHWVW3F7LLsNt63Zz2K10RE0_64
IconIndex           : 0
IconPath            : C:\Program Files\Windows NT\Accessories\wordpad.exe
Id                  : /subscriptions/<subid>/resourcegroups/0301RG/providers/Microsoft.DesktopVirtualization/applicationgroups/0301RAG/applications/Microsoft Word
Name                : 0301RAG/Microsoft Word
ShowInPortal        : False
Type                : Microsoft.DesktopVirtualization/applicationgroups/applications
```
A rövid név frissítéséhez futtassa a következő parancsmagot:

```powershell
Update-AzWvdApplication -GroupName 0301RAG -Name "Microsoft Word" -FriendlyName "WordUpdate" -ResourceGroupName 0301RG -IconIndex 0 -IconPath "C:\Program Files\Windows NT\Accessories\wordpad.exe" -ShowInPortal:$true -CommandLineSetting DoNotallow -FilePath "C:\Program Files\Windows NT\Accessories\wordpad.exe"
```

A rövid név sikeres frissítésének megerősítéséhez futtassa a következő parancsmagot:

```powershell
Get-AzWvdApplication -ResourceGroupName 0301RG -ApplicationGroupName 0301RAG | format-list FriendlyName
```

A parancsmagnak a következő kimenetet kell adnia:

```powershell
FriendlyName        : WordUpdate
```

## <a name="customize-the-display-name-for-a-remote-desktop"></a>Távoli asztal megjelenítendő nevének testreszabása

Egy közzétett távoli asztal megjelenítendő nevét a felhasználóbarát név beállításával módosíthatja. Ha manuálisan hozott létre egy gazdagép-készletet és egy asztali alkalmazást a PowerShell használatával, az alapértelmezett felhasználóbarát név a "munkamenet-asztal". Ha a GitHub Azure Resource Manager sablonnal vagy az Azure Marketplace-ajánlaton keresztül hozott létre egy gazdagép-készletet és egy asztali alkalmazást, az alapértelmezett felhasználóbarát név ugyanaz, mint a gazdagép-készlet neve.

A távoli asztal erőforrásának lekéréséhez futtassa a következő PowerShell-parancsmagot:

```powershell
Get-AzWvdDesktop -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname>
```

Ha rövid nevet szeretne rendelni a távoli asztali erőforráshoz, futtassa a következő PowerShell-parancsmagot:

```powershell
Update-AzWvdDesktop -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname> -FriendlyName <newfriendlyname>
```

## <a name="customize-a-display-name-in-azure-portal"></a>Megjelenítendő név testreszabása Azure Portal

A közzétett távoli asztal megjelenítendő nevét úgy módosíthatja, hogy egy rövid nevet állít be a Azure Portal használatával.

1. Jelentkezzen be az Azure Portalra a <https://portal.azure.com> webhelyen.

2. Keresse meg a **Windows rendszerű virtuális asztalt**.

3. A szolgáltatások területen válassza a **Windows virtuális asztal** elemet.

4. A Windows rendszerű virtuális asztal lapon válassza ki az **alkalmazáscsoport** elemet a képernyő bal oldalán, majd válassza ki a szerkeszteni kívánt alkalmazáscsoport nevét. (Ha például szerkeszteni szeretné az asztali alkalmazás csoport megjelenítendő nevét, válassza ki az **asztal** nevű alkalmazást.)

5. A képernyő bal oldalán található menüben válassza az **alkalmazások** lehetőséget.

6. Válassza ki a frissíteni kívánt alkalmazást, majd adjon meg egy új **megjelenítendő nevet**.

7. Kattintson a **Mentés** gombra. A szerkesztett alkalmazásnak ekkor meg kell jelennie a frissített névnek.

## <a name="next-steps"></a>Következő lépések

Most, hogy testre szabta a hírcsatornát a felhasználók számára, bejelentkezhet egy Windows rendszerű virtuális asztali ügyfélbe a teszteléshez. Ehhez folytassa a Kapcsolódás a Windows rendszerű virtuális asztali környezetekhez:

 * [Windows 10 vagy Windows 7 rendszerű kapcsolat](connect-windows-7-10.md)
 * [Kapcsolódás a webügyféllel](connect-web.md)
 * [Kapcsolódás az Android-ügyféllel](connect-android.md)
 * [Kapcsolódás az iOS-ügyfélhez](connect-ios.md)
 * [Kapcsolódás a macOS-ügyfélhez](connect-macos.md)
