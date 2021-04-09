---
title: fájl belefoglalása
description: fájl belefoglalása
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 12/07/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: d6b8b35b36830568d6ff8c46a381fec0cfc57e84
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96854272"
---
:::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/overview.png" alt-text="Az alkalmazás áttekintése":::

Ez az ábra az oktatóanyagban felépített megoldás munkafolyamatát ábrázolja: 

1. Az Azure Event hub-nak elküldett adathalmazok egy Azure Blob Storage-tárolóban vannak rögzítve.
2. Az adatrögzítés befejezése után létrejön egy esemény, amely egy Azure Event gridre lesz küldve. 
3. Az Event Grid továbbítja ezt az eseményt az Azure Function alkalmazásnak.
4. A Function alkalmazás az esemény adatainak blob URL-címét használja a blobnak a tárolóból való lekéréséhez. 
5. A Function alkalmazás áttelepíti a blobot az Azure szinapszis Analytics szolgáltatásba. 

Ebben a cikkben a következő lépéseket hajtja végre:

> [!div class="checklist"]
> - Az oktatóanyaghoz szükséges infrastruktúra üzembe helyezése
> - Kód közzététele egy Functions-alkalmazásra
> - Event Grid-előfizetés létrehozása 
> - Adatstream-mintaadatok a Event Hubsba
> - Rögzített adatértékek ellenőrzése az Azure szinapszis Analyticsben

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

