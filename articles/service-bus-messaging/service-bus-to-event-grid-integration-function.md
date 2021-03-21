---
title: Service Bus események kezelése Event Grid használatával Azure Functions
description: Ez a cikk a Azure Functions használatával Event Grid Service Bus események kezelésére szolgáló lépéseket ismerteti.
documentationcenter: .net
author: spelluru
ms.topic: tutorial
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-csharp
ms.openlocfilehash: afc0a5bf9b83363d1f4baab955b55148fe3a8498
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95819404"
---
# <a name="tutorial-respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-functions"></a>Oktatóanyag: válaszadás a Azure Event Grid által a Azure Functions használatával kapott Azure Service Bus eseményekre
Ebből az oktatóanyagból megtudhatja, hogyan válaszolhat a Azure Event Grid használatával fogadott Azure Service Bus eseményekre Azure Functions és Azure Logic Apps használatával. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Service Bus-névtér létrehozása
> * Minta alkalmazás előkészítése üzenetek küldéséhez
> * Üzenetek küldése a Service Bus témakörnek
> * Üzenetek fogadása a Logic Apps használatával
> * Tesztelési függvény beállítása az Azure-ban
> * A függvény és a névtér összekapcsolása az Event Griden keresztül
> * Üzenetek fogadása az Azure Functions használatával

[!INCLUDE [service-bus-event-grid-prerequisites](../../includes/service-bus-event-grid-prerequisites.md)]

