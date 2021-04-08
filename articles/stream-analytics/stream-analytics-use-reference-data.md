---
title: Referenciák használata a Azure Stream Analyticsban való keresésekhez
description: Ez a cikk azt ismerteti, hogyan használhatók a hivatkozási adatok a Azure Stream Analytics feladatok lekérdezési tervében lévő adatok kereséséhez vagy összekapcsolásához.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/18/2020
ms.openlocfilehash: e05a4cbbc5fefbfe8a92914ef480f32bdf43ca37
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99560217"
---
# <a name="using-reference-data-for-lookups-in-stream-analytics"></a>A Stream Analytics-keresések hivatkozási adatainak használata

A hivatkozási adatok (más néven keresési táblázat) egy olyan véges adathalmaz, amely statikus vagy lassan változik a természetben, a keresés végrehajtásához vagy az adatfolyamok bővítéséhez. Például egy IoT-forgatókönyvben tárolhatók metaadatok az érzékelőkről (amelyek nem változnak gyakran) a hivatkozási adatokban, és a valós idejű IoT-adatfolyamokhoz csatlakoznak. Azure Stream Analytics betölti a memóriában lévő hivatkozási adatmennyiséget, hogy alacsony késésű adatfolyam-feldolgozást érjen el. Ha a Azure Stream Analytics-feladatokban szeretné használni a hivatkozásokat, a lekérdezésben általában egy [hivatkozási adatokhoz való csatlakozást](/stream-analytics-query/reference-data-join-azure-stream-analytics) fog használni. 

## <a name="example"></a>Példa  
Valós idejű adatfolyamot hozhat létre, amikor az autók egy díjköteles standot adnak át. Az autópályadíj-kezelő valós időben rögzítheti a licencet, és olyan statikus adatkészlettel csatlakozhat, amely a lejárt licencek azonosítására szolgáló regisztrációs adatokat tartalmaz.  
  
```SQL  
SELECT I1.EntryTime, I1.LicensePlate, I1.TollId, R.RegistrationId  
FROM Input1 I1 TIMESTAMP BY EntryTime  
JOIN Registration R  
ON I1.LicensePlate = R.LicensePlate  
WHERE R.Expired = '1'
```  

Stream Analytics támogatja az Azure Blob Storage-t, és Azure SQL Database a hivatkozási adattárolási rétegként. Átalakíthatja és/vagy átmásolhatja a blob Storage-ba Azure Data Factory a [felhőalapú és a helyszíni adattárak tetszőleges számú](../data-factory/copy-activity-overview.md)felhasználására is.

## <a name="azure-blob-storage"></a>Azure Blob Storage

A hivatkozási adatok a Blobok nevében megadott dátum/idő növekvő sorrendjében (a bemeneti konfigurációban definiált) Blobok sorozatából vannak modellezve. A művelet **csak** az utolsó blob által a sorozatban megadott értéknél **nagyobb** dátum/idő használatával támogatja a folyamat végének hozzáadását. További információ: [Azure stream Analytics feladathoz tartozó blob Storage hivatkozási adatainak használata](data-protection.md).

### <a name="configure-blob-reference-data"></a>BLOB-hivatkozási adatértékek konfigurálása

A hivatkozási adatok konfigurálásához először létre kell hoznia egy olyan bemenetet, amely **hivatkozási adatokat tartalmaz**. Az alábbi táblázat bemutatja, hogy milyen tulajdonságokat kell megadnia a hivatkozási adatok bemenetének a leírásával való létrehozásakor:

