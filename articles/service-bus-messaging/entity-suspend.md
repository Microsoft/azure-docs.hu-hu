---
title: Azure Service Bus – üzenetkezelési entitások felfüggesztése
description: Ez a cikk azt ismerteti, hogyan lehet ideiglenesen felfüggeszteni és újraaktiválni Azure Service Bus üzenet entitásait (várólisták, témakörök és előfizetések).
ms.topic: article
ms.date: 09/29/2020
ms.openlocfilehash: ea1acab3d0a86b0064f8b3eef7bfd1496bd17041
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94543051"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>Üzenetkezelési entitások felfüggesztése és újraaktiválása (Letiltás)

A várólisták, témakörök és előfizetések ideiglenesen fel lesznek függesztve. A felfüggesztés letiltott állapotba helyezi az entitást, amelyben az összes üzenet a tárolóban marad. Az üzenetek azonban nem távolíthatók el és nem vehetők fel, és a vonatkozó protokollok műveleteinek hibái.

Sürgős felügyeleti okokból érdemes lehet felfüggeszteni egy entitást. Például egy hibás fogadó üzenetet küld a várólistáról, a folyamat sikertelen lesz, és még nem megfelelően végzi el az üzenetek feldolgozását és eltávolítását. Ebben az esetben előfordulhat, hogy le kívánja tiltani a fogadott várólistát, amíg ki nem javítsa és nem telepíti a kódot. 

A felfüggesztés vagy az újraaktiválás a felhasználó vagy a rendszer által végezhető el. A rendszerek csak olyan súlyos adminisztratív okok miatt felfüggesztik az entitásokat, mint például az előfizetés költségkeretének korlátozása. A felhasználó nem tudja újraaktiválni a rendszer által letiltott entitásokat, de a felfüggesztés oka miatt visszaállnak.

## <a name="queue-status"></a>Várólista állapota 
A **várólistára** beállítható állapotok a következők:

-   **Aktív**: a várólista aktív. Üzeneteket küldhet és fogadhat üzeneteket a várólistából. 
-   **Letiltva**: a várólista fel van függesztve. Ez egyenértékű a **SendDisabled** és a **ReceiveDisabled** beállításával. 
-   **SendDisabled**: nem küldhet üzeneteket a várólistába, de üzeneteket is fogadhat. Kivételt fog kapni, ha üzeneteket próbál küldeni a várólistára. 
-   **ReceiveDisabled**: küldhet üzeneteket a várólistába, de nem fogadhat üzeneteket. Kivételt fog kapni, ha üzeneteket próbál fogadni a várólistára.


### <a name="change-the-queue-status-in-the-azure-portal"></a>Módosítsa a várólista állapotát a Azure Portalban: 

1. A Azure Portal navigáljon a Service Bus-névtérhez. 
1. Válassza ki azt a várólistát, amelynek az állapotát módosítani szeretné. A várólisták a középső alsó ablaktáblán jelennek meg. 
1. A **Service Bus üzenetsor** lapon tekintse meg a várólista aktuális állapotát hiperhivatkozásként. Ha az **Áttekintés** nincs kiválasztva a bal oldali menüben, válassza ki azt a várólista állapotának megtekintéséhez. Válassza ki a várólista aktuális állapotát a módosításhoz. 

    :::image type="content" source="./media/entity-suspend/select-state.png" alt-text="Várólista állapotának kiválasztása":::
4. Válassza ki a várólista új állapotát, és kattintson az **OK gombra**. 

    :::image type="content" source="./media/entity-suspend/entity-state-change.png" alt-text="A várólista állapotának beállítása":::
    
Letilthatja a küldési és fogadási műveleteket a .NET SDK Service Bus [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) API-jai segítségével, illetve az Azure CLI vagy a Azure PowerShell használatával Azure Resource Manager sablonnal is.

### <a name="change-the-queue-status-using-azure-powershell"></a>A várólista állapotának módosítása a Azure PowerShell használatával
A várólista letiltására szolgáló PowerShell-parancs az alábbi példában látható. Az újraaktiválási parancs egyenértékű, aktív értékre van állítva `Status` . 

```powershell
$q = Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="topic-status"></a>Témakör állapota
A témakör állapota módosítható a Azure Portalban. Válassza ki a témakör aktuális állapotát a következő oldal megjelenítéséhez, amely lehetővé teszi az állapot módosítását. 

:::image type="content" source="./media/entity-suspend/topic-state-change.png" alt-text="Témakör állapotának módosítása":::

A **témakörben** beállítható állapotok a következők:
- **Aktív**: a témakör aktív. Üzeneteket küldhet a témakörbe. 
- **Letiltva**: a témakör fel van függesztve. Nem lehet üzeneteket küldeni a témakörbe. 
- **SendDisabled**: ugyanaz a hatás, mint a **Letiltva**. Nem lehet üzeneteket küldeni a témakörbe. Kivételt fog kapni, ha üzenetet próbál küldeni a témakörnek. 

## <a name="subscription-status"></a>Előfizetés állapota
Az előfizetések állapotát a Azure Portal módosíthatja. Válassza ki az előfizetés aktuális állapotát a következő oldal megjelenítéséhez, amely lehetővé teszi az állapot módosítását. 

:::image type="content" source="./media/entity-suspend/subscription-state-change.png" alt-text="Előfizetés állapotának módosítása":::

Az **előfizetéshez** megadható állapotok a következők:
- **Aktív**: az előfizetés aktív. Az előfizetéshez tartozó üzenetet fogadhat.
- **Letiltva**: az előfizetés fel van függesztve. Nem fogadhat üzeneteket az előfizetésből. 
- **ReceiveDisabled**: ugyanaz a hatás, mint a **Letiltva**. Nem fogadhat üzeneteket az előfizetésből. Kivételt fog kapni, ha üzeneteket próbál fogadni az előfizetésbe.

| Témakör állapota | Előfizetés állapota | Működés | 
| ------------ | ------------------- | -------- | 
| Aktív | Aktív | Üzeneteket küldhet a témakörnek, és üzeneteket fogadhat az előfizetésből. | 
| Aktív | Letiltva vagy fogadás letiltva | Üzeneteket küldhet a témakörbe, de nem fogadhat üzeneteket az előfizetésből | 
| Letiltva vagy küldés letiltva | Aktív | Nem küldhet üzeneteket a témakörbe, de az előfizetésben már megjelenő üzeneteket is fogadhat. | 
| Letiltva vagy küldés letiltva | Letiltva vagy fogadás letiltva | Nem lehet üzenetet küldeni a témakörnek, és nem kaphat az előfizetésből. | 

## <a name="other-statuses"></a>Egyéb állapotok
A [EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) enumerálás olyan átmeneti állapotokat is meghatároz, amelyeket csak a rendszer adhat meg. 


## <a name="next-steps"></a>Következő lépések

Az Service Bus üzenetkezeléssel kapcsolatos további tudnivalókért tekintse meg a következő témaköröket:

* [Service Bus queues, topics, and subscriptions (Service Bus-üzenetsorok, -témakörök és -előfizetések)](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/entity-state-change.png

