---
title: Időjárás-előrejelzés a Azure Machine Learning Studio (klasszikus) használatával IoT Hub adattal
description: A Azure Machine Learning Studio (klasszikus) használatával megjósolhatja, hogy az IoT hub milyen hőmérséklet-és páratartalom-adatok alapján gyűjti az adatokat az érzékelőből.
author: robinsh
manager: philmea
keywords: Időjárás-előrejelzés gépi tanulás
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 09/16/2020
ms.author: robinsh
ms.openlocfilehash: ab9e122ba0b2b50203a2d66ae14f03f3b6300f96
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96452349"
---
# <a name="weather-forecast-using-the-sensor-data-from-your-iot-hub-in-azure-machine-learning-studio-classic"></a>Időjárás-előrejelzés az IoT hub Sensor-adatainak használatával Azure Machine Learning Studio (klasszikus)

![Végpontok közötti diagram](media/iot-hub-get-started-e2e-diagram/6.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

A gépi tanulás olyan adatelemzési módszer, amely segít a számítógépeknek megtanulni a meglévő adatokból a jövőbeli viselkedések, eredmények és trendek előrejelzését. A Azure Machine Learning Studio (klasszikus) egy felhőalapú prediktív elemzési szolgáltatás, amely lehetővé teszi, hogy elemzési megoldásként gyorsan hozzon létre és helyezzen üzembe prediktív modelleket.

## <a name="what-you-learn"></a>Ismertetett témák

Az Azure IoT hub hőmérséklet-és páratartalom-adatainak használatával megtudhatja, hogyan végezheti el a Azure Machine Learning Studio (klasszikus) használatát az időjárási előrejelzések (az eső esélye) használatára. Az eső esélye az előkészített időjárási előrejelzési modell kimenete. A modell a múltbeli adatmennyiségre épül, hogy a hőmérséklet és a páratartalom alapján megbecsülje az eső esélyét.

## <a name="what-you-do"></a>Teendők

- Az időjárási előrejelző modell üzembe helyezése webszolgáltatásként.
- Felhasználói csoport hozzáadásával az IoT hub készen áll az adathozzáférésre.
- Hozzon létre egy Stream Analytics feladatot, és konfigurálja a következő feladatot:
  - Az IoT hub hőmérséklet-és páratartalom-adatainak olvasása.
  - Hívja meg a webszolgáltatást az eső véletlen eléréséhez.
  - Mentse az eredményt egy Azure Blob Storage-tárolóba.
- A Microsoft Azure Storage Explorer használatával megtekintheti az időjárás-előrejelzést.

## <a name="what-you-need"></a>Amire szükség lesz

- Fejezze be a [málna PI online szimulátor](iot-hub-raspberry-pi-web-simulator-get-started.md) oktatóanyagát vagy az eszköz egyik oktatóanyagát; például a [málna PI és a node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Ezek az alábbi követelményekre vonatkoznak:
  - Aktív Azure-előfizetés.
  - Az előfizetéshez tartozó Azure IoT hub.
  - Egy ügyfélalkalmazás, amely üzeneteket küld az Azure IoT hub-nak.
- Egy [Azure Machine learning Studio (klasszikus)](https://studio.azureml.net/) fiók.
- Egy [Azure Storage-fiók](../storage/common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-storage-accounts), egy **általános célú v2-** fiók használata javasolt, de az Azure Blob Storage-t támogató Azure Storage-fiókok is működni fognak.

> [!Note]
> Ez a cikk Azure Stream Analytics és számos más fizetős szolgáltatást használ. Az adatoknak az Azure-régiókban való átadásakor a Azure Stream Analytics további díjakat számítunk fel. Ezért jó lenne gondoskodni arról, hogy az erőforráscsoport, a IoT Hub és az Azure Storage-fiók, valamint az oktatóanyagban később hozzáadott Machine Learning Studio (klasszikus) munkaterület és Azure Stream Analytics-feladatok is elérhetők legyenek, amelyek ugyanabban az Azure-régióban találhatók. Az [Azure-termékek rendelkezésre állása régiónként lapon](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-studio&regions=all)a Azure Machine learning Studio (klasszikus) és más Azure-szolgáltatások regionális támogatását is megtekintheti.

## <a name="deploy-the-weather-prediction-model-as-a-web-service"></a>Az időjárási előrejelzési modell üzembe helyezése webszolgáltatásként

Ebben a szakaszban az Azure AI-könyvtár időjárás-előrejelző modelljét kapja meg. Ezután hozzáadhat egy R-script modult a modellhez a hőmérséklet és a páratartalom-érték tisztításához. Végül pedig prediktív webszolgáltatásként helyezi üzembe a modellt.

### <a name="get-the-weather-prediction-model"></a>Időjárás-előrejelző modell lekérése

Ebben a szakaszban megtekintheti a Azure AI Gallery időjárási előrejelzési modelljét, és megnyithatja Azure Machine Learning Studio (klasszikus).

1. Ugrás az [időjárás-előrejelző modell oldalára](https://gallery.cortanaintelligence.com/Experiment/Weather-prediction-model-1).

   ![Az időjárás-előrejelző modell oldalának megnyitása Azure AI Gallery](media/iot-hub-weather-forecast-machine-learning/weather-prediction-model-in-azure-ai-gallery.png)

1. Válassza a **Megnyitás a Studióban (klasszikus)** lehetőséget a modell megnyitásához Microsoft Azure Machine learning Studio (klasszikus). Válasszon ki egy régiót a IoT hub közelében, és a megfelelő munkaterületet a **másolási kísérletből** a katalógus előugró ablakában.

   ![Az időjárási előrejelzési modell megnyitása Azure Machine Learning Studio (klasszikus)](media/iot-hub-weather-forecast-machine-learning/open-ml-studio.png)

### <a name="add-an-r-script-module-to-clean-temperature-and-humidity-data"></a>R-script modul hozzáadása a hőmérséklet és a páratartalom értékének tisztításához

Ahhoz, hogy a modell megfelelően viselkedjen, a hőmérséklet-és páratartalom-adatnak a numerikus adatértékekre kell átállnia. Ebben a szakaszban egy R-script modult ad hozzá az időjárási előrejelzési modellhez, amely eltávolítja azokat a sorokat, amelyek hőmérséklete vagy nedvességtartalma nem alakítható át numerikus értékekre.

1. A Azure Machine Learning Studio (klasszikus) ablak bal oldalán kattintson a nyílra az eszközök panel kibontásához. Írja be a "végrehajtás" kifejezést a keresőmezőbe. Válassza az **R-parancsfájl végrehajtása** modult.

   ![Válassza az R-parancsfájl végrehajtása modult](media/iot-hub-weather-forecast-machine-learning/select-r-script-module.png)

1. Húzza az **r-szkript végrehajtása** modult a **tiszta hiányzó** adatmodulhoz, és a meglévő **r-szkript végrehajtása** modult a diagramon. Törölje a kapcsolatot a **tiszta hiányzó adatok** és az **R-szkriptek végrehajtása** modulok között, majd az ábrán látható módon kapcsolja össze az új modul bemeneteit és kimeneteit.

   ![R-szkript végrehajtásához tartozó modul hozzáadása](media/iot-hub-weather-forecast-machine-learning/add-r-script-module.png)

1. A Tulajdonságok ablak megnyitásához válassza az új **végrehajtási R-parancsfájl** modult. Másolja és illessze be az alábbi kódot az **R-szkript** mezőbe.

   ```r
   # Map 1-based optional input ports to variables
   data <- maml.mapInputPort(1) # class: data.frame

   data$temperature <- as.numeric(as.character(data$temperature))
   data$humidity <- as.numeric(as.character(data$humidity))

   completedata <- data[complete.cases(data), ]

   maml.mapOutputPort('completedata')

   ```

   Ha elkészült, a Tulajdonságok ablakban a következőhöz hasonlóan kell kinéznie:

   ![Kód hozzáadása az R-parancsfájl-modul végrehajtásához](media/iot-hub-weather-forecast-machine-learning/add-code-to-module.png)

### <a name="deploy-predictive-web-service"></a>Prediktív webszolgáltatás üzembe helyezése

Ebben a szakaszban érvényesíti a modellt, állítson be egy prediktív webszolgáltatást a modell alapján, majd telepítse a webszolgáltatást.

1. Válassza a **Futtatás** lehetőséget a modell lépéseinek ellenőrzéséhez. Ez a lépés több percet is igénybe vehet.

   ![Futtassa a kísérletet a lépések ellenőrzéséhez](media/iot-hub-weather-forecast-machine-learning/run-experiment.png)

1. Válassza a **webszolgáltatás**  >  **prediktív webszolgáltatás** beállítása lehetőséget. Megnyílik a prediktív kísérlet diagramja.

   ![Az időjárási előrejelzési modell üzembe helyezése Azure Machine Learning Studio (klasszikus)](media/iot-hub-weather-forecast-machine-learning/predictive-experiment.png)

1. A prediktív kísérlet diagramon törölje a kapcsolatot a **webszolgáltatás bemeneti** modulja és a felső **adatkészletben lévő oszlopok kijelölése** között. Ezután húzza a **webszolgáltatás bemeneti** modulját valahol a **pontszám modell** modul közelében, és kapcsolódjon az ábrán látható módon:

   ![Két modul összekötése Azure Machine Learning Studio (klasszikus)](media/iot-hub-weather-forecast-machine-learning/connect-modules-azure-machine-learning-studio.png)

1. Válassza a **Futtatás** lehetőséget a modell lépéseinek ellenőrzéséhez.

1. Válassza a **webszolgáltatás üzembe** helyezése lehetőséget a modell webszolgáltatásként való üzembe helyezéséhez.

1. A modell irányítópultján töltse le a **kérelem/válasz** **Excel 2010 vagy korábbi munkafüzetét** .

   > [!Note]
   > Ügyeljen arra, hogy az **excel 2010-as vagy korábbi munkafüzetét** akkor is letöltse, ha az Excel újabb verzióját futtatja a számítógépen.

   ![Az Excel letöltése a kérelem VÁLASZának végpontja számára](media/iot-hub-weather-forecast-machine-learning/download-workbook.png)

1. Nyissa meg az Excel-munkafüzetet, jegyezze fel a **webszolgáltatás URL-címét** és a **hozzáférési kulcsot**.

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Stream Analytics-feladatok létrehozása, konfigurálása és futtatása

### <a name="create-a-stream-analytics-job"></a>Stream Analytics-feladat létrehozása

1. A [Azure Portal](https://portal.azure.com/)válassza az **erőforrás létrehozása** lehetőséget. Írja be a "stream Analytics-feladatok" kifejezést a keresőmezőbe, majd válassza ki **stream Analytics feladatot** az eredmények legördülő listából. Amikor megnyílik a **stream Analytics feladatok** ablaktábla, válassza a **Létrehozás** lehetőséget.
1. Adja meg a feladat alábbi adatait.

   **Feladat neve**: A feladat neve. A névnek globálisan egyedinek kell lennie.

   **Előfizetés**: válassza ki az előfizetését, ha az eltér az alapértelmezetttől.

   **Erőforráscsoport**: használja ugyanazt az erőforráscsoportot, amelyet az IoT hub használ.

   **Hely**: használja ugyanazt a helyet, mint az erőforráscsoport.

   Hagyja meg az összes többi mezőt az alapértelmezett értéken.

   ![Stream Analytics-feladatok létrehozása az Azure-ban](media/iot-hub-weather-forecast-machine-learning/create-stream-analytics-job.png)

1. Válassza a **Létrehozás** lehetőséget.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Bemenet hozzáadása a Stream Analytics-feladathoz

1. Nyissa meg a Stream Analytics feladatot.
1. A **Feladattopológia** területen válassza a **Bemenetek** lehetőséget.
1. A **bemenetek** ablaktáblán válassza a **stream-bemenet hozzáadása** lehetőséget, majd a legördülő listából válassza a **IoT hub** lehetőséget. Az **új beviteli** panelen válassza a **Select IoT hub az előfizetések** közül, és adja meg a következő adatokat:

   **Bemeneti alias**: a bemenet egyedi aliasa.

   **Előfizetés**: válassza ki az előfizetését, ha az eltér az alapértelmezetttől.

   **IoT hub**: válassza ki az IoT hubot az előfizetésből.

   **Megosztott elérési házirend neve**: válassza a  **szolgáltatás** elemet. (A **iothubowner** is használhatja.)

   **Fogyasztói csoport**: válassza ki a létrehozott fogyasztói csoportot.

   Hagyja meg az összes többi mezőt az alapértelmezett értéken.

   ![Bemenet hozzáadása a Stream Analytics feladathoz az Azure-ban](media/iot-hub-weather-forecast-machine-learning/add-input-stream-analytics-job.png)

1. Kattintson a **Mentés** gombra.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Kimenet hozzáadása a Stream Analytics-feladathoz

1. A **Feladattopológia** területen válassza a **Kimenetek** lehetőséget.
1. A **kimenetek** ablaktáblán válassza a **Hozzáadás** lehetőséget, majd válassza a **blob Storage/Data Lake Storage** elemet a legördülő listából. Az **új kimenet** ablaktáblán válassza ki a **tároló kiválasztása az előfizetések** közül, és adja meg a következő adatokat:

   **Kimeneti áljel**: A kimenet egyedi áljele.

   **Előfizetés**: válassza ki az előfizetését, ha az eltér az alapértelmezetttől.

   **Storage-fiók**: a blob Storage-hoz tartozó Storage-fiók. Hozzon létre egy Storage-fiókot, vagy használjon egy meglévőt.

   **Container (tároló**): az a tároló, amelyben a blob mentve van. Létrehozhat egy tárolót, vagy használhat egy meglévőt is.

   **Esemény szerializálási formátuma**: válassza a **CSV** lehetőséget.

   ![Kimenet hozzáadása a Stream Analytics feladatokhoz az Azure-ban](media/iot-hub-weather-forecast-machine-learning/add-output-stream-analytics-job.png)

1. Kattintson a **Mentés** gombra.

### <a name="add-a-function-to-the-stream-analytics-job-to-call-the-web-service-you-deployed"></a>Függvény hozzáadása a Stream Analytics feladathoz a telepített webszolgáltatás meghívásához

1. A **feladatok topológiája** területen válassza a **függvények** lehetőséget.
1. A **függvények** ablaktáblán válassza a **Hozzáadás** lehetőséget, majd válassza ki az **Azure ml Studio** elemet a legördülő listából. (Ügyeljen arra, hogy az Azure **ml Studio** és ne az **Azure ml szolgáltatás** legyen kiválasztva.) Az **új függvény** ablaktáblán válassza a **Azure Machine Learningi függvény beállításainak manuális** megadása lehetőséget, és adja meg a következő adatokat:

   **Függvény aliasa**: ENTER `machinelearning` .

   **URL**: adja meg az Excel-munkafüzetből lejegyzett webszolgáltatás URL-címét.

   **Kulcs**: adja meg az Excel-munkafüzetből lejegyzett hozzáférési kulcsot.

   ![Függvény hozzáadása a Stream Analytics feladathoz az Azure-ban](media/iot-hub-weather-forecast-machine-learning/add-function-stream-analytics-job.png)

1. Kattintson a **Mentés** gombra.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>A Stream Analytics-feladat lekérdezésének konfigurálása

1. A **Feladattopológia** területen válassza a **Lekérdezés** lehetőséget.
1. Cserélje le a meglévő kódot az alábbira:

   ```sql
   WITH machinelearning AS (
      SELECT EventEnqueuedUtcTime, temperature, humidity, machinelearning(temperature, humidity) as result from [YourInputAlias]
   )
   Select System.Timestamp time, CAST (result.[temperature] AS FLOAT) AS temperature, CAST (result.[humidity] AS FLOAT) AS humidity, CAST (result.[scored probabilities] AS FLOAT ) AS 'probabalities of rain'
   Into [YourOutputAlias]
   From machinelearning
   ```

   A `[YourInputAlias]` elemet cserélje le a feladat bemeneti áljelére.

   A `[YourOutputAlias]` elemet cserélje le a feladat kimeneti áljelére.

1. Válassza a **lekérdezés mentése** lehetőséget.

> [!Note]
> Ha a **lekérdezés tesztelése** lehetőséget választja, a következő üzenet jelenik meg: a lekérdezés tesztelése Machine learning függvényekkel nem támogatott. Módosítsa a lekérdezést, és próbálkozzon újra. Nyugodtan figyelmen kívül hagyhatja ezt az üzenetet, és az **OK** gombra kattintva zárhatja be az üzenet mezőjét. Mielőtt továbblép a következő szakaszra, győződjön meg róla, hogy menti a lekérdezést.

### <a name="run-the-stream-analytics-job"></a>Stream Analytics-feladat futtatása

A Stream Analyticsi feladatokban válassza az **Áttekintés** lehetőséget a bal oldali ablaktáblán. Ezután válassza az **Indítás**  >  **most**  >  **Indítás** lehetőséget. Ha a feladat sikeresen elindult, a feladat állapota **Leállítva** értékről **Fut** értékre változik.

![Stream Analytics-feladat futtatása](media/iot-hub-weather-forecast-machine-learning/run-stream-analytics-job.png)

## <a name="use-microsoft-azure-storage-explorer-to-view-the-weather-forecast"></a>A Microsoft Azure Storage Explorer használatával megtekintheti az időjárás-előrejelzést

Futtassa az ügyfélalkalmazás a hőmérséklet és a páratartalom adatok IoT hubhoz való gyűjtésének és küldésének megkezdéséhez. Az IoT hub által fogadott összes üzenet esetében a Stream Analytics-feladatokban meghívja az időjárás-előrejelzési webszolgáltatást, hogy a rendszer az eső esélyt hozza létre. Az eredmény ezután az Azure Blob Storage-ba lesz mentve. Azure Storage Explorer egy eszköz, amellyel megtekintheti az eredményt.

1. [Microsoft Azure Storage Explorer letöltése és telepítése](https://storageexplorer.com/).
1. Nyissa meg Azure Storage Explorer.
1. Jelentkezzen be Azure-fiókjába.
1. Válassza ki előfizetését.
1. Válassza ki az előfizetését > **Storage** -fiókokat, > a Storage-fiókot, > **blob-tárolókat** > a tárolót.
1. Töltsön le egy. csv-fájlt, és tekintse meg az eredményt. Az utolsó oszlop az eső esélyét rögzíti.

   ![Időjárás-előrejelzési eredmény beolvasása Azure Machine Learning Studio (klasszikus)](media/iot-hub-weather-forecast-machine-learning/weather-forecast-result.png)

## <a name="summary"></a>Összefoglalás

Sikeresen használta a Azure Machine Learning Studio (klasszikus) szolgáltatást, hogy az IoT hub hőmérséklet-és páratartalom-adatai alapján előkészítse az eső esélyét.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]