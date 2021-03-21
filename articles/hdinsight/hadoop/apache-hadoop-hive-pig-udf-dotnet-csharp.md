---
title: C#, Apache Hive & Apache Pig on Apache Hadoop – Azure HDInsight
description: Megtudhatja, hogyan használhat C# nyelvű, felhasználó által definiált függvényeket az Azure HDInsight Apache Hive és Apache Pig streaming szolgáltatásával.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 12/06/2019
ms.openlocfilehash: 97b23a2b9e3b95a5ea0efcd27d0ec185b1c456f1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946543"
---
# <a name="use-c-user-defined-functions-with-apache-hive-and-apache-pig-on-apache-hadoop-in-hdinsight"></a>Felhasználó által definiált C#-függvények használata Apache Hive és Apache Pig használatával a HDInsight-on Apache Hadoop

Megtudhatja, hogyan használhatja a HDInsight-on [Apache Hive](https://hive.apache.org) és [Apache Pig](https://pig.apache.org) használatával C# felhasználó által definiált függvényeket (UDF-ket).

> [!IMPORTANT]
> A jelen dokumentum lépései a Linux-alapú HDInsight-fürtökkel működnek. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További információ: HDInsight- [összetevő verziószámozása](../hdinsight-component-versioning.md).

A kaptár és a Pig is továbbíthat az adatfeldolgozásra külső alkalmazásoknak. Ezt a folyamatot _adatfolyamként_ nevezzük. .NET-alkalmazás használatakor a rendszer az adatok átadását a STDIN-re továbbítja az alkalmazásnak, és az alkalmazás az STDOUT-on lévő eredményeket adja vissza. Az STDIN-ből és az STDOUT-ból való olvasásra és írásra használhatja `Console.ReadLine()` `Console.WriteLine()` a és a konzolt.

## <a name="prerequisites"></a>Előfeltételek

* A .NET-keretrendszer 4,5-es számú C#-kódjának írásával és létrehozásával kapcsolatos ismeretek.

    Tetszőleges IDE-t használhat. A [Visual studiót](https://www.visualstudio.com/vs) vagy a [Visual Studio Code](https://code.visualstudio.com/)-ot ajánljuk. A jelen dokumentumban ismertetett lépések a Visual Studio 2019-et használják.

* Az. exe fájlok feltöltése a fürtbe, valamint a Pig és a kaptár feladatok futtatása. [A Visual Studio, a](../../data-lake-analytics/data-lake-analytics-data-lake-tools-install.md) [Azure PowerShell](/powershell/azure)és az [Azure CLI](/cli/azure/install-azure-cli)Data Lake eszközeinek használatát javasoljuk. A jelen dokumentumban szereplő lépések a Visual studióhoz készült Data Lake Tools használatával töltik fel a fájlokat, és futtatják a példa kaptár-lekérdezést.

    A kaptár-lekérdezések futtatásának egyéb módjaival kapcsolatban lásd: [Mi az Apache Hive és a HiveQL az Azure HDInsight?](hdinsight-use-hive.md).

* Egy Hadoop a HDInsight-fürtön. A fürtök létrehozásával kapcsolatos további információkért lásd: [HDInsight-fürtök létrehozása](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="net-on-hdinsight"></a>.NET on HDInsight

A *Linux-alapú HDInsight-* fürtök a MONO-t használják [( https://mono-project.com)](https://mono-project.com) .NET-alkalmazások futtatására. A HDInsight 3,6-es verziója tartalmazza a Mono 4.2.1-es verzióját.

További információ a .NET-keretrendszer verzióival való monó kompatibilitásról: [monó kompatibilitás](https://www.mono-project.com/docs/about-mono/compatibility/).

A .NET-keretrendszer és a Mono HDInsight-verziókhoz tartozó verziójával kapcsolatos további információkért lásd: [HDInsight-összetevő verziói](../hdinsight-component-versioning.md).

## <a name="create-the-c-projects"></a>A C \# projektek létrehozása

A következő szakaszok azt ismertetik, hogyan hozható létre C#-projekt a Visual Studióban egy Apache Hive UDF és egy Apache Pig UDF számára.

### <a name="apache-hive-udf"></a>UDF Apache Hive

C#-projekt létrehozása egy Apache Hive UDF-hez:

1. Indítsa el a Visual studiót.

2. Válassza **az új projekt létrehozása** lehetőséget.

3. Az **új projekt létrehozása** ablakban válassza ki a **Console app (.NET-keretrendszer)** sablont (a C# verzióját). Ezután kattintson a **Tovább** gombra.

4. Az **új projekt konfigurálása** ablakban adja meg az *HiveCSharp* **projekt nevét** , és navigáljon az új projekt mentéséhez, vagy hozzon létre egy **helyet** . Ezután kattintson a **Létrehozás** elemre.

5. A Visual Studio IDE cserélje le a *program. cs fájl* tartalmát a következő kódra:

    ```csharp
    using System;
    using System.Security.Cryptography;
    using System.Text;
    using System.Threading.Tasks;

    namespace HiveCSharp
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Parse the string, trimming line feeds
                    // and splitting fields at tabs
                    line = line.TrimEnd('\n');
                    string[] field = line.Split('\t');
                    string phoneLabel = field[1] + ' ' + field[2];
                    // Emit new data to stdout, delimited by tabs
                    Console.WriteLine("{0}\t{1}\t{2}", field[0], phoneLabel, GetMD5Hash(phoneLabel));
                }
            }
            /// <summary>
            /// Returns an MD5 hash for the given string
            /// </summary>
            /// <param name="input">string value</param>
            /// <returns>an MD5 hash</returns>
            static string GetMD5Hash(string input)
            {
                // Step 1, calculate MD5 hash from input
                MD5 md5 = System.Security.Cryptography.MD5.Create();
                byte[] inputBytes = System.Text.Encoding.ASCII.GetBytes(input);
                byte[] hash = md5.ComputeHash(inputBytes);

                // Step 2, convert byte array to hex string
                StringBuilder sb = new StringBuilder();
                for (int i = 0; i < hash.Length; i++)
                {
                    sb.Append(hash[i].ToString("x2"));
                }
                return sb.ToString();
            }
        }
    }
    ```

6. A menüsávban válassza a Build   >  **Build megoldás** elemet a projekt létrehozásához.

7. A megoldás lezárása.

### <a name="apache-pig-udf"></a>Apache Pig UDF

C#-projekt létrehozása egy Apache Hive UDF-hez:

1. Nyissa meg a Visual Studiót.

2. A **Start** ablakban válassza az **új projekt létrehozása** lehetőséget.

3. Az **új projekt létrehozása** ablakban válassza ki a **Console app (.NET-keretrendszer)** sablont (a C# verzióját). Ezután kattintson a **Tovább** gombra.

4. Az **új projekt konfigurálása** ablakban adja meg a *PigUDF* **projekt nevét** , és lépjen a helyre, vagy hozzon létre egy **helyet** az új projekt mentéséhez a alkalmazásban. Ezután kattintson a **Létrehozás** elemre.

5. A Visual Studio IDE cserélje le a *program. cs fájl* tartalmát a következő kódra:

    ```csharp
    using System;

    namespace PigUDF
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Fix formatting on lines that begin with an exception
                    if(line.StartsWith("java.lang.Exception"))
                    {
                        // Trim the error info off the beginning and add a note to the end of the line
                        line = line.Remove(0, 21) + " - java.lang.Exception";
                    }
                    // Split the fields apart at tab characters
                    string[] field = line.Split('\t');
                    // Put fields back together for writing
                    Console.WriteLine(String.Join("\t",field));
                }
            }
        }
    }
    ```

    Ez a kód elemzi a sertésből eljuttatott sorokat, és újraformázza a-val kezdődő sorokat `java.lang.Exception` .

6. A menüsávban válassza a **Build**  >  **Build megoldás** elemet a projekt létrehozásához.

7. Hagyja megnyitva a megoldást.

## <a name="upload-to-storage"></a>Feltöltés tárolóba

Ezután töltse fel a kaptár és a Pig UDF alkalmazásokat a Storage-ba egy HDInsight-fürtön.

1. A Visual Studióban navigáljon a  >  **Server Explorer** megtekintése elemre.

1. A **Server Explorerben** kattintson a jobb gombbal az **Azure** elemre, válassza a **Kapcsolódás Microsoft Azure előfizetéshez** lehetőséget, és fejezze be a bejelentkezési folyamatot.

1. Bontsa ki azt a HDInsight-fürtöt, amelyre telepíteni kívánja az alkalmazást. Megjelenik egy bejegyzés a szöveggel **(alapértelmezett Storage-fiók)** .

    ![Alapértelmezett Storage-fiók, HDInsight-fürt, Server Explorer](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/hdinsight-storage-account.png)

    * Ha ezt a bejegyzést ki lehet bővíteni, az **Azure Storage-fiókot** használja alapértelmezett tárolóként a fürt számára. Ha meg szeretné tekinteni a fürt alapértelmezett tárolójában lévő fájlokat, bontsa ki a bejegyzést, majd kattintson duplán a **(alapértelmezett tároló)** elemre.

    * Ha ezt a bejegyzést nem lehet kibontani, a **Azure Data Lake Storage** használja a fürt alapértelmezett tárolójának. A fürt alapértelmezett tárolójában lévő fájlok megtekintéséhez kattintson duplán az **(alapértelmezett Storage-fiók)** bejegyzésre.

1. Az. exe fájlok feltöltéséhez használja az alábbi módszerek egyikét:

    * Ha **Azure Storage-fiókot** használ, válassza a **blob feltöltése** ikont.

        ![HDInsight feltöltés ikonja új projekthez](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/hdinsight-upload-icon.png)

        Az **új fájl feltöltése** párbeszédpanel **fájlnév** területén válassza a **Tallózás** lehetőséget. A **blob feltöltése** párbeszédpanelen lépjen a *HiveCSharp* projekt *bin\debug* mappájába, majd válassza ki a *HiveCSharp.exe* fájlt. Végül kattintson a **Megnyitás** elemre, majd az **OK gombra** a feltöltés befejezéséhez.

    * Ha **Azure Data Lake Storage** használ, kattintson a jobb gombbal egy üres elemre a fájl listázása területen, majd válassza a **feltöltés** lehetőséget. Végül válassza ki a *HiveCSharp.exe* fájlt, és válassza a **Megnyitás** lehetőséget.

    A *HiveCSharp.exe* feltöltésének befejezése után ismételje meg a *PigUDF.exe* fájl feltöltési folyamatát.

## <a name="run-an-apache-hive-query"></a>Apache Hive lekérdezés futtatása

Most már futtathat egy kaptár-lekérdezést, amely a kaptár UDF-alkalmazást használja.

1. A Visual Studióban navigáljon a  >  **Server Explorer** megtekintése elemre.

2. Bontsa ki az **Azure** elemet, majd bontsa ki a **HDInsight** elemet.

3. Kattintson a jobb gombbal arra a fürtre, amelyre a *HiveCSharp* alkalmazást telepítette, majd válassza a **struktúra-lekérdezés írása** lehetőséget.

4. Használja a következő szöveget a kaptár-lekérdezéshez:

    ```hiveql
    -- Uncomment the following if you are using Azure Storage
    -- add file wasbs:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Storage Gen1
    -- add file adl:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Storage Gen2
    -- add file abfs:///HiveCSharp.exe;

    SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'HiveCSharp.exe' AS
    (clientid string, phoneLabel string, phoneHash string)
    FROM hivesampletable
    ORDER BY clientid LIMIT 50;
    ```

    > [!IMPORTANT]
    > A `add file` fürthöz használt alapértelmezett tároló típusának megfelelő utasítás megjegyzésének visszaírása.

    A lekérdezés kiválasztja a, a `clientid` `devicemake` és a `devicemodel` mezőket `hivesampletable` , majd átadja a mezőket a *HiveCSharp.exe* alkalmazásnak. A lekérdezés azt várja, hogy az alkalmazás három mezőt ad vissza, amelyek a következő módon vannak tárolva:, `clientid` `phoneLabel` és `phoneHash` . A lekérdezés az alapértelmezett tároló gyökerében lévő *HiveCSharp.exe* megtalálását is várja.

5. Állítsa az alapértelmezett **interaktív** elemet a **Batch** értékre, majd válassza a **Submit (Küldés** ) lehetőséget a HDInsight-fürthöz való küldéshez. Megnyílik a **kaptár-feladatok összegző** ablaka.

6. Válassza a **frissítés** lehetőséget az összefoglalás frissítéséhez, amíg a **feladatok állapota** **Befejezve** állapotúra nem változik. A feladatok kimenetének megtekintéséhez válassza a **feladatok kimenete** elemet.

## <a name="run-an-apache-pig-job"></a>Apache Pig-feladatok futtatása

A Pig UDF-alkalmazást használó Pig-feladatot is futtathat.

1. Az SSH használatával csatlakozzon a HDInsight-fürthöz. (Például futtassa a parancsot `ssh sshuser@<clustername>-ssh.azurehdinsight.net` .) További információ: az [SSH-WithHDInsight használata](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. A Pig parancssor elindításához használja a következő parancsot:

    ```shell
    pig
    ```

    Egy `grunt>` üzenet jelenik meg.

3. A .NET-keretrendszer alkalmazást használó Pig-feladatok futtatásához adja meg a következőt:

    ```pig
    DEFINE streamer `PigUDF.exe` CACHE('/PigUDF.exe');
    LOGS = LOAD '/example/data/sample.log' as (LINE:chararray);
    LOG = FILTER LOGS by LINE is not null;
    DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
    DUMP DETAILS;
    ```

    Az `DEFINE` utasítás aliast hoz létre `streamer` a *PigUDF.exe* alkalmazáshoz, és `CACHE` betölti azt az alapértelmezett tárolóból a fürt számára. Később az `streamer` `STREAM` operátorral együtt feldolgozza a benne foglalt egyetlen sort, `LOG` és az adatmennyiséget oszlopként jeleníti meg.

    > [!NOTE]
    > A folyamatos átvitelhez használt alkalmazás nevének a \` (kezdő) karakternek kell lennie, ha a (z) aliast használ, és az "(aposztróf)" karakternek kell lennie `SHIP` .

4. Az utolsó sor megadása után a feladattípusnak indulnia kell. Az alábbi szöveghez hasonló kimenetet ad vissza:

    ```output
    (2019-07-15 16:43:25 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
    (2019-07-15 16:43:25 SampleClass5 [DEBUG] detail for id 1976092771)
    (2019-07-15 16:43:25 SampleClass5 [TRACE] verbose detail for id 1317358561)
    (2019-07-15 16:43:25 SampleClass5 [TRACE] verbose detail for id 1737534798)
    (2019-07-15 16:43:25 SampleClass7 [DEBUG] detail for id 1475865947)
    ```

5. `exit`A sertésből való kilépéshez használatos.

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtudhatta, hogyan használható a .NET-keretrendszer alkalmazása a kaptárból és a Pig on HDInsight. Ha szeretné megtudni, hogyan használhatja a Pythont a kaptárral és a Malactal, tekintse meg [a Python használata Apache Hive és az Apache Pig használatát a HDInsight-ben](python-udf-hdinsight.md)című témakört.

A kaptár használatának egyéb módjaival és a MapReduce használatával kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

* [Apache Hive használata a HDInsight](hdinsight-use-hive.md)
* [A MapReduce használata a HDInsight](hdinsight-use-mapreduce.md)
* [Pig Latin alapjai](https://pig.apache.org/docs/latest/basic.html)
