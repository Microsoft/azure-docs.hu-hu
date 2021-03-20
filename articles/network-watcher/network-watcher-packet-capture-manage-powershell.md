---
title: A csomagok rögzítésének kezelése – Azure PowerShell
titleSuffix: Azure Network Watcher
description: Ez az oldal azt ismerteti, hogyan kezelhető a Network Watcher csomag-rögzítési funkciója a PowerShell használatával
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 8779381425d4180cc7320f251de9c2cfdd1f0510
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99223423"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-powershell"></a>A csomagok rögzítésének kezelése az Azure Network Watcher a PowerShell használatával

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [Azure CLI](network-watcher-packet-capture-manage-cli.md)
> - [Azure REST API](network-watcher-packet-capture-manage-rest.md)

Network Watcher a csomagok rögzítése lehetővé teszi, hogy rögzítési munkameneteket hozzon létre a virtuális gépekre irányuló és onnan érkező forgalom nyomon követéséhez. A rögzítési munkamenethez szűrőket biztosít, hogy csak a kívánt forgalmat rögzítsen. A csomagok rögzítése segít a hálózati rendellenességek reaktív és proaktív módon történő diagnosztizálásában. A többi felhasználás magában foglalja a hálózati statisztikák összegyűjtését, a hálózati behatolásokkal kapcsolatos információk megszerzését, az ügyfél és a kiszolgáló közötti kommunikáció hibakeresését. A csomagok rögzítésének távoli kiváltásához ez a funkció megkönnyíti a csomagok rögzítésének a manuális és a kívánt gépen való futtatásának terhét, ami értékes időt takarít meg.

Ez a cikk végigvezeti a csomagok rögzítéséhez jelenleg elérhető különböző felügyeleti feladatokon.

