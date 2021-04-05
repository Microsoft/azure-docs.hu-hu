---
title: Rövid útmutató – Azure Stream Analytics-feladatok létrehozása a Visual Studio Code-ban
description: Ez a rövid útmutató bemutatja, hogyan kezdheti el az első lépéseket egy Stream Analytics feladatok létrehozásával, a bemenetek és kimenetek konfigurálásával, valamint a lekérdezés a Visual Studio Code használatával történő definiálásával.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 01/18/2020
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 104dfe7b2ce253d7ae42c7484532cfd5b2993aec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98014611"
---
# <a name="quickstart-create-an-azure-stream-analytics-job-in-visual-studio-code-preview"></a>Rövid útmutató: Azure Stream Analytics-feladatok létrehozása a Visual Studio Code-ban (előzetes verzió)

Ez a rövid útmutató bemutatja, hogyan hozhat létre és futtathat egy Azure Stream Analytics feladatot a Visual Studio Code-hoz készült Azure Stream Analytics Tools bővítmény használatával. A példában szereplő művelet beolvassa az adatokat egy Azure IoT Hub-eszközről. Olyan feladatot határozhat meg, amely több mint 27 °-ban kiszámítja az átlagos hőmérsékletet, és az eredményül kapott kimeneti eseményeket egy új fájlba írja a blob Storage-ban.

> [!NOTE]
> A Visual Studio és a Visual Studio Code-eszközök nem támogatják a Kelet-Kína, Észak-Kína, Közép-Németország és Németország északkeleti régiójában feladatait.

## <a name="before-you-begin"></a>Előkészületek

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).

* Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

