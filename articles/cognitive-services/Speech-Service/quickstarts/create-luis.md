---
title: 'Gyors útmutató: LUIS-kulcs létrehozása'
titleSuffix: Azure Cognitive Services
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre LUIS-alkalmazást, és hogyan szerezhet be kulcsot.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: dapine
ms.openlocfilehash: 5b1a5ac4867379457d161d07f4f4f2fc2d8ee6c3
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77119593"
---
# <a name="quickstart-getting-a-luis-endpoint-key"></a>Gyors útmutató: LUIS Endpoint kulcs beszerzése

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag megkezdése előtt győződjön meg arról, hogy rendelkezik az alábbi elemekkel:

* Egy LUIS-fiók. Létrehozhat egy ingyenes fiókot a [LUIS portal](https://www.luis.ai/home) segítségével.

## <a name="luis-and-speech"></a>A LUIS és a beszéd

A LUIS integrálható a Speech Service szolgáltatással a beszédből való szándékfelismerés céljából. Ehhez nincs szükség Speech Service-előfizetésre, csak a LUIS-ra.

A LUIS háromféle kulcsot használ:

|Kulcs típusa|Cél|
|--------|-------|
|Tartalomkészítés|Lehetővé teszi a LUIS-alkalmazások programozott módon történő létrehozását és módosítását|
|Kezdő|Lehetővé teszi a LUIS-alkalmazás tesztelését csak szöveg használatával|
|Végpont |Engedélyezi a hozzáférést egy adott LUIS-alkalmazáshoz|

Ebben az oktatóanyagban a végponti kulcs típusát kell megadnia. Az oktatóanyag a példa Home Automation LUIS alkalmazást használja, amelyet az [előre elkészített Home Automation-alkalmazás használata](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app) című rövid útmutatóban hozhat létre. Ha saját LUIS-alkalmazást hozott létre, azt használhatja helyette.

LUIS-alkalmazás létrehozásakor a LUIS automatikusan létrehoz egy alapszintű kulcsot, amellyel szöveges lekérdezések használatával tesztelheti az alkalmazást. Ez a kulcs nem engedélyezi a beszédfelismerési szolgáltatás integrációját, és nem fog működni ezzel az Oktatóanyaggal. Hozzon létre egy LUIS-erőforrást az Azure irányítópulton, és rendelje hozzá a LUIS alkalmazáshoz. Az oktatóanyaghoz használhatja az ingyenes előfizetési szintet.

Miután létrehozta a LUIS-erőforrást az Azure-irányítópulton, jelentkezzen be a [Luis portálra](https://www.luis.ai/home), válassza ki az alkalmazást a **saját alkalmazások** lapon, majd váltson az alkalmazás **kezelése** lapra. Végül válassza a **kulcsok és végpontok** lehetőséget az oldalsávon.

![A LUIS portál kulcs- és a végpontbeállításai](~/articles/cognitive-services/Speech-Service/media/sdk/luis-keys-endpoints-page.png)

A **kulcsok és végpont beállításai** lapon:

1. Görgessen le az **erőforrások és kulcsok** szakaszhoz, majd válassza az **erőforrás kiosztása**elemet.
1. A **kulcs kiosztása az alkalmazáshoz** párbeszédpanelen végezze el a következő módosításokat:

   * A **bérlő**területen válassza a **Microsoft**lehetőséget.
   * Az **előfizetés neve**területen válassza ki azt az Azure-előfizetést, amely a használni kívánt Luis-erőforrást tartalmazza.
   * A **kulcs**alatt válassza ki az alkalmazással használni kívánt Luis-erőforrást.

   Az új előfizetés hamarosan megjelenik a lap alján található táblázatban.

1. Kattintson a kulcs melletti ikonra a vágólapra másoláshoz. (Bármelyik kulcsot használhatja.)

![A LUIS-app előfizetési kulcsai](~/articles/cognitive-services/Speech-Service/media/sdk/luis-keys-assigned.png)


## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Leképezések felismerése](~/articles/cognitive-services/Speech-Service/quickstarts/intent-recognition.md)
