---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: dotnet
ms.openlocfilehash: dcd2cda3bad2a13a83c5f3f6700e5a57471e2065
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88653882"
---
<!--Create a media services asset REST-->

A következő Azure .NET-parancs egy új Media Services eszközt hoz létre. Cserélje le az értékeket `subscriptionID` , a `resourceGroup` és `amsAccountName` a értékeket a jelenleg használatban lévő értékekre. Adja meg az eszköz nevét az itt beállítottak szerint `assetName` .

[!code-csharp[Main](../../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]
