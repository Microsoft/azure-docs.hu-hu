---
title: Azure Functions bemelegedési trigger
description: Megtudhatja, hogyan használhatja a bemelegedési triggert Azure Functionsban.
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: Azure functions, functions, Event Processing, bemelegedési, Cold Start, Premium, dinamikus számítás, kiszolgáló nélküli architektúra
ms.service: azure-functions
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 11/08/2019
ms.author: cshoe
ms.openlocfilehash: ea418576ab8fe06964a61e48f16393e1a0566ce8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102182247"
---
# <a name="azure-functions-warm-up-trigger"></a>Azure Functions bemelegítő trigger

Ez a cikk azt ismerteti, hogyan használható a bemelegedési trigger a Azure Functionsban. A rendszer meghívja a bemelegedési eseményindítót, amikor egy példány bekerül egy futó Function-alkalmazás méretezésére. A bemelegedési művelettel előre betöltheti az egyéni függőségeket az [előmelegítési folyamat](./functions-premium-plan.md#pre-warmed-instances) során, így a függvények azonnal elkezdhetik a kérelmek feldolgozását. 

> [!NOTE]
> A bemelegedési trigger nem támogatott a használati tervekben futó Function apps esetén.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x-and-higher"></a>Csomagok – 2. x és újabb függvények

A [Microsoft. Azure. webjobs. Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet-csomag **3.0.5 vagy újabb** verziója szükséges. A csomag forráskódja az [Azure-webjobs-SDK-Extensions GitHub-](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/main/src/WebJobs.Extensions/Extensions/Warmup) tárházban található. 

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>Eseményindító

A bemelegedési trigger lehetővé teszi, hogy Definiáljon egy függvényt, amely egy új példányon fog futni, amikor bekerül a futó alkalmazásba. A bemelegedési függvény használatával megnyithatja a kapcsolatokat, betöltheti a függőségeket, vagy bármilyen más egyéni logikát futtathat, mielőtt az alkalmazás megkapja a forgalmat. 

A bemelegedési trigger olyan megosztott függőségek létrehozására szolgál, amelyeket az alkalmazás egyéb funkciói fognak használni. [Tekintse meg a megosztott függőségek példáit](./manage-connections.md#client-code-examples).

Vegye figyelembe, hogy a bemelegedési trigger csak a kibővített műveletekben, az újraindítások vagy más nem léptékű indítások során hívható meg. A bemelegedési trigger használata nélkül gondoskodnia kell arról, hogy a logikája az összes szükséges függőséget be tudja tölteni. A lusta betöltés jó példa ennek megvalósítására.

## <a name="trigger---example"></a>Trigger – példa

# <a name="c"></a>[C#](#tab/csharp)

Az alábbi példa egy [C#-függvényt](functions-dotnet-class-library.md) mutat be, amely minden egyes új példányon fut, amikor hozzáadják az alkalmazáshoz. Nincs szükség visszatérési érték attribútumra.


* A függvénynek névvel kell rendelkeznie ```warmup``` (kis-és nagybetűk megkülönböztetése), és egy alkalmazás csak egy bemelegedési függvényt tartalmazhat.
* Ha a bemelegedési .NET Class Library-függvényt szeretné használni, győződjön meg arról, hogy a csomagra hivatkozik a **Microsoft. Azure. webjobs. extensions >= 3.0.5**
    * ```<PackageReference Include="Microsoft.Azure.WebJobs.Extensions" Version="3.0.5" />```


A helyőrző megjegyzések megmutatják, hogy az alkalmazás hol deklarálja és inicializálja a megosztott függőségeket. 
[További információk a megosztott függőségekről itt](./manage-connections.md#client-code-examples).

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
 
namespace WarmupSample
{

    //Declare shared dependencies here

    public static class Warmup
    {
        [FunctionName("Warmup")]
        public static void Run([WarmupTrigger()] WarmupContext context,
            ILogger log)
        {
            //Initialize shared dependencies here
            
            log.LogInformation("Function App instance is warm 🌞🌞🌞");
        }
    }
}
```
# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)


Az alábbi példa egy bemelegedési triggert mutat be egy *function.jsa* fájlban, és egy [C# parancsfájl-függvényt](functions-reference-csharp.md) , amely minden egyes új példányon fut, amikor hozzáadják az alkalmazáshoz.

A függvénynek neve ```warmup``` (kis-és nagybetűk megkülönböztetése) kell, és az alkalmazásban csak egy bemelegedési függvény lehet.

A fájl *function.js* :

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

A [konfigurációs](#trigger---configuration) szakasz ezeket a tulajdonságokat ismerteti.

```cs
public static void Run(WarmupContext warmupContext, ILogger log)
{
    log.LogInformation("Function App instance is warm 🌞🌞🌞");  
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Az alábbi példa egy bemelegedési triggert mutat be egy *function.jsa* fájlon, és egy [JavaScript-függvényt](functions-reference-node.md)  , amely minden új példányon futni fog, amikor az alkalmazáshoz kerül.

A függvénynek névvel kell rendelkeznie ```warmup``` (kis-és nagybetűk megkülönböztetése), és egy alkalmazás csak egy bemelegedési függvényt tartalmazhat.

A fájl *function.js* :

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

A [konfigurációs](#trigger---configuration) szakasz ezeket a tulajdonságokat ismerteti.

Itt látható a JavaScript-kód:

```javascript
module.exports = async function (context, warmupContext) {
    context.log('Function App instance is warm 🌞🌞🌞');
};
```

# <a name="python"></a>[Python](#tab/python)

Az alábbi példa egy bemelegedési triggert mutat be egy *function.jsa* fájlban, és egy [Python-függvényt](functions-reference-python.md) , amely minden új példányon fut, amikor az alkalmazáshoz kerül.

A függvénynek névvel kell rendelkeznie ```warmup``` (kis-és nagybetűk megkülönböztetése), és egy alkalmazás csak egy bemelegedési függvényt tartalmazhat.

A fájl *function.js* :

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

A [konfigurációs](#trigger---configuration) szakasz ezeket a tulajdonságokat ismerteti.

Itt látható a Python-kód:

```python
import logging
import azure.functions as func


def main(warmupContext: func.Context) -> None:
    logging.info('Function App instance is warm 🌞🌞🌞')
```

# <a name="java"></a>[Java](#tab/java)

Az alábbi példa egy bemelegedési triggert mutat be, amely akkor fut le, amikor az egyes új példányok bekerülnek az alkalmazásba.

A függvénynek névvel kell rendelkeznie `warmup` (kis-és nagybetűk megkülönböztetése), és egy alkalmazás csak egy bemelegedési függvényt tartalmazhat.

```java
@FunctionName("Warmup")
public void run( ExecutionContext context) {
       context.getLogger().info("Function App instance is warm 🌞🌞🌞");
}
```

---

## <a name="trigger---attributes"></a>Trigger – attribútumok

A [C# osztályok könyvtáraiban](functions-dotnet-class-library.md)az `WarmupTrigger` attribútum a függvény konfigurálására használható.

# <a name="c"></a>[C#](#tab/csharp)

Ez a példa a [bemelegedési](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions/Extensions/Warmup/Trigger/WarmupTriggerAttribute.cs) attribútum használatát mutatja be.

Vegye figyelembe, hogy a függvényt hívni kell ```Warmup``` , és egy alkalmazásban csak egy bemelegedési függvény lehet.

```csharp
 [FunctionName("Warmup")]
        public static void Run(
            [WarmupTrigger()] WarmupContext context, ILogger log)
        {
            ...
        }
```

Teljes példaként tekintse meg az [trigger példáját](#trigger---example).

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

A C# parancsfájl nem támogatja az attribútumokat.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

A Python nem támogatja az attribútumokat.

# <a name="java"></a>[Java](#tab/java)

A bemelegedési trigger nem támogatott a Javaban attribútumként.

---

## <a name="trigger---configuration"></a>Trigger – konfiguráció

Az alábbi táblázat a fájl és attribútum *function.jsjában* beállított kötési konfigurációs tulajdonságokat ismerteti `WarmupTrigger` .

|function.jsa tulajdonságon | Attribútum tulajdonsága |Description|
|---------|---------|----------------------|
| **típusa** | n.a.| Kötelező – a következőre kell beállítani: `warmupTrigger` . |
| **irányba** | n.a.| Kötelező – a következőre kell beállítani: `in` . |
| **név** | n.a.| Kötelező – a függvény kódjában használt változó neve.|

## <a name="trigger---usage"></a>Trigger – használat

A meghívásakor a bemelegedési által aktivált függvények nem kapnak további információt.

## <a name="trigger---limits"></a>Trigger – korlátok

* A bemelegedési trigger csak a [prémium csomagon](./functions-premium-plan.md)futó alkalmazások számára érhető el.
* A bemelegedési trigger csak a kibővített műveletekben, az újraindítások vagy más nem léptékű indítások során hívható meg. A bemelegedési trigger használata nélkül gondoskodnia kell arról, hogy a logikája az összes szükséges függőséget be tudja tölteni. A lusta betöltés jó példa ennek megvalósítására.
* A bemelegedési eseményindító nem hívható meg, ha egy példány már fut.
* Function App-alkalmazásban csak egy bemelegedési trigger függvény adható meg.

## <a name="next-steps"></a>Következő lépések

[További információ az Azure functions-eseményindítók és-kötésekről](functions-triggers-bindings.md)
