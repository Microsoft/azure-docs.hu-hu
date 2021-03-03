---
title: A kiterjesztett Spark-előzmények kiszolgáló használata az alkalmazások hibakereséséhez
description: Használja a kiterjesztett Spark-előzményeket a Spark-alkalmazások hibakereséséhez és diagnosztizálásához az Azure szinapszis Analyticsben.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 10/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 4471fed7b423fe05147db30afe57f6c845fe640e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101670701"
---
# <a name="use-extended-apache-spark-history-server-to-debug-and-diagnose-apache-spark-applications"></a>Apache Spark alkalmazások hibakeresése és diagnosztizálása a kiterjesztett Apache Sparki előzményeket használó kiszolgálón

Ez a cikk útmutatást nyújt arról, hogyan használható a kiterjesztett Apache Sparki előzmények kiszolgáló a befejezett és futó Spark-alkalmazások hibakereséséhez és diagnosztizálásához.

A bővítmény tartalmaz egy adatlapot, a Graph fület és a diagnosztika lapot. Használja az **adatok** lapot a Spark-feladathoz tartozó bemeneti és kimeneti adatok vizsgálatához. A **Graph (gráf** ) lapon megtekintheti a feladatok gráfjának adatfolyamait és visszajátszását. A **diagnosztika** lapon az  **adatok elferdítés**, az **idő** és a **végrehajtó használatának elemzése** látható.

## <a name="access-the-apache-spark-history-server"></a>Hozzáférés az Apache Spark-előzménykiszolgálóhoz

A Apache Spark History Server a webes felhasználói felület a befejezett és futó Spark-alkalmazásokhoz. Az Azure szinapszis Analyticsből megnyithatja az Apache Spark History Server webes felületét.

### <a name="open-the-spark-history-server-web-ui-from-apache-spark-applications-node"></a>A Spark History Server webes felhasználói felületének megnyitása az Apache Spark-alkalmazások csomópontból

