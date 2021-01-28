---
title: Feladathoz tartozó bevitel létrehozása helyi fájlból
description: Ez a cikk bemutatja, hogyan hozhat létre egy Azure Media Services-feladathoz tartozó bemenetet egy helyi fájlból.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 75cd5922804eb1724eaca0157f2c242a86406aab
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98951378"
---
# <a name="create-a-job-input-from-a-local-file"></a>Feladathoz tartozó bevitel létrehozása helyi fájlból

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

A Media Services v3-ban a videófeldolgozási feladatok indításakor meg kell adnia a Media Services számára a bemeneti videó helyét. A bemeneti videó adathordozó-szolgáltatási eszközként tárolható, ebben az esetben egy bemeneti eszközt hoz létre egy fájl alapján (helyileg vagy az Azure Blob Storage-ban tárolva). Ez a témakör azt mutatja be, hogyan lehet feladatot létrehozni egy helyi fájlból. Teljes példaként tekintse meg ezt a [GitHub-mintát](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs).

## <a name="prerequisites"></a>Előfeltételek 

* [Hozzon létre egy Media Services fiókot](./create-account-howto.md).

## <a name="net-sample"></a>.NET-minta

A következő kód bemutatja, hogyan hozhat létre bemeneti adategységet, és hogyan használhatja azt bemenetként a feladathoz. A CreateInputAsset függvény a következő műveleteket hajtja végre:

* Az eszköz létrehozása
* Lekér egy írható [SAS URL-címet](../../storage/common/storage-sas-overview.md) az objektum [tárolójába](../../storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-a-container).
* Feltölti a fájlt a tárolóba a SAS URL-cím használatával.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

A következő kódrészlet egy kimeneti eszközt hoz létre, ha még nem létezik:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

A következő kódrészlet elküld egy kódolási feladatot:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Feladathibakódok

Lásd: [hibakódok](/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Következő lépések

[Hozzon létre egy feladatot a HTTPS URL-címről](job-input-from-http-how-to.md).
