---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 8bdd8b9d900cc50fdeb34ff7d233ac4d7e17a45c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "78191037"
---
Frissítse *az \\ \_ \_ init \_ \_ . HttpExample* -t, hogy az megfeleljen a következő kódnak, adja hozzá a `msg` paramétert a függvény definíciójában és `msg.set(name)` az `if name:` utasításban.

:::code language="python" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

A paraméter a (z `msg` ) példánya [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest) . A `set` metódus egy karakterlánc-üzenetet ír a várólistába, ebben az esetben az URL-lekérdezési karakterláncban szereplő függvénynek átadott nevet.
