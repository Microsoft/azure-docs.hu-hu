---
title: 'Gyors útmutató: egyéni események küldése webes végpontra – Event Grid, Azure Portal'
description: 'Gyors útmutató: Azure Event Grid és Azure Portal használata egyéni témakör közzétételéhez és az adott témakör eseményeire való előfizetéshez. Az eseményeket egy webalkalmazás kezeli.'
ms.date: 07/07/2020
ms.topic: quickstart
ms.openlocfilehash: 592e2d6b7393da8cb55a457b022d6c2358048cfe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96013666"
---
# <a name="quickstart-route-custom-events-to-web-endpoint-with-the-azure-portal-and-event-grid"></a>Gyors útmutató: egyéni események továbbítása webes végponthoz a Azure Portal és Event Grid

Az Azure Event Grid egy felhőalapú eseménykezelési szolgáltatás. Ebben a cikkben létrehozunk egy egyéni témakört az Azure Portallal, feliratkozunk az egyéni témakörre, majd kiváltjuk az eseményt az eredmény megtekintéséhez. Általában olyan végpontoknak szoktunk eseményeket küldeni, amelyek eseményadatokat dolgoznak fel és műveleteket hajtanak végre. A cikk egyszerűsítése érdekében azonban az eseményeket egy olyan webalkalmazásnak küldjük el, amely az üzenetek gyűjtésével és megjelenítésével foglalkozik.

