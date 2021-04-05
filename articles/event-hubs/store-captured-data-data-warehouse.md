---
title: 'Oktatóanyag: az események áttelepíthetők az Azure szinapszis Analyticsbe – Azure Event Hubs'
description: Ismerteti, hogyan lehet a Azure Event Grid és a functions használatával áttelepíteni a rögzített adatok Event Hubs az Azure szinapszis Analytics szolgáltatásba.
services: event-hubs
ms.date: 12/07/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: 77a2b35dea57c71e9e6f07056bd106df2ac109b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96854244"
---
# <a name="tutorial-migrate-captured-event-hubs-data-to-azure-synapse-analytics-using-event-grid-and-azure-functions"></a>Oktatóanyag: rögzített Event Hubs-adatszolgáltatások migrálása az Azure szinapszis Analyticsbe Event Grid és Azure Functions használatával
Az Azure Event Hubs [Capture](./event-hubs-capture-overview.md) lehetővé teszi, hogy az Azure Blob Storage-ban vagy a Azure Data Lake Storage-ban automatikusan rögzítse az adatfolyam-adatátvitelt Event Hubs. Ez az oktatóanyag bemutatja, hogyan telepítheti át a rögzített Event Hubsi adatait a Storage-ból az Azure szinapszis Analyticsbe egy [Event Grid](../event-grid/overview.md)által aktivált Azure-függvény használatával.

[!INCLUDE [event-grid-event-hubs-functions-synapse-analytics.md](../../includes/event-grid-event-hubs-functions-synapse-analytics.md)]

## <a name="next-steps"></a>Következő lépések 
Ha gyakorlatban is használható elemzésekre vágyik, használjon hatékony adatvizualizációs eszközöket az adattárházával.

Ez a cikk bemutatja, hogyan használható [a Power bi az Azure szinapszis Analytics](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect) használatával