---
title: Event Grid problémák elhárítása
description: Ez a cikk a Azure Event Grid problémák elhárításának különböző módjait ismerteti
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: d30b8464de90474ad74853cc423de700b41226a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104720559"
---
# <a name="troubleshoot-azure-event-grid-issues"></a>Azure Event Grid problémák elhárítása
Ez a cikk olyan információkat tartalmaz, amelyek segítenek a Azure Event Grid problémák elhárításában. 

## <a name="diagnostic-logs"></a>Diagnosztikai naplók
Diagnosztikai beállítások engedélyezése Event Grid témakörökhöz vagy tartományokhoz a közzétételi és kézbesítési hibák naplóinak rögzítéséhez és megtekintéséhez. További információ: [diagnosztikai naplók](enable-diagnostic-logs-topic.md).

## <a name="metrics"></a>Mérőszámok
Megtekintheti a Event Grid témaköröket és előfizetéseket, valamint riasztásokat hozhat létre rajtuk. További információ: [Event Grid mérőszámok](monitor-event-delivery.md).

## <a name="alerts"></a>Riasztások
Riasztások létrehozása Azure Event Grid mérőszámokon és tevékenység-naplózási műveletekben. További információkért tekintse [meg a riasztások Event Grid mérőszámokat és a tevékenységek naplóit](set-alerts.md).

## <a name="subscription-validation-issues"></a>Előfizetés-ellenőrzési problémák
Az esemény-előfizetés létrehozása során hibaüzenet jelenhet meg, amely szerint a megadott végpont érvényesítése sikertelen volt. Az előfizetés-ellenőrzési problémák elhárításával kapcsolatban lásd: [Event Grid előfizetés érvényességének](troubleshoot-subscription-validation.md)hibaelhárítása. 

## <a name="network-connectivity-issues"></a>Hálózati kapcsolattal kapcsolatos problémák
Számos oka lehet annak, hogy az ügyfélalkalmazások nem tudnak csatlakozni egy Event Grid témakörhöz/tartományhoz. Előfordulhat, hogy az Ön által tapasztalt kapcsolódási problémák állandóak vagy átmenetiek. A problémák megoldásával kapcsolatos további információkért lásd a [kapcsolódási problémák elhárítása](troubleshoot-network-connectivity.md)című témakört.

## <a name="error-codes"></a>Hibakódok
Ha a hibakódok (például 400, 409 és 403) hibaüzeneteket kapnak, tekintse meg a [Event Grid hibák elhárítása](troubleshoot-errors.md)című témakört. 

## <a name="distributed-tracing-net"></a>Elosztott nyomkövetés (.NET)
A Event Grid .NET-függvénytár támogatja a nyomkövetés terjesztését. Ahhoz, hogy a [CloudEvents-specifikáció útmutatást](https://github.com/cloudevents/spec/blob/master/extensions/distributed-tracing.md) biztosítson a nyomkövetés terjesztéséhez, a függvénytár beállítja a `traceparent` és a `tracestate` [ExtensionAttributes](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventgrid/Azure.Messaging.EventGrid/src/Customization#L126) , `CloudEvent` Ha az elosztott nyomkövetés engedélyezve van. Ha többet szeretne megtudni arról, hogyan engedélyezheti az elosztott nyomkövetést az alkalmazásban, tekintse meg az Azure SDK [elosztott nyomkövetési dokumentációját](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Azure.Core/samples/Diagnostics.md#Distributed-tracing).

### <a name="sample"></a>Sample
Lásd a [vonal számláló mintát](/samples/azure/azure-sdk-for-net/line-counter/). Ez a minta alkalmazás a Storage, a Event Hubs és az Event Grid ügyfeleket mutatja be ASP.NET Core integráció, elosztott nyomkövetés és üzemeltetett szolgáltatások használatával együtt. Lehetővé teszi a felhasználók számára, hogy fájlokat töltsenek fel egy blobba, amely a fájlnevet tartalmazó Event Hubs eseményt indít el. A Event Hubs processzor fogadja az eseményt, majd az alkalmazás letölti a blobot, és megszámolja a fájlban lévő sorok számát. Az alkalmazás megjeleníti a sorok darabszámát tartalmazó oldalra mutató hivatkozást. Ha a hivatkozásra kattint, a rendszer a Event Grid használatával teszi közzé a fájl nevét tartalmazó CloudEvent.

## <a name="next-steps"></a>Következő lépések
Ha további segítségre van szüksége, tegye fel a problémát a [stack overflow fórumba](https://stackoverflow.com/questions/tagged/azure-eventgrid) , vagy nyisson meg egy [támogatási jegyet](https://azure.microsoft.com/support/options/). 
