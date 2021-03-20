---
title: PowerShell-modul Windowsos virtuális asztal – Azure
description: A PowerShell-modul telepítése és beállítása a Windows rendszerű virtuális asztalhoz.
author: Heidilohr
ms.topic: how-to
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f2f01e2b58c997db08ad4427de7eef1ee3760c4a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96016811"
---
# <a name="set-up-the-powershell-module-for-windows-virtual-desktop"></a>A PowerShell-modul beállítása a Windows rendszerű virtuális asztalhoz

>[!IMPORTANT]
>Ez a tartalom Azure Resource Manager-integrációval rendelkező Windows rendszerű virtuális asztalra vonatkozik.

A Windows rendszerű virtuális asztali PowerShell-modul integrálva van a Azure PowerShell modulba. Ebből a cikkből megtudhatja, hogyan állíthatja be a PowerShell-modult, hogy a Windows rendszerű virtuális asztal parancsmagokat futtasson.

## <a name="set-up-your-powershell-environment"></a>A PowerShell-környezet beállítása

A modul használatának megkezdéséhez először telepítse a [PowerShell Core legújabb verzióját](/powershell/scripting/install/installing-powershell#powershell-core). A Windows rendszerű virtuális asztali parancsmagok jelenleg csak a PowerShell Core-ban működnek.

Ezután telepítenie kell a DesktopVirtualization modult a PowerShell-munkamenetben való használathoz.

Futtassa a következő PowerShell-parancsmagot emelt szintű módban a modul telepítéséhez:

```powershell
Install-Module -Name Az.DesktopVirtualization
```

>[!NOTE]
> Ha ez a parancsmag nem működik, próbálja megismételni a futtatást emelt szintű engedélyekkel.

Ezután futtassa a következő parancsmagot az Azure-hoz való kapcsolódáshoz:

```powershell
Connect-AzAccount
```

>[!IMPORTANT]
>Ha csatlakozik a US Gov-portálhoz, futtassa a következő parancsmagot:
> 
> ```powershell
> Connect-AzAccount -EnvironmentName AzureUSGovernment
> ```

Az Azure-fiókba való bejelentkezéshez a kapcsolódási parancsmag futtatásakor generált kód szükséges. A bejelentkezéshez nyissa meg a következőt: <https://microsoft.com/devicelogin> , írja be a kódot, majd jelentkezzen be az Azure-beli rendszergazdai hitelesítő adataival.

```powershell
Account SubscriptionName TenantId Environment

------- ---------------- -------- -----------

Youradminupn subscriptionname AzureADTenantID AzureCloud
```

Ez közvetlenül a rendszergazdai hitelesítő adataihoz tartozó előfizetésbe fog bejelentkezni.

## <a name="change-the-default-subscription"></a>Az alapértelmezett előfizetés módosítása

Ha módosítani szeretné az alapértelmezett előfizetést a bejelentkezés után, futtassa a következő parancsmagot:

```powershell
Select-AzSubscription -Subscription <preferredsubscriptionname>
```

Az Out-GridView parancsmag használatával is kiválaszthat egyet egy listából:

```powershell
Get-AzSubscription | Out-GridView -PassThru | Select-AzSubscription
```

Amikor kiválasztja a használni kívánt új előfizetést, nem kell megadnia az előfizetés AZONOSÍTÓját a későbbiekben futtatott parancsmagokban. A következő parancsmag például az előfizetés-azonosító nélkül kérdez le egy adott munkamenet-gazdagépet:

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname>
```

Az előfizetéseket parancsmagok alapján is megváltoztathatja, ha a kívánt előfizetés nevét paraméterként adja hozzá. A következő parancsmag megegyezik az előző példával, kivéve a paraméterként hozzáadott előfizetés-azonosítót a parancsmag által használt előfizetés módosításához.

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname> -SubscriptionId <subscriptionGUID>
```

## <a name="get-locations"></a>Telephelyek lekérése

A Location paraméter megadása kötelező minden New **-AzWVD** parancsmagnál, amely új objektumokat hoz létre.

Futtassa a következő parancsmagot az előfizetés által támogatott helyszínek listájának lekéréséhez:

```powershell
Get-AzLocation
```

A **Get-AzLocation** kimenete a következőképpen fog kinézni:

```powershell
Location : eastasia

DisplayName : East Asia

Providers : {Microsoft.RecoveryServices, Microsoft.ManagedIdentity,
Microsoft.SqlVirtualMachine, microsoft.insightsΓÇª}

Location : southeastasia

DisplayName : Southeast Asia

Providers : {Microsoft.RecoveryServices, Microsoft.ManagedIdentity,
Microsoft.SqlVirtualMachine, microsoft.insightsΓÇª}

Location : centralus

DisplayName : Central US

Providers : {Microsoft.RecoveryServices, Microsoft.DesktopVirtualization,
Microsoft.ManagedIdentity, Microsoft.SqlVirtualMachineΓÇª}

Location : eastus

DisplayName : East US

Providers : {Microsoft.RecoveryServices, Microsoft.DesktopVirtualization,
Microsoft.ManagedIdentity, Microsoft.SqlVirtualMachineΓÇª}
```

Ha ismeri a fiókja helyét, használhatja azt egy parancsmagban. Íme például egy olyan parancsmag, amely létrehoz egy gazdagépet a "southeastasia" helyen:

```powershell
New-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -WorkspaceName <workspacename> -Location “southeastasia”
```

## <a name="next-steps"></a>Következő lépések

Most, hogy beállította a PowerShell-modult, a parancsmagok futtatásával különféle dolgokat végezhet a Windows virtuális asztalban. Íme néhány olyan hely, amelyet használhat a modulban:

- Futtassa a [Windows rendszerű virtuális asztali oktatóanyagokat]() a saját Windows rendszerű virtuális asztali környezetének beállításához.
- [Gazdagépcsoport létrehozása a PowerShell-lel](create-host-pools-powershell.md)
- [A Windows Virtual Desktop terheléselosztási módjának beállítása](configure-host-pool-load-balancing.md)
- [A személyes asztali címkészlet-hozzárendelés típusának konfigurálása](configure-host-pool-personal-desktop-assignment-type.md)
- És még sok más!

Ha bármilyen problémába ütközik, segítségért tekintse meg a [PowerShell hibaelhárítási cikkét](troubleshoot-powershell.md) .

