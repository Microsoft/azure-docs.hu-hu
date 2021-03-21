---
title: 'ML Studio (klasszikus): betanítási szolgáltatás importálása – Azure'
description: Adatok importálása a Azure Machine Learning Studioba (klasszikus) különböző adatforrásokból. Ismerje meg, hogy milyen adattípusok és adatformátumok támogatottak.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/01/2019
ms.openlocfilehash: db5f3cc4b9530c4aeac40786756b36cc0ac98728
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100520374"
---
# <a name="import-your-training-data-into-azure-machine-learning-studio-classic-from-various-data-sources"></a>Betanítási adatok importálása Azure Machine Learning Studioba (klasszikus) különböző adatforrásokból

**a következőkre vonatkozik:** ![ A következőre vonatkozik:. ](../../../includes/media/aml-applies-to-skus/yes.png) A Machine Learning Studio (klasszikus) ![ nem vonatkozik a következőre:.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


Ha Machine Learning Studio (klasszikus) használatával szeretne saját adatait használni a prediktív elemzési megoldások fejlesztéséhez és betanításához, a következő adatok használhatók: 

* **Helyi fájl** – a saját munkaterületen lévő adatkészlet-modul létrehozásához a merevlemezről előre be kell tölteni a helyi adatokat.
* **Online adatforrások** – az [adatok importálása][import-data] modul használatával férhet hozzá az egyes online forrásokból származó adatokhoz, miközben a kísérlet fut
* **Machine learning Studio (klasszikus) kísérlet** – az adatkészletként mentett adatokat Machine learning Studio (klasszikus)
* [**SQL Server adatbázis**](use-data-from-an-on-premises-sql-server.md) – az adatok manuális másolása nélkül használhat SQL Server-adatbázis adatait

> [!NOTE]
> A Machine Learning Studio (klasszikus) szolgáltatásban számos minta adathalmaz érhető el, amelyeket az adatképzéshez használhat. Ezekről további információt [a minta adatkészletek használata Azure Machine learning Studio (klasszikus)](use-sample-datasets.md)című témakörben talál.

## <a name="prepare-data"></a>Adatok előkészítése

A Machine Learning Studio (klasszikus) úgy van kialakítva, hogy téglalap-vagy táblázatos adatokkal működjön, például a szöveges adatokat, amelyek egy adatbázisból tagolt vagy strukturált adatokat használnak, bár bizonyos esetekben nem téglalap alakú adatok is használhatók.

A legjobb, ha az adatai viszonylag tisztaak, mielőtt a Studio (klasszikus) szolgáltatásba importálja őket. Tegyük fel például, hogy ügyelnie kell az olyan problémákra, mint például a nem idézett karakterláncok.

Vannak azonban olyan modulok a Studióban (klasszikus), amelyek lehetővé teszik az adatfeldolgozást a kísérletben az Adatimportálás után. Az Ön által használt gépi tanulási algoritmustól függően előfordulhat, hogy el kell döntenie, hogyan fogja kezelni az adatok szerkezeti problémáit, például a hiányzó értékeket és a ritka adatmennyiségeket, és vannak olyan modulok, amelyek segíthetnek. A függvényeket végrehajtó modulok esetében tekintse meg a modul paletta **adat-átalakítási** szakaszát.

A kísérlet bármely pontján megtekintheti vagy letöltheti a modul által előállított adatokat a kimeneti portra kattintva. A modultól függően előfordulhat, hogy más letöltési lehetőségek is rendelkezésre állnak, vagy a Studio (klasszikus) webböngészőjében megjelenítheti az adatait.

## <a name="supported-data-formats-and-data-types"></a>Támogatott adatformátumok és adattípusok

A kísérletbe több adattípust is importálhat, attól függően, hogy milyen mechanizmust használ az adatok importálásához, és honnan származik:

* Egyszerű szöveg (. txt)
* Vesszővel tagolt értékek (CSV) fejléctel (. csv) vagy anélkül (.nh.csv)
* Tabulátorral tagolt értékek (TSV) fejléctel (. TSV) vagy anélkül (. NH. TSV)
* Excel-fájl
* Azure-tábla
* Hive-tábla
* SQL Database-táblázat
* OData-értékek
* SVMLight-adatok (. SVMLight) (lásd a formátumra vonatkozó [SVMLight-definíciót](http://svmlight.joachims.org/) )
* Attribútum-kapcsolati fájlformátum (ARFF) adatai (. ARFF) (lásd a [ARFF definícióját](https://www.cs.waikato.ac.nz/ml/weka/arff.html) a formázási adatokhoz).
* Zip-fájl (. zip)
* R-objektum vagy munkaterület-fájl (. RData

Ha olyan formátumú adatokat importál, mint például a metaadatokat tartalmazó ARFF, a Studio (klasszikus) ezt a metaadatokat használja az egyes oszlopok fejlécének és adattípusának meghatározásához.

Ha olyan adatokat importál, mint például a TSV vagy CSV formátum, amely nem tartalmazza ezt a metaadatokat, a Studio (klasszikus) az adatok mintavételezésével kikövetkezteti az egyes oszlopok adattípusát. Ha az adathalmazok nem rendelkeznek oszlopfejlécek, a Studio (klasszikus) alapértelmezett neveket is tartalmaz.

A [metaadatok szerkesztése][edit-metadata] modul használatával explicit módon megadhatja vagy módosíthatja az oszlopok fejléceit és adattípusait.

A Studio (klasszikus) a következő adattípusokat ismeri fel:

* Sztring
* Egész szám
* Dupla
* Logikai érték
* DateTime
* időtartam

A Studio egy ***adattábla*** nevű belső adattípust használ a modulok közötti adatátvitelhez. Az adatokat adattábla formátumba explicit módon konvertálhatja az [átalakítás adatkészletbe][convert-to-dataset] modul használatával.

Az adattáblán kívül más formátumokat is tartalmazó modulok a következő modulba való átadásuk előtt csendes adattáblára konvertálják az adattáblát.

Szükség esetén átalakíthatja az adattábla formátumát CSV-, TSV-, ARFF-vagy SVMLight-formátumba más konverziós modulok használatával.
A függvényeket végrehajtó modulok esetében tekintse meg a modul paletta **adatformátum-konverziók** szakaszát.

## <a name="data-capacities"></a>Adatkapacitások

A Machine Learning Studio (klasszikus) moduljai a gyakori felhasználási esetekben legfeljebb 10 GB-nyi sűrű numerikus adatokat támogató adatkészleteket támogatnak. Ha egy modul egynél több bemenetből fogad adatokat, a bemenet összesített mérete nem haladhatja meg a 10 GB-ot. A struktúra vagy a Azure SQL Database lekérdezések használatával nagyobb adatkészleteket is megadhat, vagy az adatok importálása előtt használhatja a Learning by Counts előfeldolgozást is.  

A szolgáltatásnormalizálás során a következő, 10 GB alá korlátozott adattípusok bővíthetők nagyobb adatkészletekké:

* Ritka
* Kategorikus
* Sztringek
* Bináris adatok

A következő modulok 10 GB-nál kisebb adatkészletekre vannak korlátozva:

* Ajánló modulok
* SMOTE (Synthetic Minority Oversampling Technique) modul
* Parancsfájlkezelési modulok: R, Python, SQL
* Olyan modulok, amelyeknél a kimeneti adatok mérete meghaladhatja a bemeneti adatok méretét; például az egyesítés vagy a szolgáltatáskivonatolás
* Kereszt-ellenőrzés, modell-hiperparaméterek beállítása, sorszámregresszió és multi-osztályú osztályozás nagyszámú ismétlés esetében

A pár GB-nál nagyobb adatkészletek esetében töltse fel az adatokat az Azure Storage-ba vagy a Azure SQL Databaseba, vagy használja az Azure HDInsight-t ahelyett, hogy közvetlenül egy helyi fájlból kellene feltöltenie.

A képadatokról a lemezképek [importálása](/azure/machine-learning/studio-module-reference/import-images#bkmk_Notes) modul dokumentációjában talál információt.

## <a name="import-from-a-local-file"></a>Importálás helyi fájlból

Feltölthet egy adatfájlt a merevlemezről a Studio (klasszikus) betanítási adatként való használatra. Az adatfájlok importálásakor létre kell hoznia egy adatkészlet-modult, amely készen áll a munkaterületen végzett kísérletekhez való használatra.

Az adatok helyi merevlemezről történő importálásához tegye a következőket:

1. Kattintson a Studio (klasszikus) ablakának alján található **+ új** elemre.
2. Válassza az **adatkészlet** és **a helyi fájl** lehetőséget.
3. Az **új adatkészlet feltöltése** párbeszédpanelen tallózással keresse meg a feltölteni kívánt fájlt.
4. Adjon meg egy nevet, azonosítsa az adattípust, és opcionálisan adja meg a leírást. A leírás használata ajánlott – lehetővé teszi, hogy rögzítse az adatok jövőbeli felhasználásakor megjegyezhető adatok jellemzőit.
5. Ez a jelölőnégyzet egy **meglévő adatkészlet új verziója** , amely új adatokat tartalmazó meglévő adatkészlet frissítését teszi lehetővé. Ehhez jelölje be ezt a jelölőnégyzetet, majd írja be egy meglévő adatkészlet nevét.

![Új adatkészlet feltöltése](./media/import-data/upload-dataset-from-local-file.png)

A feltöltési idő az adatok méretétől és a szolgáltatáshoz való kapcsolódás sebességétől függ. Ha tudja, hogy a fájl hosszú időt vesz igénybe, megteheti, hogy a Studio (klasszikus) szolgáltatáson belül más dolgokat is végrehajthat. Azonban a böngésző bezárása az adatfeltöltés befejezése előtt a feltöltés sikertelen lesz.

Az adatokat az adatkészletek moduljában tárolja a rendszer, és a munkaterületen bármilyen kísérlethez elérhetővé válik.

A kísérletek szerkesztésekor a modul paletta **mentett adatkészletek** listájában található **saját adatkészletek** listájában megtalálhatja a feltöltött adatkészleteket. Az adatkészletet a kísérleti vászonra húzva áthelyezheti, ha az adatkészletet további elemzési és gépi tanulási célra szeretné használni.

## <a name="import-from-online-data-sources"></a>Importálás online adatforrásokból

Az [adatimportálási][import-data] modul használatával a kísérlet során különböző online adatforrásokból származó adatok importálhatók, miközben a kísérlet fut.

> [!NOTE]
> Ez a cikk az [adatok importálása][import-data] modullal kapcsolatos általános információkat tartalmazza. Az adattípusokkal, a formátumokkal, a paraméterekkel és a gyakori kérdésekre adott válaszokkal kapcsolatos részletesebb információkért tekintse meg az [adatimportálási][import-data] modul modul-referenciát ismertető témakörét.

Az [adatok importálása][import-data] modul használatával számos online adatforrásból elérheti az adatait, miközben a kísérlet fut:

* HTTP-t használó webes URL-cím
* Hadoop a HiveQL használatával
* Azure Blob Storage
* Azure-tábla
* Azure SQL Database. SQL felügyelt példány vagy SQL Server
* Adatcsatorna-szolgáltató, OData jelenleg
* Azure Cosmos DB

Mivel ez a betanítási információ a kísérlet futtatása közben is elérhető, csak ebben a kísérletben érhető el. Az adatkészletek moduljában tárolt adatokat összehasonlítva a munkaterületen lévő összes kísérlet elérhetővé válik.

Ha az online adatforrásokat a Studio (klasszikus) kísérletben szeretné elérni, adja hozzá az [adatimportálási][import-data] modult a kísérlethez. Az adatforrás kiválasztásához és konfigurálásához válassza az **adat importálása varázslót** a **Tulajdonságok** szakaszban a részletes útmutatóban. Azt is megteheti, hogy manuálisan kiválasztja az **adatforrást** a **Tulajdonságok** területen, és megadja az adatok eléréséhez szükséges paramétereket.

A támogatott online adatforrások az alábbi táblázatban vannak részletezve. Ez a táblázat a támogatott fájlformátumokat és az adatok eléréséhez használt paramétereket is összegzi.

> [!IMPORTANT]
> Az adatok [importálása][import-data] és [exportálása][export-data] modulok jelenleg csak a klasszikus üzemi modellel létrehozott Azure Storage-ból olvashatók és írhatók. Ez azt jelenti, hogy az új Azure Blob Storage fiók típusa, amely egy gyors tárolási hozzáférési szintet vagy ritka elérésű tárolási szintet biztosít, még nem támogatott.
>
> Általában minden olyan Azure Storage-fiók, amelyet korábban hozott létre, mielőtt a szolgáltatás elérhetővé vált, nem kell érintenie a funkciót.
> Ha létre kell hoznia egy új fiókot, válassza a **klasszikus** lehetőséget a telepítési modellhez, vagy használja a Resource Managert, és válassza az **általános célú** elemet a **fiók típusaként** a **blob Storage** helyett.
>
> További információt az [Azure Blob Storage: gyakori és](../../storage/blobs/storage-blob-storage-tiers.md)ritka elérésű tárolási szintek című témakörben talál.

### <a name="supported-online-data-sources"></a>Támogatott online adatforrások
A Azure Machine Learning Studio (klasszikus) **adatimportálási** modul a következő adatforrásokat támogatja:

| Adatforrás | Description | Paraméterek |
| --- | --- | --- |
| Webes URL-cím HTTP-n keresztül |A vesszővel tagolt értékek (CSV), tabulátorral tagolt értékek (TSV), attribútum-kapcsolati fájlformátum (ARFF) és a SVM-formátumok támogatása a HTTP-t használó bármely webes URL-címről |<b>URL</b>: a fájl teljes nevét adja meg, beleértve a webhely URL-címét és a fájlnevet bármilyen kiterjesztéssel. <br/><br/><b>Adatformátum</b>: a támogatott adatformátumok egyikét adja meg: CSV, TSV, ARFF vagy SVM. Ha az adatsorok fejléctel rendelkeznek, az oszlopnevek hozzárendelésére szolgál. |
| Hadoop/HDFS |Beolvassa az adatokat a Hadoop-ben lévő elosztott tárolóból. A HiveQL, egy SQL-szerű lekérdezési nyelvet használva adhatja meg a kívánt adatértékeket. A HiveQL az adatok összesítéséhez és az Adatszűrés végrehajtásához is használható, mielőtt hozzáadja az adatokat a studióhoz (klasszikus). |<b>Struktúra adatbázis-lekérdezése</b>: megadja az adatlétrehozáshoz használt kaptár-lekérdezést.<br/><br/><b>HCatalog-kiszolgáló URI-ja</b> : a fürt nevét a fürt neve *&lt; &gt; . azurehdinsight.net* formátumban adja meg.<br/><br/><b>Hadoop felhasználói fiók neve</b>: a fürt kiépítéséhez használt Hadoop-felhasználói fiók nevét adja meg.<br/><br/><b>Hadoop felhasználói fiók jelszava</b> : a fürt kiépítés során használt hitelesítő adatokat adja meg. További információ: [Hadoop-fürtök létrehozása a HDInsight-ben](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).<br/><br/><b>Kimeneti adatforgalom helye</b>: Megadja, hogy az adatokat egy Hadoop elosztott fájlrendszerben (HDFS) vagy az Azure-ban tárolja-e a rendszer. <br/><ul>Ha a kimeneti adatokat a HDFS tárolja, adja meg a HDFS-kiszolgáló URI azonosítóját. (Ügyeljen arra, hogy a HDInsight-fürt nevét a HTTPS://előtag nélkül használja.) <br/><br/>Ha a kimeneti adatokat az Azure-ban tárolja, meg kell adnia az Azure Storage-fiók nevét, a tárterület-hozzáférési kulcsot és a tároló nevét.</ul> |
| SQL-adatbázis |Azure SQL Database, SQL felügyelt példányban vagy Azure-beli virtuális gépen futó SQL Server adatbázisban tárolt adatokat olvas be. |<b>Adatbázis-kiszolgáló neve</b>: annak a kiszolgálónak a nevét adja meg, amelyen az adatbázis fut.<br/><ul>Azure SQL Database esetén adja meg a létrehozandó kiszolgáló nevét. Általában a *&lt; generated_identifier &gt; . database.Windows.net formátumú.* <br/><br/>Azure-beli virtuális gépen futtatott SQL Server esetén a következőt adja meg *: TCP: &lt; virtuális gép DNS-neve &gt; , 1433*</ul><br/><b>Adatbázis neve </b>: a kiszolgálón található adatbázis nevét adja meg. <br/><br/><b>Kiszolgáló felhasználói fiókjának neve</b>: egy olyan fiók felhasználónevét adja meg, amely hozzáférési engedélyekkel rendelkezik az adatbázishoz. <br/><br/><b>Kiszolgáló felhasználói fiókjának jelszava</b>: megadja a felhasználói fiók jelszavát.<br/><br/><b>Adatbázis-lekérdezés</b>: adjon meg egy SQL-utasítást, amely leírja az olvasni kívánt információkat. |
| Helyszíni SQL-adatbázis |Egy SQL-adatbázisban tárolt adatokat olvas be. |<b>Adatátjáró</b>: annak a számítógépnek a neve, amelyen a SQL Server-adatbázishoz hozzáférő adatkezelés átjáró található. Az átjáró beállításával kapcsolatos információkért tekintse meg az [SQL Serverből származó adatok használatával történő speciális elemzési műveleteket a Azure Machine learning Studio (klasszikus)](use-data-from-an-on-premises-sql-server.md)című témakört.<br/><br/><b>Adatbázis-kiszolgáló neve</b>: annak a kiszolgálónak a nevét adja meg, amelyen az adatbázis fut.<br/><br/><b>Adatbázis neve </b>: a kiszolgálón található adatbázis nevét adja meg. <br/><br/><b>Kiszolgáló felhasználói fiókjának neve</b>: egy olyan fiók felhasználónevét adja meg, amely hozzáférési engedélyekkel rendelkezik az adatbázishoz. <br/><br/><b>Felhasználónév és jelszó</b>: az adatbázis hitelesítő adatainak megadásához kattintson az <b>értékek megadása</b> elemre. A SQL Server konfigurálásának módjától függően integrált Windows-hitelesítést vagy SQL Server hitelesítést használhat.<br/><br/><b>Adatbázis-lekérdezés</b>: adjon meg egy SQL-utasítást, amely leírja az olvasni kívánt információkat. |
| Azure-tábla |Beolvassa az adatokat az Azure Storage Table serviceból.<br/><br/>Ha gyakran nagy mennyiségű adattal olvas, használja az Azure Table szolgáltatást. Rugalmas, nem összehasonlítható (NoSQL), nagymértékben méretezhető, költséges és nagy rendelkezésre állású tárolási megoldást biztosít. |Az **adatimportálási** beállítások attól függően változnak, hogy a nyilvános adatokhoz vagy egy olyan privát Storage-fiókhoz fér hozzá, amelyhez bejelentkezési hitelesítő adatok szükségesek. Ezt a <b>hitelesítési típus</b> határozza meg, amelynek értéke "PublicOrSAS" vagy "account" lehet, amelyek mindegyike saját paramétereket tartalmaz. <br/><br/><b>Nyilvános vagy közös hozzáférésű aláírás (SAS) URI</b>: a paraméterek a következők:<br/><br/><ul><b>Tábla URI-ja</b>: a tábla nyilvános vagy sas URL-címét adja meg.<br/><br/><b>Megadja a tulajdonságok neveinek vizsgálatához szükséges sorokat</b>: az értékek a megadott számú sor vizsgálatára <i>legjobb n</i> , vagy <i>ScanAll</i> a tábla összes sorának lekéréséhez. <br/><br/>Ha az adat homogén és kiszámítható, javasoljuk, hogy válassza a *legjobb n* lehetőséget, és adjon meg egy számot N értékre. Nagyméretű táblák esetén ez gyorsabb olvasási időt eredményezhet.<br/><br/>Ha az adat strukturálva van, és a tábla mélysége és pozíciója alapján változik, válassza a *ScanAll* lehetőséget az összes sor vizsgálatához. Ez biztosítja az eredményül kapott tulajdonság és a metaadatok átalakításának integritását.<br/><br/></ul><b>Privát Storage-fiók</b>: a paraméterek a következők: <br/><br/><ul><b>Fióknév</b>: az olvasni kívánt táblát tartalmazó fiók nevét adja meg.<br/><br/><b>Fiók kulcsa</b>: a fiókhoz társított tárolási kulcsot adja meg.<br/><br/><b>Táblanév</b> : az olvasni kívánt adatmennyiséget tartalmazó tábla nevét adja meg.<br/><br/>A <b>tulajdonságok nevének vizsgálatára szolgáló sorok</b>: az értékek a megadott számú sor vizsgálatára <i>legjobb n</i> , vagy <i>ScanAll</i> a tábla összes sorának lekéréséhez.<br/><br/>Ha az adat homogén és kiszámítható, javasoljuk, hogy válassza a *legjobb n* lehetőséget, és adjon meg egy számot N értékre. Nagyméretű táblák esetén ez gyorsabb olvasási időt eredményezhet.<br/><br/>Ha az adat strukturálva van, és a tábla mélysége és pozíciója alapján változik, válassza a *ScanAll* lehetőséget az összes sor vizsgálatához. Ez biztosítja az eredményül kapott tulajdonság és a metaadatok átalakításának integritását.<br/><br/> |
| Azure Blob Storage |Beolvassa az Azure Storage Blob service tárolt adatokat, beleértve a képeket, a strukturálatlan szöveget vagy a bináris adatokat.<br/><br/>Az adatBlob servicet nyilvánosan elérhetővé teheti, vagy az alkalmazásadatok magánjellegű tárolására is használhatja. Az adatait HTTP-vagy HTTPS-kapcsolaton keresztül bárhonnan elérheti. |Az **adatimportálási** modul beállításai attól függően változnak, hogy a nyilvános adatokat vagy egy olyan privát Storage-fiókot használ, amelyhez bejelentkezési hitelesítő adatok szükségesek. Ezt a <b>hitelesítési típus</b> határozza meg, amelynek értéke "PublicOrSAS" vagy "account" lehet.<br/><br/><b>Nyilvános vagy közös hozzáférésű aláírás (SAS) URI</b>: a paraméterek a következők:<br/><br/><ul><b>URI</b>: a tárolási blob nyilvános vagy sas URL-címét adja meg.<br/><br/><b>Fájlformátum</b>: a blob serviceban lévő adatformátumot határozza meg. A támogatott formátumok a következők: CSV, TSV és ARFF.<br/><br/></ul><b>Privát Storage-fiók</b>: a paraméterek a következők: <br/><br/><ul><b>Fióknév</b>: az olvasni kívánt blobot tartalmazó fiók nevét adja meg.<br/><br/><b>Fiók kulcsa</b>: a fiókhoz társított tárolási kulcsot adja meg.<br/><br/><b>Tároló, könyvtár vagy blob elérési útja </b> : az olvasni kívánt adatmennyiséget tartalmazó blob nevét adja meg.<br/><br/><b>Blob fájlformátum</b>: a blob szolgáltatásban található adatformátumot határozza meg. A támogatott adatformátumok a következők: CSV, TSV, ARFF, CSV egy megadott kódolással és Excel-fájlokkal. <br/><br/><ul>Ha a formátum CSV vagy TSV, ügyeljen arra, hogy a fájl fejlécsort tartalmazzon.<br/><br/>Az Excel-munkafüzetből az Excel-munkafüzetekből is olvashat adatokból. Az <i>Excel adatformátuma</i> beállításban jelezze, hogy az adatai Excel-munkalapokon vagy Excel-táblázatban találhatók-e. Az <i>Excel-táblázat vagy a beágyazott tábla </i>beállításnál adja meg annak a lapnak vagy táblának a nevét, amelyet olvasni szeretne.</ul><br/> |
| Adatcsatorna-szolgáltató |Adatokat olvas be egy támogatott hírcsatorna-szolgáltatótól. Jelenleg csak az Open adatprotokoll (OData) formátum támogatott. |<b>Adattartalom típusa</b>: megadja a OData formátumát.<br/><br/><b>Forrás URL-címe</b>: az adatcsatorna teljes URL-címét adja meg. <br/>Például a következő URL-cím olvasható a Northwind mintaadatbázisból: https://services.odata.org/northwind/northwind.svc/ |

## <a name="import-from-another-experiment"></a>Importálás másik kísérletből

Időnként előfordulhat, hogy egy kísérletből közbenső eredményt szeretne készíteni, és egy másik kísérlet részeként használja. Ehhez mentse a modult adatkészletként:

1. Kattintson az adatkészletként menteni kívánt modul kimenetére.
2. Kattintson **a Mentés adatkészletként** lehetőségre.
3. Ha a rendszer kéri, adjon meg egy nevet és egy leírást, amely lehetővé teszi az adatkészlet egyszerű azonosítását.
4. Kattintson az **OK pipa gombra** .

A Mentés befejeződése után az adatkészlet elérhető lesz a munkaterületen lévő bármilyen kísérletben. Ez a modul paletta **mentett adatkészletek** listájában található.

## <a name="next-steps"></a>Következő lépések

[Az Adatimportálási és adatexportálási modulokat használó Azure Machine Learning Studio webszolgáltatások üzembe helyezése](web-services-that-use-import-export-modules.md)


<!-- Module References -->
[import-data]: /azure/machine-learning/studio-module-reference/import-data
[export-data]: /azure/machine-learning/studio-module-reference/export-data


<!-- Module References -->
[convert-to-dataset]: /azure/machine-learning/studio-module-reference/convert-to-dataset
[edit-metadata]: /azure/machine-learning/studio-module-reference/edit-metadata
[import-data]: /azure/machine-learning/studio-module-reference/import-data