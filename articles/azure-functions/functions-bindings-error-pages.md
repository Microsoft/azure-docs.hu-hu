---
title: Azure Functions hibakezelés és újrapróbálkozási útmutató
description: Megtudhatja, hogyan kezelheti a hibákat, és hogyan próbálkozhat újra az Azure Functions az adott kötési hibákra mutató hivatkozásokkal.
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/01/2020
ms.author: cshoe
ms.openlocfilehash: e52babd5b0612b4590f1422a941b25ba30c3f169
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93284454"
---
# <a name="azure-functions-error-handling-and-retries"></a>Azure Functions a hibakezelés és az újrapróbálkozások

A Azure Functions hibák elhárítása fontos az elveszett adat, a kihagyott események és az alkalmazás állapotának figyelése érdekében.

Ez a cikk a hibák kezelésére vonatkozó általános stratégiákat, valamint a kötésekre vonatkozó hibákra mutató hivatkozásokat ismerteti.

## <a name="handling-errors"></a>Hibák kezelése

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-retries.md)]

## <a name="binding-error-codes"></a>Kötési hibakódok

Az Azure-szolgáltatásokkal való integráció során a hibák a mögöttes szolgáltatások API-jai alapján származhatnak. A kötési hibákkal kapcsolatos információk a következő cikkek **kivételek és visszatérési kódok** szakaszában érhetők el:

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Blob Storage](functions-bindings-storage-blob-output.md#exceptions-and-return-codes)

+ [Event Hubs](functions-bindings-event-hubs-output.md#exceptions-and-return-codes)

+ [IoT hubok](functions-bindings-event-iot-output.md#exceptions-and-return-codes)

+ [Notification Hubs](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Queue Storage](functions-bindings-storage-queue-output.md#exceptions-and-return-codes)

+ [Szolgáltatásbusz](functions-bindings-service-bus-output.md#exceptions-and-return-codes)

+ [Table Storage](functions-bindings-storage-table-output.md#exceptions-and-return-codes)
