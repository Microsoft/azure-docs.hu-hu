---
title: Az Azure Functions tesztelése
description: Automatizált tesztek létrehozása C#-függvényhez a Visual Studióban és a JavaScript-függvényben a VS Code-ban
author: craigshoemaker
ms.topic: conceptual
ms.custom: devx-track-csharp, devx-track-js
ms.date: 03/25/2019
ms.author: cshoe
ms.openlocfilehash: 27bc8171509e16f25c508dd71515d8cb25a11b0b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105639870"
---
# <a name="strategies-for-testing-your-code-in-azure-functions"></a>Kódtesztelési stratégiák az Azure Functions szolgáltatásban

Ez a cikk bemutatja, hogyan hozhatók létre automatizált tesztek a Azure Functionshoz.

Az összes kód tesztelése ajánlott, azonban a legjobb eredményeket a függvény logikájának becsomagolásával és a függvényen kívüli tesztek létrehozásával érheti el. Az absztrakt logika el korlátozza a függvények kódjait, és lehetővé teszi, hogy a függvény kizárólag az egyéb osztályok vagy modulok hívására legyen felelős. Ez a cikk azonban azt mutatja be, hogyan hozhatók létre automatizált tesztek a HTTP és az időzítő által aktivált függvények esetében.

Az alábbi tartalom két különböző szakaszra oszlik, amelyek célja különböző nyelvek és környezetek megcélzása. Megtudhatja, hogyan hozhat létre teszteket a alkalmazásban:

