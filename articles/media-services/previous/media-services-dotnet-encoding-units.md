---
title: Adathordozó-feldolgozás skálázása kódolási egységek hozzáadásával – Azure |  Microsoft Docs
description: Ez a cikk bemutatja, hogyan adhat hozzá kódolási egységeket Azure Media Services .NET-tel.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 33f7625a-966a-4f06-bc09-bccd6e2a42b5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.reviewer: milangada
ms.custom: devx-track-csharp
ms.openlocfilehash: 6feb28858271f11bef0933a352f86d7545ee5bd3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103014077"
---
# <a name="how-to-scale-encoding-with-net-sdk"></a>A kódolás méretezése a .NET SDK használatával

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [Portál](media-services-portal-scale-media-processing.md)
> * [.NET](media-services-dotnet-encoding-units.md)
> * [REST](/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/rnrneverdies/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>Áttekintés
> [!IMPORTANT]
> Tekintse át az [áttekintést](media-services-scale-media-processing-overview.md) , hogy további információkat kapjon a média-feldolgozás méretezéséről.
> 
> 

A fenntartott egység típusának és a kódoláshoz fenntartott egységek számának a .NET SDK használatával történő módosításához tegye a következőket:

```csharp
IEncodingReservedUnit encodingS1ReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
encodingS1ReservedUnit.ReservedUnitType = ReservedUnitType.Basic; // Corresponds to S1
encodingS1ReservedUnit.Update();
Console.WriteLine("Reserved Unit Type: {0}", encodingS1ReservedUnit.ReservedUnitType);

encodingS1ReservedUnit.CurrentReservedUnits = 2;
encodingS1ReservedUnit.Update();

Console.WriteLine("Number of reserved units: {0}", encodingS1ReservedUnit.CurrentReservedUnits);
```

## <a name="opening-a-support-ticket"></a>Támogatási jegy megnyitása

Alapértelmezés szerint minden Media Services fiók legfeljebb 10 S2 vagy S3 Media szolgáltatás számára fenntartott egységre (MRUs) vagy 25 S1 MRUs, valamint 5 igény szerinti folyamatos átvitelre fenntartott egységre méretezhető. A támogatási jegy megnyitásával magasabb korlátot kérhet.

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
