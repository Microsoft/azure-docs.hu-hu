---
title: Kódminták – Content Moderator, .NET
description: Használja a Content Moderator a .NET-alkalmazásait az SDK-n keresztül.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: sample
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: f2b5fb5c44cbb2e0d766c49ed6b715484c22f62e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60608797"
---
# <a name="content-moderator-net-sdk-samples"></a>Content Moderator .NET SDK-minták

A következő felsorolás a .NET-hez készült Azure Content Moderator SDK használatával létrehozott kódmintákra mutató hivatkozásokat tartalmaz.

- **Segédkódtárba helyezni**: [Hozzon létre egy Content Moderator ügyfél más minták](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ModeratorHelper/Clients.cs). Lásd a [rövid útmutatót](content-moderator-helper-quickstart-dotnet.md).

## <a name="moderation"></a>Moderálás

- **Kép moderálás**: [A felnőtt és szexuális tartalom, szöveg és arcok kép kiértékelése](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageModeration/Program.cs). Lásd a [rövid útmutatót](image-moderation-quickstart-dotnet.md).
- **Egyéni rendszerképek**: [Egyéni rendszerkép listákkal mérsékelt](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageListManagement/Program.cs). Lásd a [rövid útmutatót](image-lists-quickstart-dotnet.md).

> [!NOTE]
> A maximális korlát **5 képlista**, amelyek egyenként **nem haladhatják meg a 10 000 képet**.
>

- **Szövegmoderálás**: [Szöveg cenzúrázása és a személyes adatok képernyőn](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TextModeration/Program.cs). Lásd a [rövid útmutatót](text-moderation-quickstart-dotnet.md).
- **Egyéni kifejezések**: [Az egyéni kifejezéslisták mérsékelt](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TermListManagement/Program.cs). Lásd a [rövid útmutatót](term-lists-quickstart-dotnet.md).

> [!NOTE]
> A maximális korlát **5 kifejezéslista**, amelyek egyenként **nem haladhatják meg a 10 000 kifejezést**.
>

- **Videomoderálás**: [Egy videót a felnőtt és szexuális tartalom vizsgálata, valamint az eredmények eléréséhez](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoModeration/Program.cs). Lásd a [rövid útmutatót](video-moderation-api.md).

## <a name="review"></a>Áttekintés

- **Kép feladatok**: [Vizsgálatok és ez hozza létre értékelések moderálás feladat indítása](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs). Lásd a [rövid útmutatót](moderation-jobs-quickstart-dotnet.md).
- **Kép felülvizsgálatok**: [Emberi hurok felülvizsgálatának létrehozása](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs). Lásd a [rövid útmutatót](moderation-reviews-quickstart-dotnet.md).
- **Videót felülvizsgálatok**: [Hozzon létre az emberi hurok videót felülvizsgálatok](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs). Lásd a [rövid útmutatót](video-reviews-quickstart-dotnet.md).
- **Videóátiratot felülvizsgálatok**: [Emberi hurok videóátiratot felülvizsgálatának létrehozása](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs) lásd [a rövid útmutató](video-reviews-quickstart-dotnet.md)

Az összes .NET-minta megtekintése: [Content Moderator .NET-példák a GitHubon](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator).
