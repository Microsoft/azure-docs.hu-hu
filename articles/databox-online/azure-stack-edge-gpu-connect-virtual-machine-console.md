---
title: Kapcsolódás a Azure Stack Edge Pro GPU-eszközön futó virtuálisgép-konzolhoz
description: Ismerteti, hogyan csatlakozhat a virtuálisgép-konzolhoz Azure Stack Edge Pro GPU-eszközön futó virtuális gépen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/22/2021
ms.author: alkohli
ms.openlocfilehash: 68cf157a512e9b1f6caee4734869c5bb4b836e2f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104962567"
---
# <a name="connect-to-a-virtual-machine-console-on-an-azure-stack-edge-pro-gpu-device"></a>Kapcsolódás virtuálisgép-konzolhoz Azure Stack Edge Pro GPU-eszközön

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Stack Edge Pro GPU-megoldás a virtuális gépeken keresztül nem tároló munkaterheléseket futtat. Ez a cikk azt ismerteti, hogyan csatlakozhat az eszközön üzembe helyezett virtuális gépek konzolhoz. 

A virtuálisgép-konzol lehetővé teszi, hogy a leggyakrabban elérhető távoli asztali eszközök használatával hozzáférjen a virtuális gépekhez billentyűzettel, egérrel és képernyős funkciókkal. Elérheti a konzolt, és elháríthatja a virtuális gépek eszközön történő telepítésekor észlelt problémákat. Akkor is csatlakozhat a virtuálisgép-konzolhoz, ha a virtuális gép nem tudott kiépíteni.


## <a name="workflow"></a>Munkafolyamat

A magas szintű munkafolyamat a következő lépésekből áll:

1. Kapcsolódjon a PowerShell felületéhez az eszközön.
1. Engedélyezze a konzol elérését a virtuális géphez.
1. Csatlakozzon a virtuális géphez a Remote Desktop Protocol (RDP) használatával.
1. A konzolhoz való hozzáférés visszavonása a virtuális géphez.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy végrehajtotta a következő előfeltételeket:

#### <a name="for-your-device"></a>Az eszközhöz

Az aktivált Azure Stack Edge Pro GPU-eszközhöz hozzáféréssel kell rendelkeznie. Az eszközön telepíteni kell egy vagy több virtuális gépet. A virtuális gépeket Azure PowerShellon, a sablonokon keresztül vagy a Azure Portal keresztül is üzembe helyezheti.

#### <a name="for-client-accessing-the-device"></a>Az eszközt elérő ügyfél

Győződjön meg arról, hogy van hozzáférése egy olyan ügyfélrendszer számára, amely:

