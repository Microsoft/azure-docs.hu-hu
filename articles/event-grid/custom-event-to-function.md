---
title: 'Gyors útmutató: egyéni események küldése az Azure-függvénynek – Event Grid'
description: 'Rövid útmutató: a Azure Event Grid és az Azure CLI vagy a portál használatával tehet közzé egy témakört, és feliratkozhat erre az eseményre. A végponthoz egy Azure-függvény van használatban.'
ms.date: 07/07/2020
ms.topic: quickstart
ms.openlocfilehash: 4fe4753de41443a0537636933364c7b69b25cb27
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791727"
---
# <a name="quickstart-route-custom-events-to-an-azure-function-with-event-grid"></a>Gyors útmutató: egyéni események átirányítása egy Azure-függvénybe Event Grid

Az Azure Event Grid egy felhőalapú eseménykezelési szolgáltatás. Azure Functions az egyik támogatott eseménykezelő. Ebben a cikkben létrehozunk egy egyéni témakört az Azure Portallal, feliratkozunk az egyéni témakörre, majd kiváltjuk az eseményt az eredmény megtekintéséhez. Az eseményeket egy Azure-függvénynek küldi el.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-azure-function"></a>Azure-függvény létrehozása
Az egyéni témakörre való feliratkozás előtt hozzon létre egy függvényt az események kezeléséhez. 

1. Hozzon létre egy Function alkalmazást a [Function-alkalmazás létrehozása](../azure-functions/functions-get-started.md)című témakör utasításai alapján.
2. Hozzon létre egy függvényt a **Event Grid trigger** használatával. Ha először használja ezt az triggert, akkor a bővítmény telepítéséhez a telepítés gombra kell kattintania.
    1. A **függvényalkalmazás** lapon válassza a bal oldali menü **függvények** elemét, keresse meg a **Event Grid** a sablonokban, majd válassza a **Azure Event Grid trigger** lehetőséget. 

        :::image type="content" source="./media/custom-event-to-function/function-event-grid-trigger.png" alt-text="Event Grid trigger kiválasztása":::
3. Az **új függvény** lapon adja meg a függvény nevét, majd válassza a **create Function (függvény létrehozása**) lehetőséget.

    :::image type="content" source="./media/custom-event-to-function/new-function-page.png" alt-text="Új függvény lap":::
4. A **kód + teszt** lapon megtekintheti a függvény meglévő kódját, és frissítheti azt. 

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="create-a-custom-topic"></a>Egyéni témakör létrehozása

Az Event Grid-témakörök egy felhasználó által meghatározott végpontot biztosítanak, amelyben közzéteheti az eseményeket. 