|**Tulajdonság neve**  |**Leírás**  |
|---------|---------|
|Input Alias (Bemeneti alias)   | Egy rövid név, amelyet a rendszer a lekérdezésben a bemenetre való hivatkozáshoz fog használni.   |
|Tárfiók   | Annak a Storage-fióknak a neve, ahol a Blobok találhatók. Ha a Stream Analytics-feladatokkal megegyező előfizetésben szerepel, kiválaszthatja a legördülő menüből.   |
|Storage-fiók kulcsa   | A Storage-fiókhoz társított titkos kulcs. Ezt automatikusan kitölti a rendszer, ha a Storage-fiók ugyanabban az előfizetésben van, mint a Stream Analytics-feladatokhoz.   |
|Storage-tároló   | A tárolók logikai csoportosítást biztosítanak a Microsoft Azure Blob service tárolt blobokhoz. Amikor feltölt egy blobot a Blob serviceba, meg kell adnia egy tárolót a blobhoz.   |
|Path Pattern (Elérésiút-minta)   | Ez egy kötelező tulajdonság, amely a Blobok a megadott tárolón belüli megkeresésére szolgál. Az elérési úton a következő két változó egy vagy több példányát is megadhatja:<BR>{Date}, {Time}<BR>1. példa: termékek/{Date}/{Time}/product-list.csv<BR>2. példa: termékek/{Date}/product-list.csv<BR>3. példa: product-list.csv<BR><br> Ha a blob nem létezik a megadott elérési úton, a Stream Analytics-feladatot a blob elérhetővé válása határozatlan ideig megvárja.   |
|Dátumformátum [nem kötelező]   | Ha a megadott elérésiút-mintában a {Date} értéket használta, akkor kiválaszthatja azt a dátumformátum-formátumot, amelyben a Blobok a támogatott formátumok legördülő menüjéből vannak rendszerezve.<BR>Példa: éééé/hh/nn, hh/nn/éééé stb.   |
|Időformátum [nem kötelező]   | Ha a megadott elérési úton a {Time} értéket használta, akkor kiválaszthatja azt az időformátumot, amelyben a Blobok a támogatott formátumok legördülő menüjéből vannak rendszerezve.<BR>Példa: HH, HH/hh vagy HH-mm.  |
|Esemény szerializálási formátuma   | Annak biztosítása érdekében, hogy a lekérdezések a várt módon működjenek, Stream Analytics tudnia kell, hogy melyik szerializálási formátumot használja a bejövő adatfolyamok számára. A hivatkozási adatformátumok esetében a támogatott formátumok a CSV és a JSON.  |
|Encoding   | Jelenleg az UTF-8 az egyetlen támogatott kódolási formátum.  |

### <a name="static-reference-data"></a>Statikus referenciák

Ha a hivatkozási adatok várhatóan nem változnak, akkor a statikus hivatkozási adatok támogatása a bemeneti konfigurációban statikus elérési út megadásával engedélyezhető. Azure Stream Analytics felveszi a blobot a megadott elérési útra. nem kötelező {date} és {Time} helyettesítési token. Mivel a hivatkozási adatértékek nem változtathatók meg Stream Analyticsban, a statikus hivatkozási adatblobok felülírása nem ajánlott.

### <a name="generate-reference-data-on-a-schedule"></a>Hivatkozási információ létrehozása ütemterv alapján

Ha a hivatkozási adatok lassan módosulnak, akkor a hivatkozási adatok frissítésének támogatása a {Date} és az {Time} helyettesítési tokent használó bemeneti konfigurációban megadott elérésiút-minta megadásával engedélyezhető. Stream Analytics felveszi a frissített hivatkozási adatdefiníciókat ezen elérésiút-minta alapján. Például az `sample/{date}/{time}/products.csv` **"éééé-hh-dd"** dátumformátum és a **"hh-mm"** időformátuma arra utasítja a stream Analyticst, hogy a frissített blobot `sample/2015-04-16/17-30/products.csv` 5:30 órakor, április 16-án 2015 UTC időzóna szerint vegye fel.

Azure Stream Analytics automatikusan megkeresi a frissített hivatkozási adatblobokat egy perces intervallumban. Ha az időbélyeg 10:30:00-as számú blobja kis késleltetéssel van feltöltve (például 10:30:30), a blobra hivatkozó Stream Analytics-feladatban kis késleltetést fog tapasztalni. Az ilyen helyzetek elkerülése érdekében javasoljuk, hogy a megcélzott tényleges időpontnál (10:30:00) korábbinál töltse fel a blobot, hogy a Stream Analytics feladatnak elegendő idő legyen ahhoz, hogy felderítse és betöltse a memóriába és műveleteket hajtson végre. 

