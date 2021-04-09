---
title: BLOB-Azure Storage titkosítási állapotának keresése
description: Ismerje meg, hogyan használható a Azure Portal, a PowerShell vagy az Azure CLI annak a vizsgálatához, hogy egy adott blob titkosítva van-e. Ha egy blob nincs titkosítva, Ismerje meg, hogy miként kényszerítheti a titkosítást a AzCopy használatával a blob letöltésével és újbóli feltöltésével.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: ee54357250e3f31ef9db633d933d897fff362f48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98878561"
---
# <a name="check-the-encryption-status-of-a-blob"></a>BLOB titkosítási állapotának keresése

Az Azure Storage-ba a 2017. október 20. után írt összes blokk blob, hozzáfűzési blob vagy Page blob az Azure Storage encryption használatával van titkosítva. Az ezen dátum előtt létrehozott blobokat a háttérben futó folyamat továbbra is titkosítja.

Ez a cikk bemutatja, hogyan lehet megállapítani, hogy egy adott blob titkosítva lett-e.

## <a name="check-a-blobs-encryption-status"></a>BLOB titkosítási állapotának keresése

A Azure Portal, a PowerShell vagy az Azure CLI használatával állapítsa meg, hogy a blob kódolás nélkül titkosítva van-e.

### <a name="azure-portal"></a>[Azure Portal](#tab/portal)

A Azure Portal használatával ellenőrizheti, hogy a blob titkosított-e, kövesse az alábbi lépéseket:

1. Az Azure Portalon lépjen a tárfiókra.
1. Válassza a **tárolók** lehetőséget, hogy a fiókban lévő tárolók listájára navigáljon.
1. Keresse meg a blobot, és jelenítse meg az **Áttekintés** lapot.
1. A **kiszolgáló titkosított** tulajdonságának megtekintése. Ha az **értéke TRUE (igaz**), ahogy az az alábbi ábrán is látható, a blob titkosítva lesz. Figyelje meg, hogy a blob tulajdonságai a blob létrehozásának dátumát és időpontját is tartalmazzák.

    ![A kiszolgáló titkosított tulajdonságának a Azure Portalban való ellenőrzését bemutató képernyőkép](media/storage-blob-encryption-status/blob-encryption-property-portal.png)

### <a name="powershell"></a>[PowerShell](#tab/powershell)

Ha a PowerShell segítségével szeretné megnézni, hogy a blob titkosított-e, keresse meg a blob **IsServerEncrypted** tulajdonságát. Ne felejtse el lecserélni a helyőrző értékeket a saját értékeire a szögletes zárójelekben:

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$blob = Get-AzStorageBlob -Context $account.Context `
    -Container <container> `
    -Blob <blob>
$blob.ICloudBlob.Properties.IsServerEncrypted
```

A blob létrehozási időpontjának megállapításához ellenőrizze a **létrehozott** tulajdonság értékét:

```powershell
$blob.ICloudBlob.Properties.IsServerEncrypted
```

### <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Ha az Azure CLI-vel szeretné megnézni, hogy a blob titkosított-e, keresse meg a blob **IsServerEncrypted** tulajdonságát. Ne felejtse el lecserélni a helyőrző értékeket a saját értékeire a szögletes zárójelekben:

```azurecli-interactive
az storage blob show \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --query "properties.serverEncrypted"
```

A blob létrehozási időpontjának megállapításához ellenőrizze a **létrehozott** tulajdonság értékét.

---

## <a name="force-encryption-of-a-blob"></a>BLOB titkosításának kényszerítése

Ha egy, a 2017. október 20. előtt létrehozott blobot még nem titkosított a háttérben futó folyamat, a titkosítást azonnal megteheti, ha letölti és újra feltölti a blobot. Ez egy egyszerű módja a AzCopy.

A következő szintaxissal töltheti le a blobokat a helyi fájlrendszerbe a AzCopy használatával:

```
azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'

Example:
azcopy copy 'https://storagesamples.blob.core.windows.net/sample-container/blob1.txt' 'C:\temp\blob1.txt'
```

Ha újra fel szeretné tölteni a blobot az Azure Storage-ba a AzCopy-mel, használja a következő szintaxist:

```
azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'

Example:
azcopy copy 'C:\temp\blob1.txt' 'https://storagesamples.blob.core.windows.net/sample-container/blob1.txt'
```

A AzCopy a blob-adatok másolásával kapcsolatos további információkért lásd: [adatok átvitele a AzCopy és a blob Storage](../common/storage-use-azcopy-v10.md#transfer-data)szolgáltatással.

## <a name="next-steps"></a>Következő lépések

[Inaktív adatok Azure Storage-titkosítása](../common/storage-service-encryption.md)