- [**Csomag rögzítésének elindítása**](#start-a-packet-capture)
- [**Csomagok rögzítésének leállítása**](#stop-a-packet-capture)
- [**Csomagok rögzítésének törlése**](#delete-a-packet-capture)
- [**Csomagok rögzítésének letöltése**](#download-a-packet-capture)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik a következő erőforrásokkal:

* Az Network Watcher egy példánya abban a régióban, amelyben csomagot kíván létrehozni

* Egy virtuális gép, amelyen engedélyezve van a Packet Capture bővítmény.

> [!IMPORTANT]
> A csomagok rögzítése a virtuálisgép-bővítményt igényli `AzureNetworkWatcherExtension` . A bővítmény Windows rendszerű virtuális gépen való telepítéséhez látogasson el az [azure Network Watcher Agent virtuálisgép-bővítmény a Windows](../virtual-machines/extensions/network-watcher-windows.md) rendszerhez és a Linux rendszerű virtuális gépekhez látogasson el az [Azure Network Watcher Agent virtuálisgép-bővítménye Linuxra](../virtual-machines/extensions/network-watcher-linux.md).

## <a name="install-vm-extension"></a>Virtuálisgép-bővítmény telepítése

### <a name="step-1"></a>1. lépés

```powershell
$VM = Get-AzVM -ResourceGroupName testrg -Name VM1
```

### <a name="step-2"></a>2. lépés

A következő példa lekéri a parancsmag futtatásához szükséges kiterjesztési adatokat `Set-AzVMExtension` . Ez a parancsmag telepíti a csomag-rögzítési ügynököt a vendég virtuális gépen.

> [!NOTE]
> A `Set-AzVMExtension` parancsmag végrehajtása több percet is igénybe vehet.

Windows rendszerű virtuális gépek esetén:

```powershell
$AzureNetworkWatcherExtension = Get-AzVMExtensionImage -Location WestCentralUS -PublisherName Microsoft.Azure.NetworkWatcher -Type NetworkWatcherAgentWindows -Version 1.4.585.2
$ExtensionName = "AzureNetworkWatcherExtension"
Set-AzVMExtension -ResourceGroupName $VM.ResourceGroupName  -Location $VM.Location -VMName $VM.Name -Name $ExtensionName -Publisher $AzureNetworkWatcherExtension.PublisherName -ExtensionType $AzureNetworkWatcherExtension.Type -TypeHandlerVersion $AzureNetworkWatcherExtension.Version.Substring(0,3)
```

Linux rendszerű virtuális gépek esetén:

```powershell
$AzureNetworkWatcherExtension = Get-AzVMExtensionImage -Location WestCentralUS -PublisherName Microsoft.Azure.NetworkWatcher -Type NetworkWatcherAgentLinux -Version 1.4.13.0
$ExtensionName = "AzureNetworkWatcherExtension"
Set-AzVMExtension -ResourceGroupName $VM.ResourceGroupName  -Location $VM.Location -VMName $VM.Name -Name $ExtensionName -Publisher $AzureNetworkWatcherExtension.PublisherName -ExtensionType $AzureNetworkWatcherExtension.Type -TypeHandlerVersion $AzureNetworkWatcherExtension.Version.Substring(0,3)
```

A következő példa sikeres válasz a parancsmag futtatása után `Set-AzVMExtension` .

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   
```

### <a name="step-3"></a>3. lépés

Az ügynök telepítésének biztosításához futtassa a `Get-AzVMExtension` parancsmagot, és adja át a virtuális gép nevét és a bővítmény nevét.

```powershell
Get-AzVMExtension -ResourceGroupName $VM.ResourceGroupName  -VMName $VM.Name -Name $ExtensionName
```

Az alábbi példa a futtatási választ szemlélteti `Get-AzVMExtension`

```
ResourceGroupName       : testrg
VMName                  : testvm1
Name                    : AzureNetworkWatcherExtension
Location                : westcentralus
Etag                    : null
Publisher               : Microsoft.Azure.NetworkWatcher
ExtensionType           : NetworkWatcherAgentWindows
TypeHandlerVersion      : 1.4
Id                      : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testvm1/
                          extensions/AzureNetworkWatcherExtension
PublicSettings          : 
ProtectedSettings       : 
ProvisioningState       : Succeeded
Statuses                : 
SubStatuses             : 
AutoUpgradeMinorVersion : True
ForceUpdateTag          : 
```

## <a name="start-a-packet-capture"></a>Csomag rögzítésének elindítása

Miután az előző lépések befejeződik, a rendszer telepíti a csomag-rögzítési ügynököt a virtuális gépre.

### <a name="step-1"></a>1. lépés

A következő lépés az Network Watcher példány beolvasása. Ezt a változót a `New-AzNetworkWatcherPacketCapture` 4. lépésben megadott parancsmagnak adja át a rendszer.

```powershell
$networkWatcher = Get-AzNetworkWatcher  | Where {$_.Location -eq "westcentralus" }
```

### <a name="step-2"></a>2. lépés

Egy Storage-fiók beolvasása. Ez a Storage-fiók tárolja a csomag rögzítésére szolgáló fájlt.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName testrg -Name testrgsa123
```

### <a name="step-3"></a>3. lépés

A szűrők használatával korlátozható a csomagok rögzítése által tárolt adatmennyiség. Az alábbi példa két szűrőt állít be.  Egy szűrő csak a helyi IP-10.0.0.3 származó kimenő TCP-forgalmat gyűjti a 20, 80 és 443 közötti portokra.  A második szűrő csak az UDP-forgalmat gyűjti.

```powershell
$filter1 = New-AzPacketCaptureFilterConfig -Protocol TCP -RemoteIPAddress "1.1.1.1-255.255.255.255" -LocalIPAddress "10.0.0.3" -LocalPort "1-65535" -RemotePort "20;80;443"
$filter2 = New-AzPacketCaptureFilterConfig -Protocol UDP
```

> [!NOTE]
> A csomagok rögzítéséhez több szűrő is definiálható.

### <a name="step-4"></a>4. lépés

Futtassa a `New-AzNetworkWatcherPacketCapture` parancsmagot a csomagok rögzítési folyamatának elindításához, és adja meg az előző lépésekben lekért értékeket.
```powershell

New-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -TargetVirtualMachineId $vm.Id -PacketCaptureName "PacketCaptureTest" -StorageAccountId $storageAccount.id -TimeLimitInSeconds 60 -Filter $filter1, $filter2
```

A következő példa a parancsmag futtatásának várt kimenete `New-AzNetworkWatcherPacketCapture` .

```
Name                    : PacketCaptureTest
Id                      : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatcher
                          s/NetworkWatcher_westcentralus/packetCaptures/PacketCaptureTest
Etag                    : W/"3bf27278-8251-4651-9546-c7f369855e4e"
ProvisioningState       : Succeeded
Target                  : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testvm1
BytesToCapturePerPacket : 0
TotalBytesPerSession    : 1073741824
TimeLimitInSeconds      : 60
StorageLocation         : {
                            "StorageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Storage/storageA
                          ccounts/examplestorage",
                            "StoragePath": "https://examplestorage.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-00000
                          0000000/resourcegroups/testrg/providers/microsoft.compute/virtualmachines/testvm1/2017/02/01/packetcapture_22_42_48_238.cap"
                          }
Filters                 : [
                            {
                              "Protocol": "TCP",
                              "RemoteIPAddress": "1.1.1.1-255.255.255",
                              "LocalIPAddress": "10.0.0.3",
                              "LocalPort": "1-65535",
                              "RemotePort": "20;80;443"
                            },
                            {
                              "Protocol": "UDP",
                              "RemoteIPAddress": "",
                              "LocalIPAddress": "",
                              "LocalPort": "",
                              "RemotePort": ""
                            }
                          ]


```

## <a name="get-a-packet-capture"></a>Csomag rögzítésének beolvasása

A parancsmag futtatásakor a `Get-AzNetworkWatcherPacketCapture` rendszer lekéri egy aktuálisan futó, vagy befejezett csomag rögzítési állapotát.

```powershell
Get-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

A következő példa a parancsmag kimenete `Get-AzNetworkWatcherPacketCapture` . A következő példa a rögzítés befejeződése után következik be. A PacketCaptureStatus értéke leállt, és a TimeExceeded StopReason. Ez az érték azt mutatja, hogy a csomag rögzítése sikeres volt, és futott az ideje.
```
Name                    : PacketCaptureTest
Id                      : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatcher
                          s/NetworkWatcher_westcentralus/packetCaptures/PacketCaptureTest
Etag                    : W/"4b9a81ed-dc63-472e-869e-96d7166ccb9b"
ProvisioningState       : Succeeded
Target                  : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testvm1
BytesToCapturePerPacket : 0
TotalBytesPerSession    : 1073741824
TimeLimitInSeconds      : 60
StorageLocation         : {
                            "StorageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Storage/storageA
                          ccounts/examplestorage",
                            "StoragePath": "https://examplestorage.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-00000
                          0000000/resourcegroups/testrg/providers/microsoft.compute/virtualmachines/testvm1/2017/02/01/packetcapture_22_42_48_238.cap"
                          }
Filters                 : [
                            {
                              "Protocol": "TCP",
                              "RemoteIPAddress": "1.1.1.1-255.255.255",
                              "LocalIPAddress": "10.0.0.3",
                              "LocalPort": "1-65535",
                              "RemotePort": "20;80;443"
                            },
                            {
                              "Protocol": "UDP",
                              "RemoteIPAddress": "",
                              "LocalIPAddress": "",
                              "LocalPort": "",
                              "RemotePort": ""
                            }
                          ]
CaptureStartTime        : 2/1/2017 10:43:01 PM
PacketCaptureStatus     : Stopped
StopReason              : TimeExceeded
PacketCaptureError      : []
```

## <a name="stop-a-packet-capture"></a>Csomagok rögzítésének leállítása

Ha a parancsmag futtatásával `Stop-AzNetworkWatcherPacketCapture` folyamatban van egy rögzítési munkamenet, leáll.

```powershell
Stop-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

> [!NOTE]
> A parancsmag a jelenleg futó rögzítési munkamenetben vagy már leállított meglévő munkamenetben nem ad vissza választ.

## <a name="delete-a-packet-capture"></a>Csomagok rögzítésének törlése

```powershell
Remove-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

> [!NOTE]
> A csomagok rögzítésének törlése nem törli a fájlt a Storage-fiókban.

## <a name="download-a-packet-capture"></a>Csomagok rögzítésének letöltése

A csomag-rögzítési munkamenet befejezése után a rögzítési fájl feltölthető a blob Storage-ba vagy egy helyi fájlba a virtuális gépen. A csomag rögzítésének tárolási helye a munkamenet létrehozásakor van meghatározva. A Storage-fiókba mentett rögzítési fájlok elérésének kényelmes eszköze Microsoft Azure Storage Explorer, amely letölthető innen:  https://storageexplorer.com/

Ha meg van adva egy Storage-fiók, a rendszer a csomag rögzítési fájljait a következő helyen menti a Storage-fiókba:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan automatizálható a csomagok rögzítése a virtuálisgép-riasztásokkal a [riasztások által aktivált csomagok létrehozása](network-watcher-alert-triggered-packet-capture.md) funkció megtekintésével

Annak megállapítása, hogy az [IP-forgalom ellenőrzésének](diagnose-vm-network-traffic-filtering-problem.md) meglátogatása esetén engedélyezett-e a virtuális gép bejövő vagy kimenő forgalma

<!-- Image references -->
