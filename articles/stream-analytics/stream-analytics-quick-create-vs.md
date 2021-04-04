---
title: Rövid útmutató – Azure Stream Analytics-feladatok létrehozása a Visual Studióval
description: Ez az útmutató a Stream Analytics-feladatok létrehozásának első lépéseit mutatja be, és segítségével megtanulhatja beállítani a bemeneteket és kimeneteket, és létrehozni egy lekérdezést a Visual Studio használatával.
author: enkrumah
ms.author: ebnkruma
ms.date: 06/11/2019
ms.topic: quickstart
ms.service: stream-analytics
ms.openlocfilehash: 7a02d0914ae111d6e2e00493fe27613e27d836b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98012359"
---
# <a name="quickstart-create-an-azure-stream-analytics-job-by-using-visual-studio"></a>Rövid útmutató: Azure Stream Analytics-feladatok létrehozása a Visual Studio használatával

Ez a rövid útmutató bemutatja, hogyan hozhat létre és futtathat egy Stream Analytics-feladatot a Visual Studio Azure Stream Analytics-eszközeivel. A példában szereplő művelet beolvassa az adatfolyam-adatokat egy IoT Hub eszközről. Olyan feladatot határozhat meg, amely több mint 27 °-ban kiszámítja az átlagos hőmérsékletet, és az eredményül kapott kimeneti eseményeket egy új fájlba írja a blob Storage-ban.

> [!NOTE]
> A Visual Studio és a Visual Studio Code-eszközök nem támogatják a Kelet-Kína, Észak-Kína, Közép-Németország és Németország északkeleti régiójában feladatait.

## <a name="before-you-begin"></a>Előkészületek

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).

* Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

* Telepítse a Visual Studio 2019, a Visual Studio 2015 vagy a Visual Studio 2013 Update 4 frissítést. Az Enterprise (Ultimate/Premium), Professional és Community kiadások mind támogatottak. Az Express kiadás nem támogatott.

* A Visual Studio Stream Analytics eszközeinek telepítéséhez kövesse a [telepítési utasításokat](./stream-analytics-tools-for-visual-studio-install.md).

## <a name="prepare-the-input-data"></a>A bemeneti adatok előkészítése

A Stream Analytics-feladatnak a meghatározása előtt elő kell készítenie az adatokat, amelyeket később a feladatként megadott bemenetként kell konfigurálni. A feladathoz szükséges bemeneti adatok előkészítéséhez végezze el a következő lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Válassza **az erőforrás létrehozása**  >  **eszközök internetes hálózata**  >  **IoT hub** lehetőséget.

3. A **IoT hub** ablaktáblán adja meg a következő adatokat:
   
   |**Beállítás**  |**Ajánlott érték**  |**Leírás**  |
   |---------|---------|---------|
   |Előfizetés  | \<Your subscription\> |  Válassza ki a használni kívánt Azure-előfizetést. |
   |Erőforráscsoport   |   asaquickstart-resourcegroup  |   Válassza az **Új létrehozása** elemet, majd adja meg a fiók új erőforráscsoport-nevét. |
   |Region  |  \<Select the region that is closest to your users\> | Válassza ki azt a földrajzi helyet, ahol a IoT Hub üzemeltetheti. Használja a felhasználókhoz legközelebb eső helyet. |
   |IoT Hub neve  | MyASAIoTHub  |   Válassza ki a IoT Hub nevét.   |

   ![IoT Hub létrehozása](./media/stream-analytics-quick-create-vs/create-iot-hub.png)

4. Válassza **a Next (tovább) lehetőséget: a méret és a skála beállítása**.

