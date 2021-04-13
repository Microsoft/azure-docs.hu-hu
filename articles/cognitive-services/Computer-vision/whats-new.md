---
title: A Computer Vision újdonsága
titleSuffix: Azure Cognitive Services
description: Ez a cikk a jelentésekkel kapcsolatos Computer Vision.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 01/13/2021
ms.author: pafarley
ms.openlocfilehash: e42096fc32a504ae329d3b179004b6a123de4469
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365637"
---
# <a name="whats-new-in-computer-vision"></a>A Computer Vision

Ismerje meg a szolgáltatás újdonságát. Ezek lehetnek kibocsátási megjegyzések, videók, blogbejegyzések és más típusú információk. Az oldal könyvjelzővel való kiszolgálása, hogy naprakész maradjon a szolgáltatással.

## <a name="april-2021"></a>2021. április

### <a name="computer-vision-v32-ga"></a>Computer Vision v3.2 GA

A Computer Vision API 3.2-es verzió általánosan elérhető a következő frissítésekkel:
* Továbbfejlesztett képcímkézési modell: elemzi a vizualizáció tartalmát, és létrehozza a megfelelő címkéket a képen megjelenített objektumok, műveletek és tartalom alapján. Ez a Tag [Image API-n keresztül érhető el.](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f200) További információért tekintse meg [](https://docs.microsoft.com/azure/cognitive-services/computer-vision/overview-image-analysis) [a Képelemzés](https://docs.microsoft.com/azure/cognitive-services/computer-vision/vision-api-how-to-topics/howtocallvisionapi) útmutatóját és áttekintését.
* Frissített tartalommoderálási modell: észleli a felnőtteknek szóló tartalmak jelenlétét, és jelzőket biztosít a felnőtteknek szóló, racy és gory vizualizációs tartalmakat tartalmazó képek szűréséhez. Ez az [Analyze API-n keresztül érhető el.](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f21b) További információért tekintse meg [](https://docs.microsoft.com/azure/cognitive-services/computer-vision/overview-image-analysis) [a Képelemzés](https://docs.microsoft.com/azure/cognitive-services/computer-vision/vision-api-how-to-topics/howtocallvisionapi) útmutatóját és áttekintését.
* [Az OCR (read) 73](./language-support.md#optical-character-recognition-ocr) nyelven érhető el, beleértve az egyszerűsített és hagyományos kínai, japán, koreai és latin nyelvet.
* [Az OCR (olvasás)](./overview-ocr.md) [disztribúció](./computer-vision-how-to-install-containers.md?tabs=version-3-2) nélküli tárolóként is elérhető a környezetben való üzembe helyezéshez.

> [!div class="nextstepaction"]
> [Lásd: Computer Vision v3.2 GA](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005)

## <a name="march-2021"></a>2021. március

### <a name="computer-vision-32-public-preview-update"></a>Computer Vision 3.2 nyilvános előzetes verzió frissítése

A Computer Vision API 3.2 nyilvános előzetes verziója frissítve lett. Az előzetes kiadás az összes Computer Vision rendelkezik a frissített olvasási és elemző API-okkal együtt.

> [!div class="nextstepaction"]
> [Lásd: Computer Vision 3.2-es verzió nyilvános előzetes verziója](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005)

## <a name="february-2021"></a>2021. február

### <a name="read-api-v32-public-preview-with-ocr-support-for-73-languages"></a>Read API v3.2 Public Preview with OCR support for 73 languages (Az API 3.2 nyilvános előzetes verziója OCR-támogatással 73 nyelven)
Computer Vision felhőszolgáltatásként és Docker-tárolóként elérhető Read API 3.2 nyilvános előzetes verziója a következő frissítéseket tartalmazza:
* [OCR 73 nyelvhez,](./language-support.md#optical-character-recognition-ocr) például egyszerűsített és hagyományos kínai, japán, koreai és latin nyelvhez.
* A szöveges sor kimenetének természetes olvasási sorrendje (csak latin nyelvű)
* Kézírás-stílusbesorolás szövegsorok és megbízhatósági pontszámok esetén (csak latin nyelvek esetén).
* Többoldalas dokumentum szövegének kinyerása csak a kijelölt oldalakhoz.
* A rendszer [disztribúció](./computer-vision-how-to-install-containers.md?tabs=version-3-2) nélküli tárolóként érhető el a környezetben való üzembe helyezéshez.

További [információért tekintse](Vision-API-How-to-Topics/call-read-api.md) meg a Read API útmutatóját.

> [!div class="nextstepaction"]
> [A Read API 3.2 nyilvános előzetes verzió használata](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005)


## <a name="january-2021"></a>2021. január

### <a name="spatial-analysis-container-update"></a>Térbeli elemzés tárolófrissítése

Megjelent a [Spatial Analysis](spatial-analysis-container.md) tároló új verziója egy új funkciókészletben. Ezzel a Docker-tárolóval valós idejű streamelési videókat elemezhet, hogy megértse a térbeli kapcsolatokat az emberek és azok fizikai környezeten keresztüli mozgása között. 

* [A térbeli elemzési műveletek](spatial-analysis-operations.md) most már konfigurálhatóak úgy, hogy észlelni tudja, ha egy személy egy maszkkal elfedő arcot viseli. 
    * A és a művelethez a paraméter konfigurálásával engedélyezhető egy `personcount` `personcrossingline` `personcrossingpolygon` `ENABLE_FACE_MASK_CLASSIFIER` maszkosztályozó.
    * A és a attribútumok metaadatként lesznek visszaadva a videóstreamben észlelt minden `face_mask` `face_noMask` egyes személy megbízhatósági pontszámával
* A *personcrossingpolygon* művelet ki lett egészülve, hogy lehetővé tegye a zónában töltött hosszú idő kiszámítását. A paramétert a Zóna konfigurációjában a következőre állíthatja be: , és egy `type` `zonedwelltime` új *personZoneDwellTimeEvent* típusú esemény tartalmazza a zónában töltött személy által ezredmásodpercben megadott `durationMs` mezőt.
* **Breaking change**: A *personZoneEvent esemény* új neve *personZoneEnterExitEvent.* Ezt az eseményt  a személyszavaspolygon művelet idézi elő, amikor egy személy belép a zónába, vagy kilép a zónából, és irányt mutat a zóna áthúzott oldalával.
* A videó URL-címe minden műveletben "Privát paraméter/obfuscated" lehet. Az eltolás most nem kötelező, és csak akkor fog működni, ha `KEY` `IV` és környezeti változóként van megtéve.
* Alapértelmezés szerint minden művelet engedélyezve van. A `do_calibration: false` letiltásához állítsa be a következőt: .
* Az automatikus újraszámítás támogatása (alapértelmezés szerint le van tiltva) a paraméterrel. A részletekért tekintse meg a `enable_recalibration` térbeli elemzési [műveleteket](./spatial-analysis-operations.md)
* Kameraredeknációs paraméterek a `DETECTOR_NODE_CONFIG` számára. A részletekért tekintse meg [a térbeli elemzési műveleteket.](./spatial-analysis-operations.md)


## <a name="october-2020"></a>2020. október

### <a name="computer-vision-api-v31-ga"></a>Computer Vision API v3.1 GA

Az Computer Vision API általánosan elérhető verzióra lett frissítve a 3.1-es verzióra.

## <a name="september-2020"></a>2020. szeptember

### <a name="spatial-analysis-container-preview"></a>Térbeli elemzés tároló előzetes verziója

A [Spatial Analysis tároló előzetes](spatial-analysis-container.md) verzióban érhető el. A Computer Vision térbeli elemzés funkcióval valós idejű streamelési videókat elemezhet, hogy megértse a térbeli kapcsolatokat az emberek és azok fizikai környezeten keresztüli mozgása között. A térbeli elemzés egy helyszíni Docker-tároló. 

### <a name="read-api-v31-public-preview-adds-ocr-for-japanese"></a>Read API v3.1 Public Preview adds OCR for Japanese
Computer Vision Read API 3.1 nyilvános előzetes verziója a következő képességeket teszi elérhetővé:
* OCR japán nyelvhez
* Minden szövegsornál adja meg, hogy a megjelenés Kézírás vagy Nyomtatás típusú-e, valamint egy megbízhatósági pontszámot (csak latin nyelvű).
* Többoldalas dokumentumok esetén csak a kijelölt oldalakhoz vagy oldaltartományhoz kell szöveget kinyerni.

* A Read API ezen előzetes verziója az angol, a holland, a francia, a német, az olasz, a japán, a portugál, az egyszerűsített kínai és a spanyol nyelvet támogatja.

További [információért tekintse](Vision-API-How-to-Topics/call-read-api.md) meg a Read API útmutatóját.

> [!div class="nextstepaction"]
> [További információ a Read API v3.1 Public Preview 2 -ről](https://westus2.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-2/operations/5d986960601faab4bf452005)

## <a name="july-2020"></a>2020. július

### <a name="read-api-v31-public-preview-with-ocr-for-simplified-chinese"></a>Read API v3.1 Public Preview with OCR for Simplified Chinese
Computer Vision Read API 3.1 nyilvános előzetes verziója támogatja az egyszerűsített kínai nyelveket.

* A Read API ezen előzetes verziója az angol, a holland, a francia, a német, az olasz, a portugál, az egyszerűsített kínai és a spanyol nyelvet támogatja.

További [információért tekintse](Vision-API-How-to-Topics/call-read-api.md) meg a Read API útmutatóját.

> [!div class="nextstepaction"]
> [További információ a Read API v3.1 Public Preview 1 -ről](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-1/operations/5d986960601faab4bf452005)

## <a name="may-2020"></a>2020. május
Computer Vision API 3.0-s verzió általánosan elérhető volt, a Read API frissítésekkel:

* Angol, holland, francia, német, olasz, portugál és spanyol támogatás
* Nagyobb pontosság
* Megbízhatósági pontszám minden kinyert szóhoz
* Új kimeneti formátum

További [információért tekintse](overview-ocr.md) meg az OCR áttekintését.

## <a name="march-2020"></a>2020. március

* A TLS 1.2 mostantól a szolgáltatásnak szóló összes HTTP-kérésre érvényes. További információ: [Azure Cognitive Services.](../cognitive-services-security.md)

## <a name="january-2020"></a>2020. január

### <a name="read-api-30-public-preview"></a>Read API 3.0 Public Preview

Most már használhatja a Read API 3.0-s verzióját nyomtatott vagy kézzel írt szöveg képekből való kinyerhet. A korábbi verziókhoz képest a 3.0 a következőt biztosítja:
* Nagyobb pontosság
* Új kimeneti formátum
* Megbízhatósági pontszám minden kinyert szóhoz
* A spanyol és az angol nyelv támogatása a további nyelvi paraméterrel

Kövesse a [Szöveg kinyerása rövid útmutatót](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/REST/CSharp-hand-text.md?tabs=version-3) a 3.0 API használatának elkezdődhet.

## <a name="cognitive-service-updates"></a>Cognitive Services-frissítések

[Az Azure frissítésére vonatkozó közlemények Cognitive Services](https://azure.microsoft.com/updates/?product=cognitive-services)
