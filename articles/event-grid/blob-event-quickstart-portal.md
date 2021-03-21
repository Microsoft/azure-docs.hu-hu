---
title: 'Gyors útmutató: blob Storage-események küldése a webes végpontnak – portál'
description: 'Gyors útmutató: Azure Event Grid és Azure Portal használatával hozzon létre blob Storage-fiókot, és regisztrálja az eseményeit. Küldje el az eseményeket egy webhookba.'
ms.date: 07/07/2020
ms.topic: quickstart
ms.openlocfilehash: 46cd88558334239a1a9971c63b8b2608def3c4d3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96005690"
---
# <a name="quickstart-route-blob-storage-events-to-web-endpoint-with-the-azure-portal"></a>Gyors útmutató: blob Storage-események átirányítása webes végpontra a Azure Portal

Az Azure Event Grid egy felhőalapú eseménykezelési szolgáltatás. Ebben a cikkben létrehoz egy Blob Storage-fiókot az Azure Portallal, feliratkozik a Blob Storage-hoz tartozó eseményekre, és aktivál egy eseményt az eredmény megtekintéséhez. Általában olyan végpontoknak szoktunk eseményeket küldeni, amelyek eseményadatokat dolgoznak fel és műveleteket hajtanak végre. A cikk egyszerűsítése érdekében azonban az eseményeket egy olyan webalkalmazásnak küldjük el, amely az üzenetek gyűjtésével és megjelenítésével foglalkozik.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

A folyamat végén látni fogja, hogy a rendszer elküldte az eseményadatokat a webalkalmazásnak.

![Eredmények megtekintése](./media/blob-event-quickstart-portal/view-results.png)

## <a name="create-a-storage-account"></a>Tárfiók létrehozása

