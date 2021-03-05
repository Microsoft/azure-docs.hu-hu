---
title: Oktatóanyag – Azure IoT Hub üzenet-útválasztási eredmények (.NET) megtekintése | Microsoft Docs
description: Oktatóanyag – miután az oktatóanyag 1. részében az összes erőforrást beállította, adja hozzá az üzeneteket a Azure Stream Analyticshoz, és tekintse meg az eredményeket Power BI.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2018
ms.author: robinsh
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: f441a1cf97c069c4755b436bbb8cb9268b469eb3
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102199747"
---
# <a name="tutorial-part-2---view-the-routed-messages"></a>Oktatóanyag: 2. rész – az átirányított üzenetek megtekintése

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="rules-for-routing-the-messages"></a>Az üzenetek útválasztásának szabályai

Ezek az üzenetek útválasztásának szabályai; Ezek az oktatóanyag 1. részében lettek beállítva, és ebben a második részben jelennek meg.

|Érték |Eredmény|
|------|------|
|level="storage" |Írás az Azure Storage-ba.|
|level="critical" |Írás a Service Bus-üzenetsorba. Egy logikai alkalmazás lekéri az üzenetet a sorból, és az Office 365 használatával e-mailben küldi el az üzenetet.|
|alapértelmezett |Jelenítse meg az adatokat a Power BI használatával.|

Most hozza létre azokat az erőforrásokat, amelyekhez az üzeneteket át szeretné irányítani, futtasson egy alkalmazást, hogy üzeneteket küldjön a központnak, és tekintse meg az útválasztást működés közben.

## <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása  

A Service Bus-üzenetsor a kritikusként megjelölt üzenetek fogadására használható. Állítson be egy logikai alkalmazást a Service Bus-üzenetsor monitorozására, illetve arra, hogy e-mailt küldjön, amikor a rendszer hozzáad egy üzenetet az üzenetsorhoz.

