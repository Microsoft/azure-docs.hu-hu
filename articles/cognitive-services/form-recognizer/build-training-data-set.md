---
title: Betanítási adatkészlet létrehozása egyéni modell-űrlap felismerőhöz
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan biztosíthatja, hogy a betanítási adatkészletet az űrlap-felismerő modell betanítására optimalizálták.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: pafarley
ms.openlocfilehash: ee57ccb82e771ee8ab93b09e476a94df32278069
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99585109"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>Betanítási adatkészlet létrehozása egyéni modellhez

Az űrlap-felismerő egyéni modelljének használatakor saját betanítási adatait adhatja meg a [vonat egyéni modellje](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync) műveletnek, így a modell elvégezhető az iparág-specifikus űrlapok számára. Ebből az útmutatóból megtudhatja, hogyan gyűjthet és készíthet elő adatokat a modell hatékony betanításához.

Legalább öt kitöltött azonos típusú űrlapra van szüksége.

Ha manuálisan címkézett betanítási adatmennyiséget szeretne használni, legalább öt kitöltött, azonos típusú kitöltésű űrlapon kell kezdődnie. Továbbra is használhatja a nem címkézett űrlapokat a szükséges adatkészleten kívül.

## <a name="custom-model-input-requirements"></a>Egyéni modell bemeneti követelményei

Először győződjön meg arról, hogy a betanítási adatkészlet az űrlap-felismerő bemeneti követelményeit követi.

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="training-data-tips"></a>Az adatképzéssel kapcsolatos tippek

Kövesse ezeket a további tippeket, hogy tovább optimalizálja az adatkészletet a képzéshez.

* Ha lehetséges, a képeken alapuló dokumentumok helyett szöveges alapú PDF-dokumentumokat használjon. A beolvasott PDF-fájlok képként vannak kezelve.
* A kitöltött űrlapok esetében olyan példákat használjon, amelyekben az összes mező kitöltése megtörtént.
* Minden mezőben más értékkel rendelkező űrlapot használjon.
* Ha az űrlapon lévő képek gyengébb minőségűek, használjon nagyobb adatkészletet (például 10-15-es lemezképeket).

## <a name="upload-your-training-data"></a>A betanítási adatok feltöltése

Ha összeállította a betanításhoz használni kívánt űrlap-dokumentumokat, fel kell töltenie egy Azure Blob Storage-tárolóba. Ha nem tudja, hogyan hozhat létre egy tárolóval rendelkező Azure Storage-fiókot, kövesse a [Azure Portal Azure Storage](../../storage/blobs/storage-quickstart-blobs-portal.md)gyors üzembe helyezési útmutatóját. A standard szintű teljesítményszint használata.

Ha manuálisan címkézett adatokkal kívánja használni, akkor is fel kell töltenie a *.labels.jst* , és *.ocr.jsa* betanítási dokumentumoknak megfelelő fájlokra. A fájlok létrehozásához használhatja a [minta feliratozási eszközt](./quickstarts/label-tool.md) (vagy a saját felhasználói felületét).

### <a name="organize-your-data-in-subfolders-optional"></a>Az Ön adatait almappákban rendszerezheti (opcionális)

Alapértelmezés szerint az [Egyéni modell](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync) -API-k csak a Storage-tároló gyökerében található űrlap-dokumentumokat használják. Ha azonban az API-hívásban megadhatja, az almappákban is betaníthatja az adatkészletet. A [vonat egyéni modell](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync) -hívásának törzse általában a következő formátumú, ahol `<SAS URL>` a a tároló közös hozzáférésű aláírásának URL-címe:

```json
{
  "source":"<SAS URL>"
}
```

Ha az alábbi tartalmat adja hozzá a kérelem törzséhez, az API az almappákban található dokumentumokkal fog betanítani. A `"prefix"` mező nem kötelező, és a betanítási adatkészletet a megadott karakterlánccal kezdődő elérési úttal rendelkező fájlokra korlátozza. Így `"Test"` például az API csak azokat a fájlokat vagy mappákat fogja megtekinteni, amelyek a "test" kifejezéssel kezdődnek.

```json
{
  "source": "<SAS URL>",
  "sourceFilter": {
    "prefix": "<prefix string>",
    "includeSubFolders": true
  },
  "useLabelFile": false
}
```

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan hozhat létre egy betanítási adatkészletet, egy rövid útmutató segítségével betaníthat egy egyéni űrlap-felismerő modellt, és megkezdheti az űrlapokon való használatát.

* [Modellek betanítása és űrlapadatok kinyerése az ügyféloldali kódtár vagy REST API használatával](./quickstarts/client-library.md)
* [Betanítás címkék használatával a minta feliratozási eszközzel](./quickstarts/label-tool.md)

## <a name="see-also"></a>Lásd még

* [Mi a Form Recognizer?](./overview.md)