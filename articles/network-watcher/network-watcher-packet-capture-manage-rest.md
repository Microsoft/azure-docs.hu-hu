---
title: A csomagok rögzítésének kezelése az Azure Network Watcher-REST APIsal | Microsoft Docs
description: Ez az oldal azt ismerteti, hogyan kezelhető a Network Watcher Packet Capture funkciójának használata az Azure-ban REST API
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
ms.openlocfilehash: bcdb91d7516acb8f987a3f302c04fbd2d8594fc7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019771"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-azure-rest-api"></a>A csomagok rögzítésének kezelése az Azure Network Watcher Az Azure-ban REST API

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [Azure CLI](network-watcher-packet-capture-manage-cli.md)
> - [Azure REST API](network-watcher-packet-capture-manage-rest.md)

Network Watcher a csomagok rögzítése lehetővé teszi, hogy rögzítési munkameneteket hozzon létre a virtuális gépekre irányuló és onnan érkező forgalom nyomon követéséhez. A rögzítési munkamenethez szűrőket biztosít, hogy csak a kívánt forgalmat rögzítsen. A csomagok rögzítése segít a hálózati rendellenességek reaktív és proaktív módon történő diagnosztizálásában. A többi felhasználás magában foglalja a hálózati statisztikák összegyűjtését, a hálózati behatolásokkal kapcsolatos információk megszerzését, az ügyfél és a kiszolgáló közötti kommunikáció hibakeresését. A csomagok rögzítésének távoli kiváltásához ez a funkció megkönnyíti a csomagok rögzítésének a manuális és a kívánt gépen való futtatásának terhét, ami értékes időt takarít meg.

Ez a cikk végigvezeti a csomagok rögzítéséhez jelenleg elérhető különböző felügyeleti feladatokon.

