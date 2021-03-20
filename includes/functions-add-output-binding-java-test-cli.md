---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 6a359cdd44cc0c0cfbd93bd23b69a67a641c7fbb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "80673295"
---
## <a name="update-the-tests"></a>A tesztek frissítése

Mivel az archetípus is létrehoz egy tesztet, frissítenie kell ezeket a teszteket az új paraméter a `msg` `run` metódus aláírásában való kezeléséhez.  

Keresse meg a kód helyét az _src/test/Java_ területen, nyissa meg a *function. Java* projektfájlt, és cserélje le a kód sorát a `//Invoke` következő kóddal.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/test/java/com/function/FunctionTest.java" range="48-50":::