1. Jelentkezzen be [Azure Portalba](https://portal.azure.com/).
2. Válassza a **minden szolgáltatás** lehetőséget a bal oldali navigációs menüben, keresse meg a **Event Grid**, és válassza ki **Event Grid témákat**. 

    ![Event Grid témakörök kiválasztása](./media/custom-event-to-function/select-event-grid-topics.png)
3. A **Event Grid témakörök** lapon válassza a **+ Hozzáadás** lehetőséget az eszköztáron. 

    ![Event Grid témakör hozzáadása gomb](./media/custom-event-to-function/add-event-grid-topic-button.png)

4. A **témakör létrehozása** lapon kövesse az alábbi lépéseket:

    1. Adjon egyedi **nevet** az egyéni témakörnek. A témakör nevének egyedinek kell lennie, mert a nevet egy DNS-bejegyzés képviseli. Ne a képen látható nevet használja. Ehelyett hozzon létre egy saját nevet – 3-50 karakter közé kell esnie, és csak a-z, A-Z, 0-9 és a "-" értékeket kell tartalmaznia.
    2. Válassza ki az Azure- **előfizetését**.
    3. Válassza ki ugyanazt az erőforráscsoportot az előző lépésekben.
    4. Válassza ki az Event Grid-témakör **helyét** .
    5. Tartsa meg az alapértelmezett értéket **Event Grid sémát** az **esemény sémája** mezőhöz. 

       ![Témakör létrehozása lap](./media/custom-event-to-function/create-custom-topic.png)
    6. Válassza a **Létrehozás** lehetőséget. 

5. Az egyéni témakör létrehozása után láthatja a sikeres műveletről szóló értesítést. Válassza **az Ugrás az erőforrás-csoportba** lehetőséget. 

   ![Sikeres műveletről szóló értesítés megtekintése](./media/custom-event-to-function/success-notification.png)

6. Az **erőforráscsoport** lapon válassza az Event Grid-témakört. 

   ![Az Event Grid-témakör erőforrásának kiválasztása](./media/custom-event-to-function/select-event-grid-topic.png)

7. Megjelenik az Event Grid **Event Grid témakör** lapja. A lap nyitva tartása. Később a rövid útmutatóban is használhatja. 

    ![Event Grid témakör kezdőlapja](./media/custom-event-to-function/event-grid-topic-home-page.png)

## <a name="subscribe-to-custom-topic"></a>Feliratkozás egyéni témakörre

Az Event Grid-témakörre való feliratkozással lehet tudatni az Event Griddel, hogy mely eseményeket kívánja nyomon követni, és hová szeretné küldeni az eseményeket.

1. Ezután az egyéni témakör **Event Grid témakör** lapján válassza az **+ esemény-előfizetés** lehetőséget az eszköztáron.

   ![Esemény-előfizetés hozzáadása](./media/custom-event-to-function/new-event-subscription.png)

2. Az **esemény-előfizetés létrehozása** lapon kövesse az alábbi lépéseket:
    1. Adja meg az esemény-előfizetés **nevét** .
    3. Válassza ki az **Azure-függvényt** a **végpont típusaként**. 
    4. Válassza **a végpont kiválasztása** lehetőséget. 

       ![Esemény-előfizetés értékeinek megadása](./media/custom-event-to-function/provide-subscription-values.png)

    5. A függvény végpontján válassza ki az Azure-előfizetést és az erőforráscsoportot a függvényalkalmazás, majd válassza ki a korábban létrehozott függvényalkalmazás és függvényt. Válassza a **Kiválasztás megerősítése** lehetőséget.

       ![Végpont URL-címének megadása](./media/custom-event-to-function/provide-endpoint.png)
    6. Ez a lépés nem kötelező, de ajánlott éles környezetekben. Az **esemény-előfizetés létrehozása** lapon váltson a **speciális szolgáltatások** lapra, és állítsa be a **kötegek maximális száma** és az **előnyben részesített köteg mérete kilobájtban** értéket. 
    
        A kötegelt feldolgozás magas átviteli sebességet biztosít. A **másodpercenkénti események** maximális száma beállításnál állítsa be, hogy az előfizetés hány eseményt tartalmazzon egy kötegben. Az előnyben részesített batch-méret a Batch méretének előnyben részesített felső határát adja meg kilogramm bájtban, de túlléphető, ha egyetlen esemény nagyobb ennél a küszöbértéknél.
    
        :::image type="content" source="./media/custom-event-to-function/enable-batching.png" alt-text="Kötegelt feldolgozás engedélyezése":::
    6. Az **esemény-előfizetés létrehozása** lapon válassza a **Létrehozás** lehetőséget.

## <a name="send-an-event-to-your-topic"></a>Esemény elküldése a témakörbe

Most aktiváljunk egy eseményt, és lássuk, hogyan küldi el az üzenetet az Event Grid a végpontnak. Az Azure CLI vagy a PowerShell használatával küldjön teszteseményt az egyéni témakörbe. Egy alkalmazás vagy Azure-szolgáltatás általában eseményadatokat küld el.

Az első példa az Azure CLI-t használja. Ez lekéri az egyéni témakör URL-címét és kulcsát, valamint a mintául szolgáló eseményadatokat. A `<topic name>` helyett használja az egyéni témakör nevét. Ez mintául szolgáló eseményadatokat hoz létre. A JSON `data` eleme az esemény hasznos adata. Bármilyen, megfelelően formált JSON megadható ebben a mezőben. A speciális útválasztáshoz és szűréshez használhatja a tárgy mezőt is. A CURL egy olyan segédprogram, amely HTTP-kéréseket küld.


### <a name="azure-cli"></a>Azure CLI
1. A Azure Portal válassza a **Cloud Shell** lehetőséget. Válassza a **bash** elemet a Cloud Shell ablak bal felső sarkában. 

    ![Cloud Shell – bash](./media/custom-event-quickstart-portal/cloud-shell-bash.png)
1. A következő parancs futtatásával lekérheti a **végpontot** a témakörhöz: a parancs másolása és beillesztése után frissítse a **témakör nevét** és az **erőforráscsoport nevét** a parancs futtatása előtt. 

    ```azurecli
    endpoint=$(az eventgrid topic show --name <topic name> -g <resource group name> --query "endpoint" --output tsv)
    ```
2. Futtassa a következő parancsot az egyéni témakörhöz tartozó **kulcs** lekéréséhez: a parancs másolása és beillesztése után frissítse a **témakör nevét** és az **erőforráscsoport** nevét a parancs futtatása előtt. 

    ```azurecli
    key=$(az eventgrid topic key list --name <topic name> -g <resource group name> --query "key1" --output tsv)
    ```
3. Másolja a következő utasítást az esemény-definícióba, majd nyomja le az **ENTER** billentyűt. 

    ```json
    event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/motorcycles", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "make": "Ducati", "model": "Monster"},"dataVersion": "1.0"} ]'
    ```
4. A következő **curl** -parancs futtatásával tegye közzé az eseményt:

    ```
    curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
    ```

### <a name="azure-powershell"></a>Azure PowerShell
A második példa a PowerShell használatával végez hasonló lépéseket.

1. A Azure Portal válassza a **Cloud Shell** lehetőséget (másik lehetőség: `https://shell.azure.com/` ). Válassza a **PowerShell** elemet a Cloud Shell ablak bal felső sarkában. Tekintse meg a minta **Cloud Shell** ablak rendszerképet az Azure CLI szakaszban.
2. Állítsa be a következő változókat. Az egyes parancsok másolását és beillesztését követően frissítse a **témakör nevét** és az **erőforráscsoport nevét** a parancs futtatása előtt:

    ```powershell
    $resourceGroupName = <resource group name>
    $topicName = <topic name>
    ```
3. A következő parancsok futtatásával lekérheti a **végpontot** és a **kulcsokat** a témakörhöz:

    ```powershell
    $endpoint = (Get-AzEventGridTopic -ResourceGroupName $resourceGroupName -Name $topicName).Endpoint
    $keys = Get-AzEventGridTopicKey -ResourceGroupName $resourceGroupName -Name $topicName
    ```
4. Készítse elő az eseményt. Másolja és futtassa az utasításokat a Cloud Shell ablakban. 

    ```powershell
    $eventID = Get-Random 99999

    #Date format should be SortableDateTimePattern (ISO 8601)
    $eventDate = Get-Date -Format s

    #Construct body using Hashtable
    $htbody = @{
        id= $eventID
        eventType="recordInserted"
        subject="myapp/vehicles/motorcycles"
        eventTime= $eventDate   
        data= @{
            make="Ducati"
            model="Monster"
        }
        dataVersion="1.0"
    }
    
    #Use ConvertTo-Json to convert event body from Hashtable to JSON Object
    #Append square brackets to the converted JSON payload since they are expected in the event's JSON payload syntax
    $body = "["+(ConvertTo-Json $htbody)+"]"
    ```
5. Az esemény elküldéséhez használja a **meghívás-webkérés** parancsmagot. 

    ```powershell
    Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
    ```

### <a name="verify-in-the-event-grid-viewer"></a>Ellenőrzés a Event Grid-megjelenítőben
Ön kiváltotta az eseményt, az Event Grid pedig elküldte az üzenetet a feliratkozáskor konfigurált végpontnak. Navigáljon a Event Grid aktivált függvényhez, és nyissa meg a naplókat. Ekkor meg kell jelennie az esemény adattartalmának egy másolatának a naplókban. Ha nem biztos benne, hogy először nyitja meg a naplók ablakot, vagy nyomja meg az újrakapcsolódást, majd próbálja meg újból elküldeni a teszt eseményt.

![Sikeres függvény-trigger naplója](./media/custom-event-to-function/successful-function.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha tovább kívánja használni az eseményt, akkor ne törölje a cikkben létrehozott erőforrásokat. Ellenkező esetben törölje a cikkben létrehozott erőforrásokat.

1. Válassza az **erőforráscsoportok** lehetőséget a bal oldali menüben. Ha nem látja a bal oldali menüben, válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, és válassza az **erőforráscsoportok** lehetőséget. 
2. Válassza ki az erőforráscsoportot az **erőforráscsoport** oldal elindításához. 
3. Válassza az **erőforráscsoport törlése** elemet az eszköztáron. 
4. A törlés megerősítéséhez írja be az erőforráscsoport nevét, majd válassza a **Törlés** lehetőséget. 

    ![Erőforráscsoportok](./media/custom-event-to-function/delete-resource-groups.png)

    A rendszerképben látható másik erőforráscsoportot a Cloud Shell ablak hozta létre és használta. Ha később nem tervezi használni a Cloud Shell ablakot, törölje azt. 

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerkedett vele, hogyan hozhat létre témaköröket és eseményfeliratkozásokat, bővebben is tájékozódhat arról, hogy miben nyújthat segítséget az Event Grid:

- [Bevezetés az Event Grid használatába](overview.md)
- [Azure Blob Storage-események átirányítása egyéni webes végpontra](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Virtuális gépek módosításainak monitorozása az Azure Event Grid és a Logic Apps segítségével](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Big Data típusú adatok streamelése adattárházba](event-grid-event-hubs-integration.md)
