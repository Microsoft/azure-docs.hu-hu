---
title: erőforrás () kifejezés Azure Monitor napló lekérdezésében | Microsoft Docs
description: Az erőforrás-kifejezés egy erőforrás-központú Azure Monitor-naplózási lekérdezésben használható több erőforrásból származó adatok lekérdezéséhez.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2018
ms.openlocfilehash: 2a729caefe698b13833098ba48df9d4bfbd97356
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100619518"
---
# <a name="resource-expression-in-azure-monitor-log-query"></a>erőforrás () kifejezés Azure Monitor napló lekérdezésében

A `resource` kifejezés egy olyan Azure monitor lekérdezésben van használatban, amely [egy erőforrásra](scope.md#query-scope) vonatkozik, és más erőforrásokból kéri le az adatok lekérését. 


## <a name="syntax"></a>Syntax

`resource(`*Azonosító*`)`

## <a name="arguments"></a>Argumentumok

- *Azonosító*: erőforrás erőforrás-azonosítója.

| Azonosító | Leírás | Példa
|:---|:---|:---|
| Erőforrás | Az erőforráshoz tartozó adatforrásokat tartalmazza. | erőforrás ("/Subscriptions/xxxxxxx-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourcesgroups/myresourcegroup/Providers/Microsoft.Compute/virtualmachines/myvm") |
| Erőforráscsoport vagy előfizetés | Az erőforráshoz és a benne található összes erőforráshoz tartozó adattartalom.  | erőforrás ("/Subscriptions/xxxxxxx-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourcesgroups/myresourcegroup") |


## <a name="notes"></a>Jegyzetek

* Olvasási hozzáféréssel kell rendelkeznie az erőforráshoz.


## <a name="examples"></a>Példák

```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/providers/microsoft.compute/virtualmachines/myvm").Heartbeat) | summarize count() by _ResourceId, TenantId
```
```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup).Heartbeat) | summarize count() by _ResourceId, TenantId
```


## <a name="next-steps"></a>Következő lépések

- A lekérdezési hatókör részleteiért tekintse meg a [naplózási lekérdezés hatókörét és időtartományát Azure Monitor log Analytics](scope.md) .
- A [Kusto lekérdezési nyelv](/azure/kusto/query/)teljes dokumentációjának elérése.
