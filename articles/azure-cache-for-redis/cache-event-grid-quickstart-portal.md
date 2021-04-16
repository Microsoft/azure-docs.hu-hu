---
title: 'Rövid útmutató: Események Azure Cache for Redis webes végpontra a Azure Portal'
description: A Azure Event Grid feliratkozhat Azure Cache for Redis eseményekre, elküldhet eseményeket egy webhooknak, és kezelni tudja az eseményeket egy webalkalmazásban
author: curib
ms.author: cauribeg
ms.date: 1/5/2021
ms.topic: quickstart
ms.service: cache
ms.custom:
- mode-portal
ms.openlocfilehash: e021f386f255f1cef61e28cbd4fd6116fc2aa727
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529303"
---
# <a name="quickstart-route-azure-cache-for-redis-events-to-web-endpoint-with-the-azure-portal"></a>Rövid útmutató: Események Azure Cache for Redis webes végpontra a Azure Portal

Az Azure Event Grid egy felhőalapú eseménykezelési szolgáltatás. Ebben a rövid útmutatóban a Azure Portal használatával létrehoz egy Azure Cache for Redis-példányt, feliratkozik az adott példány eseményeire, elindít egy eseményt, és megtekinti az eredményeket. Általában olyan végpontoknak szoktunk eseményeket küldeni, amelyek eseményadatokat dolgoznak fel és műveleteket hajtanak végre. A rövid útmutató egyszerűsítése érdekében azonban eseményeket fog küldeni egy webalkalmazásnak, amely összegyűjti és megjeleníti az üzeneteket. 

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

Amikor végzett, láthatja, hogy az eseményadatok el vannak küldve a webalkalmazásnak.

:::image type="content" source="media/cache-event-grid-portal/event-grid-scaling.png" alt-text="Azure Event Grid Viewer méretezése JSON formátumban.":::

## <a name="create-an-azure-cache-for-redis-cache-instance"></a>Gyorsítótárpéldány Azure Cache for Redis létrehozása 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="create-a-message-endpoint"></a>Üzenetvégpont létrehozása

