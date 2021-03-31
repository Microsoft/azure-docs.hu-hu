---
title: Az Azure IoT Hub-kötések Azure Functions
description: Megtudhatja, hogyan használhatja a IoT Hub triggert és a kötést Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 1c25543b16c3486a8f6a445427346382faaaa09a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "77586129"
---
# <a name="azure-iot-hub-bindings-for-azure-functions"></a>Az Azure IoT Hub-kötések Azure Functions

Ez a cikk azt ismerteti, hogyan használhatók a IoT Hubhoz Azure Functions kötések. Az IoT Hub támogatás az [Azure Event Hubs-kötésen](functions-bindings-event-hubs.md)alapul.

> [!IMPORTANT]
> Míg az alábbi mintakód-minták az Event hub API-t használják, a megadott szintaxis IoT Hub függvények esetében alkalmazható.

| Művelet | Típus |
|--------|------|
| Válaszadás az IoT hub Event streambe küldött eseményekre. | [Eseményindító](./functions-bindings-event-iot-trigger.md) |
| Események írása egy IoT-esemény streambe | [Kimeneti kötés](./functions-bindings-event-iot-output.md) |

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs.md)]

## <a name="next-steps"></a>Következő lépések

- [Válasz az Event hub esemény-adatfolyamba küldött eseményekre (trigger)](./functions-bindings-event-iot-trigger.md)
- [Események írása egy esemény-adatfolyamba (kimeneti kötés)](./functions-bindings-event-iot-output.md)
