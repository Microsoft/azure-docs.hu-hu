---
title: Az alkalmazások hibakereséséhez használja a Apache Spark History Server kiterjesztett funkcióit az Azure HDInsight
description: A Apache Spark History Server kiterjesztett funkciói a Spark-alkalmazások hibakeresésére és diagnosztizálására használhatók az Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/25/2019
ms.openlocfilehash: c6645bc605dbd60d331ac0de002c36384b2bbbc4
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864754"
---
# <a name="use-the-extended-features-of-the-apache-spark-history-server-to-debug-and-diagnose-spark-applications"></a>A Apache Spark History Server kiterjesztett funkcióinak használata a Spark-alkalmazások hibakereséséhez és diagnosztizálásához

Ez a cikk bemutatja, hogyan használhatja a Apache Spark History Server bővített funkcióit a befejezett vagy futó Spark-alkalmazások hibakeresésére és diagnosztizálására. A bővítmény tartalmaz egy **adatlapot,** egy **gráf** fület és egy **diagnosztika** lapot. Az **adatok** lapon megtekintheti a Spark-feladathoz tartozó bemeneti és kimeneti adatokat. A **Graph (gráf** ) lapon megtekintheti az adatfolyamot, és visszajátszhatja a feladatok gráfját. A **diagnosztika** lapon áttekintheti az **adatok eldöntését**, az **időeltérést** és a **végrehajtói használat elemzési** funkcióit.

## <a name="get-access-to-the-spark-history-server"></a>Hozzáférés a Spark History-kiszolgálóhoz

A Spark History-kiszolgáló a Spark-alkalmazások befejezéséhez és futtatásához használható webes KEZELŐFELÜLET. A Azure Portal vagy egy URL-címről is megnyithatja.

### <a name="open-the-spark-history-server-web-ui-from-the-azure-portal"></a>Nyissa meg a Spark History Server webes FELÜLETét a Azure Portal

