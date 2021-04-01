---
title: Mi az élő videó-elemzés a IoT Edgeon – Azure
description: Ez a témakör áttekintést nyújt a IoT Edge élő videók elemzéséről. A platform a IoT-megoldások fejlesztéséhez használható képességet kínálja. Például rögzítheti, rögzítheti és elemezheti az élő videót, és közzéteheti az eredményeket (videó és/vagy videó-elemzés) az Azure-szolgáltatásokban.
ms.topic: overview
ms.date: 05/27/2020
ms.openlocfilehash: b9b8726c5e8ad6850e05aeee48fccabee703080e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "90904348"
---
# <a name="what-is-live-video-analytics-on-iot-edge-preview"></a>Mi a IoT Edge Live Video Analytics? (előzetes verzió)

Az IoT Edge-en futó Live Video Analytics olyan platformot nyújt, amelyen a peremhálózatra és a felhőre kiterjedő intelligens videóalkalmazások készíthetők. A platform lehetővé teszi az élő videók rögzítését, felvételét és elemzését, valamint az eredmények (videó és/vagy videóelemzés) közzétételét az Azure-szolgáltatásokban (a felhőben és/vagy a peremhálózaton). A platform segítségével javíthatja a IoT-megoldásokat a video Analytics használatával. A IoT Edge funkcióban élő videó-elemzések kombinálhatók más Azure IoT Edge-modulokkal, Stream Analytics például a IoT Edge, Cognitive Services a IoT Edge, valamint a felhőben található Azure-szolgáltatásokkal, például az Media Services, az Event hub, Cognitive Services stb. hatékony hibrid (például Edge + Cloud) alkalmazások létrehozásához.

A IoT Edge Live Video Analytics bővíthető platformként lett kialakítva, amely lehetővé teszi a különböző videó-elemzési peremhálózati modulok (például a kognitív Services-tárolók, az Ön által létrehozott egyéni Edge-modulok vagy a saját adataival rendelkező egyéni modellek) összekapcsolását, és az élő videók elemzésének bonyolultságát anélkül, hogy az éles videó folyamatának összetettségét kellene használnia.

## <a name="accelerate-iot-solutions-development"></a>Gyorsítsa fel a IoT-megoldások fejlesztését 

A videóelemzést más IoT-érzékelőkből és/vagy üzleti adatokból származó jelekkel kombináló IoT-megoldásokkal automatizálhatja vagy félig automatizálhatja üzleti döntéseit, ami a termelékenység javulását eredményezi. Az IoT Edge-en futó Live Video Analytics lehetővé teszi, hogy gyorsabban készítsen ilyen megoldásokat. A vállalkozáshoz kapcsolódó videóelemző modulok és logika elkészítésére összepontosíthat, és a platformmal elrejtheti a videós folyamat kezelésének és futtatásának bonyolultságát.

A IoT Edge élő videó-elemzési szolgáltatásával továbbra is használhatja a [CCTV kamerákat](https://en.wikipedia.org/wiki/Closed-circuit_television_camera) a meglévő [videó-felügyeleti rendszerekkel (VM)](https://en.wikipedia.org/wiki/Video_management_system) , és a video Analytics-alkalmazásokat egymástól függetlenül is létrehozhatja. Az IoT Edge-en futó Live Video Analytics a Computer Vision SDK-kal és eszközkészletekkel együtt élvonalbeli IoT-megoldások létrehozásához használható. Az alábbi diagram ezt ábrázolja.

![IoT-megoldások fejlesztése élő videó-elemzéssel IoT Edge](./media/overview/product-diagram.svg)

## <a name="supported-environments"></a>Támogatott környezetek

A Linux x86-64-és ARM64-környezetek támogatottak.
> [!NOTE]
> A ARM64-eszközök támogatása a buildekben és az újabb verziókban érhető el `1.0.4` .
> A Azure IoT Edge Runtime ARM64-eszközökön való futtatásának támogatása [nyilvános előzetes](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verzióban érhető el.

## <a name="get-started"></a>Bevezetés

Olvassa el a következő fogalmakat, majd próbáljon ki egy rövid útmutatót a mozgásérzékelők élő videó-hírcsatornán való futtatásához.

### <a name="concepts"></a>Alapelvek

* [Médiagrafikon](media-graph-concept.md)
* [Videófelvétel](video-recording-concept.md)
* [Videó lejátszása](video-playback-concept.md)
* [Folyamatos videófelvétel](continuous-video-recording-concept.md)
* [Eseményalapú videófelvétel](event-based-video-recording-concept.md)
* [Live Video Analytics videófelvétel nélkül](analyze-live-video-concept.md)

## <a name="next-steps"></a>Következő lépések

* Kövesse a gyors útmutató [: élő videó elemzés futtatása saját modell](use-your-model-quickstart.md) használatával című cikket, amelyből megtudhatja, hogyan futtathatja a mozgásérzékelőt élő videó-hírcsatornán.
* [Terminológia](terminology.md) áttekintése
* Fedezze fel az [élő videó Analytics nyílt forráskódú anyagát](https://github.com/Azure/live-video-analytics)

