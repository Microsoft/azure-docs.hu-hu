---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/18/2020
ms.author: glenga
ms.openlocfilehash: c27f3fea6d2dd8b891220ba06b375b33e6cd950b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "80673474"
---
Egy Java-projektben a kötések kötési megjegyzésekként vannak definiálva a Function metódusban. A fájl *function.js* ezután automatikusan létrejön a jegyzetek alapján.

Keresse meg a függvény kódját a _src/Main/Java_ területen, nyissa meg a *function. Java* projektfájlt, és adja hozzá a következő paramétert a `run` metódus-definícióhoz:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="20-21":::

A `msg` paraméter egy [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) típus, amely a függvény befejeződése után üzenetként írt karakterláncok gyűjteményét jelöli. Ebben az esetben a kimenet egy nevű tárolási várólista `outqueue` . A Storage-fiókhoz tartozó kapcsolatok karakterláncát a metódus állítja be `connection` . A felhasználói karakterlánc helyett adja meg a Storage-fiók kapcsolatok sztringjét tartalmazó alkalmazásbeállítás értékét.

A `run` metódus definíciójának ekkor az alábbi példához hasonlóan kell kinéznie:  

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="16-22":::