> [!NOTE]
> Jelenleg Stream Analytics feladat csak akkor keresi a Blobok frissítését, ha a gép ideje a blob nevében kódolt időpontra van beállítva. A feladatot például a lehető leghamarabb megkeresi, `sample/2015-04-16/17-30/products.csv` de az UTC időzóna 5:30. április 16 2015-án nem korábbi. A rendszer *soha nem* keres olyan blobot, amely a felderített utolsónál korábbi kódolású.
> 
> Ha például a feladatban megtalálta a blobot, a `sample/2015-04-16/17-30/products.csv` rendszer figyelmen kívül hagyja a 5:30. április 16. előtt kódolt dátummal rendelkező fájlokat 2015, így ha egy későn érkező `sample/2015-04-16/17-25/products.csv` blob jön létre ugyanabban a tárolóban, a feladatot nem fogja használni.
> 
> Hasonlóképpen, ha `sample/2015-04-16/17-30/products.csv` csak a 10:03. április 16-án, 2015-kor, de nincs korábbi dátummal rendelkező blob a tárolóban, a feladathoz a (z) 10:03. április 2015 16-i.
> 
> Ez alól kivételt képez, ha a feladatsornak időben újra fel kell dolgoznia az adatfeldolgozást, vagy amikor a feladatot először indítja el. A kezdéskor a rendszer a feladatok kezdési időpontja előtt előállított legújabb blobot keresi. Erre azért van szükség, hogy a feladatok elindulásakor ne legyen **üres** a hivatkozás adatkészlete. Ha az egyik nem található, a feladatokban a következő diagnosztika látható: `Initializing input without a valid reference data blob for UTC time <start time>` .

A [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) a stream Analytics által a hivatkozási adatok definícióinak frissítéséhez szükséges frissített Blobok létrehozásának feladatát is felhasználhatja. A Data Factory egy felhőalapú adatintegrációs szolgáltatás, amellyel előkészíthető és automatizálható az adatok továbbítása és átalakítása. Data Factory támogatja a [nagy számú felhőalapú és helyszíni adattárakhoz való csatlakozást](../data-factory/copy-activity-overview.md) , valamint az adatáthelyezést a megadott menetrend szerint. További információ és lépésenkénti útmutató arról, hogyan állíthat be egy Data Factory folyamatot, hogy olyan Stream Analyticsre hivatkozzon, amely előre meghatározott ütemterv alapján frissül, és tekintse meg ezt a [GitHub-mintát](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ReferenceDataRefreshForASAJobs).

### <a name="tips-on-refreshing-blob-reference-data"></a>Tippek a blob-referenciák frissítéséhez

1. Ne írja felül a hivatkozási adatblobokat, mivel azok nem változtathatók meg.
2. A hivatkozási adatai frissítésének ajánlott módja a következő:
    * {Date}/{Time} használata az elérési út mintájában
    * Adjon hozzá egy új blobot a feladatban megadott tároló-és elérésiút-minta használatával
    * A sorozatban az utolsó blob által megadott dátum/idő értéknél **nagyobb** dátumot és időpontot használjon.
3. A hivatkozási adatblobok **nem** a blob "utolsó módosításának" időpontjában vannak rendezve, de csak a blob nevében megadott idő és dátum alapján a {Date} és a {Time} helyettesítéssel.
3. Ha nem szeretne nagy számú blobot listázni, érdemes törölni a nagyon régi blobokat, amelyekkel a feldolgozás már nem lesz végrehajtva. Vegye figyelembe, hogy az ASA-nek bizonyos helyzetekben újra fel kell dolgoznia egy kis mennyiségű újrafeldolgozást, például újraindítást.

## <a name="azure-sql-database"></a>Azure SQL Database

