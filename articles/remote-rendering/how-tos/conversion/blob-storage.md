---
title: Modellek átalakítása az Azure Blob Storage használatával
description: A blob Storage beállításához és használatához szükséges általános lépéseket ismerteti a modell átalakításához.
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: d8a6fd458cdcf79cdeb693b25acf72d4ec48def7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102507517"
---
# <a name="use-azure-blob-storage-for-model-conversion"></a>Modellek átalakítása az Azure Blob Storage használatával

A [modell-átalakítási](model-conversion.md) szolgáltatáshoz hozzáféréssel kell rendelkeznie az Azure Blob Storagehoz, hogy beolvassa a bemeneti adatokat, és tárolja a kimeneti adatokat. Ez a cikk a leggyakoribb lépéseket ismerteti.

## <a name="prepare-azure-storage-accounts"></a>Azure Storage-fiókok előkészítése

- Storage-fiók (StorageV2) létrehozása
- Hozzon létre egy bemeneti BLOB-tárolót a Storage-fiókban (például "arrinput")
- Hozzon létre egy kimeneti BLOB-tárolót a Storage-fiókban (például "arroutput")

> [!TIP]
> A Storage-fiók beállításának részletes ismertetését a következő témakörben tekintheti meg: gyors üzembe helyezési [modell átalakítása](../../quickstarts/convert-model.md)

A Storage-fiók és a blob-tárolók létrehozása a következő eszközök egyikével végezhető el:

- [Azure Portal](https://portal.azure.com)
- [az Command Line](/cli/azure/install-azure-cli)
- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)
- SDK-k (C#, Python...)

## <a name="ensure-azure-remote-rendering-can-access-your-storage-account"></a>Győződjön meg arról, hogy az Azure-alapú távoli renderelés hozzáférhet a Storage-fiókhoz

Az Azure távoli tépő le kell kérnie a modell adatait a Storage-fiókból, és vissza kell írnia az adatokhoz.

A következő két módon biztosíthatja az Azure távoli renderelési hozzáférését a Storage-fiókjához:

### <a name="connect-your-azure-storage-account-with-your-azure-remote-rendering-account"></a>Azure Storage-fiók összekötése az Azure távoli renderelési fiókjával

Kövesse a [fiók létrehozása](../create-an-account.md#link-storage-accounts) című szakaszban ismertetett lépéseket.

### <a name="retrieve-sas-for-the-storage-containers"></a>SAS beolvasása a Storage-tárolók számára

A tárolt hozzáférési aláírások (SAS) használatával olvasási hozzáférést biztosíthat a bemenethez, és írási hozzáférést biztosít a kimenethez. Javasoljuk, hogy minden egyes modell átalakításakor új URI-ket generáljon. Mivel az URI-k egy kis idő elteltével lejárnak, a hosszabb időtartamra kiterjedően továbbra is előfordulhat, hogy az alkalmazás váratlanul megszakad.

Az SAS-vel kapcsolatos részletek a [sas dokumentációjában](../../../storage/common/storage-sas-overview.md)találhatók.

SAS URI-t az alábbiak egyikével lehet létrehozni:

- az PowerShell-modul
  - lásd a [PowerShell-parancsfájlok példáit](../../samples/powershell-example-scripts.md)
- [az Command Line](/cli/azure/install-azure-cli)
- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)
  - kattintson a jobb gombbal a tároló "közös hozzáférési aláírás beolvasása" elemre (olvasás, hozzáférés listázása beviteli tárolóhoz, írási hozzáférés a kimeneti tárolóhoz)
- SDK-k (C#, Python...)

Példa a közös hozzáférési aláírások eszköz-átalakításban való használatára a [PowerShell-példa parancsfájljainak](../../samples/powershell-example-scripts.md#script-conversionps1)Conversion.ps1.

## <a name="upload-an-input-model"></a>Bemeneti modell feltöltése

A modellek átalakításának megkezdéséhez fel kell töltenie azt a következő lehetőségek egyikének használatával:

- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) – egy kényelmes felhasználói felület az Azure Blob Storage-ban található fájlok feltöltéséhez/letöltéséhez/kezeléséhez
- [Azure-parancssor](../../../storage/blobs/storage-quickstart-blobs-cli.md)
- [Azure PowerShell-modul](/powershell/azure/install-az-ps)
  - lásd a [PowerShell-parancsfájlok példáit](../../samples/powershell-example-scripts.md)
- [Storage SDK használata (Python, C#...)](../../../storage/index.yml)
- [Az Azure Storage REST API-k használata](/rest/api/storageservices/blob-service-rest-api)

Az adatok átalakítására való feltöltésre példaként tekintse meg a PowerShell- [példa parancsfájljainak](../../samples/powershell-example-scripts.md#script-conversionps1)Conversion.ps1.

## <a name="get-a-sas-uri-for-the-converted-model"></a>SAS URI beszerzése az átalakított modellhez

Ez a lépés hasonló a [tárolók biztonsági társításának beolvasásához](#retrieve-sas-for-the-storage-containers). Ezúttal azonban be kell szereznie egy SAS URI-t a modell fájljához, amely a kimeneti tárolóba lett írva.

Ha például SAS URI-t szeretne beolvasni a [Azure Storage Exploreron](https://azure.microsoft.com/features/storage-explorer/)keresztül, kattintson a jobb gombbal a modell fájlra, és válassza a "közös hozzáférési aláírás beolvasása" lehetőséget.

Ha nem csatlakoztatta a Storage-fiókot az Azure távoli megjelenítési fiókjához, a modell betöltéséhez közös hozzáférésű aláírás (SAS) szükséges. Megtudhatja, hogyan csatlakozhat a fiókhoz a fiók [létrehozása](../create-an-account.md#link-storage-accounts)című témakörben.

## <a name="next-steps"></a>Következő lépések

- [A modell átalakításának konfigurálása](configure-model-conversion.md)
- [A modell átalakítási REST API](conversion-rest-api.md)
