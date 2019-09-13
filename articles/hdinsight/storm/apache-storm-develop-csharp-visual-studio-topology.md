---
title: Apache Storm-topológiák a Visual Studióval és C# az Azure HDInsight
description: Megtudhatja, hogyan hozhat létre Storm C#-topológiákat a alkalmazásban. Hozzon létre egy egyszerű Word Count-topológiát a Visual Studióban a Hadoop Tools for Visual Studio használatával.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 11/27/2017
ROBOTS: NOINDEX
ms.openlocfilehash: e6f6ba131a4fb5dd31f113afd2b6de2d65aeaea0
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70915162"
---
# <a name="develop-c-topologies-for-apache-storm-by-using-the-data-lake-tools-for-visual-studio"></a>Apache Storm C# -topológiák fejlesztése a Visual studióhoz készült Data Lake Tools használatával

Megtudhatja, hogyan C# hozhat létre Apache Storm topológiát a Visual studióhoz készült Azure Data Lake (Apache Hadoop) eszközökkel. Ez a dokumentum végigvezeti a Storm-projekt létrehozásának folyamatán a Visual Studióban, helyi tesztelésen és az Azure HDInsight-fürtön való üzembe helyezésen Apache Storm.

Azt is megtudhatja, hogyan hozhat létre és Java C# -összetevőket használó hibrid topológiákat.

> [!NOTE]  
> Habár a jelen dokumentumban szereplő lépések a Visual Studióval rendelkező Windows-fejlesztési környezetre támaszkodnak, a lefordított projekt elküldhető egy Linux-vagy Windows-alapú HDInsight-fürtre. Csak a 2016. október 28. után létrehozott Linux-alapú fürtök támogatják a SCP.NET-topológiákat.

Ha Linux- C# alapú fürttel kíván topológiát használni, frissítenie kell a projekt által a 0.10.0.6 vagy újabb verzióra használt Microsoft. scp. net. SDK NuGet-csomagot. A csomag verziójának a HDInsightban telepített Storm főverziójával is egyeznie kell.

| HDInsight verziója | Apache Storm verziója | SCP.NET verziója | Alapértelmezett monó-verzió |
|:-----------------:|:-------------:|:---------------:|:--------------------:|
| 3.3 |0,10. x |0.10.x.x</br>(csak Windows-alapú HDInsight) | NA |
| 3.4 | 0.10.0. x | 0.10.0. x | 3.2.8 |
| 3.5 | 1.0.2.x | 1.0.0.x | 4.2.1 |
| 3.6 | 1.1.0.x | 1.0.0.x | 4.2.8 |

> [!IMPORTANT]  
> A Linux-alapú fürtök C#-topológiáinak a .NET 4.5-öt kell használnia, és a Mono segítségével futhatnak a HDInsight-fürtön. A lehetséges inkompatibilitások esetében a [mono kompatibilitás](https://www.mono-project.com/docs/about-mono/compatibility/) ellenőrzése.

## <a name="install-visual-studio"></a>A Visual Studio telepítése

A SCP.NET- C# ben a Visual Studio következő verzióival hozhat létre topológiákat:

* Visual Studio 2012 a 4. frissítéssel

