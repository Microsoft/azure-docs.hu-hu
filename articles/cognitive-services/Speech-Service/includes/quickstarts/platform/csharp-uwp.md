---
title: 'Gyors útmutató: a C# Univerzális Windows-platform Speech SDK (UWP) platform beállítása – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ezzel az útmutatóval beállíthatja a C# platformját a Univerzális Windows-platform (UWP) alatt a Speech Service SDK-val.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.openlocfilehash: a54a09bd1fb6393098064906fc9c746d3e00b0a2
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551472"
---
Ez az útmutató bemutatja, hogyan telepítheti a C# Univerzális Windows-platform (UWP) [SPEECH SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) -t. Ha csak azt szeretné, hogy a csomag neve megkezdhető legyen a saját számára, futtassa `Install-Package Microsoft.CognitiveServices.Speech` a parancsot a NuGet-konzolon.

> [!NOTE]
> A Universal Windows Platformon olyan alkalmazásokat fejleszthet, amelyek a Windows 10-et támogató minden eszközön futtathatók: PC-n, Xboxon, Surface Hubon stb.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

* Windows rendszeren a platformhoz a [Microsoft Visual C++ terjeszthető változata szükséges a Visual Studio 2019](https://support.microsoft.com/en-us/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0) -hez. Ha először telepíti az elsőt, újraindításra lehet szükség.
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) (bármely kiadás)

## <a name="create-a-visual-studio-project-and-install-the-speech-sdk"></a>Visual Studio-projekt létrehozása és a Speech SDK telepítése

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

Ezután az alábbi [lépésekkel](#next-steps) léphet tovább.

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [windows](../quickstart-list.md)]