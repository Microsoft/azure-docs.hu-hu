---
title: Rövid útmutató – Stream Analytics-feladatok létrehozása a Azure Portal használatával
description: Ez az útmutató a Stream Analytics-feladatok létrehozásának első lépéseit mutatja be, és segítségével megtanulhatja beállítani a bemeneteket és kimeneteket, és létrehozni egy lekérdezést.
author: enkrumah
ms.author: ebnkruma
ms.date: 06/21/2019
ms.topic: quickstart
ms.service: stream-analytics
ms.custom: mvc
ms.openlocfilehash: c585cedce677599528e036525dead13a85c8b102
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98012376"
---
# <a name="quickstart-create-a-stream-analytics-job-by-using-the-azure-portal"></a>Útmutató: Stream Analytics-feladat létrehozása az Azure Portal használatával

Az útmutató azt mutatja be, hogy hogyan kezdhet hozzá egy Stream Analytics-feladat létrehozásához. Ebben a rövid útmutatóban egy Stream Analytics feladatot határoz meg, amely a valós idejű adatfolyam-adatok olvasását és a 27-nél nagyobb hőmérsékletű üzeneteket szűri. A Stream Analytics-feladata beolvassa az adatok IoT Hubból való beolvasását, az adatok átalakítását és az adatoknak a blob Storage tárolóba való visszaírását. Az ebben a rövid útmutatóban használt bemeneti adatokat egy málna PI online szimulátor hozza létre. 

## <a name="before-you-begin"></a>Előkészületek

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).

* Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="prepare-the-input-data"></a>A bemeneti adatok előkészítése

A Stream Analytics feladatának meghatározása előtt elő kell készítenie a bemeneti adatokat. A valós idejű érzékelők adatai bekerülnek a IoT Hubba, amelyek később a feladatnak megfelelően vannak konfigurálva. A feladathoz szükséges bemeneti adatok előkészítéséhez végezze el a következő lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Válassza **az erőforrás létrehozása**  >  **eszközök internetes hálózata**  >  **IoT hub** lehetőséget.

3. A **IoT hub** ablaktáblán adja meg a következő adatokat:
   
   |**Beállítás**  |**Ajánlott érték**  |**Leírás**  |
   |---------|---------|---------|
   |Előfizetés  | \<Your subscription\> |  Válassza ki a használni kívánt Azure-előfizetést. |
   |Erőforráscsoport   |   asaquickstart-resourcegroup  |   Válassza az **Új létrehozása** elemet, majd adja meg a fiók új erőforráscsoport-nevét. |
   |Region  |  \<Select the region that is closest to your users\> | Válassza ki azt a földrajzi helyet, ahol a IoT Hub üzemeltetheti. Használja a felhasználókhoz legközelebb eső helyet. |
   |IoT Hub neve  | MyASAIoTHub  |   Válassza ki a IoT Hub nevét.   |

   ![IoT Hub létrehozása](./media/stream-analytics-quick-create-portal/create-iot-hub.png)

4. Válassza **a Next (tovább) lehetőséget: a méret és a skála beállítása**.

