---
title: Azure Percept AI-modellek
description: További információ a prototípus-készítéshez és üzembe helyezéshez elérhető AI-modellekről
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: template-concept
ms.openlocfilehash: b644adb98de5ef5e3af6e1c8d45ea1a207d05cc7
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "104956128"
---
# <a name="azure-percept-ai-models"></a>Azure Percept AI-modellek

Az Azure Percept lehetővé teszi, hogy AI-modelleket fejlesszen és helyezzen üzembe közvetlenül az Azure [Percept studióból](https://go.microsoft.com/fwlink/?linkid=2135819)az [Azure Percept DK](./overview-azure-percept-dk.md) -be. A modell üzembe helyezése az [Azure IoT hub](https://azure.microsoft.com/services/iot-hub/) és [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/#iotedge-overview)használatát is felhasználja.

## <a name="sample-ai-models"></a>Minta AI-modellek

Az Azure Percept Studio a következő alkalmazásokhoz tartalmaz minta modelleket:

- emberek észlelése
- jármű észlelése
- általános objektumok észlelése
- termékek – helyszíni észlelés

Az előre betanított modellek esetében nincs szükség kódolási vagy betanítási adatgyűjtésre. Egyszerűen [üzembe helyezheti a kívánt modellt](./how-to-deploy-model.md) az Azure Percept DK-be a portálon, és megnyithatja a fejlesztői készlet [video streamjét](./how-to-view-video-stream.md) , hogy megtekintse a modellen alapuló műveletet. A [Model következtetési telemetria](./how-to-view-telemetry.md) az [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer/releases) eszköz használatával is elérhetők.

## <a name="reference-solutions"></a>Referenciamegoldások

A rendszer szintén elérhetővé teszi A [felhasználók számlálására szolgáló hivatkozási megoldást](https://github.com/microsoft/Azure-Percept-Reference-Solutions/tree/main/people-detection-app) . Ez a hivatkozási megoldás egy nyílt forráskódú AI-alkalmazás, amely a felhasználó által megadott zóna-belépési/kilépési eseményekkel rendelkező, Edge-alapú személyek számára biztosít. A helyszíni peremhálózati eszköz videó-és AI-kimenete egressed, és az Azure-webhelyként futó felhasználói felülettel [Azure Data Lake](https://azure.microsoft.com/solutions/data-lake/). Az AI-következtetést egy nyílt forráskódú AI-modell nyújtja a személyek észlelésére.

:::image type="content" source="./media/overview-ai-models/people-detector.gif" alt-text="Térbeli elemzés – előre elkészített megoldás gif.":::

## <a name="custom-no-code-solutions"></a>Egyéni kód nélküli megoldások

Az Azure Percept Studio segítségével egyéni [jövőkép](./tutorial-nocode-vision.md) -és [beszédfelismerési](./tutorial-no-code-speech.md) megoldásokat fejleszthet, és nincs szükség kódolásra.

Az egyéni látási megoldások esetében mind az objektum-észlelési, mind a besorolási AI-modellek elérhetők. Egyszerűen töltse fel és címkézze fel a betanítási lemezképeket, amelyek igény szerint közvetlenül az Azure Percept DK Azure Percept-víziójának használatával hozhatók létre. A modell betanítása és kiértékelése könnyen elvégezhető az [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/#overview)részét képező [Custom Visionban](https://www.customvision.ai/).

</br>

> [!VIDEO https://www.youtube.com/embed/9LvafyazlJM]

Az egyéni beszédfelismerési megoldások esetében a hangsegéd-sablonok jelenleg a következő alkalmazásokhoz érhetők el:

- Vendéglátás: a szállodai szoba hangvezérelt intelligens eszközökkel van ellátva.
- Egészségügyi ellátás: hangvezérelt intelligens eszközökkel ellátott Care-eszköz.
- Leltár: a leltár hub hang-vezérelt intelligens eszközökkel van ellátva.
- Autóipar: hangvezérelt intelligens eszközökkel ellátott autóipari központ.

Az előre elkészített hangasszisztensi kulcsszavak és parancsok közvetlenül a portálon keresztül érhetők el. Egyéni kulcsszavakat és parancsokat hozhat létre és taníthat a [Speech Studióban](https://speech.microsoft.com/), amely az Azure Cognitive Services része is.

## <a name="advanced-development"></a>Fejlett fejlesztés

Tekintse meg az [Azure PERCEPT DK Advanced Development githubot](https://github.com/microsoft/azure-percept-advanced-development) , amely naprakész útmutatást, oktatóanyagokat és példákat tartalmaz a következőkhöz:

- Egyéni AI-modell üzembe helyezése az Azure Percept DK-ben
- Támogatott modell frissítése az adatátviteli tanulással
- És még sok minden más
