---
title: Videó kivágása a Media Services kódolásakor
description: Ez a témakör azt ismerteti, hogyan lehet videót alklipet használni Azure Media Services a .NET SDK-val való kódoláskor
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: how-to
ms.date: 06/09/2019
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: b70d08363db212e5039690ceaaef5dbb55bcd65f
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106107273"
---
# <a name="subclip-a-video-when-encoding-with-media-services---net"></a>Videó kivágása Media Services-.NET-kódolással

Ha [feladatokkal](/rest/api/media/jobs)kódolja a videót, levágja vagy alklipet készíthet. Ez a funkció a [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) -előállítók vagy a [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) -előállítók használatával létrehozott bármely [átalakítással](/rest/api/media/transforms) működik.

A következő C#-példa egy olyan feladatot hoz létre, amely egy adott eszközön található videót metszi el, mivel kódolási feladatot küld. 

## <a name="prerequisites"></a>Előfeltételek

A jelen témakörben ismertetett lépések végrehajtásához a következőket kell tennie:

- [Azure Media Services-fiók létrehozása](./account-create-how-to.md)
- Hozzon létre egy átalakítót és egy bemeneti és kimeneti eszközöket. A .NET-oktatóanyag használatával megtekintheti, hogyan hozhat létre átalakítót és bemeneti és kimeneti eszközöket a [feltöltési, a kódolási és a stream-videóban](stream-files-tutorial-with-api.md) .
- Tekintse át a [kódolási koncepcióval](encode-concept.md) foglalkozó témakört.

## <a name="example"></a>Példa

```csharp
/// <summary>
/// Submits a request to Media Services to apply the specified Transform to a given input video.
/// </summary>
/// <param name="client">The Media Services client.</param>
/// <param name="resourceGroupName">The name of the resource group within the Azure subscription.</param>
/// <param name="accountName"> The Media Services account name.</param>
/// <param name="transformName">The name of the transform.</param>
/// <param name="jobName">The (unique) name of the job.</param>
/// <param name="inputAssetName">The name of the input asset.</param>
/// <param name="outputAssetName">The (unique) name of the  output asset that will store the result of the encoding job. </param>
// <SubmitJob>
private static async Task<Job> JobWithBuiltInStandardEncoderWithSingleClipAsync(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string transformName,
    string jobName,
    string inputAssetName,
    string outputAssetName)
{
    var jobOutputs = new List<JobOutputAsset>
    {
        new JobOutputAsset(state: JobState.Queued, progress: 0, assetName: outputAssetName)
    };

    var clipStart = new AbsoluteClipTime()
    {
        Time = new TimeSpan(0, 0, 20)
    };

    var clipEnd = new AbsoluteClipTime()
    {
        Time = new TimeSpan(0, 0, 30)
    };

    var jobInput = new JobInputAsset(assetName: inputAssetName, start: clipStart, end: clipEnd);

    Job job = await client.Jobs.CreateAsync(
        resourceGroupName,
        accountName,
        transformName,
        jobName,
        new Job(input: jobInput, outputs: jobOutputs.ToArray(), name: jobName)
        {
            Description = $"A Job with transform {transformName} and single clip.",
            Priority = Priority.Normal,
        });

    return job;
}
```

## <a name="next-steps"></a>Következő lépések

[Kódolás egyéni átalakítással](encode-custom-presets-how-to.md) 