- [**Csomag rögzítésének beolvasása**](#get-a-packet-capture)
- [**Az összes csomag rögzítésének listázása**](#list-all-packet-captures)
- [**A csomagok rögzítési állapotának lekérdezése**](#query-packet-capture-status)
- [**Csomag rögzítésének elindítása**](#start-packet-capture)
- [**Csomagok rögzítésének leállítása**](#stop-packet-capture)
- [**Csomagok rögzítésének törlése**](#delete-packet-capture)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Előkészületek

Ebben az esetben a Network Watcher REST API-t hívja meg az IP flow ellenőrzéséhez. A ARMclient a REST API a PowerShell használatával történő meghívására szolgál. A ARMClient a chocolatey címen található a [ARMClient-on](https://chocolatey.org/packages/ARMClient)

Ez a forgatókönyv feltételezi, hogy már követte a [Network Watcher létrehozása](network-watcher-create.md) című témakör lépéseit Network Watcher létrehozásához.

> A csomagok rögzítése a virtuálisgép-bővítményt igényli `AzureNetworkWatcherExtension` . A bővítmény Windows rendszerű virtuális gépen való telepítéséhez látogasson el az [azure Network Watcher Agent virtuálisgép-bővítmény a Windows](../virtual-machines/extensions/network-watcher-windows.md) rendszerhez és a Linux rendszerű virtuális gépekhez látogasson el az [Azure Network Watcher Agent virtuálisgép-bővítménye Linuxra](../virtual-machines/extensions/network-watcher-linux.md).

## <a name="log-in-with-armclient"></a>Bejelentkezés a ARMClient

```powershell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>Virtuális gép beolvasása

Futtassa a következő szkriptet egy virtuális gép visszaküldéséhez. Ezek az információk a csomagok rögzítésének indításához szükségesek.

A következő kód változókat igényel:

- **subscriptionId** – az előfizetés-azonosító a **Get-AzSubscription** parancsmaggal is lekérhető.
- **resourceGroupName** – a virtuális gépeket tartalmazó erőforráscsoport neve.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

A következő példában a virtuális gép azonosítóját az alábbi kimenetben kell használni.

```json
...
,
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute
/virtualMachines/ContosoVM",
      "name": "ContosoVM"
    }
  ]
}
```


## <a name="get-a-packet-capture"></a>Csomag rögzítésének beolvasása

Az alábbi példa egyetlen csomag rögzítési állapotát olvassa be

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures/${packetCaptureName}/querystatus?api-version=2016-12-01"
```

A következő válaszok példákat mutatnak a csomagok rögzítési állapotának lekérdezése során kapott jellemző válaszra.

```json
{
  "name": "TestPacketCapture5",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/packetCaptures/TestPacketCapture6",
  "captureStartTime": "2016-12-06T17:20:01.5671279Z",
  "packetCaptureStatus": "Running",
  "packetCaptureError": []
}
```

```json
{
  "name": "TestPacketCapture5",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/packetCaptures/TestPacketCapture6",
  "captureStartTime": "2016-12-06T17:20:01.5671279Z",
  "packetCaptureStatus": "Stopped",
  "stopReason": "TimeExceeded",
  "packetCaptureError": []
}
```

## <a name="list-all-packet-captures"></a>Az összes csomag rögzítésének listázása

A következő példa egy adott régióban lévő összes csomag-rögzítési munkamenetet lekéri.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
armclient get "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures?api-version=2016-12-01"
```

A következő válasz egy példa arra, hogy az összes csomag rögzítésének beolvasása során melyik jellemző válasz lett visszaadva

```json
{
  "value": [
    {
      "name": "TestPacketCapture6",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/packetCaptures/TestPacketCapture6",
      "etag": "W/\"091762e1-c23f-448b-89d5-37cf56e4c045\"",
      "properties": {
        "provisioningState": "Succeeded",
        "target": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute/virtualMachines/ContosoVM",
        "bytesToCapturePerPacket": 0,
        "totalBytesPerSession": 1073741824,
        "timeLimitInSeconds": 60,
        "storageLocation": {
          "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Storage/storageAccounts/contosoexamplergdiag374",
          "storagePath": "https://contosoexamplergdiag374.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/contosoexamplerg/providers/microsoft.compute/virtualmachines/contosovm/2016/12/06/packetcap
ture_17_19_53_056.cap",
          "filePath": "c:\\temp\\packetcapture.cap"
        },
        "filters": [
          {
            "protocol": "Any",
            "localIPAddress": "",
            "localPort": "",
            "remoteIPAddress": "",
            "remotePort": ""
          }
        ]
      }
    },
    {
      "name": "TestPacketCapture7",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/packetCaptures/TestPacketCapture7",
      "etag": "W/\"091762e1-c23f-448b-89d5-37cf56e4c045\"",
      "properties": {
        "provisioningState": "Failed",
        "target": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute/virtualMachines/ContosoVM",
        "bytesToCapturePerPacket": 0,
        "totalBytesPerSession": 1073741824,
        "timeLimitInSeconds": 60,
        "storageLocation": {
          "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Storage/storageAccounts/contosoexamplergdiag374",
          "storagePath": "https://contosoexamplergdiag374.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/contosoexamplerg/providers/microsoft.compute/virtualmachines/contosovm/2016/12/06/packetcap
ture_17_23_15_364.cap",
          "filePath": "c:\\temp\\packetcapture.cap"
        },
        "filters": [
          {
            "protocol": "Any",
            "localIPAddress": "",
            "localPort": "",
            "remoteIPAddress": "",
            "remotePort": ""
          }
        ]
      }
    }
  ]
}
```

## <a name="query-packet-capture-status"></a>Lekérdezési csomag rögzítési állapota

A következő példa egy adott régióban lévő összes csomag-rögzítési munkamenetet lekéri.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$packetCaptureName = "TestPacketCapture5"
armclient get "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures/${packetCaptureName}/querystatus?api-version=2016-12-01"
```

A következő válasz egy példa egy tipikus válaszra, amelyet a rendszer a csomagok rögzítési állapotának lekérdezésekor adott vissza.

```json
{
    "name": "vm1PacketCapture",
    "id": "/subscriptions/{guid}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkWatchers/{networkWatcherName}/packetCaptures/{packetCaptureName}",
    "captureStartTime" : "9/7/2016 12:35:24PM",
    "packetCaptureStatus" : "Stopped",
    "stopReason" : "TimeExceeded",
    "packetCaptureError" : [ ]
}
```

## <a name="start-packet-capture"></a>Csomagok rögzítésének indítása

A következő példa egy csomag rögzítését hozza létre egy virtuális gépen.  A példában a paramétert úgy kell megadni, hogy rugalmasan lehessen létrehozni egy példát.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$packetCaptureName = "TestPacketCapture5"
$storageaccountname = "contosoexamplergdiag374"
$vmName = "ContosoVM"
$bytestoCaptureperPacket = "0"
$bytesPerSession = "1073741824"
$captureTimeinSeconds = "60"
$localIP = ""
$localPort = "" # Examples are: 80, or 80-120
$remoteIP = ""
$remotePort = "" # Examples are: 80, or 80-120
$protocol = "" # Valid values are TCP, UDP and Any.
$targetUri = "" # Example: /subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.compute/virtualMachine/$vmName
$storageId = "" #Example "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Storage/storageAccounts/contosoexamplergdiag374"
$storagePath = "" # Example: "https://mytestaccountname.blob.core.windows.net/capture/vm1Capture.cap"
$localFilePath = "c:\\temp\\packetcapture.cap" # Example: "d:\capture\vm1Capture.cap"

$requestBody = @"
{
    'properties':  {
                       'target':  '/${targetUri}',
                       'bytesToCapturePerPacket':  '${bytestoCaptureperPacket}',
                       'totalBytesPerSession':  '${bytesPerSession}',
                       'timeLimitinSeconds':  '${captureTimeinSeconds}',
                       'storageLocation':  {
                                               'storageId':  '${storageId}',
                                               'storagePath':  '${storagePath}',
                                               'filePath':  '${localFilePath}'
                                           },
                       'filters':  [
                                       {
                                           'protocol':  '${protocol}',
                                           'localIPAddress':  '${localIP}',
                                           'localPort':  '${localPort}',
                                           'remoteIPAddress':  '${remoteIP}',
                                           'remotePort':  '${remotePort}'
                                       }
                                   ]
                   }
}
"@

armclient PUT "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures/${packetCaptureName}?api-version=2016-07-01" $requestbody
```

## <a name="stop-packet-capture"></a>Csomagok rögzítésének leállítása

A következő példa leállítja a csomagok rögzítését egy virtuális gépen.  A példában a paramétert úgy kell megadni, hogy rugalmasan lehessen létrehozni egy példát.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$packetCaptureName = "TestPacketCapture5"
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures/${packetCaptureName}/stop?api-version=2016-12-01"
```

## <a name="delete-packet-capture"></a>Csomag rögzítésének törlése

A következő példa törli a csomagok rögzítését egy virtuális gépen.  A példában a paramétert úgy kell megadni, hogy rugalmasan lehessen létrehozni egy példát.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$packetCaptureName = "TestPacketCapture5"

armclient delete "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures/${packetCaptureName}?api-version=2016-12-01"
```

> [!NOTE]
> A csomagok rögzítésének törlése nem törli a fájlt a Storage-fiókban.

## <a name="next-steps"></a>Következő lépések

A fájlok Azure Storage-fiókokból való letöltésével kapcsolatos utasításokért tekintse meg az [Azure Blob Storage használatának első lépései a .NET használatával](../storage/blobs/storage-quickstart-blobs-dotnet.md)című témakört. Egy másik eszköz is használható Storage Explorer. Storage Explorer további információ a következő hivatkozáson található: [Storage Explorer](https://storageexplorer.com/)

Ismerje meg, hogyan automatizálható a csomagok rögzítése a virtuálisgép-riasztásokkal a [riasztások által aktivált csomagok létrehozása](network-watcher-alert-triggered-packet-capture.md) funkció megtekintésével