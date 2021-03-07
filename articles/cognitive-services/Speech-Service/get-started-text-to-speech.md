---
title: Szöveg-beszéd rövid útmutató – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan alakíthat át szöveg-beszéd átalakítást a Speech SDK használatával. Ebben a rövid útmutatóban megismerheti az objektumok kialakításával és kialakításával, a támogatott hangkimeneti formátumokkal, a beszédfelismerési parancssori felülettel és az egyéni konfigurációs beállításokkal kapcsolatos beszédfelismerési lehetőségeket.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/01/2020
ms.author: trbye
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-set-twenty-four
keywords: szöveg – beszéd
ms.openlocfilehash: 7a41c4d9c1074b376da3de556caf63ced0bc84ec
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102428198"
---
# <a name="get-started-with-text-to-speech"></a>Ismerkedés a szövegfelolvasással

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-csharp.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [C++ Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-cpp.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-javascript.md)]
::: zone-end

::: zone pivot="programming-languages-objectivec-swift"
[!INCLUDE [Objective-C and Swift Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-objectivec-swift.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-python.md)]
::: zone-end

::: zone pivot="programming-language-curl"
[!INCLUDE [REST include](includes/how-to/text-to-speech-basics/text-to-speech-basics-curl.md)]
::: zone-end

::: zone pivot="programmer-tool-spx"
[!INCLUDE [CLI Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-cli.md)]
::: zone-end

## <a name="get-position-information"></a>Pozíció adatainak beolvasása

Előfordulhat, hogy a projektnek tudnia kell, hogy a beszéd és a szöveg hogyan beszél egy szót, hogy az adott időzítésen alapuló konkrét műveleteket hajtson végre. Ha például azt szeretné, hogy kiemelje a szavakat, akkor azt is tudnia kell, mi a kiemelése, mikor kell kiemelni, és hogy mennyi ideig kell kiemelni.

Ezt az alkalmazáson belül elérhető esemény használatával hajthatja végre `WordBoundary` `SpeechSynthesizer` . Ez az esemény minden új kimondott szó elején következik be, és a megjelenő adatfolyamban, valamint a bemeneti Rákérdezés szövegének eltolásakor időeltolódást biztosít.

* `AudioOffset` azt jelenti, hogy a kimeneti hang eltelt ideje a szintézis kezdete és a következő szó kezdete között van. Ez 100 ns egységben (HNS) mérve, 10 000 HNS egyenértéktel, 1 ezredmásodpercnél.
* `WordOffset` a bemeneti sztring (eredeti szöveg vagy [SSML](speech-synthesis-markup.md)) karakter pozícióját jelenti közvetlenül a megbeszélni kívánt szó előtt.

> [!NOTE]
> `WordBoundary` az események akkor jönnek létre, amikor a kimeneti hangadatok elérhetővé válnak, ami gyorsabb lesz, mint a lejátszás kimeneti eszközön. A hívónak meg kell valósítania a stream időzítésének megfelelő szinkronizálását a "valós idejű" értékre.

Példákat talál a `WordBoundary` [szöveg – beszéd mintákban](https://aka.ms/csspeech/samples) a githubon.

## <a name="next-steps"></a>Következő lépések

* [Bevezetés a Custom Voice szolgáltatásba](how-to-custom-voice.md)
* [Szintézis javítása az SSML-lel](speech-synthesis-markup.md)
* Ismerje meg, hogyan használhatja a hosszú hangalapú [API](long-audio-api.md) -t nagyméretű szöveges mintákhoz, mint például a könyvek és hírek
* Tekintse meg a gyors üzembe helyezési [mintákat](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart) a githubon
