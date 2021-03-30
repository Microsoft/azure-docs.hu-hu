---
title: Verziószámozás a Durable Functionsban – Azure
description: Megtudhatja, hogyan implementálhatja a verziószámozást a Azure Functions Durable Functions-bővítményében.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 87cbb94dbab241630dc7585bdf4314d858d5b4da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "74232752"
---
# <a name="versioning-in-durable-functions-azure-functions"></a>Verziószámozás Durable Functions (Azure Functions)

Elkerülhetetlen, hogy a rendszer felveszi, eltávolítja és megváltoztatja a függvényeket az alkalmazások élettartama során. [Durable functions](durable-functions-overview.md) lehetővé teszi a láncolási függvények összekapcsolását olyan módokon, amelyek korábban nem voltak lehetségesek, és ez a láncolás befolyásolja a verziószámozás kezelését.

## <a name="how-to-handle-breaking-changes"></a>A feltörési változások kezelése

Több példa is van arra, hogy miként lehet a módosításokat feltörni. Ez a cikk a leggyakoribb kérdéseket tárgyalja. Az összes közülük az egyik fő téma az, hogy az új és a meglévő függvények is hatással vannak a funkció kódjának változásaira.

### <a name="changing-activity-or-entity-function-signatures"></a>A tevékenység vagy az entitás függvény aláírásának módosítása

Az aláírás módosítása a függvények nevének, bemenetének vagy kimenetének változására utal. Ha a tevékenység vagy az Entity függvény ilyen típusú változást végez, akkor az a tőle függő bármely Orchestrator-függvényt megszakíthatja. Ha úgy frissíti a Orchestrator függvényt, hogy az megfeleljen ennek a változásnak, megszakíthatja a meglévő, repülés közbeni példányokat.

Tegyük fel például, hogy a következő Orchestrator függvényt használjuk.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Ez a leegyszerűsítő függvény a **foo** eredményeit veszi át, és a **sáv** felé továbbítja azt. Tegyük fel, hogy módosítani kell a **foo** értékének visszatérési értékét a-ből a-re, `bool` `int` hogy támogassa az eredmények szélesebb választékát. Az eredmény a következőhöz hasonló:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    int result = await context.CallActivityAsync<int>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

> [!NOTE]
> Az előző C#-példák célja Durable Functions 2. x. Durable Functions 1. x esetén a helyett a értéket kell használnia `DurableOrchestrationContext` `IDurableOrchestrationContext` . A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

Ez a változás jól működik a Orchestrator függvény összes új példányán, de a repülés közbeni példányokat is megszakítja. Vegyük például azt az esetet, amikor egy összehangoló példány meghívja a nevű függvényt `Foo` , egy logikai értéket kap vissza, majd ellenőrzőpontokat. Ha az aláírás módosítása ezen a ponton történik, az ellenőrzőponttal rendelkező példány azonnal meghiúsul, amikor folytatja, és visszajátssza a hívást `context.CallActivityAsync<int>("Foo")` . Ez a hiba azért fordul elő, mert az előzmények tábla eredménye, `bool` de az új kód megpróbálja deszerializálni a-ba `int` .

Ez a példa az aláírások módosításának számos különböző módja, amely megszakíthatja a meglévő példányokat. Általánosságban elmondható, hogy ha egy Orchestrator módosítania kell egy függvény meghívásának módját, akkor a változás valószínűleg problematikus lesz.

### <a name="changing-orchestrator-logic"></a>Orchestrator-logika módosítása

A verziószámozási problémák másik osztálya úgy változik, hogy a Orchestrator függvény kódját úgy változtatja meg, hogy megzavarja a fedélzeti példányok újrajátszása logikáját.

