---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 2b2c043e70aac14c7fc6f0b58aae257624b05d13
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "80673256"
---
Egy Java-projektben a kötések kötési megjegyzésekként vannak definiálva a Function metódusban. A fájl *function.js* ezután automatikusan létrejön a jegyzetek alapján.

Keresse meg a függvény kódját a _src/Main/Java_ területen, nyissa meg a *function. Java* projektfájlt, és adja hozzá a következő paramétert a `run` metódus-definícióhoz:

```java
@QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") OutputBinding<String> msg
```

A `msg` paraméter egy [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) típus, amely a függvény befejeződése után üzenetként írt karakterláncok gyűjteményét jelöli. Ebben az esetben a kimenet egy nevű tárolási várólista `outqueue` . A Storage-fiókhoz tartozó kapcsolatok karakterláncát a metódus állítja be `connection` . A felhasználói karakterlánc helyett adja meg a Storage-fiók kapcsolatok sztringjét tartalmazó alkalmazásbeállítás értékét.

A `run` metódus definíciójának ekkor az alábbi példához hasonlóan kell kinéznie:  

```java
@FunctionName("HttpTrigger-Java")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION)  
        HttpRequestMessage<Optional<String>> request, 
        @QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") 
        OutputBinding<String> msg, final ExecutionContext context) {
    ...
}
```