1. Nyissa meg az [Azure szinapszis Analytics szolgáltatást](https://web.azuresynapse.net/).

2. Válassza a **figyelés**, majd az **alkalmazások Apache Spark** lehetőséget.

    ![Válassza a figyelés, majd a Spark-alkalmazás lehetőséget.](./media/apache-spark-history-server/click-monitor-spark-application.png)

3. Válasszon ki egy alkalmazást, majd nyissa meg a **napló lekérdezését** .

    ![A napló lekérdezési ablakának megnyitása.](./media/apache-spark-history-server/open-application-window.png)

4. Válassza ki a **Spark History Server** elemet, majd megjelenik a Spark History Server webes kezelőfelülete.

    ![A Spark-előzmények kiszolgálójának megnyitása.](./media/apache-spark-history-server/open-spark-history-server.png)

### <a name="open-the-spark-history-server-web-ui-from-data-node"></a>A Spark History Server webes felhasználói felületének megnyitása adatcsomópontból

1. Az Azure szinapszis Studio jegyzetfüzetből válassza ki a **Spark History Server** elemet a feladatok végrehajtása kimeneti cellából vagy a jegyzetfüzet dokumentum alján található állapot panelen. Válassza a **Munkamenet részletei** lehetőséget.

   ![A Spark History Server 1 elindítása](./media/apache-spark-history-server/launch-history-server2.png "A Spark History Server elindítása")

2. Válassza ki a **Spark History Server** elemet a kicsúsztatható panelen.

   ![A Spark History Server 2 elindítása](./media/apache-spark-history-server/launch-history-server.png "A Spark History Server elindítása")

## <a name="explore-the-data-tab-in-spark-history-server"></a>A Spark History Server adatlapjának megismerése

Válassza ki a megtekinteni kívánt feladatokhoz tartozó AZONOSÍTÓJÚ feladatot. Az adatnézet beszerzéséhez az eszköz menüjében válassza az **Adatvédelem** lehetőséget. Ez a szakasz bemutatja, hogyan végezheti el a különböző feladatokat az adatok lapon.

* A lapokat külön kiválasztva jelölje be a **bemenetek**, a **kimenetek** és a **táblák műveleteit** .

    ![A Spark-alkalmazások lapjai](./media/apache-spark-history-server/apache-spark-data-tabs.png)

* Másolja az összes sort a **Másolás** lehetőség kiválasztásával.

    ![A Spark-alkalmazás másolására vonatkozó adatgyűjtés](./media/apache-spark-history-server/apache-spark-data-copy.png)

* Mentse az összes fájlt CSV-fájlként a **CSV** kiválasztásával.

    ![A Spark-alkalmazás mentésének adatvédelme](./media/apache-spark-history-server/apache-spark-data-save.png)

* Keresés kulcsszavak beírásával a mezők **kereséséhez**. A keresési eredmények azonnal megjelennek.

    ![A Spark-alkalmazások keresésére vonatkozó adatgyűjtés](./media/apache-spark-history-server/apache-spark-data-search.png)

* A táblázat rendezéséhez válassza ki az oszlop fejlécét, és válassza ki a pluszjelet a sorok kibontásához, vagy válassza a mínusz jelet a sorok összecsukásához.

    ![A Spark-alkalmazás táblázatának adatkészlete](./media/apache-spark-history-server/apache-spark-data-table.png)

* Töltsön le egyetlen fájlt a **részleges Letöltés** lehetőség kiválasztásával. A kiválasztott fájlt a rendszer letölti a helyi webhelyre. Ha a fájl már nem létezik, egy új lap jelenik meg, amely hibaüzenetet jelenít meg.

    ![A Spark-alkalmazás letöltési sorával kapcsolatos adatgyűjtés](./media/apache-spark-history-server/sparkui-data-download-row.png)

* Teljes elérési út vagy relatív elérési út másolásához válassza a **teljes elérési út másolása** vagy a **relatív elérési út** másolása lehetőséget a legördülő menüből. Azure Data Lake Storage fájlok esetében **nyissa meg a Azure Storage Explorer** elindítja Azure Storage Explorer, majd a bejelentkezéskor megkeresi a mappát.

    ![A Spark-alkalmazás másolási útvonalának elérési útja](./media/apache-spark-history-server/sparkui-data-copy-path.png)

* A táblázat alatti oldalszámok kiválasztásával navigáljon a lapokhoz, ha túl sok sor van megjelenítve egy oldalon.

    ![A Spark-alkalmazásra vonatkozó adatgyűjtés oldal](./media/apache-spark-history-server/apache-spark-data-page.png)

* Mutasson az **adatok** melletti kérdőjelre az elemleírás megjelenítéséhez, vagy válassza a kérdőjelet további információk megadásához.

    ![Adatok a Spark-alkalmazásról – további információ](./media/apache-spark-history-server/sparkui-data-more-info.png)

* Küldjön visszajelzést a problémákkal kapcsolatos visszajelzések **megadásával.**

    ![A Spark Graph újra visszajelzést küld nekünk](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="graph-tab-in-apache-spark-history-server"></a>Apache Spark History-kiszolgáló gráf lapja

Válassza ki a megtekinteni kívánt feladatokhoz tartozó AZONOSÍTÓJÚ feladatot. Ezután válassza a **Graph (gráf** ) lehetőséget az eszköz menüben a feladatok gráf nézetének lekéréséhez.

### <a name="overview"></a>Áttekintés

A feladatokról a generált feladatok diagramján tekinthet meg áttekintést. Alapértelmezés szerint a Graph megjeleníti az összes feladatot. Ezt a nézetet **feladattípus** alapján szűrheti.

![Spark-alkalmazás és a feladatütemezés-feladatok azonosítója](./media/apache-spark-history-server/apache-spark-graph-jobid.png)

### <a name="display"></a>Megjelenítés

Alapértelmezés szerint a **folyamatjelző** megjelenítés van kiválasztva. Az adatfolyamatot úgy is megtekintheti, ha a **Megjelenítés** legördülő listában az **olvasás** vagy **írás** lehetőséget választja.

![Spark-alkalmazás és-feladatok diagramjának megjelenítése](./media/apache-spark-history-server/sparkui-graph-display.png)

A Graph csomópont a hő jelmagyarázatban látható színeket jeleníti meg.

![Spark-alkalmazás és a Job Graph hő](./media/apache-spark-history-server/sparkui-graph-heatmap.png)

### <a name="playback"></a>Lejátszás

A feladatok lejátszásához válassza a **Lejátszás** lehetőséget. A **Leállítás lehetőség kiválasztásával** bármikor leállíthatja a leállítást. A feladat színei különböző állapotokat mutatnak vissza a lejátszáskor:

|Szín|Értelmezés|
|-|-|
|Zöld|Sikeres: a feladatok sikeresen befejeződtek.|
|Narancssárga|Újrapróbálkozás: a feladatok olyan példányai, amelyek sikertelenek voltak, de nem befolyásolják a feladat végső eredményét. Ezek a feladatok ismétlődő vagy újrapróbálkozási példányokkal rendelkeztek, amelyek később esetleg sikeresek lehetnek.|
|Kék|Fut: a feladat fut.|
|Fehér|Várakozás vagy kihagyva: a feladat futásra vár, vagy a szakasz ki lett hagyva.|
|Piros|Sikertelen: a feladat sikertelen volt.|

Az alábbi képen zöld, narancssárga és kék állapotú színek láthatók.

![Spark-alkalmazás és a feladatütemezés színmintája, Futtatás](./media/apache-spark-history-server/sparkui-graph-color-running.png)

Az alábbi képen a zöld és a fehér állapot színei láthatók.

![Spark-alkalmazás és a feladatütemezés színének mintája, kihagyás](./media/apache-spark-history-server/sparkui-graph-color-skip.png)

Az alábbi képen a vörös és a zöld állapot színei láthatók.

![A Spark-alkalmazás és a feladatütemezés színes mintája sikertelen](./media/apache-spark-history-server/sparkui-graph-color-failed.png)

> [!NOTE]  
> Az egyes feladatokhoz tartozó lejátszás engedélyezett. Hiányos feladatok esetén a lejátszás nem támogatott.

### <a name="zoom"></a>Zoom

Az egér görgetésével nagyíthatja és kicsinyítheti a feladatok gráfját, vagy kiválaszthatja a **nagyítást, hogy illeszkedjen** a képernyőhöz.

![A Spark-alkalmazás és a feladatok gráfjának méretezése](./media/apache-spark-history-server/sparkui-graph-zoom2fit.png)

### <a name="tooltips"></a>Elemleírások

Mutasson a Graph csomópontra az elemleírás megjelenítéséhez, amikor a feladatok sikertelenek, és válasszon egy szakaszt a szakasz oldalának megnyitásához.

![Spark-alkalmazás és a feladatütemezés elemleírása](./media/apache-spark-history-server/sparkui-graph-tooltip.png)

A feladatütemezés lapon egy elemleírás és egy kis ikon jelenik meg, ha olyan feladatokkal rendelkeznek, amelyek megfelelnek a következő feltételeknek:

|Feltétel|Leírás|
|-|-|
|Az adattorzítás|az adatolvasási méret > a fázisban lévő összes feladat átlagos adatolvasási mérete, valamint az adatolvasási méret > 10 MB|
|Időbeli döntés|a végrehajtási idő > a szakasz összes feladatának átlagos végrehajtási időpontja * 2 és végrehajtási idő > 2 percen belül|
   
![Spark-alkalmazás és a feladatok gráfjának döntési ikonja](./media/apache-spark-history-server/sparkui-graph-skew-icon.png)

### <a name="graph-node-description"></a>Gráf-csomópont leírása

A feladatütemezés csomópont a következő információkat jeleníti meg az egyes szakaszok esetében:

  * ID.
  * Név vagy leírás.
  * A feladat teljes száma.
  * Olvasott adatok: a bemeneti méret és a shuffle olvasási méret összege.
  * Adatírás: a kimeneti méret és a shuffle írási méret összege.
  * Végrehajtási idő: az első kísérlet kezdési időpontja és a legutóbbi kísérlet befejezési időpontja közötti idő.
  * Sorok száma: a bemeneti rekordok, a kimeneti rekordok, a shuffle olvasási rekordok és a shuffle írási rekordok összege.
  * Haladás.

    > [!NOTE]  
    > Alapértelmezés szerint a feladatütemezés csomópontja az egyes szakaszok utolsó próbálkozásának adatait jeleníti meg (kivéve a fázis végrehajtási idejét). A lejátszás során azonban a Graph csomópont az egyes kísérletek információit jeleníti meg.
    >  
    > Az olvasási és írási adatméret 1MB = 1000 KB = 1000 * 1000 bájt.

### <a name="provide-feedback"></a>Visszajelzés küldése

Küldjön visszajelzést a problémákkal kapcsolatos visszajelzések **megadásával.**

![Spark-alkalmazás és a feladatok gráfjának visszajelzése](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="explore-the-diagnosis-tab-in-apache-spark-history-server"></a>A Apache Spark History Server diagnosztika lapjának megismerése

A diagnosztika lap megnyitásához válassza ki a kívánt AZONOSÍTÓJÚ feladatot. Ezután válassza a **diagnosztika** lehetőséget az eszköz menüjében a feladatok diagnosztizálási nézetének lekéréséhez. A diagnosztika lapon szerepel az **adatok eldöntése**, az **időeltérés** és a **végrehajtó általi használat elemzése**.

A lapfülek kiválasztásával keresse meg az **adatok eldöntését**, az **időeltérést** és a **végrehajtó használatának elemzését** .

![SparkUI-diagnosztika adatferdítő lapja](./media/apache-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>Az adattorzítás

Amikor kiválasztja az **adatok eldöntése** fület, a megadott paraméterek alapján megjelennek a megfelelő ferde feladatok.

* **Paraméterek megadása** – az első szakasz azokat a paramétereket jeleníti meg, amelyek az adatok eldöntésének észlelésére szolgálnak. Az alapértelmezett szabály a következő: a tevékenységi adatok olvasása nagyobb, mint háromszor az átlagos feladathoz tartozó adat, az olvasás pedig több mint 10 MB. Ha meg szeretné határozni saját szabályát a ferde feladatokhoz, kiválaszthatja a paramétereket. Ennek megfelelően frissül a **ferde szakasz** és a **ferde karakteres** szakasz.

* **Ferde fázis** – a második szakasz a szakaszokat jeleníti meg, amelyek a fent megadott feltételeknek megfelelő ferde feladatokat látnak el. Ha egy szakaszban több elferdített feladat van, akkor a ferde szakasz tábla csak a leginkább ferde feladatot jeleníti meg (például a legnagyobb adat az adatok eldöntéséhez).

    ![sparkui-diagnosztika adattorzításának lapja](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

* **Diagram eldöntése** – ha a döntési szakasz tábla egyik sorát választja ki, az elferdítő diagram több feladat-elosztási részletet jelenít meg az adatok olvasása és végrehajtása során. A ferde feladatok piros színnel vannak megjelölve, és a normál feladatok kék színnel vannak megjelölve. A diagram legfeljebb 100 minta feladatot jelenít meg, a feladat részletei pedig a jobb alsó panelen jelennek meg.

    ![sparkui-diagram a 10. fázishoz](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>Időbeli döntés

Az **idő megdöntése** lap a feladat végrehajtási ideje alapján elferdített feladatokat jeleníti meg.

* **Paraméterek megadása** – az első szakasz azokat a paramétereket jeleníti meg, amelyek az időeltérés észlelésére szolgálnak. Az időeltérés észlelésének alapértelmezett feltételei: a feladat-végrehajtási idő nagyobb, mint az átlagos végrehajtási idő háromszorosa, a feladat végrehajtási ideje pedig 30 másodpercnél nagyobb. A paramétereket igény szerint módosíthatja. A **ferde szakasz** és az elferdítés **diagram** a megfelelő szakaszokat és feladatokat jeleníti meg, ugyanúgy, mint a fenti **adattorzítási** lap.

* Adja meg az **időeltérést**, majd a szűrt **eredmény a szakasz** paramétereinek megadása szakaszban, a **Paraméterek** beállítása szakaszban látható. Válasszon egy elemet a **ferde fázis** szakaszban, majd a megfelelő diagramot a section3-ben, a feladat részletei pedig a jobb alsó panelen jelennek meg.

    ![sparkui-diagnosztika időtartamának eldöntése szakasz](./media/apache-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis"></a>Végrehajtói használat elemzése

A végrehajtói használati gráf megjeleníti a Spark-feladatok végrehajtójának kiosztását és futási állapotát.  

1. Válassza **a végrehajtói használat elemzése** lehetőséget, majd a végrehajtói használattal kapcsolatos négy típusú görbét, beleértve a **lefoglalt végrehajtókat**, a **végrehajtók**, az **üresjárati végrehajtók** és a **maximális végrehajtó példányok** futtatását. A lefoglalt végrehajtók esetében a "végrehajtó által hozzáadott" vagy "végrehajtó eltávolított" esemény növeli vagy csökkenti a lefoglalt végrehajtókat. További összehasonlításért tekintse meg az "esemény ütemezése" részt a "feladatok" lapon.

   ![sparkui-diagnosztikai végrehajtók lap](./media/apache-spark-history-server/sparkui-diagnosis-executors.png)

2. A szín ikonra kattintva kiválaszthatja vagy kiválaszthatja a megfelelő tartalmat az összes piszkozatban.

    ![sparkui diagnosztizálása diagram kiválasztása](./media/apache-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="known-issues"></a>Ismert problémák

A rugalmasan elosztott adatkészletek (RDD) használatával a bemeneti/kimeneti adatok nem jelennek meg az adatok lapon.

## <a name="next-steps"></a>Következő lépések

- [Azure Synapse Analytics](../overview-what-is.md)
- [.NET Apache Spark dokumentációhoz](/dotnet/spark)