1. A [Azure Portal](https://portal.azure.com/)nyissa meg a Spark-fürtöt. További információ: [fürtök listázása és megjelenítése](../hdinsight-administer-use-portal-linux.md#showClusters).
2. A **fürt irányítópultok** területen válassza a  **Spark-előzmények kiszolgáló** elemet. Ha a rendszer kéri, adja meg a Spark-fürt rendszergazdai hitelesítő adatait.

    :::image type="content" source="./media/apache-azure-spark-history-server/azure-portal-dashboard-spark-history.png " alt-text="Indítsa el a Spark History-kiszolgálót a Azure Portal." border="true"::: a Azure Portal. " Border = "true":::

### <a name="open-the-spark-history-server-web-ui-by-url"></a>A Spark History Server webes felhasználói felületének megnyitása URL-cím alapján

Nyissa meg a Spark History-kiszolgálót tallózással `https://CLUSTERNAME.azurehdinsight.net/sparkhistory` , ahol a **CLUSTERNAME** a Spark-fürt neve.

A Spark History Server webes FELÜLETe a következő képhez hasonlóan néz ki:

:::image type="content" source="./media/apache-azure-spark-history-server/hdinsight-spark-history-server.png" alt-text="A Spark-előzmények kiszolgálójának lapja." border="true":::

## <a name="use-the-data-tab-in-the-spark-history-server"></a>A Spark History-kiszolgáló adatlapjainak használata

Válassza ki a feladatsort, majd az adatnézet megjelenítéséhez válassza az eszközök menü **adatok** elemét.

+ Az egyes lapok kiválasztásával tekintse át a **bemeneteket**, a **kimeneteket** és a **táblázatos műveleteket** .

    :::image type="content" source="./media/apache-azure-spark-history-server/apache-spark-data-tabs.png" alt-text="Az adatlapok a Spark-alkalmazáshoz tartozó oldalon." border="true":::

+ A **Másolás** gombra kattintva másolja az összes sort.

    :::image type="content" source="./media/apache-azure-spark-history-server/apache-spark-data-copy.png" alt-text="Adatmásolás a Spark-alkalmazás oldalán." border="true":::

+ Mentse az összes adathalmazt. CSV-fájlt a **CSV** gomb kiválasztásával.

    :::image type="content" source="./media/apache-azure-spark-history-server/apache-spark-data-save.png" alt-text="Az adatmentés. CSV-fájl a Spark-alkalmazás adatainak oldaláról." border="true":::

+ Az adatokat úgy keresheti meg, hogy beírja a kulcsszavakat a **keresőmezőbe** . A keresési eredmények azonnal megjelennek.

    :::image type="content" source="./media/apache-azure-spark-history-server/apache-spark-data-search.png" alt-text="Az adatkeresés a Spark-alkalmazás oldalán." border="true":::

+ A táblázat rendezéséhez válassza ki az oszlop fejlécét. Kattintson a pluszjelre egy sor kibontásához a további részletek megjelenítéséhez. Válassza ki a mínuszjelet egy sor összecsukásához.

    :::image type="content" source="./media/apache-azure-spark-history-server/apache-spark-data-table.png" alt-text="A Spark-alkalmazáshoz tartozó adattábla adattáblája" border="true":::

+ Egyetlen fájl letöltéséhez kattintson a jobb oldalon található **részleges Letöltés** gombra. A kiválasztott fájl helyileg lesz letöltve. Ha a fájl már nem létezik, megnyílik egy új lap a hibaüzenetek megjelenítéséhez.

    :::image type="content" source="./media/apache-azure-spark-history-server/sparkui-data-download-row.png" alt-text="Az adatletöltési sor a Spark-alkalmazásra vonatkozó adatlapon." border="true":::

+ Másolja a teljes elérési utat vagy a relatív elérési utat úgy, hogy kiválasztja a **teljes elérési út másolása** vagy a **relatív elérési út** másolása lehetőséget, amely kibontható a letöltés menüből. Azure Data Lake Storage fájlok esetében válassza a **Megnyitás a következőben Azure Storage Explorer** lehetőséget a Azure Storage Explorer elindításához, majd a bejelentkezés után keresse meg a mappát.

    :::image type="content" source="./media/apache-azure-spark-history-server/sparkui-data-copy-path.png" alt-text="Másolja a teljes elérési utat, és másolja a relatív elérési út beállításait a Spark-alkalmazásra vonatkozó adat oldalon." border="true":::

+ Ha túl sok sor van megjelenítve egy oldalon, a táblázat alján lévő oldalszámok kiválasztásával navigáljon.

    :::image type="content" source="./media/apache-azure-spark-history-server/apache-spark-data-page.png" alt-text="A Spark-alkalmazásra vonatkozó adatok oldalszámai." border="true":::

+ További információért vigye a kurzort a **Spark-alkalmazás adatai** melletti kérdőjelre, vagy válassza ki az elemleírás megjelenítéséhez.

    :::image type="content" source="./media/apache-azure-spark-history-server/sparkui-data-more-info.png" alt-text="További információt a Spark-alkalmazás adatairól szóló oldalon kaphat." border="true":::

+  Ha visszajelzést szeretne küldeni a problémákról, válassza a **visszajelzés** küldése lehetőséget.

    :::image type="content" source="./media/apache-azure-spark-history-server/sparkui-graph-feedback.png" alt-text="Visszajelzés küldése a Spark-alkalmazás adatainak oldaláról." border="true":::

## <a name="use-the-graph-tab-in-the-spark-history-server"></a>A Spark History-kiszolgáló Graph (gráf) lapjának használata

+ Válassza ki a feladattípust, majd az eszköz menüjében válassza a **Graph (gráf** ) lehetőséget a feladatok diagramjának megjelenítéséhez. Alapértelmezés szerint a Graph megjeleníti az összes feladatot. Az eredmények szűréséhez használja a **feladattípus** legördülő menüt.

    :::image type="content" source="./media/apache-azure-spark-history-server/apache-spark-graph-jobid.png" alt-text="A Spark-alkalmazás feladattípus legördülő menüje & a feladatok gráfja oldalon." border="true":::

+ A **folyamat** alapértelmezés szerint ki van választva. Az adatfolyamatot úgy tekintheti meg, hogy a **Megjelenítés** legördülő menüben az **olvasás** vagy **írás** lehetőséget választja.

    :::image type="content" source="./media/apache-azure-spark-history-server/sparkui-graph-display.png" alt-text="Az adatfolyamot a Spark-alkalmazás & feladatok diagramja lapon találja." border="true":::

+ Az egyes feladatok háttérszíne egy Heat térképnek felel meg.

   :::image type="content" source="./media/apache-azure-spark-history-server/sparkui-graph-heatmap.png" alt-text="A Spark-alkalmazás & a feladatok Gráfjának lapja." border="true":::


    |Szín |Leírás |
    |---|---|
    |Zöld|A feladatot sikeresen befejezte.|
    |Narancssárga|A feladat nem sikerült, de ez nem befolyásolja a feladat végső eredményét. Ezek a feladatok ismétlődő vagy újrapróbálkozási példányokkal rendelkeznek, amelyek később is sikeresek lehetnek.|
    |Kék|A feladat fut.|
    |Fehér|A feladat futásra vár, vagy a szakasz kimaradt.|
    |Piros|A feladat nem sikerült.|

     :::image type="content" source="./media/apache-azure-spark-history-server/sparkui-graph-color-running.png" alt-text="Feladat futtatása a Spark-alkalmazás & a feladat gráf lapján." border="true":::

     A kihagyott szakaszok fehérben jelennek meg.
    :::image type="content" source="./media/apache-azure-spark-history-server/sparkui-graph-color-skip.png" alt-text="Kihagyott feladat a Spark-alkalmazás & a feladatok gráfja lapon." border="true":::

    :::image type="content" source="./media/apache-azure-spark-history-server/sparkui-graph-color-failed.png" alt-text="Sikertelen feladat a Spark-alkalmazás & a feladat gráf lapján." border="true":::

     > [!NOTE]  
     > A befejezett feladatokhoz a lejátszás elérhető. Kattintson a **Lejátszás** gombra a feladatok visszajátszásához. A Leállítás gomb kiválasztásával bármikor leállíthatja a feladatot. Egy feladat visszajátszásakor minden feladat szín szerint jeleníti meg az állapotát. Hiányos feladatok esetén a lejátszás nem támogatott.

+ Görgessen a nagyításhoz vagy kicsinyítéshez a feladatok gráfján, vagy válassza a **Nagyítás** lehetőséget, hogy illeszkedjen a képernyőhöz.

    :::image type="content" source="./media/apache-azure-spark-history-server/sparkui-graph-zoom2fit.png" alt-text="A Spark-alkalmazás & a feladatok diagramja lapon válassza a nagyítás elemet." border="true":::

+ Ha a feladatok sikertelenek, vigye az egérmutatót a Graph csomópontra az elemleírás megjelenítéséhez, majd válassza ki a szakaszt, és nyissa meg egy új lapon.

    :::image type="content" source="./media/apache-azure-spark-history-server/sparkui-graph-tooltip.png" alt-text="Tekintse meg az elemleírást a Spark-alkalmazás & a feladatok gráfja oldalon." border="true":::

+ A Spark-alkalmazás & feladatok diagramja lapon a szakaszok elemleírásokat és kis ikonokat jelenítenek meg, ha a feladatok megfelelnek a következő feltételeknek:
  + Adat-elferdítés: az adatolvasási méret > a fázisban lévő összes feladat átlagos adatolvasási mérete, *és* az adatolvasási méret > 10 MB.
  + Időeltérés: a végrehajtás ideje > az ebben a szakaszban szereplő összes feladat átlagos végrehajtási idejét, a végrehajtási időt *pedig* > 2 percen belül.

    :::image type="content" source="./media/apache-azure-spark-history-server/sparkui-graph-skew-icon.png" alt-text="Az elferdített feladat ikon a Spark-alkalmazás & a feladatok gráfja oldalon." border="true":::

+ A feladatütemezés csomópontja a következő információkat jeleníti meg az egyes szakaszokról:
  + ID (Azonosító)
  + Név vagy Leírás
  + Feladat teljes száma
  + Olvasott adatok: a bemeneti méret és a shuffle olvasási méret összege
  + Adatírás: a kimeneti méret és a shuffle írási méret összege
  + Végrehajtási idő: az első kísérlet kezdési időpontja és a legutóbbi kísérlet befejezési időpontja közötti idő
  + Sorok száma: a bemeneti rekordok, a kimeneti rekordok, a shuffle olvasási rekordok és a shuffle írási rekordok összege
  + Előrehaladás

    > [!NOTE]  
    > Alapértelmezés szerint a feladatütemezés csomópontja az egyes szakaszok utolsó próbálkozásának információit jeleníti meg (kivéve a fázis végrehajtási idejét). A lejátszás során azonban a feladatok gráf csomópontja az egyes kísérletekre vonatkozó információkat jeleníti meg.

    > [!NOTE]  
    > Az olvasási és adatírási méretek esetében az 1MB = 1000 KB = 1000 * 1000 bájtot használjuk.

+ Küldjön visszajelzést a problémákról, ha kijelöli az **USA visszajelzése** lehetőséget.

    :::image type="content" source="./media/apache-azure-spark-history-server/sparkui-graph-feedback.png" alt-text="A Spark-alkalmazás visszajelzési lehetősége & a feladatok gráfja oldalon." border="true":::

## <a name="use-the-diagnosis-tab-in-the-spark-history-server"></a>A Spark History-kiszolgáló diagnosztika lapjának használata

Válassza ki a feladatot AZONOSÍTÓját, majd válassza a **diagnosztika** lehetőséget az eszköz menüjében a feladatok diagnosztizálási nézetének megtekintéséhez. A **diagnosztika** lapon szerepel az **adatok eldöntése**, az **időeltérés** és a **végrehajtó általi használat elemzése**.

+ Tekintse át az **adatok eldöntését**, az **időeltérést** és a **végrehajtó használatának elemzését** a lapok kiválasztásával.

    :::image type="content" source="./media/apache-azure-spark-history-server/sparkui-diagnosis-tabs.png" alt-text="A diagnosztika lapon található elferdítés lap." border="true":::

### <a name="data-skew"></a>Az adattorzítás

Válassza az **adattorzítás** lapot. A megfelelő ferde feladatok a megadott paraméterek alapján jelennek meg.

#### <a name="specify-parameters"></a>Paraméterek megadása

A **paraméterek megadása** szakasz megjeleníti azokat a paramétereket, amelyek az adatok eldöntésének észlelésére szolgálnak. Az alapértelmezett szabály a következő: az olvasott feladat több, mint az átlagos feladathoz tartozó adat, és az olvasási feladat több mint 10 MB. Ha meg szeretné határozni saját szabályát a ferde feladatokhoz, kiválaszthatja a paramétereket. A **ferde szakasz** és a **ferde diagram** szakasza ennek megfelelően frissülni fog.

#### <a name="skewed-stage"></a>Ferde szakasz

A **ferde szakasz** szakasz azokat a szakaszokat jeleníti meg, amelyek a megadott feltételeknek megfelelő elferdített feladatokat rendelkeznek. Ha egy szakaszban több elferdített feladat van, akkor a **ferde szakasz** szakasz csak a leginkább ferde feladatot jeleníti meg (vagyis az elferdítés legnagyobb adatát).

:::image type="content" source="./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section2.png" alt-text="A diagnosztika lapon található elferdítés lap nagyobb nézete." border="true":::

##### <a name="skew-chart"></a>Diagram igazítása

Amikor kijelöl egy sort a **döntési fázis** táblában, az **elferdítő diagram** további tevékenység-elosztási részleteket jelenít meg az adatok olvasási és végrehajtási időpontja alapján. A ferde feladatok piros színnel vannak megjelölve, és a normál feladatok kék színnel vannak megjelölve. A teljesítmény szempontjából a diagram legfeljebb 100 minta feladatot jelenít meg. A feladat részletei a jobb alsó panelen jelennek meg.

:::image type="content" source="./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section3.png" alt-text="A Spark felhasználói felületének 10-es fázisának diagramja." border="true":::

### <a name="time-skew"></a>Időbeli döntés

Az **idő megdöntése** lap a feladat végrehajtási ideje alapján elferdített feladatokat jeleníti meg.

#### <a name="specify-parameters"></a>Paraméterek megadása

A **paraméterek megadása** szakasz megjeleníti azokat a paramétereket, amelyek az időeltérés észlelésére szolgálnak. Az alapértelmezett szabály: a feladat-végrehajtási idő nagyobb, mint az átlagos végrehajtási idő háromszorosa, a feladat végrehajtási ideje pedig 30 másodpercnél nagyobb. A paramétereket igény szerint módosíthatja. A **ferde szakasz** és a **ferde diagram** a megfelelő szakaszokat és feladatokat jeleníti meg, ugyanúgy, mint az **adatok eldöntése** lapon.

Ha az **időeltérést** választja, a szűrt eredmény a **paraméterek megadása** szakaszban megadott paramétereknek megfelelően a **ferde fázis** szakaszban jelenik meg. Ha kijelöl egy elemet a **ferde szakasz** szakaszban, a megfelelő diagram a harmadik szakaszban van kiválasztva, a feladat részletei pedig a jobb alsó panelen jelennek meg.

:::image type="content" source="./media/apache-azure-spark-history-server/sparkui-diagnosis-timeskew-section2.png" alt-text="A diagnosztika lapon lévő idő elferdítés lapja." border="true":::

### <a name="executor-usage-analysis-graphs"></a>Végrehajtói használati elemzési diagramok

A **végrehajtói használat gráf** megjeleníti a feladatok tényleges végrehajtói kiosztását és futási állapotát.  

Ha kiválasztja a **végrehajtó általi használat elemzését**, a rendszer négy különböző görbét készít a végrehajtó használatáról: **lefoglalt végrehajtók**, **futó** végrehajtók, **üresjárati végrehajtók** és **Max végrehajtó példányok**. Minden, a **végrehajtó által hozzáadott** vagy **végrehajtó által eltávolított** esemény növeli vagy csökkenti a lefoglalt végrehajtókat. További összehasonlításokhoz a **feladatok** lapon tekintheti meg az **esemény idővonalát** .

:::image type="content" source="./media/apache-azure-spark-history-server/sparkui-diagnosis-executors.png" alt-text="A végrehajtó használat elemzése lap a diagnosztika lapon." border="true":::

A szín ikonra kattintva kiválaszthatja vagy kiválaszthatja a megfelelő tartalmat az összes piszkozatban.

 :::image type="content" source="./media/apache-azure-spark-history-server/sparkui-diagnosis-select-chart.png" alt-text="Válassza ki a diagramot a végrehajtó használat elemzése lapon." border="true":::

## <a name="faq"></a>GYIK

### <a name="how-do-i-revert-to-the-community-version"></a>Hogyan visszaállítjuk a közösségi verziót?

A közösségi verzióra való visszaálláshoz hajtsa végre a következő lépéseket.

1. Nyissa meg a fürtöt a Ambari-ben.
1. Navigáljon a **Spark2**-  >  **konfigurációkhoz**.
1. Válassza az **Egyéni spark2 – Alapértelmezések** lehetőséget.
1. Válassza a **tulajdonság hozzáadása...** lehetőséget.
1. Adja hozzá a **Spark. UI. Enhancement. enabled = FALSE értéket**, majd mentse azt.
1. A tulajdonság a **false (hamis) értéket** állítja be.
1. A konfiguráció mentéséhez kattintson a **Mentés** gombra.

    :::image type="content" source="./media/apache-azure-spark-history-server/apache-spark-turn-off.png" alt-text="Funkció kikapcsolása az Apache Ambari-ben." border="true":::

1. A bal oldali panelen válassza a **Spark2** lehetőséget. Ezután az **Összefoglalás** lapon válassza a Spark2- **Előzmények kiszolgáló** elemet.

    :::image type="content" source="./media/apache-azure-spark-history-server/apache-spark-restart1.png" alt-text="Az Apache Ambari összefoglaló nézete." border="true":::

1. A Spark-előzmények kiszolgálójának újraindításához válassza a **Spark2-kiszolgáló** jobb oldalán található **elindított** gombot, majd válassza az **Újraindítás** lehetőséget a legördülő menüből.

    :::image type="content" source="./media/apache-azure-spark-history-server/apache-spark-restart2.png" alt-text="Indítsa újra a Spark History Servert az Apache Ambari." border="true":::  

1. Frissítse a Spark History Server webes FELÜLETét. A rendszer visszaállít a közösségi verzióra.

### <a name="how-do-i-upload-a-spark-history-server-event-to-report-it-as-an-issue"></a>Hogyan Feltölthetek egy Spark History Server-eseményt, hogy problémát jelentsenek?

Ha hibát észlel a Spark History-kiszolgálón, az alábbi lépéseket követve jelentheti be az eseményt.

1. Töltse le az eseményt a Spark History Server webes felhasználói felületén a **Letöltés** lehetőség kiválasztásával.

    :::image type="content" source="./media/apache-azure-spark-history-server/sparkui-download-event.png" alt-text="Töltse le az eseményt a Spark History Server felhasználói felületén." border="true":::

2. Válassza a **Spark-alkalmazás & a feladatok diagramja** oldalon a **visszajelzés küldése** lehetőséget.

    :::image type="content" source="./media/apache-azure-spark-history-server/sparkui-graph-feedback.png" alt-text="Visszajelzés küldése a Spark-alkalmazás & a feladatok gráfja oldalon" border="true":::

3. Adja meg a címet és a hiba leírását. Ezután húzza a. zip fájlt a Szerkesztés mezőbe, és válassza az **új probléma beküldése** lehetőséget.

    :::image type="content" source="./media/apache-azure-spark-history-server/apache-spark-file-issue.png" alt-text="Új probléma feltöltése és elküldése." border="true":::

### <a name="how-do-i-upgrade-a-jar-file-in-a-hotfix-scenario"></a>Hogyan frissíteni egy. jar-fájlt egy gyorsjavítási forgatókönyvben?

Ha gyorsjavítással szeretné frissíteni, használja a következő parancsfájlt, amely frissíteni fogja `spark-enhancement.jar*` .

**upgrade_spark_enhancement. sh**:

   ```bash
    #!/usr/bin/env bash

    # Copyright (C) Microsoft Corporation. All rights reserved.

    # Arguments:
    # $1 Enhancement jar path

    if [ "$#" -ne 1 ]; then
        >&2 echo "Please provide the upgrade jar path."
        exit 1
    fi

    install_jar() {
        tmp_jar_path="/tmp/spark-enhancement-hotfix-$( date +%s )"

        if wget -O "$tmp_jar_path" "$2"; then
            for FILE in "$1"/spark-enhancement*.jar
            do
                back_up_path="$FILE.original.$( date +%s )"
                echo "Back up $FILE to $back_up_path"
                mv "$FILE" "$back_up_path"
                echo "Copy the hotfix jar file from $tmp_jar_path   to $FILE"
                cp "$tmp_jar_path" "$FILE"

                "Hotfix done."
                break
            done
        else    
            >&2 echo "Download jar file failed."
            exit 1
        fi
    }

    jars_folder="/usr/hdp/current/spark2-client/jars"
    jar_path=$1

    if ls ${jars_folder}/spark-enhancement*.jar 1>/dev/null 2>&1;   then
        install_jar "$jars_folder" "$jar_path"
    else
        >&2 echo "There is no target jar on this node. Exit with no action."
        exit 0
    fi
   ```

#### <a name="usage"></a>Használat

`upgrade_spark_enhancement.sh https://${jar_path}`

#### <a name="example"></a>Példa

`upgrade_spark_enhancement.sh https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`

#### <a name="use-the-bash-file-from-the-azure-portal"></a>A bash-fájl használata a Azure Portal

1. Indítsa el a [Azure Portal](https://ms.portal.azure.com), majd válassza ki a fürtöt.
2. Hajtson végre egy [parancsfájl-műveletet](../hdinsight-hadoop-customize-cluster-linux.md) a következő paraméterekkel.

    |Tulajdonság |Érték |
    |---|---|
    |Parancsfájl típusa|– Egyéni|
    |Name|UpgradeJar|
    |Bash-parancsfájl URI-ja|`https://hdinsighttoolingstorage.blob.core.windows.net/shsscriptactions/upgrade_spark_enhancement.sh`|
    |Csomópont típusa (i)|Head, Worker|
    |Paraméterek|`https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`|

     :::image type="content" source="./media/apache-azure-spark-history-server/apache-spark-upload1.png" alt-text="Parancsfájl-Azure Portal küldése művelet" border="true":::

## <a name="known-issues"></a>Ismert problémák

+ Jelenleg a Spark-előzmények kiszolgálója csak a Spark 2,3-es és 2,4-es verziójában működik.

+ A RDD-t használó bemeneti és kimeneti adatok nem jelennek meg az **adatok** lapon.

## <a name="next-steps"></a>Következő lépések

+ [Apache Spark-fürt erőforrásainak kezelése a HDInsight-ben](apache-spark-resource-manager.md)
+ [Az Apache Spark beállításainak konfigurálása](apache-spark-settings.md)

## <a name="suggestions"></a>Javaslatok

Ha bármilyen észrevétele van, vagy az eszköz használata során bármilyen problémába ütközik, küldjön egy e-mailt a következő címre: ( [hdivstool@microsoft.com](mailto:hdivstool@microsoft.com) ).