Azure SQL Database a hivatkozási adatok beolvasása a Stream Analytics feladataival történik, és a memóriában pillanatképként tárolódik a feldolgozáshoz. A rendszer a hivatkozási adatok pillanatképét a konfigurációs beállításokban megadott Storage-fiókban is tárolja. A tároló automatikusan létrejön a feladatok indításakor. Ha a feladatot leállítja vagy hibás állapotba lép, a rendszer törli az automatikusan létrehozott tárolókat a feladatok újraindításakor.  

Ha a hivatkozási adatok lassan változó adathalmazt használnak, rendszeres időközönként frissítenie kell a feladatokban használt pillanatképet. Stream Analytics lehetővé teszi a frissítési sebesség beállítását a Azure SQL Database bemeneti kapcsolatok konfigurálásakor. A Stream Analytics futtatókörnyezet a frissítési gyakoriság által megadott időközönként lekérdezi a Azure SQL Database. A leggyorsabb frissítési sebesség percenként egyszer használható. Az egyes frissítések esetében Stream Analytics egy új pillanatképet tárol a megadott Storage-fiókban.

Stream Analytics két lehetőséget biztosít a Azure SQL Database lekérdezésére. A pillanatkép-lekérdezés kötelező, és az egyes feladatokban szerepelnie kell. Stream Analytics rendszeresen futtatja a pillanatkép-lekérdezést a frissítési időköz alapján, és a lekérdezés eredményét (a pillanatképet) használja a hivatkozási adathalmazként. A pillanatkép-lekérdezésnek a legtöbb forgatókönyvnek megfelelőnek kell lennie, de ha nagy adatkészletekkel és gyors frissítési sebességekkel teljesítménnyel kapcsolatos problémákba ütközik, használhatja a különbözeti lekérdezési lehetőséget. A hivatkozási adatkészlet visszaadására több mint 60 másodpercet elfoglaló lekérdezések időtúllépést eredményeznek.

A különbözeti lekérdezés beállításnál Stream Analytics először a pillanatkép-lekérdezést futtatja egy alapkonfiguráció-adatkészlet beszerzéséhez. Ezt követően a Stream Analytics rendszeres időközönként futtatja a különbözeti lekérdezést a növekményes módosítások beolvasására szolgáló frissítési időköz alapján. Ezeket a növekményes módosításokat a rendszer folyamatosan alkalmazza a hivatkozási adathalmazra, hogy azok frissítve legyenek. A különbözeti lekérdezés használatával csökkentheti a tárolási költségeket és a hálózati I/O-műveleteket.

### <a name="configure-sql-database-reference"></a>SQL Database-hivatkozás konfigurálása

A SQL Database hivatkozási adatok konfigurálásához először létre kell hoznia a **hivatkozási adatok** bevitelét. Az alábbi táblázat minden olyan tulajdonságot ismertet, amelyet meg kell adnia a hivatkozási adatok bemenetének a leírásával való létrehozásakor. További információ: [Azure stream Analytics feladathoz tartozó SQL Database hivatkozási adatainak használata](sql-reference-data.md).

Az [Azure SQL felügyelt példánya](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) használható a hivatkozásként használt adatok beviteléhez. [Konfigurálnia kell a nyilvános végpontot az SQL felügyelt példányában](../azure-sql/managed-instance/public-endpoint-configure.md) , majd manuálisan kell konfigurálnia a következő beállításokat a Azure stream Analyticsban. A SQL Servert futtató Azure-beli virtuális gépeket az alábbi beállítások manuális konfigurálásával is támogatja.

|**Tulajdonság neve**|**Leírás**  |
|---------|---------|
|Bemeneti alias|Egy rövid név, amelyet a rendszer a lekérdezésben a bemenetre való hivatkozáshoz fog használni.|
|Előfizetés|Válassza ki az előfizetését|
|Adatbázis|A hivatkozási adatait tartalmazó Azure SQL Database. SQL felügyelt példány esetén a 3342-es portot kell megadnia. Például: *sampleserver. public. database. Windows. net, 3342*|
|Felhasználónév|A Azure SQL Databasehoz társított Felhasználónév.|
|Jelszó|A Azure SQL Databasehoz társított jelszó.|
|Rendszeres frissítés|Ez a beállítás lehetővé teszi a frissítési sebesség kiválasztását. A "bekapcsolva" beállítással megadhatja a frissítési gyakoriságot a DD: óó: PP értékben.|
|Pillanatkép-lekérdezés|Ez az alapértelmezett lekérdezési beállítás, amely beolvassa a hivatkozási adatait a SQL Databaseból.|
|Különbözeti lekérdezés|A nagyméretű adatkészletekkel és a rövid frissítési gyakorisággal rendelkező speciális forgatókönyvek esetében válassza a különbözeti lekérdezés hozzáadását.|

