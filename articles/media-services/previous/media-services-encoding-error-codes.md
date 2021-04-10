---
title: Azure Media Services kódolási hibakódok | Microsoft Docs
description: Ez a témakör azokat a hibakódokat sorolja fel, amelyek akkor adhatók vissza, ha hiba történt a kódolási feladat végrehajtása során.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ce4e939f-5aee-41f9-859d-e4429815e9f2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 7e6848fb49dd63fa67a639d09754a28dd5953a32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103013482"
---
# <a name="encoding-error-codes"></a>Kódolási hibakódok

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

A következő táblázat felsorolja azokat a hibakódokat, amelyeket a rendszer a kódolási feladat végrehajtása során felmerülő hiba esetén visszaadhat.  Ha a .NET-kódban hibát szeretne kapni, használja a [ErrorDetails](/previous-versions/azure/jj126075(v=azure.100)) osztályt. Ha részletes adatokat szeretne kapni a REST-kódban, használja a [ErrorDetail](/rest/api/media/operations/errordetail) REST API.

| ErrorDetail. code | Hiba lehetséges okai |
| --- | --- |
| Ismeretlen |Ismeretlen hiba történt a feladat végrehajtása közben |
| ErrorDownloadingInputAssetMalformedContent |Olyan hibák kategóriája, amelyek a bemeneti eszköz (például hibás fájlnevek, nulla hosszúságú fájlok, helytelen formátumok stb.) letöltésével kapcsolatos hibákat fedik le. |
| ErrorDownloadingInputAssetServiceFailure |A szolgáltatással kapcsolatos problémákat fedező hibák kategóriája – például hálózati vagy tárolási hibák a letöltés során. |
| ErrorParsingConfiguration |A hibák kategóriája \<see cref="MediaTask.PrivateData"/> , ahol a feladat (konfiguráció) érvénytelen, például a konfiguráció nem érvényes rendszerkészlet, vagy érvénytelen XML-t tartalmaz. |
| ErrorExecutingTaskMalformedContent |A hibák kategóriája annak a feladatnak a végrehajtása során, hogy a bemeneti médiafájlokon belüli problémák hibát okoznak. |
| ErrorExecutingTaskUnsupportedFormat |Azon hibák kategóriája, amelyekben a média processzora nem tudja feldolgozni a megadott fájlokat – a média formátuma nem támogatott, vagy nem felel meg a konfigurációnak. Például egy csak hang kimenetet szeretne létrehozni egy olyan eszközről, amely csak videóval rendelkezik |
| ErrorProcessingTask |Az adathordozó-feldolgozó által a tartalomhoz nem kapcsolódó feladatok feldolgozásakor észlelt egyéb hibák kategóriája. |
| ErrorUploadingOutputAsset |A kimeneti eszköz feltöltésekor előforduló hibák kategóriája |
| ErrorCancelingTask |A feladat megszakítását megkísérlő hibák kategóriája |
| TransientError |Az átmeneti problémákra vonatkozó hibák kategóriája (például átmeneti Hálózatkezelési problémák az Azure Storage-ban |

Ha segítséget szeretne kérni a **Media Services** csapattól, nyisson meg egy [támogatási jegyet](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Kapcsolódó cikkek
* [Speciális kódolási feladatok végrehajtása a Media Encoder Standard-készletek testreszabásával](media-services-custom-mes-presets-with-dotnet.md)
* [Kvóták és korlátozások](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
