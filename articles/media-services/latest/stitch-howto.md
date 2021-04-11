---
title: Két vagy több videofájl összefűzése a .NET-tel | Microsoft Docs
description: Ez a cikk bemutatja, hogyan lehet összefűzni két vagy több videofájlokat.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/24/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 5eacc366961d3101a7eaf8877a34ef2d462ea76b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105111455"
---
# <a name="how-to-stitch-two-or-more-video-files-with-net"></a>Kettő vagy több videofájl összefűzése a .NET-tel

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="stitch-two-or-more-video-files"></a>Két vagy több videofájl összefűzése

Az alábbi példa azt szemlélteti, hogyan lehet előállítani egy készletet két vagy több videofájl összefűzésére. A leggyakoribb forgatókönyv az, amikor egy fejlécet vagy egy trailert szeretne hozzáadni a fő videóhoz.

> [!NOTE]
> A közösen szerkesztett videofájlokat meg kell osztania a tulajdonságokat (videó felbontása, Képkockasebesség, hangkövetési szám stb.). Ügyeljen arra, hogy ne keverje a különböző képarányú videókat vagy különböző számú hangsávot.

## <a name="prerequisites"></a>Előfeltételek

A [Media Services .net-minták](https://github.com/Azure-Samples/media-services-v3-dotnet/)klónozása vagy letöltése.  Az alább hivatkozott kód a [EncodingWithMESCustomStitchTwoAssets mappában](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/VideoEncoding/EncodingWithMESCustomStitchTwoAssets/Program.cs)található.

### <a name="net-code"></a>.NET-kód

[!code-csharp[Main](../../../media-services-v3-dotnet/VideoEncoding/EncodingWithMESCustomStitchTwoAssets/Program.cs)]