Vegye figyelembe a következő Orchestrator függvényt:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Most tegyük fel, hogy látszólag ártatlan változást kíván tenni egy másik függvény hívásának hozzáadásához.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    if (result)
    {
        await context.CallActivityAsync("SendNotification");
    }

    await context.CallActivityAsync("Bar", result);
}
```

> [!NOTE]
> Az előző C#-példák célja Durable Functions 2. x. Durable Functions 1. x esetén a helyett a értéket kell használnia `DurableOrchestrationContext` `IDurableOrchestrationContext` . A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

Ez a változás egy új függvény hívását adja hozzá a **SendNotification** a **foo** és a **Bar** között. Nincsenek aláírási változások. A probléma akkor fordul elő, amikor egy meglévő példány folytatja a hívást a **sávra**. Ha a visszajátszáskor a rendszer visszaküldi a **foo** eredeti hívását `true` , akkor a Orchestrator visszajátszás a **SendNotification**, amely nem szerepel a végrehajtás előzményeiben. Ennek eredményeképpen a tartós feladathoz tartozó keretrendszer meghiúsul, `NonDeterministicOrchestrationException` mert a **SendNotification** meghívását észlelte a rendszer, amikor a vártnál a **sáv** hívása látható. Ugyanaz a probléma akkor fordulhat elő, ha bármilyen hívást "tartós" API-khoz, például `CreateTimer` , `WaitForExternalEvent` stb.

## <a name="mitigation-strategies"></a>Kockázatcsökkentő stratégiák

Íme néhány, a verziószámozási kihívásokkal foglalkozó stratégia:

* Nincs teendő
* Az összes fedélzeti példány leállítása
* Párhuzamos üzembe helyezések

### <a name="do-nothing"></a>Nincs teendő

A feltörési változások kezelésének legegyszerűbb módja a fedélzeti előkészítési példányok meghibásodása. Az új példányok sikeresen futtatták a módosított kódot.

Ez a hiba attól függ, hogy az ilyen típusú hibák a repülésen kívüli példányok fontosságán alapulnak-e. Ha aktív fejlesztést végez, és nem érdeklik a fedélzeti példányok, ez elég jó lehet. Azonban a diagnosztika folyamatában a kivételekkel és a hibákkal kell foglalkoznia. Ha el szeretné kerülni ezeket a dolgokat, tekintse át a többi verziószámozási lehetőséget.

### <a name="stop-all-in-flight-instances"></a>Az összes fedélzeti példány leállítása

Egy másik lehetőség az összes fedélzeti példány leállítása. Az összes példány leállítása a belső **vezérlő – üzenetsor** és a **Munkatétel-várólista** várólistájának tartalmának törlésével végezhető el. A példányok örökre megakadnak, ahol ezek a felhasználók, de a naplófájlokat nem fogja felhasználni a hibaüzenetekkel. Ez a megközelítés ideális megoldás a gyors prototípus-fejlesztéshez.

> [!WARNING]
> A várólisták részletei idővel változhatnak, ezért ne használja ezt a technikát éles számítási feladatokhoz.

### <a name="side-by-side-deployments"></a>Párhuzamos üzembe helyezések

A legtöbb sikertelen működést biztosító módszer, amellyel biztosítható, hogy a megszakított változások biztonságosan üzembe helyezhetők legyenek, ha a régebbi verziókkal párhuzamosan telepíti őket. Ezt a következő módszerek bármelyikével teheti meg:

* Telepítse az összes frissítést teljes mértékben új függvényként, így a meglévő függvények is megmaradnak. Ez trükkös lehet, mert az új függvények verzióinak hívóit is frissíteni kell Ugyanezen irányelvek követésével.
* Telepítse az összes frissítést új Function-alkalmazásként egy másik Storage-fiókkal.
* Telepítse a Function alkalmazás egy új példányát ugyanazzal a Storage-fiókkal, de egy frissített `taskHub` névvel. Az ajánlott eljárás a párhuzamos üzembe helyezés.

### <a name="how-to-change-task-hub-name"></a>A feladat központ nevének módosítása

A feladat hub a következő módon konfigurálható a *host.js* fájlban:

#### <a name="functions-1x"></a>Functions 1.x

```json
{
    "durableTask": {
        "hubName": "MyTaskHubV2"
    }
}
```

#### <a name="functions-20"></a>Függvények 2,0

```json
{
    "extensions": {
        "durableTask": {
            "hubName": "MyTaskHubV2"
        }
    }
}
```

Durable Functions v1. x alapértelmezett értéke a következő: `DurableFunctionsHub` . A Durable Functions 2.0-s verziótól kezdődően az alapértelmezett Task hub neve megegyezik a Function alkalmazás nevével az Azure-ban, vagy ha az Azure-on `TestHubName` kívül fut.

Az összes Azure Storage-entitás neve a `hubName` konfigurációs érték alapján történik. Ha egy új nevet ad a Task hub számára, akkor az alkalmazás új verziójához külön üzenetsor-és előzmény-táblázatot kell létrehoznia. A Function alkalmazás azonban leállítja az események feldolgozását, illetve az előző feladathoz tartozó hub neve alatt létrehozott entitásokat.

Javasoljuk, hogy a Function alkalmazás új verzióját egy új [üzembe helyezési](../functions-deployment-slots.md)pontra telepítse. Az üzembe helyezési pontok lehetővé teszik, hogy a Function alkalmazás több példányát is futtassa, csak az egyiket aktív *üzemi* tárolóhelyként. Ha készen áll arra, hogy elérhetővé tegye az új előkészítési logikát a meglévő infrastruktúrájában, akkor olyan egyszerű lehet, hogy az új verziót az éles tárolóhelyre cseréli.

> [!NOTE]
> Ez a stratégia akkor működik a legjobban, ha HTTP-és webhook-eseményindítókat használ a Orchestrator functions szolgáltatáshoz. A nem HTTP-alapú eseményindítók, például a várólisták vagy a Event Hubs esetében az eseményindító definíciójának [olyan alkalmazás-beállításból kell származnia](../functions-bindings-expressions-patterns.md#binding-expressions---app-settings) , amely a swap művelet részeként frissül.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan kezelheti a teljesítménnyel és a skálázással kapcsolatos problémákat](durable-functions-perf-and-scale.md)
