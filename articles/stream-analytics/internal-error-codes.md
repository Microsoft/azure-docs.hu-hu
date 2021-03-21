---
title: Azure Stream Analytics hibakódokkal kapcsolatos hibák
description: Belső hibakódokkal kapcsolatos Azure Stream Analytics hibák elhárítása.
ms.author: sidram
author: sidramadoss
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: ddea74997850ff080f6c82d55d1be3cfb851deda
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019380"
---
# <a name="azure-stream-analytics-internal-error-codes"></a>Belső hibakódok Azure Stream Analytics

A tevékenységek naplóit és erőforrás-naplóit használhatja a nem várt viselkedések hibakereséséhez a Azure Stream Analytics feladatból. Ez a cikk felsorolja az összes belső hibakód leírását. A belső hibák olyan általános hibák, amelyek a Stream Analytics platformon belül történnek, ha Stream Analytics nem tudja megkülönböztetni, ha a hiba belső rendelkezésre állási hiba vagy hiba a rendszerben.

## <a name="cosmosdboutputbatchsizetoolarge"></a>CosmosDBOutputBatchSizeTooLarge

* **OK**: a Cosmos db írásához használt köteg mérete túl nagy. 
* **Javaslat**: próbálkozzon újra kisebb batch-mérettel.

## <a name="next-steps"></a>Következő lépések

* [Bemeneti kapcsolatok hibaelhárítása](stream-analytics-troubleshoot-input.md)
* [Azure Stream Analytics kimenetek hibáinak megoldása](stream-analytics-troubleshoot-output.md)
* [Azure Stream Analytics lekérdezések hibáinak megoldása](stream-analytics-troubleshoot-query.md)
* [Azure Stream Analyticsek hibakeresése erőforrás-naplók használatával](stream-analytics-job-diagnostic-logs.md)
* [AdatAzure Stream Analyticsi hibák](data-errors.md)