5. Válasszon **tarifacsomag és méretet**. Ebben a rövid útmutatóban válassza az **F1 – ingyenes** szintet, ha az előfizetése továbbra is elérhető. Ha az ingyenes szint nem érhető el, válassza ki az elérhető legalacsonyabb szintet. További információ: [IoT hub díjszabása](https://azure.microsoft.com/pricing/details/iot-hub/).

   ![A IoT Hub mérete és méretezése](./media/stream-analytics-quick-create-vs/iot-hub-size-and-scale.png)

6. Válassza az **Áttekintés + létrehozás** lehetőséget. Tekintse át IoT Hub adatait, és kattintson a **Létrehozás** gombra. A IoT Hub létrehozása néhány percet is igénybe vehet. A létrehozás folyamatát az **Értesítések** panelen követheti nyomon.

7. A IoT Hub navigációs menüjében kattintson a **Hozzáadás** elemre a **IoT eszközök** alatt. Adja meg az **eszköz azonosítóját** , és kattintson a **Mentés** gombra.

   ![Eszköz hozzáadása a IoT Hub](./media/stream-analytics-quick-create-vs/add-device-iot-hub.png)

8. Az eszköz létrehozása után nyissa meg az eszközt a **IoT-eszközök** listából. Másolja a **kapcsolódási sztringet – az elsődleges kulcsot** , és mentse egy Jegyzettömbbe, hogy később használhassa.

   ![IoT Hub eszköz-kapcsolatok karakterláncának másolása](./media/stream-analytics-quick-create-vs/save-iot-device-connection-string.png)

## <a name="create-blob-storage"></a>BLOB Storage létrehozása

1. A Azure Portal bal felső sarkában válassza az **erőforrás létrehozása**  >  **Storage**-  >  **fiók** lehetőséget.

2. A **Storage-fiók létrehozása** panelen adja meg a Storage-fiók nevét, helyét és az erőforráscsoportot. Válassza ki ugyanazt a helyet és erőforráscsoportot, mint a létrehozott IoT Hub. Ezután kattintson a **felülvizsgálat + létrehozás** gombra a fiók létrehozásához.

   ![Storage-fiók létrehozása](./media/stream-analytics-quick-create-portal/create-storage-account.png)

3. Miután létrehozta a Storage-fiókot, válassza a **Blobok** csempét az **Áttekintés** ablaktáblán.

   ![Tárfiókok áttekintése](./media/stream-analytics-quick-create-portal/blob-storage.png)

4. A **blob szolgáltatás** lapon válassza a **tároló** lehetőséget, és adja meg a tároló nevét (például *container1*). Hagyja meg a **nyilvános hozzáférési szintet** **magánjellegűként (névtelen hozzáférés nélkül)** , majd kattintson **az OK gombra**.

   ![Blobtároló létrehozása](./media/stream-analytics-quick-create-portal/create-blob-container.png)

## <a name="create-a-stream-analytics-project"></a>Stream Analytics-projekt létrehozása

1. Indítsa el a Visual Studiót.

2. Válassza a **File > New Project** (Fájl > Új projekt) lehetőséget.  

3. A bal oldali sablonlistában válassza ki a **Stream Analytics**, majd az **Azure Stream Analytics Application** (Azure Stream Analytics-alkalmazás) elemet.  

4. Adja meg az alkalmazás **nevét**, **helyét** és a **megoldás nevét**, majd kattintson az **OK** gombra.

   ![Stream Analytics-projekt létrehozása](./media/stream-analytics-quick-create-vs/create-stream-analytics-project.png)

Figyelje meg az Azure Stream Analytics-projektben szereplő elemeket.

   <img src="./media/stream-analytics-quick-create-vs/stream-analytics-project.png" alt="Azure Stream Analytics project elements" width="300px"/>


## <a name="choose-the-required-subscription"></a>A kívánt előfizetés kiválasztása

1. A Visual Studio **View** (Nézet) menüjében válassza a **Server Explorer** (Kiszolgálókezelő) lehetőséget.

2. Kattintson a jobb gombbal az **Azure** elemre, válassza a **Connect to Microsoft Azure Subscription** (Csatlakozás egy Microsoft Azure-előfizetéshez) lehetőséget, majd jelentkezzen be Azure-fiókjával.

## <a name="define-input"></a>Bemenet meghatározása

1. A **Solution Explorerben** (Megoldáskezelő) bontsa ki az **Inputs** (Bemenetek) csomópontot, majd kattintson duplán az **Input.json** fájlra.

2. Töltse ki a **Stream Analytics Input Configuration** (Stream Analytics bemeneti konfigurációja) panel mezőit a következő értékekkel:

   |**Beállítás**  |**Ajánlott érték**  |**Leírás**   |
   |---------|---------|---------|
   |Input Alias (Bemeneti alias)  |  Bevitel   |  Adja meg a feladat bemenetének azonosító nevét.   |
   |Source Type (Forrás típusa)   |  Data Stream (Adatstream) |  Válassza ki a megfelelő bemeneti forrást: adatstream vagy referenciaadat.   |
   |Forrás  |  IoT Hub |  Válassza ki a megfelelő bemeneti forrást.   |
   |Erőforrás  | Choose data source from current account (Adatforrás kiválasztása az aktuális fiókból) | Válassza a manuális adatbevitelt, vagy válasszon ki egy meglévő fiókot.   |
   |Előfizetés  |  \<Your subscription\>   | Válassza ki azt az Azure-előfizetést, amely a létrehozott IoT Hub rendelkezik.   |
   |IoT Hub  |  MyASAIoTHub   |  Válassza ki vagy adja meg a IoT Hub nevét. A rendszer automatikusan észleli a IoT Hub neveket, ha azok ugyanabban az előfizetésben jönnek létre.   |
   
3. Az egyéb beállításokat hagyja alapértelmezett értéken, és válassza a **Mentés** lehetőséget a beállítások mentéséhez.  

   ![A bemeneti adatok konfigurálása](./media/stream-analytics-quick-create-vs/stream-analytics-vs-input.png)

## <a name="define-output"></a>Kimenet meghatározása

1. A **Solution Explorerben** (Megoldáskezelő) bontsa ki az **Outputs** (Kimenetek) csomópontot, majd kattintson duplán az **Output.json** fájlra.

2. Töltse ki a **Stream Analytics Output Configuration** (Stream Analytics kimeneti konfigurációja) panel mezőit a következő értékekkel:

   |**Beállítás**  |**Ajánlott érték**  |**Leírás**   |
   |---------|---------|---------|
   |Output Alias (Kimeneti alias)  |  Kimenet   |  Adja meg a feladat kimenetének azonosító nevét.   |
   |Sink (Fogadó)   |  Blob Storage |  Válassza ki a megfelelő fogadót.    |
   |Erőforrás  |  Provide data source settings manually (Az adatforrás-beállítások manuális megadása) |  Válassza a manuális adatbevitelt, vagy válasszon ki egy meglévő fiókot.   |
   |Előfizetés  |  \<Your subscription\>   | Válassza ki azt az Azure-előfizetést, amelyhez a létrehozott tárfiók tartozik. A tárfiók tartozhat ugyanahhoz az előfizetéshez, de akár egy másik előfizetéshez is. A példa azt feltételezi, hogy a tárfiók ugyanahhoz az előfizetéshez tartozik.   |
   |Tárfiók  |  asaquickstartstorage   |  Válassza ki vagy adja meg a tárfiók nevét. Ha a tárfiókok ugyanahhoz az előfizetéshez tartoznak, a rendszer automatikusan észleli a nevüket.   |
   |Tároló  |  container1   |  Válassza ki a tárfiókjában létrehozott meglévő tárolót.   |
   |Path Pattern (Elérésiút-minta)  |  output   |  Adja meg a tárolón belül létrehozni kívánt elérési út nevét.   |
   
3. Az egyéb beállításokat hagyja alapértelmezett értéken, és válassza a **Mentés** lehetőséget a beállítások mentéséhez.  

   ![Kimeneti adatok konfigurálása](./media/stream-analytics-quick-create-vs/stream-analytics-vs-output.png)

## <a name="define-the-transformation-query"></a>A transzformációs lekérdezés definiálása

1. Nyissa meg a **Script.asaql** fájlt a Visual Studio **Solution Explorerében** (Megoldáskezelő).

2. Adja hozzá az alábbi lekérdezést:

   ```sql
   SELECT *
   INTO Output
   FROM Input
   HAVING Temperature > 27
   ```

## <a name="submit-a-stream-analytics-query-to-azure"></a>Stream Analytics-lekérdezés elküldése az Azure-ba

1. A **Query Editor** (Lekérdezésszerkesztő) szkriptszerkesztőjében válassza a **Submit To Azure** (Küldés az Azure-ba) lehetőséget.

2. Válassza a **Create a New Azure Stream Analytics job** (Új Azure Stream Analytics-feladat létrehozása) lehetőséget, majd adja meg a **feladat nevét**. Válassza ki a rövid útmutató elején használt **Előfizetés**, **Erőforráscsoport** és **Hely** elemeket.

   ![Feladat elküldése az Azure-ba](./media/stream-analytics-quick-create-vs/stream-analytics-job-to-azure.png)

## <a name="run-the-iot-simulator"></a>A IoT Simulator futtatása

1. Nyissa meg a [málna PI Azure IoT online Simulator](https://azure-samples.github.io/raspberry-pi-web-simulator/) lapot egy új böngészőablakban vagy ablakban.

2. A 15. sorban található helyőrzőt cserélje le az Azure IoT Hub Device-kapcsolatok karakterláncára, amelyet egy korábbi szakaszban mentett.

3. Kattintson a **Futtatás** elemre. A kimenetnek meg kell jelenítenie az érzékelő adatait és a IoT Hub küldött üzeneteket.

   ![Online Raspberry Pi Azure IoT-szimulátor](./media/stream-analytics-quick-create-portal/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-output"></a>A Stream Analytics-feladat indítása és a kimenet ellenőrzése

1. A feladat létrehozása után automatikusan megnyílik a feladatnézet. A feladat indításához kattintson a zöld nyílra.

   ![Stream Analytics-feladat indítása](./media/stream-analytics-quick-create-vs/start-stream-analytics-job-vs.png)

2. Módosítsa a **feladatok kimenetének indítási módját** **JobStartTime** , és válassza az **Indítás** lehetőséget.

   ![Feladatkonfigurálás indítása](./media/stream-analytics-quick-create-vs/stream-analytics-start-configuration.png)

3. Vegye figyelembe, hogy a feladat állapota **Running** (Fut) állapotra módosult, és bemeneti/kimeneti események váltak elérhetővé. Ez eltarthat néhány percig.

   ![Stream Analytics-feladat futtatása](./media/stream-analytics-quick-create-vs/stream-analytics-job-running.png)

4. Az eredmények megtekintéséhez válassza a **View** (Nézet) menü **Cloud Explorer** (Felhőkezelő) pontját, majd keresse meg a tárfiókot a saját erőforráscsoportjában. A **Blob Containers** (Blobtárolók) területen kattintson duplán a **container1** elemre, majd a **kimeneti** fájl elérési útjára.

   ![Eredmények megtekintése](./media/stream-analytics-quick-create-vs/stream-analytics-vs-results.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség rá, törölheti az erőforráscsoportot, a folyamatos átviteli feladatot és az összes kapcsolódó erőforrást. A feladat törlésével megakadályozhatja, hogy a feladat által felhasznált streamelési egységek kiszámlázásra kerüljenek. Ha a feladatot a jövőben is szeretné használni, leállíthatja, és később újraindíthatja amikor ismét szükség van rá. Ha már nem használja a feladatot, akkor a következő lépésekkel az útmutatóban létrehozott összes erőforrást törölheti:

1. Az Azure Portal bal oldali menüjében válassza az **Erőforráscsoportok** menüpontot, majd válassza ki a létrehozott erőforrás nevét.  

2. Az erőforráscsoport lapján válassza a **Törlés** elemet, írja be a törölni kívánt erőforrás nevét a szövegmezőbe, majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy egyszerű Stream Analytics-feladatot helyezett üzembe a Visual Studióval. A Stream Analytics-feladatokat az [Azure Portal](stream-analytics-quick-create-portal.md) és a [PowerShell](stream-analytics-quick-create-powershell.md) használatával is üzembe helyezheti. 

A Visual studióhoz készült Azure Stream Analytics Tools megismeréséhez folytassa a következő cikkel:

> [!div class="nextstepaction"]
> [A Visual Studio használata Azure Stream Analytics feladatok megtekintéséhez](stream-analytics-vs-tools.md)