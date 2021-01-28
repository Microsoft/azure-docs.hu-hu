---
title: SCP.NET programozási útmutató a Storm in Azure HDInsight
description: Útmutató a SCP.NET létrehozásához. NET-alapú Storm-topológiák az Azure HDInsight-ben futó Storm használatával.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 01/13/2020
ms.openlocfilehash: bd52157e2f0e20e9282d944b07f656c08d9e57da
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98932646"
---
# <a name="scp-programming-guide-for-apache-storm-in-azure-hdinsight"></a>SCP programozási útmutató az Azure HDInsight Apache Storm

Az SCP a valós idejű, megbízható, konzisztens és nagy teljesítményű adatfeldolgozási alkalmazások létrehozásához használható platform. [Apache Stormra](https://storm.incubator.apache.org/)épül, amely egy nyílt forráskódú szoftverek által tervezett adatfolyam-feldolgozó rendszer. Nathan Marz létrehozta a Stormot. A Twitter nyílt forráskódúként jelent meg. A Storm [Apache ZooKeepert](https://zookeeper.apache.org/)használ, amely egy másik Apache-projekt, amely lehetővé teszi a megbízható elosztott koordinációt és az állami felügyeletet.

Az SCP-projekt nem csak a Storm on Windows rendszert, hanem a Project által hozzáadott bővítményeket és a Windows-környezet testreszabását is elvégezte. A bővítmények közé tartozik a .NET fejlesztői élmény és a .NET-kódtárak. A Testreszabás magában foglalja a Windows-alapú telepítést.

A bővítmények és a Testreszabás nem szükséges a nyílt forráskódú szoftveres projektek elágazásához. A Storm-re épülő származtatott környezeteket is használhat.

## <a name="processing-model"></a>Feldolgozási modell

Az SCP-ben lévő adatmennyiség a rekordok folytonos adatfolyamként van modellezve. Általában a rekordok:

1. A folyamat egy várólistába kerül.
1. A rendszer a Storm-topológián belül üzemeltetett üzleti logika átveszi és átalakítja azokat.
1. Vagy a kimenetét egy másik SCP-rendszerbe rekordok, vagy elkötelezettek lesznek a (z), például elosztott fájlrendszerek és adatbázisok (például SQL Server) tárolására.

![Egy üzenetsor diagramja, amely a feldolgozásra szánt adatmennyiséget táplálja, ami viszont adattárakat táplál](./media/apache-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png)

A Storm-ben egy alkalmazás-topológia definiál egy számítási diagramot. A topológia minden csomópontja feldolgozási logikát tartalmaz. A csomópontok közötti kapcsolatok jelzik az adatfolyamot.

A bemeneti adatokat a topológiába beinjektáló csomópontok neve _kiöntő_. Ezeket az adatsorokra használhatja. A bemeneti adatok származhatnak olyan forrásokból, mint a naplófájlok, a tranzakciós adatbázisok vagy a rendszerteljesítmény-számlálók.

A bemeneti és kimeneti adatfolyamatokkal rendelkező csomópontokat a rendszer _csavaroknak_ nevezzük. A tényleges adatszűrést, kiválasztásokat és összesítést végzik.

Az SCP támogatja a legjobb erőfeszítéseket, legalább egyszer, és pontosan egyszer az adatfeldolgozást.

Elosztott adatfolyam-feldolgozó alkalmazásban hibák merülhetnek fel az adatfeldolgozás során. Ilyen hibák lehetnek például a hálózati leállás, a gép meghibásodása vagy a kód hibája. A legalább egyszeri feldolgozási funkció biztosítja, hogy az összes adatfeldolgozás legalább egyszer megtörténjen, ha a hiba történik.

A legalább egyszeri feldolgozás egyszerű és megbízható, és számos alkalmazásra illeszkedik. Ha azonban egy alkalmazásnak pontos számlálásra van szüksége, akkor a legalább egyszeri feldolgozás nem elegendő, mert ugyanazokat az adatmennyiségeket lehet lejátszani az alkalmazás topológiájában. Ebben az esetben a feldolgozás pontosan egyszer ellenőrzi, hogy az eredmény helyes-e, még akkor is, ha az adatok többször is le vannak hajtva és fel vannak dolgozva.

Az SCP lehetővé teszi, hogy a .NET-fejlesztők valós idejű adatfeldolgozási alkalmazásokat hozzanak létre Java virtuális gép (JVM) és Storm használatával. A JVM és a .NET TCP helyi szoftvercsatornán keresztül kommunikál. Minden kiöntő/bolt egy .NET/Java-feldolgozási pár, ahol a felhasználói logika egy .NET-folyamatban fut beépülő modulként.

Az alábbi lépéseket követve hozhat létre egy adatfeldolgozó alkalmazást az SCP felett:

1. Megtervezheti és implementálhatja a kiállítók adatait a várólistákból való lekéréshez.
1. Megtervezheti és megvalósíthatja a bemeneti adatokat feldolgozó és a külső áruházakba, például egy adatbázisba mentett boltokat.
1. Tervezze meg a topológiát, majd küldje el és futtassa.

A topológia meghatározza a csúcspontokat és a közöttük lévő adatforgalmat. Az SCP egy topológiai specifikációt használ, és üzembe helyezi azt egy Storm-fürtön, ahol minden csúcspont egy logikai csomóponton fut. A Storm Feladatütemező gondoskodik a feladatátvételről és a skálázásról.

Ez a cikk néhány egyszerű példát mutat be, amely végigvezeti az adatfeldolgozási alkalmazások SZOLGÁLTATÁSKAPCSOLÓDÁSI ponttal való kiépítésének folyamatán.

## <a name="scp-plug-in-interface"></a>SZOLGÁLTATÁSKAPCSOLÓDÁSI pont beépülőmodul-felülete

Az SCP beépülő modulok önálló alkalmazások. A fejlesztés során futhatnak a Visual Studióban, és az éles üzembe helyezést követően a Storm-folyamatba is csatlakoztathatók.

Az SCP beépülő modul írása ugyanúgy történik, mint bármely más Windows-konzol alkalmazás írásakor. A SCP.NET platform deklarál néhány felületet a kiöntő/bolt számára. A beépülőmodul-kód implementálja ezeket a csatolókat. Ennek a kialakításnak a fő célja, hogy lehetővé tegye, hogy az üzleti logikára koncentráljon, miközben a SCP.NET platform más dolgok kezelésére is alkalmas.

A beépülőmodul-kód a következő felületek egyikét valósítja meg. Melyik csatoló függ attól, hogy a topológia tranzakciós vagy nem tranzakciós, és hogy az összetevő egy kiöntő vagy egy bolt.

* **ISCPSpout**
* **ISCPBolt**
* **ISCPTxSpout**
* **ISCPBatchBolt**

### <a name="iscpplugin"></a>ISCPPlugin

A **ISCPPlugin** a különböző beépülő modulok általános felülete. Jelenleg ez egy dummy felület.

```csharp
public interface ISCPPlugin
{
}
```

### <a name="iscpspout"></a>ISCPSpout

A **ISCPSpout** a nem tranzakciós kiöntő felülete.

```csharp
public interface ISCPSpout : ISCPPlugin
{
    void NextTuple(Dictionary<string, Object> parms);
    void Ack(long seqId, Dictionary<string, Object> parms); 
    void Fail(long seqId, Dictionary<string, Object> parms);
}
```

A **NextTuple** hívásakor a C#-kód egy vagy több rekordok is kibocsáthat. Ha nincs probléma a kibocsátással, ez a módszer bármilyen kibocsátása nélkül visszatérhet.

A **NextTuple**, az **ACK** és a **Fail** metódusok mindegyike szoros hurokban van meghívva egy C#-folyamat egyetlen szálában. Ha nincs rekordok a kibocsátáshoz, rövid ideig **NextTuple** az alvó állapotot, például 10 ezredmásodpercet. Ez az alvó üzemmód segít elkerülni a CPU rendelkezésre állásának pazarlását.

A **ACK** és a **Fail** metódust csak akkor hívja meg a rendszer, ha a specifikációs fájl engedélyezi a visszaigazolási mechanizmust. A *emlékeztetése helyreállításkor* paraméter a visszaigazolt vagy sikertelen rekordot azonosítja. Ha a nyugtázás nem tranzakciós topológiában van engedélyezve, a következő **kibocsátó** függvényt kell használni a kiöntőben:

```csharp
public abstract void Emit(string streamId, List<object> values, long seqId);
```

Ha egy nem tranzakciós topológia nem támogatja a visszaigazolást, az **ACK** és a **Fail** függvény üres függvényekként is maradhat.

A függvények *Paraméterek* bemeneti paramétere üres szótárt ad meg, és későbbi használatra van fenntartva.

### <a name="iscpbolt"></a>ISCPBolt

A **ISCPBolt** egy nem tranzakciós bolt felülete.

```csharp
public interface ISCPBolt : ISCPPlugin
{
void Execute(SCPTuple tuple);
}
```

Ha új rekord áll rendelkezésre, a rendszer feldolgozza a **Execute** függvényt.

### <a name="iscptxspout"></a>ISCPTxSpout

A **ISCPTxSpout** a tranzakciós kiöntő felülete.

```csharp
public interface ISCPTxSpout : ISCPPlugin
{
    void NextTx(out long seqId, Dictionary<string, Object> parms);  
    void Ack(long seqId, Dictionary<string, Object> parms);         
    void Fail(long seqId, Dictionary<string, Object> parms);        
}
```

Akárcsak a nem tranzakciós partnerekhez, a **NextTx**, az **ACK** és a **Fail utasításhoz** hasonlóan minden egyes C#-folyamat egyetlen szálának szűk hurkot kell hívnia. Ha nincs rekordok a kibocsátáshoz, rövid ideig **NextTx** az alvó állapotot, például 10 ezredmásodpercet. Ez az alvó üzemmód segít elkerülni a CPU rendelkezésre állásának pazarlását.

Ha a **NextTx** új tranzakció elindítását hívja meg, a *emlékeztetése helyreállításkor* kimeneti paraméter azonosítja a tranzakciót. A tranzakciót **ACK** -ban is használják, és a művelet **meghiúsul**. Az **NextTx** metódus képes a Java-oldalra kibocsátani az adatait. A rendszer a ZooKeeper tárolja az adatvisszajátszás támogatásához. Mivel a ZooKeeper kapacitása korlátozott, a kódnak csak a metaadatokat kell kibocsátania, a tranzakciós kiöntőban nem pedig tömeges adatokat.

Mivel a Storm automatikusan visszajátssza a sikertelen tranzakciókat, a rendszer általában nem fogja hívni a **hibát** . Ha azonban az SCP képes megtekinteni a tranzakciós kiöntő által kibocsátott metaadatokat, akkor a **meghiúsulhat** , ha a metaadatok érvénytelenek.

A függvények *Paraméterek* bemeneti paramétere üres szótárt ad meg, és későbbi használatra van fenntartva.

### <a name="iscpbatchbolt"></a>ISCPBatchBolt

A **ISCPBatchBolt** egy tranzakciós bolt felülete.

```csharp
public interface ISCPBatchBolt : ISCPPlugin
{
    void Execute(SCPTuple tuple);
    void FinishBatch(Dictionary<string, Object> parms);  
}
```

Az **Execute** metódus akkor lesz meghívva, amikor egy új rekord érkezik a boltba. A **FinishBatch** metódust a tranzakció befejeződése után hívja meg a rendszer. A *Paraméterek* bemeneti paramétere jövőbeli használatra van fenntartva.

Tranzakciós topológia esetén a **StormTxAttempt** egy fontos osztály. Két taggal rendelkezik: **TX** és **AttemptId**. Az **TX** tag egy adott tranzakciót azonosít. Előfordulhat, hogy egy tranzakció többször is próbálkozik, ha a művelet meghiúsul, és a rendszer újra lejátssza.

A SCP.NET egy új **ISCPBatchBolt** objektumot hoz létre az egyes **StormTxAttempt** -objektumok feldolgozásához, akárcsak a Storm a javában. Ez a kialakítás célja a párhuzamos tranzakciók feldolgozásának támogatása. A tranzakciós kísérlet befejezése után a rendszer megsemmisíti a megfelelő **ISCPBatchBolt** objektumot, és begyűjti a szemetet.

## <a name="object-model"></a>Objektummodell

A SCP.NET egy egyszerű, a fejlesztők számára elérhető billentyűparancsokat is biztosít a programhoz. Az objektumok a **kontextus**, a **Állapottárolója** és a **SCPRuntime**. Ezeket a szakasz tárgyalja.

### <a name="context"></a>Környezet

A **környezeti** objektum egy futó környezetet biztosít az alkalmazás számára. A **ISCPSpout**, a **ISCPBolt**, a **ISCPTxSpout** vagy a **ISCPBatchBolt** minden **ISCPPlugin** -példánya egy megfelelő **környezeti** példánnyal rendelkezik. A **környezet** által biztosított funkciók a következő két részre vannak osztva:

* A teljes C# folyamat során elérhető statikus rész
* A dinamikus rész, amely csak az adott **környezeti** példány esetében érhető el

### <a name="static-part"></a>Statikus rész

```csharp
public static ILogger Logger = null;
public static SCPPluginType pluginType;
public static Config Config { get; set; }
public static TopologyContext TopologyContext { get; set; }  
```

A **naplózó** objektum naplózási célokra szolgál.

A **beépülőmodul** objektum a C# folyamat beépülőmodul-típusát jelöli. Ha a folyamat Java nélkül fut a helyi tesztelési módban, a beépülő modul típusa **SCP_NET_LOCAL**.

```csharp
public enum SCPPluginType 
{
    SCP_NET_LOCAL = 0,
    SCP_NET_SPOUT = 1,
    SCP_NET_BOLT = 2,
    SCP_NET_TX_SPOUT = 3,
    SCP_NET_BATCH_BOLT = 4  
    }
```

A **config** tulajdonság a Java oldalról kapja meg a konfigurációs paramétereket, amely a C# beépülő modul inicializálásakor továbbítja azokat. A **konfigurációs** paraméterek két részre vannak osztva: **stormConf** és **pluginConf**.

```csharp
public Dictionary<string, Object> stormConf { get; set; }  
public Dictionary<string, Object> pluginConf { get; set; }  
```

A **stormConf** rész a Storm által meghatározott paraméterek, a **pluginConf** rész pedig az scp által definiált paraméterek. Bemutatunk egy példát:

```csharp
public class Constants
{
    … …

    // constant string for pluginConf
    public static readonly String NONTRANSACTIONAL_ENABLE_ACK = "nontransactional.ack.enabled";  

    // constant string for stormConf
    public static readonly String STORM_ZOOKEEPER_SERVERS = "storm.zookeeper.servers";
    public static readonly String STORM_ZOOKEEPER_PORT = "storm.zookeeper.port";
}
```

A **TopologyContext** típusa lekéri a topológia környezetét. A funkció több párhuzamos összetevő esetében is hasznos. Bemutatunk egy példát:

```csharp
//demo how to get TopologyContext info
if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
{
    Context.Logger.Info("TopologyContext info:");
    TopologyContext topologyContext = Context.TopologyContext;
    Context.Logger.Info("taskId: {0}", topologyContext.GetThisTaskId());
    taskIndex = topologyContext.GetThisTaskIndex();
    Context.Logger.Info("taskIndex: {0}", taskIndex);
    string componentId = topologyContext.GetThisComponentId();
    Context.Logger.Info("componentId: {0}", componentId);
    List<int> componentTasks = topologyContext.GetComponentTasks(componentId);  
    Context.Logger.Info("taskNum: {0}", componentTasks.Count);
}
```

### <a name="dynamic-part"></a>Dinamikus rész

A következő felületek egy adott **környezeti** példányhoz tartoznak, amelyet a SCP.net platform hoz létre, és a kód átadása:

```csharp
// Declare the Output and Input Stream Schemas

public void DeclareComponentSchema(ComponentStreamSchema schema);

// Emit tuple to default stream.
public abstract void Emit(List<object> values);

// Emit tuple to the specific stream.
public abstract void Emit(string streamId, List<object> values);  
```

A visszaigazolást támogató, nem tranzakciós kiöntő esetén a következő módszert kell megadnia:

```csharp
// for nontransactional spout that supports ack
public abstract void Emit(string streamId, List<object> values, long seqId);  
```

A nyugtát támogató, nem tranzakciós boltnak explicit módon hívnia kell az **ACK** -t, vagy a **hibát** a kapott rekordban. Új rekord kibocsátásakor a boltnak meg kell adnia a rekord horgonyait is. A következő módszereket kell megadnia:

```csharp
public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values);
public abstract void Ack(SCPTuple tuple);
public abstract void Fail(SCPTuple tuple);
```

### <a name="statestore"></a>Állapottárolója

A **állapottárolója** objektum metaadat-szolgáltatásokat, monoton sorozatot generál, és várakozás nélküli koordinációt biztosít. A **állapottárolója**-on magasabb szintű elosztott egyidejűségi absztrakciókat hozhat létre. Ezek az absztrakciók elosztott zárolásokat, elosztott várólistákat, korlátokat és tranzakciós szolgáltatásokat tartalmaznak.

Az SCP-alkalmazások az **állapot** objektum használatával szerializálják az adatokat [Apache ZooKeeperban](https://zookeeper.apache.org/). Ez a képesség különösen értékes a tranzakciós topológia számára. Ha egy tranzakciós kiöntő nem válaszol, és újraindul, az **állapot** beolvashatja a szükséges adatokat a ZooKeeper-ből, és újraindíthatja a folyamatot.

A **állapottárolója** objektum a következő módszerekkel rendelkezik:

```csharp
/// <summary>
/// Static method to retrieve a state store of the given path and connStr 
/// </summary>
/// <param name="storePath">StateStore path</param>
/// <param name="connStr">StateStore address</param>
/// <returns>Instance of StateStore</returns>
public static StateStore Get(string storePath, string connStr);

/// <summary>
/// Create a new state object in this state store instance
/// </summary>
/// <returns>State from StateStore</returns>
public State Create();

/// <summary>
/// Retrieve all states that were previously uncommitted, excluding all exited states
/// </summary>
/// <returns>Uncommitted states</returns>
public IEnumerable<State> GetUnCommitted();

/// <summary>
/// Get all the states in the StateStore
/// </summary>
/// <returns>All the states</returns>
public IEnumerable<State> States();

/// <summary>
/// Get state or registry object
/// </summary>
/// <param name="info">Registry name (registry only)</param>
/// <typeparam name="T">Type, registry or state</typeparam>
/// <returns>Return registry or state</returns>
public T Get<T>(string info = null);

/// <summary>
/// List all the committed states
/// </summary>
/// <returns>Registries containing the committed state </returns>
public IEnumerable<Registry> Committed();

/// <summary>
/// List all the exited states in the StateStore
/// </summary>
/// <returns>Registries containing the exited states</returns>
public IEnumerable<Registry> Aborted();

/// <summary>
/// Retrieve an existing state object from this state store instance 
/// </summary>
/// <returns>State from StateStore</returns>
/// <typeparam name="T">stateId, id of the State</typeparam>
public State GetState(long stateId)
```

Az **állapot** objektum a következő módszerekkel rendelkezik:

```csharp
/// <summary>
/// Set the status of the state object to commit
/// </summary>
public void Commit(bool simpleMode = true);

/// <summary>
/// Set the status of the state object to exit
/// </summary>
public void Abort();

/// <summary>
/// Put an attribute value under the given key
/// </summary>
/// <param name="key">Key</param>
/// <param name="attribute">State attribute</param>
    public void PutAttribute<T>(string key, T attribute);

/// <summary>
/// Get the attribute value associated with the given key
/// </summary>
/// <param name="key">Key</param>
/// <returns>State attribute</returns>
    public T GetAttribute<T>(string key);
```

Ha a **simpleMode** értéke **true (igaz**), a **commit** metódus törli a ZooKeeper-ben a megfelelő ZNode. Ellenkező esetben a metódus törli az aktuális ZNode, és hozzáadja az új csomópontot a véglegesített \_ elérési úthoz.

### <a name="scpruntime"></a>SCPRuntime

A **SCPRuntime** osztály a következő két módszert biztosítja:

```csharp
public static void Initialize();

public static void LaunchPlugin(newSCPPlugin createDelegate);  
```

Az **inicializálási** módszer inicializálja a szolgáltatáskapcsolódási pont futtatókörnyezetének környezetét. Ebben a módszerben a C# folyamat a Java-oldalhoz csatlakozik a konfigurációs paraméterek és a topológiai környezet beszerzéséhez.

Az **LaunchPlugin** metódus elindítja az üzenetfeldolgozási hurkot. Ebben a hurokban a C# beépülő modul a Java-oldalról fogad üzeneteket. Ezek az üzenetek rekordok és vezérlési jeleket tartalmaznak. A beépülő modul ezután feldolgozza az üzeneteket, például a kód által megadott Interface metódus meghívásával.

A **LaunchPlugin** bemeneti paramétere a delegált. A metódus olyan objektumot adhat vissza, amely megvalósítja a **ISCPSpout**, a **ISCPBolt**, a **ISCPTxSpout** vagy a **ISCPBatchBolt** felületet.

```csharp
public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary<string, Object> parms);
```

A **ISCPBatchBolt** egy **StormTxAttempt** objektumot is beszerezhet a *Paraméterek* paraméterből, és megítélheti, hogy a kísérlet egy újrajátszott kísérlet. Az újrapróbálkozási kísérletek keresése gyakran történik a véglegesítő Bolton. A cikk későbbi részében a HelloWorldTx példa azt mutatja be, hogy ez az ellenőrzési szolgáltatás.

Az SCP beépülő modulok általában két módban futtathatók: helyi tesztelési mód és normál mód.

#### <a name="local-test-mode"></a>Helyi tesztelési mód

Ebben a módban a C#-kódban lévő SCP-bővítmények a Visual Studióban futnak a fejlesztési fázisban. Ebben a módban a **ILocalContext** felületet használhatja. Az interfész olyan metódusokat biztosít, amelyekkel a kibocsátott rekordok helyi fájlokra szerializálhatja, és visszaolvashatja őket a RAM-ba.

```csharp
public interface ILocalContext
{
    List<SCPTuple> RecvFromMsgQueue();
    void WriteMsgQueueToFile(string filepath, bool append = false);  
    void ReadFromFileToMsgQueue(string filepath);
}
```

#### <a name="regular-mode"></a>Normál mód

Ebben a módban a Storm Java-folyamat futtatja az SCP beépülő modulokat. Íme egy példa:

```csharp
namespace Scp.App.HelloWorld
{
public class Generator : ISCPSpout
{
    … …
    public static Generator Get(Context ctx, Dictionary<string, Object> parms)
    {
    return new Generator(ctx);
    }
}

class HelloWorld
{
    static void Main(string[] args)
    {
    /* Setting the environment variable here can change the log file name */
    System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "HelloWorld");

    SCPRuntime.Initialize();
    SCPRuntime.LaunchPlugin(new newSCPPlugin(Generator.Get));
    }
}
}
```

## <a name="topology-specification-language"></a>Topológia specifikációjának nyelve

Az SCP-topológia specifikációja egy tartományi-specifikus nyelv (DSL), amely az SCP-topológiák leírására és konfigurálására szolgál. Ez a [Storm CLOJURE DSL](https://storm.incubator.apache.org/documentation/Clojure-DSL.html) -re épül, és az scp-vel bővül.

A **runSpec** parancs használatával a topológiai specifikációkat közvetlenül a Storm-fürthöz is elküldheti.

A SCP.NET a következő függvényeket adta hozzá a tranzakciós topológiák definiálásához:

| Új függvény | Paraméterek | Description |
| --- | --- | --- |
| **TX – topolopy** |*topológia – név*<br />*kiöntő – Térkép*<br />*bolt – Térkép* |Egy tranzakciós topológiát határoz meg a topológia nevével, a kiöntő definíciók leképezésével és a csavarok definíciós térképével. |
| **SCP – TX-kiöntő** |*exec-név*<br />*args*<br />*mezők* |Egy tranzakciós kiöntőt határoz meg. A függvény futtatja az *exec-Name* és az *argumentumok* által megadott alkalmazást.<br /><br />A *Fields* paraméter határozza meg a kiöntő kimeneti mezőit. |
| **SCP-TX-batch-bolt** |*exec-név*<br />*args*<br />*mezők* |Egy tranzakciós batch-csavart definiál. A függvény futtatja az *exec-Name* és az *argumentumok* által megadott alkalmazást.<br /><br />A *Fields* paraméter határozza meg a bolt kimeneti mezőit. |
| **SCP-TX-commit-bolt** |*exec-név*<br />*args*<br />*mezők* |Tranzakciós véglegesítő boltot határoz meg. A függvény futtatja az *exec-Name* és az *argumentumok* által megadott alkalmazást.<br /><br />A *Fields* paraméter határozza meg a bolt kimeneti mezőit. |
| **nontx – topológia** |*topológia – név*<br />*kiöntő – Térkép*<br />*bolt – Térkép* |A nem tranzakciós topológiát definiálja a topológia nevével, a kiöntő definíciók leképezésével és a csavarok definíciós térképével. |
| **SCP – kiöntő** |*exec-név*<br />*args*<br />*mezők*<br />*paraméterek* |Nem tranzakciós kiöntőt határoz meg. A függvény futtatja az *exec-Name* és az *argumentumok* által megadott alkalmazást.<br /><br />A *Fields* paraméter határozza meg a kiöntő kimeneti mezőit.<br /><br />A *Parameters* paraméter megadása nem kötelező. Használatával olyan paramétereket adhat meg, mint például a "nem tranzakciós. ACK. enabled". |
| **SCP – bolt** |*exec-név*<br />*args*<br />*mezők*<br />*paraméterek* |Nem tranzakciós csavar definiálása. A függvény futtatja az *exec-Name* és az *argumentumok* által megadott alkalmazást.<br /><br />A *Fields* paraméter határozza meg a bolt kimeneti mezőit.<br /><br />A *Parameters* paraméter megadása nem kötelező. Használatával olyan paramétereket adhat meg, mint például a "nem tranzakciós. ACK. enabled". |

A SCP.NET a következő kulcsszavakat definiálja:

| Kulcsszó | Description |
| --- | --- |
| **: név** |A topológia neve |
| **: topológia** |Az előző táblázat függvényeit használó topológia és beépített függvények |
| **:p** |Az egyes kiöntő vagy csavarok párhuzamossági mutatója |
| **: konfiguráció** |Meghatározza, hogy vannak-e paraméterek konfigurálva, vagy a meglévők frissítése |
| **: séma** |A stream sémája |

A SCP.NET ezeket a gyakran használt paramétereket is meghatározza:

| Paraméter | Leírás |
| --- | --- |
| "plugin.name" |A C# beépülő modul. exe fájljának neve |
| "beépülő modul. ARG" |A beépülő modul argumentumai |
| "output. Schema" |A kimeneti séma |
| "nem tranzakciós. ACK. enabled" |Azt jelzi, hogy engedélyezve van-e a nyugtázás a nem tranzakciós topológia számára |

A **runSpec** parancs a BITS szolgáltatással együtt települ. A parancs használata a következő:

```csharp
.\bin\runSpec.cmd
usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target
```

Az *erőforrás-dir* paraméter nem kötelező. Akkor kell megadni, ha C#-alkalmazást szeretne csatlakoztatni. A megadott könyvtár tartalmazza az alkalmazást, a függőségeket és a konfigurációkat.

A *osztályútvonal* paraméter szintén nem kötelező. A Java-osztályútvonal adja meg, ha a specifikációs fájl Java kiöntőt vagy boltot tartalmaz.

## <a name="miscellaneous-features"></a>Egyéb funkciók

### <a name="input-and-output-schema-declarations"></a>Bemeneti és kimeneti séma deklarációi

A C#-folyamatok rekordok bocsáthat ki. Ehhez a platform a rekordok-t **bájt []** objektumba szerializálja, és átviszi az objektumokat a Java-oldalra. A Storm ezután továbbítja ezeket a rekordok a célokhoz.

Az alsóbb rétegbeli összetevőkben a C#-folyamatok a Java oldalról kapják vissza a rekordok, és a platform eredeti típusaira alakítják át őket. Ezeket a műveleteket a platform elrejti.

A szerializálás és a deszerializálás támogatásához a kódnak deklarálnia kell a bemenet és a kimenet sémáját. A séma szótárként van definiálva. Az adatfolyam-azonosító a szótár kulcsa. A kulcs értéke az oszlopok típusai. Egy összetevő több streamet is deklarálhat.

```csharp
public class ComponentStreamSchema
{
    public Dictionary<string, List<Type>> InputStreamSchema { get; set; }
    public Dictionary<string, List<Type>> OutputStreamSchema { get; set; }
    public ComponentStreamSchema(Dictionary<string, List<Type>> input, Dictionary<string, List<Type>> output)
    {
        InputStreamSchema = input;
        OutputStreamSchema = output;
    }
}
```

A rendszer a következő függvényt adja hozzá egy **környezeti** objektumhoz:

```csharp
public void DeclareComponentSchema(ComponentStreamSchema schema)
```

A fejlesztőknek biztosítaniuk kell, hogy a kibocsátott rekordok engedelmeskednek a streamhez definiált sémának. Ellenkező esetben a rendszer futásidejű kivételt fog kiváltani.

### <a name="multistream-support"></a>Többadatfolyamos támogatás

A SZOLGÁLTATÁSKAPCSOLÓDÁSI pont lehetővé teszi, hogy a kód egyszerre több különböző streamből is kibocsát vagy fogadását. A **környezeti** objektum ezt a támogatást tükrözi a **kibocsátási** metódus opcionális stream ID paramétereként.

A SCP.NET **környezeti** objektum két metódust adott hozzá. Egy vagy több rekordok bocsátanak ki egy adott streambe. A *streamazonosítója* paraméter egy karakterlánc. Az értékének azonosnak kell lennie a C#-kódban és a topológia-definíciós specifikációban.

```csharp
/* Emit tuple to the specific stream. */
public abstract void Emit(string streamId, List<object> values);

/* for nontransactional spout only */
public abstract void Emit(string streamId, List<object> values, long seqId);
```

A nem létező adatfolyamok kibocsátása futásidejű kivételeket okoz.

### <a name="fields-grouping"></a>Mezők csoportosítása

A Storm-csoportosítás beépített mezői nem működnek megfelelően a SCP.NET-ben. A Java-proxy oldalon az összes mező adattípusa valójában **bájt []**. A csoportosításhoz a következő mezők a **bájt []** objektum kivonatoló kódját használják. A kivonatoló kód az objektum címe a RAM-ban. Így a csoportosítás nem lesz megfelelő a több bájtos objektumok esetében, amelyek ugyanazt a tartalmat használják, de nem ugyanaz a címe.

A SCP.NET egy testreszabott csoportosítási módszert hoz létre, és a **[]** objektum tartalmát használja a csoportosítás végrehajtásához. A specifikációs fájlban a szintaxis a következő példához hasonlóan néz ki:

```csharp
(bolt-spec
    {
        "spout_test" (scp-field-group :non-tx [0,1])
    }
    …
)
```

Az előző specifikációs fájlban:

* `scp-field-group` Megadja, hogy a csoportosítás az SCP által megvalósított testreszabott csoportosítási mező.
* `:tx` vagy `:non-tx` azt adja meg, hogy a topológia tranzakciós-e. Ezekre az adatokra azért van szükség, mert a kezdő index eltér a tranzakciós és a nem tranzakciós topológiák között.
* `[0,1]` nullával kezdődő mezőértékeket tartalmazó kivonatoló készletet ad meg.

### <a name="hybrid-topology"></a>Hibrid topológia

A natív Storm Code Java nyelven íródott. A SCP.NET továbbfejlesztett Storm segítségével C#-kódot írhat az üzleti logikájának kezeléséhez. A SCP.NET azonban támogatja a hibrid topológiákat is, amelyek nem csak C# kiöntőt/csavarokat tartalmaznak, hanem Java-kiöntő/-boltokat is.

### <a name="specify-java-spoutbolt-in-a-specification-file"></a>Java kiöntő/bolt megadása egy specifikációs fájlban

Egy specifikációs fájlban **SCP-kiöntő** és **SCP-bolt** is használható a Java kiöntő és a csavarok megadásához. Bemutatunk egy példát:

```csharp
(spout-spec 
  (microsoft.scp.example.HybridTopology.Generator.)
  :p 1)
```

Itt `microsoft.scp.example.HybridTopology.Generator` látható a Java kiöntő osztály neve.

### <a name="specify-the-java-classpath-in-a-runspec-command"></a>A Java-osztályútvonal meghatározása egy runSpec-parancsban

Ha Java kiöntőt vagy csavarokat tartalmazó topológiát szeretne beküldeni, először állítsa össze őket a JAR-fájlok létrehozásához. Ezután adja meg azt a Java-osztályútvonal, amely a topológia beküldésekor a JAR-fájlokat tartalmazza. Bemutatunk egy példát:

```csharp
bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*
```

Itt a a `examples\HybridTopology\java\target\` Java kiöntő/bolt jar-fájlt tartalmazó mappa.

### <a name="serialization-and-deserialization-between-java-and-c"></a>A Java és a C közötti szerializálás és deszerializálás #

Az SCP-összetevők a javát és a C#-oldalt is tartalmazzák. A Java-kiöntő/-csavarokkal való kommunikációhoz, a szerializáláshoz és a deszerializáláshoz a Java oldal és a C# oldal között kell történnie, ahogyan az a következő ábrán látható:

![Az SCP-összetevőnek küldött Java-összetevők ábrája, amelyet aztán egy másik Java-összetevőnek küld.](./media/apache-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png)

#### <a name="serialization-in-the-java-side-and-deserialization-in-the-c-side"></a>Szerializálás a Java oldalon és a deszerializálás a C# oldalon

Először adja meg az alapértelmezett implementációt a szerializáláshoz a Java oldalon és a deszerializálás a C# oldalon.

A Java-oldal szerializálási módszerének megadása egy specifikációs fájlban.

```csharp
(scp-bolt
    {
        "plugin.name" "HybridTopology.exe"
        "plugin.args" ["displayer"]
        "output.schema" {}
        "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
    })
```

Adja meg a C#-oldal deszerializálási módszerét a C#-kódban.

```csharp
Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
inputSchema.Add("default", new List<Type>() { typeof(Person) });
this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());
```  

Ha az adattípus nem túl összetett, az alapértelmezett implementációnak a legtöbb esetet kezelnie kell. Az alábbi esetekben lehet saját implementációt csatlakoztatni:

* Az adattípusa túl összetett az alapértelmezett implementációhoz.
* Az alapértelmezett implementáció teljesítménye nem felel meg a követelményeknek.

A Java oldalon a szerializálási felület a következőképpen van definiálva:

```csharp
public interface ICustomizedInteropJavaSerializer {
    public void prepare(String[] args);
    public List<ByteBuffer> serialize(List<Object> objectList);
}
```

A C# oldal deszerializálási felülete a következőképpen van definiálva:

```csharp
public interface ICustomizedInteropCSharpDeserializer
{
    List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
}
```

#### <a name="serialization-in-the-c-side-and-deserialization-in-the-java-side"></a>Szerializálás a C# oldalon és a deszerializálás a Java oldalon

Adja meg a C#-oldal szerializálási módszerét a C#-kódban.

```csharp
this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer()); 
```

A Java-oldal deszerializálási módszerének megadása egy specifikációs fájlban.

```csharp
(scp-spout
   {
     "plugin.name" "HybridTopology.exe"
     "plugin.args" ["generator"]
     "output.schema" {"default" ["person"]}
     "customized.java.deserializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" "microsoft.scp.example.HybridTopology.Person"]
   }
)
```

Itt a a `"microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer"` deszerializáló neve, és az a célként megadott osztály, amelybe `"microsoft.scp.example.HybridTopology.Person"` a rendszer deszerializálja az adathalmazt.

A C# szerializáló és a Java deszerializáló saját implementációját is csatlakoztathatja.

Ez a kód a C#-szerializáló felülete:

```csharp
public interface ICustomizedInteropCSharpSerializer
{
    List<byte[]> Serialize(List<object> dataList);
}
```

Ez a kód a Java deszerializáló felülete:

```csharp
public interface ICustomizedInteropJavaDeserializer {
    public void prepare(String[] targetClassNames);
    public List<Object> Deserialize(List<ByteBuffer> dataList);
}
```

## <a name="scp-host-mode"></a>SCP-gazdagép üzemmód

Ebben a módban lefordíthatja a kódot DLL-ként, és az SCP által biztosított SCPHost.exe használhatja a topológia elküldéséhez. Egy specifikációs fájl a következő kódrészlethez hasonlít:

```csharp
(scp-spout
  {
    "plugin.name" "SCPHost.exe"
    "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
    "output.schema" {"default" ["sentence"]}
  })
