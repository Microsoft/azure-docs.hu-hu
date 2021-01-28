---
title: Egyéni MapReduce-programok futtatása – Azure HDInsight
description: Mikor és hogyan futtathat egyéni Apache MapReduce-programokat az Azure HDInsight-fürtökön.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/01/2020
ms.openlocfilehash: 0f7a3676db14dbf61d3976690c8f9c71a660f889
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943231"
---
# <a name="run-custom-mapreduce-programs"></a>Egyéni MapReduce programok futtatása

A Apache Hadoop-alapú big data rendszerek, például a HDInsight lehetővé teszik az adatfeldolgozást az eszközök és technológiák széles skálájának használatával. Az alábbi táblázat ismerteti a főbb előnyeit és szempontjait.

| Lekérdezési mechanizmus | Előnyök | Megfontolandó szempontok |
| --- | --- | --- |
| **Apache Hive a HiveQL használatával** | <ul><li>Kiváló megoldás a kötegelt feldolgozásra és a nagy mennyiségű megváltoztathatatlan adatelemzésre, adatösszesítésre és igény szerinti lekérdezésre. Ismerős SQL-szerű szintaxist használ.</li><li>Használható olyan állandó adattábla létrehozására, amely könnyen particionálható és indexelhető.</li><li>Ugyanazon az adaton több külső tábla és nézet is létrehozható.</li><li>Egy egyszerű adattárház-implementációt támogat, amely nagy kibővíthető és hibatűrési képességeket biztosít az adattárolás és a feldolgozás számára.</li></ul> | <ul><li>Megköveteli, hogy a forrásadatok legalább néhány azonosítható struktúrával rendelkezzenek.</li><li>Nem alkalmas a valós idejű lekérdezések és a sorok szintjének frissítéseire. Nagy mennyiségű adathalmazon használható a Batch-feladatokhoz.</li><li>Előfordulhat, hogy nem tudja elvégezni az összetett feldolgozási feladatok bizonyos típusait.</li></ul> |
| **Apache Pig a Pig Latin használatával** | <ul><li>Kiváló megoldás az adathalmazok, az adatkészletek egyesítése és szűrése, a függvények rekordokra vagy rekordhalmazokra való alkalmazására, valamint az adatok átalakítására oszlopok definiálásával, értékek csoportosításával, vagy az oszlopok sorokra konvertálásával.</li><li>Munkafolyamat-alapú megközelítést használhat az adatokon végzett műveletek sorozata során.</li></ul> | <ul><li>Előfordulhat, hogy az SQL-felhasználók kevésbé ismerik és nehezebben használják a Pig latint, mint a HiveQL.</li><li>Az alapértelmezett kimenet általában szövegfájl, ezért a vizualizációs eszközökkel, például az Excelben nehezebben használható. A struktúrát általában a kimeneten fogja felvenni.</li></ul> |
| **Egyéni Térkép/csökkentés** | <ul><li>Teljes körű irányítást biztosít a Térkép felett, és csökkenti a fázisokat és a végrehajtást.</li><li>Lehetővé teszi a lekérdezések optimalizálását a fürt maximális teljesítményének elérésére, illetve a kiszolgálók és a hálózat terhelésének minimalizálására.</li><li>Az összetevők számos jól ismert nyelven is megírhatók.</li></ul> | <ul><li>Nehezebb, mint a Pig vagy a kaptár használata, mivel létre kell hoznia egy saját térképet, és csökkentenie kell az összetevőket.</li><li>A csatlakozási adatkészleteket igénylő folyamatok nehezebben valósíthatók meg.</li><li>Annak ellenére, hogy elérhetők tesztelési keretrendszerek, a hibakeresési kód összetettebb, mint a normál alkalmazás, mert a kód batch-feladatként fut a Hadoop Feladatütemező felügyelete alatt.</li></ul> |
| **Apache HCatalog** | <ul><li>Elvonta a tároló elérési útját, és megkönnyíti a felügyeletet, és megszünteti a felhasználók számára az adatok tárolásának szükségességét.</li><li>Lehetővé teszi az olyan események bejelentését, mint az adatok rendelkezésre állása, ami lehetővé teszi más eszközök, például a Oozie észlelését a műveletek bekövetkezésekor.</li><li>Lehetővé teszi az adatkapcsolatok megtekintését, beleértve a kulcsok particionálását, és megkönnyíti az adatelérést.</li></ul> | <ul><li>Alapértelmezés szerint támogatja a RCFile, a CSV-szövegeket, a JSON-szövegeket, a SequenceFile és az ork-fájlformátumokat, de előfordulhat, hogy más formátumokhoz egyéni SerDe kell írnia.</li><li>A HCatalog nem szál-biztonságos.</li><li>Bizonyos korlátozások vonatkoznak az oszlopok adattípusaira, amikor a HCatalog betöltőt használja a Pig-szkriptekben. További információ: HCatLoader- [adattípusok](https://cwiki.apache.org/confluence/display/Hive/HCatalog%20LoadStore#HCatalogLoadStore-HCatLoaderDataTypes) az Apache HCatalog dokumentációjában.</li></ul> |

Ezeket a megközelítéseket általában a legegyszerűbben a szükséges eredmények megadására használhatja. Előfordulhat például, hogy csak a kaptár használatával tudja elérni az eredményeket, de összetettebb forgatókönyvek esetén a Pig használatát kell használnia, vagy akár saját térképet is írnia kell, és csökkentenie kell az összetevőket. Dönthet úgy is, hogy a kaptárral vagy a Malactal való kísérletezés után az egyéni Térkép és az összetevők csökkentése jobb teljesítményt biztosít, mivel lehetővé teszi a feldolgozás finomhangolását és optimalizálását.

## <a name="custom-mapreduce-components"></a>Egyéni Térkép/összetevők csökkentése

A Térkép/csökkentés kód két különálló függvényből áll, amelyek a **Térkép** és az összetevők **csökkentése érdekében** vannak megvalósítva. A **map** összetevő párhuzamosan fut több fürtcsomóponton, és mindegyik csomópont a csomópont saját részhalmazára való leképezést alkalmazza. A **redukáló** összetevő leképezi és összegzi az összes Térkép függvény eredményét. A két összetevővel kapcsolatos további információkért lásd: [MapReduce használata a Hadoop-ben a HDInsight-ben](hdinsight-use-mapreduce.md).

A legtöbb HDInsight-feldolgozási forgatókönyv esetében egyszerűbb és hatékonyabb a magasabb szintű absztrakció, például a Pig vagy a kaptár használata. Létrehozhat egyéni leképezést is, és csökkentheti az összetevőket a kaptár-parancsfájlokon belüli használatra, hogy kifinomultabb feldolgozást végezzen.

Az egyéni Térkép/összetevők csökkentése általában Java nyelven íródott. A Hadoop olyan adatfolyam-felületet biztosít, amely más nyelveken (például C#, F #, Visual Basic, Python és JavaScript) fejlesztett összetevők használatát is lehetővé teszi.

* Az egyéni Java MapReduce programok fejlesztésével kapcsolatos bemutatóért lásd: [Java MapReduce-programok fejlesztése a Hadoop-hez a HDInsight-on](apache-hadoop-develop-deploy-java-mapreduce-linux.md).

Érdemes lehet saját térképet létrehoznia, és a következő feltételekkel csökkenteni az összetevőket:

* Az adatok elemzésével és egyéni logikával olyan adatokat kell feldolgoznia, amelyek teljesen strukturálatlan módon vannak rendszerezve.
* Olyan összetett feladatokat kíván végrehajtani, amelyek nehéz (vagy lehetetlen) jellegűek a Pig vagy a kaptár kifejezésre, és nem kell UDF-t létrehozni. Előfordulhat például, hogy egy külső helymeghatározáshoz szolgáltatást kell használnia a földrajzi hely neveként a szélességi és hosszúsági koordinátákat, illetve a forrásadatok IP-címeit.
* A Hadoop streaming Interface használatával szeretné felhasználni a meglévő .NET-, Python-vagy JavaScript-kódokat a Map/csökkentse összetevőkben.

## <a name="upload-and-run-your-custom-mapreduce-program"></a>Egyéni MapReduce program feltöltése és futtatása

A leggyakoribb MapReduce-programok Java nyelven íródtak, és egy jar-fájlba vannak lefordítva.

1. A MapReduce program fejlesztése, lefordítása és tesztelése után a `scp` paranccsal feltöltheti a jar-fájlt a átjárócsomóponthoz.

    ```cmd
    scp mycustomprogram.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Cserélje le az CLUSTERNAME-t a fürt nevére. Ha az SSH-fiók védelméhez jelszót használt, a rendszer kéri, hogy adja meg a jelszót. Ha tanúsítványt használt, lehetséges, hogy a `-i` paraméterrel kell megadnia a titkos kulcs fájlját.

1. A fürthöz való kapcsolódáshoz használja az [SSH-parancsot](../hdinsight-hadoop-linux-use-ssh-unix.md) . Szerkessze az alábbi parancsot az CLUSTERNAME helyére a fürt nevével, majd írja be a következő parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Az SSH-munkamenetből hajtsa végre a MapReduce programot a FONALon keresztül.

    ```bash
    yarn jar mycustomprogram.jar mynamespace.myclass /example/data/sample.log /example/data/logoutput
    ```

    Ez a parancs a MapReduce-feladatot a FONALba küldi. A bemeneti fájl a `/example/data/sample.log` és a kimeneti könyvtár `/example/data/logoutput` . A bemeneti fájl és a kimeneti fájlok a fürt alapértelmezett tárolójában tárolódnak.

## <a name="next-steps"></a>Következő lépések

* [A C# és a MapReduce streaming használata Apache Hadoop HDInsight](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)
* [Java MapReduce-programok fejlesztése a HDInsight Apache Hadoop](apache-hadoop-develop-deploy-java-mapreduce-linux.md)
* [Azure Toolkit for Eclipse használata Apache Spark-alkalmazások HDInsight-fürthöz való létrehozásához](../spark/apache-spark-eclipse-tool-plugin.md)
* [Python-felhasználó által definiált függvények (UDF) használata Apache Hive és Apache Pig használatával a HDInsight-ben](python-udf-hdinsight.md)
