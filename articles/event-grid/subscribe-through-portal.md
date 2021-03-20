---
title: Előfizetések Azure Event Grid portálon keresztül
description: Ez a cikk azt ismerteti, hogyan hozhatók létre Event Grid-előfizetések a támogatott forrásokhoz, például az Azure Blob Storagehoz a Azure Portal használatával.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: e80e2243c93ab38187646256f567d6ab73c40100
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95995063"
---
# <a name="subscribe-to-events-through-portal"></a>Előfizetés az eseményekre a portálon keresztül

Ez a cikk azt ismerteti, hogyan hozhatók létre Event Grid-előfizetések a portálon keresztül.

## <a name="create-event-subscriptions"></a>Esemény-előfizetések létrehozása

Ha Event Grid-előfizetést szeretne létrehozni bármelyik támogatott [eseményforrás](overview.md#event-sources)esetében, kövesse az alábbi lépéseket. Ez a cikk bemutatja, hogyan hozhat létre Event Grid-előfizetést egy Azure-előfizetéshez.

1. Válassza az **Összes szolgáltatás** elemet.

   ![Minden szolgáltatás kiválasztása](./media/subscribe-through-portal/select-all-services.png)

1. Keressen **Event Grid előfizetéseket** , és válassza ki az elérhető lehetőségek közül.

   ![A képernyőfelvételen a Azure Portal a kiválasztott Event Grid-előfizetésekkel rendelkező keresés jelenik meg.](./media/subscribe-through-portal/search.png)

1. Válassza a **+ Esemény-előfizetés** lehetőséget.

   ![Előfizetés hozzáadása](./media/subscribe-through-portal/add-subscription.png)

1. Válassza ki a létrehozni kívánt előfizetés típusát. Ha például az Azure-előfizetéséhez tartozó eseményekre szeretne előfizetni, válassza az **Azure-előfizetések** és a cél előfizetés lehetőséget.

   ![Azure-előfizetés kiválasztása](./media/subscribe-through-portal/azure-subscription.png)

1. Az eseményforrás összes eseménytípus előfizetéséhez tartsa be az **előfizetést az összes eseménytípus** beállításnál. Egyéb esetben válassza ki az előfizetéshez tartozó eseménytípus típusát.

   ![Eseménytípus kiválasztása](./media/subscribe-through-portal/select-event-types.png)

1. Adja meg az esemény-előfizetés további részleteit, például az események kezelésére szolgáló végpontot és az előfizetés nevét.

   ![Képernyőkép, amely a "végpont részletei" és az "esemény-előfizetés részletei" szakaszt jeleníti meg a megadott előfizetés-név értékkel.](./media/subscribe-through-portal/provide-subscription-details.png)

1. A kézbesítetlen üzenetek engedélyezéséhez és az újrapróbálkozási szabályzatok testreszabásához válassza a **További szolgáltatások** lehetőséget.

   ![További funkciók kiválasztása](./media/subscribe-through-portal/select-additional-features.png)

1. Válasszon ki egy tárolót, amelyet nem kézbesítő események tárolására kíván használni, és állítsa be az újrapróbálkozások küldésének módját.

   ![A kézbesítetlen levelek engedélyezése és újrapróbálkozás](./media/subscribe-through-portal/set-deadletter-retry.png)

1. Ha elkészült, válassza a **Létrehozás** lehetőséget.

## <a name="create-subscription-on-resource"></a>Előfizetés létrehozása erőforráson

Néhány eseményforrás támogatja az esemény-előfizetések létrehozását az adott erőforráshoz tartozó portál felületén keresztül. Válassza ki az eseményforrás, és keresse meg az **eseményeket** a bal oldali ablaktáblán.

![Előfizetés részleteinek megadása](./media/subscribe-through-portal/resource-events.png)

A portálon megtekintheti az adott forráshoz kapcsolódó esemény-előfizetések létrehozásának lehetőségeit.

## <a name="next-steps"></a>Következő lépések

* További információ az események kézbesítéséről és újrapróbálkozásáról, [Event Grid az üzenetek kézbesítéséről, és próbálkozzon újra](delivery-and-retry.md).
* Az Event Grid ismertetése: [Az Event Grid bemutatása](overview.md).
* Az Event Grid használatának gyors megkezdéséhez tekintse meg [az egyéni események létrehozása és irányítása Azure Event Grid](custom-event-quickstart.md)használatával című témakört.
