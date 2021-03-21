---
title: Változó tevékenység hozzáfűzése Azure Data Factory
description: Megtudhatja, hogyan állíthatja be a változó hozzáfűzése tevékenységet úgy, hogy értéket adjon hozzá egy Data Factory folyamatban definiált meglévő tömb-változóhoz.
ms.service: data-factory
ms.topic: conceptual
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.date: 10/09/2018
ms.openlocfilehash: 5a9ed44e05c371460ae3ceab721f2236f6ec7fd6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100383414"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Változó tevékenység hozzáfűzése Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
A változó hozzáfűzése tevékenységgel hozzáadhat egy értéket egy Data Factory folyamatban definiált meglévő tömb változóhoz.

## <a name="type-properties"></a>Típus tulajdonságai

Tulajdonság | Leírás | Kötelező
-------- | ----------- | --------
name | A tevékenység neve a folyamatban | Yes
leírás | A tevékenység működését leíró szöveg | nem
típus | A tevékenység típusa AppendVariable | igen
érték | A megadott változóhoz való hozzáfűzéshez használt karakterlánc-literál vagy kifejezés objektum értéke | igen
variableName | Annak a változónak a neve, amely tevékenység alapján módosul, a változónak "Array" típusúnak kell lennie. | igen

## <a name="next-steps"></a>Következő lépések
A Data Factory által támogatott kapcsolódó vezérlési folyamatokkal kapcsolatos tevékenységek ismertetése: 

- [Változó tevékenység beállítása](control-flow-set-variable-activity.md)
