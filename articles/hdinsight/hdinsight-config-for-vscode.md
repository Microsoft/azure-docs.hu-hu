---
title: Az Azure HDInsight konfigurációs beállításainak ismertetése
description: Az Azure HDInsight bővítmény konfigurációjának bevezetése.
ms.service: hdinsight
ms.topic: how-to
ms.date: 04/07/2021
ms.custom: devx-track-python
ms.openlocfilehash: a9a444c2557ea17114123cbd5084cbbd9fe6dac6
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259997"
---
# <a name="azure-hdinsight-configuration-settings-reference"></a>Az Azure HDInsight konfigurációs beállításainak ismertetése

A Spark & kaptár Tools bővítmény a Visual Studio Code-hoz kiválóan konfigurálható. Ez az oldal ismerteti azokat a kulcs-beállításokat, amelyekkel használható.  

A VS Code-ban található beállításokkal kapcsolatos általános információkért tekintse meg a [felhasználói és munkaterület beállításait](https://code.visualstudio.com/docs/getstarted/settings), valamint az előre definiált változó támogatásával kapcsolatos tudnivalókat tartalmazó [változókat](https://code.visualstudio.com/docs/editor/variables-reference) .

## <a name="open-the-azure-hdinsight-configuration"></a>Az Azure HDInsight konfigurációjának megnyitása

1. Először nyisson meg egy mappát a munkaterület beállításainak létrehozásához.
2. Nyomja le a **CTRL + SHIFT + P** billentyűkombinációt , vagy navigáljon a  ->  **parancs paletta** megtekintéséhez... az összes parancs megjelenítéséhez.
3. Keresési **készlet konfigurációja**
4. Bontsa ki a **bővítmények** csomópontot a bal oldali könyvtárban, és válassza a **HDInsight konfiguráció** elemet.

 ![HDI konfigurációs rendszerkép](./media/HDInsight-config-for-vscode/HDInsight-config-for-vscode.png)

## <a name="general-settings"></a>Általános beállítások   

|  Tulajdonság   | Alapértelmezett | Description   |
| ----- | ----- |----- |
| HDInsight: Azure-környezet | Azure | Azure-környezet |
| HDInsight: nyílt felmérési hivatkozás letiltása | Jelölje be | A HDInsight-felmérés megnyitásának engedélyezése/letiltása |
| HDInsight: Pyspark-telepítés kihagyásának engedélyezése | Nincs bejelölve | Pyspark-telepítés kihagyásának engedélyezése/letiltása |
| HDInsight: bejelentkezési tippek engedélyezése | Nincs bejelölve | Ha ez a beállítás be van jelölve, az Azure-ba való bejelentkezéskor egy üzenet jelenik meg |
| HDInsight: korábbi bővítmény verziója | Az aktuális kiterjesztés verziószámának megjelenítése | Az előző bővítmény verziójának megjelenítése|
| HDInsight: az eredmények betűkészletének családja | -Apple-System, BlinkMacSystemFont, Segoe WPC, Segoe UI, HelveticaNeue-Light, Ubuntu, droid Sans, Sans-Serif | Adja meg az eredmények rácsának betűkészlet-családját; Állítsa üresre a szerkesztő betűkészletének használatához |
| HDInsight: az eredmények betűmérete | 13 |Adja meg az eredmények övezzétek betűméretét; Állítsa üresre a szerkesztő méretének használatához |
| HDInsight-fürt: csatolt fürt | -- | Csatolt fürtök URL-címei. Szerkesztheti a beállított JSON-fájlt is |
| HDInsight struktúra: honosítás alkalmazása | Nincs bejelölve | Választható Konfigurációs beállítások a VSCode konfigurált területi beállításához (a VSCode újraindítása szükséges a beállítások érvénybe léptetéséhez)|
| HDInsight struktúra: másolás a fejlécek közé | Nincs bejelölve | Választható Konfigurációs beállítás az eredmények nézetből való másolásához |
| HDInsight struktúra: az új sor eltávolításának másolása | Jelölje be | Választható Az eredmények nézetből származó többsoros eredmények másolásának konfigurációs beállításai |
| HDInsight struktúra › formátum: oszlopok definícióinak igazítása oszlopokban | Nincs bejelölve | Az oszlop definíciójának igazítása |
| HDInsight struktúra › formátum: adattípus ház | Nincs | Az adattípusokat nagybetűs, kisbetűs vagy none (nem formázott) formátumban kell formázni. |
| HDInsight struktúra › formátum: kulcsszó ház | Nincs | Ha a kulcsszavakat nagybetűs, kisbetűs vagy none (nem formázott) formátumban kell formázni |
| HDInsight-struktúra › formátum: vessző elhelyezése a következő utasítás előtt | Nincs bejelölve | A "mycolumn1" végpont helyett pontosvesszőt kell elhelyezni a lista minden utasításának elején, például ", mycolumn2"|
| HDInsight-struktúra › formátum: az új sorban lévő SELECT utasítás hivatkozásai | Nincs bejelölve | A SELECT utasításban lévő objektumokra mutató hivatkozásokat különálló sorokra kell bontani? Például a "SELECT C1, C2, T1", a C1 és a C2 is külön sorban
| HDInsight struktúra: hibakeresési adatok naplózása | Nincs bejelölve | Választható Hibakeresési kimenet naplózása a VS Code konzolon (Súgó – > váltógomb Fejlesztői eszközök) 
| HDInsight struktúra: az üzenetek alapértelmezett megnyitása megnyitva | Jelölje be | Alapértelmezés szerint az üzenetek ablaktábla megnyitásának értéke TRUE (igaz). hamis: lezárva|
| HDInsight struktúra: eredmények betűkészletének családja | -Apple-System, BlinkMacSystemFont, Segoe WPC, Segoe UI, HelveticaNeue-Light, Ubuntu, droid Sans, Sans-Serif | Adja meg az eredmények rácsának betűkészlet-családját; Állítsa üresre a szerkesztő betűkészletének használatához |
| HDInsight struktúra: az eredmények betűmérete | 13 | Az eredmények rács betűméretének beállítása; Állítsa üresre a szerkesztő méretének használatához |
| HDInsight struktúra › mentés CSV-ként: fejlécek belefoglalása | Jelölje be | Választható Ha az érték TRUE (igaz), az oszlopfejlécek az eredmények CSV-ként való mentésekor szerepelnek. |
| HDInsight struktúra: parancsikonok | -- | Az eredmények ablakhoz kapcsolódó parancsikonok |
| HDInsight struktúra: kötegelt idő megjelenítése| Nincs bejelölve | Választható Az egyes kötegekhez tartozó végrehajtási idő jelenjen meg |
| HDInsight struktúra: felosztott ablaktábla kijelölése | következő | Választható Azon konfigurációs beállítások, amelyekhez az új eredményhalmaz ablakait meg kell nyitni |
| HDInsight-feladatok beküldése: fürt conf | -- | Fürtkonfiguráció |
| HDInsight-feladatok beküldése: Livy conf | -- | Livy-konfiguráció. BEJEGYZÉS/kötegek |
| HDInsight Jupyter: eredmények hozzáfűzése| Jelölje be | Azt határozza meg, hogy az eredményeket az eredmények ablakhoz fűzi-e, és hogy az eredmény megjelenjen-e. |
| HDInsight Jupyter: nyelvek | -- | Alapértelmezett beállítások nyelv szerint. |
| HDInsight Jupyter › napló: részletes | Nincs bejelölve | Ha a részletes naplózás engedélyezése |
| HDInsight Jupyter › jegyzetfüzet: indítási argumentumok | Elem adható hozzá | "jupyter notebook" parancssori argumentumai. Minden argumentum egy különálló elem a tömbben. A teljes listát a "jupyter notebook--help" típust egy terminál ablakban tekintheti meg. |
| HDInsight Jupyter › jegyzetfüzet: indítási mappa | $ {workspaceRoot} |-- |
| HDInsight Jupyter: a Python bővítmény engedélyezve | Jelölje be | Az pySpark interaktív feladatok elküldésekor használja az MS-Python bővítmény Python-Interactive-ablakát. Ellenkező esetben használja a saját jupyter-ablakát |
| HDInsight Spark.NET: 7z | C:\Program Files\7-Zip | 7z.exe <elérési útja> |
| HDInsight Spark.NET: HADOOP_HOME | D:\winutils | <elérési út csak bin\winutils.exe> Windows operációs rendszerhez |
| HDInsight Spark.NET: JAVA_HOME | C:\Program Files\Java\ jdk1.8.0_201 \ | A Java Home elérési útja|
| HDInsight Spark.NET: SCALA_HOME | C:\Program Files (x86) \scala\ | A Scala Home elérési útja |
| HDInsight Spark.NET: SPARK_HOME | D:\spark-2.3.3-bin-hadoop2.7\ | A Spark Home elérési útja |
| Struktúra: lekérdezési eredmények megőrzése lapok | Nincs bejelölve | Struktúra PersistQueryResultTabs |
| Struktúra: kibontott ablaktábla kijelölése | következő | Választható Azon konfigurációs beállítások, amelyekhez az új eredményhalmaz ablakait meg kell nyitni |
| Struktúra interaktív: végrehajtható mappa másolása | Nincs bejelölve | Ha a struktúra interaktív szolgáltatás futásidejű mappáját a felhasználó tmp mappájába másolja |
| HQL interaktív kiszolgáló: burkoló port | 13424 | A kaptár interaktív szolgáltatási portja |
| HQL nyelvi kiszolgáló: nyelvi burkoló port | 12342 | A kaptár Language Service port kiszolgálók figyelik. |
| HQL nyelvi kiszolgáló: a problémák maximális száma | 100 | A kiszolgáló által létrehozott problémák maximális számát szabályozza. |
| Szinapszis Spark-számítás: szinapszis Spark számítási Azure-környezet | üres | szinapszis Spark számítási Azure-környezet |
| Szinapszis Spark Pool-feladatok beküldése: Livy conf | -- | Livy-konfiguráció. BEJEGYZÉS/kötegek
| Szinapszis Spark-készlet feladatainak beküldése: szinapszis Spark-készlet fürtje conf | -- | Szinapszis Spark-készlet konfigurálása |


## <a name="next-steps"></a>Következő lépések

- A VSCode készült Azure HDInsight kapcsolatos további információkért lásd: [Spark & kaptár a Visual Studio Code-eszközökhöz](https://docs.microsoft.com/sql/big-data-cluster/spark-hive-tools-vscode).
- A Spark & kaptár Visual Studio Code-hoz való használatát bemutató videó: [spark &-struktúra a Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706)-hoz.