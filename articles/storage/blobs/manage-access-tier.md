---
title: Blobok hozzáférési szintjeinek kezelése Azure Storage-fiókban
description: Megtudhatja, hogyan módosíthatja a Blobok szintjét egy GPv2 vagy Blob Storage fiókban
author: twooley
ms.author: twooley
ms.date: 01/11/2021
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.reviewer: klaasl
ms.openlocfilehash: cd46f5b5a8847150bd56c1daeaac470f9afd2d19
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278541"
---
# <a name="manage-the-access-tier-of-a-blob-in-an-azure-storage-account"></a>Blobok hozzáférési szintjeinek kezelése Azure Storage-fiókban

Az egyes Blobok alapértelmezett hozzáférési szinttel rendelkeznek, akár gyors, ritka elérésű vagy archív. A blob a létrehozás alatt álló Azure Storage-fiók alapértelmezett hozzáférési szintjére kerül. A Blob Storage és a GPv2 fiók szintjén elérhetők a **hozzáférési szint** attribútuma. Ez az attribútum határozza meg az alapértelmezett hozzáférési szintet minden olyan blob esetében, amely nem kifejezetten az objektum szintjén van beállítva. Az objektum szintjén beállított szinttel rendelkező objektumok esetén a fiók szintje nem lesz érvényes. Az archiválási szint csak az objektum szintjén alkalmazható. Az alábbi lépésekkel bármikor válthat a hozzáférési szintek között.

## <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>BLOB szintjeinek módosítása GPv2 vagy Blob Storage fiókban

A következő forgatókönyvek a Azure Portal vagy a PowerShellt használják:

# <a name="portal"></a>[Portál](#tab/portal)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A Azure Portal keresse meg és válassza ki az **összes erőforrás** elemet.

1. Válassza ki a tárfiókot.

1. Válassza ki a tárolót, majd válassza ki a blobot.

1. A **blob tulajdonságainál** válassza a lehetőség **módosítása** lehetőséget.

1. Válassza a **gyors**, **a lassú vagy az** **archív** hozzáférési szintet. Ha a blob jelenleg archiválás alatt áll, és egy online szintre szeretné kiszáradni, akkor a **standard** vagy a **magas** rehidratálás prioritást is kiválaszthatja.

1. Kattintson a **Save (Mentés** ) gombra a lap alján.

![BLOB-rétegek módosítása Azure Portal](media/storage-tiers/blob-access-tier.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A blob-rétegek módosításához a következő PowerShell-parancsfájl használható. A `$rgName` változót inicializálni kell az erőforráscsoport nevével. A `$accountName` változót inicializálni kell a Storage-fiók nevével. A `$containerName` változót a tároló nevével kell inicializálni. A `$blobName` változót inicializálni kell a blob nevével.

```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$containerName = ""
$blobName == ""

#Select the storage account and get the context
$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Select the blob from a container
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx

#Change the blob’s access tier to archive
$blob.ICloudBlob.SetStandardBlobTier("Archive")
```

---

## <a name="next-steps"></a>Következő lépések

- [Azure Storage-fiók alapértelmezett fiók-hozzáférési szintjeinek kezelése](../common/manage-account-default-access-tier.md)
- [Tudnivalók a blob-adatok archiválási szintről való kiszárításáról](storage-blob-rehydration.md)
- [A gyakori és ritka elérésű, valamint az archív tárolási szint díjszabásának régiók szerinti ellenőrzése Blob Storage- és GPv2-fiókok esetében](https://azure.microsoft.com/pricing/details/storage/)
