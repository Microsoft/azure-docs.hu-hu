---
title: Másolási tevékenység teljesítményének optimalizálási funkciói
description: Ismerkedjen meg a főbb funkciókkal, amelyek segítenek a másolási tevékenység teljesítményének optimalizálásában Azure Data Factory 。
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/24/2020
ms.openlocfilehash: ecb4550b218b069273cba2e3d70a9510c1cc74ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100387801"
---
# <a name="copy-activity-performance-optimization-features"></a>Másolási tevékenység teljesítményének optimalizálási funkciói

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk a másolási tevékenység teljesítmény-optimalizálási funkcióit ismerteti, amelyeket Azure Data Factory használhat.

## <a name="data-integration-units"></a>Adatintegrációs egységek

Az adatintegrációs egység olyan mérték, amely a Azure Data Factory egyetlen egységének a CPU-, memória-és hálózati erőforrás-lefoglalási kombinációját jelöli. Az adatintegrációs egység csak az [Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime)szolgáltatásra vonatkozik, a saját üzemeltetésű [integrációs](concepts-integration-runtime.md#self-hosted-integration-runtime)modul nem.

A másolási tevékenység futtatásának engedélyezése engedélyezett DIUs **2 és 256 között** van. Ha nincs megadva, vagy ha az "Auto" lehetőséget választja a felhasználói felületen, Data Factory dinamikusan alkalmazza az optimális DIU beállítást a forrás-fogadó pár és az adatminta alapján. A következő táblázat felsorolja a támogatott DIU-tartományokat és az alapértelmezett viselkedést a különböző másolási forgatókönyvekben:

| Másolási forgatókönyv | Támogatott DIU-tartomány | A szolgáltatás által meghatározott alapértelmezett DIUs |
|:--- |:--- |---- |
| A file Stores között |- **Másolás egyetlen fájlba vagy fájlból**: 2-4 <br>- **Másolás a és a rendszerből több fájlba**: 2-256 a fájlok számától és méretétől függően <br><br>Ha például egy 4 nagyméretű fájllal rendelkező mappából másol adatokból, és úgy dönt, hogy megőrzi a hierarchiát, a maximális érvényes DIU 16; Ha a fájl egyesítését választja, a maximális érvényes DIU 4. |A fájlok számától és méretétől függően 4 és 32 között |
| A file Store-ból a nem file Store-ba |- **Másolás egyetlen fájlból**: 2-4 <br/>- **Másolás több fájlból**: 2-256 a fájlok számától és méretétől függően <br/><br/>Ha például egy 4 nagy méretű fájllal rendelkező mappából másol adatokból, a maximális érvényes DIU 16. |- **Másolás Azure SQL Database vagy Azure Cosmos db**: a fogadó szintjétől (DTU/RUs) és a forrásfájl mintától függően 4 és 16 között<br>- **Másolás az Azure szinapszis analyticsbe** a Base vagy a copy utasítás használatával: 2<br>– Egyéb forgatókönyv: 4 |
| Nem a file Store-ból a file Store-ba |- **Másolás a particionálási lehetőséggel rendelkező adattárakból** (beleértve az [Azure SQL Database](connector-azure-sql-database.md#azure-sql-database-as-the-source), az [Azure SQL felügyelt példányát](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), az [Azure szinapszis Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), az [Oracle](connector-oracle.md#oracle-as-source), a [Netezza](connector-netezza.md#netezza-as-source), az [SQL Server](connector-sql-server.md#sql-server-as-a-source)és a [Teradata](connector-teradata.md#teradata-as-source)): 2-256, amikor egy mappába ír, és 2-4, ha egyetlen fájlba ír. Megjegyzés: a forrásoldali adatpartíciók akár 4 DIUs is használhatnak.<br>- **Egyéb forgatókönyvek**: 2-4 |- **Másolás Rest vagy http**: 1<br/>- **Másolás az Amazon vöröseltolódásból a** kitöltéssel: 2<br>- **Egyéb forgatókönyv**: 4 |
| Nem fájlos tárolók között |- **Másolás a particionálási lehetőséggel rendelkező adattárakból** (beleértve az [Azure SQL Database](connector-azure-sql-database.md#azure-sql-database-as-the-source), az [Azure SQL felügyelt példányát](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), az [Azure szinapszis Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), az [Oracle](connector-oracle.md#oracle-as-source), a [Netezza](connector-netezza.md#netezza-as-source), az [SQL Server](connector-sql-server.md#sql-server-as-a-source)és a [Teradata](connector-teradata.md#teradata-as-source)): 2-256, amikor egy mappába ír, és 2-4, ha egyetlen fájlba ír. Megjegyzés: a forrásoldali adatpartíciók akár 4 DIUs is használhatnak.<br/>- **Egyéb forgatókönyvek**: 2-4 |- **Másolás Rest vagy http**: 1<br>- **Egyéb forgatókönyv**: 4 |

A másolási tevékenység figyelési nézetében vagy a tevékenység kimenetében az egyes másolásokhoz használt DIUs láthatók. További információ: [másolási tevékenység figyelése](copy-activity-monitoring.md). Az alapértelmezett érték felülbírálásához a következő értéket kell megadnia a `dataIntegrationUnits` tulajdonsághoz. A másolási művelet által a futási időben használt *DIUs tényleges száma* az adatminta alapján egyenlő vagy kisebb a beállított értéknél.

**A rendszer a felhasznált DIUs \* -másolási időtartam \* egységnyi árát/DIU óradíjat** számítja fel. Tekintse meg az [aktuális árakat.](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) A helyi pénznem és a különálló kedvezmények előfizetési típusok esetén alkalmazhatók.

**Példa**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "dataIntegrationUnits": 128
        }
    }
]
```

## <a name="self-hosted-integration-runtime-scalability"></a>Saját üzemeltetésű Integration Runtime skálázhatósága

Ha nagyobb átviteli sebességet szeretne elérni, a saját üzemeltetésű integrációs modul vertikális felskálázásával vagy felskálázásával végezhető el:

- Ha a saját üzemeltetésű IR-csomópont CPU-és rendelkezésre álló memóriája nincs teljesen kihasználva, de az egyidejű feladatok végrehajtása eléri a korlátot, akkor a csomóponton futtatható egyidejű feladatok számának növelésével bővítse a skálázást.  Útmutatásért lásd [itt](create-self-hosted-integration-runtime.md#scale-up) .
- Ha viszont a CPU magas a saját üzemeltetésű IR-csomóponton, vagy kevés a rendelkezésre álló memória, akkor hozzáadhat egy új csomópontot a több csomóponton lévő terhelés felskálázásához.  Útmutatásért lásd [itt](create-self-hosted-integration-runtime.md#high-availability-and-scalability) .

Vegye figyelembe, hogy a következő esetekben az Egypéldányos tevékenységek végrehajtása több saját üzemeltetésű IR-csomópontot is kihasználhat:

- A fájlok számától és méretétől függően az adatok másolása a fájl alapú áruházakból.
- Az adatpartíciók számától függően másolhatja az adatok másolását a particionálási lehetőséggel rendelkező adattárból (beleértve az [Azure SQL Database](connector-azure-sql-database.md#azure-sql-database-as-the-source), az [Azure SQL felügyelt példányát](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), az [Azure szinapszis Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), az [Oracle](connector-oracle.md#oracle-as-source), a [Netezza](connector-netezza.md#netezza-as-source), az [SAP HANA](connector-sap-hana.md#sap-hana-as-source), az [SAP Open hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source), az [SAP Table](connector-sap-table.md#sap-table-as-source), a [SQL Server](connector-sql-server.md#sql-server-as-a-source)és a [Teradata](connector-teradata.md#teradata-as-source)).

## <a name="parallel-copy"></a>Párhuzamos másolás

A másolási tevékenységnél párhuzamos másolást ( `parallelCopies` tulajdonságot) is beállíthat, hogy jelezze a másolási tevékenység által használni kívánt párhuzamosságot. Ezt a tulajdonságot úgy tekintheti meg, mint a másolási tevékenységben a forrásból beolvasott szálak maximális száma, vagy párhuzamosan írható a fogadó adattárba.

A párhuzamos másolás az [adatintegrációs egységek](#data-integration-units) vagy a [saját üzemeltetésű IR-csomópontok](#self-hosted-integration-runtime-scalability)felé merőleges. A rendszer az összes DIUs vagy saját üzemeltetésű IR-csomópontra kiterjed.

Az egyes másolási tevékenységek futtatásakor a rendszer alapértelmezés szerint Azure Data Factory dinamikusan alkalmazza az optimális párhuzamos másolási beállítást a forrás-fogadó pár és az adatminta alapján. 

> [!TIP]
> A párhuzamos másolás alapértelmezett viselkedése általában megadja a legjobb átviteli sebességet, amelyet az ADF automatikusan határoz meg a forrás-fogadó pár, az adatok mintája, a DIUs vagy a saját üzemeltetésű IR CPU/memória/csomópont száma alapján. Tekintse át a [másolási tevékenység teljesítményének hibakeresését](copy-activity-performance-troubleshooting.md) a párhuzamos másolás finomhangolásához című témakörben.

A következő táblázat a párhuzamos másolási viselkedést sorolja fel:

| Másolási forgatókönyv | Párhuzamos másolási viselkedés |
| --- | --- |
| A file Stores között | `parallelCopies` meghatározza a párhuzamosságot **a fájl szintjén**. Az egyes fájlokon belüli adatdarabolás automatikusan és transzparens módon történik. Úgy van kialakítva, hogy egy adott adattár-típushoz a legmegfelelőbb méretet használja az adat párhuzamos betöltéséhez. <br/><br/>A párhuzamos példányszámú másolási tevékenységek tényleges száma a futási időben nem haladja meg az Ön által használt fájlok számát. Ha a másolási viselkedés **mergeFile** , a másolási tevékenység nem tudja kihasználni a fájl szintű párhuzamosságot. |
| A file Store-ból a nem file Store-ba | -Az adatok Azure SQL Database vagy Azure Cosmos DBba másolásakor a párhuzamos másolás a fogadó rétegtől (DTU/RUs) függ.<br>-Az adatok Azure-táblázatba másolásakor az alapértelmezett párhuzamos másolás 4. |
| Nem a file Store-ból a file Store-ba | – Ha az adatok másolása a partícióról engedélyezett adattárból történik (beleértve [a Azure SQL Database](connector-azure-sql-database.md#azure-sql-database-as-the-source), az [Azure SQL felügyelt példányát](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), az [Azure szinapszis Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), az [Oracle](connector-oracle.md#oracle-as-source), a [Netezza](connector-netezza.md#netezza-as-source), az [SAP HANA](connector-sap-hana.md#sap-hana-as-source), az [SAP Open hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source), az [SAP Table](connector-sap-table.md#sap-table-as-source), a [SQL Server](connector-sql-server.md#sql-server-as-a-source)és a [Teradata](connector-teradata.md#teradata-as-source)), az alapértelmezett párhuzamos másolás 4. A párhuzamos példányszámú másolási tevékenységek tényleges száma a futási időben nem haladja meg az Ön által használt adatpartíciók számát. Ha saját üzemeltetésű Integration Runtime használ, és másolja az Azure Blob/ADLS Gen2ba, vegye figyelembe, hogy a maximális effektív párhuzamos másolás 4 vagy 5.<br>– Más esetekben a párhuzamos másolás nem lép érvénybe. Még ha a párhuzamosság is meg van adva, a rendszer nem alkalmazza. |
| Nem fájlos tárolók között | -Az adatok Azure SQL Database vagy Azure Cosmos DBba másolásakor a párhuzamos másolás a fogadó rétegtől (DTU/RUs) függ.<br/>– Ha az adatok másolása a partícióról engedélyezett adattárból történik (beleértve [a Azure SQL Database](connector-azure-sql-database.md#azure-sql-database-as-the-source), az [Azure SQL felügyelt példányát](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), az [Azure szinapszis Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), az [Oracle](connector-oracle.md#oracle-as-source), a [Netezza](connector-netezza.md#netezza-as-source), az [SAP HANA](connector-sap-hana.md#sap-hana-as-source), az [SAP Open hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source), az [SAP Table](connector-sap-table.md#sap-table-as-source), a [SQL Server](connector-sql-server.md#sql-server-as-a-source)és a [Teradata](connector-teradata.md#teradata-as-source)), az alapértelmezett párhuzamos másolás 4.<br>-Az adatok Azure-táblázatba másolásakor az alapértelmezett párhuzamos másolás 4. |

Az adattárakat üzemeltető gépek terhelésének szabályozásához, vagy a másolási teljesítmény finomhangolásához felülbírálhatja az alapértelmezett értéket, és megadhatja a `parallelCopies` tulajdonság értékét. Az értéknek 1-nél nagyobb vagy azzal egyenlő egész számnak kell lennie. Futásidőben a legjobb teljesítmény érdekében a másolási tevékenység olyan értéket használ, amely kisebb vagy egyenlő, mint a megadott érték.

Ha megad egy értéket a `parallelCopies` tulajdonsághoz, vegye figyelembe a terhelés növekedését a forrás-és a fogadó adattárakban. Vegye figyelembe a terhelés növekedését is a saját üzemeltetésű integrációs modul esetében, ha a másolási tevékenységet a rendszer felhatalmazza. Ez a terhelés növekszik, különösen akkor, ha több tevékenység vagy ugyanazon tevékenység egyidejű futtatása történik ugyanazon az adattárban. Ha azt észleli, hogy az adattár vagy a saját üzemeltetésű integrációs modul túlterhelt a terheléssel, csökkentse az `parallelCopies` értéket a terhelés enyhítéséhez.

**Példa**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "parallelCopies": 32
        }
    }
]
```

## <a name="staged-copy"></a>Előkészített másolás

Amikor Adatmásolást végez egy forrás adattárból egy fogadó adattárba, dönthet úgy, hogy az Azure Blob Storage-t vagy Azure Data Lake Storage Gen2 átmeneti átmeneti tárolóként használja. Az előkészítés különösen a következő esetekben hasznos:

- **A különböző adattárokból származó adatok az Azure szinapszis Analyticsbe való betöltését a Base használatával, az adatoknak a hópehely-ból való másolásával, vagy az Amazon vöröseltolódás/HDFS performantly származó adatok betöltésével kívánja betölteni.** További részletek:
  - Az [adatok Azure szinapszis-elemzésbe való betöltéséhez használjon albase-](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-synapse-analytics)t.
  - [Hópehely-összekötő](connector-snowflake.md)
  - [Amazon Redshift-összekötő](connector-amazon-redshift.md)
  - [HDFS-összekötő](connector-hdfs.md)
- **A vállalati informatikai házirendek miatt nem szeretné megnyitni a 80-as és a 443-es porton kívüli portokat a tűzfalon.** Ha például egy helyszíni adattárból másol be egy Azure SQL Database vagy egy Azure szinapszis Analytics-re, akkor a Windows tűzfal és a vállalati tűzfal esetében aktiválni kell a kimenő TCP-kommunikációt az 1433-as porton. Ebben az esetben a szakaszos másolás kihasználhatja a saját üzemeltetésű integrációs modul előnyeit, hogy először az 443-as porton keresztül, HTTP-n vagy HTTPS-en keresztül másolja az adatok egy átmeneti tárolóba, majd az adatok betöltését SQL Database vagy Azure szinapszis Analyticsbe. Ebben a folyamatban nem kell engedélyeznie a 1433-es portot.
- **Időnként igénybe veheti a hibrid adatáthelyezést (azaz egy helyszíni adattárból a Felhőbeli adattárolóba történő másolást) lassú hálózati kapcsolaton keresztül.** A teljesítmény javítása érdekében a szakaszos másolással tömörítheti a helyszíni adatok, így kevesebb időt vesz igénybe az adatok áthelyezése a Felhőbeli átmeneti adattárba. Ezután kibonthatja az átmeneti tárolóban lévő adatok kibontását a cél adattárba való betöltés előtt.

### <a name="how-staged-copy-works"></a>A szakaszos másolás működése

Az előkészítési funkció aktiválása után a rendszer először a forrás adattárból másolja az adatokból az átmeneti tárolóba (saját Azure-Blob vagy Azure Data Lake Storage Gen2). Ezután az adatok az előkészítésből a fogadó adattárba lesznek másolva. Azure Data Factory másolási tevékenység automatikusan kezeli a kétlépcsős folyamatot, és az adatok áthelyezése után az átmeneti tárolóból is törli az ideiglenes adatok mennyiségét.

![Előkészített másolás](media/copy-activity-performance/staged-copy.png)

Ha átmeneti tároló használatával aktiválja az adatáthelyezést, megadhatja, hogy az adatok tömörítése megtörténjen-e, mielőtt áthelyezi az adatátvitelt a forrás adattárból az átmeneti tárolóba, majd kibontja a kibontást, mielőtt áthelyezi az adatok átvitelét egy ideiglenes vagy átmeneti adattárból a fogadó adattárba.

Jelenleg nem másolhat Adatmásolást két olyan adattár között, amely különböző saját üzemeltetésű IRs-kapcsolaton keresztül csatlakozik, sem a, sem a szakaszos másolat nélkül. Ilyen esetben két explicit módon láncolt másolási tevékenységet konfigurálhat a forrásról az előkészítésre való másoláshoz, majd az előkészítésből a fogadóba.

### <a name="configuration"></a>Konfiguráció

Konfigurálja a **enableStaging** beállítást a másolási tevékenységben annak megadásához, hogy szeretné-e az adattárolást a tárolóba, mielőtt betölti azt egy célhely-adattárba. A **enableStaging** beállításakor `TRUE` adja meg az alábbi táblázatban felsorolt további tulajdonságokat. 

| Tulajdonság | Leírás | Alapértelmezett érték | Kötelező |
| --- | --- | --- | --- |
| enableStaging |Itt adhatja meg, hogy egy átmeneti átmeneti tárolón keresztül kívánja-e az Adatmásolást. |Hamis |No |
| linkedServiceName |Adja meg egy [Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) vagy [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) társított szolgáltatás nevét, amely az átmeneti átmeneti tárolóként használt tároló példányára hivatkozik. |N/A |Igen, ha a **enableStaging** értéke TRUE (igaz) |
| path |Itt adhatja meg, hogy milyen elérési utat kívánja használni az előkészített adatkészletekben. Ha nem ad meg elérési utat, a szolgáltatás létrehoz egy tárolót az ideiglenes adattároláshoz. |N/A |No |
| enableCompression |Megadja, hogy a rendszer a célhelyre való másolás előtt tömöríti-e az adatfájlokat. Ez a beállítás csökkenti az átvitel alatt álló adatmennyiséget. |Hamis |No |

>[!NOTE]
> Ha a szakaszos másolást engedélyezte a tömörítést, az átmeneti blobhoz társított szolgáltatás egyszerű vagy MSI-hitelesítése nem támogatott.

A másolási tevékenység mintájának definíciója az előző táblázatban ismertetett tulajdonságokkal rendelkezik:

```json
"activities":[
    {
        "name": "CopyActivityWithStaging",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "OracleSource",
            },
            "sink": {
                "type": "SqlDWSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingStorage",
                    "type": "LinkedServiceReference"
                },
                "path": "stagingcontainer/path"
            }
        }
    }
]
```

### <a name="staged-copy-billing-impact"></a>Előkészített másolási számlázási hatás

A díj két lépésből áll: a másolás időtartama és a másolás típusa alapján.

* Ha a Felhőbeli másolás során használ átmeneti tárolást, amely az adatok Felhőbeli adattárból egy másik Felhőbeli adattárba való másolását végzi el, az Azure Integration Runtime által felhatalmazott mindkét szakaszban az [1. lépés és a 2. lépés] x [Felhőbeli másolási egység ára] értékének összegét kell megfizetnie.
* Ha a hibrid másolás során végez átmeneti előkészítést, amely egy helyszíni adattárból egy Felhőbeli adattárba másol egy, a saját üzemeltetésű integrációs modul által felhatalmazott szakaszt, akkor a [hibrid másolás időtartama] x [hibrid másolási egység ára] + [Felhőbeli másolás időtartama] x [Felhőbeli másolási egység ára].

## <a name="next-steps"></a>Következő lépések
Lásd a másolási tevékenység egyéb cikkeit:

- [Másolási tevékenység – áttekintés](copy-activity-overview.md)
- [Útmutató a másolási tevékenységek teljesítményéhez és skálázhatóságához](copy-activity-performance.md)
- [A másolási tevékenység teljesítményével kapcsolatos hibák](copy-activity-performance-troubleshooting.md)
- [Az adatok áttelepíthetők a Azure Data Factory használatával az Azure-ba vagy az adattárházból](data-migration-guidance-overview.md)
- [Adatok migrálása az Amazon S3-ból az Azure Storage-ba](data-migration-guidance-s3-azure-storage.md)