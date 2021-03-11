---
title: Beépített alkalmazások közzététele a Windows rendszerű virtuális asztalon – Azure
description: Beépített alkalmazások közzététele a Windows Virtual Desktopban.
author: Heidilohr
ms.topic: how-to
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 32441a7da0e079a2de2fbd6906184c518d60e4a5
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102562860"
---
# <a name="publish-built-in-apps-in-windows-virtual-desktop"></a>Beépített alkalmazások közzététele a Windows rendszerű virtuális asztalon

>[!IMPORTANT]
>Ez a tartalom a Windows rendszerű virtuális asztali Azure Resource Manager Windows virtuális asztali objektumokkal vonatkozik. Ha a Windowsos virtuális asztalt (klasszikus) Azure Resource Manager objektumok nélkül használja, tekintse meg [ezt a cikket](./virtual-desktop-fall-2019/publish-apps-2019.md).

Ebből a cikkből megtudhatja, hogyan tehet közzé alkalmazásokat a Windows rendszerű virtuális asztali környezetben.

## <a name="publish-built-in-apps"></a>Beépített alkalmazások közzététele

Beépített alkalmazás közzététele:

1. Kapcsolódjon az egyik virtuális géphez a gazdagép-készletben.
2. A [cikkben](/powershell/module/appx/get-appxpackage)szereplő utasítások alapján szerezze be a közzétenni kívánt alkalmazás **PackageFamilyName** .
3. Végül futtassa a következő parancsmagot az `<PackageFamilyName>` előző lépésben megtalált **PackageFamilyName** lecserélve:

   ```powershell
   New-AzWvdApplication -Name <applicationname> -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -FilePath "shell:appsFolder\<PackageFamilyName>!App" -CommandLineSetting <Allow|Require|DoNotAllow> -IconIndex 0 -IconPath <iconpath> -ShowInPortal:$true
   ```

>[!NOTE]
> A Windows virtuális asztal csak a alkalmazással kezdődő telepítési hellyel rendelkező alkalmazások közzétételét támogatja `C:\Program Files\WindowsApps` .

## <a name="update-app-icons"></a>Alkalmazás ikonjainak frissítése

Miután közzétett egy alkalmazást, az alapértelmezés szerint a Windows-alkalmazás ikonja jelenik meg a normál ikon helyett. Ha módosítani szeretné az ikont a szokásos ikonjára, helyezze a kívánt ikont egy hálózati megosztásra. A támogatott képformátumok a következők: PNG, BMP, GIF, JPG, JPEG és ICO.

## <a name="publish-microsoft-edge"></a>Microsoft Edge közzététele

A Microsoft Edge közzétételéhez használt folyamat némileg eltér a közzétételi folyamattól más alkalmazások esetében. Ha közzé szeretné tenni a Microsoft Edge-t az alapértelmezett kezdőlapon, futtassa a következő parancsmagot:

```powershell
New-AzWvdApplication -Name -ResourceGroupName -ApplicationGroupName -FilePath "shell:Appsfolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge" -CommandLineSetting <Allow|Require|DoNotAllow> -iconPath "C:\Windows\SystemApps\Microsoft.MicrosoftEdge_8wekyb3d8bbwe\microsoftedge.exe" -iconIndex 0 -ShowInPortal:$true
```

## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan konfigurálhatja a hírcsatornákat úgy, hogy megszervezzék, hogyan jelenjenek meg az alkalmazások a [Windows rendszerű virtuális asztali felhasználók számára a hírcsatornák testreszabásakor](customize-feed-for-virtual-desktop-users.md).
- Ismerje meg a MSIX-alkalmazás csatolása funkciót a [MSIX-alkalmazás beállítása](app-attach.md)című témakörben.

