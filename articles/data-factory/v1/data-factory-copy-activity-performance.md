---
title: Útmutató a másolási tevékenységek teljesítményéhez és finomhangolásához
description: A másolási tevékenység használatakor a Azure Data Factory adatáthelyezési teljesítményét befolyásoló főbb tényezők ismertetése.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/25/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 9a890719de39a71d8336d39f9932e73f7baccf87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100377210"
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Útmutató a másolási tevékenységek teljesítményéhez és finomhangolásához

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-copy-activity-performance.md)
> * [2-es verzió (aktuális verzió)](../copy-activity-performance.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse meg a következő témakört: a [másolási tevékenység teljesítményének és hangolási útmutatójának Data Factory](../copy-activity-performance.md).

Azure Data Factory másolási tevékenység egy első osztályú biztonságos, megbízható és nagy teljesítményű betöltési megoldást biztosít. Lehetővé teszi, hogy naponta több tízezer adat másolása legyen a Felhőbeli és a helyszíni adattárakban. A villámgyors betöltési teljesítmény kulcsfontosságú ahhoz, hogy a központi "Big Data" problémára koncentráljon: fejlett analitikai megoldások kiépítése és az összes adat részletes elemzésének biztosítása.

Az Azure nagyvállalati szintű adattárolási és adattárház-megoldásokat kínál, a másolási tevékenység pedig egy könnyen konfigurálható és beállítható, kifejezetten optimalizált betöltési élményt nyújt. Csak egyetlen másolási tevékenységgel érheti el a következőket:

* Az **Azure szinapszis analyticsbe** való betöltés **1,2 GB/s**-on. A használati eseteket bemutató bemutatóért lásd: [1 TB betöltése az Azure szinapszis Analytics szolgáltatásba 15 perc alatt, Azure Data Factory](data-factory-load-sql-data-warehouse.md).
* Adatbetöltése az **Azure Blob Storage** -ba **1,0 GB/s**
* Az adattöltés **Azure Data Lake Store** **1,0 GB/s**

Ez a cikk a következőket ismerteti:

* A támogatott forrás-és fogadó adattárak [teljesítmény-hivatkozási számai](#performance-reference) , amelyek segítenek a projekt megtervezésében.
* Azok a funkciók, amelyek növelhetik a másolási sebességet különböző forgatókönyvekben, beleértve a [Felhőbeli adatáthelyezési egységeket](#cloud-data-movement-units), a [párhuzamos másolást](#parallel-copy)és a [szakaszos másolást](#staged-copy);
* [Teljesítmény-finomhangolási útmutató](#performance-tuning-steps) a teljesítmény finomhangolásához, valamint a másolási teljesítményre hatással lehet a legfontosabb tényezőkre.

> [!NOTE]
> Ha nem ismeri a másolási tevékenységet általánosságban, tekintse át a cikk olvasása a [másolási tevékenység használatával](data-factory-data-movement-activities.md) című cikket.
>

## <a name="performance-reference"></a>Teljesítmény-referenciák

Az alábbi táblázatban látható a másolási átviteli sebesség (MB/s) a megadott forrás-és fogadó párok esetében a házon belüli tesztelés alapján. Az összehasonlításban azt is bemutatja, hogy a [Felhőbeli adatáthelyezési egységek](#cloud-data-movement-units) és a [adatkezelés átjáró skálázhatósága](data-factory-data-management-gateway-high-availability-scalability.md) (több átjáró-csomópont) milyen különböző beállításokkal tud segíteni a másolási teljesítményben.

![Teljesítmény-mátrix](./media/data-factory-copy-activity-performance/CopyPerfRef.png)

>[!IMPORTANT]
>Azure Data Factory 1. verziójában a felhőből a felhőbe történő másolás minimális Felhőbeli adatáthelyezési egysége kettő. Ha nincs megadva, tekintse meg a [Felhőbeli adatátviteli egységekben](#cloud-data-movement-units)használt alapértelmezett adatáthelyezési egységeket.

**Megjegyzés:**
* Az átviteli sebesség kiszámítása a következő képlettel történik: [a forrásból beolvasott adatok mérete és a másolási tevékenység futási időtartama].
* A táblázatban lévő teljesítménymutatók a [TPC-H](http://www.tpc.org/tpch/) adathalmaz használatával lettek megmérve egyetlen másolási tevékenységben.
* Az Azure-adattárakban a forrás és a fogadó ugyanabban az Azure-régióban található.
* A helyszíni és a Felhőbeli adattárak közötti hibrid másoláshoz az egyes átjáró-csomópontok olyan gépen futnak, amely külön volt a helyszíni adattárban, az alábbi specifikációval. Ha egyetlen tevékenység fut az átjárón, a másolási művelet a teszt gép CPU-, memória-vagy hálózati sávszélességének csak egy kis részét használta fel. További információ a [adatkezelés átjáróval kapcsolatos szempontokról](#considerations-for-data-management-gateway).
    <table>
    <tr>
        <td>CPU</td>
        <td>32 mag 2,20 GHz-es Intel Xeon E5-2660 v2</td>
    </tr>
    <tr>
        <td>Memória</td>
        <td>128 GB</td>
    </tr>
    <tr>
        <td>Network (Hálózat)</td>
        <td>Internetes adapter: 10 GB/s; intranetes interfész: 40 GB/s</td>
    </tr>
    </table>


> [!TIP]
> Nagyobb átviteli sebességet érhet el, ha több adatáthelyezési egységet (DMUs) használ, mint az alapértelmezett maximális DMUs, amely a felhőről a felhőbe másolási tevékenységek futtatásához 32. Például a 100 DMUs használatával az Azure Blobból másolhatja át az adatait Azure Data Lake Store **1.0 GB/s**-ra. A szolgáltatással és a támogatott forgatókönyvvel kapcsolatos részletekért tekintse meg a [Felhőbeli adatáthelyezési egységek](#cloud-data-movement-units) című szakaszt. További DMUs igényléséhez forduljon az [Azure támogatási szolgálatához](https://azure.microsoft.com/support/) .

## <a name="parallel-copy"></a>Párhuzamos másolás
A forrástól származó adatok beolvasása vagy az adatok másolása a célhelyre **párhuzamosan, a másolási tevékenység futtatásán belül**. Ez a szolgáltatás javítja a másolási művelet átviteli sebességét, és csökkenti az adatok áthelyezéséhez szükséges időt.

Ez a beállítás eltér a tevékenység definíciójának **Egyidejűség** tulajdonságával. A **párhuzamossági** tulajdonság határozza meg, hogy az **egyidejű másolási tevékenységek** hányan futnak a különböző tevékenységi időszakokból származó adatok (1 – 2, 2 – 3, 3 – 4) és így tovább. Ez a funkció akkor hasznos, ha egy korábbi terhelést hajt végre. A párhuzamos másolási képesség **egyetlen tevékenység futtatására** vonatkozik.

Nézzük meg a minta forgatókönyvet. A következő példában a múltban több szeletet kell feldolgozni. Data Factory a másolási tevékenység (tevékenység futtatása) példányát futtatja az egyes szeletekhez:

* Az első tevékenység ablakból származó adatszelet (1 – 2) = => tevékenység futtatása 1
* A második tevékenység ablakának adatszelete (2 – 3 AM) = => tevékenység 2. Futtatás
* A második tevékenység ablakának adatszelete (3 és 4 közötti érték) = => tevékenység 3. Futtatás

És így tovább.

Ebben a példában, ha a **párhuzamosság** értéke 2, a **tevékenységek 1** . és a **tevékenység futtatása 2** . adatmásolási művelet az adatátviteli teljesítmény javítása érdekében **egyszerre** két tevékenységből áll. Ha azonban több fájl van társítva a (z) 1. tevékenységhez, az adatátviteli szolgáltatás egyszerre másolja át a fájlokat a forrásról a cél egy fájlba.

### <a name="cloud-data-movement-units"></a>Felhőbeli adatáthelyezési egységek
A **Felhőbeli adatáthelyezési egység (DMU)** egy olyan mérték, amely a Data Factory egyetlen egységének a CPU-, memória-és hálózati erőforrás-lefoglalási kombinációját jelöli. A DMU a felhőből a felhőbe másolási műveletekre alkalmazható, nem hibrid másolatban.

**A másolási tevékenység futtatásához szükséges minimális Felhőbeli adatáthelyezési egységek kettő.** Ha nincs megadva, a következő táblázat felsorolja a különböző másolási helyzetekben használt alapértelmezett DMUs:

| Másolási forgatókönyv | A szolgáltatás által meghatározott alapértelmezett DMUs |
|:--- |:--- |
| Az Adatmásolás a fájl alapú tárolók között | A fájlok számától és méretétől függően 4 és 16 között kell lennie. |
| Minden más másolási forgatókönyv | 4 |

Az alapértelmezett érték felülbírálásához a **cloudDataMovementUnits** tulajdonság értékét az alábbiak szerint kell megadni. A **cloudDataMovementUnits** tulajdonság **megengedett értékei** : 2, 4, 8, 16, 32. A másolási művelet által a futási időben használt **Felhőbeli DMUs tényleges száma** a megadott értéktől függően egyenlő vagy kevesebb, mint az adatminta. Ha további információkat szeretne arról, hogy milyen teljesítménybeli nyereségre van szükség, ha több egységet állít be egy adott másolási forráshoz és fogadóhoz, tekintse meg a [teljesítmény-referenciát](#performance-reference).

```json
"activities":[
    {
        "name": "Sample copy activity",
        "description": "",
        "type": "Copy",
        "inputs": [{ "name": "InputDataset" }],
        "outputs": [{ "name": "OutputDataset" }],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "cloudDataMovementUnits": 32
        }
    }
]
```

> [!NOTE]
> Ha nagyobb átviteli sebességre van szüksége a Felhőbeli DMUs, forduljon az [Azure ügyfélszolgálatához](https://azure.microsoft.com/support/). A 8-as és újabb verzió beállítása jelenleg csak akkor működik, ha **több fájlt másol a blob Storage/Data Lake Store/Amazon S3/Cloud FTP/Cloud SFTP-ről a blob Storage/Data Lake Store/Azure SQL Database** helyre.
>

### <a name="parallelcopies"></a>parallelCopies
A **parallelCopies** tulajdonsággal jelezheti, hogy milyen párhuzamosságot kíván használni a másolási tevékenységhez. Ezt a tulajdonságot úgy tekintheti meg, mint a másolási tevékenységben lévő szálak maximális száma, amelyek beolvashatók a forrásból, vagy párhuzamosan írhatók a fogadó adattárakba.

Az egyes másolási tevékenységek futtatásához Data Factory meghatározza, hogy hány párhuzamos másolatot kell használni az adatok forrás adattárból és a célhely adattárba való másolásához. Az általa használt párhuzamos másolatok alapértelmezett száma a forrás és a fogadó használt típusától függ.

| Forrás és fogadó | A párhuzamos másolások alapértelmezett száma a szolgáltatás által meghatározva |
| --- | --- |
| Az Adatmásolás a fájl alapú tárolók között (blob Storage; Data Lake Store; Amazon S3; helyszíni fájlrendszer; helyszíni HDFS) |1 és 32 között. A fájlok méretétől és a Felhőbeli adatáthelyezési egységek (DMUs) méretétől függ, amelyek a két Felhőbeli adattárolók közötti adatmásolásra vagy a hibrid másoláshoz használt átjáró gép fizikai konfigurációjához (az adatok egy helyszíni adattárba való másolásához) használatosak. |
| Adatok másolása **bármely forrásoldali adattárból az Azure Table Storage-** ba |4 |
| Minden más forrás-és fogadó pár |1 |

Általában az alapértelmezett viselkedésnek a legjobb átviteli sebességet kell biztosítania. Az adattárakat üzemeltető gépek terhelésének szabályozására, illetve a másolási teljesítmény finomhangolására azonban dönthet úgy, hogy felülbírálja az alapértelmezett értéket, és megadja a **parallelCopies** tulajdonság értékét. Az értéknek 1 és 32 között kell lennie (a kettőt is beleértve). Futásidőben a legjobb teljesítmény érdekében a másolási tevékenység a beállított értéknél kisebb vagy azzal egyenlő értéket használ.

```json
"activities":[
    {
        "name": "Sample copy activity",
        "description": "",
        "type": "Copy",
        "inputs": [{ "name": "InputDataset" }],
        "outputs": [{ "name": "OutputDataset" }],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "parallelCopies": 8
        }
    }
]
```
Megjegyzés:

* Amikor fájlokat másol a fájl alapú tárolók között, a **parallelCopies** meghatározza a párhuzamosságot a fájl szintjén. Az egyetlen fájlon belüli adatdarabolás automatikusan és transzparens módon történik, és úgy van kialakítva, hogy egy adott forrás adattároló-típushoz a legmegfelelőbb méretet használja az adat párhuzamos és merőleges parallelCopies való betöltéséhez. Az adatátviteli szolgáltatás által a másolási művelethez használt párhuzamos másolatok tényleges száma nem haladja meg a fájlok számát. Ha a másolási viselkedés **mergeFile**, a másolási tevékenység nem tudja kihasználni a fájl szintű párhuzamosságot.
* Ha megad egy értéket a **parallelCopies** tulajdonsághoz, vegye figyelembe a terhelés növekedését a forrás-és fogadó adattárakban, valamint az átjárót, ha hibrid másolat. Ez különösen akkor fordul elő, ha ugyanazokkal a tevékenységekkel kapcsolatban több tevékenység vagy egyidejű Futtatás fut ugyanazon az adattáron. Ha azt észleli, hogy az adattár vagy az átjáró túlterhelt a terheléssel, csökkentse a **parallelCopies** értékét a terhelés enyhítése érdekében.
* Ha olyan áruházakból másol be olyan tárolókat, amelyek nem fájl-alapúak, a adatátviteli szolgáltatás figyelmen kívül hagyja a **parallelCopies** tulajdonságot. Ha meg van adva a párhuzamosság, akkor ez nem alkalmazható ebben az esetben.

> [!NOTE]
> Hibrid másolás esetén a **parallelCopies** funkció használatához a adatkezelés Gateway 1,11-es vagy újabb verzióját kell használnia.
>
>

A két tulajdonság jobb kihasználásához és az adatátviteli sebesség növeléséhez tekintse meg a minta használati eseteit. Nem kell konfigurálnia a **parallelCopies** az alapértelmezett működés kihasználása érdekében. Ha a configure és a **parallelCopies** túl kicsi, akkor előfordulhat, hogy a Felhőbeli DMUs nem teljesen teljes mértékben kihasználva.

### <a name="billing-impact"></a>Számlázási hatás
**Fontos** megjegyezni, hogy a másolási művelet teljes ideje alapján kell fizetnie. Ha egy olyan másolási feladatot használ, amely egy órát egy Felhőbeli egységgel végez, és most már 15 percet vesz igénybe négy felhő egységgel, a teljes számla majdnem ugyanaz marad. Tegyük fel például, hogy négy Felhőbeli egységet használ. Az első Felhőbeli egység 10 percet, a másodikat, 10 percet, a harmadikat, 5 percet, a negyedik pedig 5 percet, egy másolási tevékenység futtatásával tölti fel. A teljes másolási (adatáthelyezési) idő után számítunk fel díjat, amely 10 + 10 + 5 + 5 = 30 perc. A **parallelCopies** használata nem befolyásolja a számlázást.

## <a name="staged-copy"></a>Előkészített másolás
Amikor Adatmásolást végez egy forrás adattárból egy fogadó adattárba, a blob Storage-t átmeneti átmeneti tárolóként használhatja. Az előkészítés különösen a következő esetekben hasznos:

1. A **különböző adattárakból származó adatoknak az Azure szinapszis analyticsbe való betöltését a Base használatával szeretné megtekinteni**. Az Azure szinapszis Analytics a bázist nagy átviteli sebességű mechanizmusként használja nagy mennyiségű adat az Azure szinapszis Analyticsbe való betöltéséhez. A forrásadatok esetében azonban a blob Storage-ban kell szerepelnie, és meg kell felelnie a további feltételeknek. Ha a blob Storage-tól eltérő adattárból tölt be adatait, az Adatmásolást átmeneti átmeneti blob Storage használatával aktiválhatja. Ebben az esetben a Data Factory végrehajtja a szükséges adatátalakításokat annak érdekében, hogy az megfeleljen a bázisterület követelményeinek. Ezt követően a rendszer a Base használatával tölti be az adatok az Azure szinapszis Analytics szolgáltatásba. További részletekért lásd: az [adatok az Azure szinapszis analyticsbe való betöltésének használata](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-synapse-analytics) A használati eseteket bemutató bemutatóért lásd: [1 TB betöltése az Azure szinapszis Analytics szolgáltatásba 15 perc alatt, Azure Data Factory](data-factory-load-sql-data-warehouse.md).
2. **Időnként igénybe veheti a hibrid adatáthelyezést (azaz egy helyszíni adattár és egy felhőalapú adattár közötti másolást) lassú hálózati kapcsolaton keresztül**. A teljesítmény javítása érdekében a helyszínen tömörítheti az adatok mennyiségét, így kevesebb időt vesz igénybe, hogy az adatok áthelyezhetők legyenek a Felhőbeli átmeneti adattárba. Ezután kibonthatja az átmeneti tárolóban lévő adatok kibontását, mielőtt betölti azt a cél adattárba.
3. A **vállalati informatikai házirendek miatt nem kívánja megnyitni a 80-as és a 443-es porton kívüli portokat a tűzfalon**. Ha például egy helyszíni adattárból másol be egy Azure SQL Database fogadóba vagy egy Azure szinapszis Analytics-fogadóba, akkor a Windows tűzfal és a vállalati tűzfal esetében aktiválni kell a kimenő TCP-kommunikációt az 1433-as porton. Ebben a forgatókönyvben az átjáró előnyeit kihasználva először másolja át az adatmásolt blob Storage-alapú átmeneti példányba HTTP-n vagy HTTPS-en keresztül a 443-es porton. Ezután töltse be az adatait SQL Database vagy az Azure szinapszis Analytics szolgáltatásba a blob Storage-előkészítésből. Ebben a folyamatban nem kell engedélyeznie a 1433-es portot.

### <a name="how-staged-copy-works"></a>A szakaszos másolás működése
Az előkészítési funkció aktiválása után a rendszer először a forrás adattárból másolja át az adattárat az átmeneti adattárba (saját maga is). Ezt követően az adatok az előkészítési adattárból a fogadó adattárba lesznek másolva. A Data Factory automatikusan kezeli a kétlépcsős folyamatot. Az adatáthelyezés befejezése után a Data Factory az átmeneti tárolóból is törli az ideiglenes adatok mennyiségét.

A Felhőbeli másolási forgatókönyvben (a forrás-és a fogadó adattárolók a felhőben vannak), az átjáró nincs használatban. A Data Factory szolgáltatás végrehajtja a másolási műveleteket.

![Előkészített másolás: Felhőbeli forgatókönyv](media/data-factory-copy-activity-performance/staged-copy-cloud-scenario.png)

A hibrid másolási forgatókönyvben (a forrás a helyszínen, a fogadó pedig a felhőben van), az átjáró áthelyezi az adatait a forrás adattárból egy átmeneti adattárba. Data Factory a szolgáltatás áthelyezi az adatok az előkészítési adattárból a fogadó adattárba. A fordított folyamat a Felhőbeli adattárakból a helyszíni adattárba történő Adatmásolást is támogatja.

![Előkészített másolás: hibrid forgatókönyv](media/data-factory-copy-activity-performance/staged-copy-hybrid-scenario.png)

Ha átmeneti tároló használatával aktiválja az adatáthelyezést, megadhatja, hogy az adatok tömörítve legyenek-e, mielőtt az adatok átkerülnek a forrás adattárból egy ideiglenes vagy átmeneti adattárba, majd kibontja az adatok ideiglenes vagy átmeneti adattárból a fogadó adattárba való áthelyezését.

Jelenleg nem másolhat Adatmásolást két helyszíni adattár között egy átmeneti tároló használatával. Reméljük, hogy ez a lehetőség hamarosan elérhető lesz.

### <a name="configuration"></a>Konfiguráció
Konfigurálja a **enableStaging** beállítást a másolási tevékenységben annak megadásához, hogy a blob Storage-ban kívánja-e az adatelőkészítést, mielőtt betölti azt egy célhely-adattárba. Ha a **enableStaging** értéke TRUE (igaz) értékre van állítva, adja meg a következő táblázatban felsorolt további tulajdonságokat. Ha még nem rendelkezik ilyennel, létre kell hoznia egy Azure Storage vagy Storage közös hozzáférésű aláírással társított szolgáltatást az előkészítéshez.

| Tulajdonság | Leírás | Alapértelmezett érték | Kötelező |
| --- | --- | --- | --- |
| **enableStaging** |Itt adhatja meg, hogy egy átmeneti átmeneti tárolón keresztül kívánja-e az Adatmásolást. |Hamis |No |
| **linkedServiceName** |Adja meg egy [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) vagy [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) társított szolgáltatás nevét, amely az átmeneti előkészítési tárolóként használt tároló példányára hivatkozik. <br/><br/> Nem használhat megosztott hozzáférési aláírással rendelkező tárolót az adatok Azure szinapszis-elemzésbe való betöltéséhez a Base használatával. Ezt minden más esetben használhatja. |N/A |Igen, ha a **enableStaging** értéke TRUE (igaz) |
| **elérési útja** |Itt adhatja meg a blob Storage azon elérési útját, amelyben az előkészített adatértékeket tárolni szeretné. Ha nem ad meg elérési utat, a szolgáltatás létrehoz egy tárolót az ideiglenes adattároláshoz. <br/><br/> Elérési utat csak akkor kell megadni, ha megosztott hozzáférési aláírással rendelkező tárolót használ, vagy ha ideiglenes adatmennyiségre van szüksége egy adott helyen. |N/A |No |
| **enableCompression** |Megadja, hogy a rendszer a célhelyre való másolás előtt tömöríti-e az adatfájlokat. Ez a beállítás csökkenti az átvitel alatt álló adatmennyiséget. |Hamis |No |

Az alábbi példa a másolási tevékenység definícióját tartalmazza az előző táblázatban ismertetett tulajdonságokkal:

```json
"activities":[
{
    "name": "Sample copy activity",
    "type": "Copy",
    "inputs": [{ "name": "OnpremisesSQLServerInput" }],
    "outputs": [{ "name": "AzureSQLDBOutput" }],
    "typeProperties": {
        "source": {
            "type": "SqlSource",
        },
        "sink": {
            "type": "SqlSink"
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyStagingBlob",
            "path": "stagingcontainer/path",
            "enableCompression": true
        }
    }
}
]
```

### <a name="billing-impact"></a>Számlázási hatás
A díj két lépésből áll: a másolási időtartam és a másolás típusa alapján.

* Ha a Felhőbeli másolás során (az adatok egy Felhőbeli adattárból egy másik Felhőbeli adattárba való másolásával) való átmeneti tárolást használ, akkor az [1. lépés és a 2. lépés] x [Felhőbeli másolási egység ára] [a másolás időtartamának összegét kell megfizetnie.
* Ha hibrid másolás során (az adatok egy helyszíni adattárból egy Felhőbeli adattárba történő másolásával) végez előkészítést, akkor a [hibrid másolási időtartam] x [hibrid másolási egység ára] + [Felhőbeli másolás időtartama] x [Felhőbeli másolási egység ára].

## <a name="performance-tuning-steps"></a>Teljesítmény-finomhangolási lépések
Javasoljuk, hogy hajtsa végre ezeket a lépéseket a Data Factory szolgáltatás teljesítményének a másolási tevékenységgel való finomhangolásához:

1. **Hozzon létre egy alaptervet**. A fejlesztési fázisban a másolási tevékenység használatával tesztelheti a folyamatot egy reprezentatív adatmintán. A Data Factory [szeletelő modell](data-factory-scheduling-and-execution.md) használatával korlátozhatja a munkához használt adatmennyiséget.

   A **figyelési és felügyeleti alkalmazás** használatával Gyűjtse össze a végrehajtási időt és a teljesítmény jellemzőit. A Data Factory kezdőlapján válassza a **figyelés & kezelés** lehetőséget. A fanézetben válassza ki a **kimeneti adatkészletet**. A **tevékenység Windows** listájában válassza a másolási tevékenység Futtatás lehetőséget. A **tevékenység Windows** a másolási tevékenység időtartamát és a másolt adatméretet sorolja fel. Az átviteli sebesség megjelenik a **tevékenység ablak-kezelőben**. További információ az alkalmazásról: [Azure Data Factory folyamatok figyelése és felügyelete a figyelési és felügyeleti alkalmazás használatával](data-factory-monitor-manage-app.md).

   ![Tevékenységfuttatás részletei](./media/data-factory-copy-activity-performance/mmapp-activity-run-details.png)

   A cikk későbbi részében összehasonlíthatja a forgatókönyv teljesítményét és konfigurációját a tevékenységek [teljesítmény-referenciájának](#performance-reference) a tesztek alapján történő másolásához.
2. **A teljesítmény diagnosztizálása és optimalizálása**. Ha a megfigyelt teljesítmény nem felel meg az elvárásainak, meg kell határoznia a teljesítmény szűk keresztmetszeteit. Ezután optimalizálja a teljesítményt, hogy eltávolítsa vagy csökkentse a szűk keresztmetszetek hatását. A teljesítmény-diagnosztika teljes leírása meghaladja a jelen cikk hatókörét, de íme néhány gyakori szempont:

   * Teljesítménnyel kapcsolatos szolgáltatások:
     * [Párhuzamos másolás](#parallel-copy)
     * [Felhőbeli adatáthelyezési egységek](#cloud-data-movement-units)
     * [Előkészített másolás](#staged-copy)
     * [adatkezelés átjáró méretezhetősége](data-factory-data-management-gateway-high-availability-scalability.md)
   * [Adatkezelési átjáró](#considerations-for-data-management-gateway)
   * [Forrás](#considerations-for-the-source)
   * [Sink (Fogadó)](#considerations-for-the-sink)
   * [Szerializálás és deszerializálás](#considerations-for-serialization-and-deserialization)
   * [Tömörítés](#considerations-for-compression)
   * [Oszlopleképezés](#considerations-for-column-mapping)
   * [További szempontok](#other-considerations)
3. **Bontsa ki a konfigurációt a teljes adathalmazra**. Ha elégedett a végrehajtás eredményeivel és teljesítményével, kiterjesztheti a definíciót és a folyamat aktív időszakát, hogy az a teljes adathalmazra vonatkozzon.

## <a name="considerations-for-data-management-gateway"></a>Az adatkezelés átjáróval kapcsolatos megfontolások
**Átjáró beállítása**: javasoljuk, hogy egy dedikált gépet használjon adatkezelés átjáró üzemeltetéséhez. Lásd: [adatkezelés átjáró használatának szempontjai](data-factory-data-management-gateway.md#considerations-for-using-gateway).

**Átjáró figyelése és vertikális felskálázása**: egy vagy több átjárót tartalmazó logikai átjáró egyszerre több másolási tevékenységet is képes futni. Megtekintheti az erőforrás-felhasználás (CPU, memória, hálózat (in/out), stb.) közel valós idejű pillanatképét, valamint a Azure Portalon futó egyidejű feladatok számát, az [átjáró figyelése a portálon](data-factory-data-management-gateway.md#monitor-gateway-in-the-portal)című témakört. Ha nagy mennyiségű párhuzamos másolási tevékenységre van szüksége a hibrid adatáthelyezéshez, vagy nagyméretű adatmásolási művelettel vagy nagy mennyiségű adattal, vegye fontolóra az [átjáró vertikális felskálázását vagy felskálázását](data-factory-data-management-gateway-high-availability-scalability.md#scale-considerations) , hogy jobban kihasználja az erőforrást, vagy több erőforrást lehessen kiépíteni a másolásra.

## <a name="considerations-for-the-source"></a>A forrás szempontjai
### <a name="general"></a>Általános kérdések
Ügyeljen arra, hogy az alapul szolgáló adattár ne legyen túlterhelve más, vagy azon kívül futó munkaterhelések között.

A Microsoft adattárak esetében lásd: az adattárakra jellemző [témakörök figyelése és finomhangolása](#performance-reference) , valamint az adattárolási teljesítmény jellemzőinek megértése, a válaszidő csökkentése és az átviteli sebesség maximalizálása.

Ha a blob Storage-ból másolja az Azure szinapszis Analytics-be, érdemes **lehet a teljesítmény növelésére használni** További részleteket a következő témakörben talál: [adatok betöltése az Azure szinapszis analyticsbe](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-synapse-analytics) . A használati eseteket bemutató bemutatóért lásd: [1 TB betöltése az Azure szinapszis Analytics szolgáltatásba 15 perc alatt, Azure Data Factory](data-factory-load-sql-data-warehouse.md).

### <a name="file-based-data-stores"></a>Fájl alapú adattárak
*(Blob Storage, Data Lake Store, Amazon S3, helyszíni fájlrendszerek és helyszíni HDFS tartalmazza)*

* Fájlok **átlagos mérete és a fájlok száma**: a másolási tevékenység egyszerre egy fájlt továbbít. Azonos mennyiségű adat áthelyezéséhez a teljes átviteli sebesség kisebb, ha az adatok több kis fájlból állnak, és nem csupán néhány nagyméretű fájlt, mert az egyes fájlok rendszerindítási fázisa miatt. Ezért, ha lehetséges, a kis méretű fájlokat a nagyobb adatátviteli sebesség érdekében egyesítheti nagyobb fájlokba.
* **Fájlformátum és tömörítés**: a teljesítmény növelésének további módjaiért tekintse meg a [szerializálási és deszerializálási szempontokat](#considerations-for-serialization-and-deserialization) , valamint [a tömörítési szempontokat](#considerations-for-compression) ismertető szakaszt.
* A helyszíni **fájlrendszer** esetében, amelyben **adatkezelés átjáró** szükséges, tekintse meg a [adatkezelés átjáróval kapcsolatos szempontok](#considerations-for-data-management-gateway) című szakaszt.

### <a name="relational-data-stores"></a>Rokon adattárak
*(Tartalmazza a SQL Database; Az Azure szinapszis Analytics; Amazon vöröseltolódás; SQL Server adatbázisok; és Oracle-, MySQL-, DB2-, Teradata-, Sybase-és PostgreSQL-adatbázisok stb.)*

* **Adatminta**: a táblázat sémája hatással van a másolási teljesítményre. A nagyméretű sorok mérete jobb teljesítményt nyújt, mint a kis sorok mérete, így ugyanazon mennyiségű adat másolása is megadható. Ennek az az oka, hogy az adatbázis hatékonyabban tudja lekérni a kevesebb sort tartalmazó kötegeket.
* **Lekérdezési vagy tárolt eljárás**: optimalizálja a másolási tevékenység forrásaként megadott lekérdezés vagy tárolt eljárás logikáját, hogy az adatgyűjtést hatékonyabban lehessen beolvasni.
* Az **adatkezelés Gateway** használatát igénylő helyszíni, például a SQL Server és az **Oracle számára a** következő témakörben talál tudnivalókat: adatkezelés Gateway című szakasz.

## <a name="considerations-for-the-sink"></a>A fogadó szempontjai
### <a name="general"></a>Általános kérdések
Ügyeljen arra, hogy az alapul szolgáló adattár ne legyen túlterhelve más, vagy azon kívül futó munkaterhelések között.

A Microsoft-adattárak esetében tekintse meg az adattárakra jellemző [témakörök figyelését és finomhangolását](#performance-reference) . Ezek a témakörök segítenek megérteni az adattár teljesítményének jellemzőit, és azt, hogy miként lehet csökkenteni a válaszidőt és maximalizálni az átviteli sebességet.

Ha a **blob Storage** -ból az **Azure szinapszis Analytics** szolgáltatásba másol adatait, a teljesítmény növelése érdekében érdemes lehet a **Base** használata. További részleteket a következő témakörben talál: [adatok betöltése az Azure szinapszis analyticsbe](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-synapse-analytics) . A használati eseteket bemutató bemutatóért lásd: [1 TB betöltése az Azure szinapszis Analytics szolgáltatásba 15 perc alatt, Azure Data Factory](data-factory-load-sql-data-warehouse.md).

### <a name="file-based-data-stores"></a>Fájl alapú adattárak
*(Blob Storage, Data Lake Store, Amazon S3, helyszíni fájlrendszerek és helyszíni HDFS tartalmazza)*

* **Másolási viselkedés**: Ha más fájl-alapú adattárból másol adatokból, a másolási tevékenység három lehetőséggel rendelkezik a **copyBehavior** tulajdonságon keresztül. Megőrzi a hierarchiát, lelapul vagy összevonja a fájlokat. Vagy a hierarchia megőrzése vagy összeolvasztása nem tartalmaz teljesítménnyel kapcsolatos terhelést, de a fájlok egyesítése a teljesítmény növelését okozza.
* **Fájlformátum és tömörítés**: a teljesítmény javítása érdekében tekintse meg a [szerializálási és deszerializálási szempontokat](#considerations-for-serialization-and-deserialization) , valamint a [tömörítési szempontokat](#considerations-for-compression) ismertető szakaszt.
* **Blob Storage**: a blob Storage jelenleg csak blokk blobokat támogat az optimalizált adatátvitelhez és átviteli sebességhez.
* Adatkezelés **-** **átjáró** használatát igénylő helyszíni fájlrendszerek esetében tekintse meg a [adatkezelés átjáróval kapcsolatos szempontokat](#considerations-for-data-management-gateway) ismertető szakaszt.

### <a name="relational-data-stores"></a>Rokon adattárak
*(Tartalmazza a SQL Database, az Azure szinapszis Analytics, a SQL Server-adatbázisok és az Oracle-adatbázisok)*

* **Másolási viselkedés**: attól függően, hogy milyen tulajdonságokkal rendelkezik a **sqlSink**, a másolási tevékenység különböző módokon írja be az adatot a céladatbázisbe.
  * Alapértelmezés szerint az adatátviteli szolgáltatás a tömeges másolási API-t használja az adat hozzáfűzési módban való beszúrásához, amely a legjobb teljesítményt biztosítja.
  * Ha tárolt eljárást állít be a fogadóban, az adatbázis az adatsorokat egyszerre egy sorban alkalmazza a tömeges betöltés helyett. A teljesítmény jelentősen csökken. Ha az adathalmaz mérete nagy, akkor érdemes lehet a **sqlWriterCleanupScript** tulajdonság használatára váltani.
  * Ha a **sqlWriterCleanupScript** tulajdonságot minden egyes másolási tevékenységhez beállítja, a szolgáltatás elindítja a parancsfájlt, majd a tömeges másolási API használatával szúrja be az adatmennyiséget. Ha például felül szeretné írni a teljes táblázatot a legfrissebb adatokkal, megadhat egy parancsfájlt, amely először törli az összes rekordot, mielőtt tömegesen betölti az új adatokat a forrásból.
* **Az adatminta és a köteg mérete**:
  * A táblázat sémája hatással van a másolási teljesítményre. Az azonos mennyiségű adat másolásához a nagyméretű sorok mérete jobb teljesítményt nyújt, mint a kisebb sorok mérete, mert az adatbázis hatékonyabban tudja véglegesíteni az adatkötegeket.
  * A másolási tevékenység kötegek sorozatában szúrja be az adathalmazokat. A Batch-sorok számát a **writeBatchSize** tulajdonság használatával állíthatja be. Ha az adatok kis sorokkal rendelkeznek, magasabb értékre állíthatja a **writeBatchSize** tulajdonságot, hogy kihasználhassa az alacsonyabb kötegek terhelését és a magasabb átviteli sebességet. Ha az adatsorok mérete nagy, ügyeljen a **writeBatchSize** növelésére. A magas érték azt eredményezheti, hogy a másolási hiba oka az adatbázis túlterhelése.
* A helyszíni, SQL Server és Oracle **-alapú** , **adatkezelés átjáró** használatát igénylő adatbázisok esetében tekintse meg az [adatkezelés-átjáró szempontjai](#considerations-for-data-management-gateway) című szakaszt.

### <a name="nosql-stores"></a>NoSQL-tárolók
*(Tartalmazza a Table Storage és a Azure Cosmos DB)*

* A **Table Storage** esetében:
  * **Partíció**: az adat az összekapcsolt partíciók közötti írásával jelentősen rontja a teljesítményt. Rendezze a forrásadatokat a partíciós kulcs szerint, hogy a rendszer hatékonyan beszúrja az adatait egy partícióba egy másik után, vagy módosítsa úgy a logikát, hogy az adatait egyetlen partícióba írja.
* **Azure Cosmos db** esetén:
  * **Köteg mérete**: a **writeBatchSize** tulajdonság állítja be a Azure Cosmos db szolgáltatásnak küldött párhuzamos kérelmek számát a dokumentumok létrehozásához. Nagyobb teljesítmény várható a **writeBatchSize** növelésekor, mert a rendszer több párhuzamos kérelmet kap Azure Cosmos db. Ha azonban a Azure Cosmos DB írásakor a szabályozást szeretné megtekinteni (a hibaüzenet "kérések aránya nagy"). A különböző tényezők a szabályozáshoz vezethetnek, beleértve a dokumentumok méretét, a dokumentumok használati feltételeit és a cél gyűjtemény indexelési házirendjét. A nagyobb másolási sebesség eléréséhez érdemes lehet egy jobb gyűjteményt használni, például: S3.

## <a name="considerations-for-serialization-and-deserialization"></a>A szerializálás és a deszerializálás szempontjai
A szerializálás és a deszerializálás akkor fordulhat elő, ha a bemeneti adatkészlet vagy a kimeneti adatkészlet egy fájl. A másolási tevékenység által támogatott fájlformátumok részleteit a [támogatott fájl-és Tömörítési formátumok](data-factory-supported-file-and-compression-formats.md) című részben tekintheti meg.

**Másolási viselkedés**:

* Fájlok másolása a fájl alapú adattárak között:
  * Ha a bemeneti és kimeneti adatkészletek esetében ugyanaz vagy nincs fájlformátum-beállítás, az adatáthelyezési szolgáltatás a szerializálás vagy a deszerializálás nélkül hajtja végre a bináris másolást. A forgatókönyvhöz képest magasabb átviteli sebesség jelenik meg, amelyben a forrás és a fogadó fájlformátum beállításai eltérnek egymástól.
  * Ha a bemeneti és kimeneti adatkészletek egyaránt szöveges formátumúak, és csak a kódolás típusa eltérő, az adatáthelyezési szolgáltatás csak a kódolást végzi. Nem végez szerializálást és deszerializálást, ami némi teljesítménybeli terhelést okoz a bináris másoláshoz képest.
  * Ha a bemeneti és kimeneti adatkészletek esetében különböző fájlformátumok vagy különböző konfigurációk vannak, például elválasztó karakterek, az adatátviteli szolgáltatás deszerializálja a forrás adatokat adatfolyamként, átalakítja, majd szerializálja a megadott kimeneti formátumba. Ez a művelet sokkal jelentősebb teljesítményt eredményez, mint a többi forgatókönyvhöz képest.
* Ha olyan adattárból másol fájlokat, amely nem fájl alapú (például egy fájl alapú tárolóból egy kapcsolódó tárolóba), akkor a szerializálás vagy a deszerializálás lépés szükséges. Ez a lépés jelentős teljesítménybeli terhelést eredményez.

**Fájlformátum**: a választott fájlformátum hatással lehet a másolási teljesítményre. Például a Avro egy kompakt bináris formátum, amely adatokat tárol a metaadatokban. Széleskörű támogatást nyújt a Hadoop ökoszisztéma feldolgozásához és lekérdezéséhez. A Avro azonban drágább a szerializálás és a deszerializálás során, ami alacsonyabb másolási sebességet eredményez a szöveges formátumhoz képest. Válassza ki a fájlformátumot a teljes feldolgozási folyamat során. Megkezdheti az adatok tárolási formáját, a forrás adattárakat, illetve a külső rendszerekből való kinyerést. a tárolás, az analitikus feldolgozás és a lekérdezés legjobb formátuma; és milyen formátumban kell exportálni az adatközpontokat a jelentéskészítési és vizualizációs eszközök számára. Előfordulhat, hogy az olvasási és írási teljesítményhez az optimálisnál gyengébb fájlformátum jó választás, ha figyelembe vesszük a teljes analitikai folyamatot.

## <a name="considerations-for-compression"></a>A tömörítés szempontjai
Ha a bemeneti vagy kimeneti adatkészlete egy fájl, a másolási tevékenységgel tömörítést vagy kibontást állíthat be, amikor adatokat ír a célhelyre. Ha a tömörítést választja, a bemeneti/kimeneti (I/O) és a CPU között kompromisszumot kell végeznie. A számítási erőforrásokban felmerülő adatterhek további tömörítése. A cserében azonban csökkenti a hálózati I/O-t és a tárolást. Az adatoktól függően a teljes másolási átviteli sebesség is megjelenhet.

**Kodek**: a másolási tevékenység támogatja a gzip, a bzip2 és a deflate tömörítési típusokat. Az Azure HDInsight mindhárom típust felhasználhatja feldolgozásra. Minden tömörítési kodeknek van előnye. Például a bzip2 a legkisebb másolási sebességgel rendelkezik, de a legjobb kaptár-lekérdezési teljesítményt a bzip2-vel, mert a feldolgozásra is feloszthatja. A gzip a leginkább kiegyensúlyozott megoldás, és a leggyakrabban használatos. Válassza ki azt a kodeket, amely a legjobban megfelel a végpontok közötti forgatókönyvnek.

**Szint**: két lehetőség közül választhat az egyes tömörítési kodekekhez: a leggyorsabb tömörített és optimálisan tömörített. A leggyorsabb tömörített beállítás a lehető leggyorsabban tömöríti az adatgyűjtést, még akkor is, ha az eredményül kapott fájl nem tömöríthető optimálisan. Az optimálisan tömörített beállítás több időt tölt a tömörítéssel, és minimális adatmennyiséget eredményez. Mindkét lehetőség tesztelésével ellenőrizheti, hogy az adott esetben jobb általános teljesítményt nyújt-e.

**Megfontolás**: Ha nagy mennyiségű adatmennyiséget szeretne másolni egy helyszíni tároló és a felhő között, fontolja meg az ideiglenes blob Storage használatát tömörítéssel. Az átmeneti tárterület használata akkor hasznos, ha a vállalati hálózat és az Azure-szolgáltatások sávszélessége a korlátozási tényező, és a bemeneti adatkészletet és a kimeneti adatkészletet egyaránt tömörítetlen formában szeretné megadni. Pontosabban egyetlen másolási tevékenységet is megszakíthat két másolási tevékenységre. Az első másolási tevékenység a forrásról egy ideiglenes vagy átmeneti blobba másol tömörített formában. A második másolási tevékenység átmásolja a tömörített adatot az előkészítésből, majd kibontja, miközben a fogadóba ír.

## <a name="considerations-for-column-mapping"></a>Az oszlop-hozzárendelés szempontjai
A másolási tevékenység **columnMappings** tulajdonságát beállíthatja úgy, hogy a bemeneti oszlopok összes vagy egy részhalmazát hozzárendelje a kimeneti oszlopokhoz. Miután az adatátviteli szolgáltatás beolvassa az adatokat a forrásból, az adatoknak a fogadóba való írása előtt el kell végeznie az oszlop-hozzárendelést az adatokon. Ez az extra feldolgozás csökkenti a másolási sebességet.

Ha a forrás adattára lekérdezhető, például ha egy olyan SQL Database vagy SQL Server, vagy egy NoSQL-tároló (például Table Storage vagy Azure Cosmos DB), akkor az oszlopok leképezése helyett az oszlopok szűrését és a logika átrendezését kell megfontolnia a **lekérdezési** tulajdonsághoz. Így a kivetítés akkor történik meg, amikor az adatátviteli szolgáltatás beolvassa az adatokat a forrás adattárból, ahol sokkal hatékonyabbá válik.

## <a name="other-considerations"></a>További szempontok
Ha a másolni kívánt adatméret nagy, beállíthatja az üzleti logikáját, hogy tovább particionálja az adatmennyiséget a Data Factory szeletelő mechanizmusának használatával. Ezután ütemezze a másolási tevékenységet úgy, hogy gyakrabban fusson az egyes másolási tevékenységek adatméretének csökkentése érdekében.

Legyen óvatos az adathalmazok számával és a másolási tevékenységekkel, amelyeknek Data Factoryt kell megadniuk az ugyanahhoz az adattárhoz való csatlakozáshoz. Számos egyidejű másolási feladat szabályozhatja az adattárakat, és csökkentheti a teljesítményt, átmásolhatja a feladat belső újrapróbálkozásait, és bizonyos esetekben a végrehajtási hibákat is.

## <a name="sample-scenario-copy-from-a-sql-server-database-to-blob-storage"></a>Példa: másolás egy SQL Server adatbázisból a blob Storage-ba
**Forgatókönyv**: egy folyamat úgy van felépítve, hogy egy SQL Server-adatbázisból másolja át a blob Storage-ba az adatok CSV formátumban történő másolását. A másolási feladatok gyorsabb elvégzéséhez a CSV-fájlokat a bzip2 formátumba kell tömöríteni.

**Tesztelés és elemzés**: a másolási tevékenység átviteli sebessége kevesebb, mint 2 Mbps, ami sokkal lassabb, mint a teljesítménnyel kapcsolatos teljesítményteszt.

**Teljesítmény elemzése és Finomhangolás**: a teljesítménnyel kapcsolatos probléma megoldásához tekintse át az adatfeldolgozás és-áthelyezés módját.

1. **Adatolvasás**: az átjáró megnyit egy kapcsolódást a SQL Serverhoz, és elküldi a lekérdezést. SQL Server válaszol, ha az adatfolyamot az átjárón keresztül az intraneten keresztül küldi el.
2. **Adatok szerializálása és tömörítése**: az ÁTJÁRÓ a CSV formátumba szerializálja az adatfolyamot, és tömöríti az adatok egy bzip2-adatfolyamba.
3. **Adatok írása**: az átjáró feltölti a bzip2 streamet a blob Storage-ba az interneten keresztül.

Amint láthatja, az adatfeldolgozás folyamatban van, és folyamatos átvitel alatt áll: SQL Server > LAN > átjáró > WAN > blob Storage. **A teljes teljesítmény a folyamaton belüli minimális átviteli sebességtől**.

![Adatfolyam](./media/data-factory-copy-activity-performance/case-study-pic-1.png)

Az alábbi tényezők közül egy vagy több a teljesítmény szűk keresztmetszetét okozhatja:

* **Forrás**: SQL Server maga a nagy terhelés miatt alacsony átviteli sebességű.
* **Adatkezelés átjáró**:
  * **LAN**: az átjáró a SQL Server számítógéptől távol található, és alacsony sávszélességű kapcsolattal rendelkezik.
  * **Átjáró**: az átjáró elérte a terhelési korlátozásokat a következő műveletek elvégzéséhez:
    * **Szerializálás**: az adatfolyam CSV-formátumba való szerializálásának lassú átviteli sebessége van.
    * **Tömörítés**: lassú tömörítési kodeket (például bzip2, 2,8 Mbps, Core i7) választott.
  * **WAN**: a vállalati hálózat és az Azure-szolgáltatások közötti sávszélesség alacsony (például T1 = 1 544 kbps; T2 = 6 312 kbps).
* Fogadó **: a** blob Storage alacsony átviteli sebességgel rendelkezik. (Ez a forgatókönyv nem valószínű, mert az SLA-ja legalább 60 MBps-t garantál.)

Ebben az esetben a bzip2 adattömörítés a teljes folyamat lelassulását okozhatja. A gzip tömörítési kodekre való áttérés megkönnyíti ezt a szűk keresztmetszetet.

## <a name="sample-scenarios-use-parallel-copy"></a>Példa forgatókönyvek: párhuzamos másolás használata
**I. forgatókönyv:** Másolja az 1 000 1-MB fájlokat a helyszíni fájlrendszerből a blob Storage-ba.

**Elemzés és teljesítmény finombeállítása**: Ha egy négymagos gépre telepítette az átjárót, a Data Factory 16 párhuzamos másolatot használ a fájlok a fájlrendszerből a blob Storage-ba való párhuzamos áthelyezéséhez. Ennek a párhuzamos végrehajtásnak nagy átviteli sebességet kell eredményeznie. Explicit módon megadhatja a párhuzamos másolatok darabszámát is. Sok kis fájl másolásakor a párhuzamos másolatok jelentősen segítik az átviteli sebességet az erőforrások hatékonyabb használatával.

![1\. példa](./media/data-factory-copy-activity-performance/scenario-1.png)

**II. forgatókönyv**: másolja a blob Storage-ból 500 MB-nyi 20 blobot a Data Lake Store analitikaba, majd hangolja a teljesítményt.

**Elemzés és teljesítmény finomhangolása**: ebben a forgatókönyvben a Data Factory a blob Storage-ból másolt adatok átmásolása egy egypéldányos (**parallelCopies** -érték 1) és egy Felhőbeli adatáthelyezési egység használatával Data Lake Store. A megfigyelt átviteli sebesség a [teljesítmény-referenciák szakaszban](#performance-reference)leírtak szerint fog megjelenni.

![2\. példa](./media/data-factory-copy-activity-performance/scenario-2.png)

**III. forgatókönyv**: az egyes fájlok mérete nagyobb, mint több tucat MBS, és a teljes kötet nagy.

**Elemzés és teljesítmény**: a **parallelCopies** növelése nem eredményez jobb másolási teljesítményt az egyfelhős DMU erőforrás-korlátai miatt. Ehelyett több Felhőbeli DMUs kell megadnia ahhoz, hogy további erőforrások legyenek az adatok áthelyezéséhez. Ne határozzon meg értéket a **parallelCopies** tulajdonsághoz. Data Factory kezeli a párhuzamosságot. Ebben az esetben, ha a **cloudDataMovementUnits** 4 értékre állítja, az átviteli sebesség körülbelül négy alkalommal történik.

![3\. példa](./media/data-factory-copy-activity-performance/scenario-3.png)

## <a name="reference"></a>Referencia
Az alábbiakban a támogatott adattárakkal kapcsolatos Teljesítményfigyelés és hangolási referenciák találhatók:

* Azure Blob Storage: a blob Storage-hoz szükséges [méretezhetőségi és teljesítményi célok](../../storage/blobs/scalability-targets.md) , valamint a blob Storage-hoz kapcsolódó [teljesítmény-és méretezhetőségi ellenőrzőlista](../../storage/blobs/storage-performance-checklist.md).
* Azure Table Storage: a táblázatos tároláshoz [szükséges méretezhetőségi](../../storage/tables/scalability-targets.md) és teljesítményi célok, [valamint a Table Storage teljesítményére és méretezhetőségére vonatkozó ellenőrzőlista](../../storage/tables/storage-performance-checklist.md).
* Azure SQL Database: nyomon követheti [a teljesítményt](../../azure-sql/database/monitor-tune-overview.md) , és ellenőrizheti az adatbázis-tranzakciós egység (DTU) százalékos arányát.
* Azure szinapszis Analytics: a rendszer az adatraktár-egységekben (DWU) méri a képességét. Lásd: [a számítási teljesítmény kezelése az Azure szinapszis Analyticsben (áttekintés)](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)
* Azure Cosmos DB: [a Azure Cosmos db teljesítmény szintjei](../../cosmos-db/performance-levels.md)
* Helyszíni SQL Server: [teljesítmény figyelése és finomhangolása](/sql/relational-databases/performance/monitor-and-tune-for-performance)
* Helyszíni fájlkiszolgáló: a [fájlkiszolgálók teljesítményének finomhangolása](/previous-versions//dn567661(v=vs.85))