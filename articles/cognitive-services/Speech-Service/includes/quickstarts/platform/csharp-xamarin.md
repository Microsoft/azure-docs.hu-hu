---
title: 'Gyors útmutató: a C#-hoz készült Speech SDK (Xamarin) platform beállítása – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ezzel az útmutatóval beállíthatja a platformot C# Xamarin a Speech Service SDK-val.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.custom: devx-track-csharp
ms.openlocfilehash: 8c941f0c96c127a46bb0b888d0500223ebcd9be2
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552751"
---
Ez az útmutató bemutatja, hogyan telepítheti a [Xamarin](/xamarin/get-started/what-is-xamarin)készült [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) -t, amely egy nyílt forráskódú platform, amely modern és nagy teljesítményű alkalmazásokat hoz létre iOS, Android és Windows rendszerre a .NET használatával. Ha csak azt szeretné, hogy a csomag neve megkezdhető legyen a saját számára, futtassa `Install-Package Microsoft.CognitiveServices.Speech` a parancsot a NuGet-konzolon.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

* Windows rendszeren a platformhoz a [Microsoft Visual C++ terjeszthető változata szükséges a Visual Studio 2019](https://support.microsoft.com/en-us/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0) -hez. Ha először telepíti az elsőt, újraindításra lehet szükség.
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

## <a name="create-a-visual-studio-project-and-install-the-speech-sdk"></a>Visual Studio-projekt létrehozása és a Speech SDK telepítése

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-xamarin-create-proj.md)]

A Speech SDK már telepítve van. Most már törölheti vagy újra használhatja az előző lépésekben létrehozott "HelloWorld" projektet.

Ezután az alábbi [lépésekkel](#next-steps) léphet tovább.

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [windows](../quickstart-list.md)]