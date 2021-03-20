---
title: Azure Cosmos DB ütemezett méretezése Azure Functions időzítő használatával
description: Megtudhatja, hogyan méretezheti a Azure Cosmos DB átviteli sebességének változásait a PowerShell és a Azure Functions használatával.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 01/13/2020
ms.author: mjbrown
ms.openlocfilehash: c60f3fc6b4ce4a1aead273fedb81e39de697f576
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93339257"
---
# <a name="scale-azure-cosmos-db-throughput-by-using-azure-functions-timer-trigger"></a>Azure Cosmos DB átviteli sebesség méretezése Azure Functions időzítő-trigger használatával
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Az Azure Cosmos-fiók teljesítménye a kérelmek egységenkénti egységében (RU/s) kifejezett kiépített átviteli sebességen alapul. A kiépítés második részletességgel történik, és a legmagasabb RU/s óradíj alapján számítjuk fel a díjat. Ez a kiépített kapacitási modell lehetővé teszi, hogy a szolgáltatás kiszámítható és konzisztens teljesítményt, garantált kis késést és magas rendelkezésre állást biztosítson. A legtöbb éles számítási feladat ezeket a szolgáltatásokat tartalmazza. A fejlesztési és tesztelési környezetekben azonban, ahol a Azure Cosmos DB csak a munkaidőn belül használatos, az átviteli sebességet reggel, a munkaidő után pedig az esti leskálázást is igénybe veheti.

Az átviteli sebességet [Azure Resource Manager sablonokon](./templates-samples-sql.md), az [Azure CLI](cli-samples.md)-n, a [powershellen](powershell-samples.md), a Core (SQL) API-fiókokon, illetve a nyelvspecifikus Azure Cosmos db SDK-k használatával állíthatja be. A Resource Manager-sablonok, az Azure CLI vagy a PowerShell használatának előnye, hogy támogatják az összes Azure Cosmos DB Model API-t.

## <a name="throughput-scheduler-sample-project"></a>Átviteli sebesség ütemező minta projekt

A Azure Cosmos DB ütemezésének egyszerűsítése érdekében az [Azure Cosmos átviteli ütemező](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler)nevű minta projektet hoztunk létre. Ez a projekt egy Azure Functions alkalmazás két időzítő eseményindítóval – "ScaleUpTrigger" és "ScaleDownTrigger". Az eseményindítók egy PowerShell-parancsfájlt futtatnak, amely minden egyes Eseményindítóban meghatározza az adott erőforrás átviteli sebességét `resources.json` . Az ScaleUpTrigger úgy van konfigurálva, hogy 8 ÓRAKOR UTC-kor fusson, és a ScaleDownTrigger 6 ÓRAKOR UTC-re van konfigurálva, és ezek az időpontok könnyen frissíthetők a `function.json` fájlon belül az egyes triggerekhez.

Ezt a projektet helyileg is elvégezheti, ha módosítani szeretné a felskálázásra és a lefelé irányuló Azure Cosmos DB erőforrásokat, valamint az ütemezett futtatást. Később üzembe helyezheti azt egy Azure-előfizetésben, és felügyelt szolgáltatás-identitással biztonságossá teheti az Azure [szerepköralapú hozzáférés-vezérlés (Azure RBAC)](role-based-access-control.md) engedélyekkel az "Azure Cosmos db operátor" szerepkörrel az átviteli sebesség az Azure Cosmos-fiókokon való beállításához.

## <a name="next-steps"></a>Következő lépések

- Tudjon meg többet, és töltse le a mintát [Azure Cosmos db adatátviteli ütemezésből](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler).