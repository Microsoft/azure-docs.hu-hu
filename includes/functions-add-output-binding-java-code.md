---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 3705f58a37c109ebe0b774603c60e246fc174f25
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "80673251"
---
Most az új `msg` paraméter használatával írhatja a függvény kódjából a kimeneti kötést. Adja hozzá a következő kódrészletet a sikeres válaszként, hogy hozzáadja az értékét `name` a `msg` kimeneti kötéshez.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="34":::

Ha kimeneti kötést használ, nem kell használnia az Azure Storage SDK kódját a hitelesítéshez, a várólista-hivatkozás beszerzéséhez vagy az adatíráshoz. A functions futtatókörnyezet és a várólista kimeneti kötése elvégzi ezeket a feladatokat.

A `run` metódusnak most az alábbi példához hasonlóan kell kinéznie:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="17-38":::