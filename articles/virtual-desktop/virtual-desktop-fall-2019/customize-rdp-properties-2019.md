---
title: RDP-tulajdonságok testreszabása PowerShell-alapú Windows virtuális asztallal (klasszikus) – Azure
description: A Windows rendszerű virtuális asztali (klasszikus) RDP-tulajdonságainak testreszabása PowerShell-parancsmagokkal.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 1df576e3316a529ec38c4a53960ff817d129ffd8
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2020
ms.locfileid: "88002250"
---
# <a name="customize-remote-desktop-protocol-properties-for-a--windows-virtual-desktop-classic-host-pool"></a>Windows rendszerű virtuális asztali (klasszikus) címkészlet RDP protokoll tulajdonságainak testreszabása

>[!IMPORTANT]
>Ez a tartalom a Windows rendszerű virtuális asztali (klasszikus) verzióra vonatkozik, amely nem támogatja Azure Resource Manager Windows rendszerű virtuális asztali objektumokat. Ha Azure Resource Manager Windows rendszerű virtuális asztali objektumokat szeretne felügyelni, tekintse meg [ezt a cikket](../customize-rdp-properties.md).

A gazdagépek RDP protokoll (RDP) tulajdonságainak (például a többmonitoros élmény és a hangátirányítás) testreszabása lehetővé teszi, hogy a felhasználók igényei alapján optimális élményt nyújtson a felhasználóknak. Az RDP-tulajdonságokat a Windows virtuális asztalon a **set-RdsHostPool** parancsmag **-CustomRdpProperty** paraméterrel szabhatja testre.

A támogatott tulajdonságok és az alapértelmezett értékek teljes listájáért tekintse meg a [támogatott RDP-fájlok beállításait](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context) .

Először [töltse le és importálja a](/powershell/windows-virtual-desktop/overview/) PowerShell-munkamenetben használni kívánt Windows virtuális asztali PowerShell-modult, ha még nem tette meg. Ezután futtassa a következő parancsmagot a fiókjába való bejelentkezéshez:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="default-rdp-properties"></a>Alapértelmezett RDP-tulajdonságok

Alapértelmezés szerint a közzétett RDP-fájlok a következő tulajdonságokat tartalmazzák:

|RDP-tulajdonságok | Asztali számítógépek | RemoteApp-alkalmazások |
|---|---| --- |
| Többszörös figyelési mód | Engedélyezve | N/A |
| Meghajtó-átirányítások engedélyezve | Meghajtók, vágólap, nyomtatók, COM-portok, USB-eszközök és intelligens kártyák| Meghajtók, vágólap és nyomtatók |
| Távoli hang mód | Helyi lejátszás | Helyi lejátszás |

A gazdagép-készlethez definiált egyéni tulajdonságok felülbírálják ezeket az alapértelmezett értékeket.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>Egyetlen egyéni RDP-tulajdonság hozzáadása vagy szerkesztése

Egyetlen egyéni RDP-tulajdonság hozzáadásához vagy szerkesztéséhez futtassa a következő PowerShell-parancsmagot:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```

> [!div class="mx-imgBorder"]
> ![Képernyőkép a Get-RDSRemoteApp PowerShell-parancsmagról, amelynek neve és FriendlyName ki van emelve.](../media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Több egyéni RDP-tulajdonság hozzáadása vagy szerkesztése

Több egyéni RDP-tulajdonság hozzáadásához vagy szerkesztéséhez futtassa az alábbi PowerShell-parancsmagokat úgy, hogy az egyéni RDP-tulajdonságokat pontosvesszővel tagolt karakterláncként adja meg:

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```

> [!div class="mx-imgBorder"]
> ![Képernyőkép a Get-RDSRemoteApp PowerShell-parancsmagról, amelynek neve és FriendlyName ki van emelve.](../media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>Az összes egyéni RDP-tulajdonság alaphelyzetbe állítása

Az egyéni RDP-tulajdonságokat alapértékre állíthatja úgy, hogy az [Egyéni RDP-tulajdonság hozzáadása vagy szerkesztése](#add-or-edit-a-single-custom-rdp-property)lehetőségre kattint, vagy a következő PowerShell-parancsmag futtatásával alaphelyzetbe állíthatja a gazdagépek összes egyéni RDP-tulajdonságát:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```

> [!div class="mx-imgBorder"]
> ![Képernyőkép a Get-RDSRemoteApp PowerShell-parancsmagról, amelynek neve és FriendlyName ki van emelve.](../media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>További lépések

Most, hogy testreszabta az adott címkészlet RDP-tulajdonságait, bejelentkezhet egy Windows rendszerű virtuális asztali ügyfélbe, hogy egy felhasználói munkamenet részeként tesztelje őket. A következő két útmutató bemutatja, hogyan csatlakozhat egy munkamenethez az Ön által választott ügyfél használatával:

- [Kapcsolódás a Windows asztali ügyféllel](connect-windows-7-10-2019.md)
- [Kapcsolódás a webügyféllel](connect-web-2019.md)
