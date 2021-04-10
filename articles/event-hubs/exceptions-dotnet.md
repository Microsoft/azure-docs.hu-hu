---
title: Azure Event Hubs – .NET-kivételek
description: Ez a cikk az Azure Event Hubs .NET-üzenetkezelési kivételek és a javasolt műveletek listáját tartalmazza.
services: event-hubs
documentationcenter: na
author: spelluru
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 09/23/2020
ms.author: spelluru
ms.openlocfilehash: af2d820fd255d041ade21a00d7d7a9ac6b12fcba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98745902"
---
# <a name="eventhubsexception---net"></a>EventHubsException – .NET
Egy **EventHubsException** akkor aktiválódik, ha egy Event Hubs-specifikus művelet hibát okozott, beleértve a szolgáltatáson belüli és az ügyfélre vonatkozó hibákat is. 

## <a name="exception-information"></a>Kivétel adatai
A kivétel a következő környezetfüggő információkat tartalmazza a hiba kontextusának és relatív súlyosságának megismeréséhez. 

- **IsTransient**: azt határozza meg, hogy a kivételt helyreállítható vagy sem kell-e tekinteni. Abban az esetben, ha átmenetinek ítélték, a megfelelő újrapróbálkozási házirend már alkalmazva lett, és az újrapróbálkozások sikertelenek voltak.
- **OK**: olyan jól ismert okokat biztosít, amelyek segítenek kategorizálni és tisztázni a kiváltó okot. Ezek az okok arra szolgálnak, hogy engedélyezzék a kivételek szűrését és más logikát a kivételt jelző üzenet szövegének vizsgálatakor. Néhány kulcsfontosságú hiba oka:
    - **Lezárt ügyfél**: akkor következik be, amikor egy olyan Event hub-ügyfél van, amely már le van zárva vagy el lett távolítva. Javasoljuk, hogy ellenőrizze az alkalmazás kódját, hogy az Event Hubs ügyféloldali függvénytárból származó objektumok létrehozása és bezárása megtörténjen-e a kívánt hatókörben.
    - **Szolgáltatás időkorlátja**: azt jelzi, hogy a Event Hubs szolgáltatás a várt időn belül nem válaszolt a műveletre. Lehetséges, hogy ezt a problémát átmeneti hálózati hiba vagy szolgáltatási probléma okozta. Előfordulhat, hogy a Event Hubs szolgáltatás sikeresen befejezte a kérést; az állapot nem ismert. Javasoljuk, hogy próbálja meg ellenőrizni az aktuális állapotot, és szükség esetén próbálkozzon újra.
    - **Kvóta túllépve**: azt jelzi, hogy túl sok aktív olvasási művelet van egyetlen fogyasztói csoport számára. Ez a korlát a Event Hubs névtér szintjétől függ, és szükség lehet a magasabb szintre való áttérésre. Alternatív megoldásként további fogyasztói csoportokat hozhat létre, és gondoskodhat arról, hogy az egyes csoportokhoz tartozó fogyasztói ügyfelek száma a korláton belül legyen. További információ: [Azure Event Hubs kvóták és korlátozások](event-hubs-quotas.md).
    - Az **üzenet mérete túllépve**: az események maximális mérete az egyes eseményekhez és a kötegekhez is engedélyezett. Magában foglalja az esemény adatait, valamint a hozzájuk kapcsolódó metaadatokat és rendszerterhelést. A hiba megoldásához csökkentse a kötegekben küldött események számát, vagy csökkentse az üzenetben szereplő adatmennyiséget. Mivel a méretkorlát változhat, tekintse meg az [Azure Event Hubs kvóták és](event-hubs-quotas.md) az egyes sajátosságokra vonatkozó korlátok című témakört.
    - A **fogyasztó kapcsolata megszakadt**: az Event hub szolgáltatás leválasztott egy fogyasztói ügyfelet az Event hub-példányból. Ez általában akkor fordul elő, ha egy magasabb tulajdonosi szintű fogyasztó a tulajdonjogot és a fogyasztói csoportok párosítását állítja be.
    - Az **erőforrás nem található**: a Event Hubs szolgáltatás nem talált olyan erőforrást, mint például az Event hub, a fogyasztói csoport vagy a partíció. Lehet, hogy törölték, vagy a Event Hubs szolgáltatással kapcsolatos probléma merült fel.

## <a name="handling-exceptions"></a>Kivételek kezelése
A **EventHubException**  többféleképpen is reagálhat a hibákra. Az egyik módszer a kivételek szűrésére vonatkozó záradék alkalmazása a Catch blokk részeként.

```csharp
try
{
    // Read events using the consumer client
}
catch (EventHubsException ex) where 
    (ex.Reason == EventHubsException.FailureReason.ConsumerDisconnected)
{
    // Take action based on a consumer being disconnected
}
```

## <a name="next-steps"></a>Következő lépések
Más kivételek vannak dokumentálva az [örökölt cikkben](event-hubs-messaging-exceptions.md). Némelyikük csak az örökölt Event Hubs .NET-ügyfél könyvtárára vonatkozik.