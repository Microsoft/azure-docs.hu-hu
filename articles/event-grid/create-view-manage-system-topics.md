---
title: Rendszertémakörök létrehozása, megtekintése és kezelése Azure Event Grid (portál)
description: Ez a cikk bemutatja, hogyan lehet megtekinteni a meglévő rendszertémakört, Azure Event Grid rendszertémaköröket létrehozni a Azure Portal használatával.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 2dbb0893668d72cfebf2d64e6515ff6ab6fed9bc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86115111"
---
# <a name="create-view-and-manage-event-grid-system-topics-in-the-azure-portal"></a>Event Grid rendszertémakörök létrehozása, megtekintése és kezelése a Azure Portal
Ez a cikk bemutatja, hogyan hozhat létre és kezelhet rendszertémaköröket a Azure Portal használatával. A rendszertémakörök áttekintését itt találja: [rendszertémakörök](system-topics.md).

## <a name="create-a-system-topic"></a>Rendszerbeli témakör létrehozása
Az Azure-erőforrásokhoz (Storage-fiókhoz, Event Hubs névtérhez stb.) rendszertémakört kétféleképpen lehet létrehozni:

- Egy erőforrás **események** lapja (például Storage-fiók vagy Event Hubs névtér) használatával. Ha a Azure Portal **események** lapját használja egy Azure-forrás által kiváltott eseményhez (például: Azure Storage-fiók), a portál létrehoz egy rendszertémakört az Azure-erőforráshoz, majd előfizetést hoz létre a rendszertémakörhöz. Ha első alkalommal hoz létre egy esemény-előfizetést az Azure-erőforráshoz, adja meg a rendszertémakör nevét. A második alkalommal a rendszertéma neve jelenik meg a csak olvasható módban. Tekintse meg a gyors útmutató [: blob Storage-események átirányítása webes végpontra a Azure Portal](blob-event-quickstart-portal.md#subscribe-to-the-blob-storage) a részletes lépések végrehajtásához.
- A **Event Grid rendszertémakörök** lap használata. A következő lépésekkel hozhat létre rendszertémakört a **Event Grid rendszertémakörök** lapon. 

1. Jelentkezzen be [Azure Portalba](https://portal.azure.com).
2. A felső keresőmezőbe írja be **Event Grid rendszertémakörök** kifejezést, majd nyomja le az **ENTER** billentyűt. 

    ![Rendszertémakörök keresése](./media/create-view-manage-system-topics/search-system-topics.png)
3. A **Event grid rendszer témakörei** lapon válassza a **+ Hozzáadás** lehetőséget az eszköztáron.

    ![Rendszertéma hozzáadása – eszköztár gomb](./media/create-view-manage-system-topics/add-system-topic-menu.png)
4. A **Event grid rendszer létrehozása** lapon hajtsa végre a következő lépéseket:
    1. Válassza ki a **témakör típusát**. A következő példában a **Storage-fiókok** lehetőség van kiválasztva. 
    2. Válassza ki azt az **Azure-előfizetést** , amely rendelkezik a Storage-fiók erőforrásával. 
    3. Válassza ki a Storage-fiókkal rendelkező **erőforráscsoportot** . 
    4. Válassza ki a **Storage-fiókot**. 
    5. Adja meg a létrehozandó rendszertémakör **nevét** . 
    
        > [!NOTE]
        > Ezt a rendszertémakör-nevet használhatja a metrikák és a diagnosztikai naplók kereséséhez.
    6. Válassza az **Áttekintés + létrehozás** lehetőséget.

        ![Rendszertémakör létrehozása](./media/create-view-manage-system-topics/create-event-grid-system-topic-page.png)
    5. Tekintse át a beállításokat, majd válassza a **Létrehozás** lehetőséget. 
        
        ![Rendszertémakör áttekintése és létrehozása](./media/create-view-manage-system-topics/system-topic-review-create.png)
    6. Miután az üzembe helyezés sikeres volt, kattintson az **erőforrás keresése** elemre, és tekintse meg a létrehozott rendszertémakör **Event grid rendszer témakörét** . 

        ![Rendszertémakör lap](./media/create-view-manage-system-topics/system-topic-page.png)


## <a name="view-all-system-topics"></a>Az összes rendszertémakör megtekintése
Az alábbi lépéseket követve megtekintheti az összes meglévő Event Grid rendszertémakört. 

1. Jelentkezzen be [Azure Portalba](https://portal.azure.com).
2. A felső keresőmezőbe írja be **Event Grid rendszertémakörök** kifejezést, majd nyomja le az **ENTER** billentyűt. 

    ![Rendszertémakörök keresése](./media/create-view-manage-system-topics/search-system-topics.png)
3. A **Event Grid rendszertémakörök** lapon láthatja az összes rendszertémakört. 

    ![A rendszertémakörök listája](./media/create-view-manage-system-topics/list-system-topics.png)
4. A részletek megtekintéséhez válasszon ki egy **rendszertémakört** a listából. 

    ![Rendszertémakör részletei](./media/create-view-manage-system-topics/system-topic-details.png)

    Ez az oldal a rendszertémakör részletes adatait jeleníti meg, például a következő információkat: 
    - Forrás. Annak az erőforrásnak a neve, amelyen a rendszertémakört létrehozták.
    - Forrás típusa Az erőforrás típusa. Például: `Microsoft.Storage.StorageAccounts` , `Microsoft.EventHub.Namespaces` , `Microsoft.Resources.ResourceGroups` stb.
    - A rendszertémakörhöz létrehozott előfizetések.

    Ez az oldal a következő műveletek elvégzését teszi lehetővé:
    - Hozzon létre egy esemény-előfizetés kiválasztása **+ esemény-előfizetést** az eszköztáron. 
    - Esemény-előfizetés törlése. Válassza a **Törlés** lehetőséget az eszköztáron. 
    - Címkék hozzáadása a rendszer témakörhöz. A bal oldali menüben válassza a **címkék** lehetőséget, majd adja meg a címke nevét és értékeit. 


## <a name="delete-a-system-topic"></a>Rendszer-témakör törlése
1. A rendszertémakörök [megtekintése](#view-all-system-topics) szakasz utasításait követve tekintheti meg az összes rendszertémakört, majd a listából válassza ki a törölni kívánt rendszertémakört. 
2. A **Event Grid rendszertémakör** lapon válassza a **Törlés** lehetőséget az eszköztáron. 

    ![Rendszertémakör – törlés gomb](./media/create-view-manage-system-topics/system-topic-delete-button.png)
3. A jóváhagyás lapon kattintson az **OK gombra** a törlés megerősítéséhez. Törli a rendszertémakört és a rendszertémakör összes esemény-előfizetését is.  

## <a name="create-an-event-subscription"></a>Esemény-előfizetés létrehozása
1. A rendszertémakörök [megtekintése](#view-all-system-topics) szakasz utasításait követve tekintheti meg az összes rendszertémakört, majd a listából válassza ki a törölni kívánt rendszertémakört. 
2. A **Event Grid rendszertéma** lapon válassza a **+ esemény-előfizetés** lehetőséget az eszköztáron. 

    ![Rendszertémakör – esemény-előfizetés hozzáadása gomb](./media/create-view-manage-system-topics/add-event-subscription-button.png)
3. Győződjön meg arról, hogy a **témakör típusa**, a **forrás erőforrás** és a **témakör neve** automatikusan fel van töltve. Adjon meg egy nevet, válasszon egy **végpont típusát**, majd adja meg a **végpontot**. Ezután válassza a **Létrehozás** lehetőséget az esemény-előfizetés létrehozásához. 

    ![Rendszertémakör – esemény-előfizetés létrehozása](./media/create-view-manage-system-topics/create-event-subscription.png)

## <a name="next-steps"></a>Következő lépések
A Azure Event Grid által támogatott rendszertémakörökkel és a témakörökkel kapcsolatos további tudnivalókért tekintse meg [Azure Event Grid szakasz rendszertémaköreit](system-topics.md) . 
