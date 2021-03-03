---
title: Azure Percept AI-modellek
description: További információ a prototípus-készítéshez és üzembe helyezéshez elérhető AI-modellekről
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/16/2021
ms.custom: template-concept
ms.openlocfilehash: e61fb00b4ddd309124c11d1749f448c742d660d0
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663189"
---
# <a name="azure-percept-ai-models"></a>Azure Percept AI-modellek

Az Azure Percept lehetővé teszi, hogy AI-modelleket fejlesszen és helyezzen üzembe közvetlenül az Azure [Percept studióból](https://go.microsoft.com/fwlink/?linkid=2135819)az Azure Percept DK-be. A modell üzembe helyezése az [Azure IoT hub](https://azure.microsoft.com/services/iot-hub/) és [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/#iotedge-overview)használatát is felhasználja.

## <a name="sample-ai-models"></a>Minta AI-modellek

Az Azure Percept Studio a következő alkalmazásokhoz tartalmaz minta modelleket:

- Arcfelismerés
- emberek észlelése
- jármű észlelése
- általános objektumok észlelése
- termékek – helyszíni észlelés

Az előre betanított modellek esetében nincs szükség kódolási vagy betanítási adatgyűjtésre. Egyszerűen üzembe helyezheti a kívánt modellt az Azure Percept DK-be a portálon, és megnyithatja a fejlesztői készlet video streamjét, hogy megtekintse a modellen alapuló műveletet. A Model következtetési telemetria az [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer/releases) eszköz használatával is elérhetők.

## <a name="pre-built-solutions"></a>Előre elkészített megoldások

A [felhasználók észlelésére szolgáló, előre elkészített térbeli elemzési megoldás](https://github.com/george-moore/Santa-Cruz-AI-App) is elérhető. Az előre elkészített megoldás egy nyílt forráskódú AI-alkalmazás, amely a felhasználó által megadott zóna-belépési/kilépési eseményekkel rendelkező, Edge-alapú személyek számára biztosít. A helyszíni peremhálózati eszköz videó-és AI-kimenete egressed, és az Azure-webhelyként futó felhasználói felülettel [Azure Data Lake](https://azure.microsoft.com/solutions/data-lake/). Az AI-következtetést egy nyílt forráskódú AI-modell nyújtja a személyek észlelésére.

:::image type="content" source="./media/overview-ai-models/people-detector.gif" alt-text="Térbeli elemzés – előre elkészített megoldás gif.":::

## <a name="custom-no-code-solutions"></a>Egyéni kód nélküli megoldások

Az Azure Percept Studio segítségével egyéni [jövőkép](./tutorial-nocode-vision.md) -és beszédfelismerési megoldásokat fejleszthet, és nincs szükség kódolásra.

Az egyéni látási megoldások esetében mind az objektum-észlelési, mind a besorolási AI-modellek elérhetők. Egyszerűen töltse fel és címkézze fel a betanítási lemezképeket, amelyek igény szerint közvetlenül az Azure Percept DK Azure Percept-víziójának használatával hozhatók létre. A modell betanítása és kiértékelése könnyen elvégezhető az [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/#overview)részét képező [Custom Visionban](https://www.customvision.ai/).

Az egyéni beszédfelismerési megoldások esetében a hangsegéd-sablonok jelenleg a következő alkalmazásokhoz érhetők el:

- Vendéglátás: a szállodai szoba hangvezérelt intelligens eszközökkel van ellátva.
- Egészségügyi ellátás: hangvezérelt intelligens eszközökkel ellátott Care-eszköz.
- Leltár: a leltár hub hang-vezérelt intelligens eszközökkel van ellátva.
- Autóipar: hangvezérelt intelligens eszközökkel ellátott autóipari központ.

Az előre elkészített hangasszisztensi kulcsszavak és parancsok közvetlenül a portálon keresztül érhetők el. Egyéni kulcsszavakat és parancsokat hozhat létre és taníthat a [Speech Studióban](https://speech.microsoft.com/), amely az Azure Cognitive Services része is.

## <a name="advanced-development"></a>Fejlett fejlesztés

A fejlett fejlesztők számára az elérhető [Jupyter-jegyzetfüzetek](https://github.com/microsoft/Project-Santa-Cruz-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb) a Pythonban egy előre betanított TensorFlow-modell (MobileNetSSDV2Lite) használatával végzik el az adatátvitelt az objektumok észlelésére szolgáló egyéni adatkészlet segítségével. A jegyzetfüzet a távoli számítási példányokat a [Azure Machine Learningon](https://azure.microsoft.com/services/machine-learning/#product-overview) keresztül használja, és a felhőben a AzureML portál használatával vagy helyileg, a [Visual Studio Code](https://code.visualstudio.com/)-ban futtatható.

Emellett néhány hasznos Python- [szkriptet](https://github.com/microsoft/Project-Santa-Cruz-Preview/tree/main/Sample-Scripts-and-Notebooks/Official/Scripts) is tartalmaz az adatkészletek és a [dev Tools Pack telepítőjének](https://github.com/microsoft/Project-Santa-Cruz-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/dev-tools-installer.md)kezeléséhez, amely a fejlett AI-megoldások fejlesztéséhez szükséges összes eszközt telepíti és konfigurálja.