5. Válasszon **tarifacsomag és méretet**. Ebben a rövid útmutatóban válassza az **F1 – ingyenes** szintet, ha az előfizetése továbbra is elérhető. További információ: [IoT hub díjszabása](https://azure.microsoft.com/pricing/details/iot-hub/).

   ![A IoT Hub mérete és méretezése](./media/stream-analytics-quick-create-portal/iot-hub-size-and-scale.png)

6. Válassza az **Áttekintés + létrehozás** lehetőséget. Tekintse át IoT Hub adatait, és kattintson a **Létrehozás** gombra. A IoT Hub létrehozása néhány percet is igénybe vehet. A létrehozás folyamatát az **Értesítések** panelen követheti nyomon.

7. A IoT Hub navigációs menüjében kattintson a **Hozzáadás** elemre a **IoT eszközök** alatt. Adja meg az **eszköz azonosítóját** , és kattintson a **Mentés** gombra.

   ![Eszköz hozzáadása a IoT Hub](./media/stream-analytics-quick-create-portal/add-device-iot-hub.png)

8. Az eszköz létrehozása után nyissa meg az eszközt a **IoT-eszközök** listából. Másolja a **kapcsolódási sztringet – az elsődleges kulcsot** , és mentse egy Jegyzettömbbe, hogy később használhassa.

   ![IoT Hub eszköz-kapcsolatok karakterláncának másolása](./media/stream-analytics-quick-create-portal/save-iot-device-connection-string.png)

## <a name="create-blob-storage"></a>BLOB Storage létrehozása

1. A Azure Portal bal felső sarkában válassza az **erőforrás létrehozása**  >  **Storage**-  >  **fiók** lehetőséget.

2. A **Storage-fiók létrehozása** panelen adja meg a Storage-fiók nevét, helyét és az erőforráscsoportot. Válassza ki ugyanazt a helyet és erőforráscsoportot, mint a létrehozott IoT Hub. Ezután kattintson a **felülvizsgálat + létrehozás** gombra a fiók létrehozásához.

   ![Storage-fiók létrehozása](./media/stream-analytics-quick-create-portal/create-storage-account.png)

3. Miután létrehozta a Storage-fiókot, válassza a **Blobok** csempét az **Áttekintés** ablaktáblán.

   ![Tárfiókok áttekintése](./media/stream-analytics-quick-create-portal/blob-storage.png)

4. A **blob szolgáltatás** lapon válassza a **tároló** lehetőséget, és adja meg a tároló nevét (például *container1*). Hagyja meg a **nyilvános hozzáférési szintet** **magánjellegűként (névtelen hozzáférés nélkül)** , majd kattintson **az OK gombra**.

   ![Blobtároló létrehozása](./media/stream-analytics-quick-create-portal/create-blob-container.png)

## <a name="create-a-stream-analytics-job"></a>Stream Analytics-feladat létrehozása

1. Jelentkezzen be az Azure Portalra.

2. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.  

3. Válassza az **elemzési**  >  **stream Analytics feladatot** az eredmények listából.  

4. Töltse ki a Stream Analytics-feladat paneljét a következő információkkal:

   |**Beállítás**  |**Ajánlott érték**  |**Leírás**  |
   |---------|---------|---------|
   |Feladat neve   |  MyASAJob   |   Adjon meg egy nevet a Stream Analytics-feladat azonosításához. A Stream Analytics-feladat neve csak alfanumerikus karaktereket, kötőjeleket és aláhúzásjeleket tartalmazhat, és legalább 3, de legfeljebb 63 karakter hosszúságú lehet. |
   |Előfizetés  | \<Your subscription\> |  Válassza ki a feladathoz használni kívánt Azure-előfizetést. |
   |Erőforráscsoport   |   asaquickstart-resourcegroup  |   Válassza ki ugyanazt az erőforráscsoportot, mint a IoT Hub. |
   |Hely  |  \<Select the region that is closest to your users\> | Válassza ki azt a földrajzi helyet, ahol a Stream Analytics-feladatot üzemeltetni szeretné. A nagyobb teljesítmény és az adatátviteli díjak csökkentése érdekében válassza a felhasználóihoz legközelebb eső helyet. |
   |Streamelési egységek  | 1  |   A Streamelési egységek azoknak a számítási erőforrásoknak felelnek meg, amelyek a feladat futtatásához szükségesek. Ez az érték alapértelmezés szerint 1. A streamelési egységek skálázásával kapcsolatos további tudnivalókért olvassa el a [Skálázás streamelési egységekkel](stream-analytics-streaming-unit-consumption.md) című cikket.   |
   |Üzemeltetési környezet  |  Felhőbeli  |   A Stream Analytics-feladatok a felhőben vagy a peremhálózaton is üzembe helyezhetők. A felhő lehetővé teszi az Azure-felhőbe való üzembe helyezést, az Edge pedig lehetővé teszi, hogy IoT Edge eszközre telepítsen. |

   ![Feladat létrehozása](./media/stream-analytics-quick-create-portal/create-asa-job.png)

5. Ha a feladatot egy irányítópulton szeretné elhelyezni, válassza ki a **Rögzítés az irányítópulton** jelölőnégyzetet, ezután pedig válassza a **Létrehozás** lehetőséget.  

6. Az *üzembe helyezés folyamatban...* értesítés jelenik meg a böngészőablak jobb felső sarkában. 

## <a name="configure-job-input"></a>Feladatbemenet konfigurálása

Ebben a szakaszban egy IoT Hub eszköz bemenetét fogja konfigurálni a Stream Analytics feladathoz. Használja a rövid útmutató előző szakaszában létrehozott IoT Hub.

1. Keresse meg a Stream Analytics-feladatot.  

2. Válassza a **bemenetek**  >  **hozzáadása stream bemeneti**  >  **IoT hub** lehetőséget.  

3. Töltse ki a **IoT hub** oldalt a következő értékekkel:

   |**Beállítás**  |**Ajánlott érték**  |**Leírás**  |
   |---------|---------|---------|
   |Bemeneti alias  |  IoTHubInput   |  Adja meg a feladat bemenetének azonosító nevét.   |
   |Előfizetés   |  \<Your subscription\> |  Válassza ki azt az Azure-előfizetést, amelyhez a létrehozott tárfiók tartozik. A tárfiók tartozhat ugyanahhoz az előfizetéshez, de akár egy másik előfizetéshez is. A példa azt feltételezi, hogy a tárfiók ugyanahhoz az előfizetéshez tartozik. |
   |IoT Hub  |  MyASAIoTHub |  Adja meg az előző szakaszban létrehozott IoT Hub nevét. |

4. Az egyéb beállításokat hagyja alapértelmezett értéken, és válassza a **Mentés** lehetőséget a beállítások mentéséhez.  

   ![A bemeneti adatok konfigurálása](./media/stream-analytics-quick-create-portal/configure-asa-input.png)
 
## <a name="configure-job-output"></a>Feladatkimenet konfigurálása

1. Keresse meg a korábban létrehozott Stream Analytics-feladatot.  

2. Válassza a **kimenetek**  >    >  **blob Storage** hozzáadása elemet.  

3. A **Blobtároló** oldalon adja meg a következő értékeket:

   |**Beállítás**  |**Ajánlott érték**  |**Leírás**  |
   |---------|---------|---------|
   |Kimeneti alias |   BlobOutput   |   Adja meg a feladat kimenetének azonosító nevét. |
   |Előfizetés  |  \<Your subscription\>  |  Válassza ki azt az Azure-előfizetést, amelyhez a létrehozott tárfiók tartozik. A tárfiók tartozhat ugyanahhoz az előfizetéshez, de akár egy másik előfizetéshez is. A példa azt feltételezi, hogy a tárfiók ugyanahhoz az előfizetéshez tartozik. |
   |A(z) |  asaquickstartstorage |   Válassza ki vagy adja meg a tárfiók nevét. Ha a tárfiókok ugyanahhoz az előfizetéshez tartoznak, a rendszer automatikusan észleli a nevüket.       |
   |Tároló |   container1  |  Válassza ki a tárfiókjában létrehozott meglévő tárolót.   |

4. Az egyéb beállításokat hagyja alapértelmezett értéken, és válassza a **Mentés** lehetőséget a beállítások mentéséhez.  

   ![Kimenet konfigurálása](./media/stream-analytics-quick-create-portal/configure-asa-output.png)
 
## <a name="define-the-transformation-query"></a>A transzformációs lekérdezés definiálása

1. Keresse meg a korábban létrehozott Stream Analytics-feladatot.  

2. Válassza ki a **Lekérdezés** lehetőséget, és frissítse a lekérdezés adatait a következő módon:  

   ```sql
   SELECT *
   INTO BlobOutput
   FROM IoTHubInput
   HAVING Temperature > 27
   ```

3. Ebben a példában a lekérdezés beolvassa az adatokat a IoT Hubból, és átmásolja egy új fájlba a blobban. Kattintson a **Mentés** gombra.  

   ![A transzformáció konfigurálása](./media/stream-analytics-quick-create-portal/add-asa-query.png)

## <a name="run-the-iot-simulator"></a>A IoT Simulator futtatása

1. Nyissa meg a [málna PI Azure IoT online szimulátort](https://azure-samples.github.io/raspberry-pi-web-simulator/).

2. A 15. sorban található helyőrzőt cserélje le az Azure IoT Hub Device-kapcsolatok karakterláncára, amelyet egy korábbi szakaszban mentett.

3. Kattintson a **Futtatás** elemre. A kimenetnek meg kell jelenítenie az érzékelő adatait és a IoT Hub küldött üzeneteket.

   ![Online Raspberry Pi Azure IoT-szimulátor](./media/stream-analytics-quick-create-portal/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>A Stream Analytics-feladat indítása és a kimenet ellenőrzése

1. Térjen vissza a feladat áttekintési oldalára, és válassza az **Indítás** lehetőséget.

2. A **kezdési feladatnál** válassza a **most** lehetőséget a **feladatok kimenetének kezdési ideje** mezőhöz. Ezután válassza az **Indítás** lehetőséget a feladatok elindításához.

3. Néhány perc elteltével keresse meg a portálon azt a tárfiókot és tárolót, amelyet a feladat kimeneteként beállított. Mostanra a kimeneti fájl megjelent a tárolóban. Az első indítás után pár percbe telhet, amíg a feladat elindul, de ezt követően mindig futni fog, amint adatok érkeznek.  

   ![A transzformált adatok](./media/stream-analytics-quick-create-portal/check-asa-results.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, a Stream Analytics feladatot és az összes kapcsolódó erőforrást. A feladat törlésével megakadályozhatja, hogy a feladat által felhasznált streamelési egységek kiszámlázásra kerüljenek. Ha a feladatot a jövőben is szeretné használni, leállíthatja, és később újraindíthatja amikor ismét szükség van rá. Ha már nem használja a feladatot, akkor a következő lépésekkel az útmutatóban létrehozott összes erőforrást törölheti:

1. Az Azure Portal bal oldali menüjében válassza az **Erőforráscsoportok** menüpontot, majd válassza ki a létrehozott erőforrás nevét.  

2. Az erőforráscsoport lapján válassza a **Törlés** elemet, írja be a törölni kívánt erőforrás nevét a szövegmezőbe, majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy egyszerű Stream Analytics-feladatot helyezett üzembe az Azure Portallal. Stream Analytics feladatokat a [PowerShell](stream-analytics-quick-create-powershell.md), a [Visual Studio](stream-analytics-quick-create-vs.md)és a [Visual Studio Code](quick-create-visual-studio-code.md)használatával is üzembe helyezhet.

Az egyéb bemeneti források beállításával és a valós idejű észlelés végrehajtásával kapcsolatos információkért olvassa el az alábbi cikkeket:

> [!div class="nextstepaction"]
> [Valós idejű csalásészlelés az Azure Stream Analytics használatával](stream-analytics-real-time-fraud-detection.md)
