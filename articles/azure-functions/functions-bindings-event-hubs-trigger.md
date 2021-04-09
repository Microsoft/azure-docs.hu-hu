---
title: Azure Event Hubs trigger a Azure Functionshoz
description: Ismerje meg, hogyan használhatja az Azure Event Hubs triggert a Azure Functionsban.
author: craigshoemaker
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: c2b8302e64f7dcc657fd20ed5d918ed6816d750d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102608911"
---
# <a name="azure-event-hubs-trigger-for-azure-functions"></a>Azure Event Hubs trigger a Azure Functionshoz

Ez a cikk azt ismerteti, hogyan használható az [Azure Event Hubs](../event-hubs/event-hubs-about.md) trigger a Azure Functionshoz. Azure Functions támogatja az aktiválási és [kimeneti kötéseket](functions-bindings-event-hubs-output.md) a Event Hubshoz.

További információ a telepítésről és a konfigurációról: [Áttekintés](functions-bindings-event-hubs.md).

[!INCLUDE [functions-bindings-event-hubs-trigger](../../includes/functions-bindings-event-hubs-trigger.md)]

## <a name="hostjson-settings"></a>Beállítások host.js

A [host.js](functions-host-json.md#eventhub) fájl olyan beállításokat tartalmaz, amelyek az Event hub-eseményindító működését vezérlik. A rendelkezésre álló beállításokkal kapcsolatos részletekért tekintse [ meg ahost.json Settings](functions-bindings-event-hubs.md#hostjson-settings) szakaszt.

## <a name="next-steps"></a>Következő lépések

- [Események írása egy esemény-adatfolyamba (kimeneti kötés)](./functions-bindings-event-hubs-output.md)