1. A [Azure Portal](https://portal.azure.com)válassza az **+ erőforrás létrehozása** lehetőséget. A keresőmezőbe írja a **logikai alkalmazás** kifejezést, és nyomja le az Enter billentyűt. A találatok között válassza a logikai alkalmazás lehetőséget, majd a **Létrehozás** gombra kattintva folytassa a **logikai alkalmazás létrehozása** panelt. Töltse ki a mezőket.

   **Név**: Ez a mező a logikai alkalmazás neve. Ez az oktatóanyag a **ContosoLogicApp** nevet használja.

   **Előfizetés**: Válassza ki az Azure-előfizetést.

   **Erőforráscsoport**: válassza a **meglévő használata** lehetőséget, és válassza ki az erőforráscsoportot. Ez az oktatóanyag a **ContosoResources** erőforráscsoportot használja.

   **Hely**: Használja a saját helyzetét. Ez az oktatóanyag az **USA nyugati régióját** használja.

   **Log Analytics engedélyezése**: ezt a kapcsolót ki kell kapcsolni.

   ![A logikai alkalmazás létrehozása képernyő](./media/tutorial-routing-view-message-routing-results/create-logic-app.png)

   Válassza a **Létrehozás** lehetőséget. Az alkalmazás üzembe helyezése néhány percet igénybe vehet.

2. Lépjen a logikai alkalmazáshoz. A logikai alkalmazás elérésének legegyszerűbb módja az **erőforráscsoportok** kiválasztása, válassza ki az erőforráscsoportot (ez az oktatóanyag az **ContosoResources**-t használja), majd válassza ki a logikai alkalmazást az erőforrások listájából. 

    Megjelenik a Logic Apps Designer lap (előfordulhat, hogy jobbra kell görgetnie, hogy megjelenjen a teljes lap). A Logic Apps Designer lapon görgessen lefelé, amíg meg nem jelenik az **üres Logic app +** elemet tartalmazó csempe, és válassza ki azt. Az alapértelmezett lap az "Ön". Ha ez az ablaktábla üres, válassza az **összes** lehetőséget az összes elérhető összekötő és eseményindító megjelenítéséhez.

3. Az összekötők listájából válassza a **Service Bus** lehetőséget.

   ![Az összekötők listája](./media/tutorial-routing-view-message-routing-results/logic-app-connectors.png)

4. Megjelenik az eseményindítók listája. Válassza ki, hogy **mikor érkezik üzenet egy várólistában (automatikus kiegészítés)/Service Bus**.

   ![A Service Bus eseményindítóinak listája](./media/tutorial-routing-view-message-routing-results/logic-app-triggers.png)

5. A következő képernyőn töltse ki a Kapcsolat neve mezőt. Ez az oktatóanyag a **ContosoConnection** nevet használja.

   ![A Service Bus üzenetsor csatlakoztatásának beállítása](./media/tutorial-routing-view-message-routing-results/logic-app-define-connection.png)

   Válassza ki a Service Bus névteret. Ez az oktatóanyag a **ContosoSBNamespace** névteret használja. A névtér kiválasztásakor a Portal lekérdezi a Service Bus-névteret a kulcsok lekéréséhez. Válassza a **RootManageSharedAccessKey** elemet, majd válassza a **Létrehozás** lehetőséget.

   ![A kapcsolatok beállításának befejezése](./media/tutorial-routing-view-message-routing-results/logic-app-finish-connection.png)

6. A következő képernyőn a legördülő listából válassza ki az üzenetsor nevét (ebben az oktatóanyagban ez **contososbqueue**). A többi mező esetében használhatja az alapértelmezett értékeket.

   ![Az üzenetsor beállításai](./media/tutorial-routing-view-message-routing-results/logic-app-queue-options.png)

7. Állítsa be a műveletet, hogy e-mailt küldjön, amikor egy üzenet érkezik az üzenetsorba. A Logic Apps Designerben válassza az **+ új lépés** lehetőséget egy lépés hozzáadásához, majd válassza az **összes** lehetőséget az összes elérhető lehetőség megjelenítéséhez. A **válasszon műveletet** ablaktáblán keresse meg és válassza ki az **Office 365 Outlook** elemet. A műveletek képernyőn válassza az **E-mail küldése/Office 365 Outlook** lehetőséget.  

   ![A Office 365 beállításai](./media/tutorial-routing-view-message-routing-results/logic-app-select-outlook.png)

8. A kapcsolódás beállításához jelentkezzen be a munkahelyi vagy iskolai fiókjába. Ha ez túllépi az időkorlátot, csak próbálkozzon újra. Adja meg az e-mailek címzettjeinek e-mail-címeit. Adja meg a tárgyat is, és írja be a szövegtörzsben megjeleníteni kívánt üzenetet. Teszteléshez adja meg a saját e-mail-címét címzettként.

   Válassza a **dinamikus tartalom hozzáadása** lehetőséget, hogy megjelenjen a tartalom az üzenetből, amelyet felvehet. Válassza a **Tartalom** lehetőséget, amellyel belefoglalhatja az üzenetet az e-mailbe.

   ![A logikai alkalmazás e-mail-beállításai](./media/tutorial-routing-view-message-routing-results/logic-app-send-email.png)

9. Kattintson a **Mentés** gombra. Ezután zárja be a Logic App Designert.

## <a name="set-up-azure-stream-analytics"></a>Az Azure Stream Analytics beállítása

A Power BI-vizualizáció adatainak megtekintéséhez először állítson be egy Stream Analytics-feladatot az adatok lekérésére. Ne feledje, hogy a rendszer csak azokat az üzeneteket küldi az alapértelmezett végpontra, amelyek **level** tulajdonsága **normal** értékű, és a Stream Analytics-feladat csak ezeket kéri le a Power BI-vizualizációhoz.

### <a name="create-the-stream-analytics-job"></a>A Stream Analytics-feladat létrehozása

1. A [Azure Portal](https://portal.azure.com)válassza az **erőforrás létrehozása**  >  **eszközök internetes hálózata**  >  **stream Analyticsi feladatot**.

2. Adja meg a feladat alábbi adatait.

   **Feladat neve**: A feladat neve. A névnek globálisan egyedinek kell lennie. Ez az oktatóanyag a **contosoJob** nevet használja.

   **Előfizetés**: az oktatóanyaghoz használt Azure-előfizetés.

   **Erőforráscsoport**: Használja az IoT Hubja által használt erőforráscsoportot. Ez az oktatóanyag a **ContosoResources** erőforráscsoportot használja.

   **Hely**: Használja a beállítási szkriptnél is használt helyet. Ez az oktatóanyag az **USA nyugati régióját** használja.

   ![A stream Analytics-feladatok létrehozása](./media/tutorial-routing-view-message-routing-results/stream-analytics-create-job.png)

3. Válassza a **Létrehozás** lehetőséget a feladatok létrehozásához. A telepítés eltarthat néhány percig.

    Ha vissza szeretne térni a feladatokhoz, válassza az **erőforráscsoportok** lehetőséget. Ez az oktatóanyag a **ContosoResources** erőforráscsoportot használja. Válassza ki az erőforráscsoportot, majd válassza ki a Stream Analytics feladatot az erőforrások listájában.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Bemenet hozzáadása a Stream Analytics-feladathoz

1. A **feladatok topológiája** területen válassza a **bemenetek** lehetőséget.

2. A **bemenetek** ablaktáblán válassza a **stream-bemenet hozzáadása** lehetőséget, és válassza a IoT hub lehetőséget. A megjelenő képernyőn töltse ki az alábbi mezőket:

   **Bemeneti áljel**: Ez az oktatóanyag a **contosoinputs** áljelet használja.

   **IoT hub kiválasztása az előfizetésből**: válassza ezt a választógombot.

   **Előfizetés**: válassza ki az oktatóanyaghoz használni kívánt Azure-előfizetést.

   **IoT hub**: válassza ki az IoT hubot. Ez az oktatóanyag a **ContosoTestHub** erőforrást használja.

   **Végpont**: Válassza az **Üzenetkezelés** lehetőséget. (Ha a Műveletek monitorozása lehetőséget választja, akkor az IoT Hub telemetriaadatait kapja meg, nem pedig az átküldött adatokat.) 

   **Megosztott elérési házirend neve**: válassza a **szolgáltatás** elemet. A Portal automatikusan kitölti a megosztott elérési házirend kulcsát.

   **Fogyasztói csoport**: válassza ki az oktatóanyag 1. részében beállított fogyasztói csoportot. Ez az oktatóanyag a **contosoconsumers** csoportot használja.
   
   A többi mezőnél fogadja el az alapértelmezett beállításokat. 

   ![A stream Analytics-feladathoz tartozó bemenetek beállítása](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-inputs.png)

3. Kattintson a **Mentés** gombra.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Kimenet hozzáadása a Stream Analytics-feladathoz

1. A **feladatok topológiája** területen válassza a **kimenetek** lehetőséget.

2. A **kimenetek** ablaktáblán válassza a **Hozzáadás** lehetőséget, majd válassza a **Power bi** lehetőséget. A megjelenő képernyőn töltse ki az alábbi mezőket:

   **Kimeneti áljel**: A kimenet egyedi áljele. Ez az oktatóanyag a **contosooutputs** áljelet használja. 

   **Adatkészlet neve**: A Power BI-ban használni kívánt adatkészletnév. Ez az oktatóanyag a **contosodataset** nevet használja. 

   **Tábla neve**: A Power BI-ban használni kívánt táblanév. Ez az oktatóanyag a **contosotable** nevet használja.

  **Hitelesítési mód**: válassza ki a használni kívánt módot.

   A többi mezőnél fogadja el az alapértelmezett beállításokat.

3. Válassza az **Engedélyezés** lehetőséget, majd jelentkezzen be Power bi-fiókjába. (Ez több próbálkozást is igénybe vehet).

   ![A stream Analytics-feladatokhoz tartozó kimenetek beállítása](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-outputs.png)

4. Kattintson a **Mentés** gombra.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>A Stream Analytics-feladat lekérdezésének konfigurálása

1. A **feladatok topológiája** területen válassza a **lekérdezés** lehetőséget.

2. A `[YourInputAlias]` elemet cserélje le a feladat bemeneti áljelére. Ez az oktatóanyag a **contosoinputs** bemeneti áljelet használja.

3. A `[YourOutputAlias]` elemet cserélje le a feladat kimeneti áljelére. Ez az oktatóanyag a **contosooutputs** áljelet használja.

   ![A stream Analytics-feladatokhoz tartozó lekérdezés beállítása](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-query.png)

4. Kattintson a **Mentés** gombra.

5. Zárja be a Lekérdezés panelt. Visszatérhet az erőforráscsoport erőforrásainak nézetéhez. Válassza ki a Stream Analytics feladatot. Ebben az oktatóanyagban a neve: **contosoJob**.

### <a name="run-the-stream-analytics-job"></a>Stream Analytics-feladat futtatása

A stream Analyticsi feladatokban válassza az **Indítás**  >  **most**  >  **Indítás** lehetőséget. Ha a feladat sikeresen elindult, a feladat állapota **Leállítva** értékről **Fut** értékre változik.

A Power BI-jelentés beállításához adatokra van szükség, ezért a Power BI-t az eszköz létrehozása és az eszközszimulációs alkalmazás futtatása után fogja beállítani.

## <a name="run-simulated-device-app"></a>Szimulált eszköz alkalmazásának futtatása

Az oktatóanyag 1. részében beállíthatja, hogy egy eszköz IoT-eszköz használatával legyen szimulálva. Ebben a szakaszban letölti a .NET-konzol alkalmazást, amely az eszközről a felhőbe irányuló üzeneteket küldő eszközt szimulálja egy IoT-hubhoz (feltéve, hogy nem töltötte le az alkalmazást és az erőforrásokat az 1. részben).

Ez az alkalmazás üzeneteket küld a különböző üzenet-útválasztási módszerekhez. Van egy olyan mappa is a letöltésben, amely tartalmazza a teljes Azure Resource Manager sablont és a paramétereket tartalmazó fájlt, valamint az Azure CLI-t és a PowerShell-parancsfájlokat.

Ha nem tölti le a fájlokat az adattárból az oktatóanyag 1. részében, folytassa a [IoT-eszköz szimulációjának](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)letöltésével. A hivatkozás kiválasztásával letölt egy tárházat több alkalmazással; a keresett megoldás a következő: IOT-hub/oktatóanyagok/Routing/IoT_SimulatedDevice. SLN. 

Kattintson duplán a megoldás fájlra (IoT_SimulatedDevice. SLN) a kód megnyitásához a Visual Studióban, majd nyissa meg a Program.cs. Az `{your hub name}` értéket cserélje le az IoT Hub gazdagépnevére. Az IoT Hub gazdagépnevének formátuma: **{iot-hub-name}.azure-devices.net**. Ebben az oktatóanyagban a központ gazdagépneve: **ContosoTestHub.azure-devices.net**. Ezután a `{your device key}` értéket cserélje le az eszközkulcsra, amelyet korábban, a szimulált eszköz beállítása során mentett. 

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

   Ez az eredmény azt jelenti, hogy a következő utasítások igazak. 

   * A Service Bus-üzenetsorra történő irányítás megfelelően működik.
   * Az üzenetet a Service Bus-üzenetsorból lekérő logikai alkalmazás megfelelően működik.
   * A logikai alkalmazás Outlook-összekötője megfelelően működik. 

2. A [Azure Portal](https://portal.azure.com)válassza az **erőforráscsoportok** lehetőséget, majd válassza ki az erőforráscsoportot. Ez az oktatóanyag a **ContosoResources** erőforráscsoportot használja. 

    Válassza ki a Storage-fiókot, válassza a tárolók **lehetőséget, majd** válassza ki a tárolót. Ez az oktatóanyag a **contosoresults** tárolót használja. Megjelenik egy mappa, amelyben lefúrhat a könyvtárakba, amíg egy vagy több fájlt nem lát. Nyissa meg az egyik fájlt. A fájlok a tárfiókhoz irányított bejegyzéseket tartalmazzák. 

   ![Az eredmény fájljai a Storage-ban](./media/tutorial-routing-view-message-routing-results/results-in-storage.png)

Ez az eredmény azt jelenti, hogy a következő utasítás igaz.

   * A tárfiókhoz történő útválasztás megfelelően működik.

Most, hogy az alkalmazás továbbra is fut, állítsa be az Power BI vizualizációt az alapértelmezett útválasztáson keresztül érkező üzenetek megtekintéséhez.

## <a name="set-up-the-power-bi-visualizations"></a>A Power BI-vizualizációk beállítása

1. Jelentkezzen be a [Power BI](https://powerbi.microsoft.com/)-fiókjába.

2. Lépjen a **Munkaterületek** részhez, és válassza ki azt a munkaterületet, amelyet a Stream Analytics-feladat kimenetének létrehozásakor állított be. Ez az oktatóanyag a **My Workspace** nevű munkaterületet használja. 

3. Válassza az **adatkészletek** lehetőséget. Ha nem rendelkezik adatkészletekkel, várjon néhány percet, és ellenőrizze újra.

   Megjelenik az adatkészlet, amelyet a Stream Analytics-feladat kimenetének létrehozásakor adott meg. Ez az oktatóanyag a **contosodataset** nevet használja. (Akár 5-10 percet is igénybe vehet, mire az adatkészlet először megjelenik.)

4. A **műveletek** területen válassza az első ikont a jelentés létrehozásához.

   ![Power BI munkaterület a műveletekkel és jelentés ikonnal kiemelve](./media/tutorial-routing-view-message-routing-results/power-bi-actions.png)

5. Hozzon létre egy vonaldiagramot, amely a valós időben jeleníti meg a hőmérséklet változását.

   * A jelentés létrehozása lapon adjon hozzá egy vonalas diagramot a diagram ikonjának kiválasztásával.

     ![A vizualizációk és mezők](./media/tutorial-routing-view-message-routing-results/power-bi-visualizations-and-fields.png)

   * A **Mezők** panelen bontsa ki Stream Analytics-feladat kimenetének létrehozásakor megadott táblát. Ez az oktatóanyag a **contosotable** nevet használja.

   * Az **EventEnqueuedUtcTime** elemet húzza a **Vizualizációk** panel **Tengely** részére.

   * Húzza a **hőmérséklet** elemet az **Értékek** helyre.

   Létrejön a vonaldiagram. Az X tengely az UTC időzóna szerinti dátumot is időt mutatja. Az Y tengelyen az érzékelőből származó hőmérsékleti adatok láthatók.

6. Hozzon létre egy másik vonaldiagramot, amely a valós időben jeleníti meg a páratartalom változását. A második diagram beállításához hajtsa végre ugyanezt a folyamatot az első diagramon, helyezze a **EventEnqueuedUtcTime** az x tengelyre (**tengelyre**) és a **páratartalmat** az y tengelyen (**értékek**).

   ![Az utolsó Power BI jelentés a két diagrammal](./media/tutorial-routing-view-message-routing-results/power-bi-report.png)

7. Válassza a **Mentés** lehetőséget a jelentés mentéséhez, ha a rendszer kéri, adjon meg egy nevet a jelentés számára.

Az adatoknak mindkét diagramon meg kell jelenniük. Ez az eredmény azt jelenti, hogy a következő utasítások igazak:

   * Az alapértelmezett végpontra történő irányítás megfelelően működik.
   * Az Azure Stream Analytics-feladat streamelése megfelelően működik.
   * A Power BI-vizualizáció megfelelően van beállítva.

A diagramokat a Power BI ablak felső részén található frissítés gombra kattintva frissítheti a legfrissebb információk megjelenítéséhez. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 

Ha el szeretné távolítani az oktatóanyag mindkét részén létrehozott összes Azure-erőforrást, törölje az erőforráscsoportot. Ez a művelet törli a csoportban lévő összes erőforrást. Ebben az esetben eltávolítja az IoT Hubot, a Service Bus-névteret és -üzenetsort, a logikai alkalmazást, a tárfiókot és magát az erőforráscsoportot is. Eltávolíthatja a Power BI erőforrásokat is, és törölheti az oktatóanyag során elküldött e-maileket.

### <a name="clean-up-resources-in-the-power-bi-visualization"></a>Erőforrások eltávolítása a Power BI-vizualizációban

Jelentkezzen be a [Power BI](https://powerbi.microsoft.com/)-fiókjába. Lépjen a munkaterülethez. Ez az oktatóanyag a **My Workspace** nevű munkaterületet használja. Az Power BI vizualizáció eltávolításához lépjen az adatkészletek elemre, és válassza a Kuka ikont az adatkészlet törléséhez. Ez az oktatóanyag a **contosodataset** nevet használja. Az adatkészlet eltávolításakor a jelentés is törlődik.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Erőforrások törlése az Azure CLI használatával

Az erőforráscsoport az [az group delete](/cli/azure/group#az-group-delete) paranccsal távolítható el. `$resourceGroup` az oktatóanyag elején a **ContosoResources** vissza lett állítva.

```azurecli-interactive
az group delete --name $resourceGroup
```

### <a name="use-powershell-to-clean-up-resources"></a>Erőforrások törlése a PowerShell használatával

Az erőforráscsoport eltávolításához használja a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) parancsot. `$resourceGroup` az oktatóanyag elején a **ContosoResources** vissza lett állítva.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroup
```

### <a name="clean-up-test-emails"></a>Tesztelési e-mailek törlése

Előfordulhat, hogy törölni szeretné a Beérkezett üzenetek mappában lévő azon e-mailek mennyiségét is, amelyek a logikai alkalmazáson keresztül jöttek létre, miközben az eszköz fut.

## <a name="next-steps"></a>Következő lépések

Ebben a kétrészes oktatóanyagban megtanulta, hogyan lehet az üzenet-útválasztás használatával átirányítani IoT Hub üzeneteket a különböző célhelyekre az alábbi feladatok végrehajtásával.  

**I. rész: erőforrások létrehozása, üzenetek útválasztásának beállítása**
> [!div class="checklist"]
> * Hozza létre az erőforrásokat – egy IoT hub, egy Storage-fiók, egy Service Bus üzenetsor és egy szimulált eszköz.
> * Konfigurálja a végpontokat és az üzenetek útvonalait IoT Hubban a Storage-fiók és a Service Bus üzenetsor számára.

**II. rész: üzenetek küldése a hubhoz, útválasztásos eredmények megtekintése**
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