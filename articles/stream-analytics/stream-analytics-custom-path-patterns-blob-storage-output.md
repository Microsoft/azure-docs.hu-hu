---
title: Egyéni blob kimeneti particionálás Azure Stream Analytics
description: Ez a cikk az egyéni DateTime elérésiút-mintákat, valamint a blob Storage-kimenet Azure Stream Analytics-feladatokból származó egyéni mező vagy attribútumok funkcióit ismerteti.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/15/2020
ms.custom: seodec18
ms.openlocfilehash: cb9d8edd24dcc8809f2b207a4db80653b0e140e4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98014036"
---
# <a name="azure-stream-analytics-custom-blob-output-partitioning"></a>Egyéni blob kimeneti particionálás Azure Stream Analytics

Azure Stream Analytics támogatja az egyéni Blobok kimenetének particionálását egyéni mezőkkel vagy attribútumokkal és egyéni DateTime elérésiút-mintákkal. 

## <a name="custom-field-or-attributes"></a>Egyéni mező vagy attribútumok

Az egyéni mezők vagy a bemeneti attribútumok javítják az adatfeldolgozási és jelentéskészítési munkafolyamatokat azáltal, hogy nagyobb mértékben szabályozzák a kimenetet.

### <a name="partition-key-options"></a>Partíciós kulcs beállításai

A bemeneti adatok particionálásához használt partíciós kulcs vagy oszlopnév tartalmazhat bármely, a [blob-nevek](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata)számára elfogadható karaktert. A beágyazott mezőket nem lehet partíciós kulcsként használni, kivéve, ha az aliasokkal együtt használják őket, de bizonyos karaktereket is használhat a fájlok hierarchiájának létrehozásához. A következő lekérdezéssel például létrehozhat egy olyan oszlopot, amely két másik oszlop adatait kombinálja egy egyedi partíciós kulcs létrehozásához.

```sql
SELECT name, id, CONCAT(name, "/", id) AS nameid
```

A partíció kulcsának NVARCHAR (MAX), BIGINT, FLOAT vagy BIT értékűnek kell lennie (1,2 kompatibilitási szint vagy magasabb). A DateTime, a Array és a Records típusok nem támogatottak, de a karakterláncokra konvertált partíciós kulcsként használhatók. További információ: [Azure stream Analytics adattípusok](/stream-analytics-query/data-types-azure-stream-analytics).

### <a name="example"></a>Példa

Tegyük fel, hogy a feladatok olyan külső videojáték-szolgáltatáshoz csatlakoztatott élő felhasználói munkamenetek bemeneti adatait veszik át, ahol a betöltött adatok egy oszlopot tartalmaznak **client_id** a munkamenetek azonosítására. Az adatok **client_id** alapján történő particionálásához állítsa be a blob Path Pattern mezőt úgy, hogy felvegyen egy **{client_id}** partíciós tokent a blob kimeneti tulajdonságaiban a feladatok létrehozásakor. Ahogy a különböző **client_id** értékekkel rendelkező adatok a stream Analytics feladatokon keresztül áramlanak, a kimeneti adatok külön mappákba lesznek mentve, a mappa egyetlen **client_id** értéke alapján.

![Elérésiút-minta ügyfél-azonosítóval](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-path-pattern-client-id.png)

Hasonlóképpen, ha a megadott feladatnak több millió érzékelőkből származó érzékelő adata volt, ahol az egyes érzékelők **sensor_id** voltak, akkor a Path minta **{sensor_id}** lenne, hogy az egyes érzékelők adatai különböző mappákba legyenek particionálva.  


A REST API használatával a kérelemhez használt JSON-fájl kimeneti szakasza a következőhöz hasonló lehet:  

![REST API kimenet](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-rest-output.png)

Miután a feladatot megkezdte, az *ügyfelek* tárolója a következőhöz hasonló lehet:  

![Ügyfelek tárolója](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-clients-container.png)

Az egyes mappák több blobot is tartalmazhatnak, amelyek mindegyike egy vagy több rekordot tartalmaz. A fenti példában a "06000000" címkével ellátott mappában található egyetlen blob a következő tartalommal:

![BLOB tartalma](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-contents.png)

Figyelje meg, hogy a blobban lévő összes rekordhoz tartozik egy **client_id** oszlop, amely megegyezik a mappa nevével, mivel a kimeneti elérési úton lévő kimenet particionálásához használt oszlop **client_id**.

### <a name="limitations"></a>Korlátozások

1. Az elérésiút-minta blob output tulajdonságában csak egy egyéni partíciós kulcs engedélyezett. A következő elérésiút-minták mindegyike érvényes:

   * cluster1/{Date}/{aFieldInMyData}  
   * cluster1/{Time}/{aFieldInMyData}  
   * cluster1/{aFieldInMyData}  
   * cluster1/{Date}/{Time}/{aFieldInMyData} 
   
2. A partíciós kulcsok nem megkülönböztetik a kis-és nagybetűket, így a partíciós kulcsok, például a "John" és a "John" egyenértékűek Emellett a kifejezések nem használhatók partíciós kulcsként. Az **{columna + columnB}** például nem működik.  

