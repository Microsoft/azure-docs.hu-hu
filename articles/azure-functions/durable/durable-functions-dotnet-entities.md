---
title: Fejlesztői útmutató a tartós entitásokhoz a .NET-Azure Functions
description: A tartós entitások használata a .NET-ben a Azure Functions Durable Functions bővítménnyel.
author: sebastianburckhardt
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: azfuncdf
ms.openlocfilehash: 88d2a23104b67dae8fd480406eb9171e9f3d5652
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92740006"
---
# <a name="developers-guide-to-durable-entities-in-net"></a>Fejlesztői útmutató a tartós entitásokhoz a .NET-ben

Ebben a cikkben a tartós entitások .NET-tel való fejlesztéséhez rendelkezésre álló interfészeket ismertetjük részletesen, például példákat és általános tanácsokat. 

Az Entity functions lehetővé teszi a kiszolgáló nélküli alkalmazások fejlesztői számára, hogy az alkalmazás állapotát részletes entitások gyűjteményének megfelelően szervezze. A mögöttes fogalmakkal kapcsolatos további információkért tekintse meg a [tartós entitások: fogalmakat](durable-functions-entities.md) ismertető cikket.

Jelenleg két API-t kínálunk az entitások definiálásához:

- Az **osztály-alapú szintaxis** az entitásokat és a műveleteket osztályként és metódusként jelöli. Ez a szintaxis egyszerűen olvasható kódot hoz létre, és lehetővé teszi, hogy a műveletek típussal ellenőrzött módon legyenek meghívva az interfészeken keresztül. 

- A **függvény-alapú szintaxis** egy alacsonyabb szintű felület, amely függvényekként jelöli meg az entitásokat. Részletesen szabályozhatja az entitások műveleteinek elküldését, valamint az entitás állapotának kezelését.  

