---
title: A csomagok rögzítésének kezelése az Azure Network Watcher használatával – Azure CLI | Microsoft Docs
description: Ez az oldal azt ismerteti, hogyan kezelhető a Network Watcher csomag-rögzítési funkciója az Azure CLI használatával
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: cb0c1d10-f7f2-4c34-b08c-f73452430be8
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: efb8fba655570a661b6b001eb93942475eb2170d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101698637"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-azure-cli"></a>A csomagok rögzítésének kezelése az Azure Network Watcher Az Azure CLI használatával

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [Azure CLI](network-watcher-packet-capture-manage-cli.md)
> - [Azure REST API](network-watcher-packet-capture-manage-rest.md)

Network Watcher a csomagok rögzítése lehetővé teszi, hogy rögzítési munkameneteket hozzon létre a virtuális gépekre irányuló és onnan érkező forgalom nyomon követéséhez. A rögzítési munkamenethez szűrőket biztosít, hogy csak a kívánt forgalmat rögzítsen. A csomagok rögzítése segít a hálózati rendellenességek reaktív és proaktív módon történő diagnosztizálásában. A többi felhasználás magában foglalja a hálózati statisztikák összegyűjtését, a hálózati behatolásokkal kapcsolatos információk megszerzését, az ügyfél és a kiszolgáló közötti kommunikáció hibakeresését. A csomagok rögzítésének távoli kiváltásához ez a funkció megkönnyíti a csomagok rögzítésének a manuális és a kívánt gépen való futtatásának terhét, ami értékes időt takarít meg.

A cikkben szereplő lépések végrehajtásához [telepítenie kell a Mac, Linux és Windows rendszerhez készült azure Command-Line felületet (Azure CLI)](/cli/azure/install-azure-cli).

Ez a cikk végigvezeti a csomagok rögzítéséhez jelenleg elérhető különböző felügyeleti feladatokon.

