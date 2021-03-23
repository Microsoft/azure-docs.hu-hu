---
title: Ütemezés és végrehajtás a Data Factory
description: Az Azure Data Factory alkalmazás modelljének ütemezési és végrehajtási szempontjainak megismerése.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: f613a14f84999fced888ae72de11328bc7c90811
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104780004"
---
# <a name="data-factory-scheduling-and-execution"></a>Data Factory ütemezés és végrehajtás
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse meg a [folyamat-végrehajtás és eseményindítók](../concepts-pipeline-execution-triggers.md) című cikket.

Ez a cikk ismerteti az Azure Data Factory-alkalmazásmodell ütemezési és végrehajtási aspektusait. Ez a cikk azt feltételezi, hogy tisztában van a Data Factory az alkalmazás modelljével kapcsolatos fogalmak, például a tevékenységek, a folyamatok, a társított szolgáltatások és az adatkészletek alapjaival. A Azure Data Factory alapvető fogalmait a következő cikkekben találja:

* [A Data Factory bemutatása](data-factory-introduction.md)
* [Pipelines](data-factory-create-pipelines.md)
* [Adatkészletek](data-factory-create-datasets.md) 

## <a name="start-and-end-times-of-pipeline"></a>A folyamat kezdő és befejező időpontja
A folyamat csak a **kezdő** és a **befejező** időpont között aktív. A kezdési időpont előtt vagy a befejezési időpont előtt nem hajtható végre. Ha a folyamat szüneteltetve van, a rendszer nem hajtja végre az indítási és befejezési időponttól függetlenül. Ahhoz, hogy egy folyamat fusson, nem szabad szüneteltetni. Ezeket a beállításokat (indítás, Befejezés, szüneteltetett) a folyamat definíciójában találja: 

```json
"start": "2017-04-01T08:00:00Z",
"end": "2017-04-01T11:00:00Z"
"isPaused": false
```

További információ ezekről a tulajdonságokról: [folyamatok létrehozása](data-factory-create-pipelines.md) című cikk. 


## <a name="specify-schedule-for-an-activity"></a>Tevékenység ütemtervének megadása
Nem a végrehajtott folyamat. Ez a folyamat által a folyamat általános környezetében végrehajtott tevékenységek. Egy tevékenységhez ismétlődő ütemezést is megadhat a tevékenység JSON-fájl **Scheduler** szakaszának használatával. Például ütemezhet egy tevékenységet óránkénti futásra az alábbiak szerint:  

```json
"scheduler": {
    "frequency": "Hour",
    "interval": 1
},
```

Ahogy az a következő ábrán is látható, egy adott tevékenységhez tartozó ütemterv megadásával egy sor, a folyamat kezdő és befejező időpontjában eltelt időszakot eredményező ablak jelenik meg. A kieséssel ellátott ablakok rögzített méretű, nem átfedésben lévő, összefüggő időintervallumok. Az adott tevékenységhez tartozó logikai kiesési ablakokat a **tevékenység ablakoknak** nevezzük.

![Feladatütemező – példa](media/data-factory-scheduling-and-execution/scheduler-example.png)

Egy tevékenység **Scheduler** tulajdonsága nem kötelező. Ha ezt a tulajdonságot adja meg, meg kell egyeznie a tevékenység kimeneti adatkészletének definíciójában megadott lépésszám értékével. Jelenleg a kimeneti adatkészlet határozza meg az ütemezést. Ezért akkor is létre kell hoznia egy kimeneti adatkészletet, ha a tevékenység nem eredményez kimenetet. 

## <a name="specify-schedule-for-a-dataset"></a>Adatkészlet ütemtervének megadása
Egy Data Factory folyamat egyik tevékenysége nulla vagy több bemeneti **adatkészletet** is igénybe vehet, és egy vagy több kimeneti adatkészletet hoz létre. Egy tevékenység esetében megadhatja azt a ritmust, amelyen a bemeneti adatok rendelkezésre állnak, vagy a kimeneti adatok az adatkészlet-definíciók **rendelkezésre állási** szakasza alapján állíthatók elő. 

A **rendelkezésre állási** szakasz **gyakorisága** meghatározza az időegységet. A gyakoriság megengedett értékei a következők: perc, óra, nap, hét és hónap. A rendelkezésre állási szakasz **intervallum** tulajdonsága a gyakoriság szorzóját határozza meg. Például: Ha a gyakoriság beállítása nap, és az intervallum értéke 1 a kimeneti adatkészlet esetében, a rendszer naponta állítja elő a kimeneti adatokat. Ha a gyakoriságot percben adja meg, javasoljuk, hogy az intervallumot 15-nél kevesebb értékre állítsa be. 

