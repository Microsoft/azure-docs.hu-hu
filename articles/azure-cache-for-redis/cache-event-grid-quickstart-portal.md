---
title: 'Gyors útmutató: az Azure cache átirányítása a Redis-események webes végpontba Azure Portal'
description: Azure Event Grid használata az Azure cache-re való előfizetéshez Redis eseményekhez, az események küldése webhookba, és az események kezelése egy webalkalmazásban
ms.date: 1/5/2021
ms.topic: quickstart
ms.service: cache
author: curib
ms.author: cauribeg
ms.openlocfilehash: 5bdd6b0e6f97f7e5a738ab17d68282cf402004b0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99055549"
---
# <a name="quickstart-route-azure-cache-for-redis-events-to-web-endpoint-with-the-azure-portal"></a>Gyors útmutató: az Azure cache átirányítása a Redis-események webes végpontba Azure Portal

Az Azure Event Grid egy felhőalapú eseménykezelési szolgáltatás. Ebben a rövid útmutatóban a Azure Portal használatával hozzon létre egy Azure cache-t a Redis-példányhoz, fizessen elő az adott példány eseményeire, indítson el egy eseményt, és tekintse meg az eredményeket. Általában olyan végpontoknak szoktunk eseményeket küldeni, amelyek eseményadatokat dolgoznak fel és műveleteket hajtanak végre. A rövid útmutató egyszerűsítése érdekében azonban olyan webalkalmazásnak küldheti az eseményeket, amely összegyűjti és megjeleníti az üzeneteket. 

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

Ha elkészült, látni fogja, hogy a rendszer elküldte az eseményre vonatkozó információt a webalkalmazásnak.

:::image type="content" source="media/cache-event-grid-portal/event-grid-scaling.png" alt-text="Azure Event Grid megjelenítői skálázás JSON formátumban.":::

## <a name="create-an-azure-cache-for-redis-cache-instance"></a>Azure cache létrehozása a Redis cache-példányhoz 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="create-a-message-endpoint"></a>Üzenetvégpont létrehozása

