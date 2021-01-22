---
title: Media Services v2 – v3 áttelepítési minták összehasonlítása | Microsoft Docs
description: Olyan minták összessége, amelyek segítenek összehasonlítani a Azure Media Services v2 és v3 közötti kódok közötti különbségeket.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2021
ms.author: inhenkel
ms.openlocfilehash: 7705443bbe810866cd5b52946b310b5b79bfa072
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2021
ms.locfileid: "98690371"
---
# <a name="media-services-migration-code-sample-comparison"></a>Media Services áttelepítési kód minta-összehasonlítás

![Áttelepítési útmutató emblémája](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

A kódok némelyikével összehasonlíthatja, hogy az SDK-k hogyan használják a dolgokat.

## <a name="samples-for-comparison"></a>Összehasonlítási minták

A következő táblázat felsorolja a v2 és v3 közötti összehasonlítást a gyakori forgatókönyvek esetében.

|Eset|v2 API|V3 API|
|---|---|---|
|Eszköz létrehozása és fájl feltöltése |[v2 .NET-példa](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[v3 .NET – példa](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|Feladatok elküldése|[v2 .NET-példa](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[v3 .NET – példa](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>Bemutatja, hogyan hozhat létre először egy átalakítót, majd küldhet el egy feladatot.|
|Eszköz közzététele AES-titkosítással |1. létrehozás `ContentKeyAuthorizationPolicyOption`<br/>2. létrehozás `ContentKeyAuthorizationPolicy`<br/>3. létrehozás `AssetDeliveryPolicy`<br/>4. `Asset` tartalom létrehozása és feltöltése, illetve `Job` Küldés és használat `OutputAsset`<br/>5 `AssetDeliveryPolicy` . hozzárendelés `Asset`<br/>6. létrehozás `ContentKey`<br/>7. csatolás a következőhöz: `ContentKey``Asset`<br/>8. létrehozás `AccessPolicy`<br/>9. létrehozás `Locator`<br/><br/>[v2 .NET-példa](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. létrehozás `ContentKeyPolicy`<br/>2. létrehozás `Asset`<br/>3. tartalom feltöltése vagy használata `Asset` másként `JobOutput`<br/>4. létrehozás `StreamingLocator`<br/><br/>[v3 .NET – példa](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|
|Feladat részleteinek beolvasása és feladatok kezelése |[Feladatok kezelése a v2-sel](../previous/media-services-dotnet-manage-entities.md#get-a-job-reference) |[Feladatok kezelése a v3-vel](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L546)|

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
