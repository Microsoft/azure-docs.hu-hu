---
title: Event Hubs dedikált fürt létrehozása a Azure Portal használatával
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre Azure Event Hubs-fürtöt a Azure Portal használatával.
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 6ff4ee1f098407ba8b3cd2727410bdfc842db89a
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102040090"
---
# <a name="quickstart-create-a-dedicated-event-hubs-cluster-using-azure-portal"></a>Rövid útmutató: dedikált Event Hubs-fürt létrehozása Azure Portal használatával 
Event Hubs-fürtök egybérlős üzemelő példányokat biztosítanak a legigényesebb folyamatos átviteli igényű ügyfelek számára. Ez az ajánlat garantált 99,99%-os SLA-val rendelkezik, és csak a dedikált díjszabási szinten érhető el. Egy [Event Hubs fürt](event-hubs-dedicated-overview.md) másodpercenként több millió eseményt tud befogadni a garantált kapacitással és a másodpercenkénti késéssel. A fürtön belül létrehozott névterek és az Event hubok a standard ajánlat összes funkcióját tartalmazzák, többek között a bejövő korlátok nélkül. A dedikált ajánlat további díj nélkül tartalmazza a népszerű [Event Hubs Capture](event-hubs-capture-overview.md) szolgáltatást, amely lehetővé teszi, hogy automatikusan batch-és naplózza az adatstreameket az [Azure-Blob Storage](../storage/blobs/storage-blobs-introduction.md) vagy az [1. generációs Azure Data Lake Storage](../data-lake-store/data-lake-store-overview.md).

A dedikált fürtök kiosztása és számlázása a **Kapacitási egységek (ke)** alapján történik, a processzor-és memória-erőforrások előre lefoglalt mennyisége. Minden fürthöz 1, 2, 4, 8, 12, 16 vagy 20 ke lehet vásárolni. Ebben a rövid útmutatóban bemutatjuk, hogyan hozhat létre egy 1 CU Event Hubs fürtöt a Azure Portal használatával.