- [C# a Visual Studióban a xUnit](#c-in-visual-studio)
- [JavaScript a VS Code-ban a Jesttel](#javascript-in-vs-code)

A minta tárház a [githubon](https://github.com/Azure-Samples/azure-functions-tests)érhető el.

## <a name="c-in-visual-studio"></a>C# a Visual Studióban

Az alábbi példa azt ismerteti, hogyan hozhat létre C# Function alkalmazást a Visual Studióban, és hogyan futtathat és tesztelheti a [xUnit](https://github.com/xunit/xunit).

![Azure Functions tesztelése a C# használatával a Visual Studióban](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="setup"></a>Beállítás

A környezet beállításához hozzon létre egy Function és test alkalmazást. A következő lépések segítséget nyújtanak a tesztek támogatásához szükséges alkalmazások és függvények létrehozásában:

1. [Hozzon létre egy új functions-alkalmazást](./functions-get-started.md) , és nevezze el a **függvényeket**
2. [Hozzon létre egy http-függvényt a sablonból](./functions-get-started.md) , és nevezze el **MyHttpTrigger**.
3. [Hozzon létre egy időzítő függvényt a sablonból](./functions-create-scheduled-function.md) , és nevezze el **MyTimerTrigger**.
4. [Hozzon létre egy XUnit teszt alkalmazást](https://xunit.net/docs/getting-started/netcore/cmdline) a megoldásban, és nevezze el a **functions.** tests nevet.
5. A NuGet használata a tesztelési alkalmazásból a [Microsoft. AspNetCore. MVC](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc/) -re mutató hivatkozás hozzáadásához
6. [Hivatkozzon a *functions* alkalmazásra](/visualstudio/ide/managing-references-in-a-project) a *functions. tesztek* alkalmazásban.

### <a name="create-test-classes"></a>Tesztelési osztályok létrehozása

Most, hogy létrejöttek a projektek, létrehozhatók az automatizált tesztek futtatásához használt osztályok.

A függvények a [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) egy példányát veszik fel az üzenetek naplózásának kezelésére. Egyes tesztek vagy nem naplóznak üzeneteket, vagy nem érintik a naplózás megvalósításának módját. Más teszteknek ki kell értékelniük a naplózott üzeneteket annak megállapításához, hogy egy teszt átadásra kerül-e.

Létre fog hozni egy nevű új osztályt, `ListLogger` amely a tesztelés során kiértékelésre kerülő üzenetek belső listáját tartalmazza. A szükséges felület megvalósításához `ILogger` az osztálynak hatókörre van szüksége. A következő osztály kigúnyolja az osztálynak átadandó tesztelési esetek hatókörét `ListLogger` .

Hozzon létre egy új osztályt a *functions. tesztek* **NullScope. cs** nevű projektben, és adja meg a következő kódot:

```csharp
using System;

namespace Functions.Tests
{
    public class NullScope : IDisposable
    {
        public static NullScope Instance { get; } = new NullScope();

        private NullScope() { }

        public void Dispose() { }
    }
}
```

Ezután hozzon létre egy új osztályt a *functions. tesztek* **ListLogger. cs** nevű projektben, és adja meg a következő kódot:

```csharp
using Microsoft.Extensions.Logging;
using System;
using System.Collections.Generic;
using System.Text;

namespace Functions.Tests
{
    public class ListLogger : ILogger
    {
        public IList<string> Logs;

        public IDisposable BeginScope<TState>(TState state) => NullScope.Instance;

        public bool IsEnabled(LogLevel logLevel) => false;

        public ListLogger()
        {
            this.Logs = new List<string>();
        }

        public void Log<TState>(LogLevel logLevel,
                                EventId eventId,
                                TState state,
                                Exception exception,
                                Func<TState, Exception, string> formatter)
        {
            string message = formatter(state, exception);
            this.Logs.Add(message);
        }
    }
}
```

Az `ListLogger` osztály a következő tagokat valósítja meg, mint az `ILogger` illesztőfelület:

- **BeginScope**: a hatókörök kontextust adhatnak a naplózáshoz. Ebben az esetben a teszt csak a osztály statikus példányára mutat, `NullScope` hogy lehetővé tegye a teszt működését.

- **IsEnabled**: az alapértelmezett érték `false` van megadva.

- **Napló**: Ez a metódus a megadott `formatter` függvényt használja az üzenet formázásához, majd hozzáadja az eredményül kapott szöveget a `Logs` gyűjteményhez.

A `Logs` gyűjtemény egy példánya, amely a `List<string>` konstruktorban van inicializálva.

Ezután hozzon létre egy új fájlt a *functions. tesztek* **LoggerTypes. cs** nevű projektben, és adja meg a következő kódot:

```csharp
namespace Functions.Tests
{
    public enum LoggerTypes
    {
        Null,
        List
    }
}
```

Ez az enumerálás meghatározza a tesztek által használt naplózó típusát.

Most hozzon létre egy új osztályt a *functions. tesztek* **TestFactory. cs** nevű projektben, és adja meg a következő kódot:

```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Http.Internal;
using Microsoft.Extensions.Logging;
using Microsoft.Extensions.Logging.Abstractions;
using Microsoft.Extensions.Primitives;
using System.Collections.Generic;

namespace Functions.Tests
{
    public class TestFactory
    {
        public static IEnumerable<object[]> Data()
        {
            return new List<object[]>
            {
                new object[] { "name", "Bill" },
                new object[] { "name", "Paul" },
                new object[] { "name", "Steve" }

            };
        }

        private static Dictionary<string, StringValues> CreateDictionary(string key, string value)
        {
            var qs = new Dictionary<string, StringValues>
            {
                { key, value }
            };
            return qs;
        }

        public static HttpRequest CreateHttpRequest(string queryStringKey, string queryStringValue)
        {
            var context = new DefaultHttpContext();
            var request = context.Request;
            request.Query = new QueryCollection(CreateDictionary(queryStringKey, queryStringValue));
            return request;
        }

        public static ILogger CreateLogger(LoggerTypes type = LoggerTypes.Null)
        {
            ILogger logger;

            if (type == LoggerTypes.List)
            {
                logger = new ListLogger();
            }
            else
            {
                logger = NullLoggerFactory.Instance.CreateLogger("Null Logger");
            }

            return logger;
        }
    }
}
```

Az `TestFactory` osztály a következő tagokat valósítja meg:

- **Adatok**: Ez a tulajdonság a mintaadatok [IEnumerable](/dotnet/api/system.collections.ienumerable) gyűjteményét adja vissza. A kulcs érték párok a lekérdezési karakterláncba átadott értékeket jelölik.

- **CreateDictionary**: Ez a metódus fogadja a kulcs/érték párokat argumentumként, és visszaadja a `Dictionary` `QueryCollection` lekérdezési karakterlánc értékeit jelölő új létrehozáshoz használt újat.

- **CreateHttpRequest**: Ez a METÓDUS egy HTTP-kérést hoz létre, amely a megadott lekérdezési karakterlánc-paraméterekkel inicializálva van.

- **CreateLogger**: a naplózó típus alapján ez a metódus a teszteléshez használt naplózó osztályt adja vissza. A `ListLogger` nyomon követi a naplózott üzeneteket a tesztek kiértékeléséhez.

Végül hozzon létre egy új osztályt a *functions. tesztek* **FunctionsTests. cs** nevű projektben, és adja meg a következő kódot:

```csharp
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;
using Xunit;

namespace Functions.Tests
{
    public class FunctionsTests
    {
        private readonly ILogger logger = TestFactory.CreateLogger();

        [Fact]
        public async void Http_trigger_should_return_known_string()
        {
            var request = TestFactory.CreateHttpRequest("name", "Bill");
            var response = (OkObjectResult)await MyHttpTrigger.Run(request, logger);
            Assert.Equal("Hello, Bill. This HTTP triggered function executed successfully.", response.Value);
        }

        [Theory]
        [MemberData(nameof(TestFactory.Data), MemberType = typeof(TestFactory))]
        public async void Http_trigger_should_return_known_string_from_member_data(string queryStringKey, string queryStringValue)
        {
            var request = TestFactory.CreateHttpRequest(queryStringKey, queryStringValue);
            var response = (OkObjectResult)await MyHttpTrigger.Run(request, logger);
            Assert.Equal($"Hello, {queryStringValue}. This HTTP triggered function executed successfully.", response.Value);
        }

        [Fact]
        public void Timer_should_log_message()
        {
            var logger = (ListLogger)TestFactory.CreateLogger(LoggerTypes.List);
            MyTimerTrigger.Run(null, logger);
            var msg = logger.Logs[0];
            Assert.Contains("C# Timer trigger function executed at", msg);
        }
    }
}
```

Az ebben az osztályban megvalósított tagok a következők:

- **Http_trigger_should_return_known_string**: Ez a teszt egy http-függvény lekérdezési karakterlánc-értékeivel rendelkező kérelmet hoz létre, `name=Bill` és ellenőrzi, hogy a rendszer visszaadja-e a várt választ.

- **Http_trigger_should_return_string_from_member_data**: Ez a teszt xUnit-attribútumokat használ a http-függvényhez tartozó mintaadatok biztosításához.

- **Timer_should_log_message**: Ez a teszt létrehoz egy példányt, `ListLogger` és átadja egy időzítő függvénynek. A függvény futtatása után a rendszer ellenőrzi, hogy a napló be van-e jelölve, hogy a várt üzenet megtalálható legyen.

Ha a tesztek során szeretné elérni az alkalmazás beállításait, a függvénybe [beillesztheti egy](./functions-dotnet-dependency-injection.md) `IConfiguration` példány kigúnyolt környezeti változó értékeit.

### <a name="run-tests"></a>Tesztek futtatása

A tesztek futtatásához navigáljon a **tesztelési tallózóhoz** , és kattintson az **összes futtatása** parancsra.

![Azure Functions tesztelése a C# használatával a Visual Studióban](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="debug-tests"></a>Hibakeresési tesztek

A tesztek hibakereséséhez állítson be egy töréspontot egy teszten, navigáljon a **tesztelési tallózóhoz** , és kattintson a **Futtatás > hibakeresés utolsó futtatása** elemre.

## <a name="javascript-in-vs-code"></a>JavaScript a VS Code-ban

Az alábbi példa azt ismerteti, hogyan hozható létre JavaScript-függvény alkalmazás a VS Code-ban, és hogyan futtatható és tesztelhető a [Jest](https://jestjs.io)használatával. Ez az eljárás a [vs Code functions bővítményt](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) használja Azure functions létrehozásához.

![Azure Functions tesztelése a JavaScripttel a VS Code-ban](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="setup"></a>Beállítás

A környezet beállításához a futtatásával inicializáljon egy új Node.js alkalmazást egy üres mappában `npm init` .

```bash
npm init -y
```

Ezután telepítse a Jestet a következő parancs futtatásával:

```bash
npm i jest
```

Most frissítse _package.jsa-on_ a meglévő teszt parancs lecseréléséhez a következő paranccsal:

```bash
"scripts": {
    "test": "jest"
}
```

### <a name="create-test-modules"></a>Tesztelési modulok létrehozása

A projekt inicializálása után létrehozhatja az automatizált tesztek futtatásához használt modulokat. Először hozzon létre egy *tesztelés* nevű új mappát a támogatási modulok tárolásához.

A *tesztelési* mappában adjon hozzá egy új fájlt **defaultContext.js** nevet, és adja hozzá a következő kódot:

```javascript
module.exports = {
    log: jest.fn()
};
```

Ez a modul kigúnyolja a *log* függvényt, amely az alapértelmezett végrehajtási környezetet jelöli.

Ezután adjon hozzá egy új fájlt, nevezze el **defaultTimer.js**, majd adja hozzá a következő kódot:

```javascript
module.exports = {
    IsPastDue: false
};
```

Ez a modul implementálja a `IsPastDue` tulajdonságot, hogy az egy hamis időzítő példány legyen. Az időzítő konfigurációk, például a NCRONTAB-kifejezések nem szükségesek itt, mivel a teszt-hám egyszerűen hívja meg a függvényt közvetlenül az eredmény teszteléséhez.

Ezután a VS Code functions bővítmény használatával [hozzon létre egy új JavaScript http-függvényt](/azure/developer/javascript/tutorial-vscode-serverless-node-01) , és nevezze el *HttpTrigger*. A függvény létrehozása után adjon hozzá egy új fájlt a **index.test.js** nevű mappában, és adja hozzá a következő kódot:

```javascript
const httpFunction = require('./index');
const context = require('../testing/defaultContext')

test('Http trigger should return known text', async () => {

    const request = {
        query: { name: 'Bill' }
    };

    await httpFunction(context, request);

    expect(context.log.mock.calls.length).toBe(1);
    expect(context.res.body).toEqual('Hello Bill');
});
```

A sablonból a HTTP-függvény egy "Hello" karakterláncot ad vissza, amely a lekérdezési karakterláncban megadott névvel van összefűzve. Ez a teszt egy kérelem hamis példányát hozza létre, és átadja a HTTP-függvénynek. A teszt ellenőrzi, hogy a rendszer egyszer hívja-e a *log* metódust, és a visszaadott szöveg "Hello Bill".

Ezután a VS Code functions bővítmény használatával hozzon létre egy új JavaScript-időzítő függvényt, és nevezze el *TimerTrigger*. A függvény létrehozása után adjon hozzá egy új fájlt a **index.test.js** nevű mappában, és adja hozzá a következő kódot:

```javascript
const timerFunction = require('./index');
const context = require('../testing/defaultContext');
const timer = require('../testing/defaultTimer');

test('Timer trigger should log message', () => {
    timerFunction(context, timer);
    expect(context.log.mock.calls.length).toBe(1);
});
```

A sablon időzítő funkciója naplóz egy üzenetet a függvény törzsének végén. Ez a teszt azt biztosítja, hogy a *log* függvényt egyszer kell meghívni.

### <a name="run-tests"></a>Tesztek futtatása

A tesztek futtatásához nyomja le a **CTRL + ~** billentyűkombinációt a parancssori ablak megnyitásához, majd futtassa a következő parancsot `npm test` :

```bash
npm test
```

![Azure Functions tesztelése a JavaScripttel a VS Code-ban](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="debug-tests"></a>Hibakeresési tesztek

A tesztek hibakereséséhez adja hozzá a következő konfigurációt a *launch.js* fájlhoz:

```json
{
  "type": "node",
  "request": "launch",
  "name": "Jest Tests",
  "disableOptimisticBPs": true,
  "program": "${workspaceRoot}/node_modules/jest/bin/jest.js",
  "args": [
      "-i"
  ],
  "internalConsoleOptions": "openOnSessionStart"
}
```

Ezután állítson be egy töréspontot a tesztben, és nyomja le az **F5** billentyűt.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan írhat automatizált teszteket a függvények számára, folytassa ezeket az erőforrásokat:

- [Nem HTTP által aktivált függvény manuális futtatása](./functions-manually-run-non-http.md)
- [Azure Functions hibakezelés](./functions-bindings-error-pages.md)
- [Az Azure Function Event Grid helyi hibakeresést indít](./functions-debug-event-grid-trigger-local.md)