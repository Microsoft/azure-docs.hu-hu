---
title: IoT-megoldás létrehozása Azure Stream Analytics használatával
description: Első lépések oktatóanyag a Tollbooth-forgatókönyv Stream Analytics IoT megoldásához
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: ddec53b18cd6f374a5665298b43b46122bcfa143
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98016150"
---
# <a name="build-an-iot-solution-by-using-stream-analytics"></a>IoT-megoldás létrehozása Stream Analytics használatával

## <a name="introduction"></a>Bevezetés
Ebben a megoldásban megtudhatja, hogyan használhatja a Azure Stream Analyticst az adatok valós idejű elemzéséhez. A fejlesztők könnyedén egyesítheti az adatstreameket, például a streameket, a naplókat és az eszköz által generált eseményeket, valamint a múltbeli rekordokat vagy a hivatkozási adatokat az üzleti elemzések elvégzéséhez. A Microsoft Azureban üzemeltetett, teljes mértékben felügyelt, valós idejű adatfolyam-számítási szolgáltatásként Azure Stream Analytics a rugalmasságot, a kis késést és a méretezhetőséget percek alatt üzembe helyezheti.

A megoldás befejezése után a következőkre nyílik lehetősége:

* Ismerkedjen meg a Azure Stream Analytics-portálon.
* Folyamatos átviteli feladatok konfigurálása és üzembe helyezése.
* A valós problémák megfogalmazása és megoldása a Stream Analytics lekérdezési nyelv használatával.
* A Stream Analytics magabiztos használatával fejlesztheti az ügyfeleinek folyamatos átviteli megoldásait.
* A figyelési és a naplózási felülettel elháríthatja a problémákat.

