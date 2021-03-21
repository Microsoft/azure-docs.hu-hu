---
title: Azure Durable Functions-egység tesztelése
description: Ismerje meg, hogyan lehet a test Durable Functions.
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: 89b6419e95b3971b0d272490e19354f300204e1e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103491044"
---
# <a name="durable-functions-unit-testing"></a>Durable Functions egység tesztelése

Az egység tesztelése a modern szoftverfejlesztési eljárások fontos részét képezi. Az egység-tesztek ellenőrzik az üzleti logikát és a védelemtől való megfelelést, és a jövőben nem figyelt feltörési változásokat jelentenek. A Durable Functions könnyedén növelheti a bonyolultságot, így az egységek tesztelésének bevezetésével elkerülhető a változások megszakítása. A következő szakaszokban bemutatjuk, hogyan kell tesztelni a három függvényt – a-összehangoló ügyfelet, a Orchestrator és a tevékenységi funkciókat.

> [!NOTE]
> Ez a cikk útmutatást nyújt a Durable Functions alkalmazásokhoz Durable Functions 2. x. A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

## <a name="prerequisites"></a>Előfeltételek

A cikkben szereplő példák a következő fogalmakat és keretrendszerek ismeretét igénylik:

* Egységtesztelés

* Tartós függvények

* [xUnit](https://github.com/xunit/xunit) – tesztelési keretrendszer

* [MOQ](https://github.com/moq/moq4) -modellezési keretrendszer

## <a name="base-classes-for-mocking"></a>A modellezés alaposztályai

A kigúnyolás a következő felületen keresztül támogatott:

* [IDurableOrchestrationClient](/dotnet/api/microsoft.azure.webjobs.IDurableOrchestrationClient), [IDurableEntityClient](/dotnet/api/microsoft.azure.webjobs.IDurableEntityClient) és [IDurableClient](/dotnet/api/microsoft.azure.webjobs.IDurableClient)

* [IDurableOrchestrationContext](/dotnet/api/microsoft.azure.webjobs.IDurableOrchestrationContext)

* [IDurableActivityContext](/dotnet/api/microsoft.azure.webjobs.IDurableActivityContext)
  
* [IDurableEntityContext](/dotnet/api/microsoft.azure.webjobs.IDurableEntityContext)

Ezek a felületek a Durable Functions által támogatott különböző triggerekkel és kötésekkel használhatók. A Azure Functions végrehajtásakor a functions Runtime a következő felületek konkrét megvalósításával fogja futtatni a függvény kódját. Az egység tesztelésekor a felületek kigúnyolt változatát átadhatja az üzleti logikájának teszteléséhez.

## <a name="unit-testing-trigger-functions"></a>Unit Testing trigger functions

Ebben a szakaszban az egység tesztelése ellenőrzi a következő HTTP-trigger függvény logikáját az új előkészítések elindításához.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

Az egység teszt feladata a `Retry-After` Válasz adattartalomban megadott fejléc értékének ellenőrzése. Így az egység tesztelése kigúnyol néhány `IDurableClient` módszert a kiszámítható működés biztosítása érdekében.

Először egy kigúnyoló keretrendszert (ebben az esetben[MOQ](https://github.com/moq/moq4) ) használunk a következő modellezéshez `IDurableClient` :

```csharp
// Mock IDurableClient
var durableClientMock = new Mock<IDurableClient>();
```

> [!NOTE]
> Habár a felületek kiépítéséhez közvetlenül az illesztőfelületet osztályként kell megvalósítani, a modellezési keretrendszerek különböző módokon egyszerűsítik a folyamatot. Ha például egy új metódust ad hozzá a csatolóhoz a kisebb kiadások között, a MOQ nem igénylik a kód módosításait a konkrét implementációkkal szemben.

Ezt követően a `StartNewAsync` rendszer kigúnyolja a metódust, hogy egy jól ismert példány azonosítóját adja vissza.

```csharp
// Mock StartNewAsync method
durableClientMock.
    Setup(x => x.StartNewAsync(functionName, It.IsAny<object>())).
    ReturnsAsync(instanceId);
```

A következő `CreateCheckStatusResponse` a kigúnyolva, hogy mindig üres HTTP 200 választ ad vissza.

```csharp
// Mock CreateCheckStatusResponse method
durableClientMock
    // Notice that even though the HttpStart function does not call IDurableClient.CreateCheckStatusResponse() 
    // with the optional parameter returnInternalServerErrorOnFailure, moq requires the method to be set up
    // with each of the optional parameters provided. Simply use It.IsAny<> for each optional parameter
    .Setup(x => x.CreateCheckStatusResponse(It.IsAny<HttpRequestMessage>(), instanceId, returnInternalServerErrorOnFailure: It.IsAny<bool>())
    .Returns(new HttpResponseMessage
    {
        StatusCode = HttpStatusCode.OK,
        Content = new StringContent(string.Empty),
        Headers =
        {
            RetryAfter = new RetryConditionHeaderValue(TimeSpan.FromSeconds(10))
        }
    });
```

`ILogger` szintén kigúnyolva:

```csharp
// Mock ILogger
var loggerMock = new Mock<ILogger>();
```  

Most a `Run` metódust az egység tesztből kell hívni:

```csharp
// Call Orchestration trigger function
var result = await HttpStart.Run(
    new HttpRequestMessage()
    {
        Content = new StringContent("{}", Encoding.UTF8, "application/json"),
        RequestUri = new Uri("http://localhost:7071/orchestrators/E1_HelloSequence"),
    },
    durableClientMock.Object,
    functionName,
    loggerMock.Object);
 ```

 Az utolsó lépés a kimenet összehasonlítása a várt értékkel:

```csharp
// Validate that output is not null
Assert.NotNull(result.Headers.RetryAfter);

// Validate output's Retry-After header value
Assert.Equal(TimeSpan.FromSeconds(10), result.Headers.RetryAfter.Delta);
```

Az összes lépés egyesítése után az egység tesztelése a következő kódot fogja tartalmazni:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HttpStartTests.cs)]

## <a name="unit-testing-orchestrator-functions"></a>Unit Testing Orchestrator functions

A Orchestrator függvények még érdekesebbek az egység tesztelése során, mivel általában sokkal több üzleti logikával rendelkeznek.

Ebben a szakaszban az egység tesztek ellenőrzik a Orchestrator függvény kimenetét `E1_HelloSequence` :

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

Az egység tesztelési kódja a modell létrehozásával kezdődik:

```csharp
var durableOrchestrationContextMock = new Mock<IDurableOrchestrationContext>();
```

Ezt követően a tevékenység metódusának hívásait a rendszer kigúnyolja:

```csharp
durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Tokyo")).ReturnsAsync("Hello Tokyo!");
durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Seattle")).ReturnsAsync("Hello Seattle!");
durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "London")).ReturnsAsync("Hello London!");
```

Ezután az egység tesztelése a következő `HelloSequence.Run` metódust fogja hívni:

```csharp
var result = await HelloSequence.Run(durableOrchestrationContextMock.Object);
```

Végül a rendszer érvényesíti a kimenetet:

```csharp
Assert.Equal(3, result.Count);
Assert.Equal("Hello Tokyo!", result[0]);
Assert.Equal("Hello Seattle!", result[1]);
Assert.Equal("Hello London!", result[2]);
```

Az összes lépés egyesítése után az egység tesztelése a következő kódot fogja tartalmazni:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceOrchestratorTests.cs)]

## <a name="unit-testing-activity-functions"></a>Unit Testing Activity functions

A Activity functions olyan egységként is tesztelhető, mint a nem tartós függvények.

Ebben a szakaszban az egység tesztelése ellenőrzi a `E1_SayHello` tevékenységi függvény viselkedését:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

Az egység tesztek pedig ellenőrzik a kimenet formátumát. Az egység tesztek közvetlenül vagy Mock osztályt használhatnak a paraméterek típusával `IDurableActivityContext` :

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceActivityTests.cs)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [További információ a xUnit](https://xunit.net/docs/getting-started/netcore/cmdline)
> 
> [További információ a MOQ](https://github.com/Moq/moq4/wiki/Quickstart)
