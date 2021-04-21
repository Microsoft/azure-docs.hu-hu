---
title: Oktatóanyag – Azure IoT Hub üzenet-útválasztási eredmények (.NET) | Microsoft Docs
description: Oktatóanyag – Miután az oktatóanyag 1. részével beállította az összes erőforrást, lehetősége van üzeneteket Azure Stream Analytics és az eredményeket a Power BI.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2018
ms.author: robinsh
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: 7d6b5f02a752e7bbdc1d4da83b60b1a86aba5626
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775024"
---
# <a name="tutorial-part-2---view-the-routed-messages"></a>Oktatóanyag: 2. rész – Az útvonalba küldött üzenetek megtekintése

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="rules-for-routing-the-messages"></a>Az üzenetek útválasztására vonatkozó szabályok

Ezek az üzenetek útválasztásának szabályai; Ezek az oktatóanyag 1. részében voltak beállítva, és láthatja, hogy a második részben működnek.

|Érték |Eredmény|
|------|------|
|level="storage" |Írás az Azure Storage-ba.|
|level="critical" |Írás a Service Bus-üzenetsorba. Egy logikai alkalmazás lekéri az üzenetet a sorból, és az Office 365 használatával e-mailben küldi el az üzenetet.|
|alapértelmezett |Jelenítse meg az adatokat a Power BI használatával.|

Most létrehozhatja az erőforrásokat, amelyekre az üzenetek irányítva lesznek, futtathat egy alkalmazást, amely üzeneteket küld a központnak, és láthatja az útválasztást.

## <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása  

A Service Bus-üzenetsor a kritikusként megjelölt üzenetek fogadására használható. Állítson be egy logikai alkalmazást a Service Bus-üzenetsor monitorozására, illetve arra, hogy e-mailt küldjön, amikor a rendszer hozzáad egy üzenetet az üzenetsorhoz.

