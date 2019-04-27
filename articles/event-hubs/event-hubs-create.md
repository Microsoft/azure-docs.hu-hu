---
title: Rövid Azure-útmutató – Eseményközpont létrehozása az Azure Portallal | Microsoft Docs
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre egy Azure-eseményközpontot az Azure Portallal, majd hogyan küldhet és fogadhat eseményeket a .NET Standard SDK használatával.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: quickstart
ms.custom: mvc
ms.date: 01/23/2019
ms.author: shvija
ms.openlocfilehash: 9cd7a207a76ddfbeeb3b2e6b46abbc6423825093
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60823058"
---
# <a name="quickstart-create-an-event-hub-using-azure-portal"></a>Gyors útmutató: Létrehoz egy eseményközpontot, az Azure portal használatával
Az Azure Event Hubs egy Big Data streamplatform és eseményfeldolgozó szolgáltatás, amely másodpercenként több millió esemény fogadására és feldolgozására képes. Az Event Hubs képes az elosztott szoftverek és eszközök által generált események, adatok vagy telemetria feldolgozására és tárolására. Az eseményközpontokba elküldött adatok bármilyen valós idejű elemzési szolgáltató vagy kötegelési/tárolóadapter segítségével átalakíthatók és tárolhatók. Az Event Hubs részletes áttekintéséért lásd az [Event Hubs áttekintését](event-hubs-about.md) és az [Event Hubs-szolgáltatásokat](event-hubs-features.md) ismertető cikket.

Ebben a rövid útmutatóban az [Azure Portal](https://portal.azure.com) használatával hoz létre eseményközpontot.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez győződjön meg róla, hogy rendelkezik az alábbiakkal:

- Egy Azure-előfizetés. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/), mielőtt hozzákezd.
- [Visual Studio 2017 3-as frissítés (verziószám: 15.3, 26730.01)](https://www.visualstudio.com/vs) vagy újabb.
- A [.NET Standard SDK](https://www.microsoft.com/net/download/windows) 2.0-s vagy újabb verziója.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az erőforráscsoport Azure-erőforrások logikai gyűjteménye. Minden erőforrás üzembe helyezése és kezelése erőforráscsoportban történik. Hozzon létre egy erőforráscsoportot:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldali menüben válassza az **Erőforráscsoportok** lehetőséget. Ezután kattintson az **Add** (Hozzáadás) gombra.

   ![Erőforráscsoportok – Hozzáadás gomb](./media/event-hubs-quickstart-portal/resource-groups1.png)

2. A **előfizetés**, válassza ki a nevét, az Azure-előfizetést, amelyben szeretné létrehozni az erőforráscsoportot.
3. Adjon meg egy egyedi **az erőforráscsoport neve**. A rendszer azonnal ellenőrzi, hogy a név elérhető-e az aktuálisan kiválasztott Azure-előfizetésben.
4. Válassza ki a **régió** az erőforráscsoportnak.
5. Válassza ki **felülvizsgálat + létrehozás**.

   ![Erőforráscsoport – létrehozás](./media/event-hubs-quickstart-portal/resource-groups2.png)
6. Az a **felülvizsgálat + létrehozás** lapon jelölje be **létrehozás**. 

## <a name="create-an-event-hubs-namespace"></a>Event Hubs-névtér létrehozása

Egy Event Hubs-névtér egyedi hatókörkezelési tárolót biztosít, amelyre a teljes tartománynevével lehet hivatkozni, és ebben a tárolóban egy vagy több eseményközpontot is létrehozhat. Ha létre szeretne hozni egy névteret az erőforráscsoportban a portál használatával, végezze el az alábbi műveleteket:

1. Az Azure Portalon kattintson az **Erőforrás létrehozása** gombra a képernyő bal felső részén.
2. Válassza ki **minden szolgáltatás** a bal oldali menüben, és válassza a **csillag (`*`)** melletti **az Event Hubs** a a **Analytics** kategória. Ellenőrizze, hogy **az Event Hubs** adnak hozzá **Kedvencek** a bal oldali navigációs menüben. 
    
   ![Az Event Hubs keresése](./media/event-hubs-quickstart-portal/select-event-hubs-menu.png)
3. Válassza ki **az Event Hubs** alatt **Kedvencek** a bal oldali navigációs menü, és válassza a **Hozzáadás** az eszköztáron.

   ![Gomb hozzáadása](./media/event-hubs-quickstart-portal/event-hubs-add-toolbar.png)
4. Az a **névtér létrehozása** lapon, tegye a következőket:
    1. Adja meg a névtér nevét. A rendszer azonnal ellenőrzi, hogy a név elérhető-e.
    2. Válassza ki a tarifacsomagot (Basic vagy Standard).
    3. Válassza ki a **előfizetés** , amelyre szeretné létrehozni a névteret.
    4. Válassza ki a **hely** a névtérhez.
    5. Kattintson a **Létrehozás** gombra. Lehet, hogy néhány percet várnia kell, amíg a rendszer teljes mértékben kiépíti az erőforrásokat.

       ![Eseményközpont-névtér létrehozása](./media/event-hubs-quickstart-portal/create-event-hub1.png)
5. Frissítse a **az Event Hubs** oldalon tekintheti meg az eseményközpont-névteret. Az eseményközpont létrehozása a riasztások állapotát ellenőrizheti. 

    ![Eseményközpont-névtér létrehozása](./media/event-hubs-quickstart-portal/event-hubs-refresh.png)
6. Válassza ki a névteret. Megjelenik a kezdőlapja a **Event Hubs-névtér** a portálon. 

   ![A névtér kezdőlapja](./media/event-hubs-quickstart-portal/namespace-home-page.png)
    
## <a name="create-an-event-hub"></a>Eseményközpont létrehozása

Ha eseményközpontot szeretne létrehozni egy névtérben, végezze el a következő műveleteket:

1. Az Event Hubs-Namespace oldalon válassza ki a **az Event Hubs** a bal oldali menüben.
1. Az ablak tetején kattintson a **+ Eseményközpont** elemre.
   
    ![Eseményközpont hozzáadása – gomb](./media/event-hubs-quickstart-portal/create-event-hub4.png)
1. Adjon egy nevet az eseményközpontnak, majd kattintson a **Létrehozás** elemre.
   
    ![Eseményközpont létrehozása](./media/event-hubs-quickstart-portal/create-event-hub5.png)
4. Az eseményközpont létrehozása a riasztások állapotát ellenőrizheti. Az eseményközpont létrehozása után megjelenik a listában, az event hubs az alábbi képen látható módon:

    ![Eseményközpont létrehozása](./media/event-hubs-quickstart-portal/event-hub-created.png)

Gratulálunk! A Portal segítségével létrehozott egy Event Hubs-névteret és egy eseményközpontot a névtéren belül. 

## <a name="next-steps"></a>További lépések

Ebben a cikkben egy erőforráscsoportot, egy Event Hubs-névteret és egy eseményközpontot hozott létre. Események küldése (vagy) események fogadása az event hubs részletes utasításokért lásd: a **események küldéséhez és fogadásához** oktatóanyagok: 

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [.NET-keretrendszer](event-hubs-dotnet-framework-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [Node.js](event-hubs-node-get-started-send.md)
- [Go](event-hubs-go-get-started-send.md)
- [C (csak küldése)](event-hubs-c-getstarted-send.md)
- [Az Apache Storm (csak reecive)](event-hubs-storm-getstarted-receive.md)


[Azure portal]: https://portal.azure.com/
[3]: ./media/event-hubs-quickstart-portal/sender1.png
[4]: ./media/event-hubs-quickstart-portal/receiver1.png
