---
title: Azure Service Bus Explorer használata adatműveletek végrehajtásához Service Bus (előzetes verzió)
description: Ez a cikk azt ismerteti, hogyan használható a portálon alapuló Azure Service Bus Explorer a Azure Service Bus adatok eléréséhez.
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: 0b5274c492a1dfb2523c52d7aea2b7ebf8eae675
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738956"
---
# <a name="use-service-bus-explorer-to-perform-data-operations-on-service-bus-preview"></a>Service Bus Explorer használata adatműveletek végrehajtásához Service Bus (előzetes verzió)

## <a name="overview"></a>Áttekintés

Azure Service Bus lehetővé teszi, hogy a küldő és a fogadó ügyfélalkalmazások leválasztják az üzleti logikát az ismerős pont-pont (Üzenetsor) és a közzétételi-előfizetés (témakör-előfizetés) szemantika használatával.

Egy Azure Service Bus névtéren végrehajtott műveletek két típusúak 
   * Felügyeleti műveletek – Service Bus névtér, várólisták, témakörök és előfizetések létrehozása, frissítése, törlése.
   * Adatműveletek – üzenetek küldése és fogadása várólistákból, témakörökből és előfizetésből.

A Azure Service Bus Explorer a felügyeleti műveleteken túl kibővíti a portál funkcióit, hogy támogassa az adatműveleteket (küldés, fogadás, betekintés) a várólisták, témakörök és előfizetések (valamint a kézbesítetlen levelek alentitásai) számára közvetlenül a Azure Portal.

> [!NOTE]
> Ez a cikk a Azure Portal Azure Service Bus Explorer funkcióit mutatja be.
>
> A Azure Service Bus Explorer eszköz ***nem*** a közösségi TULAJDONú OSS-eszköz [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer).
>

## <a name="prerequisites"></a>Előfeltételek

A Service Bus Explorer eszköz használatához a következő feladatokat kell elvégeznie: 

- Azure Service Bus névtér kiépítése.
- Hozzon létre egy várólistát, amely elküldheti és fogadja az üzenetet, vagy egy előfizetést tartalmazó témakörből kipróbálhatja a funkciót. A várólisták, témakörök és előfizetések létrehozásával kapcsolatos további információkért tekintse meg a következő cikkeket: 
    - [Rövid útmutató – várólisták létrehozása](service-bus-quickstart-portal.md)
    - [Gyors útmutató – témakörök létrehozása](service-bus-quickstart-topics-subscriptions-portal.md)
