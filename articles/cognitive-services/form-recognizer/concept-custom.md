---
title: Egyéni modellek – űrlap-felismerő
titleSuffix: Azure Cognitive Services
description: Ismerje meg az űrlap-felismerő API egyéni modelljeivel kapcsolatos fogalmakat – használat és korlátok.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: lajanuar
ms.openlocfilehash: 9d339d02310fddc3e5fd463f7d37e253614dba17
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106067588"
---
# <a name="form-recognizer-custom-models"></a>Űrlap-felismerő egyéni modelljei

Az űrlap-felismerő speciális gépi tanulási technológiát használ az űrlapok és dokumentumok adatainak elemzéséhez és kinyeréséhez. Az űrlap-felismerő modell olyan kinyert adatok ábrázolása, amelyek hivatkozásként szolgálnak az adott tartalom elemzéséhez. Az űrlap-felismerő modelljeinek két típusa létezik:

* **Egyéni modellek**. Az űrlap-felismerő egyéni modelljei a vállalatra jellemző _űrlapok_ kinyert adatait jelölik. Az egyéni modelleket ki kell tanítani a különböző űrlapadatok elemzéséhez.

* **Előre elkészített modellek**. Az űrlap-felismerő jelenleg a _nyugták, a névjegykártyák, az azonosító kártyák_ és a _számlák előre összeállított_ modelljeit támogatja. Az előre elkészített modellek felderítik és kinyerik a dokumentumképek adatait, és a kinyert adatokat egy strukturált JSON-kimenetben adják vissza.

## <a name="what-does-a-custom-model-do"></a>Mi az egyéni modell?

Az űrlap-felismerő használatával olyan modellt is betaníthat, amely kinyeri az adatokat a használati eset szempontjából releváns űrlapokból. Első lépésként öt példát kell megadnia ugyanarra az űrlapra. Egyéni modelljét címkézett adatkészletek használatával vagy anélkül is betaníthatja.

## <a name="create-use-and-manage-your-custom-model"></a>Egyéni modell létrehozása, használata és kezelése

Az egyéni modell létrehozásával, betanításával és használatával kapcsolatos lépések a következők:

> [!div class="nextstepaction"]
>[&#120783;. A betanítási adatkészlet összeállítása](build-training-data-set.md#custom-model-input-requirements)

Az egyéni modell létrehozása a betanítási adatkészlet létrehozásával kezdődik. A minta adatkészlethez legalább öt befejezett, azonos típusú kész űrlapra lesz szüksége. Különböző fájltípusok lehetnek, és szöveget és kézírást is tartalmazhatnak. Az űrlapoknak ugyanolyan típusú dokumentumnak kell lenniük, és követniük kell az űrlap-felismerő [bemeneti követelményeit](build-training-data-set.md#custom-model-input-requirements) .  

> [!div class="nextstepaction"]
> [&#120784;. Töltse fel a betanítási adatkészletet](build-training-data-set.md#upload-your-training-data)

A betanítási adatait fel kell töltenie egy Azure Blob Storage-tárolóba. Ha nem tudja, hogyan hozhat létre egy tárolóval rendelkező Azure Storage-fiókot, tekintse [meg a Azure Portal Azure Storage](../../storage/blobs/storage-quickstart-blobs-portal.md)gyors üzembe helyezését *ismertető témakört* . Az ingyenes díjszabási csomaggal (F0) próbálja ki a szolgáltatást, és később frissítsen egy fizetős szintre az éles környezetben.  

> [!div class="nextstepaction"]
>[&#120785;. Az egyéni modell betanítása](quickstarts/client-library.md#train-a-custom-model)

A modellt [a címkével ellátott](quickstarts/client-library.md#train-a-model-with-labels) adatkészletek [nélkül](quickstarts/client-library.md#train-a-model-without-labels) is betaníthatja. A címke nélküli adatkészletek kizárólag az elrendezési API-ra támaszkodnak a kulcsfontosságú információk észlelésére és azonosítására a hozzáadott emberi bevitel nélkül. A címkével ellátott adatkészletek az elrendezési API-ra is támaszkodnak, de a kiegészítő emberi bevitel is szerepel, például az adott címkéket és a mezők helyét. Ha a címkével ellátott és a címkével ellátott adatmennyiséget egyaránt használni szeretné, kezdje a címkével ellátott betanítási adattípushoz legalább öt befejezett formát, majd vegyen fel címkézetlen adatkészletet a szükséges adatkészletbe.  

>[!div class="nextstepaction"]
>[&#120786;. Dokumentumok elemzése az egyéni modellel](quickstarts/client-library.md#analyze-forms-with-a-custom-model)

Tesztelje az újonnan betanított modellt olyan űrlap használatával, amely nem része a betanítási adatkészletnek. Az egyéni modell teljesítményének növeléséhez továbbra is folytathatja a képzést.  

> [!div class="nextstepaction"]
>[&#120787;. Egyéni modellek kezelése](quickstarts/client-library.md#manage-custom-models)

Bármikor megtekintheti az előfizetéshez tartozó egyéni modellek listáját, lekérheti az adott egyéni modell adatait, vagy törölhet egy egyéni modellt a fiókjából.

## <a name="next-steps"></a>Következő lépések

Az API-referenciák dokumentációjának megismerésével további információkat tudhat meg az űrlap felismerő ügyfelének könyvtárából.

> [!div class="nextstepaction"]
> [Űrlap-felismerő API-referenciája](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm)
>