1. Jelentkezzen be [Azure Portalba](https://portal.azure.com/).

1. Blob Storage létrehozásához kattintson az **Erőforrás létrehozása** elemre. 

1. A **Storage** elemet kiválasztva szűrje az elérhető lehetőségeket, majd válassza a **Storage-fiók – blob, fájl, táblázat, üzenetsor** lehetőséget.

   ![Tárhely kiválasztása](./media/blob-event-quickstart-portal/create-storage.png)

   Az eseményekre feliratkozáshoz vagy egy általános célú v2 tárfiókot vagy Blob Storage-fiókot hozzon létre.
   
1. A **Storage-fiók létrehozása** oldalon hajtsa végre a következő lépéseket:
    1. Válassza ki Azure-előfizetését. 
    2. **Erőforráscsoport** esetén hozzon létre egy új erőforráscsoportot, vagy válasszon ki egy meglévőt. 
    3. Adja meg a tárfiók nevét. 
    4. Válassza az **Áttekintés + létrehozás** lehetőséget. 

       ![Kezdeti lépések](./media/blob-event-quickstart-portal/provide-blob-values.png)    
    5. A **felülvizsgálat + létrehozás** oldalon tekintse át a beállításokat, majd kattintson a **Létrehozás** gombra. 

        >[!NOTE]
        > Csak a **StorageV2 (általános célú v2)** és a **BlobStorage** típusú tárolási fiókok támogatják az események integrálását. A **Storage (Genral Purpose v1)** *nem* támogatja a Event Grid integrációját.

## <a name="create-a-message-endpoint"></a>Üzenetvégpont létrehozása

A Blob Storage-eseményekre való feliratkozás előtt hozzuk létre az eseményüzenet végpontját. A végpont általában az eseményadatok alapján hajt végre műveleteket. Az útmutató egyszerűsítése érdekében egy olyan [előre létrehozott webalkalmazást](https://github.com/Azure-Samples/azure-event-grid-viewer) helyezünk üzembe, amely megjeleníti az esemény üzeneteit. Az üzembe helyezett megoldás egy App Service-csomagot, egy App Service-webalkalmazást és egy, a GitHubról származó forráskódot tartalmaz.

1. A megoldásnak az előfizetésébe való telepítéséhez válassza az **Üzembe helyezés az Azure-ban** lehetőséget. 

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png" alt="Button to Deploy to Aquent." /></a>
2. Az **Egyéni telepítés** oldalon hajtsa végre a következő lépéseket: 
    1. Az **erőforráscsoport** mezőben válassza ki a Storage-fiók létrehozásakor létrehozott erőforráscsoportot. Az oktatóanyag törlésével megkönnyíti a tisztítást, miután az erőforráscsoportot törli.  
    2. A **hely neve** mezőben adja meg a webalkalmazás nevét.
    3. A **üzemeltetési terv neve** mezőben adja meg a webalkalmazás üzemeltetéséhez használni kívánt app Service-csomag nevét.
    4. Jelölje be az Elfogadom a **fenti feltételeket és kikötéseket** jelölőnégyzetet. 
    5. Válassza a **Beszerzés** lehetőséget. 

       ![Központi telepítési paraméterek](./media/blob-event-quickstart-portal/template-deploy-parameters.png)
1. Az üzembe helyezés befejezése eltarthat néhány percig. Válassza a riasztások (harang ikon) lehetőséget a portálon, majd válassza az **Ugrás az erőforrás-csoportba** lehetőséget. 

    ![Riasztás – navigáljon az erőforráscsoporthoz](./media/blob-event-quickstart-portal/navigate-resource-group.png)
4. Az **erőforráscsoport** lap erőforrások listájában válassza ki a létrehozott webalkalmazást. Ezen a listán a App Service terv és a Storage-fiók is látható. 

    ![Webhely kiválasztása](./media/blob-event-quickstart-portal/resource-group-resources.png)
5. A webalkalmazás **app Service** oldalán válassza ki azt az URL-címet, amellyel navigálni szeretne a webhelyhez. Az URL-címnek a következő formátumúnak kell lennie: `https://<your-site-name>.azurewebsites.net` .
    
    ![Navigáljon a webhelyre](./media/blob-event-quickstart-portal/web-site.png)

6. Győződjön meg arról, hogy a hely látható, de még nem adtak hozzá eseményeket.

   ![Új hely megtekintése](./media/blob-event-quickstart-portal/view-site.png)

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-the-blob-storage"></a>Feliratkozás a Blob Storage-ra

A témakörre való feliratkozással lehet tudatni az Event Griddel, hogy mely eseményeket kívánja nyomon követni, és hová szeretné küldeni az eseményeket.

1. A portálon navigáljon a korábban létrehozott Azure Storage-fiókhoz. A bal oldali menüben válassza a **minden erőforrás** lehetőséget, és válassza ki a Storage-fiókját. 
2. A **Storage-fiók** lapon válassza a bal oldali menü **események** elemét. 
1. Válassza a **További beállítások**, majd a **Webhook** elemet. Eseményeket küld a megjelenítői alkalmazásnak a végponthoz tartozó webes Hook használatával. 

   ![Webhook kiválasztása](./media/blob-event-quickstart-portal/select-web-hook.png)
3. Az **esemény-előfizetés létrehozása** oldalon hajtsa végre a következő lépéseket: 
    1. Adja meg az esemény-előfizetés **nevét** .
    2. Adja meg a **rendszer témakör** **nevét** . A rendszertémakörökről a [rendszertémakörök áttekintésében](system-topics.md)talál további információt.

       ![Adja meg az esemény-előfizetés és a rendszer témakör nevét](./media/blob-event-quickstart-portal/event-subscription-name-system-topic.png)
    2. Válassza a **webes Hook** lehetőséget a **végpont típusához**. 

       ![Webes Hook-végpont típusának kiválasztása](./media/blob-event-quickstart-portal/select-web-hook-end-point-type.png)
4. A **végpont** esetében kattintson a **válasszon egy végpontot** lehetőségre, és adja meg a webalkalmazás URL-címét, és adja hozzá `api/updates` a Kezdőlap URL-címét (például: `https://spegridsite.azurewebsites.net/api/updates` ), majd válassza a **kijelölés megerősítése** lehetőséget.

   ![Végpont kijelölésének megerősítése](./media/blob-event-quickstart-portal/confirm-endpoint-selection.png)
5. Most az esemény- **előfizetés létrehozása** lapon válassza a **Létrehozás** lehetőséget az esemény-előfizetés létrehozásához. 

   ![Naplók kiválasztása](./media/blob-event-quickstart-portal/create-subscription.png)

1. Tekints meg újra a webalkalmazást, ahol láthatja, hogy az fogadta az előfizetés érvényesítési eseményét. Az eseményadatok kibontásához kattintson a szem ikonra. Az Event Grid elküldi az érvényesítési eseményt, így a végpont megerősítheti, hogy eseményadatokat akar kapni. A webalkalmazás az előfizetés érvényesítéséhez szükséges kódot tartalmaz.

   ![Előfizetési esemény megtekintése](./media/blob-event-quickstart-portal/view-subscription-event.png)

Most aktiváljunk egy eseményt, és lássuk, hogyan küldi el az üzenetet az Event Grid a végpontnak.

## <a name="send-an-event-to-your-endpoint"></a>Esemény elküldése a végpontra

A Blob Storage-hoz egy eseményt egy fájl feltöltésével aktiválhat. A fájlnak nem kell tartalommal rendelkeznie. A cikkek feltételezik, hogy van egy testfile.txt fájlja, de bármilyen fájlt használhat.

1. A Azure Portal navigáljon a blob Storage-fiókjához, és válassza a **tárolók** lehetőséget az **Áttekintés** oldalon.

   ![Blobok kiválasztása](./media/blob-event-quickstart-portal/select-blobs.png)

1. Válassza a **+ Tároló** lehetőséget. Adja meg a tároló nevét, és használja az összes hozzáférési szintet, majd válassza a **Létrehozás** lehetőséget. 

   ![Tároló hozzáadása](./media/blob-event-quickstart-portal/add-container.png)

1. Válassza ki az új tárolót.

   ![Tároló kiválasztása](./media/blob-event-quickstart-portal/select-container.png)

1. Fájl feltöltéséhez válassza a **Feltöltés** lehetőséget. A **blob feltöltése** lapon keresse meg és válassza ki a tesztelni kívánt fájlt, majd válassza a **feltöltés** lehetőséget a lapon. 

   ![Feltöltés kiválasztása](./media/blob-event-quickstart-portal/upload-file.png)

1. Keresse meg tallózással a tesztfájlt, és töltse fel.

1. Ön kiváltotta az eseményt, az Event Grid pedig elküldte az üzenetet a feliratkozáskor konfigurált végpontnak. Az üzenet JSON formátumú, és egy vagy több eseményből álló tömböt tartalmaz. A következő példában a JSON-üzenet egyetlen eseményt tartalmazó tömböt tartalmaz. Megtekintheti a webalkalmazást, és megfigyelheti, hogy egy **blob által létrehozott** esemény érkezett. 

   ![BLOB létrehozott esemény](./media/blob-event-quickstart-portal/blob-created-event.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha tovább kívánja használni az eseményt, akkor ne törölje a cikkben létrehozott erőforrásokat. Ellenkező esetben törölje a cikkben létrehozott erőforrásokat.

Válassza ki az erőforráscsoportot, majd válassza az **Erőforráscsoport törlése** elemet.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan hozhat létre egyéni témaköröket és esemény-előfizetéseket, bővebben is tájékozódhat arról, hogy miben nyújthat segítséget az Event Grid:

- [Bevezetés az Event Grid használatába](overview.md)
- [Azure Blob Storage-események átirányítása egyéni webes végpontra](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Virtuális gépek módosításainak monitorozása az Azure Event Grid és a Logic Apps segítségével](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Big Data típusú adatok streamelése adattárházba](event-grid-event-hubs-integration.md)
