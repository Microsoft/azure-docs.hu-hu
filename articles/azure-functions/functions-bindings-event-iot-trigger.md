---
title: Azure IoT Hub trigger a Azure Functionshoz
description: Megtudhatja, hogyan válaszolhat az IoT hub Event streambe Azure Functions küldött eseményekre.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 5c9309834b407ee56d29e38afd965ac947fc8a4f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102612286"
---
# <a name="azure-iot-hub-trigger-for-azure-functions"></a>Azure IoT Hub trigger a Azure Functionshoz

Ez a cikk azt ismerteti, hogyan használhatók a IoT Hub Azure Functions kötései. Az IoT Hub támogatás az [Azure Event Hubs-kötésen](functions-bindings-event-hubs.md)alapul.

További információ a telepítésről és a konfigurációról: [Áttekintés](functions-bindings-event-iot.md).

> [!IMPORTANT]
> Míg az alábbi mintakód-minták az Event hub API-t használják, a megadott szintaxis IoT Hub függvények esetében alkalmazható.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-trigger.md)]

## <a name="hostjson-properties"></a>Tulajdonságok host.js

A [host.js](functions-host-json.md#eventhub) fájl olyan beállításokat tartalmaz, amelyek az Event hub-eseményindító működését vezérlik. A rendelkezésre álló beállításokkal kapcsolatos részletekért tekintse [ meg ahost.json Settings](functions-bindings-event-iot.md#hostjson-settings) szakaszt.

## <a name="next-steps"></a>Következő lépések

- [Események írása egy esemény-adatfolyamba (kimeneti kötés)](./functions-bindings-event-iot-output.md)