> [!NOTE]
> Ez az önkiszolgáló élmény jelenleg előzetes verzióban érhető el az [Azure Portalon](https://aka.ms/eventhubsclusterquickstart). Ha bármilyen kérdése van a dedikált ajánlattal kapcsolatban, lépjen kapcsolatba a [Event Hubs csapatával](mailto:askeventhubs@microsoft.com).


## <a name="prerequisites"></a>Előfeltételek
A rövid útmutató elvégzéséhez győződjön meg róla, hogy rendelkezik az alábbiakkal:

- Egy Azure-fiók. Ha még nem rendelkezik ilyennel, a Kezdés előtt [vásároljon egy fiókot](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) . Ez a funkció ingyenes Azure-fiókkal nem támogatott. 
- [Visual Studio](https://visualstudio.microsoft.com/vs/) 2017 Update 3 (15,3-es, 26730,01-es verzió) vagy újabb.
- A [.NET Standard SDK](https://dotnet.microsoft.com/download) 2.0-s vagy újabb verziója.
- [Létrehozott egy erőforráscsoportot](../event-hubs/event-hubs-create.md#create-a-resource-group).

## <a name="create-an-event-hubs-dedicated-cluster"></a>dedikált Event Hubs-fürt létrehozása
Az Event Hubs-fürtök egyedi hatókörű tárolót biztosítanak, amelyben egy vagy több névteret hozhat létre. Ebben az előzetes verzióban a portál önkiszolgáló élményét, 1 CU-fürtöt hozhat létre a kiválasztott régiókban. Ha 1 CU-nál nagyobb fürtre van szüksége, küldhet egy Azure-támogatási kérést a fürt vertikális felskálázásához a létrehozása után.

> [!IMPORTANT]
> A fürt létrehozása után legalább 4 órával nem fogja tudni törölni a fürtöt. Ezért a fürt legalább 4 órányi használat után kell fizetnie. További információ a díjszabásról: [Event Hubs-díjszabás](https://azure.microsoft.com/pricing/details/event-hubs/). 

Ha a Azure Portal használatával szeretne fürtöt létrehozni az erőforráscsoporthoz, hajtsa végre a következő lépéseket:

1. [Ezt a hivatkozást](https://aka.ms/eventhubsclusterquickstart) követve hozzon létre egy fürtöt Azure Portalon. Ezzel szemben válassza a **minden szolgáltatás** lehetőséget a bal oldali navigációs panelen, majd írja be a "Event Hubs fürtök" kifejezést a keresőmezőbe, és válassza a "Event Hubs fürtök" lehetőséget az eredmények listájából.
2. A **fürt létrehozása** lapon adja meg a következő beállításokat:
    1. Adja meg a **fürt nevét**. A rendszer azonnal ellenőrzi, hogy a név elérhető-e.
    2. Válassza ki azt az **előfizetést** , amelyben létre szeretné hozni a fürtöt.
    3. Válassza ki azt az **erőforráscsoportot** , amelyben létre szeretné hozni a fürtöt.
    4. Válasszon egy **helyet** a fürt számára. Ha az előnyben részesített régió szürkén jelenik meg, átmenetileg nem áll rendelkezésre kapacitás, és a Event Hubs csapatnak küldhet [támogatási kérelmet](#submit-a-support-request) .
    5. A lap alján kattintson a **Next: Tags (tovább** ) gombra. Lehet, hogy néhány percet várnia kell, amíg a rendszer teljes mértékben kiépíti az erőforrásokat.

        ![Event Hubs-fürt létrehozása – alapismeretek lap](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-basics-page.png)
3. A **címkék** lapon adja meg a következőket:
    1. Adja meg a hozzáadni kívánt címke **nevét** és **értékét** . Ez a lépés **nem kötelező**.  
    2. Kattintson a **felülvizsgálat + létrehozás** gombra.

        ![Event Hubs-fürt oldalának létrehozása – címkék lap](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-tags-page.png)
4. A **felülvizsgálat + létrehozás** oldalon tekintse át a részleteket, és válassza a **Létrehozás** lehetőséget. 

    ![Event Hubs-fürt létrehozása lap – áttekintés + oldal létrehozása](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-review-create-page.png)

## <a name="create-a-namespace-and-event-hub-within-a-cluster"></a>Névtér és Event hub létrehozása fürtön belül

1. Ha névteret szeretne létrehozni a fürtön belül, a fürt **Event Hubs-fürt** lapján válassza a **+ névtér** lehetőséget a felső menüben.

    ![Fürt kezelése lap – névtér hozzáadása gomb](./media/event-hubs-dedicated-cluster-create-portal/cluster-management-page-add-namespace-button.png)
2. A **névtér létrehozása** oldalon hajtsa végre a következő lépéseket:
    1. Adja meg **a névtér nevét**.  A rendszer ellenőrzi, hogy a név elérhető-e.
    2. A névtér örökli a következő tulajdonságokat:
        1. Előfizetés azonosítója
        2. Erőforráscsoport
        3. Hely
        4. Fürt neve
    3. A névtér létrehozásához válassza a **Létrehozás** elemet. Most már kezelheti a fürtöt.  

        ![Névtér létrehozása a fürt oldalán](./media/event-hubs-dedicated-cluster-create-portal/create-namespace-cluster-page.png)
3. Miután létrehozta a névteret, [létrehozhat egy Event hub](event-hubs-create.md#create-an-event-hub) -t, ahogy azt általában egy névtéren belül létrehozta. 


## <a name="submit-a-support-request"></a>Támogatási kérelem beküldése

Ha módosítani szeretné a fürt méretét a létrehozás után, vagy ha az előnyben részesített régió nem érhető el, a következő lépéseket követve küldje el a támogatási kérést:

1. [Azure Portal](https://portal.azure.com)a bal oldali menüben kattintson a **Súgó és támogatás** elemre.
2. Válassza az **+ új támogatási kérés** lehetőséget a támogatási menüből.
3. A támogatás lapon kövesse az alábbi lépéseket:
    1. A **probléma típusa** beállításnál válassza a **technikai** lehetőséget a legördülő listából.
    2. Az **Előfizetés** beállításnál válassza ki az előfizetését.
    3. A **szolgáltatás** területen válassza **a saját szolgáltatások**, majd a **Event Hubs** lehetőséget.
    4. **Erőforrás** esetén válassza ki a fürtöt, ha az már létezik, ellenkező esetben válassza az **általános kérdés/erőforrás nem érhető el** lehetőséget.
    5. A **probléma típusa** beállításnál válassza a **kvóta** lehetőséget.
    6. A **probléma altípusa** elemnél válassza a következő értékek egyikét a legördülő listából:
        1. Válassza a **DEDIKÁLT SKU kérése** lehetőséget, hogy a szolgáltatás támogatott legyen a régióban.
        2. Válassza ki a **dedikált fürt vertikális felskálázására vagy méretezésére vonatkozó kérést** , ha a dedikált fürt vertikális felskálázását vagy méretezését szeretné végezni. 
    7. A **Tárgy** mezőben írja le a problémát.

        ![Támogatási jegy oldala](./media/event-hubs-dedicated-cluster-create-portal/support-ticket.png)

 ## <a name="delete-a-dedicated-cluster"></a>Dedikált fürt törlése
 
1. A fürt törléséhez válassza a **Törlés** lehetőséget a felső menüben. 

    > [!IMPORTANT]
    > A fürt létrehozása után legalább 4 órával nem fogja tudni törölni a fürtöt. Ezért a fürt legalább 4 órányi használat után kell fizetnie. További információ a díjszabásról: [Event Hubs-díjszabás](https://azure.microsoft.com/pricing/details/event-hubs/).     
1. Ekkor megjelenik egy üzenet, amely megerősíti, hogy szeretné törölni a fürtöt.
1. Írja be a **fürt nevét** , és válassza a **Törlés** lehetőséget a fürt törléséhez.

    ![Fürt törlése lap](./media/event-hubs-dedicated-cluster-create-portal/delete-cluster-page.png)


## <a name="next-steps"></a>Következő lépések
Ebben a cikkben egy Event Hubs-fürtöt hozott létre. Az Event hub eseményeinek küldésére és fogadására, valamint az események Azure Storage-ba vagy Azure Data Lake Storeba való rögzítésére vonatkozó részletes utasításokért tekintse meg a következő oktatóanyagokat:

- Események küldése és fogadása 
    - [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-java-get-started-send.md)
- [Event Hubs rögzítésének engedélyezése a Azure Portal használatával](event-hubs-capture-enable-through-portal.md)
- [Az Azure Event Hubs használata a Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
