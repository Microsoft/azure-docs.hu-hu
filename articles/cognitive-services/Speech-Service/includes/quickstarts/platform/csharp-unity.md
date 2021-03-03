---
title: 'Rövid útmutató: a C# Unity platform telepítője Speech SDK'
titleSuffix: Azure Cognitive Services
description: Ezzel az útmutatóval beállíthatja a platformot C# egységre a Speech Service SDK használatával.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.custom: devx-track-csharp
ms.openlocfilehash: 895943b00ef2c2541b7de6cc81648877c9a73c0d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750292"
---
Ez az útmutató bemutatja, hogyan telepítheti az [Unity](https://unity3d.com/) [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) -t.

> [!NOTE]
> Az egységhez készült Speech SDK támogatja a Windows Desktopot (x86 és x64) vagy Univerzális Windows-platform (x86, x64, ARM/ARM64), Android (x86, ARM32/64) és iOS (x64 Simulator és ARM64)

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

- Windows rendszeren a platformhoz a [Microsoft Visual C++ terjeszthető változata szükséges a Visual Studio 2019](https://support.microsoft.com/en-us/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0) -hez. Ha először telepíti az elsőt, újraindításra lehet szükség.
- [Unity 2018,3 vagy újabb](https://store.unity.com/) , az [Unity 2019,1 támogatásával UWP ARM64](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal).
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). A Visual Studio 2017 15,9-es vagy újabb verziója is elfogadható.
- A Windows ARM64 támogatásához telepítse a [ARM64 választható Build-eszközeit, valamint a ARM64 készült Windows 10 SDK-](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/)t.

## <a name="install-the-speech-sdk"></a>A Speech SDK telepítése

Az egységhez készült Speech SDK telepítéséhez kövesse az alábbi lépéseket:

1. Töltse le és nyissa meg az Unity-hoz készült [SPEECH SDK](https://aka.ms/csspeech/unitypackage)-t, amely egységként (. unitypackage) van csomagolva, és már társítva kell lennie az egységhez. Amikor megnyílik az eszközoldali csomag, megjelenik az **egységességi csomag importálása** párbeszédpanel. Előfordulhat, hogy a lépés működéséhez létre kell hoznia egy üres projektet, és meg kell nyitnia azt.

   [![Unity-csomag importálása párbeszédpanel az Unity Editorban](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-unity-01-import.png)](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-unity-01-import.png#lightbox)

1. Győződjön meg arról, hogy az összes fájl ki van választva, majd válassza az **Importálás** lehetőséget. Néhány pillanat elteltével a rendszer importálja az Unity Asset csomagot a projektbe.

Az adategység-csomagok Unity-ba történő importálásával kapcsolatos további információkért tekintse meg az [Unity dokumentációját](https://docs.unity3d.com/Manual/AssetPackages.html).

Ezután az alábbi [lépésekkel](#next-steps) léphet tovább.

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [windows](../quickstart-list.md)]