## <a name="prerequisites"></a>Előfeltételek
[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="create-a-custom-topic"></a>Egyéni témakör létrehozása

Az Event Grid-témakörök egy felhasználó által meghatározott végpontot biztosítanak, amelyben közzéteheti az eseményeket. 

1. Jelentkezzen be [Azure Portalba](https://portal.azure.com/).
2. A témakörben található keresősáv mezőbe írja be **Event Grid témaköröket**, majd válassza a **Event Grid témakörök** elemet a legördülő listából. 

    :::image type="content" source="./media/custom-event-quickstart-portal/select-event-grid-topics.png" alt-text="Event Grid témakörök keresése és kiválasztása":::
3. A **Event Grid témakörök** lapon válassza a **+ Hozzáadás** lehetőséget az eszköztáron. 

    :::image type="content" source="./media/custom-event-quickstart-portal/add-event-grid-topic-button.png" alt-text="Event Grid témakör hozzáadása gomb":::
4. A **témakör létrehozása** lapon kövesse az alábbi lépéseket:
    1. Válassza ki az Azure- **előfizetését**.
    2. Válasszon ki egy meglévő erőforráscsoportot, vagy válassza az **új létrehozása** lehetőséget, és adja meg az **erőforráscsoport** **nevét** .
    3. Adjon egyedi **nevet** az egyéni témakörnek. A témakör nevének egyedinek kell lennie, mert a nevet egy DNS-bejegyzés képviseli. Ne a képen látható nevet használja. Ehelyett hozzon létre egy saját nevet – 3-50 karakter közé kell esnie, és csak a-z, A-Z, 0-9 és a "-" értékeket kell tartalmaznia.
    4. Válassza ki az Event Grid-témakör **helyét** .
    5. Kattintson a lap alján található **felülvizsgálat + létrehozás** lehetőségre. 

        :::image type="content" source="./media/custom-event-quickstart-portal/create-custom-topic.png" alt-text="Témakör létrehozása lap":::
    6. A **témakör létrehozása** lap **Áttekintés + létrehozás** lapján válassza a **Létrehozás** lehetőséget. 
    
        :::image type="content" source="./media/custom-event-quickstart-portal/review-create-page.png" alt-text="Beállítások áttekintése és létrehozás":::
5. Miután az üzembe helyezés sikeres volt, írja be újra **Event Grid témaköröket** a keresősávba, és válassza a **Event Grid témakörök** lehetőséget a legördülő listából, ahogy korábban tette. 
6. Válassza ki a listából létrehozott témakört. 

    :::image type="content" source="./media/custom-event-quickstart-portal/select-event-grid-topic.png" alt-text="Válassza ki a témakört a listából":::

7. Megjelenik a témakör **Event Grid témakör** lapja. A lap nyitva tartása. Később a rövid útmutatóban is használhatja. 

    :::image type="content" source="./media/custom-event-quickstart-portal/event-grid-topic-home-page.png" alt-text="Event Grid témakör kezdőlapja":::

## <a name="create-a-message-endpoint"></a>Üzenetvégpont létrehozása
Mielőtt létrehoz egy előfizetést az egyéni témakörhöz, hozzon létre egy végpontot az esemény üzenethez. A végpont általában az eseményadatok alapján hajt végre műveleteket. Az útmutató egyszerűsítése érdekében egy olyan [előre létrehozott webalkalmazást](https://github.com/Azure-Samples/azure-event-grid-viewer) helyezünk üzembe, amely megjeleníti az esemény üzeneteit. Az üzembe helyezett megoldás egy App Service-csomagot, egy App Service-webalkalmazást és egy, a GitHubról származó forráskódot tartalmaz.

1. A cikk lapon válassza az **üzembe helyezés az Azure** -ban lehetőséget a megoldás előfizetésre való telepítéséhez. Az Azure Portalon adjon meg értékeket a paraméterekhez.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"  alt="Button to Deploy to Aquent." /></a>
1. Az üzembe helyezés befejezése eltarthat néhány percig. A sikeres üzembe helyezést követően tekintse meg a webalkalmazást, hogy meggyőződjön annak működéséről. Egy webböngészőben navigáljon a következő helyre: `https://<your-site-name>.azurewebsites.net`.

    Ha a telepítés sikertelen, ellenőrizze a hibaüzenetet. Ennek az lehet az oka, hogy a webhely neve már használatban van. Telepítse újra a sablont, és válasszon másik nevet a hely számára. 
1. A hely látható, de még nem lett közzétéve esemény.

   ![Új hely megtekintése](./media/custom-event-quickstart-portal/view-site.png)

## <a name="subscribe-to-custom-topic"></a>Feliratkozás egyéni témakörre

Az Event Grid-témakörre való feliratkozással lehet tudatni az Event Griddel, hogy mely eseményeket kívánja nyomon követni, és hová szeretné küldeni az eseményeket.

1. Ezután az egyéni témakör **Event Grid témakör** lapján válassza az **+ esemény-előfizetés** lehetőséget az eszköztáron.

    :::image type="content" source="./media/custom-event-quickstart-portal/new-event-subscription.png" alt-text="Esemény-előfizetés hozzáadása gomb":::
2. Az **esemény-előfizetés létrehozása** lapon kövesse az alábbi lépéseket:
    1. Adja meg az esemény-előfizetés **nevét** .
    3. Válassza ki a **végpont típusához** tartozó **webhookot** . 
    4. Válassza **a végpont kiválasztása** lehetőséget. 

        :::image type="content" source="./media/custom-event-quickstart-portal/provide-subscription-values.png" alt-text="Esemény-előfizetés értékeinek megadása":::
    5. A webhook végponthoz adja meg a webalkalmazás URL-címét, és adja hozzá az `api/updates` elemet a kezdőlap URL-címéhez. Válassza a **Kiválasztás megerősítése** lehetőséget.

        :::image type="content" source="./media/custom-event-quickstart-portal/provide-endpoint.png" alt-text="Végpont URL-címének megadása":::
    6. Vissza az **esemény-előfizetés létrehozása** lapon válassza a **Létrehozás** lehetőséget.

3. Tekints meg újra a webalkalmazást, ahol láthatja, hogy az fogadta az előfizetés érvényesítési eseményét. Az eseményadatok kibontásához kattintson a szem ikonra. Az Event Grid elküldi az érvényesítési eseményt, így a végpont megerősítheti, hogy eseményadatokat akar kapni. A webalkalmazás az előfizetés érvényesítéséhez szükséges kódot tartalmaz.

    ![Előfizetési esemény megtekintése](./media/custom-event-quickstart-portal/view-subscription-event.png)

## <a name="send-an-event-to-your-topic"></a>Esemény elküldése a témakörbe

Most aktiváljunk egy eseményt, és lássuk, hogyan küldi el az üzenetet az Event Grid a végpontnak. Az Azure CLI vagy a PowerShell használatával küldjön teszteseményt az egyéni témakörbe. Egy alkalmazás vagy Azure-szolgáltatás általában eseményadatokat küld el.

Az első példa az Azure CLI-t használja. Ez lekéri az egyéni témakör URL-címét és kulcsát, valamint a mintául szolgáló eseményadatokat. A `<topic name>` helyett használja az egyéni témakör nevét. Ez mintául szolgáló eseményadatokat hoz létre. A JSON `data` eleme az esemény hasznos adata. Bármilyen, megfelelően formált JSON megadható ebben a mezőben. A speciális útválasztáshoz és szűréshez használhatja a tárgy mezőt is. A CURL egy olyan segédprogram, amely HTTP-kéréseket küld.


### <a name="azure-cli"></a>Azure CLI
1. A Azure Portal válassza a **Cloud Shell** lehetőséget. A Cloud Shell megnyílik a webböngésző alsó ablaktábláján. 

    :::image type="content" source="./media/custom-event-quickstart-portal/select-cloud-shell.png" alt-text="Cloud Shell ikon kiválasztása":::
1. Válassza a **bash** elemet a Cloud Shell ablak bal felső sarkában. 

    ![Cloud Shell – bash](./media/custom-event-quickstart-portal/cloud-shell-bash.png)
1. A következő parancs futtatásával lekérheti a **végpontot** a témakörhöz: a parancs másolása és beillesztése után frissítse a **témakör nevét** és az **erőforráscsoport nevét** a parancs futtatása előtt. A jelen témakör-végpontra vonatkozó példákat fog közzétenni. 

    ```azurecli
    endpoint=$(az eventgrid topic show --name <topic name> -g <resource group name> --query "endpoint" --output tsv)
    ```
2. Futtassa a következő parancsot az egyéni témakörhöz tartozó **kulcs** lekéréséhez: a parancs másolása és beillesztése után frissítse a **témakör nevét** és az **erőforráscsoport** nevét a parancs futtatása előtt. Ez az Event Grid témakör elsődleges kulcsa. Ha ezt a kulcsot szeretné lekérni a Azure Portal, váltson a **Event Grid témakör** lap **hozzáférési kulcsok** lapjára. Ahhoz, hogy az eseményt egy egyéni témakörbe lehessen elküldeni, szüksége lesz a hozzáférési kulcsra. 

    ```azurecli
    key=$(az eventgrid topic key list --name <topic name> -g <resource group name> --query "key1" --output tsv)
    ```
3. Másolja a következő utasítást az esemény-definícióba, majd nyomja le az **ENTER** billentyűt. 

    ```json
    event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/motorcycles", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "make": "Ducati", "model": "Monster"},"dataVersion": "1.0"} ]'
    ```
4. A következő **curl** -parancs futtatásával tegye közzé az eseményt: a parancsban a `aeg-sas-key` fejléc a korábban kapott hozzáférési kulcsra van beállítva. 

    ```
    curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
    ```

### <a name="azure-powershell"></a>Azure PowerShell
A második példa a PowerShell használatával végez hasonló lépéseket.

1. A Azure Portal válassza a **Cloud Shell** lehetőséget (másik lehetőség: `https://shell.azure.com/` ). A Cloud Shell megnyílik a webböngésző alsó ablaktábláján. 

    :::image type="content" source="./media/custom-event-quickstart-portal/select-cloud-shell.png" alt-text="Cloud Shell ikon kiválasztása":::
1. A **Cloud Shell** a Cloud Shell ablak bal felső sarkában kattintson a **PowerShell** elemre. Tekintse meg a minta **Cloud Shell** ablak rendszerképet az Azure CLI szakaszban.
2. Állítsa be a következő változókat. Az egyes parancsok másolását és beillesztését követően frissítse a **témakör nevét** és az **erőforráscsoport nevét** a parancs futtatása előtt:

    **Erőforráscsoport**:
    ```powershell
    $resourceGroupName = "<resource group name>"
    ```

    **Event Grid témakör neve**:    
    ```powershell
    $topicName = "<topic name>"
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
Ön kiváltotta az eseményt, az Event Grid pedig elküldte az üzenetet a feliratkozáskor konfigurált végpontnak. Tekintse meg a webalkalmazást az imént elküldött esemény megtekintéséhez.

:::image type="content" source="./media/custom-event-quickstart-portal/event-grid-viewer-end.png" alt-text="Event Grid megjelenítője":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha tovább kívánja használni az eseményt, akkor ne törölje a cikkben létrehozott erőforrásokat. Ellenkező esetben törölje a cikkben létrehozott erőforrásokat.

1. Válassza az **erőforráscsoportok** lehetőséget a bal oldali menüben. Ha nem látja a bal oldali menüben, válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, és válassza az **erőforráscsoportok** lehetőséget. 

    ![Erőforráscsoportok](./media/custom-event-quickstart-portal/delete-resource-groups.png)
1. Válassza ki az erőforráscsoportot az **erőforráscsoport** oldal elindításához. 
1. Válassza az **erőforráscsoport törlése** elemet az eszköztáron. 
1. A törlés megerősítéséhez írja be az erőforráscsoport nevét, majd válassza a **Törlés** lehetőséget. 

    A rendszerképben látható másik erőforráscsoportot a Cloud Shell ablak hozta létre és használta. Ha később nem tervezi használni a Cloud Shell ablakot, törölje azt. 

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan hozhat létre egyéni témaköröket és esemény-előfizetéseket, bővebben is tájékozódhat arról, hogy miben nyújthat segítséget az Event Grid:

- [Bevezetés az Event Grid használatába](overview.md)
- [Azure Blob Storage-események átirányítása egyéni webes végpontra](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Virtuális gépek módosításainak monitorozása az Azure Event Grid és a Logic Apps segítségével](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Big Data típusú adatok streamelése adattárházba](event-grid-event-hubs-integration.md)
