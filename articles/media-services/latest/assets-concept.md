---
title: Objektumok
titleSuffix: Azure Media Services
description: Ismerje meg, hogy milyen eszközöket és hogyan használják a Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 27082499263af6eedd7d9d628b7b1746a9177672
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88606832"
---
# <a name="assets-in-azure-media-services-v3"></a>Eszközök a Azure Media Services v3-ban

A Azure Media Servicesban az [Asset](/rest/api/media/assets) adategység alapvető fogalom. Itt adhatja meg az adathordozót (például feltöltéssel vagy élő betöltéssel), kimeneti adathordozóval (a feladatok kimenetéről), és közzéteheti az adathordozót (adatfolyamként). 

Az eszköz az [Azure Storage-fiókban](storage-account-concept.md) található blob-tárolóra van leképezve, és az adategységben található fájlok a tárolóban blokk blobként tárolódnak. Az eszközök tartalmazzák az Azure Storage-ban tárolt digitális fájlokra vonatkozó információkat (beleértve a videó, a hang, a képek, a miniatűr gyűjtemények, a szöveges számok és a lezárt feliratok fájljait).

Media Services támogatja a blob-rétegeket, ha a fiók általános célú v2 (GPv2) tárolót használ. A GPv2 használatával fájlokat helyezhet át a ritka elérésű [vagy archív tárolóba](../../storage/blobs/storage-blob-storage-tiers.md). Az **archiválási** tárterület alkalmas a forrásfájlok archiválására, ha már nincs rá szükség (például a kódolásuk után).

Az **archív** tárolási szint csak olyan nagy méretű forrásfájlok esetében ajánlott, amelyek már kódoltak, és a kódolási feladatok kimenete kimeneti blob-tárolóba került. A kimeneti tárolóban lévő blobokat, amelyeket egy adategységhez szeretne rendelni, és a tartalom adatfolyamként való továbbítására vagy elemzésére használja, **gyakran vagy ritkán** **használt tárolási** rétegben kell lennie.

## <a name="naming"></a>Elnevezés 

### <a name="assets"></a>Objektumok

Az eszköz nevének egyedinek kell lennie. A (z) Media Services v3-erőforrások neve (például eszközök, feladatok, átalakítások) Azure Resource Manager elnevezési megkötések hatálya alá tartozik. További információ: [elnevezési konvenciók](media-services-apis-overview.md#naming-conventions).

### <a name="blobs"></a>Blobok

Az eszközön belüli fájlok/Blobok nevének a [blob neve](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) és az [NTFS-név követelményeit](/windows/win32/fileio/naming-a-file)is követnie kell. Ennek a követelménynek az oka, hogy a fájlok a blob Storage-ból egy helyi NTFS-lemezre másolhatók feldolgozásra.

## <a name="next-steps"></a>Következő lépések

[Media Services áttekintése](media-services-overview.md)

## <a name="see-also"></a>Lásd még

[Media Services v2 és v3 közötti különbségek](migrate-from-v2-to-v3.md)
