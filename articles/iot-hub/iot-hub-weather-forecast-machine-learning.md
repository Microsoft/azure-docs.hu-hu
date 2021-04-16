---
title: Időjárás-előrejelzés Azure Machine Learning Studio (klasszikus) használatával IoT Hub adatokkal
description: A Azure Machine Learning Studio (klasszikus) használatával előrejelezni tudja az eső esélyét az IoT Hub által az érzékelőktől gyűjtött hőmérsékleti és páratartalom-adatok alapján.
author: robinsh
manager: philmea
keywords: időjárás-előrejelzés gépi tanulás
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 09/16/2020
ms.author: robinsh
ms.openlocfilehash: 455d78ed21403952046448dd4447b5ec54f77c00
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566979"
---
# <a name="weather-forecast-using-the-sensor-data-from-your-iot-hub-in-azure-machine-learning-studio-classic"></a>Időjárás-előrejelzés az IoT Hub érzékelőinek adataival a Azure Machine Learning Studióban (klasszikus)

![Végpontok között ábrázolt diagram](media/iot-hub-get-started-e2e-diagram/6.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

A gépi tanulás az adattudomány egyik technikája, amely segít a számítógépeknek a meglévő adatokból való tanulásban a jövőbeli viselkedések, kimenetek és trendek előrejelzéséhez. Azure Machine Learning Studio (klasszikus) egy felhőalapú prediktív elemzési szolgáltatás, amely lehetővé teszi a prediktív modellek elemzési megoldásként való gyors létrehozását és üzembe helyezését. Ebből a cikkből megtudhatja, hogyan használhatja a Azure Machine Learning Studio (klasszikus) időjárás-előrejelzést (az eső esélyét) az Azure IoT Hub hőmérséklet- és páratartalom-adataival. Az eső esélye egy előkészített időjárás-előrejelzési modell kimenete. A modell a korábbi adatokra épülve előrejelzi az eső esélyét a hőmérséklet és a páratartalom alapján.

## <a name="prerequisites"></a>Előfeltételek

- Töltse ki a [Raspberry Pi online szimulátor oktatóanyagát](iot-hub-raspberry-pi-web-simulator-get-started.md) vagy az eszköz egyik oktatóanyagát. A [Raspberry Pi-hoz ](iot-hub-raspberry-pi-kit-node-get-started.md) például használhatja a node.jsvagy a [Telemetria](quickstart-send-telemetry-dotnet.md) küldése gyorsútmutatók valamelyikét. Ezek a cikkek a következő követelményeket fedik le:
  - Aktív Azure-előfizetés.
  - Egy Azure IoT Hub az előfizetés alatt.
  - Egy ügyfélalkalmazás, amely üzeneteket küld az Azure IoT Hubnak.
- Egy [Azure Machine Learning Studio- (klasszikus)](https://studio.azureml.net/) fiók.
- Előnyben [részesítünk egy Azure Storage-fiókot](../storage/common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-storage-accounts), egy általános célú **v2-fiókot,** de minden olyan Azure Storage-fiók is működni fog, amely támogatja az Azure Blob Storage-et.

> [!Note]
> Ez a cikk Azure Stream Analytics és számos más fizetős szolgáltatást használ. További díjakat kell fizetni a Azure Stream Analytics, amikor az adatokat Azure-régiók között kell átvini. Ezért jó volna, ha az erőforráscsoport, az IoT Hub és az Azure Storage-fiók , valamint az Machine Learning Studio (klasszikus) munkaterület és az oktatóanyag későbbi része által hozzáadott Azure Stream Analytics-feladat is ugyanabban az Azure-régióban legyen. A (klasszikus) Azure Machine Learning Azure-szolgáltatások regionális támogatását az Azure termék rendelkezésre állása régiónként oldalon [ellenőrizheti.](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-studio&regions=all)

## <a name="deploy-the-weather-prediction-model-as-a-web-service"></a>Az időjárás-előrejelzési modell üzembe helyezése webszolgáltatásként

Ebben a szakaszban az időjárás-előrejelzési modellt a Azure AI le. Ezután hozzáad egy R-szkriptmodult a modellhez a hőmérséklet- és páratartalom-adatok megtisztítása érdekében. Végül üzembe helyezheti a modellt prediktív webszolgáltatásként.

### <a name="get-the-weather-prediction-model"></a>Az időjárás-előrejelzési modell lekérte

Ebben a szakaszban lekérte az időjárás-előrejelzési modellt a Azure AI Gallery, és megnyitja a Azure Machine Learning Studióban.

1. Ugrás az [időjárás-előrejelzési modell oldalára.](https://gallery.cortanaintelligence.com/Experiment/Weather-prediction-model-1)

   ![Nyissa meg az időjárás-előrejelzési modell oldalát a Azure AI Gallery](media/iot-hub-weather-forecast-machine-learning/weather-prediction-model-in-azure-ai-gallery.png)

1. Válassza **a Megnyitás a Studióban (klasszikus)** lehetőséget a modell a Microsoft Azure Machine Learning (klasszikus) megnyitásához. Válasszon ki egy régiót az IoT Hub közelében, és a megfelelő munkaterületet a Másolás **kísérlet a** katalógusból előugró ablakban.

   ![Az időjárás-előrejelzési modell megnyitása a Azure Machine Learning Studióban (klasszikus)](media/iot-hub-weather-forecast-machine-learning/open-ml-studio.png)

### <a name="add-an-r-script-module-to-clean-temperature-and-humidity-data"></a>R-szkriptmodul hozzáadása a hőmérséklet- és páratartalom-adatok megtisztítására

Ahhoz, hogy a modell megfelelően viselkedjen, a hőmérsékleti és páratartalom-adatokat numerikus adatokká kell konvertálni. Ebben a szakaszban egy R-szkript modult fog hozzáadni az időjárás-előrejelzési modellhez, amely eltávolít minden olyan sort, amely a hőmérsékletre vagy páratartalomra vonatkozó adatértékeket tartalmaz, és nem konvertálható numerikus értékekre.

1. A Azure Machine Learning Studio (klasszikus) ablakának bal oldalán kattintson a nyílra az eszközök panelének kibontásához. Írja be a "Végrehajtás" kódot a keresőmezőbe. Válassza az **R-szkript végrehajtása modult.**

   ![Válassza az R-szkript végrehajtása modul lehetőséget](media/iot-hub-weather-forecast-machine-learning/select-r-script-module.png)

1. Húzza az **R-szkript végrehajtása** modult a **Hiányzó** adatok tisztítása modul és a meglévő **R-szkript** végrehajtása modul közelében a diagramon. Törölje a Clean **Missing Data** (Hiányzó adatok törlése) és az Execute R **Script (R-szkript** végrehajtása) modul közötti kapcsolatot, majd csatlakoztassa az új modul bemenetét és kimenetét az ábrán látható módon.

   ![R-szkript végrehajtása modul hozzáadása](media/iot-hub-weather-forecast-machine-learning/add-r-script-module.png)

1. Válassza ki az **új R-szkript végrehajtása modult** a tulajdonságok ablakának megnyitásához. Másolja és illessze be az alábbi kódot az **R-szkript mezőbe.**

   ```r
   # Map 1-based optional input ports to variables
   data <- maml.mapInputPort(1) # class: data.frame

   data$temperature <- as.numeric(as.character(data$temperature))
   data$humidity <- as.numeric(as.character(data$humidity))

   completedata <- data[complete.cases(data), ]

   maml.mapOutputPort('completedata')

   ```

   Ha elkészült, a tulajdonságok ablakának az alábbihoz hasonlóan kell kinéznie:

   ![Kód hozzáadása az R-szkript végrehajtása modulhoz](media/iot-hub-weather-forecast-machine-learning/add-code-to-module.png)

### <a name="deploy-predictive-web-service"></a>Prediktív webszolgáltatás üzembe helyezése

Ebben a szakaszban ellenőrizheti a modellt, beállít egy prediktív webszolgáltatást a modell alapján, majd üzembe helyezheti a webszolgáltatást.

1. Válassza **a Futtatás** lehetőséget a modell lépései érvényesítéséhez. Ez a lépés eltarthat néhány percig.

   ![A lépések érvényesítéséhez futtassa a kísérletet](media/iot-hub-weather-forecast-machine-learning/run-experiment.png)

1. Válassza a SET UP WEB SERVICE Predictive Web Service **(WEBSZOLGÁLTATÁS**  >  **prediktív webszolgáltatásának beállítása) lehetőséget.** Megnyílik a prediktív kísérlet diagramja.

   ![Az időjárás-előrejelzési modell üzembe helyezése a Azure Machine Learning Studióban (klasszikus)](media/iot-hub-weather-forecast-machine-learning/predictive-experiment.png)

1. A prediktív kísérlet diagramon törölje a **webszolgáltatás** bemeneti modulja és a felül található **Select Columns in Dataset** (Adatkészlet oszlopainak kijelölése) modul közötti kapcsolatot. Ezután húzza a **webszolgáltatás bemeneti modulját** a **Score Model** (Modell pontozása) modulhoz, és csatlakoztassa a következő módon:

   ![Két modul csatlakoztatása a Azure Machine Learning Studióban (klasszikus)](media/iot-hub-weather-forecast-machine-learning/connect-modules-azure-machine-learning-studio.png)

1. Válassza **a FUTTATÁS** lehetőséget a modell lépései érvényesítéséhez.

1. Válassza **a DEPLOY WEB SERVICE (WEBSZOLGÁLTATÁS ÜZEMBE** HELYEZÉSE) lehetőséget a modell webszolgáltatásként való üzembe helyezéséhez.

1. A modell irányítópultján töltse le az **Excel 2010-es** vagy korábbi munkafüzetet **a REQUEST/RESPONSE számára.**

   > [!Note]
   > Akkor is töltse le az **Excel 2010-et** vagy korábbi munkafüzetet, ha az Excel egy újabb verzióját futtatja a számítógépén.

   ![Töltse le az Excelt a REQUEST RESPONSE végponthoz](media/iot-hub-weather-forecast-machine-learning/download-workbook.png)

1. Nyissa meg az Excel-munkafüzetet, jegyezze fel a **WEBSZOLGÁLTATÁS URL-címét** és **HOZZÁFÉRÉSI KULCSát.**

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Új feladat létrehozása, konfigurálása Stream Analytics futtatása

### <a name="create-a-stream-analytics-job"></a>Stream Analytics-feladat létrehozása

1. A [Azure Portal](https://portal.azure.com/)válassza az **Erőforrás létrehozása lehetőséget.** Írja be a "stream analytics job" (Stream **Analytics-feladat)** adatokat a Keresőmezőbe, és válassza Stream Analytics feladat lehetőséget az eredmények legördülő menüből. Amikor **megnyílik Stream Analytics feladat panelje,** válassza a **Létrehozás lehetőséget.**
1. Adja meg a feladat alábbi adatait.

   **Feladat neve**: A feladat neve. A névnek globálisan egyedinek kell lennie.

   **Előfizetés:** Válassza ki az előfizetését, ha az eltér az alapértelmezetttől.

   **Erőforráscsoport:** Használja ugyanazt az erőforráscsoportot, mint az IoT Hub.

   **Hely:** Használja ugyanazt a helyet, mint az erőforráscsoport.

   Hagyja meg az összes többi mező alapértelmezett beállítását.

   ![Új Stream Analytics létrehozása az Azure-ban](media/iot-hub-weather-forecast-machine-learning/create-stream-analytics-job.png)

1. Válassza a **Létrehozás** lehetőséget.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Bemenet hozzáadása a Stream Analytics-feladathoz

1. Nyissa meg a Stream Analytics feladatot.
1. A **Feladattopológia** területen válassza a **Bemenetek** lehetőséget.
1. A **Bemenetek panelen** válassza a **Streambemenet** hozzáadása lehetőséget, majd IoT Hub **a** legördülő menüből. Az Új **bemenet panelen** válassza az Előfizetések IoT Hub **lehetőséget,** és adja meg a következő adatokat:

   **Bemeneti alias:** A bemenet egyedi aliasa.

   **Előfizetés:** Válassza ki az előfizetését, ha az eltér az alapértelmezetttől.

   **IoT Hub:** Válassza ki az IoT Hubot az előfizetéséből.

   **Megosztott hozzáférési szabályzat neve:** Válassza a **szolgáltatás lehetőséget.** (Az **iothubowner** is használható.)

   **Fogyasztói csoport:** Válassza ki a létrehozott fogyasztói csoportot.

   Hagyja meg az összes többi mező alapértelmezett beállítását.

   ![Bemenet hozzáadása a Stream Analytics feladathoz az Azure-ban](media/iot-hub-weather-forecast-machine-learning/add-input-stream-analytics-job.png)

1. Kattintson a **Mentés** gombra.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Kimenet hozzáadása a Stream Analytics-feladathoz

1. A **Feladattopológia** területen válassza a **Kimenetek** lehetőséget.
1. A **Kimenetek panelen** válassza a **Hozzáadás** lehetőséget, majd a legördülő menüből Data Lake Storage **Blob Storage/Data Lake Storage** gombra. Az Új **kimenet panelen** válassza a Tároló kiválasztása **az előfizetései** közül lehetőséget, és adja meg a következő adatokat:

   **Kimeneti áljel**: A kimenet egyedi áljele.

   **Előfizetés:** Válassza ki az előfizetését, ha az eltér az alapértelmezetttől.

   **Tárfiók:** A blobtároló tárfiókja. Létrehozhat egy tárfiókot, vagy használhat egy meglévőt.

   **Tároló:** A tároló, ahová a blob mentve van. Létrehozhat egy tárolót, vagy használhat egy meglévőt.

   **Esemény szerializálási formátuma:** Válassza a **CSV lehetőséget.**

   ![Kimenet hozzáadása a Stream Analytics feladathoz az Azure-ban](media/iot-hub-weather-forecast-machine-learning/add-output-stream-analytics-job.png)

1. Kattintson a **Mentés** gombra.

### <a name="add-a-function-to-the-stream-analytics-job-to-call-the-web-service-you-deployed"></a>Függvény hozzáadása a Stream Analytics feladathoz az üzembe helyezett webszolgáltatás hívására

1. A **Feladat topológiája alatt válassza** a **Függvények lehetőséget.**
1. A **Függvények panelen** válassza a **Hozzáadás** lehetőséget, majd a legördülő **menüből** válassza az Azure ML Studio lehetőséget. (Győződjön meg arról, hogy az **Azure ML Studio**, és nem az Azure ML Service lehetőséget **választja.)** Az Új **függvény panelen** válassza a Függvénybeállítások **megadása Azure Machine Learning lehetőséget,** és adja meg a következő adatokat:

   **Függvény aliasa:** Írja be a következőt: `machinelearning` .

   **URL:** Adja meg az Excel-munkafüzetből lejegyzett WEB SERVICE URL-címet.

   **Kulcs:** Adja meg az Excel-munkafüzetből lejegyzett HOZZÁFÉRÉSI KULCSOT.

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

1. Válassza a **Lekérdezés mentése lehetőséget.**

> [!Note]
> Ha a **Lekérdezés tesztelése** lehetőséget választja, a következő üzenet jelenik meg: A lekérdezési teszt Machine Learning függvényekkel nem támogatott. Módosítsa a lekérdezést, és próbálkozzon újra. Nyugodtan figyelmen kívül hagyhatja ezt az üzenetet, és bezárhatja az **üzenetmezőt** az OK gombra választva. Mielőtt továbblépne a következő szakaszra, mentse a lekérdezést.

### <a name="run-the-stream-analytics-job"></a>Stream Analytics-feladat futtatása

A Stream Analytics bal oldali **panelen** válassza az Áttekintés lehetőséget. Ezután válassza **az Indítás**  >  **most indítás**  >  **lehetőséget.** Ha a feladat sikeresen elindult, a feladat állapota **Leállítva** értékről **Fut** értékre változik.

![Stream Analytics-feladat futtatása](media/iot-hub-weather-forecast-machine-learning/run-stream-analytics-job.png)

## <a name="use-microsoft-azure-storage-explorer-to-view-the-weather-forecast"></a>A Microsoft Azure Storage Explorer az időjárás-előrejelzés megtekintéséhez

Futtassa az ügyfélalkalmazást a hőmérsékleti és páratartalom-adatok gyűjtésének és az IoT Hubnak való elküldését. Az IoT Hub által fogadott összes üzenethez a Stream Analytics az időjárás-előrejelzési webszolgáltatást hívja meg az eső esélyének előállításához. Az eredmény ezután az Azure Blob Storage-ba lesz mentve. Azure Storage Explorer egy olyan eszköz, amely az eredmény megtekintésére használható.

1. [Töltse le és telepítse a Microsoft Azure Storage Explorer.](https://storageexplorer.com/)
1. Nyissa meg Azure Storage Explorer.
1. Jelentkezzen be Azure-fiókjába.
1. Válassza ki előfizetését.
1. Válassza ki a **Storage> fiókok előfizetését** > a tárfiókhoz, > **blobtárolókat** > a tárolóban.
1. Az eredményért töltsön le egy .csv-fájlt. Az utolsó oszlop az eső esélyét rögzíti.

   ![Időjárás-előrejelzési eredmény lekérte a Azure Machine Learning Studio (klasszikus)](media/iot-hub-weather-forecast-machine-learning/weather-forecast-result.png)

## <a name="summary"></a>Összefoglalás

Sikeresen használta a Azure Machine Learning Studiót, hogy az IoT Hub által kapott hőmérsékleti és páratartalom-adatok alapján az eső esélyét kapja meg.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]