---
title: Service Bus események kezelése Event Grid használatával Azure Logic Apps
description: Ez a cikk a Azure Logic Apps használatával Event Grid Service Bus események kezelésére szolgáló lépéseket ismerteti.
documentationcenter: .net
author: spelluru
ms.topic: tutorial
ms.date: 10/16/2020
ms.author: spelluru
ms.custom: devx-track-csharp
ms.openlocfilehash: 93375f6047fbe4eda2132e024dab0e067e83ccf1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95999011"
---
# <a name="tutorial-respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-logic-apps"></a>Oktatóanyag: válaszadás a Azure Event Grid által a Azure Logic Apps használatával kapott Azure Service Bus eseményekre
Ebből az oktatóanyagból megtudhatja, hogyan válaszolhat a Azure Event Grid által a Azure Logic Apps használatával fogadott Azure Service Bus eseményekre. 

[!INCLUDE [service-bus-event-grid-prerequisites](../../includes/service-bus-event-grid-prerequisites.md)]

## <a name="receive-messages-by-using-logic-apps"></a>Üzenetek fogadása a Logic Apps használatával
Ebben a lépésben egy Azure logikai alkalmazást hoz létre, amely Service Bus eseményeket fogad Azure Event Grid használatával. 

1. Hozzon létre egy logikai alkalmazást a Azure Portal.
    1. Válassza az **+ erőforrás létrehozása** lehetőséget, válassza az **integráció**, majd a **logikai alkalmazás** lehetőséget. 
    2. A **logikai alkalmazás létrehozása** lapon adja meg a logikai alkalmazás **nevét** .
    3. Válassza ki az Azure- **előfizetését**. 
    4. Válassza az **erőforráscsoport** **meglévő használata** lehetőséget, majd válassza ki azt az erőforráscsoportot, amelyet a korábban létrehozott más erőforrásokhoz (például az Azure-függvényhez, Service Bus névtérhez) használt. 
    5. Válassza ki a logikai alkalmazás **helyét** . 
    6. Válassza a **Felülvizsgálat és létrehozás** lehetőséget. 
    1. A logikai alkalmazás létrehozásához a **felülvizsgálat + létrehozás** lapon válassza a **Létrehozás** lehetőséget. 
1. A **Logic apps Designer** lapon válassza a **sablon** alatt az **üres logikai alkalmazás** lehetőséget. 
1. A tervezőben hajtsa végre a következő lépéseket:
    1. **Event Grid** keresése. 
    2. Válassza ki, **hogy mikor történjen egy erőforrás-esemény – Azure Event Grid**. 

        ![Logic Apps Designer – Event Grid trigger kiválasztása](./media/service-bus-to-event-grid-integration-example/logic-apps-event-grid-trigger.png)
4. Válassza a **Bejelentkezés** lehetőséget, adja meg az Azure-beli hitelesítő adatait, majd válassza a **hozzáférés engedélyezése** lehetőséget. 
5. Az **erőforrás-esemény bekövetkeztekor** oldalon hajtsa végre a következő lépéseket:
    1. Válassza ki Azure-előfizetését. 
    2. Az **erőforrástípus** mezőben válassza a **Microsoft. ServiceBus. névterek** lehetőséget. 
    3. Az **Erőforrás neve** mezőben válassza ki a Service Bus névteret. 
    4. Válassza az **új paraméter hozzáadása** lehetőséget, majd válassza az **utótag-szűrő** elemet. 
    5. Az **utótag-szűrő** mezőben adja meg a második Service Bus témakör-előfizetés nevét. 
        ![Logic Apps Designer – esemény konfigurálása](./media/service-bus-to-event-grid-integration-example/logic-app-configure-event.png)
6. Válassza az **+ új lépés** lehetőséget a tervezőben, és hajtsa végre a következő lépéseket:
    1. **Service Bus** keresése.
    2. Válassza ki **Service Bus** a listában. 
    3. Válassza az **üzenetek beolvasása** lehetőséget a **műveletek** listában. 
    4. Válassza az **üzenetek beolvasása egy témakör-előfizetésből (betekintési zárolás)** lehetőséget. 

        ![Logic Apps Designer – üzenetek beolvasása művelet](./media/service-bus-to-event-grid-integration-example/service-bus-get-messages-step.png)
    5. Adja meg **a kapcsolatok nevét**. Például: **üzenetek beolvasása az előfizetés témakörből**, majd válassza ki a Service Bus névteret. 

        ![Logic Apps Designer – válassza ki a Service Bus névteret](./media/service-bus-to-event-grid-integration-example/logic-apps-select-namespace.png) 
    6. Válassza a **RootManageSharedAccessKey** lehetőséget, majd válassza a **Létrehozás** lehetőséget.

        ![Logic Apps Designer – válassza ki a megosztott elérési kulcsot](./media/service-bus-to-event-grid-integration-example/logic-app-shared-access-key.png) 
    8. Válassza ki a **témakört** és az **előfizetést**. 
    
        ![Képernyőkép, amely bemutatja, hol válassza ki a témakört és az előfizetést.](./media/service-bus-to-event-grid-integration-example/logic-app-select-topic-subscription.png)
7. Válassza az **+ új lépés** lehetőséget, majd hajtsa végre a következő lépéseket: 
    1. Válassza a **Service Bus** lehetőséget.
    2. Válassza ki **az üzenet végrehajtása témakör-előfizetésben** a műveletek listájában. 
    3. Válassza ki a Service Bus **témakört**.
    4. Válassza ki a második **előfizetést** a témakörhöz.
    5. **Az üzenet zárolási jogkivonatának** kiválasztásához válassza a **jogkivonat zárolása** elemet a **dinamikus tartalomban**. 

        ![Logic Apps Designer – az üzenet befejezése](./media/service-bus-to-event-grid-integration-example/logic-app-complete-message.png)
8. A logikai alkalmazás mentéséhez kattintson a **Mentés** gombra a Logic apps Designer eszköztárán. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/save-logic-app.png" alt-text="Logikai alkalmazás mentése":::
1. Ha még nem küldött tesztüzenet a témakörben, az üzenetek [küldése a Service Bus témakörbe](#send-messages-to-the-service-bus-topic) című rész útmutatását követve üzeneteket küldhet a témakörbe. 
1. Váltson a logikai alkalmazás **Áttekintés** lapjára. Megjelenik a logikai alkalmazás futtatása a **futtatási előzményekben** az elküldött üzeneteknél. Néhány percet is igénybe vehet, mielőtt megtekinti a logikai alkalmazás futtatását. A lap frissítéséhez kattintson a **frissítés** gombra az eszköztáron. 

    ![Logic Apps Designer – logikai alkalmazás futtatása](./media/service-bus-to-event-grid-integration-example/logic-app-runs.png)
1. Válassza ki a logikai alkalmazás futtatását a részletek megtekintéséhez. Figyelje meg, hogy a for ciklusban 5 üzenet lett feldolgozva. 
    
    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/logic-app-run-details.png" alt-text="Logikai alkalmazás futtatásának részletei":::    

## <a name="troubleshoot"></a>Hibaelhárítás
Ha a várakozás után nem látja a meghívásokat, az alábbi lépéseket kell követnie: 

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