---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/18/2020
ms.author: glenga
ms.openlocfilehash: 179ae760f146a5ac3041a54065ae12147f3f9bf0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97739814"
---
Mivel az archetípus is létrehoz egy tesztet, frissítenie kell ezeket a teszteket az új paraméter a `msg` `run` metódus aláírásában való kezeléséhez.  

Keresse meg a kód helyét az _src/test/Java_ területen, nyissa meg a *function. Java* projektfájlt, és cserélje le a kód sorát a `//Invoke` következő kóddal.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/test/java/com/function/FunctionTest.java" range="48-50":::
