---
title: A C# és a MapReduce használata a Hadoop-ben a HDInsight-Azure-ban
description: Ismerje meg, hogyan hozhat létre MapReduce-megoldásokat a C# használatával a Apache Hadoop Azure HDInsight való létrehozásához.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, seoapr2020, devx-track-csharp
ms.date: 04/28/2020
ms.openlocfilehash: 6e2758fd650547978924c25f8f3faf1e3e7194f6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101699385"
---
# <a name="use-c-with-mapreduce-streaming-on-apache-hadoop-in-hdinsight"></a>A C# és a MapReduce streaming használata Apache Hadoop HDInsight

Ismerje meg, hogyan hozhat létre MapReduce-megoldást a C# használatával a HDInsight.

A Apache Hadoop streaming lehetővé teszi, hogy parancsfájl vagy végrehajtható fájl használatával futtasson MapReduce-feladatokat. Itt a .NET használatával lehet megvalósítani a leképező és a szűkítőt a Word Count megoldásban.

## <a name="net-on-hdinsight"></a>.NET on HDInsight

A HDInsight-fürtök [mono https://mono-project.com) ](https://mono-project.com) -t használnak (.NET-alkalmazások futtatására. A HDInsight 3,6-es verziója tartalmazza a Mono 4.2.1-es verzióját. További információ a HDInsight-mel együtt használt mono verziójáról: [Apache Hadoop HDInsight-verziókkal elérhető összetevők](../hdinsight-component-versioning.md).

További információ a .NET-keretrendszer verzióival való monó kompatibilitásról: [monó kompatibilitás](https://www.mono-project.com/docs/about-mono/compatibility/).

## <a name="how-hadoop-streaming-works"></a>Az Hadoop streaming működése

A dokumentumban a folyamatos átvitelhez használt alapszintű folyamat a következő:

1. A Hadoop az adatleképezést (ebben a példában a *mapper.exe* ) a stdin-re továbbítja.
2. A Mapper dolgozza fel az adatfeldolgozást, és tabulátorral tagolt kulcs/érték párokat bocsát ki az STDOUT-ra.
3. A kimenetet a Hadoop beolvassa, majd átadja a következőnek a redukáló számára (ebben a példában a *reducer.exe* ) a stdin-ben.
4. A redukáló beolvassa a tabulátorral tagolt kulcs/érték párokat, feldolgozza az adatokat, majd tabulátorral tagolt kulcs/érték párokat bocsát ki az STDOUT-on.
5. A kimenetet a Hadoop olvassa be, és a kimeneti könyvtárba írja.

További információ a folyamatos átvitelről: [Hadoop streaming](https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html).

## <a name="prerequisites"></a>Előfeltételek

* A Visual Studióval.

* A .NET-keretrendszer 4,5-es számú C#-kódjának írásával és létrehozásával kapcsolatos ismeretek.

* Az. exe fájlok a fürtbe való feltöltésének módja. A jelen dokumentumban szereplő lépések a Visual studióhoz készült Data Lake Tools használatával töltik fel a fájlokat a fürt elsődleges tárolójába.

* Ha a PowerShellt használja, szüksége lesz az az [modulra](/powershell/azure/).

* Egy Apache Hadoop-fürt a HDInsight-on. Lásd: Ismerkedés [a HDInsight Linux rendszeren](../hadoop/apache-hadoop-linux-tutorial-get-started.md).

* A fürtök elsődleges tárolójának URI-sémája. Ez a séma `wasb://` Az Azure Storage-hoz, `abfs://` Azure Data Lake Storage Gen2 vagy `adl://` Azure Data Lake Storage Gen1hoz. Ha a biztonságos átvitel engedélyezve van az Azure Storage vagy a Data Lake Storage Gen2 számára, akkor az URI a következő lesz: `wasbs://` vagy `abfss://` .

## <a name="create-the-mapper"></a>A Mapper létrehozása

A Visual Studióban hozzon létre egy új .NET Framework Console-alkalmazást a *Mapper* nevű alkalmazásban. Használja az alábbi kódot az alkalmazáshoz:

```csharp
using System;
using System.Text.RegularExpressions;

namespace mapper
{
    class Program
    {
        static void Main(string[] args)
        {
            string line;
            //Hadoop passes data to the mapper on STDIN
            while((line = Console.ReadLine()) != null)
            {
                // We only want words, so strip out punctuation, numbers, etc.
                var onlyText = Regex.Replace(line, @"\.|;|:|,|[0-9]|'", "");
                // Split at whitespace.
                var words = Regex.Matches(onlyText, @"[\w]+");
                // Loop over the words
                foreach(var word in words)
                {
                    //Emit tab-delimited key/value pairs.
                    //In this case, a word and a count of 1.
                    Console.WriteLine("{0}\t1",word);
                }
            }
        }
    }
}
```

Az alkalmazás létrehozása után hozza létre a */bin/Debug/mapper.exe* -fájl létrehozásához a projekt könyvtárában.

## <a name="create-the-reducer"></a>A szűkítő létrehozása

A Visual Studióban hozzon létre egy *szűkítő* nevű új .NET-keretrendszer-alkalmazást. Használja az alábbi kódot az alkalmazáshoz:

```csharp
using System;
using System.Collections.Generic;

namespace reducer
{
    class Program
    {
        static void Main(string[] args)
        {
            //Dictionary for holding a count of words
            Dictionary<string, int> words = new Dictionary<string, int>();

            string line;
            //Read from STDIN
            while ((line = Console.ReadLine()) != null)
            {
                // Data from Hadoop is tab-delimited key/value pairs
                var sArr = line.Split('\t');
                // Get the word
                string word = sArr[0];
                // Get the count
                int count = Convert.ToInt32(sArr[1]);

                //Do we already have a count for the word?
                if(words.ContainsKey(word))
                {
                    //If so, increment the count
                    words[word] += count;
                } else
                {
                    //Add the key to the collection
                    words.Add(word, count);
                }
            }
            //Finally, emit each word and count
            foreach (var word in words)
            {
                //Emit tab-delimited key/value pairs.
                //In this case, a word and a count of 1.
                Console.WriteLine("{0}\t{1}", word.Key, word.Value);
            }
        }
    }
}
```

Az alkalmazás létrehozása után hozza létre a */bin/Debug/reducer.exe* -fájl létrehozásához a projekt könyvtárában.

## <a name="upload-to-storage"></a>Feltöltés tárolóba

Ezután fel kell töltenie a *Mapper* és a *redukáló* alkalmazásokat a HDInsight-tárolóba.

1. A Visual Studióban válassza a  >  **Server Explorer** megtekintése lehetőséget.

1. Kattintson a jobb gombbal az **Azure** elemre, válassza a **Kapcsolódás Microsoft Azure előfizetéshez...** lehetőséget, és fejezze be a bejelentkezési folyamatot.

1. Bontsa ki azt a HDInsight-fürtöt, amelyre telepíteni kívánja az alkalmazást. Megjelenik egy bejegyzés a szöveggel **(alapértelmezett Storage-fiók)** .

   ![Storage-fiók, HDInsight-fürt, Server Explorer, Visual Studio](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-storage-account.png)

   * Ha a **(alapértelmezett Storage-fiók)** bejegyzést ki lehet bővíteni, az **Azure Storage-fiókot** használja alapértelmezett tárolóként a fürt számára. Ha meg szeretné tekinteni a fürt alapértelmezett tárolójában lévő fájlokat, bontsa ki a bejegyzést, majd kattintson duplán a **(alapértelmezett tároló)** elemre.

   * Ha az **(alapértelmezett Storage-fiók)** bejegyzést nem lehet kibontani, akkor a **Azure Data Lake Storage** a fürt alapértelmezett tárolójának fogja használni. A fürt alapértelmezett tárolójában lévő fájlok megtekintéséhez kattintson duplán az **(alapértelmezett Storage-fiók)** bejegyzésre.

1. Az. exe fájlok feltöltéséhez használja az alábbi módszerek egyikét:

    * Ha **Azure Storage-fiókot** használ, válassza a **blob feltöltése** ikont.

        ![HDInsight feltöltés ikonja Mapper, Visual Studio](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-upload-icon.png)

        Az **új fájl feltöltése** párbeszédpanel **fájlnév** területén válassza a **Tallózás** lehetőséget. A **blob feltöltése** párbeszédpanelen lépjen a *Mapper* projekt *bin\debug* mappájába, majd válassza ki a *mapper.exe* fájlt. Végül kattintson a **Megnyitás** elemre, majd az **OK gombra** a feltöltés befejezéséhez.

    * **Azure Data Lake Storage** esetében kattintson a jobb gombbal egy üresre a fájl listázása területen, majd válassza a **feltöltés** lehetőséget. Végül válassza ki a *mapper.exe* fájlt, majd kattintson a **Megnyitás** gombra.

    A *mapper.exe* feltöltésének befejezése után ismételje meg a *reducer.exe* fájl feltöltési folyamatát.

## <a name="run-a-job-using-an-ssh-session"></a>Feladatok futtatása: SSH-munkamenet használata

Az alábbi eljárás azt ismerteti, hogyan futtathat MapReduce-feladatokat SSH-munkamenet használatával:

1. A fürthöz való kapcsolódáshoz használja az [SSH-parancsot](../hdinsight-hadoop-linux-use-ssh-unix.md) . Szerkessze az alábbi parancsot az CLUSTERNAME helyére a fürt nevével, majd írja be a következő parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. A MapReduce-feladatok elindításához használja a következő parancsok egyikét:

   * Ha az alapértelmezett tároló az **Azure Storage**:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files wasbs:///mapper.exe,wasbs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

    * Ha az alapértelmezett tárterület **Data Lake Storage Gen1**:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files adl:///mapper.exe,adl:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   * Ha az alapértelmezett tárterület **Data Lake Storage Gen2**:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files abfs:///mapper.exe,abfs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   A következő lista ismerteti, hogy az egyes paraméterek és lehetőségek a következőket jelölik:

   |Paraméter | Leírás |
   |---|---|
   |Hadoop-streaming. jar|A streaming MapReduce funkciót tartalmazó jar-fájlt adja meg.|
   |– fájlok|Megadja a feladatokhoz tartozó *mapper.exe* és *reducer.exe* fájlokat. A `wasbs:///` , `adl:///` vagy `abfs:///` protokoll deklarációja, mielőtt az egyes fájlok a fürt alapértelmezett tárterületének gyökerére mutatnak.|
   |– Mapper|Megadja a leképezést megvalósító fájlt.|
   |-szűkítő|Meghatározza a szűkítőt megvalósító fájlt.|
   |– bemenet|Megadja a bemeneti adatokat.|
   |-kimenet|Megadja a kimeneti könyvtárat.|

1. A MapReduce-feladatok befejezése után a következő parancs használatával tekintheti meg az eredményeket:

   ```bash
   hdfs dfs -text /example/wordcountout/part-00000
   ```

   A következő szöveg egy példa a parancs által visszaadott adatmennyiségre:

   ```output
   you     1128
   young   38
   younger 1
   youngest        1
   your    338
   yours   4
   yourself        34
   yourselves      3
   youth   17
   ```

## <a name="run-a-job-using-powershell"></a>Feladatok futtatása: a PowerShell használatával

A következő PowerShell-szkripttel futtasson egy MapReduce-feladatot, és töltse le az eredményeket.

[!code-powershell[main](../../../powershell_scripts/hdinsight/use-csharp-mapreduce/use-csharp-mapreduce.ps1?range=5-87)]

Ez a parancsfájl kéri a fürt bejelentkezési fiókjának nevét és jelszavát, valamint a HDInsight-fürt nevét. A feladatok befejezése után a rendszer letölti a kimenetet egy *output.txt* nevű fájlba. A következő szöveg egy példa a `output.txt` fájlban található fájlokra:

```output
you     1128
young   38
younger 1
youngest        1
your    338
yours   4
yourself        34
yourselves      3
youth   17
```

## <a name="next-steps"></a>Következő lépések

* [MapReduce használata a HDInsight-on Apache Hadoop](hdinsight-use-mapreduce.md).
* [Használjon C# felhasználó által definiált függvényt Apache Hive és Apache Pig használatával](apache-hadoop-hive-pig-udf-dotnet-csharp.md).
* [Java MapReduce programok fejlesztése](apache-hadoop-develop-deploy-java-mapreduce-linux.md)