Mielőtt feliratkozik a gyorsítótárpéldány eseményeire, létre kell hozni az eseményüzenet végpontját. A végpont általában az eseményadatok alapján hajt végre műveleteket. A rövid útmutató egyszerűsítése érdekében [](https://github.com/Azure-Samples/azure-event-grid-viewer) üzembe fog helyezni egy előre felépített webalkalmazást, amely megjeleníti az eseményüzeneteket. Az üzembe helyezett megoldás egy App Service-csomagot, egy App Service-webalkalmazást és egy, a GitHubról származó forráskódot tartalmaz.

1. Válassza **az Üzembe helyezés az Azure-ban** a GitHubon README lehetőséget a megoldás előfizetésben való üzembe helyezéséhez. 

:::image type="content" source="media/cache-event-grid-portal/deploy-to-azure.png" alt-text="Üzembe helyezés az Azure-ban gomb.":::

2. Az Egyéni **üzembe helyezés lapon** tegye a következőket: 
    1. Az **Erőforráscsoport mezőben** válassza ki a gyorsítótárpéldány létrehozásakor létrehozott erőforráscsoportot. Az erőforráscsoport törlésével könnyebb lesz törölni a felesleges okat, miután végzett az oktatóanyagtal.  
    2. A **Webhely neve alatt** adja meg a webalkalmazás nevét.
    3. A **Hosting plan name (Üzemeltetési csomag** neve) alatt adja meg a App Service-csomag nevét, amely a webalkalmazás üzemeltetéséhez lesz használva.
    4. Jelölje be az Elfogadom a fenti feltételeket és feltételeket **jelölőnégyzetet.** 
    5. Válassza a **Beszerzés** lehetőséget. 
    
    | Beállítás      | Ajánlott érték  | Leírás |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Előfizetés** | Legördülő menüben válassza ki az előfizetését. | Az előfizetés, amelyben létre kell hoznia ezt a webalkalmazást. | 
    | **Erőforráscsoport** | Legördülő menüben válasszon ki egy erőforráscsoportot, vagy válassza az **Új** létrehozása lehetőséget, és adjon meg egy új erőforráscsoport-nevet. | Ha az összes alkalmazás-erőforrást egyetlen erőforráscsoportban szeretné kezelni vagy törölni, egyszerűen kezelheti vagy törölheti őket. | 
    | **Hely neve** | Adja meg a webalkalmazás nevét. | Ez az érték nem lehet üres. | 
    | **Üzemeltetési csomag neve** | Adja meg a App Service a webalkalmazás üzemeltetési tervének nevét. | Ez az érték nem lehet üres. | 

1. A portálon válassza a Riasztások (harang ikon), majd az **Ugrás az erőforráscsoporthoz lehetőséget.** 

    :::image type="content" source="media/cache-event-grid-portal/deployment-notification.png" alt-text="Azure Portal üzembe helyezési értesítés.":::

4. Az **Erőforráscsoport lapon** az erőforrások listájában válassza ki a létrehozott webalkalmazást. A listában megjelenik a App Service terv és a gyorsítótárpéldány is. 

5. A **App Service** lapján válassza ki a webhelyre mutató URL-címet. Az URL-címnek a következő formátumban kell lennie: `https://<your-site-name>.azurewebsites.net` .

6. Ellenőrizze, hogy látja-e a webhelyet, de még nem tett közzé eseményeket.

    :::image type="content" source="media/cache-event-grid-portal/blank-event-grid-viewer.png" alt-text="Üres Event Grid Megtekintő webhely.":::

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-the-azure-cache-for-redis-instance"></a>Feliratkozás a Azure Cache for Redis példányra

Ebben a lépésben feliratkozik egy témakörre, amelyből Event Grid, hogy mely eseményeket szeretné nyomon követni, és hová szeretné küldeni az eseményeket.

1. A portálon keresse meg a korábban létrehozott gyorsítótárpéldányt. 
2. A **Azure Cache for Redis** bal **oldali menüben válassza** az Események lehetőséget. 
3. Válassza **a Web Hook lehetőséget.** Eseményeket küld a megjelenítő alkalmazásnak a végponthoz web hook használatával. 

     :::image type="content" source="media/cache-event-grid-portal/event-grid-web-hook.png" alt-text="Azure Portal Események lap.":::

4. Az **Esemény-előfizetés létrehozása lapon** adja meg a következőket: 

    | Beállítás      | Ajánlott érték  | Leírás |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Név** | Adja meg az esemény-előfizetés nevét. | Az értéknek 3–64 karakter hosszúságúnak kell lennie. Csak betűket, számokat és kötőjeleket tartalmazhat. | 
    | **Eseménytípusok** | Legördülő menüből válassza ki, hogy mely eseménytípus(ak)t szeretné leküldni a célhelyre. Ebben a rövid útmutatóban a gyorsítótárpéldányt skálázjuk. | A rendelkezésre álló lehetőségek a javítás, a méretezés, az importálás és az exportálás. | 
    | **Végpont típusa** | Válassza **a Web Hook lehetőséget.** | Eseménykezelő az események fogadására. | 
    | **Végpont** | Kattintson **a Végpont kiválasztása elemre,** adja meg a webalkalmazás URL-címét, és adja hozzá a címet a kezdőlap URL-címéhez (például : ), majd válassza a Kiválasztás megerősítése `api/updates` `https://cache.azurewebsites.net/api/updates` **lehetőséget.** | Ez a korábban létrehozott webalkalmazás URL-címe. | 

5. Most az **Esemény-előfizetés létrehozása lapon** válassza a **Létrehozás** lehetőséget az esemény-előfizetés létrehozásához. 

6. Tekints meg újra a webalkalmazást, ahol láthatja, hogy az fogadta az előfizetés érvényesítési eseményét. Az eseményadatok kibontásához kattintson a szem ikonra. Az Event Grid elküldi az érvényesítési eseményt, így a végpont megerősítheti, hogy eseményadatokat akar kapni. A webalkalmazás az előfizetés érvényesítéséhez szükséges kódot tartalmaz.

    :::image type="content" source="media/cache-event-grid-portal/subscription-event.png" alt-text="Azure Event Grid Viewer.":::

## <a name="send-an-event-to-your-endpoint"></a>Esemény elküldése a végpontra

Most aktiváljunk egy eseményt, és lássuk, hogyan küldi el az üzenetet az Event Grid a végpontnak. A saját példányát Azure Cache for Redis skálázjuk.

1. A Azure Portal lépjen a saját Azure Cache for Redis, és válassza a bal **oldali** menü Skáláz lehetőséget.

1. Válassza ki a kívánt tarifacsomagot a **Méretezés lapon,** majd kattintson a **Kijelölés gombra.** 

    A következő korlátozásokkal skálázhat egy másik tarifacsomagra:
    
    * Magasabb tarifacsomagról nem skálázhat alacsonyabb tarifacsomagra.
      * Prémium szintű gyorsítótárról **nem** skálázhat le standard vagy **alapszintű** **gyorsítótárra.**
      * Standard gyorsítótárról alapszintű  gyorsítótárra **nem** skálázhat le.
    * Az alapszintű **gyorsítótárakról** a **Standard** gyorsítótárakra skálázhat, de a méretet nem módosíthatja egyszerre. Ha más méretre van szüksége, ezt követően a kívánt méretre skálázhatja a műveletet.
    * Alapszintű gyorsítótárról nem  skálázhat közvetlenül prémium **szintű gyorsítótárra.** Először skálázhat **alapszintűről** **Standardra** egy skálázási műveletben, majd egy későbbi skálázási műveletben **Standardról** **Prémiumra.**
    * Nagyobb méretről nem skálázhat le **250 MB-os C0-méretre.**
 
    Amíg a gyorsítótár az új tarifacsomagra skáláz, **a** méretezés állapota megjelenik a Azure Cache for Redis **panelen.** Ha a skálázás befejeződött, az állapot Skálázásról **Fut** **állapotra változik.**

1. Ön kiváltotta az eseményt, az Event Grid pedig elküldte az üzenetet a feliratkozáskor konfigurált végpontnak. Az üzenet JSON formátumban van, és egy egy vagy több eseményt tartalmazó tömböt tartalmaz. A következő példában a JSON-üzenet egy egyetlen eseményt tartalmazó tömböt tartalmaz. Tekintse meg a webalkalmazást, és figyelje meg, hogy egy **ScalingCompleted esemény** érkezett. 

    :::image type="content" source="media/cache-event-grid-portal/event-grid-scaling.png" alt-text="Azure Event Grid Viewer méretezésESON formátumban.":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy folytatja az eseményekkel való munkát, akkor ne takarítsa meg az ebben a rövid útmutatóban létrehozott erőforrásokat. Ellenkező esetben törölje az ebben a rövid útmutatóban létrehozott erőforrásokat.

Válassza ki az erőforráscsoportot, majd válassza az **Erőforráscsoport törlése** elemet.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan hozhat létre egyéni témaköröket és esemény-előfizetéseket, bővebben is tájékozódhat arról, hogy miben nyújthat segítséget az Event Grid:

- [Reagálás a Azure Cache for Redis eseményekre](cache-event-grid.md)
- [Bevezetés az Event Grid használatába](../event-grid/overview.md)
