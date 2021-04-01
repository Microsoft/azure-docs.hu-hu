---
title: Azure PowerShell parancsfájl-minta – a blob-tárolók teljes számlázási méretének kiszámítása | Microsoft Docs
description: Egy tároló teljes méretének kiszámítása az Azure Blob Storage-ban számlázási célokra.
services: storage
author: fhryo-msft
ms.service: storage
ms.subservice: blobs
ms.devlang: powershell
ms.topic: sample
ms.date: 12/29/2020
ms.author: fryu
ms.openlocfilehash: dfc338844e310102447e2498ee9cce8f28a79b9f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97809564"
---
# <a name="calculate-the-total-billing-size-of-a-blob-container"></a>BLOB-tároló teljes számlázási méretének kiszámítása

Ez a szkript kiszámítja egy tároló méretét az Azure Blob Storage-ban a számlázási költségek becslése céljából. A parancsfájl a tárolóban lévő Blobok méretét összesíti.

> [!IMPORTANT]
> Előfordulhat, hogy a cikkben megadott parancsfájl nem tudja pontosan kiszámítani a blob-Pillanatképek számlázási méretét.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> Ez a PowerShell-parancsfájl számlázási célokra kiszámítja a tároló méretét. Ha más célra szeretné kiszámítani a tároló méretét, tekintse meg [a blob Storage-tárolók teljes méretét](../scripts/storage-blobs-container-calculate-size-powershell.md) egy becslést biztosító egyszerűbb parancsfájlhoz.

## <a name="determine-the-size-of-the-blob-container"></a>A blob-tároló méretének meghatározása

A blob-tároló teljes mérete magában foglalja a tároló méretét, valamint a tárolóban lévő összes blob méretét.

A következő szakasz ismerteti, hogyan számítja ki a tárolási kapacitást a blob-tárolók és a Blobok számára. A következő szakaszban a len (X) a karakterláncban szereplő karakterek számát jelenti.

### <a name="blob-containers"></a>Blobtárolók

A következő számítás azt ismerteti, hogyan lehet megbecsülni a blob-tárolók által felhasznált tárterület méretét:

```
48 bytes + Len(ContainerName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
For-Each Signed Identifier[512 bytes]
```

A következő részletezés:

* 48 bájt az egyes tárolók esetében az utolsó módosítás időpontja, az engedélyek, a nyilvános beállítások és néhány rendszer-metaadat szerepel.

* A tároló nevét Unicode-ként tárolja a rendszer, ezért a karakterek számát és a szorzást két értékre kell helyezni.

* A blob-tárolók összes tárolt metaadata esetében a név (ASCII) hosszát, valamint a karakterlánc értékének hosszát tárolja.

* A 512 bájt/aláírt azonosító tartalmazza az aláírt azonosító nevét, a kezdési időt, a lejárati időt és az engedélyeket.

### <a name="blobs"></a>Blobok

A következő számítások azt mutatják be, hogyan lehet megbecsülni a Blobok által felhasznált tárterület mennyiségét.

* BLOB blokkolása (alap blob vagy pillanatkép):

   ```
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   8 bytes + number of committed and uncommitted blocks * Block ID Size in bytes +
   SizeInBytes(data in unique committed data blocks stored) +
   SizeInBytes(data in uncommitted data blocks)
   ```

* Oldal blobja (alap blob vagy pillanatkép):

   ```
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   number of nonconsecutive page ranges with data * 12 bytes +
   SizeInBytes(data in unique pages stored)
   ```

A következő részletezés:

* 124 bájt terhelés a blobhoz, amely a következőket tartalmazza:
    - Utolsó módosítás időpontja
    - Méret
    - Cache-Control
    - Content-Type
    - Tartalom nyelve
    - Content-Encoding
    - Content-MD5
    - Engedélyek
    - Pillanatkép-információk
    - Lízing
    - Egyes rendszermetaadatok

* A blob nevét Unicode-ként tárolja a rendszer, ezért a karakterek számát és a szorzást két értékkel kell elvégeznie.

* A tárolt metaadatok mindegyik blokkja esetében adja hozzá a név (ASCII-ként tárolt) hosszát, valamint a karakterlánc értékének hosszát.

* A blokk Blobok esetén:
  * 8 bájt a tiltási listához.
  * A blokk AZONOSÍTÓjának mérete bájtban megadva.
  * Az összes véglegesített és nem véglegesített blokkban lévő adatméret.

    >[!NOTE]
    >Pillanatképek használata esetén ez a méret csak az alap-vagy pillanatkép-blob egyedi adatait tartalmazza. Ha a nem véglegesített blokkokat egy hét után nem használják, a rendszer a szemetet gyűjti. Ezt követően nem számítanak bele a számlázás felé.

* Oldal Blobok esetén:
  * A nem egymást követő oldalak tartományának száma 12 bájtos adatidővel. Az **GetPageRanges** API meghívásakor megjelenő egyedi oldalak tartományának száma.

  * Az összes tárolt oldal bájtjainak mérete bájtban kifejezve.

    >[!NOTE]
    >A pillanatképek használata esetén ez a méret csak az alap blob egyedi lapjait, vagy a jelenleg megszámolt pillanatkép-blobot tartalmazza.

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size-ex.ps1 "Calculate container size")]

## <a name="next-steps"></a>Következő lépések

- Lásd: [a blob Storage-tárolók teljes méretének kiszámítása](../scripts/storage-blobs-container-calculate-size-powershell.md) egy egyszerű parancsfájlhoz, amely a tároló méretére vonatkozó becslést biztosít.

- Az Azure Storage számlázási szolgáltatásával kapcsolatos további információkért lásd: a [Windows Azure Storage szolgáltatás számlázásának ismertetése](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/).

- További információ a Azure PowerShell modullal kapcsolatban: [Azure PowerShell dokumentáció](/powershell/azure/).

- Az [Azure Storage](../blobs/storage-samples-blobs-powershell.md)-hoz készült PowerShell-minták további Storage PowerShell-parancsfájlokat is találhatnak.