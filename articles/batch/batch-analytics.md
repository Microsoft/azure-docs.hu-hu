---
title: Azure Batch Analitika
description: A Batch Analytics témakörei a Batch szolgáltatás erőforrásaihoz elérhető eseményekre és riasztásokra vonatkozó információkat tartalmaznak.
ms.topic: reference
ms.date: 10/08/2020
ms.openlocfilehash: 0d55ecd7f10e267a9cb469dffcdf26c131c8ce41
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91849511"
---
# <a name="batch-analytics"></a>Batch-elemzés

A szakasz témakörei a Batch szolgáltatás erőforrásaihoz elérhető eseményekre és riasztásokra vonatkozó információkat tartalmaznak.

A Batch diagnosztikai naplók engedélyezésével és elfogyasztásával kapcsolatos további információkért tekintse meg a [Azure batch diagnosztikai naplózást](./batch-diagnostics.md) ismertető témakört.

## <a name="diagnostic-logs"></a>Diagnosztikai naplók

A Azure Batch szolgáltatás a következő diagnosztikai naplózási eseményeket bocsátja ki bizonyos batch-erőforrások élettartama alatt.

### <a name="service-log-events"></a>Szolgáltatás-naplózási események

- [Készlet létrehozása](batch-pool-create-event.md)
- [Készlet törlésének kezdete](batch-pool-delete-start-event.md)
- [Készlet törlése kész](batch-pool-delete-complete-event.md)
- [Készlet átméretezésének kezdete](batch-pool-resize-start-event.md)
- [A készlet átméretezése befejeződött](batch-pool-resize-complete-event.md)
- [Készlet-méretezés](batch-pool-autoscale-event.md)
- [Feladat indítása](batch-task-start-event.md)
- [A feladat befejeződött](batch-task-complete-event.md)
- [Sikertelen feladat](batch-task-fail-event.md)
- [Sikertelen feladat-ütemterv](batch-task-schedule-fail-event.md)