- Azure-előfizetés. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).
- A [Visual studio 2019](https://www.visualstudio.com/vs/) a következő számítási feladatokkal rendelkezik: .net Desktop-fejlesztés, Azure-fejlesztés, ASP.net és webfejlesztés, Node.js fejlesztés és Python-fejlesztés.
- Töltse le a [EventHubsCaptureEventGridDemo minta projektet](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) a számítógépre.
    - WindTurbineDataGenerator – egy egyszerű közzétevő, amely egy minta szélturbina-adatokat küld egy rögzítésre alkalmas Event hubhoz
    - FunctionDWDumper – Egy Azure-függvény, amely Event Grid-értesítést kap, ha az Azure Storage blobba egy Avro-fájlt rögzít a rendszer. Megkapja a blob URI elérési útját, beolvassa a tartalmát, és leküldi ezeket az adatokat az Azure szinapszis Analytics (dedikált SQL-készlet) számára.

## <a name="deploy-the-infrastructure"></a>Az infrastruktúra üzembe helyezése
Ebben a lépésben üzembe helyezi a szükséges infrastruktúrát egy [Resource Manager-sablonnal](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json). A sablon központi telepítésekor a következő erőforrások jönnek létre:

* Az Event hub engedélyezve van a rögzítési funkcióval.
* A rögzített fájlok Storage-fiókja. 
* App Service-csomag a Function app üzemeltetéséhez
* Függvényalkalmazás az esemény feldolgozásához
* SQL Server az adattárház üzemeltetéséhez
* Az áttelepített adat tárolására szolgáló Azure szinapszis Analytics (dedikált SQL-készlet)

### <a name="use-azure-cli-to-deploy-the-infrastructure"></a>Az infrastruktúra üzembe helyezése az Azure CLI használatával

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
2. Kattintson a felül **Cloud Shell** gombra.

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/azure-portal.png" alt-text="Azure Portal":::
3. Megjelenik a böngésző alján megnyíló Cloud Shell.

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/launch-cloud-shell.png" alt-text="Cloud Shell":::
4. Ha a Cloud Shell a **bash** és a **PowerShell** közötti választás lehetőséget látja, válassza a **bash** elemet. 
5. Ha első alkalommal használja a Cloud Shell, hozzon létre egy Storage-fiókot a **tároló létrehozása** lehetőség kiválasztásával. Azure Cloud Shell szükség van egy Azure Storage-fiókra néhány fájl tárolásához. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/create-storage-cloud-shell.png" alt-text="Tároló létrehozása Cloud Shellhoz":::
6. Várjon, amíg a Cloud Shell inicializálása be nem fejeződik. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/cloud-shell-initialized.png" alt-text="Cloud Shell inicializálva":::
1. Hozzon létre egy Azure-erőforráscsoportot az alábbi CLI-parancs futtatásával: 
    1. Másolja és illessze be a következő parancsot a Cloud Shell ablakába. Szükség szerint módosítsa az erőforráscsoport nevét és helyét.

        ```azurecli
        az group create -l eastus -n rgDataMigration
        ```
    2. Nyomja le az **ENTER** billentyűt. 

        Alább bemutatunk egy példát:
    
        ```azurecli
        user@Azure:~$ az group create -l eastus -n rgDataMigration
        {
          "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/rgDataMigration",
          "location": "eastus",
          "managedBy": null,
          "name": "rgDataMigration",
          "properties": {
            "provisioningState": "Succeeded"
          },
          "tags": null
        }
        ```
2. Telepítse az előző szakaszban említett összes erőforrást (Event hub, Storage-fiók, functions alkalmazás, Azure szinapszis Analytics) az alábbi CLI-parancs futtatásával: 
    1. Másolja és illessze be a parancsot a Cloud Shell ablakába. Azt is megteheti, hogy az Ön által választott szerkesztőbe szeretne másolni/beilleszteni, értékeket állít be, majd a parancsot a Cloud Shellba másolja. 

        > [!IMPORTANT]
        > A parancs futtatása előtt a következő entitások értékeit kell megadni: 
        > - A korábban létrehozott erőforráscsoport neve.
        > - Az Event hub-névtér neve. 
        > - Az Event hub neve. Az értéket (hubdatamigration) is meghagyhatja.
        > - Az SQL Server neve.
        > - Az SQL-felhasználó és a jelszó neve. 
        > - Az adatbázis neve.
        > - A Storage-fiók neve. 
        > - A Function alkalmazás neve. 


        ```azurecli
        az deployment group create \
            --resource-group rgDataMigration \
            --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
            --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
        ```
    3.  A parancs futtatásához nyomja le az **ENTER** billentyűt a Cloud Shell ablakban. Ez a folyamat hosszabb időt is igénybe vehet, hiszen egy csomó erőforrást hoz létre. A parancs eredményében ellenőrizze, hogy nincsenek-e hibák. 
1. A Cloud Shell ablak jobb felső sarkában található **Cloud Shell** gombra kattintva válassza ki a Cloud shellt a portál (vagy) **X** gombján. 

### <a name="verify-that-the-resources-are-created"></a>Az erőforrások létrehozásának ellenőrzése

1. A Azure Portal a bal oldali menüben válassza az **erőforráscsoportok** lehetőséget. 
2. Az erőforráscsoportok listájának szűréséhez írja be az erőforráscsoport nevét a keresőmezőbe. 
3. Válassza ki az erőforráscsoportot a listában.

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/select-resource-group.png" alt-text="Az erőforráscsoport kiválasztása":::
4. Győződjön meg arról, hogy az erőforráscsoport következő erőforrásai láthatók:

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/resources-in-resource-group.png" alt-text="Az erőforráscsoporthoz tartozó erőforrások" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/resources-in-resource-group.png":::

### <a name="create-a-table-in-azure-synapse-analytics"></a>Tábla létrehozása az Azure szinapszis Analytics szolgáltatásban
Hozzon létre egy táblázatot az adattárházban a [CreateDataWarehouseTable. SQL](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql) parancsfájl futtatásával. A szkript futtatásához használhatja a Visual studiót vagy a lekérdezés-szerkesztőt a portálon. A következő lépések bemutatják, hogyan használhatja a lekérdezés-szerkesztőt: 

1. Az erőforráscsoport erőforrásainak listájában válassza ki a **DEDIKÁLT SQL-készletet**. 
2. A **DEDIKÁLT SQL-készlet** oldalon, a bal oldali menüben a **gyakori feladatok** szakaszban válassza a **lekérdezéstervező (előzetes verzió)** lehetőséget. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/sql-data-warehouse-page.png" alt-text="Az Azure szinapszis Analytics lapja":::
2. Adja meg az SQL Server **felhasználójának** és **jelszavának** nevét, majd kattintson **az OK gombra**. Ha megjelenik egy üzenet, amely arról tájékoztatja, hogy az ügyfél hozzáférhet az SQL Serverhez, kövesse az alábbi lépéseket:
    1. Válassza ki a hivatkozást: a **kiszolgáló tűzfalának beállítása**. 
    2. A **tűzfalbeállítások** lapon válassza az **ügyfél IP-** címének hozzáadása lehetőséget az eszköztáron, majd kattintson a **Mentés** gombra az eszköztáron. 
    3. Kattintson az **OK gombra** a sikerességi üzenetben.
    4. Váltson vissza a **DEDIKÁLT SQL-készlet** lapra, és válassza a bal oldali menüben a **Lekérdezés-szerkesztő (előzetes verzió)** lehetőséget. 
    5. Adja meg a **felhasználót** és a **jelszót**, majd kattintson **az OK gombra**. 
1. A lekérdezési ablakban másolja és futtassa a következő SQL-parancsfájlt: 

    ```sql
    CREATE TABLE [dbo].[Fact_WindTurbineMetrics] (
        [DeviceId] nvarchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL, 
        [MeasureTime] datetime NULL, 
        [GeneratedPower] float NULL, 
        [WindSpeed] float NULL, 
        [TurbineSpeed] float NULL
    )
    WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    ```

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/run-sql-query.png" alt-text="SQL-lekérdezés futtatása":::
5. Tartsa meg ezt a lapot vagy ablakot úgy, hogy az oktatóanyag végén ellenőrizze, hogy létrejöttek-e az adatgyűjtés. 

### <a name="update-the-function-runtime-version"></a>A függvény futásidejű verziójának frissítése

1. Nyisson meg egy másik lapot a böngészőben, és navigáljon a [Azure Portal](https://portal.azure.com).
1. A Azure Portal a bal oldali menüben válassza az **erőforráscsoportok** lehetőséget.
1. Válassza ki azt az erőforráscsoportot, amelyben a Function alkalmazás létezik. 
1. Válassza ki a **Function alkalmazást** az erőforráscsoport erőforrásainak listájában.
1. A bal oldali menü **Beállítások** területén válassza a **konfiguráció** lehetőséget. 
1. Váltson a **függvény futtatókörnyezet beállításai** lapra a jobb oldali ablaktáblán. 
1. Frissítse a **futtatókörnyezet verzióját** a következőre: **~ 3**. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/function-runtime-version.png" alt-text="A függvény futásidejű verziójának frissítése":::
6. Válassza az eszköztár **Save** (Mentés) elemét. 
1. A **módosítások mentése** megerősítő előugró ablakban válassza a **Folytatás** lehetőséget. 

## <a name="publish-the-azure-functions-app"></a>Az Azure Functions-alkalmazás közzététele

1. Indítsa el a Visual studiót.
2. Nyissa meg a **EventHubsCaptureEventGridDemo. SLN** megoldást, amelyet az előfeltételek részeként letöltött a [githubról](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) . A `/samples/e2e/EventHubsCaptureEventGridDemo` mappában található. 
3. A Megoldáskezelő kattintson a jobb gombbal a **FunctionEGDWDumper** projekt elemre, és válassza a **Közzététel** lehetőséget.
4. Ha a következő képernyő jelenik meg, kattintson a **Start** gombra. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/start-publish-button.png" alt-text="Start gomb a publish (közzététel) szakaszban.":::
5. A **Közzététel** párbeszédpanelen válassza az **Azure** a **cél** számára lehetőséget, majd kattintson a **Tovább gombra**. 
6. Válassza az **Azure függvényalkalmazás (Windows)** lehetőséget, és kattintson a **tovább** gombra.
7. A **functions példány** lapon válassza ki az Azure-előfizetését, bontsa ki az erőforráscsoportot, és válassza ki az alkalmazás lehetőséget, majd kattintson a **Befejezés gombra**. Ha még nem tette meg, be kell jelentkeznie az Azure-fiókjába. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/publish-select-function-app.png" alt-text="Válassza ki a Function alkalmazást":::
8. A **közzétételi** lap **szolgáltatás függőségei** szakaszban válassza a **Konfigurálás** a **tárolóhoz** lehetőséget. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/publish-storage-configure-link.png" alt-text="A tárolási szolgáltatás függőségének beállítása hivatkozás kiválasztása":::
1. Kövesse az alábbi lépéseket a **függőség konfigurálása** lapon: 
    1. Válassza ki a korábban létrehozott **Storage-fiókot** , majd kattintson a **tovább** gombra. 

        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/select-dependency-storage.png" alt-text="Storage-fiók kiválasztása":::
    10. Adja meg **a kapcsolatok karakterláncának nevét**, és válassza a **nincs** lehetőséget a **kapcsolatok karakterláncának mentése** beállításnál, majd kattintson a **tovább** gombra. 
    
        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/dependency-storage-connection-string.png" alt-text="Adja meg a kapcsolatok karakterláncának nevét":::      
    1. Törölje a **C#-programkód** és a **Secrets Store** beállítást, majd kattintson a **Befejezés gombra**.  
    
        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/dependency-storage-changes-summary.png" alt-text="Változások összefoglalásának áttekintése":::
1. Miután a Visual Studio konfigurálta a profilt, kattintson a **Közzététel** elemre.

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/select-publish.png" alt-text="Közzététel kiválasztása":::
2. Az **Azure-függvényt** tartalmazó lapon nyissa meg a bal oldali menüben a  **függvények** elemet. Győződjön meg arról, hogy a **EventGridTriggerMigrateData** függvény megjelenik a listában. Ha nem látja, próbálkozzon újra a Visual studióból való közzétételsel, majd frissítse az oldalt a portálon. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/confirm-function-creation.png" alt-text="Függvény létrehozásának megerősítése":::    

A függvény közzététele után feliratkozhat az eseményre.

## <a name="subscribe-to-the-event"></a>Feliratkozás az eseményre

1. A webböngésző új lapján vagy új ablakában navigáljon a [Azure Portal](https://portal.azure.com).
2. A Azure Portal a bal oldali menüben válassza az **erőforráscsoportok** lehetőséget. 
3. Az erőforráscsoportok listájának szűréséhez írja be az erőforráscsoport nevét a keresőmezőbe. 
4. Válassza ki az erőforráscsoportot a listában.
1. Válassza ki a **Event Hubs névteret** az erőforrások listájából.
1. A **Event Hubs névtér** lapon válassza a bal oldali menü **események** elemét, majd válassza az eszköztár **+ esemény előfizetése** elemét. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/event-hub-add-subscription-link.png" alt-text="Esemény-előfizetés hivatkozás hozzáadása egy Event Hubs névtér események lapján":::
1. Az **esemény-előfizetés létrehozása** lapon kövesse az alábbi lépéseket:
    1. Adja meg az esemény- **előfizetés** nevét. 
    1. Adja meg a **rendszer témakör** nevét. A rendszertémakör egy végpontot biztosít a küldő számára az események küldéséhez. További információ: [rendszertémakörök](../articles/event-grid/system-topics.md)
    1. A **végpont típusa** beállításnál válassza az **Azure-függvény** lehetőséget.
    1. A **végpont** mezőben válassza ki a hivatkozást.
    1. Az **Azure-függvény kiválasztása** lapon kövesse az alábbi lépéseket, ha nincsenek automatikusan kitöltve.
        1. Válassza ki az Azure-függvényt tartalmazó Azure-előfizetést. 
        1. Válassza ki a függvényhez tartozó erőforráscsoportot. 
        1. Válassza ki a Function alkalmazást.
        1. Válassza ki az üzembe helyezési tárolóhelyet. 
        1. Válassza ki a **EventGridTriggerMigrateData** függvényt. 
    1. Az **Azure-függvény kiválasztása** lapon válassza a **kijelölés megerősítése** lehetőséget.
    1. Ezután vissza az esemény- **előfizetés létrehozása** lapon válassza a **Létrehozás** lehetőséget. 
    
        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/event-subscription-select-function.png" alt-text="Esemény-előfizetés létrehozása a függvény használatával" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/event-subscription-select-function.png":::
1. Ellenőrizze, hogy létrejött-e az esemény-előfizetés. Váltson az **esemény-előfizetések** lapra az Event Hubs névtér **események** lapján. 
    
    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/confirm-event-subscription.png" alt-text="Esemény-előfizetés megerősítése" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/confirm-event-subscription.png":::
1. Válassza ki a App Service tervet (nem az App Service) az erőforráscsoport erőforrásainak listájában. 

## <a name="run-the-app-to-generate-data"></a>Az alkalmazás futtatása az adatok létrehozásához
Elkészült az Event hub beállításával, az SQL-készlet (korábban SQL Data Warehouse), az Azure Function app és az Event előfizetés kiosztásával. Mielőtt futtatna egy alkalmazást, amely adatokat állít elő az eseményközpont számára, konfigurálnia kell néhány értéket.

1. A Azure Portalban navigáljon az erőforráscsoporthoz, mint korábban. 
2. Válassza ki a Event Hubs névteret.
3. A **Event Hubs névtér** lapon válassza a bal oldali menüben a **megosztott elérési házirendek** elemet.
4. A szabályzatok listájában válassza a **RootManageSharedAccessKey** lehetőséget. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/event-hub-namespace-shared-access-policies.png" alt-text="Event Hubs névtér megosztott elérési házirendjeinek lapja":::    
1. Kattintson a **kapcsolódási karakterlánc – elsődleges kulcs** szövegmező melletti Másolás gombra. 
1. Térjen vissza a Visual Studio-megoldáshoz. 
1. Kattintson a jobb gombbal a **WindTurbineDataGenerator** projekt elemre, és válassza **a beállítás indítási projektként** lehetőséget. 
1. A WindTurbineDataGenerator projektben nyissa meg a **program.cs** fájlt.
1. Cserélje le a `<EVENT HUBS NAMESPACE CONNECTION STRING>` elemet a portálról másolt kapcsolatok karakterláncra. 
1. A helyére írja be az `<EVENT HUB NAME>` Event hub nevét. 

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```
6. Hozza létre a megoldást. Futtassa a **WindTurbineGenerator.exe** alkalmazást. 
7. Néhány perc elteltével a másik böngésző lapon megnyithatja a lekérdezési ablakot, és lekérdezheti az áttelepített adattárházban található táblázatot.

    ```sql
    select * from [dbo].[Fact_WindTurbineMetrics]    
    ```

    ![Lekérdezés eredményei](media/event-grid-event-hubs-functions-synapse-analytics/query-results.png)

## <a name="monitor-the-solution"></a>A megoldás figyelése
Ez a szakasz segít a megoldás figyelésében és hibaelhárításában. 

### <a name="view-captured-data-in-the-storage-account"></a>Rögzített adattárolás megtekintése a Storage-fiókban
1. Navigáljon az erőforráscsoporthoz, és válassza ki az események rögzítéséhez használt Storage-fiókot. 
1. A **Storage-fiók** lapon válassza a bal oldali menüben a **Storage Explorer (előzetes** verzió) lehetőséget.
1. Bontsa ki a **blob-tárolók** csomópontot, majd válassza a **windturbinecapture** 
1. Nyissa meg az azonos nevű mappát a jobb oldali ablaktáblán található **Event Hubs névtérrel** . 
1. Nyissa meg a (z) nevű mappát az Event hub-ban (**hubdatamigration**). 
1. A mappákban megjelenő részletezéssel megtekintheti a AVRO-fájlokat. Bemutatunk egy példát:

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/storage-captured-file.png" alt-text="Rögzített fájl a tárolóban" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/storage-captured-file.png":::
    

### <a name="verify-that-the-event-grid-trigger-invoked-the-function"></a>Ellenőrizze, hogy a Event Grid eseményindító meghívja-e a függvényt.
1. Navigáljon az erőforráscsoporthoz, és válassza ki a Function alkalmazást. 
1. Válassza a bal oldali menü **függvények** elemét.
1. Válassza ki a **EventGridTriggerMigrateData** függvényt a listából. 
1. A **függvény** lapon válassza a bal oldali menü **monitor** elemét. 
1. Válassza a **Konfigurálás** lehetőséget az Application-elemzések konfigurálásához a Meghívási naplók rögzítéséhez. 
1. Hozzon létre egy új **Application Insights** -erőforrást, vagy használjon egy meglévő erőforrást. 
1. Váltson vissza a függvény **figyelő** lapjára. 
1. Ellenőrizze, hogy az eseményeket küldő ügyfélalkalmazás (**WindTurbineDataGenerator**) továbbra is fut-e. Ha nem, futtassa az alkalmazást. 
1. Várjon néhány percet (5 perc vagy több), majd a **refresh (frissítés** ) gombra kattintva megtekintheti a függvények hívásait.    

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/function-invocations.png" alt-text="Függvény meghívása":::
1. Válassza ki a meghívást a részletek megtekintéséhez.

    Az Event Grid elküldi az eseményadatokat az előfizetőknek. Az alábbi példa azokat az eseményeket mutatja be, amelyek akkor jönnek létre, amikor az Event hub-on keresztül történő adatátvitelt egy blobban rögzíti. Figyelje meg, hogy az `fileUrl` objektum tulajdonsága a `data` tárolóban lévő blobra mutat. A Function alkalmazás ezt az URL-címet használja a rögzített adattal rendelkező blob-fájl lekéréséhez.

    ```json
    {
        "topic": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourcegroups/rgDataMigration/providers/Microsoft.EventHub/namespaces/spehubns1207",
        "subject": "hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "id": "4538f1a5-02d8-4b40-9f20-36301ac976ba",
        "data": {
            "fileUrl": "https://spehubstorage1207.blob.core.windows.net/windturbinecapture/spehubns1207/hubdatamigration/0/2020/12/07/21/49/12.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "0",
            "sizeInBytes": 473444,
            "eventCount": 2800,
            "firstSequenceNumber": 55500,
            "lastSequenceNumber": 58299,
            "firstEnqueueTime": "2020-12-07T21:49:12.556Z",
            "lastEnqueueTime": "2020-12-07T21:50:11.534Z"
        },
        "dataVersion": "1",
        "metadataVersion": "1",
        "eventTime": "2020-12-07T21:50:12.7065524Z"
    }
    ```

### <a name="verify-that-the-data-is-stored-in-the-dedicated-sql-pool"></a>Annak ellenőrzése, hogy az adat a dedikált SQL-készletben van-e tárolva
A böngészőablakban, ahol meg van nyitva a lekérdezési ablak, kérdezheti le a táblát az áttelepített adathoz tartozó dedikált SQL-készletben.

![Lekérdezés eredményei](media/event-grid-event-hubs-functions-synapse-analytics/query-results.png)

