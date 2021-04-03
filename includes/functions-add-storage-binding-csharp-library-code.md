---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/05/2019
ms.author: glenga
ms.openlocfilehash: be3c746146012195757ab06de0c424dbc8297e9a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "78190916"
---
Adjon hozzá egy olyan kódot, amely a `msg` kimeneti kötési objektumot használja üzenetsor-üzenet létrehozásához. Adja hozzá ezt a kódot a metódus visszaadása előtt.

:::code language="csharp" range="28-32" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" :::

Ezen a ponton a függvénynek a következőképpen kell kinéznie:

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="14-36":::
