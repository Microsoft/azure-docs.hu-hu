---
title: Az Azure VNET-átjáró és-kapcsolatok hibáinak megoldása – Azure CLI
titleSuffix: Azure Network Watcher
description: Ez az oldal azt ismerteti, hogyan használható az Azure Network Watcher Az Azure CLI-vel való hibakereséséhez
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: d04ba6cc5f8527f8c05fbeb323c65230447e1e2e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019703"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-azure-cli"></a>Az Azure Network Watcher Azure CLI-vel Virtual Network átjáró és kapcsolatok hibáinak megoldása

> [!div class="op_single_selector"]
> - [Portál](diagnose-communication-problem-between-networks.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [Azure CLI](network-watcher-troubleshoot-manage-cli.md)
> - [REST API](network-watcher-troubleshoot-manage-rest.md)

A Network Watcher számos képességgel rendelkezik, amelyek az Azure hálózati erőforrásainak megismeréséhez kapcsolódnak. Ezen képességek egyike az erőforrás-hibaelhárítás. Az erőforrás-hibaelhárítás a portál, a PowerShell, a CLI vagy a REST API használatával hívható meg. A híváskor Network Watcher megvizsgálja egy Virtual Network átjáró vagy egy kapcsolat állapotát, és visszaadja az eredményeit.

A cikkben szereplő lépések végrehajtásához [telepítenie kell az Azure parancssori felületét Mac, Linux és Windows rendszerre (CLI)](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Előkészületek

Ez a forgatókönyv feltételezi, hogy már követte a [Network Watcher létrehozása](network-watcher-create.md) című témakör lépéseit Network Watcher létrehozásához.

A támogatott átjáró-típusok listáját a [támogatott átjárók típusainál](network-watcher-troubleshoot-overview.md#supported-gateway-types)találhatja meg.

## <a name="overview"></a>Áttekintés

Az erőforrás-hibaelhárítás lehetővé teszi a Virtual Network átjárókkal és kapcsolatokkal kapcsolatos hibák elhárítását. Ha az erőforrás-hibaelhárításra vonatkozó kérést végez, a rendszer lekérdezi és ellenőrzi a naplókat. Az ellenőrzés befejezésekor a rendszer az eredményeket adja vissza. Az erőforrás-hibaelhárítási kérelmek hosszú ideig futó kérelmek, amelyek több percet is igénybe vehetnek az eredmények visszaküldéséhez. A hibaelhárítási naplók a megadott Storage-fiók tárolójában tárolódnak.

## <a name="retrieve-a-virtual-network-gateway-connection"></a>Virtual Network átjáróval létesített kapcsolatok beolvasása

Ebben a példában az erőforrás-hibaelhárítást egy kapcsolatban futtatja a rendszer. Virtual Network átjárót is átadhat. A következő parancsmag az erőforráscsoport VPN-kapcsolatait sorolja fel.

```azurecli
az network vpn-connection list --resource-group resourceGroupName
```

Miután megkapta a kapcsolat nevét, futtathatja ezt a parancsot az erőforrás-azonosító lekéréséhez:

```azurecli
az network vpn-connection show --resource-group resourceGroupName --ids vpnConnectionIds
```

## <a name="create-a-storage-account"></a>Tárfiók létrehozása

Az erőforrás-hibaelhárítás az erőforrás állapotával kapcsolatos információkat ad vissza, és a naplókat is menti egy áttekinthető Storage-fiókba. Ebben a lépésben létrehozunk egy Storage-fiókot, ha meglévő Storage-fiók létezik, használhatja azt.

1. A tárfiók létrehozása

    ```azurecli
    az storage account create --name storageAccountName --location westcentralus --resource-group resourceGroupName --sku Standard_LRS
    ```

1. A Storage-fiók kulcsainak beolvasása

    ```azurecli
    az storage account keys list --resource-group resourcegroupName --account-name storageAccountName
    ```

1. A tároló létrehozása

    ```azurecli
    az storage container create --account-name storageAccountName --account-key {storageAccountKey} --name logs
    ```

## <a name="run-network-watcher-resource-troubleshooting"></a>Network Watcher erőforrás-hibaelhárítás futtatása

A parancsmaggal elháríthatja az erőforrásokat `az network watcher troubleshooting` . Átadjuk a parancsmagot, az erőforráscsoportot, a Network Watcher nevét, a kapcsolódás azonosítóját, a Storage-fiók azonosítóját, valamint a blob elérési útját, amely a hibakeresési eredményeket tárolja.

```azurecli
az network watcher troubleshooting start --resource-group resourceGroupName --resource resourceName --resource-type {vnetGateway/vpnConnection} --storage-account storageAccountName  --storage-path https://{storageAccountName}.blob.core.windows.net/{containerName}
```

A parancsmag futtatása után Network Watcher áttekinti az erőforrást az állapot ellenőrzéséhez. Visszaadja az eredményeket a rendszerhéjnak, és a megadott Storage-fiókban tárolja az eredmények naplóit.

## <a name="understanding-the-results"></a>Az eredmények megismerése

A művelet szövege általános útmutatást nyújt a probléma megoldásához. Ha a probléma megoldására is sor kerül, további útmutatást nyújt a hivatkozáshoz. Abban az esetben, ha nincs további útmutatás, a válasz egy támogatási eset megnyitására szolgáló URL-címet biztosít.  A válasz tulajdonságaival és a benne foglalt információkkal kapcsolatos további információkért tekintse meg [Network Watcher a hibakeresés áttekintése című](network-watcher-troubleshoot-overview.md) témakört.

A fájlok Azure Storage-fiókokból való letöltésével kapcsolatos utasításokért tekintse meg az [Azure Blob Storage használatának első lépései a .NET használatával](../storage/blobs/storage-quickstart-blobs-dotnet.md)című témakört. Egy másik eszköz is használható Storage Explorer. Storage Explorer további információ a következő hivatkozáson található: [Storage Explorer](https://storageexplorer.com/)

## <a name="next-steps"></a>Következő lépések

Ha a beállítások megváltoztak a VPN-kapcsolat leállításakor, tekintse meg a [hálózati biztonsági csoportok kezelése](../virtual-network/manage-network-security-group.md) a hálózati biztonsági csoport és az esetlegesen érintett biztonsági szabályok nyomon követéséhez című témakört.