Ez a cikk elsősorban az osztályon alapuló szintaxisra összpontosít, mivel a legtöbb alkalmazáshoz jobban alkalmazkodik. A [függvény-alapú szintaxis](#function-based-syntax) azonban megfelelő lehet azon alkalmazások esetében, amelyek az entitás állapotának és műveleteinek saját absztrakcióit szeretnék meghatározni vagy kezelni. Emellett érdemes lehet olyan kódtárakat megvalósítani, amelyek a Class-alapú szintaxis által jelenleg nem támogatott általános feltételt igényelnek. 

> [!NOTE]
> Az osztály-alapú szintaxis csak a függvény-alapú szintaxison felüli réteg, így mindkét változat egyszerre használható ugyanazon az alkalmazásban. 
 
## <a name="defining-entity-classes"></a>Entitás-osztályok definiálása

A következő példa egy olyan entitás implementációja, `Counter` amely egy egész szám típusú értéket tárol, és négy műveletet kínál: `Add` , `Reset` `Get` és `Delete` .

```csharp
[JsonObject(MemberSerialization.OptIn)]
public class Counter
{
    [JsonProperty("value")]
    public int Value { get; set; }

    public void Add(int amount) 
    {
        this.Value += amount;
    }

    public Task Reset() 
    {
        this.Value = 0;
        return Task.CompletedTask;
    }

    public Task<int> Get() 
    {
        return Task.FromResult(this.Value);
    }

    public void Delete() 
    {
        Entity.Current.DeleteState();
    }

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

A `Run` függvény tartalmazza az osztály-alapú szintaxis használatának kötelezővé tételét. *Statikus* Azure-függvénynek kell lennie. Egyszer végrehajtja az entitás által feldolgozott összes műveleti üzenet esetében. Ha `DispatchAsync<T>` a hívása megtörtént, és az entitás még nincs a memóriában, egy típusú objektumot hoz létre, `T` és feltölti a mezőket a tárolóban található utolsó megőrzött JSON-ből (ha van ilyen). Ezután meghívja a metódust a megfelelő névvel.

> [!NOTE]
> Az osztály-alapú entitások állapota **implicit módon jön létre** , mielőtt az entitás feldolgozza a műveletet, és a hívásával **explicit módon törölhető** egy műveletben `Entity.Current.DeleteState()` .

### <a name="class-requirements"></a>Osztályra vonatkozó követelmények
 
Az entitás-osztályok a POCOs (egyszerű CLR-objektumok), amelyek nem igényelnek különleges szuperosztály, illesztőfelület vagy attribútum használatát. Azonban

- Az osztálynak szerkeszthető kell lennie (lásd: [entitások építése](#entity-construction)).
- Az osztálynak JSON-szerializálható kell lennie (lásd: [entitás szerializálása](#entity-serialization)).

Emellett a műveletnek meghívott bármely módszernek meg kell felelnie a további követelményeknek:

- Egy műveletnek legfeljebb egy argumentummal kell rendelkeznie, és nem tartalmazhat túlterhelést vagy általános típusú argumentumot.
- Egy olyan művelet, amely egy illesztőfelületnek a felülettel való meghívásához szükséges, vagy a-t kell visszaadnia `Task` `Task<T>` .
- Az argumentumoknak és a visszatérési értékeknek szerializálható értékeknek vagy objektumoknak kell lenniük.

### <a name="what-can-operations-do"></a>Milyen műveleteket végezhetnek el?

Az entitások összes művelete képes olvasni és frissíteni az entitás állapotát, és az állapot változásai automatikusan megmaradnak a tárolóban. Emellett a műveletek külső I/O-vagy egyéb számításokat is végezhetnek az általános korlátokon belül az összes Azure Functions.

A műveletek a környezet által biztosított funkciókhoz is hozzáférnek `Entity.Current` :

* `EntityName`: az aktuálisan végrehajtó entitás neve.
* `EntityKey`: az aktuálisan végrehajtó entitás kulcsa.
* `EntityId`: a jelenleg végrehajtó entitás azonosítója (tartalmazza a nevet és a kulcsot).
* `SignalEntity`: egyirányú üzenet küldése egy entitásnak.
* `CreateNewOrchestration`: elindítja az új előkészítést.
* `DeleteState`: törli az entitás állapotát.

Például módosíthatjuk a számláló entitást, hogy elindítson egy előkészítést, amikor a számláló eléri a 100-et, és bemeneti argumentumként továbbítja az entitás AZONOSÍTÓját:

```csharp
    public void Add(int amount) 
    {
        if (this.Value < 100 && this.Value + amount >= 100)
        {
            Entity.Current.StartNewOrchestration("MilestoneReached", Entity.Current.EntityId);
        }
        this.Value += amount;      
    }
```

## <a name="accessing-entities-directly"></a>Entitások közvetlen elérése

Az osztály-alapú entitások közvetlenül, az entitáshoz és annak műveleteihez explicit karakterlánc-nevek használatával érhetők el. Alább néhány példát láthatunk. az alapul szolgáló fogalmak (például a jelek és hívások) mélyebb magyarázatát lásd: az [Access-entitások](durable-functions-entities.md#access-entities)vitafóruma. 

> [!NOTE]
> Ha lehetséges, az [entitások felületeken keresztüli elérését](#accessing-entities-through-interfaces)javasoljuk, mert több típusú ellenőrzést is biztosít.

### <a name="example-client-signals-entity"></a>Példa: ügyfél-jelzési entitás

A következő Azure http-függvény REST-konvenciók használatával valósítja meg a TÖRLÉSi műveletet. Törlési jelet küld a számláló entitásnak, amelynek a kulcsa az URL-cím elérési útja.

```csharp
[FunctionName("DeleteCounter")]
public static async Task<HttpResponseMessage> DeleteCounter(
    [HttpTrigger(AuthorizationLevel.Function, "delete", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    await client.SignalEntityAsync(entityId, "Delete");    
    return req.CreateResponse(HttpStatusCode.Accepted);
}
```

### <a name="example-client-reads-entity-state"></a>Példa: ügyfél olvasási entitásának állapota

A következő Azure http-függvény REST-konvenciók használatával valósítja meg a GET műveletet. Beolvassa a számláló entitás aktuális állapotát, amelynek a kulcsa az URL-cím elérési útja.

```csharp
[FunctionName("GetCounter")]
public static async Task<HttpResponseMessage> GetCounter(
    [HttpTrigger(AuthorizationLevel.Function, "get", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    var state = await client.ReadEntityStateAsync<Counter>(entityId); 
    return req.CreateResponse(state);
}
```

> [!NOTE]
> A által visszaadott objektum `ReadEntityStateAsync` csak egy helyi másolat, azaz az entitás állapotának pillanatképe néhány korábbi időpontból. Különösen előfordulhat, hogy elavult, és ennek az objektumnak a módosítása nem befolyásolja az aktuális entitást. 

### <a name="example-orchestration-first-signals-then-calls-entity"></a>Példa: első jelek, majd az entitás meghívása

A következő összehangolás egy számláló entitást jelöl meg a növeléshez, majd meghívja ugyanazt az entitást a legutóbbi érték beolvasásához.

```csharp
[FunctionName("IncrementThenGet")]
public static async Task<int> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId("Counter", "myCounter");

    // One-way signal to the entity - does not await a response
    context.SignalEntity(entityId, "Add", 1);

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");

    return currentValue;
}
```

## <a name="accessing-entities-through-interfaces"></a>Entitások elérése felületeken keresztül

A illesztőfelületek az entitások generált proxy objektumokon keresztül való elérésére használhatók. Ez a módszer biztosítja, hogy egy művelet neve és argumentuma megfelel a megvalósított értékeknek. Ha lehetséges, javasoljuk, hogy használjon felületeket az entitásokhoz való hozzáféréshez.

A számláló példáját például a következőképpen módosíthatja:

```csharp
public interface ICounter
{
    void Add(int amount);
    Task Reset();
    Task<int> Get();
    void Delete();
}

public class Counter : ICounter
{
    ...
}
```

Az entitás-osztályok és az entitás-illesztőfelületek hasonlóak az [Orleans](https://www.microsoft.com/research/project/orleans-virtual-actors/)által népszerű gabona-és gabona-illesztőfelületekhez. A tartós entitások és a Orleans közötti hasonlóságokkal és különbségekkel kapcsolatos további információkért lásd: [összehasonlítás a virtuális szereplőkkel](durable-functions-entities.md#comparison-with-virtual-actors).

A típusok ellenőrzésének biztosításán kívül a felületek hasznosak lehetnek az alkalmazáson belüli problémáinak jobb elkülönítéséhez. Mivel például egy entitás több felületet is megvalósíthat, egyetlen entitás több szerepkört is kiszolgálhat. Mivel a felületet több entitás is megvalósíthatja, az általános kommunikációs minták újrafelhasználható könyvtárakként is végrehajthatók.

### <a name="example-client-signals-entity-through-interface"></a>Példa: ügyfél-jeleket kezelő entitás kapcsolaton keresztül

Az ügyfél kódja `SignalEntityAsync<TEntityInterface>` a használatával jeleket küldhet a megvalósítás alatt álló entitásoknak `TEntityInterface` . Például:

```csharp
[FunctionName("DeleteCounter")]
public static async Task<HttpResponseMessage> DeleteCounter(
    [HttpTrigger(AuthorizationLevel.Function, "delete", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    await client.SignalEntityAsync<ICounter>(entityId, proxy => proxy.Delete());    
    return req.CreateResponse(HttpStatusCode.Accepted);
}
```

Ebben a példában a `proxy` paraméter a dinamikusan generált példánya `ICounter` , amely belsőleg lefordítja a hívást `Delete` egy jellé.

> [!NOTE]
> Az `SignalEntityAsync` API-kat csak egyirányú műveletekhez lehet használni. Még ha egy művelet is visszaadja `Task<T>` , a `T` paraméter értéke mindig NULL lesz, vagy `default` nem a tényleges eredmény.
Nem érdemes például jelezni a `Get` műveletet, mivel a rendszer nem ad vissza értéket. Ehelyett az ügyfelek `ReadStateAsync` közvetlenül is hozzáférhetnek a számláló állapotához, vagy elindíthatnak egy Orchestrator-függvényt, amely meghívja a `Get` műveletet. 

### <a name="example-orchestration-first-signals-then-calls-entity-through-proxy"></a>Példa: első jelek, majd az entitás meghívása proxyn keresztül

Ha egy entitást egy előkészítésen belül szeretne meghívni vagy jelezni, a használható a `CreateEntityProxy` csatoló típusával együtt az entitáshoz tartozó proxy létrehozásához. Ez a proxy ezután a következő műveletekkel hívható meg vagy jelezheti a műveleteket:

```csharp
[FunctionName("IncrementThenGet")]
public static async Task<int> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId("Counter", "myCounter");
    var proxy = context.CreateEntityProxy<ICounter>(entityId);

    // One-way signal to the entity - does not await a response
    proxy.Add(1);

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await proxy.Get();

    return currentValue;
}
```

Implicit módon a visszaadott műveletek, illetve a visszaadott `void` `Task` vagy `Task<T>` meghívott műveletek. Az egyik megváltoztathatja ezt az alapértelmezett viselkedést, és a műveleteket, még akkor is, ha az adott feladatot a `SignalEntity<IInterfaceType>` metódus explicit módon használva.

### <a name="shorter-option-for-specifying-the-target"></a>A cél megadásának rövidebb beállítása

Ha felületet használó entitást hív vagy jelez, az első argumentumnak meg kell adnia a célként megadott entitást. A cél megadható az entitás AZONOSÍTÓjának megadásával, vagy olyan esetekben, amikor csak egy osztály valósítja meg az entitást, csak az Entity (entitás) kulcsot:

```csharp
context.SignalEntity<ICounter>(new EntityId(nameof(Counter), "myCounter"), ...);
context.SignalEntity<ICounter>("myCounter", ...);
```

Ha csak az Entity kulcs van megadva, és a futtatókörnyezetben nem található egyedi implementáció, `InvalidOperationException` a rendszer kidobja. 

### <a name="restrictions-on-entity-interfaces"></a>Entitás-illesztőfelületek korlátozásai

A szokásos módon minden paraméter-és visszatérési típusnak JSON-szerializálható kell lennie. Ellenkező esetben a szerializálási kivételeket a rendszer futásidőben kidobja.

Néhány további szabályt is érvénybe léptetett:
* Az entitás-illesztőfelületeknek csak metódusokat kell meghatározniuk.
* Az entitás-illesztőfelületek nem tartalmazhatnak általános paramétereket.
* Az entitás-illesztőfelületi metódusok nem rendelkezhetnek egynél több paraméterrel.
* Az entitás-illesztőfelületi metódusoknak vissza kell térniük `void` , `Task` vagy `Task<T>` 

Ha a szabályok bármelyike meg van sértve, a `InvalidOperationException` rendszer futásidőben, amikor a felületet a vagy a típus argumentumként használja `SignalEntity` `CreateProxy` . A kivételt jelző üzenet ismerteti, hogy melyik szabály lett megszakítva.

> [!NOTE]
> A visszaadott illesztőfelület-metódusok `void` csak egyirányú (egyirányú), nem más néven (kétirányú) lehet. A visszaadott vagy hívható illesztőfelület-metódusok `Task` `Task<T>` . Ha a hívása megtörténik, a művelet eredményét visszaállítják, vagy a művelet által eldobott kivételeket. Ha azonban a jelezve van, nem adják vissza a művelet tényleges eredményét vagy kivételét, de csak az alapértelmezett értéket.

## <a name="entity-serialization"></a>Entitás szerializálása

Mivel az entitások állapota tartósan marad, az Entity osztálynak szerializálható kell lennie. Az Durable Functions Runtime erre a célra használja a [JSON.net](https://www.newtonsoft.com/json) könyvtárat, amely számos házirendet és attribútumot támogat a szerializálási és a deszerializálási folyamat szabályozásához. A leggyakrabban használt C#-adattípusok (beleértve a tömböket és a gyűjtemény típusát) már szerializálható, és könnyen használhatók a tartós entitások állapotának meghatározásához.

A Json.NET például egyszerűen szerializálhatja és deszerializálhatja a következő osztályt:

```csharp
[JsonObject(MemberSerialization = MemberSerialization.OptIn)]
public class User
{
    [JsonProperty("name")]
    public string Name { get; set; }

    [JsonProperty("yearOfBirth")]
    public int YearOfBirth { get; set; }

    [JsonProperty("timestamp")]
    public DateTime Timestamp { get; set; }

    [JsonProperty("contacts")]
    public Dictionary<Guid, Contact> Contacts { get; set; } = new Dictionary<Guid, Contact>();

    [JsonObject(MemberSerialization = MemberSerialization.OptOut)]
    public struct Contact
    {
        public string Name;
        public string Number;
    }

    ...
}
```

### <a name="serialization-attributes"></a>Szerializálási attribútumok

A fenti példában úgy döntöttünk, hogy több attribútumot is tartalmaz, amelyek láthatóvá teszik a mögöttes szerializálást:
- A osztályt megjegyzésekkel láthatjuk `[JsonObject(MemberSerialization.OptIn)]` el, hogy emlékeztessenk, hogy az osztálynak szerializálható kell lennie, és csak olyan tagokat kell megőriznie, amelyek kifejezetten JSON-tulajdonságokkal vannak megjelölve.
-  A megőrzött mezőket megjegyzésekkel láthatjuk `[JsonProperty("name")]` , hogy egy mező a megőrzött entitás állapotának részét képezi-e, és meg kell határozni a JSON-ábrázolásban használandó tulajdonságnév nevét.

Ezek az attribútumok azonban nem szükségesek; más konvenciók vagy attribútumok akkor is engedélyezettek, ha a Json.NET-mel működnek. Előfordulhat például, hogy az egyik `[DataContract]` attribútumot vagy egyetlen attribútumot sem használ:

```csharp
[DataContract]
public class Counter
{
    [DataMember]
    public int Value { get; set; }
    ...
}

public class Counter
{
    public int Value;
    ...
}
```

Alapértelmezés szerint az osztály neve *nem* a JSON-ábrázolás részeként van tárolva: ez az `TypeNameHandling.None` alapértelmezett beállítás. Ez az alapértelmezett viselkedés felülbírálható a `JsonObject` vagy `JsonProperty` attribútumok használatával.

### <a name="making-changes-to-class-definitions"></a>Az osztályok definícióinak módosítása

Némi gondra van szükség, amikor egy alkalmazás futtatása után módosítja az osztály definícióját, mert a tárolt JSON-objektum már nem felel meg az új osztály definíciójának. Az adatformátumok módosítása azonban gyakran lehetséges, ha az egyik ismeri a által használt deszerializálás folyamatát `JsonConvert.PopulateObject` .

Íme néhány példa a változásokra és azok hatására:

1. Ha új tulajdonságot ad hozzá, amely nem szerepel a tárolt JSON-fájlban, akkor a rendszer az alapértelmezett értéket feltételezi.
1. Ha eltávolít egy tulajdonságot, amely megtalálható a tárolt JSON-fájlban, az előző tartalom elvész.
1. Ha egy tulajdonságot átneveznek, a hatás ugyanúgy történik, mint ha eltávolítja a régit, és hozzáad egy újat.
1. Ha a tulajdonság típusa módosítva lett, és a rendszer már nem tudja deszerializálni a tárolt JSON-ből, kivétel keletkezik.
1. Ha a tulajdonság típusa módosítva van, de továbbra is deszerializálható a tárolt JSON-ból, akkor azt.

Számos lehetőség áll rendelkezésre a Json.NET viselkedésének testreszabásához. Ha például egy kivételt szeretne kényszeríteni, ha a tárolt JSON olyan mezőt tartalmaz, amely nem szerepel a osztályban, akkor az attribútumot kell megadnia `JsonObject(MissingMemberHandling = MissingMemberHandling.Error)` . Egyéni kódot is írhat a deszerializálás számára, amely tetszőleges formátumban tárolhatja a JSON-t.

## <a name="entity-construction"></a>Entitások kialakítása

Néha azt szeretnénk, hogy jobban szabályozzák az entitás-objektumok kialakításának módját. Az entitás-objektumok létrehozásakor az alapértelmezett viselkedés módosításának számos beállítását ismertetjük. 

### <a name="custom-initialization-on-first-access"></a>Egyéni inicializálás az első hozzáféréskor

Időnként néhány speciális inicializálást kell végrehajtania, mielőtt egy műveletet egy soha nem elért vagy törölt entitásra hajtanak végre. Ennek a viselkedésnek a megadásához egy feltételes feltételt adhat hozzá a következőhöz `DispatchAsync` :

```csharp
[FunctionName(nameof(Counter))]
public static Task Run([EntityTrigger] IDurableEntityContext ctx)
{
    if (!ctx.HasState)
    {
        ctx.SetState(...);
    }
    return ctx.DispatchAsync<Counter>();
}
```

### <a name="bindings-in-entity-classes"></a>Kötések az Entity classs szolgáltatásban

A normál függvényektől eltérően az Entity Class metódusok nem rendelkeznek közvetlen hozzáféréssel a bemeneti és kimeneti kötésekhez. Ehelyett a kötési adatrögzítést a belépési pont függvény deklarációjában kell rögzíteni, majd át kell adni a `DispatchAsync<T>` metódusnak. A rendszer az összes átadott objektumot `DispatchAsync<T>` argumentumként automatikusan átadja az entitás osztály konstruktorának.

Az alábbi példa azt szemlélteti, hogyan lehet `CloudBlobContainer` elérhetővé tenni egy, a [blob bemeneti kötésből](../functions-bindings-storage-blob-input.md) származó hivatkozást egy osztály alapú entitás számára.

```csharp
public class BlobBackedEntity
{
    [JsonIgnore]
    private readonly CloudBlobContainer container;

    public BlobBackedEntity(CloudBlobContainer container)
    {
        this.container = container;
    }

    // ... entity methods can use this.container in their implementations ...
    
    [FunctionName(nameof(BlobBackedEntity))]
    public static Task Run(
        [EntityTrigger] IDurableEntityContext context,
        [Blob("my-container", FileAccess.Read)] CloudBlobContainer container)
    {
        // passing the binding object as a parameter makes it available to the
        // entity class constructor
        return context.DispatchAsync<BlobBackedEntity>(container);
    }
}
```

A Azure Functions-kötésekkel kapcsolatos további információkért tekintse meg a [Azure functions triggerek és kötések](../functions-triggers-bindings.md) dokumentációját.

### <a name="dependency-injection-in-entity-classes"></a>Függőség injekció az Entity classs szolgáltatásban

Az entitás osztályok támogatják [Azure functions függőségi injekciót](../functions-dotnet-dependency-injection.md). Az alábbi példa bemutatja, hogyan regisztrálhat egy `IHttpClientFactory` szolgáltatást egy osztály alapú entitásba.

```csharp
[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();
        }
    }
}
```

Az alábbi kódrészlet bemutatja, hogyan építheti be a befecskendezett szolgáltatást az Entity osztályba.

```csharp
public class HttpEntity
{
    [JsonIgnore]
    private readonly HttpClient client;

    public HttpEntity(IHttpClientFactory factory)
    {
        this.client = factory.CreateClient();
    }

    public Task<int> GetAsync(string url)
    {
        using (var response = await this.client.GetAsync(url))
        {
            return (int)response.StatusCode;
        }
    }

    [FunctionName(nameof(HttpEntity))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<HttpEntity>();
}
```

> [!NOTE]
> A szerializálással kapcsolatos problémák elkerülése érdekében ügyeljen arra, hogy kizárjon mezőket a befecskendezett értékek tárolásához a szerializálásból.

> [!NOTE]
> A normál .NET-Azure Functions konstruktorának használatakor a függvények belépési pontjának metódusát be kell jelenteni az osztály *alapú entitásokhoz* `static` . A nem statikus függvény belépési pontjának deklarálása ütközést okozhat a normál Azure Functions objektum-inicializáló és a tartós entitások objektum-inicializáló között.

## <a name="function-based-syntax"></a>Függvény-alapú szintaxis

Eddig az osztály-alapú szintaxisra koncentrálunk, ahogy azt várjuk, hogy jobban alkalmazkodjon a legtöbb alkalmazáshoz. A függvény-alapú szintaxis azonban olyan alkalmazások esetében lehet megfelelő, amelyek az entitás állapotának és műveleteinek saját absztrakcióit szeretnék meghatározni vagy kezelni. Emellett célszerű lehet olyan könyvtárak megvalósítása is, amelyek az osztály-alapú szintaxis által jelenleg nem támogatott általános feltételt igényelnek. 

A függvény-alapú szintaxissal az Entity függvény explicit módon kezeli a művelet elküldését, és explicit módon kezeli az entitás állapotát. Az alábbi kód például a függvény-alapú szintaxis használatával megvalósított *számláló* entitást mutatja.  

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            ctx.SetState(ctx.GetState<int>() + ctx.GetInput<int>());
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(ctx.GetState<int>());
            break;
        case "delete":
            ctx.DeleteState();
            break;
    }
}
```

### <a name="the-entity-context-object"></a>Az entitás környezeti objektuma

Az entitás-specifikus funkciók egy típusú környezeti objektumon keresztül érhetők el `IDurableEntityContext` . Ez a környezeti objektum az Entity függvény paramétereként és az aszinkron helyi tulajdonságon keresztül érhető el `Entity.Current` .

A következő tagok információt nyújtanak az aktuális műveletről, és lehetővé teszik a visszatérési érték megadását. 

* `EntityName`: az aktuálisan végrehajtó entitás neve.
* `EntityKey`: az aktuálisan végrehajtó entitás kulcsa.
* `EntityId`: a jelenleg végrehajtó entitás azonosítója (tartalmazza a nevet és a kulcsot).
* `OperationName`: az aktuális művelet neve.
* `GetInput<TInput>()`: az aktuális művelet bemenetének beolvasása.
* `Return(arg)`: egy olyan értéket ad vissza, amely a műveletet nevezte.

A következő tagok kezelik az entitás állapotát (létrehozás, olvasás, frissítés, törlés). 

* `HasState`: az, hogy az entitás létezik-e, van-e valamilyen állapota. 
* `GetState<TState>()`: az entitás aktuális állapotát kéri le. Ha még nem létezik, a rendszer létrehozza.
* `SetState(arg)`: az entitás állapotának létrehozása vagy frissítése.
* `DeleteState()`: törli az entitás állapotát, ha az létezik. 

Ha az által visszaadott állapot `GetState` egy objektum, akkor közvetlenül módosítható az alkalmazás kódjával. A végén nem kell újból meghívni `SetState` (de nem árt). Ha `GetState<TState>` többször is meghívva van, ugyanazt a típust kell használni.

Végezetül a következő tagok más entitások jelzésére vagy új összehangolás indítására használhatók:

* `SignalEntity(EntityId, operation, input)`: egyirányú üzenet küldése egy entitásnak.
* `CreateNewOrchestration(orchestratorFunctionName, input)`: elindítja az új előkészítést.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Tudnivalók az entitásokkal kapcsolatos fogalmakról](durable-functions-entities.md)