A következő példában a bemeneti adatok óránként, a kimeneti adatok pedig óránként () lesznek elérhetők `"frequency": "Hour", "interval": 1` . 

**Bemeneti adatkészlet:** 

```json
{
    "name": "AzureSqlInput",
    "properties": {
        "published": false,
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```


**Kimeneti adatkészlet**

```json
{
    "name": "AzureBlobOutput",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mypath/{Year}/{Month}/{Day}/{Hour}",
            "format": {
                "type": "TextFormat"
            },
            "partitionedBy": [
                { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
                { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
                { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
                { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" }}
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Jelenleg **a kimeneti adatkészlet vezérli az ütemtervet**. Ez azt jelenti, hogy a kimeneti adatkészlethez megadott ütemtervet egy tevékenység futásidőben történő futtatására használják. Ezért akkor is létre kell hoznia egy kimeneti adatkészletet, ha a tevékenység nem eredményez kimenetet. Ha a tevékenység nem fogad semmilyen bemenetet, kihagyhatja a bemeneti adatkészlet létrehozását. 

A következő folyamat-definícióban az **ütemező** tulajdonság a tevékenység ütemezésének megadására szolgál. Ez a tulajdonság nem kötelező. Jelenleg a tevékenység ütemtervének meg kell egyeznie a kimeneti adatkészlethez megadott ütemtervvel.
 
```json
{
    "name": "SamplePipeline",
    "properties": {
        "description": "copy activity",
        "activities": [
            {
                "type": "Copy",
                "name": "AzureSQLtoBlob",
                "description": "copy activity",
                "typeProperties": {
                    "source": {
                        "type": "SqlSource",
                        "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 100000,
                        "writeBatchTimeout": "00:05:00"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureSQLInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }
        ],
        "start": "2017-04-01T08:00:00Z",
        "end": "2017-04-01T11:00:00Z"
    }
}
```

Ebben a példában a tevékenység óránként fut a folyamat kezdési és befejezési időpontja között. A kimeneti adatokat óránként, három órás időszakra készíti elő a rendszer (8 – 9 órakor, 9 – 10 ÓRAKOR és 10 ÓRAKOR – 11 ÓRAKOR). 

A tevékenység-Futtatás által felhasznált vagy előállított adategységeket **adatszeletnek** nevezzük. Az alábbi ábrán egy olyan tevékenység látható, amely egy bemeneti adatkészlettel és egy kimeneti adatkészlettel rendelkezik: 

![Rendelkezésre állási ütemező](./media/data-factory-scheduling-and-execution/availability-scheduler.png)

Az ábrán a bemeneti és a kimeneti adatkészlet óránkénti adatszeletei láthatók. A diagramon három olyan bemeneti szelet látható, amely készen áll a feldolgozásra. Az 10-11-es tevékenység folyamatban van, ami a 10-11 AM kimeneti szeletet állítja elő. 

A (z) [SliceStart](data-factory-functions-variables.md#data-factory-system-variables) és a [SliceEnd](data-factory-functions-variables.md#data-factory-system-variables)változó használatával elérheti az adatkészlet JSON-fájljában az aktuális szelethez társított időintervallumot. Hasonlóképpen, a WindowStart és a WindowEnd használatával is elérheti a tevékenységi időszakhoz társított időintervallumot. Egy tevékenység ütemtervének meg kell egyeznie a tevékenység kimeneti adatkészletének ütemtervével. Ezért a SliceStart és a SliceEnd értékek ugyanazok, mint a WindowStart és a WindowEnd érték. További információ ezekről a változókról: [Data Factory függvények és rendszerváltozók](data-factory-functions-variables.md#data-factory-system-variables) cikkei.  

Ezeket a változókat különböző célokra használhatja a tevékenység JSON-ban. Használhatja például az idősoros adatokat jelölő bemeneti és kimeneti adatkészletből származó adatok kiválasztását (például: 8 – 9). Ez a példa a **WindowStart** és a **WindowEnd** segítségével kiválasztja a tevékenység futtatásához szükséges adatokat, és átmásolja a megfelelő **folderPath** rendelkező blobba. A **folderPath** paraméter úgy van, hogy minden órában külön mappa legyen.  

Az előző példában a bemeneti és a kimeneti adatkészletekhez megadott ütemterv megegyezik (óránként). Ha a tevékenység bemeneti adatkészlete eltérő gyakorisággal érhető el, azaz 15 percenként, az ezt a kimeneti adatkészletet előállító tevékenység még óránként egyszer fut, mivel a kimeneti adatkészlet a tevékenység ütemtervét vezeti. További információ: [különböző gyakorisággal rendelkező adatkészletek modellezése](#model-datasets-with-different-frequencies).

## <a name="dataset-availability-and-policies"></a>Adatkészlet rendelkezésre állása és házirendjei
A gyakoriság és az intervallum tulajdonságainak használatát az adatkészlet definíciójának rendelkezésre állási szakaszában tekintheti meg. Van néhány egyéb tulajdonság, amely hatással van egy tevékenység ütemezésére és végrehajtására. 

### <a name="dataset-availability"></a>Adatkészlet rendelkezésre állása 
A következő táblázat a **rendelkezésre állási** szakaszban használható tulajdonságokat ismerteti:

| Tulajdonság | Leírás | Kötelező | Alapértelmezett |
| --- | --- | --- | --- |
| frequency |Megadja az adatkészlet-szelet gyártásának időegységét.<br/><br/><b>Támogatott gyakoriság</b>: perc, óra, nap, hét, hónap |Igen |NA |
| interval |A gyakoriság szorzóját adja meg<br/><br/>A "Frequency x Interval" érték határozza meg, hogy milyen gyakran történjen a szelet előállítása.<br/><br/>Ha az adatkészletet óránként kell darabolni, a <b>gyakoriságot</b> <b>óra</b>értékre kell állítani, és az <b>intervallumot</b> <b>1-re</b>kell állítania.<br/><br/><b>Megjegyzés</b>: Ha a gyakoriságot percben adja meg, azt javasoljuk, hogy az intervallumot 15-nél kevesebbre állítsa be |Igen |NA |
| stílus |Megadja, hogy a szelet az intervallum elején/végén legyen-e előkészítve.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Ha a gyakoriság értéke hónap, és a Style EndOfInterval értékre van állítva, a szelet a hónap utolsó napján jön létre. Ha a stílus StartOfInterval értékre van állítva, a szelet a hónap első napján jön létre.<br/><br/>Ha a gyakoriság beállítása nap, a stílus pedig EndOfInterval, a szelet a nap utolsó órájában jön létre.<br/><br/>Ha a gyakoriság értéke óra, és a stílus értéke EndOfInterval, a szelet az óra végén jön létre. A szeletek esetében például 1 – 2 PM-időszak esetén a SZELET 2 ÓRAKOR jön létre. |Nem |EndOfInterval |
| anchorDateTime |Meghatározza a ütemező által az adatkészlet-szeletek határainak kiszámításához használt abszolút pozíciót. <br/><br/><b>Megjegyzés</b>: Ha a AnchorDateTime olyan részek vannak, amelyek részletesebbek, mint a gyakoriság, akkor a rendszer figyelmen kívül hagyja a további szemcsés részeket. <br/><br/>Ha például az <b>intervallum</b> <b>óránként</b> (Frequency: Hour és Interval: 1), a <b>AnchorDateTime</b> pedig <b>perc és másodperc</b>értéket tartalmaz, a rendszer figyelmen kívül hagyja a AnchorDateTime <b>perc és másodperc</b> részét. |Nem |01/01/0001 |
| offset |TimeSpan, amely az összes adatkészlet összes szeletének kezdetét és végét eltolja. <br/><br/><b>Megjegyzés</b>: Ha a anchorDateTime és az eltolás is meg van adva, az eredmény a kombinált eltolás. |Nem |NA |

### <a name="offset-example"></a>eltolási példa
Alapértelmezés szerint a napi ( `"frequency": "Day", "interval": 1` ) szeletek a 12 UTC időpontnál (éjfélkor) kezdődnek. Ha a kezdési időpontot 6 UTC-időre szeretné használni, állítsa be az eltolást az alábbi kódrészletben látható módon: 

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>anchorDateTime példa
A következő példában az adatkészlet 23 óránként egyszer jön létre. Az első szelet a anchorDateTime által megadott időpontban kezdődik `2017-04-19T08:00:00` (UTC idő szerint).

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 23,    
    "anchorDateTime":"2017-04-19T08:00:00"    
}
```

### <a name="offsetstyle-example"></a>eltolás/stílus – példa
A következő adatkészlet havi adathalmaz, amely minden hónap 3. napján, 8:00 ÓRAKOR () van előkészítve `3.08:00:00` :

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

### <a name="dataset-policy"></a>Adatkészlet-házirend
Az adatkészlet rendelkezhet egy olyan érvényesítési házirenddel, amely meghatározza, hogy a szeletek végrehajtásával létrehozott adatokat hogyan lehet érvényesíteni, mielőtt készen áll a felhasználásra. Ilyen esetekben a szelet végrehajtásának befejeződése után a kimeneti szelet állapota úgy módosul, hogy az **Érvényesítés** alállapotára **várakozik** . A szeletek ellenőrzése után a szelet állapota **készre** változik. Ha egy adatszeletet állítottak elő, de nem adták át az ellenőrzést, akkor a rendszer nem dolgozza fel a szelettől függő alsóbb rétegbeli szeletek tevékenységeit. A [folyamatok figyelése és kezelése](data-factory-monitor-manage-pipelines.md) a Data Factory adatszeletek különböző állapotait fedi le.

Az adatkészlet definíciójának **szabályzat** szakasza meghatározza azokat a feltételeket vagy feltételt, amelyeknek az adatkészlet-szeleteknek teljesíteniük kell. A következő táblázat a **szabályzat** szakaszban használható tulajdonságokat ismerteti:

| Házirend neve | Leírás | Alkalmazva erre | Kötelező | Alapértelmezett |
| --- | --- | --- | --- | --- |
| minimumSizeMB | Ellenőrzi, hogy egy **Azure-blobban** lévő adat megfelel-e a minimális méretre vonatkozó követelményeknek (megabájtban). |Azure-blob |Nem |NA |
| minimumRows | Ellenőrzi, hogy a **Azure SQL Database** vagy az **Azure táblában** lévő adatmennyiség tartalmazza-e a sorok minimális számát. |<ul><li>Azure SQL Database</li><li>Azure-tábla</li></ul> |Nem |NA |

#### <a name="examples"></a>Példák
**minimumSizeMB:**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

**minimumRows**

```json
"policy":
{
    "validation":
    {
        "minimumRows": 100
    }
}
```

További információt ezekről a tulajdonságokról és példákról az [adatkészletek létrehozása](data-factory-create-datasets.md) című cikkben talál. 

## <a name="activity-policies"></a>Tevékenység-szabályzatok
A házirendek hatással vannak egy tevékenység futásidejű viselkedésére, különösen akkor, ha egy tábla szeletét dolgozzák fel. A részleteket a következő táblázat tartalmazza.

| Tulajdonság | Megengedett értékek | Alapértelmezett érték | Leírás |
| --- | --- | --- | --- |
| Egyidejűség |Egész szám <br/><br/>Maximális érték: 10 |1 |A tevékenység egyidejű végrehajtásának száma.<br/><br/>Meghatározza, hogy hány párhuzamos tevékenység-végrehajtás történhet a különböző szeleteken. Ha például egy tevékenységnek az elérhető adatmennyiség nagy készletén kell haladnia, a nagyobb párhuzamossági érték felgyorsítja az adatfeldolgozást. |
| executionPriorityOrder |NewestFirst<br/><br/>OldestFirst |OldestFirst |Meghatározza a feldolgozás alatt álló adatszeletek sorrendjét.<br/><br/>Ha például 2 szelete van (egy 16:00-kor történik, egy másik pedig 5 órakor), és mindkettő függőben van. Ha úgy állítja be a executionPriorityOrder, hogy a NewestFirst, a szeletet 5 ÓRAKOR dolgozza fel a rendszer. Hasonlóképpen, ha úgy állítja be a executionPriorityORder, hogy a OldestFIrst legyen, akkor a szelet 4 ÓRAKOR lesz feldolgozva. |
| retry |Egész szám<br/><br/>A maximális érték lehet 10 |0 |Az újrapróbálkozások száma, mielőtt a szelet adatfeldolgozása sikertelenként van megjelölve. Az adatszeletek tevékenység-végrehajtásának újrapróbálkozása a megadott újrapróbálkozások számával történik. Az újrapróbálkozás a hiba után a lehető leghamarabb megtörténik. |
| timeout |időtartam |00:00:00 |A tevékenység időtúllépése. Példa: 00:10:00 (a 10 perc időtúllépését jelenti)<br/><br/>Ha egy érték nincs megadva vagy 0, az időtúllépés végtelen.<br/><br/>Ha egy szelet adatfeldolgozási ideje meghaladja az időtúllépési értéket, a rendszer megszakítja, és a rendszer megkísérli a feldolgozást. Az újrapróbálkozások száma az Újrapróbálkozás tulajdonságtól függ. Időtúllépés esetén az állapot időtúllépés értékre van állítva. |
| késedelem |időtartam |00:00:00 |A szelet adatfeldolgozásának megkezdése előtti késleltetés meghatározása.<br/><br/>Az adatszeletek tevékenységének végrehajtása akkor indul el, ha a késés a várt végrehajtási idő alatt van.<br/><br/>Példa: 00:10:00 (a 10 perc késleltetését jelenti) |
| longRetry |Egész szám<br/><br/>Maximális érték: 10 |1 |A hosszú újrapróbálkozási kísérletek száma a szelet végrehajtásának meghiúsulása előtt.<br/><br/>a longRetry-kísérletek longRetryInterval szerint vannak elfoglalva. Ha tehát az újrapróbálkozási kísérletek közötti időt kell megadnia, használja a longRetry. Ha az újrapróbálkozási és a longRetry is meg van adva, az egyes longRetry-kísérletek az újrapróbálkozási kísérleteket is tartalmazzák, és a kísérletek maximális száma újrapróbálkozás * longRetry.<br/><br/>Ha például a következő beállítások szerepelnek a tevékenység-házirendben:<br/>Újrapróbálkozás: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>Tegyük fel, hogy csak egy szeletet kell végrehajtani (az állapot várakozik), és a tevékenység végrehajtása minden alkalommal meghiúsul. Kezdetben 3 egymást követő végrehajtási kísérlet lenne. Minden kísérlet után a szelet állapota újra próbálkozik. Az első 3 próbálkozás után a szelet állapota LongRetry lesz.<br/><br/>Egy óra (azaz a longRetryInteval értéke) után egy másik 3 egymást követő végrehajtási kísérlet lenne. Ezt követően a szelet állapota meghiúsul, és a rendszer nem próbálkozik újra. Ezért összesen 6 kísérlet történt.<br/><br/>Ha bármelyik végrehajtás sikeres, a szelet állapota készen áll, és a rendszer nem próbálkozik újra.<br/><br/>a longRetry olyan helyzetekben használhatók, ahol a függő adat nem determinisztikus időpontokban érkezik, vagy az általános környezet az adatfeldolgozási folyamat alatt álló adatfeldolgozás. Ilyen esetekben előfordulhat, hogy egy másik után újrapróbálkozik, és a kívánt kimenet eltelte után egy idő elteltével nem jár sikerrel.<br/><br/>Figyelmeztetés: ne állítson be magas értéket a longRetry vagy a longRetryInterval. A magasabb értékek jellemzően más rendszerszintű problémákat jelentenek. |
| longRetryInterval |időtartam |00:00:00 |A hosszú újrapróbálkozási kísérletek közötti késleltetés |

További információ: [folyamatok](data-factory-create-pipelines.md) című cikk. 

## <a name="parallel-processing-of-data-slices"></a>Az adatszeletek párhuzamos feldolgozása
A folyamat kezdő dátumát a múltban állíthatja be. Ha így tesz, Data Factory a múltban automatikusan kiszámítja (visszatölti) az összes adatszeletet, és megkezdi a feldolgozást. Például: Ha létrehoz egy folyamatot a 2017-04-01 kezdési dátummal, és az aktuális dátum a 2017-04-10. Ha a kimeneti adatkészlet lépésszám napi szinten van, akkor a Data Factory elindítja a 2017-04-01 és 2017-04-09 közötti összes szelet feldolgozását, mivel a kezdő dátum a múltban van. A 2017-04-10-es szelet még nincs feldolgozva, mert a rendelkezésre állási szakaszban szereplő Style tulajdonság értéke alapértelmezés szerint EndOfInterval. A rendszer először a legrégebbi szeletet dolgozza fel, mivel a executionPriorityOrder alapértelmezett értéke OldestFirst. A Style tulajdonság leírását az [adatkészlet rendelkezésre állása](#dataset-availability) című szakaszban találja. A executionPriorityOrder szakasz leírását a [tevékenység-szabályzatok](#activity-policies) című szakaszban találja. 

A visszafelé feldolgozható adatszeleteket konfigurálhatja párhuzamosan a tevékenység JSON-fájljának **házirend** szakaszában található **Egyidejűség** tulajdonság beállításával. Ez a tulajdonság határozza meg, hogy hány párhuzamos tevékenység-végrehajtás végezhető el különböző szeleteken. Az egyidejűség tulajdonság alapértelmezett értéke 1. Ezért alapértelmezés szerint az egyik szelet feldolgozása egyszerre történik. A maximális érték 10. Ha egy folyamatnak az elérhető adatmennyiség nagy készletén kell haladnia, nagyobb párhuzamossági értékkel kell felgyorsítani az adatfeldolgozást. 

## <a name="rerun-a-failed-data-slice"></a>Sikertelen adatszelet újrafuttatása
Amikor hiba történik egy adatszelet feldolgozásakor, megtudhatja, miért nem sikerült a szeletek feldolgozása Azure Portal pengék használatával, vagy az alkalmazás figyelésével és kezelésével. További részletekért lásd: [folyamatok figyelése és kezelése Azure Portal Blades](data-factory-monitor-manage-pipelines.md) vagy [monitoring and Management app](data-factory-monitor-manage-app.md) használatával.

Vegye figyelembe a következő példát, amely két tevékenységet mutat be. Activity1 és a 2. tevékenység. A Activity1 felhasználja a Dataset1 elemet szeletét, és egy Dataset2-szeletet hoz létre, amelyet a Activity2 bemenetként használ a végső adatkészlet szeletének létrehozásához.

![Sikertelen szelet](./media/data-factory-scheduling-and-execution/failed-slice.png)

Az ábrán látható, hogy a legutóbbi három szeletből nem sikerült előállítani a 9-10-es Dataset2-szeletet. Data Factory automatikusan nyomon követi az idősorozat-adatkészlet függőségét. Ennek eredményeképpen nem indítja el a 9-10-as alsóbb rétegbeli szelet tevékenység-futtatását.

Data Factory monitorozási és felügyeleti eszközök lehetővé teszik a hibás szelet diagnosztikai naplóiba való részletezést, így könnyen megtalálhatja a probléma okát, és kijavíthatja azt. A probléma kijavítása után egyszerűen elindíthatja a tevékenység futtatását, hogy a hibás szeletet hozzon létre. Az adatszeletek állapot-átváltásának újrafuttatásával és értelmezésével kapcsolatos további információkért lásd: [folyamatok figyelése és kezelése Azure Portal Blades](data-factory-monitor-manage-pipelines.md) vagy [monitoring and Management app](data-factory-monitor-manage-app.md)használatával.

Miután újrafuttatta a **Dataset2**-hez készült 9-10-es szeletet, Data Factory elindítja a 9-10 am függő szelet futtatását a végső adatkészleten.

![Sikertelen szeletelő újrafuttatása](./media/data-factory-scheduling-and-execution/rerun-failed-slice.png)

## <a name="multiple-activities-in-a-pipeline"></a>Több tevékenység egy adott folyamatban
Egy folyamathoz azonban több tevékenység is tartozhat. Ha egy folyamat több tevékenységgel rendelkezik, és egy tevékenység kimenete nem egy másik tevékenység bemenete, akkor a tevékenységek párhuzamosan futhatnak, ha a tevékenységekhez tartozó bemeneti adatszeletek készen állnak.

Összefűzhet két tevékenységet (vagyis egymás után futtathatja őket), ha az egyik tevékenység kimeneti adatkészletét a másik tevékenység bemeneti adatkészleteként állítja be. A tevékenységek lehetnek ugyanabban a folyamatban vagy különböző folyamatokban. A második tevékenység csak akkor fut le, ha az első Befejezés sikeresen befejeződött.

Vegyük például a következő esetet, amikor egy folyamat két tevékenységgel rendelkezik:

1. A D1-es külső bemeneti adatkészletet igénylő a1-es tevékenység, amely D2 kimeneti adatkészletet hoz létre.
2. A D2 adatkészletből bemenetet igénylő a2-es tevékenység, amely a D3 kimeneti adatkészletet állítja elő.

Ebben a forgatókönyvben az a1 és az a2 tevékenységek ugyanabban a folyamatban vannak. Az A1-es tevékenység akkor fut le, amikor a külső adatforrások elérhetők, és elérte az ütemezett rendelkezésre állási gyakoriságot. Az A2-es tevékenység akkor fut le, amikor a D2-ből származó ütemezett szeletek elérhetővé válnak, és a rendelkezésre állási gyakoriság elérhető. Ha hiba van a D2 adatkészlet egyik szeletében, az a2 nem fut az adott szeletre, amíg elérhetővé nem válik.

Az azonos folyamat mindkét tevékenységével rendelkező diagram nézet a következő ábrához hasonlóan fog kinézni:

![Tevékenységek láncolása ugyanabban a folyamatban](./media/data-factory-scheduling-and-execution/chaining-one-pipeline.png)

Ahogy azt korábban említettük, a tevékenységek különböző folyamatokban lehetnek. Ilyen esetben a diagram nézet a következő ábrához hasonlóan fog kinézni:

![Tevékenységek láncolása két folyamatban](./media/data-factory-scheduling-and-execution/chaining-two-pipelines.png)

A példában szereplő függelékben tekintse meg a másolás szekvenciálisan szakaszát.

## <a name="model-datasets-with-different-frequencies"></a>Különböző gyakoriságú adatkészletek modellezése
A mintákban a bemeneti és a kimeneti adatkészletek, valamint a tevékenység-ütemterv ablakának gyakorisága azonos. Bizonyos forgatókönyvek esetében a kimenetet egy vagy több bemenet gyakorisága alapján kell létrehozni. Data Factory támogatja a forgatókönyvek modellezését.

### <a name="sample-1-produce-a-daily-output-report-for-input-data-that-is-available-every-hour"></a>1. példa: napi kimeneti jelentés készítése minden órában elérhető bemeneti adatokhoz
Vegyünk például egy olyan forgatókönyvet, amelyben az Azure Blob Storage-ban óránként elérhető érzékelőkből származó mérési adatok szerepelnek. Napi összesítő jelentést szeretne készíteni olyan statisztikákkal, mint például a Mean, a maximum és a minimum a nap [Data Factory kaptár tevékenységgel](data-factory-hive-activity.md).

Az alábbi módszerekkel modellezheti ezt a forgatókönyvet Data Factory használatával:

**Bemeneti adatkészlet**

Az óránkénti bemeneti fájlok el lesznek dobva a mappában az adott napon. A bemenet rendelkezésre állása **óránként** van beállítva (frekvencia: óra, intervallum: 1).

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Kimeneti adatkészlet**

A rendszer minden nap létrehoz egy kimeneti fájlt a nap mappájába. A kimenet rendelkezésre állása **napi** beállítás (gyakoriság: nap és időköz: 1).

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Tevékenység: struktúra tevékenysége egy folyamatban**

A kaptár parancsfájlja a megfelelő *datetime* adatokat fogadja el olyan paraméterekként, amelyek a **WindowStart** változót használják az alábbi kódrészletben látható módon. A struktúra parancsfájlja ezt a változót használja az adatoknak a nap megfelelő mappájából való betöltéséhez, majd az Összesítés futtatásával hozza létre a kimenetet.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-01-01T08:00:00",
    "end":"2015-01-01T11:00:00",
    "description":"hive activity",
    "activities": [
        {
            "name": "SampleHiveActivity",
            "inputs": [
                {
                    "name": "AzureBlobInput"
                }
            ],
            "outputs": [
                {
                    "name": "AzureBlobOutput"
                }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "type": "HDInsightHive",
            "typeProperties": {
                "scriptPath": "adftutorial\\hivequery.hql",
                "scriptLinkedService": "StorageLinkedService",
                "defines": {
                    "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
                    "Month": "$$Text.Format('{0:MM}',WindowStart)",
                    "Day": "$$Text.Format('{0:dd}',WindowStart)"
                }
            },
            "scheduler": {
                "frequency": "Day",
                "interval": 1
            },            
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 2,
                "timeout": "01:00:00"
            }
         }
     ]
   }
}
```

Az alábbi ábrán egy adatfüggőségi pontból származó forgatókönyv látható.

![Adatfüggőség](./media/data-factory-scheduling-and-execution/data-dependency.png)

Az egyes napok kimeneti szelete 24 óránkénti szelettől függ egy bemeneti adatkészletből. A Data Factory automatikusan kiszámítja ezeket a függőségeket úgy, hogy kideríti a bemeneti adatszeleteket, amelyek ugyanabban az időszakban esnek, mint a készítendő kimeneti szelet. Ha a 24 bemeneti szelet bármelyike nem érhető el, Data Factory várja, amíg a bemeneti szelet készen áll a napi tevékenység futtatásának megkezdése előtt.

### <a name="sample-2-specify-dependency-with-expressions-and-data-factory-functions"></a>2. minta: függőség meghatározása kifejezésekkel és Data Factory függvényekkel
Vegyünk egy másik forgatókönyvet. Tegyük fel, hogy van egy kaptár tevékenysége, amely két bemeneti adatkészletet dolgoz fel. Az egyikük naponta új adatmennyiséggel rendelkezik, de az egyikük minden héten új adatmennyiséget kap. Tegyük fel, hogy egy összekapcsolást szeretne végezni a két bemenet között, és minden nap kimenetet hoz létre.

Az egyszerű megközelítés, amelyben a Data Factory automatikusan kiírja a megfelelő bemeneti szeleteket a feldolgozáshoz a kimeneti adatszelet időszakára való igazítással.

Minden tevékenység futtatásához meg kell adnia, hogy a Data Factory a heti bemeneti adatkészlethez a múlt heti adatszeletet használja. Ezt a viselkedést az alábbi kódrészletben látható módon Azure Data Factory függvények használatával hajthatja végre.

**Input1: Azure-Blob**

Az első bemenet az Azure Blob naponta frissül.

```json
{
  "name": "AzureBlobInputDaily",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Input2: Azure-Blob**

A Input2 az Azure Blob hetente frissül.

```json
{
  "name": "AzureBlobInputWeekly",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 7
    }
  }
}
```

**Kimenet: Azure Blob**

A rendszer minden nap létrehoz egy kimeneti fájlt a mappában az adott napon. A kimenet rendelkezésre állása **nap** (frekvencia: nap, intervallum: 1).

```json
{
  "name": "AzureBlobOutputDaily",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Tevékenység: struktúra tevékenysége egy folyamatban**

A kaptár tevékenység a két bemenetet hozza létre, és minden nap létrehoz egy kimeneti szeletet. Minden nap kimeneti szeletét megadhatja az előző heti bemeneti szelettől az alábbiak szerint.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-01-01T08:00:00",
    "end":"2015-01-01T11:00:00",
    "description":"hive activity",
    "activities": [
      {
        "name": "SampleHiveActivity",
        "inputs": [
          {
            "name": "AzureBlobInputDaily"
          },
          {
            "name": "AzureBlobInputWeekly",
            "startTime": "Date.AddDays(SliceStart, - Date.DayOfWeek(SliceStart))",
            "endTime": "Date.AddDays(SliceEnd,  -Date.DayOfWeek(SliceEnd))"  
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutputDaily"
          }
        ],
        "linkedServiceName": "HDInsightLinkedService",
        "type": "HDInsightHive",
        "typeProperties": {
          "scriptPath": "adftutorial\\hivequery.hql",
          "scriptLinkedService": "StorageLinkedService",
          "defines": {
            "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
            "Month": "$$Text.Format('{0:MM}',WindowStart)",
            "Day": "$$Text.Format('{0:dd}',WindowStart)"
          }
        },
        "scheduler": {
          "frequency": "Day",
          "interval": 1
        },            
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 2,  
          "timeout": "01:00:00"
        }
       }
     ]
   }
}
```

A Data Factory által támogatott függvények és rendszerváltozók listáját [Data Factory függvények és rendszerváltozók](data-factory-functions-variables.md) című részben tekintheti meg.

## <a name="appendix"></a>Függelék

### <a name="example-copy-sequentially"></a>Példa: másolás szekvenciálisan
Több másolási művelet is futtatható egymás után szekvenciális/rendezett módon. Előfordulhat például, hogy egy folyamaton belül két másolási tevékenység van (CopyActivity1 és CopyActivity2) a következő bemeneti adatok kimeneti adatkészletekkel:   

CopyActivity1

Bemenet: adatkészlet. Kimenet: Dataset2.

CopyActivity2

Bemenet: Dataset2.  Kimenet: Dataset3.

A CopyActivity2 csak akkor fut le, ha a CopyActivity1 sikeresen futott, és a Dataset2 elérhető.

Itt látható a minta folyamat JSON-fájlja:

```json
{
    "name": "ChainActivities",
    "properties": {
        "description": "Run activities in sequence",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset1"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob1ToBlob2",
                "description": "Copy data from a blob to another"
            },
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset3"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob2ToBlob3",
                "description": "Copy data from a blob to another"
            }
        ],
        "start": "2016-08-25T01:00:00Z",
        "end": "2016-08-25T01:00:00Z",
        "isPaused": false
    }
}
```

Figyelje meg, hogy a példában az első másolási tevékenység (Dataset2) kimeneti adatkészlete a második tevékenység bemenetként van megadva. Ezért a második tevékenység csak akkor fut le, ha az első tevékenység kimeneti adatkészlete készen áll.  

A példában a CopyActivity2 különböző bemenettel rendelkezhet, például a Dataset3, de a Dataset2 bemenetként adja meg a CopyActivity2, így a tevékenység addig nem fut le, amíg a CopyActivity1 be nem fejeződik. Például:

CopyActivity1

Bemenet: Dataset1 elemet. Kimenet: Dataset2.

CopyActivity2

Bemenetek: Dataset3, Dataset2. Kimenet: Dataset4.

```json
{
    "name": "ChainActivities",
    "properties": {
        "description": "Run activities in sequence",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset1"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlobToBlob",
                "description": "Copy data from a blob to another"
            },
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset3"
                    },
                    {
                        "name": "Dataset2"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset4"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob3ToBlob4",
                "description": "Copy data from a blob to another"
            }
        ],
        "start": "2017-04-25T01:00:00Z",
        "end": "2017-04-25T01:00:00Z",
        "isPaused": false
    }
}
```

Figyelje meg, hogy a példában két bemeneti adatkészlet van megadva a második másolási tevékenységhez. Ha több bemenet van megadva, a rendszer csak az első bemeneti adatkészletet használja az adatok másolásához, de más adatkészleteket is függőségként használ. A CopyActivity2 csak az alábbi feltételek teljesülése után indul el:

* A CopyActivity1 sikeresen befejeződött, és a Dataset2 elérhető. Ez az adatkészlet nem használatos az adatok Dataset4 való másolása során. Csak ütemezési függőségként működik a CopyActivity2 esetében.   
* A Dataset3 elérhető. Ez az adatkészlet a célhelyre másolt adatokat jelöli. 
