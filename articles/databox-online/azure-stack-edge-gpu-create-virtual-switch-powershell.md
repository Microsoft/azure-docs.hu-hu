---
title: Új virtuális kapcsoló létrehozása Azure Stack Edge-ben a PowerShell használatával
description: Ismerteti, hogyan lehet virtuális kapcsolót létrehozni egy Azure Stack peremhálózati eszközön a PowerShell használatával.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 04/06/2021
ms.author: alkohli
ms.openlocfilehash: 1ad86695510a8fe93bbeeab27db53f5afbef92fd
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106556178"
---
# <a name="create-a-new-virtual-switch-in-azure-stack-edge-pro-gpu-via-powershell"></a>Új virtuális kapcsoló létrehozása Azure Stack Edge Pro GPU-n keresztül a PowerShell-lel

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Ez a cikk azt ismerteti, hogyan hozhat létre új virtuális kapcsolót az Azure Stack Edge Pro GPU-eszközön. Hozzon létre például egy új virtuális kapcsolót, ha azt szeretné, hogy a virtuális gépek egy másik fizikai hálózati porton keresztül csatlakozzanak.

## <a name="vm-deployment-workflow"></a>Virtuális gép üzembe helyezésének munkafolyamata

1. Kapcsolódjon a PowerShell felületéhez az eszközön.
2. A rendelkezésre álló fizikai hálózati adapterek lekérdezése.
3. Hozzon létre egy virtuális kapcsolót.
4. Ellenőrizze az automatikusan létrehozott virtuális hálózatot és alhálózatot.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg az alábbiakról:

- Olyan ügyfélszámítógéphez férhet hozzá, amely hozzáfér az eszköz PowerShell-felületéhez. Lásd: [Kapcsolódás a PowerShell felületéhez](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface). 

    Az ügyfélszámítógépnek [támogatott operációs](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)rendszert kell futtatnia.

- A helyi felhasználói felületen engedélyezheti a számítást az eszköz egyik fizikai hálózati adapterén, a [számítási hálózat engedélyezése](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network) az eszközön című részben leírtak szerint. 


## <a name="connect-to-the-powershell-interface"></a>Csatlakozás a PowerShell-felülethez

[Kapcsolódjon az eszköz PowerShell-felületéhez](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).

## <a name="query-available-network-interfaces"></a>Elérhető hálózati adapterek lekérdezése

1. A következő parancs használatával jelenítheti meg azon fizikai hálózati adapterek listáját, amelyeken létrehozhat egy új virtuális kapcsolót. Ezen hálózati adapterek egyikét kell választania.

    ```powershell
    Get-NetAdapter -Physical
    ```
    Íme egy példa a kimenetre:
    
    ```powershell
        [10.100.10.10]: PS>Get-NetAdapter -Physical
        
        Name                      InterfaceDescription                    ifIndex Status       MacAddress       LinkSpeed
        ----                      --------------------                    ------- ------       ----------        -----
        Port2                     QLogic 2x1GE+2x25GE QL41234HMCU NIC ...      12 Up           34-80-0D-05-26-EA ...ps
        Ethernet                  Remote NDIS Compatible Device                11 Up           F4-02-70-CD-41-39 ...ps
        Port1                     QLogic 2x1GE+2x25GE QL41234HMCU NI...#3       9 Up           34-80-0D-05-26-EB ...ps
        Port5                     Mellanox ConnectX-4 Lx Ethernet Ad...#2       8 Up           0C-42-A1-C0-E3-99 ...ps
        Port3                     QLogic 2x1GE+2x25GE QL41234HMCU NI...#4       7 Up           34-80-0D-05-26-E9 ...ps
        Port6                     Mellanox ConnectX-4 Lx Ethernet Adapter       6 Up           0C-42-A1-C0-E3-98 ...ps
        Port4                     QLogic 2x1GE+2x25GE QL41234HMCU NI...#2       4 Up           34-80-0D-05-26-E8 ...ps
        
        [10.100.10.10]: PS>
    ```
2. Válasszon egy hálózati adaptert:

    - A **fel** állapotában. 
    - Egyetlen meglévő virtuális kapcsoló sem használja. Jelenleg csak egy vswitch konfigurálható hálózati adapterek esetében. 
    
    A meglévő virtuális kapcsoló és hálózati adapter társításának vizsgálatához futtassa a `Get-HcsExternalVirtualSwitch` parancsot.
 
    Íme egy példa a kimenetre.

    ```powershell
    [10.100.10.10]: PS>Get-HcsExternalVirtualSwitch

    Name                          : vSwitch1
    InterfaceAlias                : {Port2}
    EnableIov                     : True
    MacAddressPools               :
    IPAddressPools                : {}
    ConfigurationSource           : Dsc
    EnabledForCompute             : True
    SupportsAcceleratedNetworking : False
    DbeDhcpHostVnicName           : f4a92de8-26ed-4597-a141-cb233c2ba0aa
    Type                          : External
    
    [10.100.10.10]: PS>
    ```
    Ebben az esetben a 2-es port egy meglévő virtuális kapcsolóhoz van társítva, ezért nem használható.

## <a name="create-a-virtual-switch"></a>Virtuális kapcsoló létrehozása

A következő parancsmag használatával hozzon létre egy új virtuális kapcsolót a megadott hálózati adapteren. A művelet befejezése után a számítási példányok használhatják az új virtuális hálózatot.

```powershell
Add-HcsExternalVirtualSwitch -InterfaceAlias <Network interface name> -WaitForSwitchCreation $true
```

A `Get-HcsExternalVirtualSwitch` parancs használatával azonosíthatja az újonnan létrehozott kapcsolót. A létrehozott új kapcsoló neve a (z `vswitch-<InterfaceAlias>` ). 

Íme egy példa a kimenetre:

```powershell
[10.100.10.10]: P> Add-HcsExternalVirtualSwitch -InterfaceAlias Port5 -WaitForSwitchCreation $true
[10.100.10.10]: PS>Get-HcsExternalVirtualSwitch

Name                          : vSwitch1
InterfaceAlias                : {Port2}
EnableIov                     : True
MacAddressPools               :
IPAddressPools                : {}
ConfigurationSource           : Dsc
EnabledForCompute             : True
SupportsAcceleratedNetworking : False
DbeDhcpHostVnicName           : f4a92de8-26ed-4597-a141-cb233c2ba0aa
Type                          : External

Name                          : vswitch-Port5
InterfaceAlias                : {Port5}
EnableIov                     : True
MacAddressPools               :
IPAddressPools                :
ConfigurationSource           : Dsc
EnabledForCompute             : False
SupportsAcceleratedNetworking : False
DbeDhcpHostVnicName           : 9b301c40-3daa-49bf-a20b-9f7889820129
Type                          : External

[10.100.10.10]: PS>
```

## <a name="verify-network-subnet"></a>Hálózat, alhálózat ellenőrzése 

Miután létrehozta az új virtuális kapcsolót, Azure Stack Edge Pro GPU automatikusan létrehoz egy virtuális hálózatot és alhálózatot, amely megfelel annak. Virtuális gépek létrehozásakor ezt a virtuális hálózatot használhatja.

<!--To identify the virtual network and subnet associated with the new switch that you created, use the `Get-HcsVirtualNetwork` command. This cmdlet will be released in April some time. -->

## <a name="next-steps"></a>Következő lépések

- [Virtuális gépek üzembe helyezése az Azure Stack Edge Pro GPU-eszközön Azure PowerShell használatával](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)

- [Virtuális gépek üzembe helyezése az Azure Stack Edge Pro GPU-eszközön a Azure Portal használatával](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)