- Elérheti az eszköz PowerShell-felületét. Az ügyfél [támogatott operációs rendszert](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)futtat.
- Az ügyfél PowerShell 7,0-es vagy újabb verzióját futtatja. A PowerShell ezen verziója Windows-, Mac-és Linux-ügyfeleken működik. Lásd a [PowerShell 7,0 telepítésére](/powershell/scripting/whats-new/what-s-new-in-powershell-70?view=powershell-7.1&preserve-view=true)vonatkozó utasításokat.
- Távoli asztali képességekkel rendelkezik. Attól függően, hogy Windows, macOS vagy Linux rendszert használ, telepítenie kell a [Távoli asztali ügyfelek](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)egyikét. Ez a cikk útmutatást nyújt a [Windows távoli asztal](/windows-server/remote/remote-desktop-services/clients/windowsdesktop#install-the-client) és a [FreeRDP](https://www.freerdp.com/). <!--Which version of FreeRDP to use?-->


## <a name="connect-to-vm-console"></a>Kapcsolódás a VM-konzolhoz

Az alábbi lépéseket követve csatlakozhat az eszközön a virtuálisgép-konzolhoz.

### <a name="connect-to-the-powershell-interface-on-your-device"></a>Kapcsolódás a PowerShell-felülethez az eszközön

Az első lépés az eszköz [PowerShell-felületének csatlakoztatása](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) . 

### <a name="enable-console-access-to-the-vm"></a>Konzolhoz való hozzáférés engedélyezése a virtuális géphez

1.  A PowerShell-felületen futtassa a következő parancsot a virtuálisgép-konzolhoz való hozzáférés engedélyezéséhez.

    ```powershell
    Grant-HcsVMConnectAccess -ResourceGroupName <VM resource group> -VirtualMachineName <VM name>
    ```
2. Jegyezze fel a virtuális gép AZONOSÍTÓját a minta kimenetében. Ezt egy későbbi lépéshez kell megadnia.

    ```powershell
    [10.100.10.10]: PS>Grant-HcsVMConnectAccess -ResourceGroupName mywindowsvm1rg -VirtualMachineName mywindowsvm1
        
    VirtualMachineId       : 81462e0a-decb-4cd4-96e9-057094040063
    VirtualMachineHostName : 3V78B03
    ResourceGroupName      : mywindowsvm1rg
    VirtualMachineName     : mywindowsvm1
    Id                     : 81462e0a-decb-4cd4-96e9-057094040063
    [10.100.10.10]: PS>
    ```

### <a name="connect-to-the-vm"></a>Kapcsolódás a virtuális géphez

Most már használhat egy Távoli asztal ügyfelet a virtuálisgép-konzolhoz való kapcsolódáshoz.

#### <a name="use-windows-remote-desktop"></a>Windows Távoli asztal használata

1. Hozzon létre egy új szövegfájlt, és adja meg a következő szöveget.

    ```
    pcb:s:<VM ID from PowerShell>;EnhancedMode=0
    full address:s:<IP address of the device>   
    server port:i:2179
    username:s:EdgeARMUser
    negotiate security layer:i:0
    ```
1. Mentse a fájlt **. rdp* néven az ügyfél rendszerén. Ezt a profilt fogja használni a virtuális géphez való kapcsolódáshoz.
1. A virtuális géphez való kapcsolódáshoz kattintson duplán a profilra. Adja meg a következő hitelesítő adatokat:

    - **Felhasználónév**: Jelentkezzen be EdgeARMUser-ként.
    - **Password (jelszó**): adja meg az eszköz helyi Azure Resource Manager jelszavát. Ha elfelejtette a jelszót, [állítsa Alaphelyzetbe Azure Resource Manager jelszót a Azure Portal használatával](azure-stack-edge-gpu-set-azure-resource-manager-password.md#reset-password-via-the-azure-portal). 

#### <a name="use-freerdp"></a>FreeRDP használata

Ha a FreeRDP-t használja a Linux-ügyfélen, futtassa a következő parancsot: 

```powershell
./wfreerdp /u:EdgeARMUser /vmconnect:<VM ID from PowerShell> /v:<IP address of the device>
```

## <a name="revoke-vm-console-access"></a>Virtuálisgép-konzolhoz való hozzáférés visszavonása

A virtuálisgép-konzolhoz való hozzáférés visszavonásához térjen vissza az eszköz PowerShell-felületéhez. Futtassa az alábbi parancsot:

```
Revoke-HcsVMConnectAccess -ResourceGroupName <VM resource group> -VirtualMachineName <VM name>
```
Íme egy példa a kimenetre:

```powershell
[10.100.10.10]: PS>Revoke-HcsVMConnectAccess -ResourceGroupName mywindowsvm1rg -VirtualMachineName mywindowsvm1

VirtualMachineId       : 81462e0a-decb-4cd4-96e9-057094040063
VirtualMachineHostName : 3V78B03
ResourceGroupName      : mywindowsvm1rg
VirtualMachineName     : mywindowsvm1
Id                     : 81462e0a-decb-4cd4-96e9-057094040063

[10.100.10.10]: PS>
```
> [!NOTE] 
> Javasoljuk, hogy miután végzett a virtuálisgép-konzollal, visszavonja a hozzáférést, vagy zárja be a PowerShell ablakot a munkamenetből való kilépéshez. 

## <a name="next-steps"></a>Következő lépések

- A Azure Portal [Azure stack Edge Pro](azure-stack-edge-gpu-troubleshoot.md) hibáinak megoldása.