- [**Csomag rögzítésének elindítása**](#start-a-packet-capture)
- [**Csomagok rögzítésének leállítása**](#stop-a-packet-capture)
- [**Csomagok rögzítésének törlése**](#delete-a-packet-capture)
- [**Csomagok rögzítésének letöltése**](#download-a-packet-capture)

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik a következő erőforrásokkal:

- Az Network Watcher egy példánya abban a régióban, amelyben csomagot kíván létrehozni
- Egy virtuális gép, amelyen engedélyezve van a Packet Capture bővítmény.

> [!IMPORTANT]
> A csomagok rögzítése megköveteli, hogy az ügynök fusson a virtuális gépen. Az ügynök bővítményként van telepítve. A virtuálisgép-bővítményekre vonatkozó utasításokért látogasson el a [virtuális gépek bővítményeire és szolgáltatásaira](../virtual-machines/extensions/features-windows.md).

## <a name="install-vm-extension"></a>Virtuálisgép-bővítmény telepítése

### <a name="step-1"></a>1. lépés

Futtassa a `az vm extension set` parancsot a csomag rögzítési ügynökének telepítéséhez a vendég virtuális gépen.

Windows rendszerű virtuális gépek esetén:

```azurecli-interactive
az vm extension set --resource-group resourceGroupName --vm-name virtualMachineName --publisher Microsoft.Azure.NetworkWatcher --name NetworkWatcherAgentWindows --version 1.4
```

Linux rendszerű virtuális gépek esetén:

```azurecli-interactive
az vm extension set --resource-group resourceGroupName --vm-name virtualMachineName --publisher Microsoft.Azure.NetworkWatcher --name NetworkWatcherAgentLinux --version 1.4
```

### <a name="step-2"></a>2. lépés

Az ügynök telepítésének biztosításához futtassa a parancsot, `vm extension show` és adja át az erőforráscsoportot és a virtuális gép nevét. Ellenőrizze az eredményül kapott listát, és győződjön meg arról, hogy az ügynök telepítve van.

Windows rendszerű virtuális gépek esetén:

```azurecli-interactive
az vm extension show --resource-group resourceGroupName --vm-name virtualMachineName --name NetworkWatcherAgentWindows
```

Linux rendszerű virtuális gépek esetén:

```azurecli-interactive
az vm extension show --resource-group resourceGroupName --vm-name virtualMachineName --name AzureNetworkWatcherExtension
```

Az alábbi példa a futtatási választ szemlélteti `az vm extension show`

```json
{
  "autoUpgradeMinorVersion": true,
  "forceUpdateTag": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/extensions/NetworkWatcherAgentWindows",
  "instanceView": null,
  "location": "westcentralus",
  "name": "NetworkWatcherAgentWindows",
  "protectedSettings": null,
  "provisioningState": "Succeeded",
  "publisher": "Microsoft.Azure.NetworkWatcher",
  "resourceGroup": "{resourceGroupName}",
  "settings": null,
  "tags": null,
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "typeHandlerVersion": "1.4",
  "virtualMachineExtensionType": "NetworkWatcherAgentWindows"
}
```

## <a name="start-a-packet-capture"></a>Csomag rögzítésének elindítása

Miután az előző lépések befejeződik, a rendszer telepíti a csomag-rögzítési ügynököt a virtuális gépre.

### <a name="step-1"></a>1. lépés

Egy Storage-fiók beolvasása. Ez a Storage-fiók tárolja a csomag rögzítésére szolgáló fájlt.

```azurecli-interactive
az storage account list
```

### <a name="step-2"></a>2. lépés

Ezen a ponton készen áll a csomagok rögzítésére.  Először is vizsgáljuk meg a konfigurálni kívánt paramétereket. A szűrők egy ilyen paraméter, amely a csomagok rögzítése által tárolt adatmennyiség korlátozására használható. Az alábbi példa egy csomagot állít be több szűrővel.  Az első három szűrő csak a helyi IP-10.0.0.3 gyűjti a kimenő TCP-forgalmat a 20., 80-es és 443-es portra.  Az utolsó szűrő csak az UDP-forgalmat gyűjti.

```azurecli-interactive
az network watcher packet-capture create --resource-group {resourceGroupName} --vm {vmName} --name packetCaptureName --storage-account {storageAccountName} --filters "[{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"20\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"80\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"443\"},{\"protocol\":\"UDP\"}]"
```

A következő példa a parancs futtatásának várt kimenete `az network watcher packet-capture create` .

```json
{
  "bytesToCapturePerPacket": 0,
  "etag": "W/\"b8cf3528-2e14-45cb-a7f3-5712ffb687ac\"",
  "filters": [
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "20"
    },
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "80"
    },
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "443"
    },
    {
      "localIpAddress": "",
      "localPort": "",
      "protocol": "UDP",
      "remoteIpAddress": "",
      "remotePort": ""
    }
  ],
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/pa
cketCaptures/packetCaptureName",
  "name": "packetCaptureName",
  "provisioningState": "Succeeded",
  "resourceGroup": "NetworkWatcherRG",
  "storageLocation": {
    "filePath": null,
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/gwteststorage123abc",
    "storagePath": "https://gwteststorage123abc.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/{resourceGroupName}/p
roviders/microsoft.compute/virtualmachines/{vmName}/2017/05/25/packetcapture_16_22_34_630.cap"
  },
  "target": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}",
  "timeLimitInSeconds": 18000,
  "totalBytesPerSession": 1073741824
}
```

## <a name="get-a-packet-capture"></a>Csomag rögzítésének beolvasása

A parancs futtatásakor a rendszer `az network watcher packet-capture show-status` lekéri egy aktuálisan futó, vagy befejezett csomag rögzítési állapotát.

```azurecli-interactive
az network watcher packet-capture show-status --name packetCaptureName --location {networkWatcherLocation}
```

A következő példa a parancs kimenete `az network watcher packet-capture show-status` . A következő példa a rögzítés leállítását, a TimeExceeded StopReason.

```
{
  "additionalProperties": {
    "status": "Succeeded"
  },
  "captureStartTime": "2016-12-06T17:20:01.5671279Z",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/packetCaptures/packetCaptureName",
  "name": "packetCaptureName",
  "packetCaptureError": [],
  "packetCaptureStatus": "Stopped",
  "stopReason": "TimeExceeded"
}
```

## <a name="stop-a-packet-capture"></a>Csomagok rögzítésének leállítása

`az network watcher packet-capture stop`Ha a parancs futtatása folyamatban van, a rögzítési munkamenet leáll.

```azurecli-interactive
az network watcher packet-capture stop --name packetCaptureName --location westcentralus
```

> [!NOTE]
> A parancs nem ad vissza választ, ha egy jelenleg futó rögzítési munkamenetben vagy egy már leállított meglévő munkamenetben futott.

## <a name="delete-a-packet-capture"></a>Csomagok rögzítésének törlése

```azurecli-interactive
az network watcher packet-capture delete --name packetCaptureName --location westcentralus
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