1. A [Azure Portal](https://portal.azure.com)válassza a **+ Erőforrás létrehozása lehetőséget.** A keresőmezőbe írja a **logikai alkalmazás** kifejezést, és nyomja le az Enter billentyűt. A megjelenő keresési eredmények közül válassza a  Logikai alkalmazás, majd a Létrehozás lehetőséget a Logikai alkalmazás **létrehozása panelre való folytatáshoz.** Töltse ki a mezőket.

   **Név**: Ez a mező a logikai alkalmazás neve. Ez az oktatóanyag a **ContosoLogicApp** nevet használja.

   **Előfizetés**: Válassza ki az Azure-előfizetést.

   **Erőforráscsoport:** Válassza a **Meglévő használata lehetőséget,** majd válassza ki az erőforráscsoportot. Ez az oktatóanyag a **ContosoResources** erőforráscsoportot használja.

   **Hely**: Használja a saját helyzetét. Ez az oktatóanyag az **USA nyugati régióját** használja.

   **Log Analytics engedélyezése:** Ezt a váltógombot ki kell kapcsolva.

   ![A Logikai alkalmazás létrehozása képernyő](./media/tutorial-routing-view-message-routing-results/create-logic-app.png)

   Válassza a **Létrehozás** lehetőséget. Az alkalmazás üzembe helyezése eltarthat néhány percig.

2. Lépjen a logikai alkalmazáshoz. A logikai alkalmazás legegyszerűbben úgy megjelenik, ha kiválasztja az Erőforráscsoportok **lehetőséget,** kiválasztja az erőforráscsoportot (ez az oktatóanyag a **ContosoResources** erőforrást használja), majd kiválasztja a logikai alkalmazást az erőforrások listájából. 

    Megjelenik a Logic Apps Designer lap (előfordulhat, hogy jobbra kell görgetnie, hogy megjelenjen a teljes lap). A Logic Apps Designer lapon görgessen lefelé, amíg meg nem jelenik az Üres logikai alkalmazás **+** csempe, és válassza ki. Az alapértelmezett lap a "For You". Ha ez a panel üres, válassza az **Összes** lehetőséget az összes elérhető összekötő és eseményindító kiválasztásához.

3. Válassza **Service Bus** összekötők listájából a Megfelelők lehetőséget.

   ![Az összekötők listája](./media/tutorial-routing-view-message-routing-results/logic-app-connectors.png)

4. Megjelenik az eseményindítók listája. Válassza **a When a message is received in a queue (auto-complete) /Service Bus**.

   ![Az eseményindítók listája a Service Bus](./media/tutorial-routing-view-message-routing-results/logic-app-triggers.png)

5. A következő képernyőn töltse ki a Kapcsolat neve mezőt. Ez az oktatóanyag a **ContosoConnection** nevet használja.

   ![Az üzenetsor kapcsolatának Service Bus beállítása](./media/tutorial-routing-view-message-routing-results/logic-app-define-connection.png)

   Válassza ki Service Bus névteret. Ez az oktatóanyag a **ContosoSBNamespace** névteret használja. A névtér kiválasztásakor a Portal lekérdezi a Service Bus-névteret a kulcsok lekéréséhez. Válassza **a RootManageSharedAccessKey, majd** a Létrehozás **lehetőséget.**

   ![A kapcsolat beállításának befejezése](./media/tutorial-routing-view-message-routing-results/logic-app-finish-connection.png)

6. A következő képernyőn a legördülő listából válassza ki az üzenetsor nevét (ebben az oktatóanyagban ez **contososbqueue**). A többi mező esetében használhatja az alapértelmezett értékeket.

   ![Az üzenetsor beállításai](./media/tutorial-routing-view-message-routing-results/logic-app-queue-options.png)

7. Állítsa be a műveletet, hogy e-mailt küldjön, amikor egy üzenet érkezik az üzenetsorba. A Logic Apps Designerben válassza az **+** Új lépés lehetőséget  egy lépés hozzáadásához, majd válassza az Összes lehetőséget az összes elérhető lehetőség kiválasztásához. A Művelet **kiválasztása panelen** keresse meg és válassza az **Office 365 Outlook lehetőséget.** A Műveletek képernyőn válassza az **E-mail küldése / Office 365 Outlook lehetőséget.**  

   ![Az Office 365 beállításai](./media/tutorial-routing-view-message-routing-results/logic-app-select-outlook.png)

8. A kapcsolat beállításhoz jelentkezzen be munkahelyi vagy iskolai fiókjába. Ha ez időkorreklott, csak próbálkozzon újra. Adja meg az e-mailek címzettjeinek e-mail-címeit. Adja meg a tárgyat is, és írja be a szövegtörzsben megjeleníteni kívánt üzenetet. Teszteléshez adja meg a saját e-mail-címét címzettként.

   Válassza **a Dinamikus tartalom hozzáadása** lehetőséget a felvehető üzenet tartalmának a megjelenítése érdekében. Válassza a **Tartalom** lehetőséget, amellyel belefoglalhatja az üzenetet az e-mailbe.

   ![A logikai alkalmazás e-mail-beállításai](./media/tutorial-routing-view-message-routing-results/logic-app-send-email.png)

9. Kattintson a **Mentés** gombra. Ezután zárja be a Logic App Designert.

## <a name="set-up-azure-stream-analytics"></a>Az Azure Stream Analytics beállítása

A Power BI-vizualizáció adatainak megtekintéséhez először állítson be egy Stream Analytics-feladatot az adatok lekérésére. Ne feledje, hogy a rendszer csak azokat az üzeneteket küldi az alapértelmezett végpontra, amelyek **level** tulajdonsága **normal** értékű, és a Stream Analytics-feladat csak ezeket kéri le a Power BI-vizualizációhoz.

### <a name="create-the-stream-analytics-job"></a>A Stream Analytics-feladat létrehozása

1. A [Azure Portal](https://portal.azure.com)válassza az **Erőforrás létrehozása eszközök internetes hálózata** Stream Analytics  >    >  **feladatot.**

2. Adja meg a feladat alábbi adatait.

   **Feladat neve**: A feladat neve. A névnek globálisan egyedinek kell lennie. Ez az oktatóanyag a **contosoJob** nevet használja.

   **Előfizetés:** Az oktatóanyaghoz használt Azure-előfizetés.

   **Erőforráscsoport**: Használja az IoT Hubja által használt erőforráscsoportot. Ez az oktatóanyag a **ContosoResources** erőforráscsoportot használja.

   **Hely**: Használja a beállítási szkriptnél is használt helyet. Ez az oktatóanyag az **USA nyugati régióját** használja.

   ![A Stream Analytics-feladat létrehozása](./media/tutorial-routing-view-message-routing-results/stream-analytics-create-job.png)

3. A **feladat létrehozásához** válassza a Létrehozás lehetőséget. Az üzembe helyezés eltarthat néhány percig.

    A feladathoz való visszaúthoz válassza az **Erőforráscsoportok lehetőséget.** Ez az oktatóanyag a **ContosoResources** erőforráscsoportot használja. Válassza ki az erőforráscsoportot, majd válassza ki Stream Analytics erőforráslistában a feladathoz.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Bemenet hozzáadása a Stream Analytics-feladathoz

1. A **Feladat topológiája alatt válassza** a **Bemenetek lehetőséget.**

2. A **Bemenetek panelen** válassza a **Streambemenet hozzáadása lehetőséget, majd** IoT Hub. A megjelenő képernyőn töltse ki az alábbi mezőket:

   **Bemeneti áljel**: Ez az oktatóanyag a **contosoinputs** áljelet használja.

   **Válassza IoT Hub lehetőséget az előfizetéséből:** Válassza ezt a választógombot.

   **Előfizetés:** Válassza ki az oktatóanyaghoz használt Azure-előfizetést.

   **IoT Hub:** Válassza ki az IoT Hubot. Ez az oktatóanyag a **ContosoTestHub** erőforrást használja.

   **Végpont**: Válassza az **Üzenetkezelés** lehetőséget. (Ha a Műveletek monitorozása lehetőséget választja, akkor az IoT Hub telemetriaadatait kapja meg, nem pedig az átküldött adatokat.) 

   **Megosztott hozzáférési szabályzat neve:** Válassza ki a **szolgáltatást.** A Portal automatikusan kitölti a megosztott elérési házirend kulcsát.

   **Fogyasztói csoport:** Válassza ki az oktatóanyag 1. részében beállított fogyasztói csoportot. Ez az oktatóanyag a **contosoconsumers** csoportot használja.
   
   A többi mezőnél fogadja el az alapértelmezett beállításokat. 

   ![A Stream Analytics-feladat bemenetének beállítása](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-inputs.png)

3. Kattintson a **Mentés** gombra.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Kimenet hozzáadása a Stream Analytics-feladathoz

1. A **Feladat topológiája alatt válassza** a **Kimenetek lehetőséget.**

2. A **Kimenetek panelen** válassza **a** Hozzáadás lehetőséget, majd a **Power BI** lehetőséget. A megjelenő képernyőn töltse ki az alábbi mezőket:

   **Kimeneti áljel**: A kimenet egyedi áljele. Ez az oktatóanyag a **contosooutputs** áljelet használja. 

   **Adatkészlet neve**: A Power BI-ban használni kívánt adatkészletnév. Ez az oktatóanyag a **contosodataset** nevet használja. 

   **Tábla neve**: A Power BI-ban használni kívánt táblanév. Ez az oktatóanyag a **contosotable** nevet használja.

  **Hitelesítési mód:** Válassza ki a használni kívánt módot.

   A többi mezőnél fogadja el az alapértelmezett beállításokat.

3. Válassza **az Authorize (Engedély)** lehetőséget, és jelentkezzen be a Power BI fiókjába. (Ez egynél több próbálkozásra is szükség lehet).

   ![A Stream Analytics-feladat kimenetének beállítása](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-outputs.png)

4. Kattintson a **Mentés** gombra.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>A Stream Analytics-feladat lekérdezésének konfigurálása

1. A **Feladat topológiája alatt válassza** a Lekérdezés **lehetőséget.**

2. A `[YourInputAlias]` elemet cserélje le a feladat bemeneti áljelére. Ez az oktatóanyag a **contosoinputs** bemeneti áljelet használja.

3. A `[YourOutputAlias]` elemet cserélje le a feladat kimeneti áljelére. Ez az oktatóanyag a **contosooutputs** áljelet használja.

   ![A Stream Analytics-feladat lekérdezésének beállítása](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-query.png)

4. Kattintson a **Mentés** gombra.

5. Zárja be a Lekérdezés panelt. Visszatér az erőforráscsoport erőforrásainak nézetére. Válassza ki Stream Analytics feladatot. Ebben az oktatóanyagban a neve: **contosoJob**.

### <a name="run-the-stream-analytics-job"></a>Stream Analytics-feladat futtatása

A Stream Analytics válassza az **Indítás most**  >  **indítás**  >  **lehetőséget.** Ha a feladat sikeresen elindult, a feladat állapota **Leállítva** értékről **Fut** értékre változik.

A Power BI-jelentés beállításához adatokra van szükség, ezért a Power BI-t az eszköz létrehozása és az eszközszimulációs alkalmazás futtatása után fogja beállítani.

## <a name="run-simulated-device-app"></a>Szimulálteszköz-alkalmazás futtatása

Az oktatóanyag 1. részében beállít egy eszközt egy IoT-eszköz használatával való szimuláláshoz. Ebben a szakaszban letölti a .NET-konzolalkalmazást, amely azt szimulálja, hogy az eszköz eszközről a felhőbe küldött üzeneteket küld egy IoT Hubnak (feltéve, hogy még nem letöltötte az alkalmazást és az erőforrásokat az 1. részben).

Ez az alkalmazás üzeneteket küld a különböző üzenet-útválasztási módszerekhez. A letöltés tartalmaz egy mappát is, amely tartalmazza a teljes Azure Resource Manager sablont és paraméterfájlt, valamint az Azure CLI- és PowerShell-szkripteket.

Ha az oktatóanyag 1. részében nem letöltötte a fájlokat az adattárból, töltse le őket most az [IoT-eszközszimulációból.](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) Erre a hivatkozásra kattintva letölt egy több alkalmazást is tároló adattárat; A keresett megoldás az iot-hub/Tutorials/Routing/IoT_SimulatedDevice.sln. 

Kattintson duplán a megoldásfájlra (IoT_SimulatedDevice.sln) a kód megnyitásához a Visual Studio, majd nyissa meg a Program.cs fájlt. Az `{your hub name}` értéket cserélje le az IoT Hub gazdagépnevére. Az IoT Hub gazdagépnevének formátuma: **{iot-hub-name}.azure-devices.net**. Ebben az oktatóanyagban a központ gazdagépneve: **ContosoTestHub.azure-devices.net**. Ezután a `{your device key}` értéket cserélje le az eszközkulcsra, amelyet korábban, a szimulált eszköz beállítása során mentett. 

   ```csharp
        static string s_myDeviceId = "Contoso-Test-Device";
        static string s_iotHubUri = "ContosoTestHub.azure-devices.net";
        // This is the primary key for the device. This is in the portal. 
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key. 
        static string s_deviceKey = "{your device key}";
   ```

## <a name="run-and-test"></a>Futtatás és tesztelés

Futtassa a konzolalkalmazást. Várjon néhány percet. Az alkalmazás konzolképernyőjén láthatja az elküldött üzeneteket.

Az alkalmazás másodpercenként elküld egy új üzenetet az eszközről a felhőbe az IoT Hubnak. Az üzenet egy JSON-szerializált objektumot tartalmaz az eszköz azonosítójával, a hőmérséklettel, a páratartalommal és az üzenet szintjével, amely alapértelmezés szerint `normal`. Az alkalmazás véletlenszerűen rendeli hozzá a `critical` vagy a `storage` szintet, így az üzenet a tárfiókhoz vagy a Service Bus-üzenetsorhoz lesz irányítva (a logikai alkalmazás pedig ennek hatására egy e-mailt küld). Az alapértelmezett olvasás (`normal`) megjelenik a BI-jelentésben, amelyet a következő lépésben fog beállítani.

Ha minden helyesen lett beállítva, a következő eredményeket fogja látni:

1. E-maileket kap a kritikus üzenetekről.

   ![Az eredményül kapott e-mailek](./media/tutorial-routing-view-message-routing-results/results-in-email.png)

   Ez az eredmény azt jelenti, hogy a következő állítások igazak. 

   * A Service Bus-üzenetsorra történő irányítás megfelelően működik.
   * Az üzenetet a Service Bus-üzenetsorból lekérő logikai alkalmazás megfelelően működik.
   * A logikai alkalmazás Outlook-összekötője megfelelően működik. 

2. A [Azure Portal](https://portal.azure.com)válassza az **Erőforráscsoportok lehetőséget,** majd válassza ki az erőforráscsoportot. Ez az oktatóanyag a **ContosoResources** erőforráscsoportot használja. 

    Válassza ki a tárfiókot, válassza a **Tárolók,** majd a Tároló lehetőséget. Ez az oktatóanyag a **contosoresults** tárolót használja. Megjelenik egy mappa, amelyben lefúrhat a könyvtárakba, amíg egy vagy több fájlt nem lát. Nyissa meg az egyik fájlt. A fájlok a tárfiókhoz irányított bejegyzéseket tartalmazzák. 

   ![Az eredményfájlok a tárolóban](./media/tutorial-routing-view-message-routing-results/results-in-storage.png)

Ez az eredmény azt jelenti, hogy a következő állítás igaz.

   * A tárfiókhoz történő útválasztás megfelelően működik.

Most, hogy az alkalmazás továbbra is fut, állítsa be a Power BI vizualizációt az alapértelmezett útválasztáson keresztül érkező üzenetek megjelenítéséhez.

## <a name="set-up-the-power-bi-visualizations"></a>A Power BI-vizualizációk beállítása

1. Jelentkezzen be a [Power BI](https://powerbi.microsoft.com/)-fiókjába.

2. Lépjen a **Munkaterületek** részhez, és válassza ki azt a munkaterületet, amelyet a Stream Analytics-feladat kimenetének létrehozásakor állított be. Ez az oktatóanyag a **My Workspace** nevű munkaterületet használja. 

3. Válassza **az Adatkészletek lehetőséget.** Ha még nincsenek adatkészletei, várjon néhány percet, és ellenőrizze újra.

   Megjelenik az adatkészlet, amelyet a Stream Analytics-feladat kimenetének létrehozásakor adott meg. Ez az oktatóanyag a **contosodataset** nevet használja. (Akár 5-10 percet is igénybe vehet, mire az adatkészlet először megjelenik.)

4. A **MŰVELETEK alatt** válassza az első ikont egy jelentés létrehozásához.

   ![Power BI munkaterület kiemelt Műveletek és jelentés ikonnal](./media/tutorial-routing-view-message-routing-results/power-bi-actions.png)

5. Hozzon létre egy vonaldiagramot, amely a valós időben jeleníti meg a hőmérséklet változását.

   * A jelentéskészítési oldalon vegyen fel egy vonaldiagramot a vonaldiagram ikon kiválasztásával.

     ![A vizualizációk és mezők](./media/tutorial-routing-view-message-routing-results/power-bi-visualizations-and-fields.png)

   * A **Mezők** panelen bontsa ki Stream Analytics-feladat kimenetének létrehozásakor megadott táblát. Ez az oktatóanyag a **contosotable** nevet használja.

   * Az **EventEnqueuedUtcTime** elemet húzza a **Vizualizációk** panel **Tengely** részére.

   * Húzza a **hőmérséklet** elemet az **Értékek** helyre.

   Létrejön a vonaldiagram. Az X tengely az UTC időzóna szerinti dátumot is időt mutatja. Az Y tengelyen az érzékelőből származó hőmérsékleti adatok láthatók.

6. Hozzon létre egy másik vonaldiagramot, amely a valós időben jeleníti meg a páratartalom változását. A második diagram beállításához kövesse ugyanazt a folyamatot az első diagramon, és helyezze el az **EventEnqueuedUtcTime** értéket az x tengelyen (**Tengely**) és a páratartalommal az y tengelyen (**Értékek**). 

   ![Az utolsó Power BI jelentés a két diagrammal](./media/tutorial-routing-view-message-routing-results/power-bi-report.png)

7. Válassza **a Mentés** lehetőséget a jelentés mentéséhez, és ha a rendszer kéri, adja meg a jelentés nevét.

Az adatoknak mindkét diagramon meg kell jelenniük. Ez az eredmény azt jelenti, hogy a következő állítások igazak:

   * Az alapértelmezett végpontra történő irányítás megfelelően működik.
   * Az Azure Stream Analytics-feladat streamelése megfelelően működik.
   * A Power BI-vizualizáció megfelelően van beállítva.

A diagramok frissítéséhez a legutóbbi adatokat láthatja, ha a Frissítés gomb az ablak tetején Power BI gombra. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 

Ha az oktatóanyag mindkét részében létrehozott összes Azure-erőforrást el szeretné távolítani, törölje az erőforráscsoportot. Ez a művelet törli a csoportban lévő összes erőforrást. Ebben az esetben eltávolítja az IoT Hubot, a Service Bus-névteret és -üzenetsort, a logikai alkalmazást, a tárfiókot és magát az erőforráscsoportot is. Emellett eltávolíthatja a Power BI erőforrásokat, és eltávolíthatja az oktatóanyag során küldött e-maileket.

### <a name="clean-up-resources-in-the-power-bi-visualization"></a>Erőforrások eltávolítása a Power BI-vizualizációban

Jelentkezzen be a [Power BI](https://powerbi.microsoft.com/)-fiókjába. Lépjen a munkaterülethez. Ez az oktatóanyag a **My Workspace** nevű munkaterületet használja. A vizualizáció Power BI az Adatkészletek gombra kattintva törölheti az adatkészletet a kuka ikonnal. Ez az oktatóanyag a **contosodataset** nevet használja. Az adatkészlet eltávolításakor a jelentés is törlődik.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Erőforrások megtisztítása az Azure CLI használatával

Az erőforráscsoport az [az group delete](/cli/azure/group#az_group_delete) paranccsal távolítható el. `$resourceGroup` az oktatóanyag elején **a ContosoResources** lett beállítva.

```azurecli-interactive
az group delete --name $resourceGroup
```

### <a name="use-powershell-to-clean-up-resources"></a>Erőforrások tisztítása a PowerShell használatával

Az erőforráscsoport eltávolításához használja a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) parancsot. `$resourceGroup` az oktatóanyag elején **a ContosoResources** lett beállítva.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroup
```

### <a name="clean-up-test-emails"></a>Teszt e-mailek tisztítása

Az is előfordulhat, hogy törölni szeretné a beérkező e-mailek mennyiségét, amelyek az eszközalkalmazás futtatása közben a logikai alkalmazáson keresztül keletkeztek.

## <a name="next-steps"></a>Következő lépések

Ebben a kétrészes oktatóanyagban az alábbi feladatok elvégzésével megtanulta, hogyan használható az üzenetek IoT Hub üzenetek különböző célhelyre való átirányítására.  

**I. rész: Erőforrások létrehozása, üzenetek útválasztásának beállítása**
> [!div class="checklist"]
> * Hozza létre az erőforrásokat – egy IoT Hubot, egy tárfiókot, Service Bus üzenetsort és egy szimulált eszközt.
> * Konfigurálja a tárfiókhoz és az üzenetsorhoz IoT Hub végpontokat és üzenetútvonalokat Service Bus üzenetsorhoz.

**2. rész: Üzenetek küldése a központba, az útvonalon keresztüli eredmények megtekintése**
> [!div class="checklist"]
> * Egy logikai alkalmazás létrehozása, amely egy üzenet Service Bus-üzenetsorba való érkezésekor indul el és küld e-maileket.
> * Egy olyan alkalmazás letöltése és futtatása, amely a Hubhoz üzeneteket küldő IoT-eszközt szimulál a különböző útválasztási lehetőségekhez.
> * Power BI-vizualizáció létrehozása az alapértelmezett végpontra küldött adatokról.
> * Eredmények megtekintése...
> * ... a Service Bus-üzenetsorban és az e-mailekben.
> * ... a tárfiókban.
> * ... a Power BI-vizualizációban.

A következő oktatóanyag az IoT-eszközök állapotának kezelését mutatja be. 

> [!div class="nextstepaction"]
> [Metrikák és diagnosztika beállítása és használata IoT Hub](tutorial-use-metrics-and-diags.md)