---
title: Kodekkel tömörített hang streamelése a Speech SDK – Speech service használatával
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan streamelhet tömörített hangot a Speech Service-be a Speech SDK-val. Elérhető C++, C# és Java rendszerekhez Linux, Java androidos és Objective-C iOS rendszeren.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: amishu
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-set-twenty-two
ms.openlocfilehash: db902019b4fb1237c8403c719862d8fca4ba4f28
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772522"
---
# <a name="use-codec-compressed-audio-input-with-the-speech-sdk"></a>Kodekkel tömörített hangbemenet használata a Speech SDK-val

A Speech service SDK képes tömörített hangformátumok elfogadására. Leválasztja a hangot, mielőtt nyers PCM-ként továbbítja azt a Speech service-nek.

Platform | Nyelvek | Támogatott GStreamer-verzió
| :--- | ---: | :---:
Windows (az UWP kivételével)  | C++, C#, Java, Python | [1.18.3](https://gstreamer.freedesktop.org/data/pkg/windows/1.18.3/)
Linux  | C++, C#, Java, Python | [támogatott Linux-disztribúciók és célarchitektúrák](~/articles/cognitive-services/speech-service/speech-sdk.md)
Android  | Java | [1.18.3](https://gstreamer.freedesktop.org/data/pkg/android/1.18.3/)

## <a name="speech-sdk-version-required-for-compressed-audio-input"></a>A tömörített hangbemenethez szükséges Speech SDK-verzió
* A Speech SDK 1.10.0-s vagy újabb verziójára van szükség az RHEL 8 és a CentOS 8 esetében
* A Windowshoz a Speech SDK 1.11.0-s vagy újabb verziójára van szükség.
* A Speech SDK 1.16.0-s vagy újabb verziója a legújabb gstreamerhez Windows és Android rendszeren.

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

## <a name="gstreamer-required-to-handle-compressed-audio"></a>A GStreamer a tömörített hang kezeléséhez szükséges

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/python/prerequisites.md)]
::: zone-end

## <a name="example-code-using-codec-compressed-audio-input"></a>Kódkód például kodek tömörített hangbemenet használatával

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/examples.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/python/examples.md)]
::: zone-end

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [A beszédfelismerés elsajátítás](./get-started-speech-to-text.md)
