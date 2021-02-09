---
title: A Computer Vision újdonságai
titleSuffix: Azure Cognitive Services
description: Ez a cikk a Computer Visionával kapcsolatos híreket tartalmaz.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 01/13/2021
ms.author: pafarley
ms.openlocfilehash: 1128504ee5f8c086d6a3c30a9fdd021550394f3f
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2021
ms.locfileid: "99981765"
---
# <a name="whats-new-in-computer-vision"></a>A Computer Vision újdonságai

Ismerje meg a szolgáltatás újdonságait. Ezek az elemek kibocsátási megjegyzések, videók, blogbejegyzések és más típusú információk lehetnek. Könyvjelző ezen az oldalon naprakészen maradhat a szolgáltatással.

## <a name="february-2021"></a>2021. február

### <a name="read-api-v32-public-preview-with-ocr-support-for-73-languages"></a>Az API v 3.2 nyilvános előzetes verziója az OCR-támogatással 73 nyelven
A Computer Vision Read API v 3.2 nyilvános előzetes verziója a következő funkciókat tartalmazza:
* [OCR a 73 nyelvekhez](./language-support.md#optical-character-recognition-ocr) , beleértve az egyszerűsített és a hagyományos kínai, Japán, Koreai és latin nyelveket.
* A szöveges sorok kimenete a természetes olvasási sorrendben.
* Szövegsorok osztályozása kézírás stílusaként vagy nem a megbízhatósági pontszám mellett (csak latin nyelveken).
* Többoldalas dokumentumok esetén csak a kijelölt lapok vagy az oldal tartománya számára kell szöveget kinyerni.

További információt az [előzetes verzió funkcióinak olvasása](concept-recognizing-text.md#natural-reading-order-output) című témakörben talál.

> [!div class="nextstepaction"]
> [A Read API v 3.2 nyilvános előzetes verziójának használata](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-2/operations/5d986960601faab4bf452005)


## <a name="january-2021"></a>2021. január

### <a name="spatial-analysis-container-update"></a>Térbeli elemzési tároló frissítése

A [térbeli elemzési tároló](spatial-analysis-container.md) új verziója megjelent egy új szolgáltatáskészlettel. Ez a Docker-tároló lehetővé teszi a valós idejű streaming videó elemzését, hogy megértse az emberek közötti térbeli kapcsolatokat és a fizikai környezetek közötti mozgást. 

* A [térbeli elemzési műveletek](spatial-analysis-operations.md) most már úgy is konfigurálhatók, hogy megismerjék, van-e olyan védelmi arc, amely magában foglalja a maszkot. 
    * A (z) és a (z `personcount` ) paraméter konfigurálásával engedélyezhető a maszk besorolása a `personcrossingline` és a `personcrossingpolygon` műveletekhez `ENABLE_FACE_MASK_CLASSIFIER` .
    * Az attribútumokat `face_mask` és a `face_noMask` rendszer a videó streamben észlelt minden személy megbízhatósági pontszámát tartalmazó metaadatként adja vissza.
* A *personcrossingpolygon* művelet ki lett terjesztve, hogy lehetővé váljon a személy számára a zónában töltött tartózkodási idő kiszámításának engedélyezése. A `type` (z) paramétert beállíthatja a művelet `zonedwelltime` *personZoneDwellTimeEvent* , és egy új típusú esemény is szerepel a `durationMs` zónában töltött személy ezredmásodpercben megadott számával.
* **Megszakítási változás**: a *personZoneEvent* esemény átnevezve lett a *personZoneEnterExitEvent* névre. Ez az esemény akkor következik be, amikor egy személy belép vagy kilép a zónából, és irányt ad a *personcrossingpolygon* , amely áthaladt a zóna számozott oldalával.
* A videó URL-címe az összes műveletben "privát paraméterként/elhomályosítva" adható meg. A elhomályosítás most nem kötelező, és csak akkor működik, ha `KEY` `IV` környezeti változókként vannak megadva.
* A kalibrálás alapértelmezés szerint engedélyezve van az összes művelethez. A letiltásához állítsa be a parancsot `do_calibration: false` .
* Az automatikus újrakalibrálás támogatása (alapértelmezés szerint letiltva) a `enable_recalibration` paraméterrel, a részletekért tekintse meg a [térbeli elemzési műveleteket](https://docs.microsoft.com/azure/cognitive-services/computer-vision/spatial-analysis-operations) ismertető témakört.
* A kamera-kalibrálási paraméterek a következőre: `DETECTOR_NODE_CONFIG` . Részletekért tekintse meg a [térbeli elemzési műveleteket](https://docs.microsoft.com/azure/cognitive-services/computer-vision/spatial-analysis-operations) .


## <a name="october-2020"></a>2020. október

### <a name="computer-vision-api-v31-ga"></a>Computer Vision API v 3.1 GA

Az általánosan elérhető Computer Vision API frissítve lett a v 3.1-re.

## <a name="september-2020"></a>2020. szeptember

### <a name="spatial-analysis-container-preview"></a>Térbeli elemzési tároló – előzetes verzió

A [térbeli elemzési tároló](spatial-analysis-container.md) mostantól előzetes verzióban érhető el. A Computer Vision térbeli elemzési funkciója lehetővé teszi, hogy elemezze a valós idejű adatfolyam-továbbítási videókat, hogy megértse a személyek közötti térbeli kapcsolatokat és a fizikai környezetek közötti mozgást. A térbeli elemzés egy olyan Docker-tároló, amelyet a helyszínen használhat. 

### <a name="read-api-v31-public-preview-adds-ocr-for-japanese"></a>Az API v 3.1-es verziójának nyilvános előzetes verziója hozzáadja a japán OCR-t
A Computer Vision Read API v 3.1 nyilvános előzetes verziója hozzáadja ezeket a képességeket:
* OCR japán nyelvhez
* Mindegyik szövegmezőnél jelezze, hogy a megjelenés a kézírás vagy a nyomtatási stílus, valamint a megbízhatósági pontszám (csak latin nyelvek esetén).
* Többoldalas dokumentumok esetén csak a kijelölt lapok vagy az oldal tartománya számára kell szöveget kinyerni.

* Az olvasási API előzetes verziója az angol, holland, francia, német, olasz, Japán, portugál, egyszerűsített kínai és spanyol nyelveket támogatja.

További információért tekintse meg az [OLVASÁSI API áttekintése](concept-recognizing-text.md) című témakört.

> [!div class="nextstepaction"]
> [További információ az olvasási API v 3.1 nyilvános előzetes verziójáról 2](https://westus2.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-2/operations/5d986960601faab4bf452005)

## <a name="july-2020"></a>2020. július

### <a name="read-api-v31-public-preview-with-ocr-for-simplified-chinese"></a>Az API v 3.1 nyilvános előzetes verziójának olvasása az OCR használatával egyszerűsített kínai nyelven
A Computer Vision Read API v 3.1 nyilvános előzetes verziója támogatja az egyszerűsített kínai verziót.

* Az olvasási API előzetes verziója az angol, holland, francia, német, olasz, portugál, egyszerűsített kínai és spanyol nyelveket támogatja.

További információért tekintse meg az [OLVASÁSI API áttekintése](concept-recognizing-text.md) című témakört.

> [!div class="nextstepaction"]
> [További információ az olvasási API v 3.1 nyilvános előzetes verziójának 1](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-1/operations/5d986960601faab4bf452005)

## <a name="may-2020"></a>2020. május
Computer Vision API v 3.0 általánosan elérhető, az [OLVASÁSI API](concept-recognizing-text.md)frissítéseivel:

* Angol, holland, francia, német, olasz, portugál és spanyol nyelven történő támogatás
* Javított pontosság
* Minden kinyert szó megbízhatósági pontszáma
* Új kimeneti formátum

## <a name="march-2020"></a>2020. március

* A TLS 1,2 mostantól a szolgáltatáshoz tartozó összes HTTP-kérelem esetében érvénybe lép. További információ: [Azure Cognitive Services Security](../cognitive-services-security.md).

## <a name="january-2020"></a>2020. január

### <a name="read-api-30-public-preview"></a>Olvasási API 3,0 nyilvános előzetes verzió

Most lehetősége van arra, hogy az olvasási API 3,0-es verzióját használja a nyomtatott vagy a kézírásos szöveg kinyeréséhez a képekből. A korábbi verziókhoz képest a 3,0 a következőket biztosítja:
* Javított pontosság
* Új kimeneti formátum
* Minden kinyert szó megbízhatósági pontszáma
* A spanyol és az angol nyelv támogatása a további nyelvi paraméterrel

A 3,0 API használatának megkezdéséhez kövesse a [kinyerési szöveg](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/REST/CSharp-hand-text.md?tabs=version-3) rövid útmutatóját.

## <a name="cognitive-service-updates"></a>A kognitív szolgáltatás frissítései

[Cognitive Services Azure Update-hirdetmények](https://azure.microsoft.com/updates/?product=cognitive-services)