## <a name="additional-prerequisites"></a>További előfeltételek
Telepítse a [Visual Studio 2019](https://www.visualstudio.com/vs) alkalmazást, és adja meg az **Azure-fejlesztési** számítási feladatot. Ez a munkaterhelés olyan **Azure functions-eszközöket** tartalmaz, amelyekkel Azure functions-projekteket hozhat létre, építhet ki és helyezhet üzembe a Visual Studióban. 

## <a name="deploy-the-function-app"></a>A függvényalkalmazás üzembe helyezése 

>[!NOTE]
> Ha többet szeretne megtudni egy Azure Functions alkalmazás létrehozásáról és üzembe helyezéséről, olvassa el a [Azure functions fejlesztése a Visual Studióval](../azure-functions/functions-develop-vs.md) című témakört.

1. Nyissa meg a **ReceiveMessagesOnEvent. cs** fájlt a **SBEventGridIntegration. SLN** megoldás **FunctionApp1** projektje alapján. 
1. Cserélje le a `<SERCICE BUS NAMESPACE - CONNECTION STRING>` karakterláncot a Service Bus névtérhez tartozó kapcsolódási sztringre. Meg kell egyeznie azzal, amelyet a **MessageSender** projekt **program. cs** fájljában használt a megoldásban. 
1. Kattintson a jobb gombbal a **FunctionApp1** elemre, majd válassza a **Közzététel** lehetőséget. 
1. A **közzétételi** lapon válassza az **Indítás** lehetőséget. Ezek a lépések eltérhetnek a megjelenő, de a közzétételi folyamatnak hasonlónak kell lennie. 
1. A **közzétételi** varázsló **cél** lapján válassza az **Azure** a **célként** lehetőséget. 
1. Az **adott cél** lapon válassza az **Azure függvényalkalmazás (Windows)** lehetőséget. 
1. A **functions példány** lapon válassza az **új Azure-függvény létrehozása** lehetőséget. 
1. A **függvényalkalmazás (Windows)** lapon kövesse az alábbi lépéseket:
    1. Adja meg a Function alkalmazás **nevét** .
    1. Válasszon ki egy Azure- **előfizetést**.
    1. Válasszon ki egy meglévő **erőforráscsoportot** , vagy hozzon létre egy új erőforráscsoportot. Ebben az oktatóanyagban válassza ki a Service Bus névteret tartalmazó erőforráscsoportot. 
    1. Válassza ki a App Servicehoz tartozó **csomag típusát** .
    1. Válasszon egy **helyet**. Válassza ki ugyanazt a helyet, mint a Service Bus névtér. 
    1. Válasszon ki egy meglévő **Azure Storage-tárolót** , vagy válassza az **új** lehetőséget a functions alkalmazás által használandó új Storage-fiók létrehozásához. 
    1. Válassza a **Létrehozás** lehetőséget a functions-alkalmazás létrehozásához. 
1. A **közzétételi** varázsló **functions példány** lapján kattintson a **Befejezés gombra**. 
1. A Visual Studióban a **közzétételi** lapon válassza a **Közzététel** lehetőséget a functions-alkalmazás közzétételéhez az Azure-ban. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/publish-function-app.png" alt-text="Functions-alkalmazás közzététele":::    
1. A **kimeneti** ablakban tekintse meg a build és a publish üzeneteit, és győződjön meg arról, hogy a sikeresek voltak. 
1. Most a **közzétételi** lapon válassza a kezelés lehetőséget **Azure Portal**. 
1. A Azure Portal **függvényalkalmazás** lapján válassza a bal oldali menüben a **függvények** lehetőséget, és győződjön meg arról, hogy két függvény jelenik meg: 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/functions.png" alt-text="Két functions-kezelő Event Grid trigger és HTTP-trigger":::

    > [!NOTE]
    > A a `EventGridTriggerFunction` [Event Grid triggert](../azure-functions/functions-bindings-event-grid-trigger.md) használ, a pedig `HTTPTriggerFunction` http- [triggert](../azure-functions/functions-bindings-http-webhook-trigger.md)használ.
1. A listából válassza a **EventGridTriggerFunction** lehetőséget. Javasoljuk, hogy a Event Grid triggert a Azure Functions használatával használja, mivel a HTTP-trigger használatának néhány előnye van. Részletekért lásd: az [Azure Function eseménykezelő Event Grid eseményekhez](../event-grid/handler-functions.md).
1. A EventGridTriggerFunction **függvény** lapján válassza a bal oldali menü **monitor** elemét. 
1. Válassza a **Konfigurálás** lehetőséget a behívási napló rögzítéséhez Application Insights konfigurálásához. Ezen a lapon figyelhetők a függvények végrehajtása Service Bus események fogadásakor Event Gridról. 
1. A **Application Insights** lapon adja meg az erőforrás nevét, válassza ki az erőforrás **helyét** , majd kattintson **az OK gombra**. 
1. Válassza ki a felül található **EventGridTriggerFunction** (navigációs menü) a **függvény** lapra való visszatéréshez. 
1. Erősítse meg, hogy a **figyelő** oldalon van. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/monitor-before.png" alt-text="A függvény figyelése lap a függvény meghívása előtt":::
    
    Tartsa meg ezt a lapot a webböngésző egyik lapján. Ha később szeretné megtekinteni a függvény meghívását, frissítse ezt a lapot.

## <a name="connect-the-function-and-namespace-via-event-grid"></a>A függvény és a névtér összekapcsolása az Event Griden keresztül
Ebben a szakaszban a függvényt és a Service Bus névteret a Azure Portal használatával köti össze. 

Azure Event Grid előfizetés létrehozásához kövesse az alábbi lépéseket:

1. A Azure Portal nyissa meg a névteret, majd a bal oldali panelen válassza az **események** lehetőséget. Megjelenik a névtér ablaka, a jobb oldali panelen két Event Grid-előfizetéssel. 
    
    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/service-bus-events-page.png" alt-text="Service Bus – események lap":::
2. Válassza az **+ esemény-előfizetés** lehetőséget az eszköztáron. 
3. Az **esemény-előfizetés létrehozása** oldalon hajtsa végre a következő lépéseket:
    1. Adja meg az előfizetés **nevét** . 
    2. Adja meg a **rendszer témakör** **nevét** . A rendszertémakörök az Azure-erőforrások, például az Azure Storage-fiók és a Azure Service Bus számára létrehozott témakörök. További információ a rendszertémakörökről: a [rendszertémakörök áttekintése](../event-grid/system-topics.md).
    2. Válassza ki az **Azure-függvényt** a **végpont típusa mezőben**, majd kattintson **a végpont kiválasztása** elemre. 

        ![Service Bus – Event Grid előfizetés](./media/service-bus-to-event-grid-integration-example/event-grid-subscription-page.png)
    3. Az **Azure-függvény kiválasztása** lapon válassza ki az előfizetést, az erőforráscsoportot, a Function alkalmazást, a tárolóhelyet és a függvényt, majd válassza a **kijelölés megerősítése** lehetőséget. 

        ![Függvény – a végpont kiválasztása](./media/service-bus-to-event-grid-integration-example/function-select-endpoint.png)
    4. Az **esemény-előfizetés létrehozása** lapon váltson a **szűrők** lapra, és végezze el a következő feladatokat:
        1. Válassza ki a **tulajdonos szűrésének engedélyezése** lehetőséget.
        2. Adja meg a korábban létrehozott Service Bus témakörhöz (**S1**) tartozó előfizetés nevét.
        3. Válassza a **Létrehozás** gombot. 

            ![Esemény-előfizetés szűrője](./media/service-bus-to-event-grid-integration-example/event-subscription-filter.png)
4. Váltson az **események** oldal **esemény-előfizetések** lapjára, és ellenőrizze, hogy megjelenik-e az esemény-előfizetés a listában.

    ![Esemény-előfizetés a listában](./media/service-bus-to-event-grid-integration-example/event-subscription-in-list.png)

## <a name="monitor-the-functions-app"></a>A functions alkalmazás figyelése
A korábbi Service Bus témakörbe küldött üzenetek továbbítása az előfizetésbe (S1) történik. Event Grid továbbítja az üzeneteket az előfizetésben az Azure-függvénynek. Az oktatóanyag ezen lépésében megerősíti a függvény meghívását, és megtekintheti a naplózott tájékoztató üzeneteket. 

1. Az Azure Function alkalmazás lapján váltson a **figyelés** lapra, ha még nem aktív. Meg kell jelennie a Service Bus témakörben közzétett összes üzenethez tartozó bejegyzésnek. Ha nem látja őket, frissítse az oldalt néhány perc várakozás után. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/function-monitor.png" alt-text="A függvény figyelése lap a meghívások után":::
2. A részletek megtekintéséhez válassza ki a meghívást a listából. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/invocation-details.png" alt-text="Függvény meghívása – részletek" lightbox="./media/service-bus-to-event-grid-integration-example/invocation-details.png":::

    A **figyelés** lap **naplók** lapján is megtekintheti a naplózási adatokat az üzenetek küldésekor. Némi késés történt, ezért néhány percet is igénybe vehet, hogy megjelenjenek a naplózott üzenetek. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/function-logs.png" alt-text="Függvénynaplók" lightbox="./media/service-bus-to-event-grid-integration-example/function-logs.png":::

## <a name="troubleshoot"></a>Hibaelhárítás
Ha a várakozás után nem látja az összes függvényt, akkor kövesse az alábbi lépéseket: 

1. Győződjön meg arról, hogy az üzenetek eljutnak a Service Bus témakörhöz. Tekintse meg a **beérkező üzenetek** számlálóját a **Service Bus témakör** oldalon. Ebben az esetben kétszer futtattam a **MessageSender** alkalmazást, ezért 10 üzenet jelenik meg (az egyes futtatások 5 üzenete).

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/topic-incoming-messages.png" alt-text="Service Bus témakör lap – bejövő üzenetek":::    
1. Ellenőrizze, hogy nincsenek-e **aktív üzenetek** a Service Bus-előfizetésben. 
    Ha nem jelennek meg események ezen a lapon, ellenőrizze, hogy a **Service Bus előfizetés** oldal nem jeleníti-e meg az **aktív üzenetek darabszámát**. Ha a számláló száma nullánál nagyobb, akkor az előfizetésben lévő üzenetek valamilyen oknál fogva nem jutnak el a kezelő függvényhez (az esemény-előfizetési kezelőhöz). Ellenőrizze, hogy megfelelően állította-e be az esemény-előfizetést. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/subscription-active-message-count.png" alt-text="A Service Bus-előfizetés aktív üzeneteinek száma":::    
1. A **kézbesítési eseményeket** a Service Bus névtér **események** lapján is megtekintheti. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/event-subscription-page.png" alt-text="Események lapon továbbított események" lightbox="./media/service-bus-to-event-grid-integration-example/invocation-details.png":::
1. Azt is láthatja, hogy az események az esemény- **előfizetés** oldalon lesznek továbbítva. Ezt a lapot az **események** oldalon az esemény-előfizetés kiválasztásával érheti el. 
    
    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/event-subscription-delivered-events.png" alt-text="Esemény-előfizetések lapja – kézbesítési események":::
    


## <a name="next-steps"></a>Következő lépések

* További tudnivalók az [Azure Event Grid](../event-grid/index.yml) szolgáltatásról.
* További tudnivalók az [Azure Functions](../azure-functions/index.yml) szolgáltatásról.
* További tudnivalók az [Azure App Service szolgáltatás Logic Apps funkciójáról](../logic-apps/index.yml).
* További tudnivalók az [Azure Service Bus](/azure/service-bus/) szolgáltatásról.


[2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid2.png
[3]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid3.png
[7]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid7.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[10]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid10.png
[11]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid11.png
[12]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12.png
[12-1]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-1.png
[12-2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-2.png
[13]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid13.png
[14]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid14.png
[15]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid15.png
[16]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid16.png
[17]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid17.png
[18]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid18.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png