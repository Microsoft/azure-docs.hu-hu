---
title: Azure rövid útmutató – Event hub létrehozása a Azure Portal használatával
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre Azure Event hub-t Azure Portal használatával.
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 6bd9d247db6cd5a956ff4bce9b70f1f8a5a7499a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101742041"
---
# <a name="quickstart-create-an-event-hub-using-azure-portal"></a>Rövid útmutató: Eseményközpont létrehozása az Azure Portallal
Az Azure Event Hubs egy Big streaming platform-és esemény-betöltési szolgáltatás, amely másodpercenként több millió eseményt képes fogadni és feldolgozni. Az Event Hubs képes az elosztott szoftverek és eszközök által generált események, adatok vagy telemetria feldolgozására és tárolására. Az eseményközpontokba elküldött adatok bármilyen valós idejű elemzési szolgáltató vagy kötegelési/tárolóadapter segítségével átalakíthatók és tárolhatók. Az Event Hubs részletes áttekintéséért lásd az [Event Hubs áttekintését](event-hubs-about.md) és az [Event Hubs-szolgáltatásokat](event-hubs-features.md) ismertető cikket.

Ebben a rövid útmutatóban az [Azure Portal](https://portal.azure.com) használatával hoz létre eseményközpontot.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez győződjön meg róla, hogy rendelkezik az alábbiakkal:

- Egy Azure-előfizetés. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/), mielőtt hozzákezd.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az erőforráscsoport Azure-erőforrások logikai gyűjteménye. Minden erőforrás üzembe helyezése és kezelése erőforráscsoportban történik. Erőforráscsoport létrehozása:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs sávon válassza az **erőforráscsoportok** lehetőséget. Ezután válassza a **Hozzáadás** elemet.

   ![Erőforráscsoportok – Hozzáadás gomb](./media/event-hubs-quickstart-portal/resource-groups1.png)

1. Az **előfizetés** mezőben válassza ki annak az Azure-előfizetésnek a nevét, amelyben létre kívánja hozni az erőforráscsoportot.
1. Adja **meg az erőforráscsoport egyedi nevét**. A rendszer azonnal ellenőrzi, hogy a név elérhető-e az aktuálisan kiválasztott Azure-előfizetésben.
1. Válassza ki az erőforráscsoport **régióját** .
1. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.

   ![Erőforráscsoport – létrehozás](./media/event-hubs-quickstart-portal/resource-groups2.png)
1. A **felülvizsgálat + létrehozás** lapon válassza a **Létrehozás** lehetőséget. 

## <a name="create-an-event-hubs-namespace"></a>Event Hubs-névtér létrehozása

Az Event Hubs névtér egy egyedi hatókörű tárolót biztosít, amelyben létrehoz egy vagy több Event hubokat. Ha létre szeretne hozni egy névteret az erőforráscsoportban a portál használatával, végezze el az alábbi műveleteket:

1. Az Azure Portalon kattintson az **Erőforrás létrehozása** gombra a képernyő bal felső részén.
1. A bal oldali menüben válassza a **minden szolgáltatás** lehetőséget, majd válassza a **csillag ( `*` )** lehetőséget a **Event Hubs** elem mellett az **elemzési** kategóriában. Győződjön meg arról, hogy a bal oldali navigációs menüben a **Event Hubs** a **Kedvencek közé** kerül. 
    
   ![Event Hubs keresése](./media/event-hubs-quickstart-portal/select-event-hubs-menu.png)
1. A bal oldali navigációs menüben kattintson a **Kedvencek** elemre, majd a **Hozzáadás** elemre az eszköztáron válassza a **Event Hubs** lehetőséget.

   ![Hozzáadás gomb](./media/event-hubs-quickstart-portal/event-hubs-add-toolbar.png)