3. Ha egy bemeneti adatfolyam a 8000-es számú partíciós kulccsal rendelkező rekordokból áll, a rendszer hozzáfűzi a rekordokat a meglévő blobokhoz, és szükség esetén csak új blobokat hoz létre. Ha a kardinális értéke több mint 8000, a meglévő Blobok nem fognak megjelenni, és az új Blobok nem hozhatók létre tetszőleges számú rekordhoz ugyanazzal a partíciós kulccsal.

4. Ha a blob kimenete nem módosítható, akkor a rendszer minden alkalommal létrehoz egy új blobot, amikor adatokat küld [a rendszer a](../storage/blobs/storage-blob-immutable-storage.md)stream Analytics.

## <a name="custom-datetime-path-patterns"></a>Egyéni DateTime elérésiút-minták

Az egyéni DateTime elérésiút-minták lehetővé teszik olyan kimeneti formátum megadását, amely összehangolja a kaptár-adatfolyam-konvenciókat, így Azure Stream Analytics az adatok Azure-HDInsight való elküldésének lehetősége, és az alsóbb rétegbeli feldolgozáshoz Azure Databricks. Az egyéni DateTime elérésiút-minták egyszerűen implementálva vannak a `datetime` blob kimenetének elérési útja mezőjében szereplő kulcsszóval, a formátum megadásával együtt. Például: `{datetime:yyyy}`.

### <a name="supported-tokens"></a>Támogatott jogkivonatok

A következő formátumú megadási jogkivonatok használhatók önállóan vagy kombinálva egyéni DateTime formátumok eléréséhez:

|Megadási formátum   |Description   |Az eredmények például 2018-01-02T10:06:08|
|----------|-----------|------------|
|{datetime: ÉÉÉÉ}|Az év egy négyjegyű számként|2018|
|{datetime: PP}|Hónap, 01 és 12 között|01|
|{datetime: M}|Hónap 1 és 12 között|1|
|{datetime: DD}|01 és 31 közötti nap|02|
|{datetime: d}|1 és 31 közötti nap|2|
|{datetime: HH}|Óra 24 órás formátumban, 00 és 23 között|10|
|{datetime: PP}|Perc – 00 – 60|06|
|{datetime: m}|Perctől 0-tól 60-ig|6|
|{datetime: SS}|Másodperc/00 – 60|08|

Ha nem kíván egyéni DateTime mintázatot használni, hozzáadhatja a {Date} és/vagy {Time} tokent az elérési út előtagjához, és létrehozhatja a legördülő listát a beépített DateTime formátumokkal.

![Régi DateTime formátumok Stream Analytics](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-old-date-time-formats.png)

### <a name="extensibility-and-restrictions"></a>Bővíthetőség és korlátozások

Annyi tokent használhat, `{datetime:<specifier>}` mint ahogyan az elérési út mintában, amíg el nem éri az elérési út előtagjának karakteres korlátját. A formázási megadások nem kombinálhatók egyetlen jogkivonaton belül a dátum és idő legördülő lista által már felsorolt kombinációk fölé. 

A következő elérésiút-partícióhoz `logs/MM/dd` :

|Érvényes kifejezés   |Érvénytelen kifejezés   |
|----------|-----------|
|`logs/{datetime:MM}/{datetime:dd}`|`logs/{datetime:MM/dd}`|

Ugyanazt a formátumot használhatja többször is az elérési út előtagjaként. A tokent minden alkalommal meg kell ismételni.

### <a name="hive-streaming-conventions"></a>Struktúra streaming konvenciói

A blob Storage egyéni elérésiút-mintái használhatók a kaptár streaming konvencióval, amely arra vár, hogy a mappák a mappa nevében legyenek felcímkézve `column=` .

Például: `year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}/hour={datetime:HH}`.

Az egyéni kimenet kiküszöböli a táblák módosításának gondját, és manuálisan adja hozzá a partíciókat a Azure Stream Analytics és a struktúra közötti port adataihoz. Ehelyett számos mappát lehet automatikusan hozzáadni a paranccsal:

```SQL
MSCK REPAIR TABLE while hive.exec.dynamic.partition true
```

### <a name="example"></a>Példa

Hozzon létre egy Storage-fiókot, egy erőforráscsoportot, egy Stream Analytics feladatot és egy bemeneti forrást a [Azure Stream Analytics Azure Portal](stream-analytics-quick-create-portal.md) gyors útmutatójának megfelelően. A gyors üzembe helyezési útmutatóban használt mintaadatok is használhatók a [githubon](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Samples/GettingStarted/HelloWorldASA-InputStream.json).

Hozzon létre egy blob kimeneti gyűjtőt a következő konfigurációval:

![BLOB kimeneti fogadó Stream Analytics létrehozása](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-create-output-sink.png)

A teljes elérési út mintája a következő:


`year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}`


A művelet elindításakor a rendszer a blob-tárolóban létrehozza az elérésiút-minta alapján létrehozott mappastruktúrát. A napi szintre szűkítheti a részletezést.

![BLOB-kimenet Stream Analytics egyéni elérésiút-mintázattal](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-output-folder-structure.png)

## <a name="next-steps"></a>Következő lépések

* [A Azure Stream Analytics kimenetének megismerése](stream-analytics-define-outputs.md)
