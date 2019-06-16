---
title: Beszéd eszköz SDK-dokumentáció
titleSuffix: Azure Cognitive Services
description: Kibocsátási megjegyzések – mi változott a legfrissebb verziókban
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: wellsi
ms.openlocfilehash: 1c91cde45a6a420376af36f70487adf7fe0ee83a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66751807"
---
# <a name="release-notes-of-cognitive-services-speech-devices-sdk"></a>A Cognitive Services beszédfelismerő eszközök SDK kibocsátási megjegyzései
A következő szakaszok lista módosul a legfrissebb verziókban.

## <a name="speech-devices-sdk-151"></a>Beszéd eszközök SDK 1.5.1:

*   Például [beszélgetés Beszédátírási](conversation-transcription-service.md) a mintaalkalmazás.
*   Frissítve a [beszéd SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) összetevőt a verzió 1.5.1. További információkért lásd: a [kibocsátási megjegyzések](https://aka.ms/csspeech/whatsnew).

## <a name="cognitive-services-speech-devices-sdk-150-2019-may-release"></a>A cognitive Services beszédfelismerő eszközök SDK 1.5.0: A 2019-május kiadás

*   Beszéd eszközök SDK már általánosan elérhető és nem egy korlátozott előzetes verzió.
*   Frissítve a [beszéd SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) verzióra 1.5.0 összetevőt. További információkért lásd: a [kibocsátási megjegyzések](https://aka.ms/csspeech/whatsnew).
*   Új ébresztési word technológia számos lehetőséget kínál a jelentős minőségének javítása, a használhatatlanná tévő változások megtekintéséhez.
*   Új hang feldolgozási folyamat a jobb szélen mező felismerése.

**Használhatatlanná tévő változásai**

*   Az új ébresztési word technológia miatt minden ébresztési szavakat a továbbfejlesztett ébresztési word Portalon újra létre kell hozni. Teljesen eltávolítja az eszközről a régi kulcsszavak távolítsa el a régi alkalmazást.
    - ADB com.microsoft.coginitiveservices.speech.samples.sdsdkstarterapp eltávolítása

## <a name="cognitive-services-speech-devices-sdk-140-2019-apr-release"></a>Cognitive Services Speech Devices SDK 1.4.0: A 2019-április kiadás 

* Frissítve a [beszéd SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) verzióra 1.4.0-s összetevőt. További információkért lásd: a [kibocsátási megjegyzések](https://aka.ms/csspeech/whatsnew). 

## <a name="cognitive-services-speech-devices-sdk-131-2019-mar-release"></a>A cognitive Services beszédfelismerő eszközök SDK 1.3.1: A 2019-március kiadás 

* Frissítve a [beszéd SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) verzióra 1.3.1 összetevőt. További információkért lásd: a [kibocsátási megjegyzések](https://aka.ms/csspeech/whatsnew). 
*   Frissített ébresztési word kezelése, tekintse meg a használhatatlanná tévő változások.
*   Mintaalkalmazás hozzáadása nyelvválasztás beszédfelismerés és a fordítás.

**Használhatatlanná tévő változásai** 

*   [Hálózati ébresztési szó telepítése](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws) lett egyszerűsített, azt az alkalmazás részeként, és nem kell külön telepíteni az eszközön.
*   Az ébresztési word felismerés megváltozott, és két események támogatottak.
    - RecognizingKeyword, azt jelzi, a beszéd eredmény (nem ellenőrzött) kulcsszó szöveget tartalmaz.
    - RecognizedKeyword, azt jelzi, hogy az adott kulcsszó FELISMERVE befejeződött kulcsszó felismerése.


## <a name="cognitive-services-speech-devices-sdk-110-2018-nov-release"></a>Cognitive Services Speech Devices SDK 1.1.0: 2018-november kiadás 

* Frissítve a [beszéd SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) összetevőt a 1.1.0-s verzió. További információkért lásd: a [kibocsátási megjegyzések](https://aka.ms/csspeech/whatsnew). 
* A továbbfejlesztett hang feldolgozási algoritmus úgy lett továbbfejlesztve, távolságban mező beszédfelismerés pontosságát.
* Mintaalkalmazás támogatása a kínai speech recognition.

## <a name="cognitive-services-speech-devices-sdk-101-2018-oct-release"></a>Cognitive Services Speech Devices SDK 1.0.1: 2018-október kiadás 

* Frissítve a [beszéd SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) összetevőt a 1.0.1-es verziója. További információkért lásd: a [kibocsátási megjegyzések](https://aka.ms/csspeech/whatsnew). 
* Beszédfelismerés pontosságát javulni fog a feldolgozási teljesítmény javítása hang algoritmus  
* Egy folyamatos felismerés hang munkamenet kijavítanak.

**Használhatatlanná tévő változásai** 

* Ebben a kiadásban bevezetett kompatibilitástörő változások számos. Ellenőrizze a [ezt oldal](https://aka.ms/csspeech/breakingchanges_1_0_0) az API-k vonatkozó részletekért. 
* A KWS modell fájlokat, amelyek nem kompatibilisek a Speech Devices SDK-val 1.0.1. A meglévő hálózati Word-fájlokat törlődik, miután az új hálózati Word-fájlokat az eszközre írt. 

## <a name="cognitive-services-speech-devices-sdk-050-2018-aug-release"></a>Cognitive Services Speech Devices SDK 0.5.0: 2018-Aug kiadás

* Továbbfejlesztett beszédfelismerés pontosságát az audio feldolgozási kódban a hiba kijavítását.
* Frissítve a [beszéd SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) összetevőt a 0.5.0-s verzió. További információkért lásd: a [kibocsátási megjegyzések](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release).

## <a name="cognitive-services-speech-devices-sdk-0212733-2018-may-release"></a>A cognitive Services beszédfelismerő eszközök SDK 0.2.12733: 2018-május kiadás

A Cognitive Services beszédfelismerő eszközök SDK első nyilvános előzetes verziója.