```

Itt `"plugin.name"` van megadva, `"SCPHost.exe"` amelyet az scp SDK biztosít. A SCPHost.exe három paramétert fogad el a következő sorrendben:

1. A DLL neve, amely `"HelloWorld.dll"` ebben a példában található.
1. Az osztály neve, amely ebben a `"Scp.App.HelloWorld.Generator"` példában szerepel.
1. Egy nyilvános statikus metódus neve, amely meghívható a **ISCPPlugin** egy példányának beszerzéséhez.

Gazdagép módban a kód lefordítása DLL-ként a SZOLGÁLTATÁSKAPCSOLÓDÁSI pont platformjának meghívásához. Mivel a platform a teljes feldolgozási logika teljes körű felügyeletét teszi elérhetővé, javasoljuk, hogy a topológiát SCP-gazdagép módban küldje el. Így egyszerűbbé válik a fejlesztési élmény. Emellett nagyobb rugalmasságot és jobb visszamenőleges kompatibilitást biztosít a későbbi kiadásokhoz.

## <a name="scp-programming-examples"></a>SCP-programozási példák

### <a name="helloworld"></a>HelloWorld

A következő egyszerű HelloWorld példa a SCP.NET ízét mutatja be. Nem tranzakciós topológiát használ egy **generátor** nevű kifolyóval, és két boltot, a **Splittert** és a **számlálót**. A kiöntő **generátor** véletlenszerűen generál mondatokat, és kibocsátja ezeket a mondatokat az **elosztóra**. Az **elválasztói** csavar a mondatokat szavakra osztja szét, és kibocsátja ezeket a szavakat a **Counter** boltba. A **számláló** az egyes szavak előfordulásának rögzítéséhez szótárt használ.

Ez a példa két specifikációs fájlt tartalmaz: HelloWorld. spec és HelloWorld \_ EnableAck. spec. A C#-kód alapján megállapítható, hogy engedélyezve van-e a nyugtázás, ha a `pluginConf` Java-oldalról beolvassa az objektumot.

```csharp
/* demo how to get pluginConf info */
if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
{
    enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
}
Context.Logger.Info("enableAck: {0}", enableAck);
```

Ha az igazolás engedélyezve van a kifolyóban, a szótár gyorsítótárazza a nem nyugtázott rekordok. Ha a `Fail` hívása megtörtént, a rendszer visszajátssza a meghiúsult rekordot.

```csharp
public void Fail(long seqId, Dictionary<string, Object> parms)
{
    Context.Logger.Info("Fail, seqId: {0}", seqId);
    if (cachedTuples.ContainsKey(seqId))
    {
        /* get the cached tuple */
        string sentence = cachedTuples[seqId];

        /* replay the failed tuple */
        Context.Logger.Info("Re-Emit: {0}, seqId: {1}", sentence, seqId);
        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), seqId);
    }
    else
    {
        Context.Logger.Warn("Fail(), can't find cached tuple for seqId {0}!", seqId);
    }
}
```

### <a name="helloworldtx"></a>HelloWorldTx

A következő HelloWorldTx példa bemutatja a tranzakciós topológia megvalósítását. A példában egy **generátor** nevű kiöntő, egy **részleges darabszámú** batch-bolt és egy **Count-Sum** nevű véglegesítő bolt szerepel. A példa három meglévő szövegfájlt is tartalmaz: DataSource0.txt, DataSource1.txt és DataSource2.txt.

Az egyes tranzakciókban a kiöntő **generátor** véletlenszerűen kiválasztja a meglévő három fájlból származó két fájlt, és kibocsátja a két fájlnevet a **részleges számú** boltba. A **részleges számú** bolt:

1. Beolvas egy fájlnevet a kapott rekordból.
1. Megnyitja a megfelelő fájlt.
1. Megszámolja a fájlban lévő szavak számát.
1. Kibocsátja a szavak darabszámát a **Count-Sum** boltba.

A **Count összegű** bolt összefoglalja a teljes darabszámot.

Ha pontosan egyszer szeretné elérni a szemantikai műveletet, a **Count-Sum** véglegesítő boltnak meg kell ítélnie, hogy egy visszajátszott tranzakcióról van-e szó. Ebben a példában a következő statikus tag változóval rendelkezik:

```csharp
public static long lastCommittedTxId = -1; 
```

**ISCPBatchBolt** -példány létrehozásakor az objektum értéke a bemeneti paraméterekből lesz lekérdezve `txAttempt` .

```csharp
public static CountSum Get(Context ctx, Dictionary<string, Object> parms)
{
    /* for transactional topology, we can get txAttempt from the input parms */
    if (parms.ContainsKey(Constants.STORM_TX_ATTEMPT))
    {
        StormTxAttempt txAttempt = (StormTxAttempt)parms[Constants.STORM_TX_ATTEMPT];
        return new CountSum(ctx, txAttempt);
    }
    else
    {
        throw new Exception("null txAttempt");
    }
}
```

Ha a `FinishBatch` hívása megtörtént, `lastCommittedTxId` frissül, ha nem egy újrajátszott tranzakció.

```csharp
public void FinishBatch(Dictionary<string, Object> parms)
{
    /* judge whether it is a replayed transaction */
    bool replay = (this.txAttempt.TxId <= lastCommittedTxId);

    if (!replay)
    {
        /* If it is not replayed, update the totalCount and lastCommittedTxId value */
        totalCount = totalCount + this.count;
        lastCommittedTxId = this.txAttempt.TxId;
    }
    … …
}
```

### <a name="hybridtopology"></a>HybridTopology

Ez a topológia egy Java kiöntőt és egy C#-csavart tartalmaz. Az SCP platform által biztosított alapértelmezett szerializálási és deszerializálási implementációt használja. Tekintse meg a HybridTopology. spec fájlt a példák \\ HybridTopology mappában a specifikációs fájl részleteihez. Lásd még: SubmitTopology.bat a Java-osztályútvonal megadásához.

### <a name="scphostdemo"></a>SCPHostDemo

Ez a példa lényegében ugyanaz, mint a HelloWorld. Az egyetlen különbség, hogy a kód DLL-ként van lefordítva, és a topológia SCPHost.exe használatával van elküldve. Részletesebb magyarázatért tekintse meg az SCP-gazdagép üzemmódját ismertető szakaszt.

## <a name="next-steps"></a>Következő lépések

Az SCP használatával létrehozott Apache Storm topológiák példáit a következő cikkekben találja:

* [C#-topológiák fejlesztése a Visual Studio használatával történő HDInsight Apache Stormhoz](apache-storm-develop-csharp-visual-studio-topology.md)
* [Események feldolgozása az Azure Event Hubs és az Apache Storm on HDInsight](apache-storm-develop-csharp-event-hub-topology.md)
* [Jármű-érzékelő adatainak feldolgozása a Event Hubs Apache Storm használatával a HDInsight](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [Kinyerési, átalakítási és betöltési (ETL) az Azure Event Hubs az Apache HBase](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)
