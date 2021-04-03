---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/11/2020
ms.author: glenga
ms.openlocfilehash: dab5f0f24fa1f36b611eb79329336832d8a4b3cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "78191039"
---
`msg`Hozzon létre egy olyan kódot, amely a kimeneti kötési objektumot használja az üzenetsor `context.bindings` létrehozásához. Adja hozzá ezt a kódot az `context.res` utasítás előtt.

:::code language="typescript" range="10" source="~/functions-docs-typescript/functions-add-output-binding-storage-queue-cli/HttpExample/index.ts":::

Ezen a ponton a függvénynek a következőképpen kell kinéznie:

:::code language="typescript" source="~/functions-docs-typescript/functions-add-output-binding-storage-queue-cli/HttpExample/index.ts":::