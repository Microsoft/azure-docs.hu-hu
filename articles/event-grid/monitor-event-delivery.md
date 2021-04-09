---
title: Azure Event Grid mérőszámok megtekintése és riasztások beállítása
description: Ez a cikk bemutatja, hogyan tekintheti meg Azure Event Grid témakörök és előfizetések metrikáit, és hogyan hozhat létre riasztásokat a Azure Portal használatával.
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: 6f6c119c16452246ec6eeb57ab392b29608938a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104598558"
---
# <a name="monitor-event-grid-message-delivery"></a>Figyelő Event Grid üzenet kézbesítése 
Ez a cikk azt ismerteti, hogyan használható a portál a Event Grid témakörök és előfizetések metrikáinak megjelenítéséhez, valamint riasztások létrehozásához. 

> [!IMPORTANT]
> A Azure Event Grid támogatott metrikák listáját itt tekintheti meg: [mérőszámok](metrics.md).

## <a name="view-custom-topic-metrics"></a>Egyéni témakör metrikáinak megtekintése

Ha közzétett egy egyéni témakört, megtekintheti a metrikákat. 

1. Jelentkezzen be [Azure Portalba](https://portal.azure.com/).
2. A témakörben található keresősáv mezőbe írja be **Event Grid témaköröket**, majd válassza a **Event Grid témakörök** elemet a legördülő listából. 

    :::image type="content" source="./media/custom-event-quickstart-portal/select-event-grid-topics.png" alt-text="Event Grid témakörök keresése és kiválasztása":::
3. Válassza ki az egyéni témakört a témakörök listájában. 

    :::image type="content" source="./media/monitor-event-delivery/select-custom-topic.png" alt-text="Egyéni témakör kiválasztása":::
4. Tekintse meg az egyéni esemény témakör metrikáit a **Event Grid témakör** oldalon. A következő ábrán az **alapvető** tudnivalókat tartalmazó szakasz az erőforráscsoport, az előfizetés stb. értékre van lecsökkentve. 

    :::image type="content" source="./media/monitor-event-delivery/custom-topic-metrics.png" alt-text="Esemény metrikáinak megtekintése":::

    A támogatott metrikákkal rendelkező diagramokat a **Event Grid témakör** oldal **mérőszámok** lapján lehet létrehozni.

    :::image type="content" source="./media/monitor-event-delivery/topics-metrics-page.png" alt-text="Témakör – mérőszámok lap":::

    Tekintse meg például a **közzétett események** metrikájának mérőszámait tartalmazó diagramot.

    :::image type="content" source="./media/monitor-event-delivery/custom-topic-metrics-example.png" alt-text="Közzétett események mérőszáma":::


## <a name="view-subscription-metrics"></a>Előfizetési mérőszámok megtekintése
1. Az előző szakasz lépéseit követve navigáljon a **Event Grid témakör** oldalára. 
2. Válassza ki az előfizetést az alsó ablaktáblán az alábbi példában látható módon. 

    :::image type="content" source="./media/monitor-event-delivery/select-event-subscription.png" alt-text="Esemény-előfizetés kiválasztása":::    

    **Event Grid előfizetéseket** a Azure Portal keresési sávjában is megkeresheti, ha az esemény-előfizetés megjelenítéséhez a **témakör típusa**, az **előfizetés** és a **hely** lehetőséget választja. 

    :::image type="content" source="./media/monitor-event-delivery/event-subscriptions-page.png" alt-text="Esemény-előfizetés kiválasztása Event Grid-előfizetések lapról":::        

    Egyéni témakörök esetén válassza ki a témakörök **típusaként** **Event Grid témákat** . A rendszertémák területen válassza ki az Azure-erőforrás típusát (például Storage- **fiókok (blob, GPv2))**. 
3. A diagramon az előfizetés kezdőlapján tekintheti meg az előfizetés mérőszámait. Az elmúlt 1 óra, 6 óra, 12 óra, 1 nap, 7 nap vagy 30 nap alatt megtekintheti az **általános**, a **hibák** és a **késési** metrikákat. 

    :::image type="content" source="./media/monitor-event-delivery/subscription-home-page-metrics.png" alt-text="Metrikák az előfizetés kezdőlapján":::    

## <a name="view-system-topic-metrics"></a>Rendszertémakör metrikáinak megtekintése

1. Jelentkezzen be [Azure Portalba](https://portal.azure.com/).
2. A témakörben található keresősáv mezőbe írja be **Event Grid Rendszertémaköröket**, majd válassza a **Event Grid rendszertémakörök** lehetőséget a legördülő listából. 

    :::image type="content" source="./media/monitor-event-delivery/search-system-topics.png" alt-text="Event Grid rendszerbeli témakörök keresése és kiválasztása":::
3. A témakörök listájából válassza ki a rendszer témakört. 

    :::image type="content" source="./media/monitor-event-delivery/select-system-topic.png" alt-text="A System témakör kiválasztása":::
4. Tekintse meg a rendszertémakör metrikáit a **Event Grid rendszertémakör** oldalon. A következő ábrán az **alapvető** tudnivalókat tartalmazó szakasz az erőforráscsoport, az előfizetés stb. értékre van lecsökkentve. 

    :::image type="content" source="./media/monitor-event-delivery/system-topic-overview-metrics.png" alt-text="A rendszertémakör metrikáinak megtekintése az Áttekintés oldalon":::

    A támogatott metrikákkal rendelkező diagramokat a **Event Grid témakör** oldal **mérőszámok** lapján lehet létrehozni.

    :::image type="content" source="./media/monitor-event-delivery/system-topic-metrics-page.png" alt-text="Rendszertémakör – mérőszámok lap":::

    > [!IMPORTANT]
    > A Azure Event Grid támogatott metrikák listáját itt tekintheti meg: [mérőszámok](metrics.md).

## <a name="next-steps"></a>Következő lépések
Lásd az alábbi cikkeket:

- A metrikák és a tevékenységek naplózási műveleteinek létrehozásáról a [riasztások beállítása](set-alerts.md)című témakörben olvashat bővebben.
- További információ az események kézbesítéséről és újrapróbálkozásáról, [Event Grid az üzenetek kézbesítéséről, és próbálkozzon újra](delivery-and-retry.md).