- Győződjön meg arról, hogy tagja a következő szerepkörök egyikének a névtéren: 
    - [Service Bus adattulajdonos](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner) 
    - [Közreműködő](../role-based-access-control/built-in-roles.md#contributor) 
    - [Tulajdonos](../role-based-access-control/built-in-roles.md#owner)


## <a name="using-the-service-bus-explorer"></a>A Service Bus Explorer használata

A Azure Service Bus Explorer használatához navigáljon ahhoz a Service Bus névtérhez, amelyen el szeretné végezni a küldési, betekintési és fogadási műveleteket.

Ha egy várólistán műveleteket kíván végrehajtani, válassza a **"várólisták"** lehetőséget a navigációs menüből. Ha egy témakörhöz (és kapcsolódó előfizetésekhez) szeretne műveleteket végrehajtani, válassza a **témakörök** lehetőséget. 

:::image type="content" source="./media/service-bus-explorer/queue-topics-left-navigation.png" alt-text="Entitás kiválasztása":::

A **"Queues"** vagy a **"témakörök**" kiválasztása után válassza ki az adott várólistát vagy témakört.

Válassza ki a **"Service Bus Explorer (előzetes verzió)"** lehetőséget a bal oldali navigációs menüből.

:::image type="content" source="./media/service-bus-explorer/left-navigation-menu-selected.png" alt-text="SB Explorer – bal oldali navigációs menü":::

### <a name="sending-a-message-to-a-queue-or-topic"></a>Üzenet küldése üzenetsor vagy témakör számára

Ha üzenetet szeretne küldeni egy **üzenetsor** vagy egy **témakör** számára, kattintson a **_Küldés_** lapra a Service Bus Explorerben.

Üzenet összeállítása itt: 

1. Válassza ki a **tartalom típusát** "text/plain", "Application/XML" vagy "Application/JSON" értékűre.
2. Adja hozzá az üzenet **tartalmát**. Győződjön meg arról, hogy az megegyezik a korábban beállított **tartalomtípussal** .
3. A **Speciális tulajdonságok** beállítása (nem kötelező) – ilyenek például a korrelációs azonosító, az üzenet azonosítója, a címke, a ReplyTo, az élettartam (TTL) és az ütemezett sorba helyezni idő (ütemezett üzenetekhez).
4. **Egyéni tulajdonságok** beállítása – bármely olyan felhasználói tulajdonság lehet, amely a szótár kulcsára van beállítva.

Az üzenet létrejötte után kattintson a Send (küldés) elemre.

:::image type="content" source="./media/service-bus-explorer/send-experience.png" alt-text="Üzenet összeállítása":::

Ha a küldési művelet sikeresen befejeződött, 

* Ha a várólistára küldi az üzenetet, az **aktív üzenetek** metrikái számlálója megnő.

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="QueueAfterSendMetrics":::

* Ha a témakörbe küldi az üzenetet, az **aktív üzenetek** mérőszámai számláló azon előfizetés értékét fogja növelni, amelyben az üzenet át lett irányítva.

    :::image type="content" source="./media/service-bus-explorer/topic-after-send-metrics.png" alt-text="TopicAfterSendMetrics":::

### <a name="receiving-a-message-from-a-queue"></a>Üzenet fogadása egy várólistából

A Service Bus Explorer fogadási funkciója lehetővé teszi, hogy egyszerre egyetlen üzenetet kapjon. A fogadási művelet a **ReceiveAndDelete** mód használatával történik.

> [!IMPORTANT]
> Vegye figyelembe, hogy a Service Bus Explorer által végrehajtott fogadási művelet ***roncsolásos fogadás***, azaz az üzenet el lesz távolítva a várólistából, amikor megjelenik a Service Bus Explorer eszközön.
>
> Az üzenetek várólistából való eltávolítása nélküli tallózáshoz érdemes lehet a ***betekintés*** funkciót használni.
>

Üzenet fogadása egy várólistáról (vagy annak kézbesítetlen levelek-alsorából) 

1. Kattintson a Service Bus Explorer ***fogadás*** fülére.
2. Ellenőrizze a metrikákat, és győződjön meg arról, hogy vannak-e **aktív üzenetek** vagy **kézbesítetlen üzenetek** a fogadáshoz.

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="QueueAfterSendMetrics":::

3. Válasszon a ***várólista** _ vagy a _ *_kézbesítetlen levelek_** alvárólista között.

    :::image type="content" source="./media/service-bus-explorer/queue-or-deadletter.png" alt-text="QueueOrDeadletter":::

4. Kattintson a ***fogadás** _ gombra, majd a _ *_Igen_** lehetőségre a "fogadás és törlés" művelet megerősítéséhez.


Ha a fogadási művelet sikeres, az üzenet adatai a rácson az alábbiak szerint jelennek meg. A részletek megjelenítéséhez kiválaszthatja az üzenetet a rácsból.

:::image type="content" source="./media/service-bus-explorer/receive-message-from-queue-2.png" alt-text="Képernyőkép a Azure Service Bus Explorerben a várólisták ablakról, amelyen megjelenik egy üzenet, amely a várólistában kiválasztott aktív üzenetre mutat.":::


### <a name="peeking-a-message-from-a-queue"></a>Üzenet bepillantása egy várólistából

A betekintés funkcióval a Service Bus Explorer használatával megtekintheti az üzenetsor vagy a kézbesítetlen levelek-várólista legfontosabb 32-üzeneteit.

1. Ha egy várólistán szeretné betekintést az üzenetbe, kattintson a ***betekintés*** lapra a Service Bus Explorerben.

    :::image type="content" source="./media/service-bus-explorer/peek-tab-selected.png" alt-text="PeekTab":::

2. Ellenőrizze a metrikákat, hogy vannak-e **aktív üzenetek** vagy **kézbesítetlen üzenetek** a betekintés előtt.

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="QueueAfterSendMetrics":::

3. Ezután válasszon a ***várólista** _ vagy a _ *_kézbesítetlen levelek_** alvárólista között.

    :::image type="content" source="./media/service-bus-explorer/queue-or-deadletter.png" alt-text="QueueOrDeadletter":::

4. Kattintson a ***betekintés*** gombra. 

Miután a betekintés művelet befejeződik, legfeljebb 32 üzenet jelenik meg a rácson az alábbi módon. Egy adott üzenet részleteinek megtekintéséhez válassza ki azt a rácsból. 

:::image type="content" source="./media/service-bus-explorer/peek-message-from-queue-2.png" alt-text="PeekMessageFromQueue":::

> [!NOTE]
>
> Mivel a betekintés nem roncsolásos művelet, az üzenet **nem** lesz eltávolítva a várólistából.
>

### <a name="receiving-a-message-from-a-subscription"></a>Üzenet fogadása egy előfizetésből

A várólistához hasonlóan a ***fogadási*** művelet is elvégezhető egy előfizetésen (vagy annak kézbesítetlen levelek-entitásán). Mivel azonban az előfizetés a témakör kontextusában él, a fogadási műveletet a Service Bus Explorer egy adott témakörhöz való navigálásával hajtja végre.

> [!IMPORTANT]
> Vegye figyelembe, hogy a Service Bus Explorer által végrehajtott fogadási művelet ***roncsolásos fogadás***, azaz az üzenet el lesz távolítva a várólistából, amikor megjelenik a Service Bus Explorer eszközön.
>
> Az üzenetek várólistából való eltávolítása nélküli tallózáshoz érdemes lehet a ***betekintés*** funkciót használni.
>

1. Kattintson a ***fogadás** _ lapra, és válassza ki az adott _-*_előfizetést_* a legördülő listából.

    :::image type="content" source="./media/service-bus-explorer/receive-subscription-tab-selected.png" alt-text="ReceiveTabSelected":::

2. Válasszon az ***előfizetés** _ vagy a _ *_kézbesítetlen levelek_** alentitás között.

    :::image type="content" source="./media/service-bus-explorer/subscription-or-deadletter.png" alt-text="SubscriptionOrDeadletter":::

3. Kattintson a ***fogadás** _ gombra, majd a _ *_Igen_** lehetőségre a "fogadás és törlés" művelet megerősítéséhez.

Ha a fogadási művelet sikeres, a fogadott üzenet az alábbi módon jelenik meg a rácson. Az üzenet részleteinek megtekintéséhez kattintson az üzenetre.

:::image type="content" source="./media/service-bus-explorer/receive-message-from-subscription.png" alt-text="Képernyőfelvétel a Azure Service Bus Explorer Receive (fogadás) lapjáról, amelyen a kapott aktív üzenet részletei jelennek meg.":::

### <a name="peeking-a-message-from-a-subscription"></a>Üzenet bepillantása egy előfizetésből

Az előfizetésben vagy a kézbesítetlen levelek alentitásban lévő üzenetek tallózásához a ***betekintés*** funkció is használható az előfizetésben.

1. Kattintson a ***betekintés** _ lapra, és válassza ki az adott _-*_előfizetést_* a legördülő listából.

    :::image type="content" source="./media/service-bus-explorer/peek-subscription-tab-selected.png" alt-text="PeekTabSelected":::

2. Válasszon az ***előfizetés** _ vagy a _ *_kézbesítetlen levelek_** alentitás között.

    :::image type="content" source="./media/service-bus-explorer/subscription-or-deadletter.png" alt-text="SubscriptionOrDeadletter":::

3. Kattintson a ***betekintés*** gombra.

Miután a betekintés művelet befejeződik, legfeljebb 32 üzenet jelenik meg a rácson az alábbi módon. Egy adott üzenet részleteinek megtekintéséhez válassza ki azt a rácsból. 

:::image type="content" source="./media/service-bus-explorer/peek-message-from-subscription.png" alt-text="PeekMessageFromSubscription":::

> [!NOTE]
>
> Mivel a betekintés nem roncsolásos művelet, az üzenet **nem** lesz eltávolítva a várólistából.
>

## <a name="next-steps"></a>Következő lépések

   * További információ a Service Bus [várólistákról](service-bus-queues-topics-subscriptions.md#queues) és [témakörökről](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions)
   * További információ [Service Bus várólisták létrehozásáról a Azure Portal használatával](service-bus-quickstart-portal.md)
   * További információ [Service Bus témakörök és előfizetések létrehozásáról a Azure Portal használatával](service-bus-quickstart-topics-subscriptions-portal.md)