A gyorsítótár-példány eseményeire való feliratkozás előtt hozzon létre egy végpontot az esemény üzenethez. A végpont általában az eseményadatok alapján hajt végre műveleteket. A rövid útmutató egyszerűsítése érdekében egy [előre elkészített webalkalmazást](https://github.com/Azure-Samples/azure-event-grid-viewer) fog üzembe helyezni, amely megjeleníti az esemény üzeneteit. Az üzembe helyezett megoldás egy App Service-csomagot, egy App Service-webalkalmazást és egy, a GitHubról származó forráskódot tartalmaz.

1. Válassza az **üzembe helyezés az Azure** -ban a githubon readme elemet, és telepítse a megoldást az előfizetésre. 

:::image type="content" source="media/cache-event-grid-portal/deploy-to-azure.png" alt-text="Üzembe helyezés az Azure-ban gomb.":::

2. Az **Egyéni telepítés** oldalon hajtsa végre a következő lépéseket: 
    1. Az **erőforráscsoport** mezőben válassza ki a gyorsítótár-példány létrehozásakor létrehozott erőforráscsoportot. Az oktatóanyag törlésével megkönnyíti a tisztítást, miután az erőforráscsoportot törli.  
    2. A **hely neve** mezőben adja meg a webalkalmazás nevét.
    3. A **üzemeltetési terv neve** mezőben adja meg a webalkalmazás üzemeltetéséhez használni kívánt app Service-csomag nevét.
    4. Jelölje be az Elfogadom a **fenti feltételeket és kikötéseket** jelölőnégyzetet. 
    5. Válassza a **Beszerzés** lehetőséget. 
    
    | Beállítás      | Ajánlott érték  | Leírás |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Előfizetés** | Legördülő menüből válassza ki az előfizetését. | Az előfizetés, amely alatt létre kívánja hozni a webalkalmazást. | 
    | **Erőforráscsoport** | Legördülő listából válassza ki az erőforráscsoportot, vagy válassza az **új létrehozása** elemet, és adjon meg egy új erőforráscsoport-nevet. | Az összes alkalmazás-erőforrás egy erőforráscsoporthoz való elhelyezésével könnyedén kezelheti és törölheti azokat. | 
    | **Hely neve** | Adja meg a webalkalmazás nevét. | Ez az érték nem lehet üres. | 
    | **Üzemeltetési csomag neve** | Adja meg a webalkalmazás üzemeltetéséhez használni kívánt App Service-csomag nevét. | Ez az érték nem lehet üres. | 

1. Válassza a riasztások (harang ikon) lehetőséget a portálon, majd válassza az **Ugrás az erőforrás-csoportba** lehetőséget. 

    :::image type="content" source="media/cache-event-grid-portal/deployment-notification.png" alt-text="Azure Portal üzembe helyezési értesítés.":::

4. Az **erőforráscsoport** lap erőforrások listájában válassza ki a létrehozott webalkalmazást. Ezen a listán a App Service terv és a gyorsítótár-példány is látható. 

5. A webalkalmazás **app Service** oldalán válassza ki azt az URL-címet, amellyel navigálni szeretne a webhelyhez. Az URL-címnek a következő formátumúnak kell lennie: `https://<your-site-name>.azurewebsites.net` .

6. Győződjön meg arról, hogy a hely látható, de még nem adtak hozzá eseményeket.

    :::image type="content" source="media/cache-event-grid-portal/blank-event-grid-viewer.png" alt-text="Üres Event Grid megjelenítői hely.":::

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-the-azure-cache-for-redis-instance"></a>Előfizetés az Azure cache Redis-példányra

Ebben a lépésben előfizet egy témakörre, hogy elmondja Event Grid mely eseményeket szeretné nyomon követni, és hová kell elküldeni az eseményeket.

1. A portálon navigáljon a korábban létrehozott gyorsítótár-példányhoz. 
2. A **Redis készült Azure cache** lapon válassza a bal oldali menü **események** elemét. 
3. Válassza a **web Hook** lehetőséget. Eseményeket küld a megjelenítői alkalmazásnak a végponthoz tartozó webes Hook használatával. 

     :::image type="content" source="media/cache-event-grid-portal/event-grid-web-hook.png" alt-text="Azure Portal események lap.":::

4. Az **esemény-előfizetés létrehozása** lapon adja meg a következőket: 

    | Beállítás      | Ajánlott érték  | Leírás |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Név** | Adja meg az esemény-előfizetés nevét. | Az értéknek 3 – 64 karakter hosszúnak kell lennie. Csak betűket, számokat és kötőjeleket tartalmazhat. | 
    | **Események típusai** | A legördülő listából válassza ki, hogy mely eseménytípus (ek) et szeretné leküldeni a célhelyre. Ebben a rövid útmutatóban a gyorsítótár-példányt fogjuk méretezni. | A javítások, skálázás, Importálás és exportálás az elérhető lehetőségek. | 
    | **Végpont típusa** | Válassza a **web Hook** lehetőséget. | Az események fogadására szolgáló eseménykezelő. | 
    | **Végpont** | Kattintson a **végpont kiválasztása** elemre, és adja meg a webalkalmazás URL-címét, és adja hozzá `api/updates` a Kezdőlap URL-címét (például: `https://cache.azurewebsites.net/api/updates` ), majd válassza a **kijelölés megerősítése** lehetőséget. | Ez a korábban létrehozott webalkalmazás URL-címe. | 

5. Most az esemény- **előfizetés létrehozása** lapon válassza a **Létrehozás** lehetőséget az esemény-előfizetés létrehozásához. 

6. Tekints meg újra a webalkalmazást, ahol láthatja, hogy az fogadta az előfizetés érvényesítési eseményét. Az eseményadatok kibontásához kattintson a szem ikonra. Az Event Grid elküldi az érvényesítési eseményt, így a végpont megerősítheti, hogy eseményadatokat akar kapni. A webalkalmazás az előfizetés érvényesítéséhez szükséges kódot tartalmaz.

    :::image type="content" source="media/cache-event-grid-portal/subscription-event.png" alt-text="Azure Event Grid Viewer.":::

## <a name="send-an-event-to-your-endpoint"></a>Esemény elküldése a végpontra

Most aktiváljunk egy eseményt, és lássuk, hogyan küldi el az üzenetet az Event Grid a végpontnak. Az Azure cache-t a Redis-példányra fogjuk méretezni.

1. A Azure Portal navigáljon az Azure cache Redis-példányhoz, és válassza a bal oldali menü **Méretezés** elemét.

1. Válassza ki a kívánt díjszabási szintet a **skála** lapon, és kattintson a **kiválasztás** elemre. 

    A következő korlátozásokkal méretezheti át egy másik díjszabási szintet:
    
    * Magasabb díjszabási szintet nem lehet alacsonyabb díjszabási szinten méretezni.
      * **Prémium** szintű gyorsítótárból nem méretezhető **standard** vagy **alapszintű** gyorsítótár.
      * **Standard** szintű gyorsítótárból nem méretezhető **alapszintű** gyorsítótár.
    * **Alapszintű** gyorsítótárból **szabványos** gyorsítótárra méretezheti, de a méret nem módosítható egyszerre. Ha más méretre van szüksége, egy későbbi skálázási műveletet is végrehajthat a kívánt méretre.
    * **Alapszintű** gyorsítótárból nem lehet közvetlenül **prémium** szintű gyorsítótárra méretezni. Első lépésként az **alapszintű** és a **standard** közötti méretezés egy skálázási műveletben, majd a **standard** és a **prémium** között egy későbbi skálázási műveletben.
    * A **C0 (250 MB)** mérete nem méretezhető nagyobb méretre.
 
    Míg a gyorsítótár az új díjszabási szinten méretezhető, a Redis panelhez tartozó **Azure cache** -ben egy **skálázási** állapot jelenik meg. A skálázás befejezése után az állapot a **méretezéstől** a **futtatásig** változik.

1. Ön kiváltotta az eseményt, az Event Grid pedig elküldte az üzenetet a feliratkozáskor konfigurált végpontnak. Az üzenet JSON formátumú, és egy vagy több eseményből álló tömböt tartalmaz. A következő példában a JSON-üzenet egyetlen eseményt tartalmazó tömböt tartalmaz. Megtekintheti a webalkalmazást, és megfigyelheti, hogy **ScalingCompleted** esemény érkezett. 

    :::image type="content" source="media/cache-event-grid-portal/event-grid-scaling.png" alt-text="Azure Event Grid megjelenítői skálázás JSON formátumban.":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy folytatja az esemény használatát, ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ellenkező esetben törölje az ebben a rövid útmutatóban létrehozott erőforrásokat.

Válassza ki az erőforráscsoportot, majd válassza az **Erőforráscsoport törlése** elemet.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan hozhat létre egyéni témaköröket és esemény-előfizetéseket, bővebben is tájékozódhat arról, hogy miben nyújthat segítséget az Event Grid:

- [Az Azure cache-re való reagálás Redis eseményekhez](cache-event-grid.md)
- [Bevezetés az Event Grid használatába](../event-grid/overview.md)

