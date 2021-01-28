---
title: Feladathoz tartozó bemenet létrehozása HTTPS URL-címről
description: Ez a témakör bemutatja, hogyan hozhat létre egy Azure Media Services-feladatot egy HTTPS URL-címről.
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
ms.openlocfilehash: 1073a13f477894e1b35d732fd1cd6191747a62a2
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955987"
---
# <a name="create-a-job-input-from-an-https-url"></a>Feladathoz tartozó bemenet létrehozása HTTPS URL-címről

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

A Media Services v3-ban a videófeldolgozási feladatok indításakor meg kell adnia a Media Services számára a bemeneti videó helyét. Az egyik lehetőség, ha egy HTTPS URL-címet ad meg a feladathoz tartozó bemenetként (a példában látható módon). Vegye figyelembe, hogy az AMS v3 jelenleg nem támogatja a HTTPS URL-címekkel történő darabolásos átviteli kódolást. Teljes példaként tekintse meg ezt a [GitHub-mintát](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

> [!TIP]
> A fejlesztés megkezdése előtt tekintse át [az Media Services V3 API](media-services-apis-overview.md) -kkal való fejlesztést (az API-k elérésére vonatkozó információkat, elnevezési konvenciókat stb.).

## <a name="net-sample"></a>.NET-minta

A következő kód bemutatja, hogyan hozhat létre egy HTTPS URL-címmel rendelkező feladatot.

[!code-csharp[Main](../../../media-services-v3-dotnet-quickstarts/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Feladathibakódok

Lásd: [hibakódok](/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Következő lépések

[Hozzon létre egy feladatot egy helyi fájlból](job-input-from-local-file-how-to.md).