## <a name="prerequisites"></a>Előfeltételek
A megoldás elvégzéséhez a következő előfeltételek szükségesek:
* [Azure-előfizetés](https://azure.microsoft.com/pricing/free-trial/)

## <a name="scenario-introduction-hello-toll"></a>Forgatókönyv bevezetése: "Hello, díjköteles!"
A díjköteles állomás gyakori jelenség. Számos gyorsforgalmi, hidakon és alagúton találkozhat a világ különböző pontjain. Minden díjköteles állomáshoz több díjköteles fülke tartozik. A manuális fülkékben leállítja az útdíjat egy kísérőnek. Az automatizált fülkékben az egyes fülkék tetején lévő érzékelők egy RFID-kártyát vizsgálnak meg, amelyet a jármű szélvédőn helyeztek el az autópályadíj-fülke továbbítása során. A járművek ezen autópályadíj-állomásokon keresztüli áthaladását egyszerűen megjelenítheti, mint az érdekes műveleteket elvégező esemény-adatfolyamok.

![A díjköteles fülkékben lévő autók képe](media/stream-analytics-build-an-iot-solution-using-stream-analytics/cars-in-toll-booth.jpg)

## <a name="incoming-data"></a>Bejövő adatértékek
Ez a megoldás két adatfolyammal működik. A díjköteles állomások beléptetéséhez és kilépéséhez telepített érzékelők az első streamet adják meg. A második stream egy statikus keresési adatkészlet, amely a jármű regisztrációs adatokat tartalmaz.

### <a name="entry-data-stream"></a>Bejegyzés adatfolyama
A belépési adatfolyam tartalmazza az autókra vonatkozó információkat, amikor autópályadíj-állomásokat visznek be. A kilépési adatok eseményei a minta alkalmazásban található webalkalmazásból áramlanak egy Event hub-várólistába.

| TollID | EntryTime | LicensePlate | Állapot | Gyártó | Modellezés | VehicleType | VehicleWeight | Autópályadíj | Címke |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 |2014-09-10 12:01:00.000 |JNB 7001 |NY |Honda |CRV |1 |0 |7 | |
| 1 |2014-09-10 12:02:00.000 |YXZ 1001 |NY |Toyota |Camry |1 |0 |4 |123456789 |
| 3 |2014-09-10 12:02:00.000 |ABC 1004 |CT |Ford |Taurus |1 |0 |5 |456789123 |
| 2 |2014-09-10 12:03:00.000 |XYZ 1003 |CT |Toyota |Corolla |1 |0 |4 | |
| 1 |2014-09-10 12:03:00.000 |BNJ 1007 |NY |Honda |CRV |1 |0 |5 |789123456 |
| 2 |2014-09-10 12:05:00.000 |CDE 1007 |NJ |Toyota |4x4 |1 |0 |6 |321987654 |

Itt látható az oszlopok rövid leírása:

| Oszlop | Leírás |
| --- | --- |
| TollID |A díjköteles fülke egyedi azonosítására szolgáló díjköteles fülke azonosítója |
| EntryTime |A járműnek az autópályadíj-kezelőbe való belépésének dátuma és időpontja (UTC) |
| LicensePlate |A jármű rendszámtábla-száma |
| Állapot |Egyesült Államok állapot |
| Gyártó |A személygépkocsi gyártója |
| Modellezés |Az autó modelljének száma |
| VehicleType |1 a személyszállító járművek vagy a 2 kereskedelmi járművek számára |
| WeightType |A jármű súlya tonnában kifejezve; 0 személyszállító járművek esetében |
| Autópályadíj |Az autópályadíj értéke USD-ben |
| Címke |Az autóban található e-címke, amely automatizálja a fizetést; üres, ahol a fizetés manuálisan lett végrehajtva |

### <a name="exit-data-stream"></a>Kilépés az adatfolyamból
A kilépési adatfolyam az autópályadíj-állomást elhagyó autókkal kapcsolatos információkat tartalmaz. A kilépési adatok eseményei a minta alkalmazásban található webalkalmazásból áramlanak egy Event hub-várólistába.

| **TollId** | **ExitTime** | **LicensePlate** |
| --- | --- | --- |
| 1 |2014-09-10T12:03:00.0000000 Z |JNB 7001 |
| 1 |2014-09-10T12:03:00.0000000 Z |YXZ 1001 |
| 3 |2014-09-10T12:04:00.0000000 Z |ABC 1004 |
| 2 |2014-09-10T12:07:00.0000000 Z |XYZ 1003 |
| 1 |2014-09-10T12:08:00.0000000 Z |BNJ 1007 |
| 2 |2014-09-10T12:07:00.0000000 Z |CDE 1007 |

Itt látható az oszlopok rövid leírása:

| Oszlop | Leírás |
| --- | --- |
| TollID |A díjköteles fülke egyedi azonosítására szolgáló díjköteles fülke azonosítója |
| ExitTime |A jármű kilépésének dátuma és időpontja az autópályadíj-kezelőből UTC szerint |
| LicensePlate |A jármű rendszámtábla-száma |

### <a name="commercial-vehicle-registration-data"></a>Kereskedelmi jármű regisztrációs adatvédelme
A megoldás egy kereskedelmi jármű regisztrációs adatbázisának statikus pillanatképét használja. Ezeket az adatfájlokat a rendszer JSON-fájlként menti az Azure Blob Storage-ba, amely tartalmazza a mintát.

| LicensePlate | Regisztrációban | Lejárt |
| --- | --- | --- |
| SVT 6023 |285429838 |1 |
| XLZ 3463 |362715656 |0 |
| BAC 1005 |876133137 |1 |
| RIV 8632 |992711956 |0 |
| SNY 7188 |592133890 |0 |
| ELH 9896 |678427724 |1 |

Itt látható az oszlopok rövid leírása:

| Oszlop | Leírás |
| --- | --- |
| LicensePlate |A jármű rendszámtábla-száma |
| Regisztrációban |A jármű regisztrációs azonosítója |
| Lejárt |A jármű regisztrációs állapota: 0, ha a jármű regisztrálása aktív, 1 Ha a regisztráció lejárt |

## <a name="set-up-the-environment-for-azure-stream-analytics"></a>A környezet beállítása Azure Stream Analytics
A megoldás végrehajtásához Microsoft Azure előfizetésre van szükség. Ha nem rendelkezik Azure-fiókkal, [kérhet egy ingyenes próbaverziót](https://azure.microsoft.com/pricing/free-trial/).

Ügyeljen arra, hogy a cikk végén található "az Azure-fiók tisztítása" című szakaszban ismertetett lépéseket követve az Azure-kreditek lehető legjobb használatát végezze el.

## <a name="deploy-the-sample"></a>A minta üzembe helyezése
Több olyan erőforrás is van, amely egyszerűen üzembe helyezhető egy erőforráscsoporthoz, néhány kattintással együtt. A megoldás definícióját a GitHub-adattár tárolja a következő helyen: [https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp) .

### <a name="deploy-the-tollapp-template-in-the-azure-portal"></a>A TollApp-sablon üzembe helyezése a Azure Portal
1. A TollApp-környezet Azure-ba történő üzembe helyezéséhez használja ezt a hivatkozást az [TollApp Azure-sablon üzembe helyezéséhez](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-stream-analytics%2Fmaster%2FSamples%2FTollApp%2FVSProjects%2FTollAppDeployment%2Fazuredeploy.json).

2. Ha a rendszer kéri, jelentkezzen be a Azure Portalba.

3. Válassza ki azt az előfizetést, amelyben a különböző erőforrások számlázása történik.

4. Adjon meg egy új erőforráscsoportot, amely egy egyedi név, például: `MyTollBooth` .

5. Válasszon egy Azure-beli helyet.

6. **Időtartamot** másodpercben megadva. Ezt az értéket használja a minta-webalkalmazásban, hogy milyen gyakran kell elküldeni az adatküldést az Event hub szolgáltatásba.

7. **Győződjön** meg arról, hogy elfogadja a feltételeket és a kikötéseket.

8. Válassza a **rögzítés az irányítópulton** lehetőséget, hogy a későbbiekben könnyen megtalálhatja az erőforrásokat.

9. Válassza a **vásárlás** lehetőséget a sablon telepítéséhez.

10. Néhány pillanat múlva megjelenik egy értesítés, amely megerősíti, hogy az üzemelő **példány sikeresen** megtörtént.

### <a name="review-the-azure-stream-analytics-tollapp-resources"></a>Tekintse át a Azure Stream Analytics TollApp-erőforrásait

1. Jelentkezzen be az Azure Portalra

2. Keresse meg az előző szakaszban megnevezett erőforráscsoportot.

3. Ellenőrizze, hogy az erőforráscsoport tartalmazza-e az alábbi erőforrásokat:
   - Egy Cosmos DB fiók
   - Egy Azure Stream Analytics feladatot
   - Egy Azure Storage-fiók
   - Egy Azure Event hub
   - Két Web Apps

## <a name="examine-the-sample-tollapp-job"></a>A minta TollApp-feladatok vizsgálata
1. Az előző szakaszban található erőforráscsoport után válassza ki a **tollapp** nevű stream Analytics folyamatos átviteli feladatot (a név véletlenszerű karaktereket tartalmaz az egyediség érdekében).

2. A feladatok **Áttekintés** lapján figyelje **meg a** lekérdezési szintaxist.

   ```sql
   SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
   INTO CosmosDB
   FROM EntryStream TIMESTAMP BY EntryTime
   GROUP BY TUMBLINGWINDOW(minute, 3), TollId
   ```

   A lekérdezés szándékának parafrázisa érdekében tegyük fel, hogy meg kell számolni a díjköteles standot megadható járművek számát. Mivel az autópálya-autópályadíjat futtató fülke folyamatos streamtel rendelkezik, a beléptetési események olyan adatfolyamok, amelyek soha nem állnak le. A stream számszerűsítése érdekében meg kell határoznia egy "időszakot" a méréshez. Pontosítjuk a kérdést, hogy a "hány jármű adja meg a díjköteles standot három percenként?" Ezt általában a kiesések száma is említi.

   Amint láthatja, a Azure Stream Analytics egy olyan lekérdezési nyelvet használ, amely hasonló az SQL-hez, és néhány bővítmény hozzáadásával megadja a lekérdezés időszerű szempontjait.  További részletekért olvassa el a lekérdezésben használt [Időkezelési](/stream-analytics-query/time-management-azure-stream-analytics) és [ablak](/stream-analytics-query/windowing-azure-stream-analytics) -létrehozási konstrukciókat.

3. Vizsgálja meg a TollApp-minta feladatának bemeneteit. A jelenlegi lekérdezésben csak a EntryStream-bevitelt használja a rendszer.
   - A **EntryStream** -bemenet egy olyan Event hub-kapcsolat, amely minden alkalommal, amikor egy autó egy Tollbooth belép az autópályán. A minta részét képező webalkalmazás létrehozza az eseményeket, és ezek az események várólistára kerülnek ebben az Event hub-ban. Vegye figyelembe, hogy ezt a bemenetet a streaming lekérdezés FROM záradékában kérdezi le a rendszer.
   - A **ExitStream** -bemenet egy olyan Event hub-kapcsolat, amely minden egyes alkalommal, amikor egy autó kilép egy Tollbooth az autópályán. Ez a folyamatos átviteli bemenet a lekérdezés szintaxisának későbbi változataiban használatos.
   - A **regisztrációs** bemenet egy Azure Blob Storage-csatlakozás, amely a fájl statikus registration.jsmutat, és szükség esetén a keresésekhez használható. Ez a hivatkozási adat a lekérdezés szintaxisának későbbi változataiban használatos.

4. Vizsgálja meg a TollApp-minta feladatainak kimeneteit.
   - A **Cosmos db** output egy Cosmos adatbázis-tároló, amely megkapja a kimeneti fogadó eseményeit. Vegye figyelembe, hogy ezt a kimenetet a streaming lekérdezés INTO záradékában kell használni.

## <a name="start-the-tollapp-streaming-job"></a>Az TollApp streaming-feladatok elindítása
A folyamatos átviteli feladat elindításához kövesse az alábbi lépéseket:

1. A feladatokhoz tartozó **Áttekintés** lapon válassza az **Indítás** lehetőséget.

2. A **kezdési feladatok** ablaktáblán válassza a **most** lehetőséget.

3. Néhány pillanat elteltével a feladatok futtatása után a folyamatos átviteli feladatok **Áttekintés** lapján tekintse meg a **figyelési** grafikont. A gráfnak több ezer bemeneti eseményt és több tízezer kimeneti eseményt kell megjelenítenie.

## <a name="review-the-cosmosdb-output-data"></a>A CosmosDB kimeneti adatai áttekintése
1. Keresse meg a TollApp erőforrásokat tartalmazó erőforráscsoportot.

2. Válassza ki a **tollapp \<random\> -Cosmos** nevű Azure Cosmos db fiókot.

3. Válassza ki a **adatkezelő** fejlécet a adatkezelő lap megnyitásához.

4. Bontsa ki a **tollAppDatabase**  >  **tollAppCollection**  >  **dokumentumait**.

5. Az azonosítók listájában számos dokumentum jelenik meg, amint a kimenet elérhetővé válik.

6. Válassza ki az egyes azonosítókat a JSON-dokumentum áttekintéséhez. Figyelje meg, hogy minden tollid, windowend és az adott ablakban lévő autók száma.

7. További három perc elteltével a rendszer egy másik négy dokumentumból álló készletet is rendelkezésre bocsát, tollid egy dokumentumot.


## <a name="report-total-time-for-each-car"></a>Jelentés az egyes autók teljes ideje
Ahhoz, hogy egy autó áthaladjon az útdíjon, az átlagos idő segít felmérni a folyamat hatékonyságát és a felhasználói élményt.

A teljes idő megkereséséhez csatlakoztassa a EntryTime streamet a ExitTime streamhez. Csatlakoztassa a két bemeneti streamet az EQUAL Matching TollId és a LicencePlate oszlopokhoz. A **JOIN** operátor megköveteli, hogy olyan időbeli eltérést határozzon meg, amely leírja az összekapcsolt események közötti elfogadható időtartamot. A **DATEDIFF** függvény használatával megadhatja, hogy az események ne legyenek több, mint 15 perc egymástól. A **DATEDIFF** függvényt is alkalmazza a kilépéshez és a belépési időpontokhoz, hogy kiszámítsa a tényleges időt, amikor egy autó az autópályadíj-állomáson költ. Vegye figyelembe, hogy a **DATEDIFF** használata nem **illesztési** feltétel helyett **Select** utasításban használatos.

```sql
SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute, EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN ExitStream TIMESTAMP BY ExitTime
ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15
```

### <a name="to-update-the-tollapp-streaming-job-query-syntax"></a>Az TollApp streaming Job lekérdezési szintaxisának frissítése:

1. A feladatokhoz tartozó **Áttekintés** lapon válassza a **Leállítás** lehetőséget.

2. Várjon néhány percet, amíg megtörténik az értesítés, hogy a feladatot leállították.

3. A feladatok TOPOLÓGIÁjának fejléce alatt válassza ki **< > lekérdezést** .

4. Illessze be a helyesbített streaming SQL-lekérdezést.

5. A lekérdezés mentéséhez válassza a **Mentés** lehetőséget. Erősítse meg az **Igen** gombot a módosítások mentéséhez.

6. A feladatokhoz tartozó **Áttekintés** lapon válassza az **Indítás** lehetőséget.

7. A **kezdési feladatok** ablaktáblán válassza a **most** lehetőséget.

### <a name="review-the-total-time-in-the-output"></a>A kimenetben lévő teljes idő áttekintése
Az előző szakaszban leírt lépések megismétlésével tekintse át a CosmosDB kimeneti adatait a folyamatos átviteli feladatokból. Tekintse át a legújabb JSON-dokumentumokat.

Ez a dokumentum például egy bizonyos rendszámtábla, a entryTime és a kilépési idő, valamint a DATEDIFF számított DurationInMinutes mező alapján mutatja be az autópályadíj-fülke időtartamát két percen belül:
```JSON
{
    "tollid": 4,
    "entrytime": "2018-04-05T06:51:39.0491173Z",
    "exittime": "2018-04-05T06:53:09.0491173Z",
    "licenseplate": "JVR 9425",
    "durationinminutes": 2,
    "id": "ff52eb25-d580-7566-2879-1f52bba6601e",
    "_rid": "+8E4AI1DZgBjAAAAAAAAAA==",
    "_self": "dbs/+8E4AA==/colls/+8E4AI1DZgA=/docs/+8E4AI1DZgBjAAAAAAAAAA==/",
    "_etag": "\"ad02f6b8-0000-0000-0000-5ac5c8330000\"",
    "_attachments": "attachments/",
    "_ts": 1522911283
}
```

## <a name="report-vehicles-with-expired-registration"></a>Járművek jelentése lejárt regisztrációval
A Azure Stream Analytics statikus pillanatképeket használhat a hivatkozási adatstreamekkel való csatlakozáshoz. A funkció bemutatásához használja az alábbi kérdést. A regisztrációs bemenet egy statikus blob JSON-fájl, amely felsorolja a licencek felcímkézett érvényességi idejét. A rendszámtábla összekapcsolásával a rendszer összehasonlítja a hivatkozási adatforgalmat az autópályadíjon áthaladó összes járműre.

Ha egy kereskedelmi jármű regisztrálva van a díjköteles vállalatnál, akkor az az autópályadíj-kezelőn keresztül az ellenőrzés leállítása nélkül továbbítható. A regisztrációs keresési táblázat segítségével azonosíthatja az összes olyan kereskedelmi járművet, amelyen lejárt a regisztráció.

```sql
SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN Registration
ON EntryStream.LicensePlate = Registration.LicensePlate
WHERE Registration.Expired = '1'
```

1. Ismételje meg az előző szakaszban leírt lépéseket az TollApp streaming Job lekérdezési szintaxisának frissítéséhez.

2. Az előző szakaszban leírt lépések megismétlésével tekintse át a CosmosDB kimeneti adatait a folyamatos átviteli feladatokból.

Példa a kimenetre:
```json
    {
        "entrytime": "2018-04-05T08:01:28.0252168Z",
        "licenseplate": "GMT 3221",
        "tollid": 1,
        "registrationid": "763220582",
        "id": "47db0535-9716-4eb2-db58-de7886966cbf",
        "_rid": "y+F8AJ9QWACSAQAAAAAAAA==",
        "_self": "dbs/y+F8AA==/colls/y+F8AJ9QWAA=/docs/y+F8AJ9QWACSAQAAAAAAAA==/",
        "_etag": "\"88007d8d-0000-0000-0000-5ac5d7e20000\"",
        "_attachments": "attachments/",
        "_ts": 1522915298
    }
```

## <a name="scale-out-the-job"></a>A feladatok vertikális felskálázása
A Azure Stream Analytics rugalmasan méretezhető, így nagy mennyiségű adattal kezelhetők. Az Azure Stream Analytics-lekérdezés a **Partition by** záradék használatával közli, hogy ez a lépés milyen nagyságrendű. A **PartitionID** egy speciális oszlop, amelyet a rendszer a bemenet (Event hub) PARTÍCIÓs azonosítójának megfelelően feltesz.

A lekérdezésnek a partíciókhoz való kiskálázásához szerkessze a lekérdezési szintaxist a következő kódra:
```sql
SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
INTO CosmosDB
FROM EntryStream
TIMESTAMP BY EntryTime
PARTITION BY PartitionId
GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId
```

A folyamatos átviteli feladatok vertikális felskálázása több folyamatos átviteli egységre:

1. **Állítsa le** az aktuális feladatot.

2. Frissítse a lekérdezési szintaxist a **< > lekérdezés** lapon, és mentse a módosításokat.

3. A folyamatos átviteli feladatban a CONFIGURE (Konfigurálás) fejléc alatt válassza a **skála** lehetőséget.

4. Csúsztassa a **folyamatos átviteli egységek** csúszkát 1-től 6-ra. A folyamatos átviteli egységek határozzák meg a feladatokhoz tartozó számítási teljesítmény mennyiségét. Kattintson a **Mentés** gombra.

5. **Indítsa el** a folyamatos átviteli feladatot a további skála bemutatásához. A Azure Stream Analytics több számítási erőforráson keresztül osztja el a munkát, és jobb teljesítményt érhet el, és az erőforrások közötti munkát a PARTITION BY záradékban kijelölt oszlop használatával particionálja.

## <a name="monitor-the-job"></a>A feladat figyelése
A **figyelő** terület a futó feladattal kapcsolatos statisztikát tartalmaz. A Storage-fióknak ugyanabban a régióban kell lennie (például a dokumentum többi részeként).

![Azure Stream Analytics feladatok figyelése](media/stream-analytics-build-an-iot-solution-using-stream-analytics/stream-analytics-job-monitoring.png)

A tevékenység- **naplókat** a feladatok irányítópultjának **Beállítások** területéről is elérheti.

## <a name="clean-up-the-tollapp-resources"></a>A TollApp-erőforrások tisztítása
1. Állítsa le a Stream Analytics feladatot a Azure Portalban.

2. Keresse meg azt az erőforráscsoportot, amely a TollApp-sablonnal kapcsolatos nyolc erőforrást tartalmaz.

3. Válassza az **Erőforráscsoport törlése** elemet. A törlés megerősítéséhez írja be az erőforráscsoport nevét.

## <a name="conclusion"></a>Összegzés
Ez a megoldás a Azure Stream Analytics szolgáltatáshoz vezetett. Bemutatta, hogyan konfigurálhatja a Stream Analytics feladathoz tartozó bemeneteket és kimeneteket. Az autópályadíj-alapú adatforgatókönyvben a megoldás a mozgásban lévő adatterületen felmerülő gyakori problémák magyarázatát, valamint azt ismerteti, Hogyan oldhatók meg a Azure Stream Analytics egyszerű SQL-szerű lekérdezésekkel. A megoldás az SQL-bővítményt az időbeli adatkezeléshez használt szerkezetekben ismertette. Megmutatta, hogyan csatlakoztathatók az adatfolyamok, hogyan bővíthetők az adatfolyamok statikus hivatkozási adatokkal, és hogyan méretezhető le egy lekérdezés a nagyobb átviteli sebesség eléréséhez.

Bár ez a megoldás jó bevezetést biztosít, nem végezhető el semmilyen módon. Több lekérdezési mintát is megtalálhat a SAQL nyelv használatával a [gyakori stream Analytics használati mintákra vonatkozó lekérdezési példákban](stream-analytics-stream-analytics-query-patterns.md).