1. A **névtér létrehozása** oldalon hajtsa végre a következő lépéseket:  
   1. Válassza ki azt az **előfizetést** , amelyben létre kívánja hozni a névteret.  
   1. Válassza ki az előző lépésben létrehozott **erőforráscsoportot** .   
   1. Adja meg a névtér **nevét** . A rendszer azonnal ellenőrzi, hogy a név elérhető-e.  
   1. Válassza ki a névtér **helyét** .
   1. Válassza ki az **árképzési szintet** (alapszintű vagy standard). Az alapszintű és a standard csomag közötti különbségekről további információt a következő témakörben talál: [Event Hubs díjszabás](https://azure.microsoft.com/pricing/details/event-hubs/), a [szintek közötti különbségek](event-hubs-faq.md#what-is-the-difference-between-event-hubs-basic-and-standard-tiers)és a [kvóták és korlátok](event-hubs-quotas.md). 
   1. Hagyja meg az **átviteli egységek** beállításait. Az átviteli egységek előre megvásárolt kapacitásegységek. Az átviteli egységekkel kapcsolatos további tudnivalókért tekintse meg a [Event Hubs méretezhetőségét](event-hubs-scalability.md#throughput-units)ismertető témakört.  
   1. Kattintson a lap alján található **felülvizsgálat + létrehozás** lehetőségre.
      
      ![Eseményközponti névtér létrehozása](./media/event-hubs-quickstart-portal/create-event-hub1.png)
   1. A **felülvizsgálat + létrehozás** oldalon tekintse át a beállításokat, majd kattintson a **Létrehozás** gombra. Várjon, amíg az üzembe helyezés befejeződik. 
      
      ![Felülvizsgálat + Létrehozás lap](./media/event-hubs-quickstart-portal/review-create.png)
      
   1. A **központi telepítés** lapon válassza az **erőforrás keresése** lehetőséget a névtér oldalának megnyitásához. 
      
      ![Az üzembe helyezés befejeződött – ugrás az erőforráshoz](./media/event-hubs-quickstart-portal/deployment-complete.png)  
   1. Ellenőrizze, hogy megjelenik-e a **Event Hubs névtér** oldal a következő példához hasonlóan:   
      
      ![A névtér kezdőlapja](./media/event-hubs-quickstart-portal/namespace-home-page.png)       

      > [!NOTE]
      > Az Azure Event Hubs egy Kafka-végpontot biztosít. Ez a végpont lehetővé teszi, hogy a Event Hubs névteret natív módon tudja értelmezni [Apache Kafka](https://kafka.apache.org/intro) üzenetküldési protokoll és API-k. Ezzel a képességgel a Kafka-témakörökhöz hasonlóan tud kommunikálni az Event hubokkal a protokoll-ügyfelek módosítása vagy a saját fürtök futtatása nélkül. A Event Hubs a Apache Kafka 1,0-es és újabb [verzióit](https://kafka.apache.org/10/documentation.html) támogatja. További információ: [Event Hubs használata Apache Kafka alkalmazásokból](event-hubs-for-kafka-ecosystem-overview.md).
    
## <a name="create-an-event-hub"></a>Eseményközpont létrehozása

Ha eseményközpontot szeretne létrehozni egy névtérben, végezze el a következő műveleteket:

1. A Event Hubs névtér lapon válassza a bal oldali menüben a **Event Hubs** lehetőséget.
1. Az ablak tetején válassza a **+ Event hub** elemet.
   
    ![Eseményközpont hozzáadása – gomb](./media/event-hubs-quickstart-portal/create-event-hub4.png)
1. Adja meg az Event hub nevét, majd válassza a **Létrehozás** lehetőséget.
   
    ![Eseményközpont létrehozása](./media/event-hubs-quickstart-portal/create-event-hub5.png)

    A **partíciók számának** beállítása lehetővé teszi, hogy integrálással a felhasználást számos fogyasztó között. További információ: [Partitions (partíciók](event-hubs-scalability.md#partitions)).

    Az **üzenet-megőrzési** beállítás határozza meg, hogy a Event Hubs szolgáltatás mennyi ideig tart az adatokban. További információ: [Event megtartás](event-hubs-features.md#event-retention).
1. Az Event hub létrehozásának állapotát a riasztásokban tekintheti meg. Az Event hub létrehozása után megjelenik az Event hubok listájában.

    ![Az Event hub létrehozva](./media/event-hubs-quickstart-portal/event-hub-created.png)
    
## <a name="next-steps"></a>Következő lépések

Ebben a cikkben egy erőforráscsoportot, egy Event Hubs-névteret és egy eseményközpontot hozott létre. Az események esemény-központból (vagy) való küldésével kapcsolatos részletes utasításokért tekintse meg a következő oktatóanyagokat: 

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [JavaScript](event-hubs-node-get-started-send.md)
- [Ugrás](event-hubs-go-get-started-send.md)
- [C (csak küldés)](event-hubs-c-getstarted-send.md)
- [Apache Storm (csak fogadás)](event-hubs-storm-getstarted-receive.md)


[Azure portal]: https://portal.azure.com/
[3]: ./media/event-hubs-quickstart-portal/sender1.png
[4]: ./media/event-hubs-quickstart-portal/receiver1.png
