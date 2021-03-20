---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/05/2019
ms.author: glenga
ms.openlocfilehash: 5e1a2622df0038141dd5cb05237f93d5e33e0bfb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "78190915"
---
A C# Class Library-projektekben a kötések kötési attribútumokként vannak definiálva a Function metódusban. A függvények által igényelt fájl *function.js* automatikusan létrejön ezen attribútumok alapján.

Nyissa meg a *HttpExample. cs* projektfájlt, és adja hozzá a következő paramétert a `Run` metódus-definícióhoz:

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="17":::

A `msg` paraméter egy `ICollector<T>` típus, amely a függvény befejeződése után kimeneti kötésbe írt üzenetek gyűjteményét jelöli. Ebben az esetben a kimenet egy nevű tárolási várólista `outqueue` . A Storage-fiókhoz tartozó kapcsolatok karakterláncát a következő értékre állítja be: `StorageAccountAttribute` . Ez az attribútum azt a beállítást jelzi, amely a Storage-fiókhoz tartozó kapcsolatok sztringjét tartalmazza, és alkalmazható az osztály, a metódus vagy a paraméter szintjén. Ebben az esetben kihagyhatja, `StorageAccountAttribute` mert már használja az alapértelmezett Storage-fiókot.

A futtatási metódus definíciójának ekkor a következőhöz hasonlóan kell kinéznie:  

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="14-18":::