* Telepítse a [Visual Studio Code](https://code.visualstudio.com/)-ot.

## <a name="install-the-azure-stream-analytics-tools-extension"></a>A Azure Stream Analytics-eszközök bővítményének telepítése

1. Nyissa meg a Visual Studio Code-ot.

2. A bal oldali ablaktáblán található **bővítmények** területen keressen rá **stream Analytics** , majd válassza a **telepítés** lehetőséget a **Azure stream Analytics Tools** bővítményben.

3. A bővítmény telepítése után ellenőrizze, hogy **Azure stream Analytics eszközök** láthatók-e az **engedélyezett bővítményekben**.

   ![A Visual Studio Code-ban elérhető bővítmények Azure Stream Analytics eszközei](./media/quick-create-visual-studio-code/enabled-extensions.png)

## <a name="activate-the-azure-stream-analytics-tools-extension"></a>A Azure Stream Analytics Tools bővítmény aktiválása

1. Válassza ki az **Azure** ikont a Visual Studio Code tevékenység sávján. Az oldalsó sáv **stream Analytics** területén válassza a **Bejelentkezés az Azure**-ba lehetőséget.

   ![Bejelentkezés az Azure-ba a Visual Studio Code-ban](./media/quick-create-visual-studio-code/azure-sign-in.png)

2. Amikor bejelentkezett, az Azure-fiók neve megjelenik a Visual Studio Code ablak bal alsó sarkában található állapotsorban.

> [!NOTE]
> A Azure Stream Analytics Tools bővítmény automatikusan aláírja a következő alkalommal, ha nem kijelentkezik. Ha a fiókja kétfaktoros hitelesítést használ, javasoljuk, hogy PIN-kód helyett telefonos hitelesítést használjon.
> Ha problémák merülnek fel az erőforrások listázása során, a kijelentkezés és az újbóli bejelentkezés általában segít. A kijelentkezéshez adja meg a parancsot `Azure: Sign Out` .

## <a name="prepare-the-input-data"></a>A bemeneti adatok előkészítése

A Stream Analytics feladatának megadása előtt elő kell készítenie azokat az adatokat, amelyeket később feladatként kell konfigurálni. A feladathoz szükséges bemeneti adatok előkészítéséhez végezze el a következő lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Válassza **az erőforrás létrehozása**  >  **eszközök internetes hálózata**  >  **IoT hub** lehetőséget.

3. A **IoT hub** ablaktáblán adja meg a következő adatokat:

   |**Beállítás**  |**Ajánlott érték**  |**Leírás**  |
   |---------|---------|---------|
   |Előfizetés  | \<Your subscription\> |  Válassza ki a használni kívánt Azure-előfizetést. |
   |Erőforráscsoport   |   asaquickstart-resourcegroup  |   Válassza az **Új létrehozása** elemet, majd adja meg a fiók új erőforráscsoport-nevét. |
   |Region  |  \<Select the region that is closest to your users\> | Válassza ki azt a földrajzi helyet, ahol az IoT hubot üzemeltetni szeretné. Használja a felhasználókhoz legközelebb eső helyet. |
   |IoT Hub neve  | MyASAIoTHub  |   Válassza ki az IoT hub nevét.   |

   ![IoT Hub létrehozása](./media/quick-create-visual-studio-code/create-iot-hub.png)

4. Válassza **a Next (tovább) lehetőséget: a méret és a skála beállítása**.

5. Válassza ki a **díjszabási és méretezési szintet**. Ebben a rövid útmutatóban válassza az **F1 – ingyenes** szintet, ha az előfizetése továbbra is elérhető. Ha az ingyenes szint nem érhető el, válassza ki az elérhető legalacsonyabb szintet. További információ: [Azure IoT hub díjszabása](https://azure.microsoft.com/pricing/details/iot-hub/).

   ![Az IoT hub mérete és méretezése](./media/quick-create-visual-studio-code/iot-hub-size-and-scale.png)

6. Válassza az **Áttekintés + létrehozás** lehetőséget. Tekintse át az IoT hub adatait, és válassza a **Létrehozás** lehetőséget. Az IoT Hub létrehozása eltarthat néhány percig. Az **értesítések** ablaktáblán nyomon követheti a folyamat előrehaladását.

7. Az IoT hub navigációs menüjében válassza a **Hozzáadás** lehetőséget a **IoT-eszközök** alatt. Adja meg az **eszköz** azonosítóját, majd válassza a **Mentés** lehetőséget.

   ![Eszköz hozzáadása az IoT hub-hoz](./media/quick-create-visual-studio-code/add-device-iot-hub.png)

8. Az eszköz létrehozása után nyissa meg az eszközt a **IoT-eszközök** listából. Másolja a karakterláncot a **kapcsolódási sztringbe (elsődleges kulcs)** , és mentse egy Jegyzettömbbe, hogy később használhassa.

   ![IoT hub-eszköz kapcsolatok karakterláncának másolása](./media/quick-create-visual-studio-code/save-iot-device-connection-string.png)

## <a name="run-the-iot-simulator"></a>A IoT Simulator futtatása

1. Nyissa meg a [málna PI Azure IoT online Simulator](https://azure-samples.github.io/raspberry-pi-web-simulator/) lapot egy új böngészőablakban vagy ablakban.

2. Cserélje le a 15. sorban látható helyőrzőt a korábban mentett IoT hub-eszközhöz.

3. Válassza a **Futtatás** lehetőséget. A kimenetnek meg kell jelennie az IoT hub-ba küldendő érzékelők és üzenetek.

   ![Málna PI Azure IoT online szimulátor kimenettel](./media/quick-create-visual-studio-code/ras-pi-connection-string.png)

## <a name="create-blob-storage"></a>BLOB Storage létrehozása

1. A Azure Portal bal felső sarkában válassza az **erőforrás létrehozása**  >  **Storage**-  >  **fiók** lehetőséget.

2. A **Storage-fiók létrehozása** panelen adja meg a Storage-fiók nevét, helyét és az erőforráscsoportot. Válassza ki ugyanazt a helyet és erőforráscsoportot, mint a létrehozott IoT hubot. Ezután válassza a **felülvizsgálat + létrehozás** lehetőséget a fiók létrehozásához.

   ![Storage-fiók létrehozása](./media/quick-create-visual-studio-code/create-storage-account.png)

3. A Storage-fiók létrehozása után válassza a **Blobok** csempét az **Áttekintés** ablaktáblán.

   ![Tárfiókok áttekintése](./media/quick-create-visual-studio-code/blob-storage.png)

4. A **blob szolgáltatás** lapon válassza a **tároló** lehetőséget, és adja meg a tároló nevét (például **container1**). Hagyja a **nyilvános hozzáférési szintet** **magánjellegűként (névtelen hozzáférés nélkül)** , majd kattintson **az OK gombra**.

   ![Blobtároló létrehozása](./media/quick-create-visual-studio-code/create-blob-container.png)

## <a name="create-a-stream-analytics-project"></a>Stream Analytics-projekt létrehozása

1. A Visual Studio Code-ban válassza a **CTRL + SHIFT + P** billentyűkombinációt a parancs paletta megnyitásához. Ezután írja be az **ASA** értéket, és válassza az **ASA: új projekt létrehozása** lehetőséget.

   ![Új projekt létrehozása](./media/quick-create-visual-studio-code/create-new-project.png)

2. Adja meg a projekt nevét (például **myASAproj**), és válasszon egy mappát a projekt számára.

    ![Projekt nevének létrehozása](./media/quick-create-visual-studio-code/create-project-name.png)

3. Az új projekt hozzá lesz adva a munkaterülethez. A Stream Analytics projektek három mappából állnak: **bemenetek**, **kimenetek** és **függvények**. Emellett a lekérdezési szkripttel **(*. asaql)**, a fájl **JobConfig.jsával** és a konfigurációs fájl **asaproj.jsával** is rendelkezik.

    A konfigurációs fájl **asaproj.js** tartalmazza a stream Analytics feladatnak az Azure-ba való küldéséhez szükséges bemeneteket, kimeneteket és a feladatok konfigurációs fájljának adatait.

    ![Projektfájlok Stream Analytics a Visual Studio Code-ban](./media/quick-create-visual-studio-code/asa-project-files.png)

> [!Note]
> Amikor bemeneteket és kimeneteket ad hozzá a parancssorból, a rendszer automatikusan hozzáadja a megfelelő elérési utakat a **asaproj.jshoz** . Ha közvetlenül ad hozzá vagy távolít el bemeneteket vagy kimeneteket a lemezen, manuálisan kell hozzáadnia vagy eltávolítania őket a **asaproj.js**. Dönthet úgy, hogy egy helyen helyezi el a bemeneteket és kimeneteket, majd más feladatokban hivatkozik rájuk, ha megadja az elérési utakat a fájl egyes **asaproj.js** .

## <a name="define-the-transformation-query"></a>A transzformációs lekérdezés definiálása

1. Nyissa meg a **myASAproj. asaql** mappát a projekt mappájából.

2. Adja hozzá az alábbi lekérdezést:

   ```sql
   SELECT *
   INTO Output
   FROM Input
   HAVING Temperature > 27
   ```

## <a name="define-a-live-input"></a>Élő bemenet megadása

1. Kattintson a jobb gombbal a Stream Analytics projekt **bemenetek** mappájára. Ezután válassza az **ASA: bemenet hozzáadása** lehetőséget a helyi menüből.

    ![Bemenet hozzáadása a bemenetek mappából](./media/quick-create-visual-studio-code/add-input-from-inputs-folder.png)

    Vagy válassza a **CTRL + SHIFT + P** billentyűkombinációt a Command paletta megnyitásához, majd írja be az **ASA: bemenet hozzáadása** elemet.

   ![Stream Analytics bevitel hozzáadása a Visual Studio Code-ban](./media/quick-create-visual-studio-code/add-input.png)

2. A bemeneti típushoz válassza a **IoT hub** lehetőséget.

   ![Válassza az IoT hub lehetőséget a bemeneti beállításként](./media/quick-create-visual-studio-code/iot-hub.png)

3. Ha hozzáadta a bemenetet a parancssorból, válassza ki a Stream Analytics lekérdezési parancsfájlt, amely a bemenetet fogja használni. A rendszer automatikusan kitölti a **myASAproj. asaql** fájl elérési útját.

   ![Stream Analytics szkript kiválasztása a Visual Studio Code-ban](./media/quick-create-visual-studio-code/asa-script.png)

4. A legördülő menüben válassza az **Azure-előfizetések kiválasztása** lehetőséget.

    ![Kiválasztás az előfizetések közül](./media/quick-create-visual-studio-code/add-input-select-subscription.png)

5. Szerkessze az újonnan létrehozott **IoTHub1.js** fájlt a következő értékekkel. Tartsa meg az itt nem említett mezők alapértelmezett értékeit.

   |Beállítás|Ajánlott érték|Leírás|
   |-------|---------------|-----------|
   |Név|Bevitel|Adjon meg egy nevet a feladatok bemenetének azonosításához.|
   |IotHubNamespace|MyASAIoTHub|Válassza ki vagy adja meg az IoT hub nevét. Az IoT hub-nevek automatikusan észlelhetők, ha ugyanabban az előfizetésben jönnek létre.|
   |SharedAccessPolicyName|iothubowner| |

   A Codelensben funkcióval karakterláncot adhat meg, kiválaszthatja a legördülő listából, vagy megváltoztathatja a szöveget közvetlenül a fájlban. Az alábbi képernyőképen az **előfizetések közül választhat** , példaként. A hitelesítő adatok automatikusan szerepelnek, és a helyi hitelesítőadat-kezelőben lesznek mentve.

   ![A bemenet konfigurálása a Visual Studio Code-ban](./media/quick-create-visual-studio-code/configure-input.png)

   ![Bemeneti érték konfigurálása a Visual Studio Code-ban](./media/quick-create-visual-studio-code/configure-input-value.png)

## <a name="preview-input"></a>Előzetes verzió bemenete

A legfelső sorból válassza az **adatok Előnézete** **IoTHub1.jsa** következőt:. Egyes bemeneti adatok beolvasása az IoT hub-ból történik, és az előnézet ablakban látható. Ez a folyamat hosszabb időt is igénybe vehet.

 ![Élő bemenet megtekintése](./media/quick-create-visual-studio-code/preview-live-input.png)

## <a name="define-an-output"></a>Kimenet meghatározása

1. A parancs paletta megnyitásához kattintson a **CTRL + SHIFT + P** billentyűkombinációra. Ezután írja be az **ASA: kimenet hozzáadása** értéket.

   ![Stream Analytics kimenet hozzáadása a Visual Studio Code-ban](./media/quick-create-visual-studio-code/add-output.png)

2. A fogadó típushoz válassza a **blob Storage** lehetőséget.

3. Válassza ki azt a Stream Analytics lekérdezési parancsfájlt, amely ezt a bemenetet fogja használni.

4. Adja meg a kimeneti fájl nevét **BlobStorage** néven.

5. Szerkessze a **BlobStorage** a következő értékek használatával. Tartsa meg az itt nem említett mezők alapértelmezett értékeit. A Codelensben funkció használatával kiválaszthatja a legördülő listából, vagy megadhat egy karakterláncot.

   |Beállítás|Ajánlott érték|Leírás|
   |-------|---------------|-----------|
   |Név|Kimenet| Adjon meg egy nevet a feladatok kimenetének azonosításához.|
   |Tárfiók|asaquickstartstorage|Válassza ki vagy adja meg a Storage-fiók nevét. A rendszer automatikusan észleli a Storage-fiókok neveit, ha azok ugyanabban az előfizetésben jönnek létre.|
   |Tároló|container1|Válassza ki a tárfiókjában létrehozott meglévő tárolót.|
   |Path Pattern (Elérésiút-minta)|output|Adja meg a tárolón belül létrehozni kívánt elérési út nevét.|

   ![Kimenet konfigurálása a Visual Studio Code-ban](./media/quick-create-visual-studio-code/configure-output.png)

## <a name="compile-the-script"></a>A szkript fordítása

Parancsfájl-fordítási ellenőrzések szintaxisa és a Azure Resource Manager-sablonok létrehozása az automatikus központi telepítéshez.

A szkriptek fordítását kétféleképpen lehet elindítani:

- Válassza ki a parancsfájlt a munkaterületről, majd fordítsa le a parancsot a parancssorból.

   ![A szkript fordításához használja a Visual Studio Code parancs-palettát.](./media/quick-create-visual-studio-code/compile-script1.png)

- Kattintson a jobb gombbal a parancsfájlra, és válassza az **ASA: fordítási parancsfájl** elemet.

    ![A fordításhoz kattintson a jobb gombbal a Stream Analytics szkriptre](./media/quick-create-visual-studio-code/compile-script2.png)

A fordítás után megkeresheti a két létrehozott Azure Resource Manager sablonokat a projekt **üzembe helyezési** mappájában. A rendszer ezt a két fájlt használja az automatikus központi telepítéshez.

![Központi telepítési sablonok Stream Analytics a Fájlkezelőben](./media/quick-create-visual-studio-code/deployment-templates.png)

## <a name="submit-a-stream-analytics-job-to-azure"></a>Stream Analytics-feladatok elküldése az Azure-ba

1. A lekérdezési parancsfájl parancsfájl-szerkesztő ablakában válassza a **Küldés az Azure**-ba lehetőséget.

   ![Válassza ki az előfizetések szövegét a parancsfájl-szerkesztőben](./media/quick-create-visual-studio-code/submit-job.png)

2. Válassza ki az előfizetését az előugró listából.

3. Válassza **a feladatok kiválasztása** lehetőséget. Ezután válassza az **új feladatok létrehozása** lehetőséget.

4. Adja meg a **myASAjob** nevét. Ezután kövesse az utasításokat az erőforráscsoport és a hely kiválasztásához.

5. Válassza **az elküldés az Azure-** ba lehetőséget. A naplókat a kimeneti ablakban találja. 

6. A feladatok létrehozásakor megtekintheti **stream Analytics Explorerben**.

    ![Felsorolt feladatok Stream Analytics Explorerben](./media/quick-create-visual-studio-code/list-job.png)

## <a name="start-the-stream-analytics-job-and-check-output"></a>A Stream Analytics-feladat indítása és a kimenet ellenőrzése

1. Nyissa meg **stream Analytics Explorert** a Visual Studio Code-ban, és keresse meg a **myASAJob** feladatot.

2. Kattintson a jobb gombbal a feladatokra. Ezután válassza a **Start** lehetőséget a helyi menüben.

   ![Indítsa el a Stream Analytics feladatot a Visual Studio Code-ban](./media/quick-create-visual-studio-code/start-asa-job-vs-code.png)

3. A felugró ablakban válassza a **most** lehetőséget a feladatok elindításához.

4. Vegye figyelembe, hogy a feladatok állapota **futásra** módosult. Kattintson a jobb gombbal a feladatra, majd válassza a **feladatok nézet megnyitása a portálon** lehetőséget a bemeneti és a kimeneti esemény metrikáinak megtekintéséhez. Ez a művelet néhány percet is igénybe vehet.

5. Az eredmények megtekintéséhez nyissa meg a blob Storage-t a Visual Studio Code-bővítményben vagy a Azure Portal.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, törölje az erőforráscsoportot, a folyamatos átviteli feladatot és az összes kapcsolódó erőforrást. A művelet törlésével elkerülhető a feladatok által felhasznált folyamatos átviteli egységek számlázása. 

Ha a későbbiekben tervezi használni a feladatot, leállíthatja, és később újraindíthatja. Ha még nem használja ezt a feladatot, a következő lépésekkel törölheti az ebben a rövid útmutatóban létrehozott összes erőforrást:

1. A Azure Portal bal oldali menüjében válassza az **erőforráscsoportok** lehetőséget, majd válassza ki a létrehozott erőforrás nevét.  

2. Az erőforráscsoport oldalán kattintson a **Törlés** parancsra. Adja meg a törlendő erőforrás nevét a szövegmezőben, majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy egyszerű Stream Analytics feladatot telepített a Visual Studio Code használatával. Stream Analytics feladatokat a [Azure Portal](stream-analytics-quick-create-portal.md), a [PowerShell](stream-analytics-quick-create-powershell.md)és a [Visual Studio](stream-analytics-quick-create-vs.md)használatával is üzembe helyezhet.

A Visual Studio Code Azure Stream Analytics eszközeinek megismeréséhez folytassa a következő cikkekkel:

* [A Visual Studio Code segítségével helyileg tesztelheti Stream Analytics lekérdezéseket a mintaadatok használatával](visual-studio-code-local-run.md)

* [Azure Stream Analytics feladatok helyi tesztelése élő bevitelsel a Visual Studio Code-ban](visual-studio-code-local-run-live-input.md)

* [Azure Stream Analytics feladatok megtekintése a Visual Studio Code használatával](visual-studio-code-explore-jobs.md)

* [CI/CD-folyamatok beállítása a NPM-csomag használatával](./cicd-overview.md)