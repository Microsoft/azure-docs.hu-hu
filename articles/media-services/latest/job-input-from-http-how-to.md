---
title: Azure Media Services-feladathoz tartozó bemenet létrehozása HTTPS URL-címről
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
ms.openlocfilehash: b71262b57e9e07c58eb6b3f6e0506151063d77d5
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98895356"
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