## <a name="size-limitation"></a>Méret korlátozása

A legjobb teljesítmény érdekében ajánlott a 300 MB-nál kisebb méretű hivatkozási adatkészleteket használni. Az 5 GB-os vagy alacsonyabb szintű hivatkozási adatkészletek támogatottak a 6 SUs vagy újabb feladatokban. A nagyon nagy referenciák használata hatással lehet a feladatok végpontok közötti késésére. Mivel a lekérdezések összetettsége az állapot-nyilvántartó feldolgozást is magában foglalja, például ablakos összesítéseket, időbeli illesztéseket és időbeli elemzési függvényeket, a rendszer várhatóan csökkenti a hivatkozási adatok maximális támogatott méretét. Ha Azure Stream Analytics nem tudja betölteni a hivatkozási adatok betöltését, és összetett műveleteket hajt végre, a feladat elfogy a memóriában, és sikertelen lesz. Ilyen esetekben a SU% kihasználtsági metrika eléri a 100%-ot.    

|**Folyamatos átviteli egységek száma**  |**Ajánlott méret**  |
|---------|---------|
|1   |50 MB vagy kevesebb   |
|3   |150 MB vagy kevesebb   |
|6 és azon túl   |5 GB vagy alacsonyabb.    |

A tömörítés támogatása nem érhető el a hivatkozási értékekhez.

## <a name="joining-multiple-reference-datasets-in-a-job"></a>Több hivatkozási adatkészlet csatlakoztatása egy feladathoz
A lekérdezés egyetlen lépésében csak egy adatfolyam-bevitelt lehet csatlakoztatni egy hivatkozással. A lekérdezés több lépésben történő lebontásával azonban több hivatkozási adatkészlethez is csatlakozhat. Erre mutat példát az alábbi ábra.

```SQL  
With Step1 as (
    --JOIN input stream with reference data to get 'Desc'
    SELECT streamInput.*, refData1.Desc as Desc
    FROM    streamInput
    JOIN    refData1 ON refData1.key = streamInput.key 
)
--Now Join Step1 with second reference data
SELECT *
INTO    output 
FROM    Step1
JOIN    refData2 ON refData2.Desc = Step1.Desc 
``` 

## <a name="iot-edge-jobs"></a>IoT Edge feladatok

Stream Analytics Edge-feladatok esetében csak a helyi hivatkozási adatok támogatottak. Ha egy feladatot központilag telepítenek IoT Edge eszközre, a felhasználó által megadott fájl elérési útjából betölti a hivatkozási adatait. Az eszközön készen áll egy hivatkozási adatfájl. Windows-tároló esetén helyezze a hivatkozási adatfájlt a helyi meghajtón, és ossza meg a helyi meghajtót a Docker-tárolóval. Linux-tároló esetén hozzon létre egy Docker-kötetet, és töltse fel az adatfájlt a kötetre.

IoT Edge frissítésre vonatkozó referenciákat egy központi telepítés indítja el. Az aktiválás után a Stream Analytics modul a futó feladatok leállítása nélkül kiválasztja a frissített adatok körét.

A referenciák frissítése kétféleképpen lehetséges:

* A Stream Analytics-feladatokban szereplő hivatkozási adatútvonal frissítése Azure Portalból.

* Frissítse a IoT Edge üzemelő példányt.

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Útmutató: Stream Analytics-feladat létrehozása az Azure Portal használatával](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.get.started]: ./stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/
