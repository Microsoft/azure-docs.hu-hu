---
title: Készlet létrehozása a lemeztitkosítás engedélyezése mellett
description: Megtudhatja, hogyan titkosíthatja a csomópontokat a lemeztitkosítási konfigurációval egy platform által felügyelt kulccsal.
author: pkshultz
ms.topic: how-to
ms.date: 04/16/2021
ms.author: peshultz
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 40281211e5eb70089f4168dcb02720c912120a35
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576108"
---
# <a name="create-a-pool-with-disk-encryption-enabled"></a>Készlet létrehozása a lemeztitkosítás engedélyezése mellett

Amikor a virtuálisgép-konfigurációval [](nodes-and-pools.md#virtual-machine-configuration)hoz létre Azure Batch készletet, a készletben lévő számítási csomópontokat platform által felügyelt kulccsal titkosíthatja a lemeztitkosítási konfiguráció megadásával.

Ez a cikk bemutatja, hogyan hozhat létre engedélyezett lemeztitkosítással rendelkező Batch-készletet.

## <a name="why-use-a-pool-with-disk-encryption-configuration"></a>Miért érdemes lemeztitkosítási konfigurációval készletet használni?

A Batch-készlettel elérheti és tárolhatja az adatokat a számítási csomópont operációs rendszerén és ideiglenes lemezén. A kiszolgálóoldali lemez platform által felügyelt kulccsal való titkosítása alacsony többletterheléssel és kényelmes védelemmel védi az adatokat.

A Batch ezen lemeztitkosítási technológiák valamelyikét alkalmazza a számítási csomópontokon a készletkonfiguráció és a regionális támogatási képesség alapján.

- [Felügyelt lemeztitkosítás a platform által felügyelt kulcsokkal](../virtual-machines/disk-encryption.md#platform-managed-keys)
- [Titkosítás a gazdagépen platform által felügyelt kulccsal](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)
- [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md)

Nem fogja tudni megadni, hogy melyik titkosítási módszert alkalmazza a rendszer a készletben lévő csomópontokra. Ehelyett ön adja meg a csomópontjaikon a titkosítani kívánt céllemezeket, és a Batch kiválaszthatja a megfelelő titkosítási módszert, biztosítva, hogy a megadott lemezek titkosítva vannak a számítási csomóponton.

> [!IMPORTANT]
> Ha egyéni rendszerképből [](batch-sig-images.md)hoz létre készletet, csak Windows rendszerű virtuális gépek használata esetén engedélyezheti a lemeztitkosítást.

## <a name="azure-portal"></a>Azure Portal

Amikor Batch-készletet hoz létre a Azure Portal a Disk Encryption Configuration (Lemeztitkosítási konfiguráció) alatt válassza a **TemporaryDisk** vagy az **OsAndTemporaryDisk** **lehetőséget.**

:::image type="content" source="media/disk-encryption/portal-view.png" alt-text="Képernyőkép a Lemeztitkosítás konfigurálása lehetőségről a Azure Portal.":::

A készlet létrehozása után a lemeztitkosítási konfigurációs célokat a készlet Tulajdonságok szakaszában **láthatja.**

:::image type="content" source="media/disk-encryption/configuration-target.png" alt-text="Képernyőkép a lemez titkosítási konfigurációs céljairól a Azure Portal.":::

## <a name="examples"></a>Példák

Az alábbi példák bemutatják, hogyan titkosíthatja az operációs rendszert és az ideiglenes lemezeket egy Batch-készleten a Batch .NET SDK, a Batch REST API és az Azure CLI használatával.

### <a name="batch-net-sdk"></a>Batch .NET SDK

```csharp
pool.VirtualMachineConfiguration.DiskEncryptionConfiguration = new DiskEncryptionConfiguration(
    targets: new List<DiskEncryptionTarget> { DiskEncryptionTarget.OsDisk, DiskEncryptionTarget.TemporaryDisk }
    );
```

### <a name="batch-rest-api"></a>Batch REST API

REST API URL-cím:

```
POST {batchURL}/pools?api-version=2020-03-01.11.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

Kérés törzse:

```
"pool": {
    "id": "pool2",
    "vmSize": "standard_a1",
    "virtualMachineConfiguration": {
        "imageReference": {
            "publisher": "Canonical",
            "offer": "UbuntuServer",
            "sku": "18.04-LTS"
        },
        "diskEncryptionConfiguration": {
            "targets": [
                "OsDisk",
                "TemporaryDisk"
            ]
        }
        "nodeAgentSKUId": "batch.node.ubuntu 18.04"
    },
    "resizeTimeout": "PT15M",
    "targetDedicatedNodes": 5,
    "targetLowPriorityNodes": 0,
    "taskSlotsPerNode": 3,
    "enableAutoScale": false,
    "enableInterNodeCommunication": false
}
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az batch pool create \
    --id diskencryptionPool \
    --vm-size Standard_DS1_V2 \
    --target-dedicated-nodes 2 \
    --image canonical:ubuntuserver:18.04-LTS \
    --node-agent-sku-id "batch.node.ubuntu 18.04" \
    --disk-encryption-targets OsDisk TemporaryDisk
```

## <a name="next-steps"></a>Következő lépések

- További információ a [kiszolgálóoldali titkosításról a Azure Disk Storage.](../virtual-machines/disk-encryption.md)
- A Batch részletes áttekintését a Batch szolgáltatás munkafolyamatát [és erőforrásait ismertető témakörben olvashatja el.](batch-service-workflow-features.md)