* Visual Studio 2013 a 4. frissítéssel vagy a [Visual studio 2013 Közösséggel](https://go.microsoft.com/fwlink/?LinkId=517284)

* Visual Studio 2015 vagy [Visual studio 2015 Közösség](https://go.microsoft.com/fwlink/?LinkId=532606)

* Visual Studio 2017 (bármely kiadás)

## <a name="install-data-lake-tools-for-visual-studio"></a>A Visual studióhoz készült Data Lake-eszközök telepítése

A Visual studióhoz készült Data Lake Tools telepítéséhez kövesse az Ismerkedés a [Visual studio Data Lake Tools használatával](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)című témakör lépéseit.

## <a name="install-java"></a>Java telepítése

Ha a Visual studióból küld el egy Storm-topológiát, a SCP.NET létrehoz egy zip-fájlt, amely tartalmazza a topológiát és a függőségeket. A Java a zip-fájlok létrehozásához használatos, mert olyan formátumot használ, amely a Linux-alapú fürtökkel kompatibilis.

1. Telepítse a Java Developer Kit (JDK) 7-es vagy újabb verzióját a fejlesztői környezetbe. Az Oracle JDK-t az [Oracle](https://aka.ms/azure-jdks)-ből szerezheti be. [Más Java-disztribúciókat](https://openjdk.java.net/)is használhat.

2. A `JAVA_HOME` környezeti változónak a Java-t tartalmazó könyvtárra kell mutatnia.

3. A `PATH` környezeti változónak tartalmaznia kell `%JAVA_HOME%\bin` a könyvtárat.

A következő C# konzol alkalmazással ellenőrizheti, hogy a Java és a JDK helyesen van-e telepítve:

```csharp
using System;
using System.IO;
namespace ConsoleApplication2
{
   class Program
   {
       static void Main(string[] args)
       {
           string javaHome = Environment.GetEnvironmentVariable("JAVA_HOME");
           if (!string.IsNullOrEmpty(javaHome))
           {
               string jarExe = Path.Combine(javaHome + @"\bin", "jar.exe");
               if (File.Exists(jarExe))
               {
                   Console.WriteLine("JAVA Is Installed properly");
                    return;
               }
               else
               {
                   Console.WriteLine("A valid JAVA JDK is not found. Looks like JRE is installed instead of JDK.");
               }
           }
           else
           {
             Console.WriteLine("A valid JAVA JDK is not found. JAVA_HOME environment variable is not set.");
           }
       }  
   }
}
```

## <a name="apache-storm-templates"></a>Apache Storm sablonok

A Visual studióhoz készült Data Lake Tools a következő sablonokat biztosítja:

| Projekttípus | Bemutatja |
| --- | --- |
| Storm-alkalmazás |Üres Storm-topológiai projekt. |
| Storm Azure SQL-író minta |Írás a Azure SQL Databaseba. |
| Storm Azure Cosmos DB olvasó minta |Olvasás a Azure Cosmos DBról. |
| Storm Azure Cosmos DB-író minta |Írás a Azure Cosmos DBba. |
| Storm EventHub-olvasó minta |Az Azure Event Hubs beolvasása. |
| Storm EventHub-író minta |Írás az Azure Event Hubsba. |
| Storm HBase-olvasó minta |A HDInsight-fürtök HBase olvasása. |
| Storm HBase-író minta |Írás a HBase a HDInsight-fürtökön. |
| Storm Hybrid Sample |Java-összetevők használata. |
| Storm-minta |Egy alapszintű szavas topológia. |

> [!WARNING]  
> Nem minden sablon működik a Linux-alapú HDInsight. Előfordulhat, hogy a sablonok által használt NuGet-csomagok nem kompatibilisek a Mono használatával. A lehetséges problémák azonosításához keresse meg a [mono-kompatibilitási](https://www.mono-project.com/docs/about-mono/compatibility/) dokumentumot, és használja a [.net-hordozhatósági elemzőt](../hdinsight-hadoop-migrate-dotnet-to-linux.md#automated-portability-analysis) .

A jelen dokumentumban ismertetett lépések során az alapszintű Storm alkalmazás-projekttípus használatával hozzon létre egy topológiát.

### <a name="apache-hbase-templates-notes"></a>Apache HBase-sablonok megjegyzései

A HBase-olvasó és-író sablonok a HBase REST API, nem pedig a HBase Java API-t használják a HDInsight-fürtön lévő HBase való kommunikációhoz.

### <a name="eventhub-templates-notes"></a>EventHub-sablonok megjegyzései

> [!IMPORTANT]  
> Előfordulhat, hogy a EventHub-olvasó sablonban található Java-alapú EventHub kiöntő összetevő nem működik a Storm on HDInsight 3,5-es vagy újabb verziójával. Az összetevő frissített verziója a [githubon](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/HDI3.5/lib)érhető el.

Az ezt az összetevőt használó, például a HDInsight 3,5-as Storm-vel működő topológiát itt tekintheti meg: [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

## <a name="create-a-c-topology"></a>C# Topológia létrehozása

1. Nyissa meg a Visual studiót, válassza a **fájl** > **új**, majd a **projekt**elemet.

2. Az **új projekt** ablakban bontsa ki a **telepített** > **sablonok**csomópontot, és válassza a **Azure Data Lake**lehetőséget. A sablonok listájában válassza a **Storm Application**elemet. A képernyő alján adja meg a **WordCount** nevet az alkalmazás neveként.

    ![Képernyőfelvétel az új projekt ablakról](./media/apache-storm-develop-csharp-visual-studio-topology/new-project.png)

3. A projekt létrehozása után a következő fájlokkal kell rendelkeznie:

   * **Program.cs**: Ez a fájl határozza meg a projekt topológiáját. Alapértelmezés szerint létrejön egy kiöntő és egy boltból álló alapértelmezett topológia.

   * **Spout.cs**: Egy példa kiöntő, amely véletlenszerű számokat bocsát ki.

   * **Bolt.cs**: Egy példa, amely a kiöntő által kibocsátott számok számát tárolja.

     A projekt létrehozásakor a NuGet letölti a legújabb [SCP.NET-csomagot](https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/).

     [!INCLUDE [scp.net version important](../../../includes/hdinsight-storm-scpdotnet-version.md)]

### <a name="implement-the-spout"></a>A kiöntő implementálása

1. Nyissa meg a **Spout.cs**. A kiöntő használatával külső forrásból származó adatok olvashatók be a topológiában. A kiöntő fő összetevői a következők:

   * **NextTuple**: Felhívta a Storm, amikor a kiöntő jogosult új rekordok kibocsátására.

   * **ACK** (csak tranzakciós topológia): Kezeli a többi összetevő által kezdeményezett, a kiöntőból elküldett rekordok topológiájában megjelenő nyugtákat. Egy rekord nyugtázása lehetővé teszi, hogy a kiöntő tudja, hogy az alsóbb rétegbeli összetevők sikeresen feldolgozták.

   * **Sikertelen** művelet (csak tranzakciós topológia): Azokat a rekordok kezeli, amelyek nem képesek a topológia más összetevőinek feldolgozására. A Fail metódus implementálása lehetővé teszi a rekord újbóli kibocsátását, hogy újra feldolgozhatók legyenek.

2. Cserélje le a **kiöntő** osztály tartalmát a következő szövegre: Ez a kiöntő véletlenszerűen mondatot bocsát ki a topológiába.

    ```csharp
    private Context ctx;
    private Random r = new Random();
    string[] sentences = new string[] {
        "the cow jumped over the moon",
        "an apple a day keeps the doctor away",
        "four score and seven years ago",
        "snow white and the seven dwarfs",
        "i am at two with nature"
    };

    public Spout(Context ctx)
    {
        // Set the instance context
        this.ctx = ctx;

        Context.Logger.Info("Generator constructor called");

        // Declare Output schema
        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // The schema for the default output stream is
        // a tuple that contains a string field
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
    }

    // Get an instance of the spout
    public static Spout Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Spout(ctx);
    }

    public void NextTuple(Dictionary<string, Object> parms)
    {
        Context.Logger.Info("NextTuple enter");
        // The sentence to be emitted
        string sentence;

        // Get a random sentence
        sentence = sentences[r.Next(0, sentences.Length - 1)];
        Context.Logger.Info("Emit: {0}", sentence);
        // Emit it
        this.ctx.Emit(new Values(sentence));

        Context.Logger.Info("NextTuple exit");
    }

    public void Ack(long seqId, Dictionary<string, Object> parms)
    {
        // Only used for transactional topologies
    }

    public void Fail(long seqId, Dictionary<string, Object> parms)
    {
        // Only used for transactional topologies
    }
    ```

### <a name="implement-the-bolts"></a>A csavarok implementálása

1. Törölje a meglévő **bolt.cs** -fájlt a projektből.

2. **Megoldáskezelő**kattintson a jobb gombbal a projektre, és válassza az**új elem** **hozzáadása** > lehetőséget. A listából válassza a **Storm bolt**lehetőséget, majd adja meg a **Splitter.cs** nevet. Ismételje meg ezt a folyamatot egy **Counter.cs**nevű második bolt létrehozásához.

   * **Splitter.cs**: Egy olyan csavart valósít meg, amely a mondatokat egyedi szavakkal osztja szét, és új szavakat bocsát ki.

   * **Counter.cs**: Egy olyan csavart valósít meg, amely megszámolja az egyes szavakat, és kibocsátja a szavak új adatfolyamát, valamint az egyes szavak darabszámát.

     > [!NOTE]  
     > Ezek a csavarok a streamek olvasására és írására szolgálnak, de egy olyan adatforrást is használhat, amely egy adatbázissal vagy szolgáltatással kommunikál.

3. Nyissa meg a **Splitter.cs**. Alapértelmezés szerint csak egy metódussal rendelkezik: **Végrehajtás**. Az EXECUTE metódus akkor lesz meghívva, amikor a bolt egy rekordot kap a feldolgozáshoz. Itt elolvashatja és feldolgozhatja a bejövő rekordok, és kibocsáthatja a kimenő rekordok.

4. Cserélje le az **osztó** osztály tartalmát a következő kódra:

    ```csharp
    private Context ctx;

    // Constructor
    public Splitter(Context ctx)
    {
        Context.Logger.Info("Splitter constructor called");
        this.ctx = ctx;

        // Declare Input and Output schemas
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        // Input contains a tuple with a string field (the sentence)
        inputSchema.Add("default", new List<Type>() { typeof(string) });
        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // Outbound contains a tuple with a string field (the word)
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
    }

    // Get a new instance of the bolt
    public static Splitter Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Splitter(ctx);
    }

    // Called when a new tuple is available
    public void Execute(SCPTuple tuple)
    {
        Context.Logger.Info("Execute enter");

        // Get the sentence from the tuple
        string sentence = tuple.GetString(0);
        // Split at space characters
        foreach (string word in sentence.Split(' '))
        {
            Context.Logger.Info("Emit: {0}", word);
            //Emit each word
            this.ctx.Emit(new Values(word));
        }

        Context.Logger.Info("Execute exit");
    }
    ```

5. Nyissa meg a **Counter.cs**, és cserélje le az osztály tartalmát a következő kódra:

    ```csharp
    private Context ctx;

    // Dictionary for holding words and counts
    private Dictionary<string, int> counts = new Dictionary<string, int>();

    // Constructor
    public Counter(Context ctx)
    {
        Context.Logger.Info("Counter constructor called");
        // Set instance context
        this.ctx = ctx;

        // Declare Input and Output schemas
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        // A tuple containing a string field - the word
        inputSchema.Add("default", new List<Type>() { typeof(string) });

        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // A tuple containing a string and integer field - the word and the word count
        outputSchema.Add("default", new List<Type>() { typeof(string), typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
    }

    // Get a new instance
    public static Counter Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Counter(ctx);
    }

    // Called when a new tuple is available
    public void Execute(SCPTuple tuple)
    {
        Context.Logger.Info("Execute enter");

        // Get the word from the tuple
        string word = tuple.GetString(0);
        // Do we already have an entry for the word in the dictionary?
        // If no, create one with a count of 0
        int count = counts.ContainsKey(word) ? counts[word] : 0;
        // Increment the count
        count++;
        // Update the count in the dictionary
        counts[word] = count;

        Context.Logger.Info("Emit: {0}, count: {1}", word, count);
        // Emit the word and count information
        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word, count));
        Context.Logger.Info("Execute exit");
    }
    ```

### <a name="define-the-topology"></a>A topológia definiálása

A kiöntő és a csavarok egy gráfba vannak rendezve, amely meghatározza, hogy az adatáramlás Hogyan zajlik az összetevők között. Ehhez a topológiához a gráf a következő:

![Az összetevők elrendezésének diagramja](./media/apache-storm-develop-csharp-visual-studio-topology/wordcount-topology.png)

A kiöntő kibocsátja a mondatokat, és az elosztó bolt példányaira terjeszti őket. Az osztó-csavar a mondatokat szavakba bontja, amelyek el vannak osztva a Counter-boltba.

Mivel a rendszer a számláló példányában helyileg tartja a szavak darabszámát, meg kell győződnie arról, hogy az adott szavak ugyanarra a Counter bolt-példányra kerülnek. Minden példány nyomon követi az egyes szavakat. Mivel az elosztói bolt nem tart fenn állapotot, valójában nem számít, hogy az elválasztó melyik példánya fogadja ezt a mondatot.

Nyissa meg a **program.cs**. A fontos módszer a **GetTopologyBuilder**, amely a Storm számára elküldött topológia definiálására szolgál. Cserélje le a **GetTopologyBuilder** tartalmát a következő kódra a korábban ismertetett topológia megvalósításához:

```csharp
// Create a new topology named 'WordCount'
TopologyBuilder topologyBuilder = new TopologyBuilder("WordCount" + DateTime.Now.ToString("yyyyMMddHHmmss"));

// Add the spout to the topology.
// Name the component 'sentences'
// Name the field that is emitted as 'sentence'
topologyBuilder.SetSpout(
    "sentences",
    Spout.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"sentence"}}
    },
    1);
// Add the splitter bolt to the topology.
// Name the component 'splitter'
// Name the field that is emitted 'word'
// Use suffleGrouping to distribute incoming tuples
//   from the 'sentences' spout across instances
//   of the splitter
topologyBuilder.SetBolt(
    "splitter",
    Splitter.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"word"}}
    },
    1).shuffleGrouping("sentences");

// Add the counter bolt to the topology.
// Name the component 'counter'
// Name the fields that are emitted 'word' and 'count'
// Use fieldsGrouping to ensure that tuples are routed
//   to counter instances based on the contents of field
//   position 0 (the word). This could also have been
//   List<string>(){"word"}.
//   This ensures that the word 'jumped', for example, will always
//   go to the same instance
topologyBuilder.SetBolt(
    "counter",
    Counter.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"word", "count"}}
    },
    1).fieldsGrouping("splitter", new List<int>() { 0 });

// Add topology config
topologyBuilder.SetTopologyConfig(new Dictionary<string, string>()
{
    {"topology.kryo.register","[\"[B\"]"}
});

return topologyBuilder;
```

## <a name="submit-the-topology"></a>A topológia elküldése

1. **Megoldáskezelő**kattintson a jobb gombbal a projektre, és válassza a **Küldés a Storm on HDInsight**lehetőséget.

   > [!NOTE]  
   > Ha a rendszer kéri, adja meg az Azure-előfizetéséhez tartozó hitelesítő adatokat. Ha egynél több előfizetéssel rendelkezik, jelentkezzen be a HDInsight-fürtön található Stormot tartalmazóba.

2. Válassza ki a Storm on HDInsight-fürtöt a **Storm-fürt** legördülő listából, majd válassza a **Küldés**lehetőséget. A **kimeneti** ablak segítségével figyelheti, hogy a Küldés sikeres volt-e.

3. A topológia sikeres elküldését követően meg kell jelennie a fürt **Storm-topológiáinak** . Válassza ki a **WordCount** -topológiát a listából a futó topológiával kapcsolatos információk megtekintéséhez.

   > [!NOTE]  
   > A **Storm-topológiákat** a **Server Explorerben**is megtekintheti. Bontsa ki az **Azure** > **HDInsight**csomópontot, kattintson a jobb gombbal a HDInsight-fürtön található Storm elemre, majd válassza a **Storm-topológiák megtekintése**lehetőséget.

    A topológiában található összetevők adatainak megtekintéséhez kattintson duplán az összetevőre a diagramon.

4. A topológia leállításához a **topológia összefoglaló** nézetében kattintson a **kill** (Törlés) elemre.

   > [!NOTE]  
   > A Storm-topológiák továbbra is futnak, amíg inaktiválják őket, vagy a fürtöt törölték.

## <a name="transactional-topology"></a>Tranzakciós topológia

Az előző topológia nem tranzakciós. A topológiában található összetevők nem implementálják az üzenetek ismételt lejátszásának funkcióit. A tranzakciós topológiára példaként hozzon létre egy projektet, és válassza a **Storm Sample** lehetőséget a projekt típusaként.

A tranzakciós topológiák a következő műveleteket hajtják végre az adatvisszajátszás támogatásához:

* **Metaadatok gyorsítótárazása**: A kifolyónak a kibocsátott adatokra vonatkozó metaadatokat kell tárolnia, hogy hiba esetén az adatok beolvasása és újbóli kibocsátása is megtörténjen. Mivel a minta által kibocsátott adat kicsi, az egyes rekordok nyers adatát egy szótárban tárolja a rendszer a lejátszáshoz.

* **ACK**: A topológia minden egyes boltja meghívja `this.ctx.Ack(tuple)` a nyugtát, hogy sikeresen feldolgozta a rekordot. Amikor az összes bolt elismerte a rekordot, `Ack` meghívja a kiöntő metódusát. A `Ack` metódus lehetővé teszi a kiöntő számára, hogy eltávolítsa az újrajátszás céljából gyorsítótárazott adatfájlokat.

* **Sikertelen**: Minden egyes bolt meghívja `this.ctx.Fail(tuple)` azt a jelzést, hogy egy rekord feldolgozása meghiúsult. A hiba `Fail` a kiöntő metódusára kerül, ahol a rekordot gyorsítótárazott metaadatok használatával lehet lejátszani.

* **Sorozatszám azonosítója**: Egy rekord kibocsátásakor megadhat egy egyedi sorszám-azonosítót. Ez az érték azonosítja az Ismétlési (ACK és sikertelen) adatfeldolgozási rekordot. A **Storm Sample** projekt kiöntő például a következőt használja az adatkibocsátáskor:

        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);

    Ez a kód olyan rekordot bocsát ki, amely az alapértelmezett adatfolyamra vonatkozó mondatot tartalmaz, és tartalmazza a **lastSeqId**lévő Sequence ID értéket. Ebben a példában a **lastSeqId** minden kibocsátott rekordnál növekszik.

Ahogy azt a **Storm Sample** projekt mutatja be, a konfiguráció alapján beállítható, hogy egy összetevő tranzakciós lehet-e futásidőben.

## <a name="hybrid-topology-with-c-and-java"></a>Hibrid topológia C# és Java

A Visual studióhoz Data Lake-eszközöket is használhat hibrid topológiák létrehozásához, ahol egyes összetevők C# és más a Java.

Hibrid topológia esetén például hozzon létre egy projektet, és válassza a **Storm Hybrid Sample**elemet. Ez a minta a következő fogalmakat mutatja be:

* **Java kiöntő** és  **C# bolt**: Definiálva a **HybridTopology_javaSpout_csharpBolt**.

    * A tranzakciós verzió definiálva van a **HybridTopologyTx_javaSpout_csharpBolt**-ben.

* kiöntő és **Java-bolt**: **C#** Definiálva a **HybridTopology_csharpSpout_javaBolt**.

    * A tranzakciós verzió definiálva van a **HybridTopologyTx_csharpSpout_javaBolt**-ben.

  > [!NOTE]  
  > Ez a verzió azt is bemutatja, hogyan használható a Clojure-kód egy szövegfájlban Java-összetevőként.


A projekt elküldésekor használt topológia átváltásához vigye `[Active(true)]` az utasítást a használni kívánt topológiára, mielőtt elküldené a fürtbe.

> [!NOTE]  
> A szükséges Java-fájlok a projekt részét képezik a **JavaDependency** mappában.

Hibrid topológia létrehozásakor és elküldésekor vegye figyelembe a következőket:

* A **JavaComponentConstructor** használatával hozzon létre egy Java-osztály egy példányát egy kiöntő vagy egy bolt számára.

* A **Microsoft. scp. Storm. multilang. CustomizedInteropJSONSerializer** használatával szerializálhatja az adatok Java-összetevőkből a JSON-be vagy onnan való bevezetését.

* Ha a topológiát a kiszolgálóra küldi, a **további konfigurációk** lehetőséget kell használnia a **Java-fájlok elérési útjának**megadásához. A megadott elérési útnak a Java-osztályokat tartalmazó JAR-fájlokat tartalmazó könyvtárnak kell lennie.

### <a name="azure-event-hubs"></a>Azure Event Hubs

A SCP.NET verziójának 0.9.4.203 egy új osztályt és metódust vezet be, amely kifejezetten az Event hub kiöntő (Event Hubs) által beolvasott Java-kiöntő használatát mutatja be. Ha olyan topológiát hoz létre, amely egy Event hub kiöntőt használ, használja a következő módszereket:

* **EventHubSpoutConfig** osztály: Létrehoz egy objektumot, amely tartalmazza a kiöntő összetevő konfigurációját.

* **TopologyBuilder. SetEventHubSpout** metódus: Hozzáadja az Event hub kiöntő összetevőjét a topológiához.

> [!NOTE]  
> A kiöntő által létrehozott adatszerializáláshoz továbbra is a **CustomizedInteropJSONSerializer** kell használnia.

## <a id="configurationmanager"></a>ConfigurationManager használata

Ne használja a **ConfigurationManager** , hogy lekérje a konfigurációs értékeket a bolt és a kiöntő összetevőkből. Ennek hatására a mutató nullára vált. Ehelyett a projekt konfigurációját a rendszer a Storm-topológiába helyezi át kulcs-érték párokként a topológia kontextusában. A konfigurációs értékekre támaszkodó összes összetevőnek az inicializálás során le kell kérnie azokat a környezetből.

A következő kód bemutatja, hogyan kérheti le ezeket az értékeket:

```csharp
public class MyComponent : ISCPBolt
{
    // To hold configuration information loaded from context
    Configuration configuration;
    ...
    public MyComponent(Context ctx, Dictionary<string, Object> parms)
    {
        // Save a copy of the context for this component instance
        this.ctx = ctx;
        // If it exists, load the configuration for the component
        if(parms.ContainsKey(Constants.USER_CONFIG))
        {
            this.configuration = parms[Constants.USER_CONFIG] as System.Configuration.Configuration;
        }
        // Retrieve the value of "Foo" from configuration
        var foo = this.configuration.AppSettings.Settings["Foo"].Value;
    }
    ...
}
```

Ha az összetevő egy `Get` példányának visszaadására módszert használ, győződjön meg arról, hogy a és `Dictionary<string, Object>` a `Context` paramétereket is átadja a konstruktornak. A következő példa egy alapszintű `Get` módszer, amely megfelelő módon továbbítja ezeket az értékeket:

```csharp
public static MyComponent Get(Context ctx, Dictionary<string, Object> parms)
{
    return new MyComponent(ctx, parms);
}
```

## <a name="how-to-update-scpnet"></a>A SCP.NET frissítése

A SCP.NET-támogatás legújabb kiadásai a NuGet-on keresztül frissültek. Ha új frissítés érhető el, frissítési értesítést kap. A frissítés manuális kereséséhez kövesse az alábbi lépéseket:

1. A **Megoldáskezelőben** kattintson a jobb gombbal a projektre, és válassza a **NuGet-csomagok kezelése** lehetőséget.

2. A Package Managerben válassza a **frissítések**lehetőséget. Ha van elérhető frissítés, a lista jelenik meg. A telepítéshez kattintson a **frissítés** elemre a csomag telepítéséhez.

> [!IMPORTANT]  
> Ha a projekt olyan SCP.NET korábbi verziójával lett létrehozva, amely nem használta a NuGet-et, akkor a következő lépéseket kell végrehajtania egy újabb verzióra való frissítéshez:
>
> 1. A **Megoldáskezelőben** kattintson a jobb gombbal a projektre, és válassza a **NuGet-csomagok kezelése** lehetőséget.
> 2. A **Keresés** mezőben keressen rá, majd adja hozzá a **Microsoft. scp. net. SDK** -t a projekthez.

## <a name="troubleshoot-common-issues-with-topologies"></a>A topológiákkal kapcsolatos gyakori problémák elhárítása

### <a name="null-pointer-exceptions"></a>NULL értékű mutató kivételei

Ha egy Linux-alapú C# HDInsight-fürttel rendelkező topológiát használ, a **ConfigurationManager** -t használó, a konfigurációs beállítások olvasására használt bolt és kiöntő összetevők null értékű kivételeket adhatnak vissza.

A projekt konfigurációját a rendszer a Storm-topológiába helyezi át kulcs-érték párokként a topológia kontextusában. Lekérhető az összetevők számára az inicializáláskor átadott szótár objektumból.

További információkért tekintse meg a jelen dokumentum [ConfigurationManager](#configurationmanager) című szakaszát.

### <a name="systemtypeloadexception"></a>System.TypeLoadException

Ha Linux-alapú HDInsight C# -fürttel rendelkező topológiát használ, a következő hibaüzenet jelenhet meg:

    System.TypeLoadException: Failure has occurred while loading a type.

Ez a hiba akkor fordul elő, ha olyan bináris fájlt használ, amely nem kompatibilis a Mono által támogatott .NET-verzióval.

Linux-alapú HDInsight-fürtök esetén győződjön meg arról, hogy a projekt a .NET 4,5-hez lefordított bináris fájlokat használ.

### <a name="test-a-topology-locally"></a>Topológia helyi tesztelése

Bár a topológia egyszerűen üzembe helyezhető egy fürtben, bizonyos esetekben előfordulhat, hogy helyileg kell tesztelni a topológiát. A következő lépésekkel futtathatja és tesztelheti a jelen cikkben található példa topológiát a fejlesztési környezetben.

> [!WARNING]  
> A helyi tesztelés csak az alapszintű, C#csak a-alapú topológiák esetében működik. Nem használhat helyi tesztelést olyan hibrid topológiák vagy topológiák esetében, amelyek több streamet használnak.

1. **Megoldáskezelő**kattintson a jobb gombbal a projektre, és válassza a **Tulajdonságok**lehetőséget. A projekt tulajdonságainál módosítsa a **kimeneti típust** a **Console alkalmazásba**.

    ![Képernyőkép a projekt tulajdonságairól, a kimenet típusa kiemelve](./media/apache-storm-develop-csharp-visual-studio-topology/hdi-output-type-window.png)

   > [!NOTE]
   > Ne felejtse el módosítani a **kimeneti típust** az **osztály-tárba** , mielőtt telepítené a topológiát a fürtön.

2. **Megoldáskezelő**kattintson a jobb gombbal a projektre, majd válassza az > **új elem hozzáadása elemet**. Válassza az **osztály**lehetőséget, majd adja meg az **LocalTest.cs** nevet. Végül kattintson a **Hozzáadás**gombra.

3. Nyissa meg a **LocalTest.cs**, és adja hozzá a következő **using** utasítást a felső részen:

    ```csharp
    using Microsoft.SCP;
    ```

4. Használja a következő kódot a **LocalTest** osztály tartalmáként:

    ```csharp
    // Drives the topology components
    public void RunTestCase()
    {
        // An empty dictionary for use when creating components
        Dictionary<string, Object> emptyDictionary = new Dictionary<string, object>();

        #region Test the spout
        {
            Console.WriteLine("Starting spout");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext spoutCtx = LocalContext.Get();
            // Get a new instance of the spout, using the local context
            Spout sentences = Spout.Get(spoutCtx, emptyDictionary);

            // Emit 10 tuples
            for (int i = 0; i < 10; i++)
            {
                sentences.NextTuple(emptyDictionary);
            }
            // Use LocalContext to persist the data stream to file
            spoutCtx.WriteMsgQueueToFile("sentences.txt");
            Console.WriteLine("Spout finished");
        }
        #endregion

        #region Test the splitter bolt
        {
            Console.WriteLine("Starting splitter bolt");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext splitterCtx = LocalContext.Get();
            // Get a new instance of the bolt
            Splitter splitter = Splitter.Get(splitterCtx, emptyDictionary);

            // Set the data stream to the data created by the spout
            splitterCtx.ReadFromFileToMsgQueue("sentences.txt");
            // Get a batch of tuples from the stream
            List<SCPTuple> batch = splitterCtx.RecvFromMsgQueue();
            // Process each tuple in the batch
            foreach (SCPTuple tuple in batch)
            {
                splitter.Execute(tuple);
            }
            // Use LocalContext to persist the data stream to file
            splitterCtx.WriteMsgQueueToFile("splitter.txt");
            Console.WriteLine("Splitter bolt finished");
        }
        #endregion

        #region Test the counter bolt
        {
            Console.WriteLine("Starting counter bolt");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext counterCtx = LocalContext.Get();
            // Get a new instance of the bolt
            Counter counter = Counter.Get(counterCtx, emptyDictionary);

            // Set the data stream to the data created by splitter bolt
            counterCtx.ReadFromFileToMsgQueue("splitter.txt");
            // Get a batch of tuples from the stream
            List<SCPTuple> batch = counterCtx.RecvFromMsgQueue();
            // Process each tuple in the batch
            foreach (SCPTuple tuple in batch)
            {
                counter.Execute(tuple);
            }
            // Use LocalContext to persist the data stream to file
            counterCtx.WriteMsgQueueToFile("counter.txt");
            Console.WriteLine("Counter bolt finished");
        }
        #endregion
    }
    ```

    Szánjon egy kis időt a kód megjegyzésének beolvasására. Ez a kód a **LocalContext** használatával futtatja az összetevőket a fejlesztői környezetben, és megőrzi az összetevők közötti adatfolyamot a helyi meghajtón lévő szöveges fájlok számára.

1. Nyissa meg a **program.cs**, és adja hozzá a következőt a **Main** metódushoz:

    ```csharp
    Console.WriteLine("Starting tests");
    System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-LocalTest");
    // Initialize the runtime
    SCPRuntime.Initialize();

    //If we are not running under the local context, throw an error
    if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
    {
        throw new Exception(string.Format("unexpected pluginType: {0}", Context.pluginType));
    }
    // Create test instance
    LocalTest tests = new LocalTest();
    // Run tests
    tests.RunTestCase();
    Console.WriteLine("Tests finished");
    Console.ReadKey();
    ```

2. Mentse a módosításokat, majd kattintson az **F5** gombra, **vagy válassza a hibakeresés** > **indítása** a projekt elindításához lehetőséget. A konzol ablakának meg kell jelennie, és a tesztek állapotaként kell megjelennie. Ha a **tesztek befejeződött** , nyomja le az egyik billentyűt az ablak bezárásához.

3. A **Windows Intéző** segítségével keresse meg a projektet tartalmazó könyvtárat. Példa: **C:\Users\<your_user_name>\Documents\Visual Studio 2013\Projects\WordCount\WordCount**. Ebben a könyvtárban nyissa meg a **bin**mappát, majd kattintson a **hibakeresés**elemre. A tesztek futtatásakor létrehozott szövegfájlokat a mondatok. txt, a Counter. txt és az Splitter. txt fájl tartalmazza. Nyissa meg az egyes szövegfájlokat, és vizsgálja meg az adatelemzést.

   > [!NOTE]  
   > A karakterlánc-adatok az ezekben a fájlokban lévő decimális értékek tömbje is megmaradnak. Például \[: [97 103 111]] az **osztó. txt** fájlban a szó *és*a.

> [!NOTE]  
> Ügyeljen arra, hogy a HDInsight-fürtön való üzembe helyezés előtt állítsa vissza a **projekt típusát** az **osztály-függvénytárba** .

### <a name="log-information"></a>Napló adatai

A használatával `Context.Logger`egyszerűen naplózhatja a topológiák összetevőinek adatait. A következő parancs például létrehoz egy tájékoztató naplóbejegyzést:

```csharp
Context.Logger.Info("Component started");
```

A naplózott adatok a **Hadoop szolgáltatás naplójából**tekinthetők meg, amely a **Server Explorerben**található. Bontsa ki a Storm on HDInsight-fürtön lévő bejegyzést, majd bontsa ki a **Hadoop-szolgáltatás naplóját**. Végül válassza ki a megtekinteni kívánt naplófájlt.

> [!NOTE]  
> A naplókat a fürt által használt Azure Storage-fiók tárolja. A naplók a Visual Studióban való megtekintéséhez be kell jelentkeznie a Storage-fiókot birtokló Azure-előfizetésbe.

### <a name="view-error-information"></a>Hiba információinak megtekintése

A futó topológiában történt hibák megtekintéséhez kövesse az alábbi lépéseket:

1. A **Server Explorerben**kattintson a jobb gombbal a Storm on HDInsight-fürtre, és válassza a **Storm-topológiák megtekintése**lehetőséget.

2. A **kiöntő** és a **boltokban**az **utolsó hiba** oszlop az utolsó hibával kapcsolatos információkat tartalmazza.

3. Válassza ki azt az összetevő **kiöntő azonosítóját** vagy **azonosítóját** , amelynél a hiba szerepel. A megjelenő Részletek lapon további hibaüzenetek jelennek meg az oldal alján található **hibák** szakaszban.

4. Ha további információt szeretne kapni, válasszon ki egy **portot** az oldal **végrehajtók** szakaszából, hogy az elmúlt néhány percben megjelenjen a Storm Worker-napló.

### <a name="errors-submitting-topologies"></a>Hibák a topológiák elküldésekor

Ha hibát tapasztal a topológia HDInsight való elküldésekor, megtalálhatja a HDInsight-fürtön a topológiai beküldést kezelő kiszolgálóoldali összetevők naplóit. A naplók beolvasásához használja a következő parancsot egy parancssorból:

    scp sshuser@clustername-ssh.azurehdinsight.net:/var/log/hdinsight-scpwebapi/hdinsight-scpwebapi.out .

Cserélje le a __sshuser__ -t a fürthöz tartozó SSH-felhasználói fiókra. Cserélje le a __clustername__ nevet a HDInsight-fürt nevére. A és `scp` `ssh` a HDInsight használatával kapcsolatos további információkért lásd: az [SSH használata a HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

A beadványok több okból is meghiúsulnak:

* A JDK nincs telepítve, vagy nem található az elérési úton.
* A szükséges Java-függőségek nem szerepelnek a beadványban.
* Inkompatibilis függőségek.
* Ismétlődő topológia neve.

Ha a `hdinsight-scpwebapi.out` napló tartalmaz egy `FileNotFoundException`, ezt a következő feltételek okozhatják:

* A JDK nem a fejlesztési környezet elérési útjában van. Ellenőrizze, hogy a JDK telepítve van-e a fejlesztői környezetben, `%JAVA_HOME%/bin` és az elérési úton van-e.
* Hiányzik egy Java-függőség. Győződjön meg róla, hogy a beküldés részeként a szükséges. jar fájlokat is tartalmazza.

## <a name="next-steps"></a>További lépések

Az adatok Event Hubsból való feldolgozására példa: [események feldolgozása az Azure Event Hubs és a Storm on HDInsight](apache-storm-develop-csharp-event-hub-topology.md).

Példa egy C# olyan topológiára, amely több streambe osztja el a stream-adatforgalmat, lásd: [ C# Storm example](https://github.com/Blackmist/csharp-storm-example).

A topológiák létrehozásával C# kapcsolatos további információkért lásd: [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md).

Ha többet szeretne tudni a HDInsight és a HDInsight-mintákról, tekintse meg a következő dokumentumokat:

**Microsoft SCP.NET**

* [SCP programozási útmutató](apache-storm-scp-programming-guide.md)

**Apache Storm on HDInsight**

* [Topológiák üzembe helyezése és figyelése Apache Storm a HDInsight-on](apache-storm-deploy-monitor-topology.md)
* [HDInsight alatt futó Apache Storm példatopológiái](apache-storm-example-topology.md)

**Apache Hadoop a HDInsight**

* [Apache Hive használata a HDInsight Apache Hadoop használatával](../hadoop/hdinsight-use-hive.md)
* [Az Apache Pig használata a Apache Hadoop on HDInsight](../hadoop/hdinsight-use-pig.md)
* [Apache Hadoop MapReduce használata a HDInsight Apache Hadoop használatával](../hadoop/hdinsight-use-mapreduce.md)

**Apache HBase a HDInsight-on**

* [Első lépések az Apache HBase a HDInsight](../hbase/apache-hbase-tutorial-get-started-linux.md)
