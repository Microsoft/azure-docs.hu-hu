---
title: Az adatáthelyezés a másolási tevékenység használatával
description: 'Ismerje meg az adatáthelyezést Data Factory folyamatokban: adatáttelepítés a felhőalapú tárolók között, valamint egy helyszíni tároló és egy felhőalapú tároló között. Másolási tevékenység használata.'
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/05/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 8a3fe99896ff244d2e35737919e6797141095acb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100364018"
---
# <a name="move-data-by-using-copy-activity"></a>Az adatáthelyezés a másolási tevékenység használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-data-movement-activities.md)
> * [2-es verzió (aktuális verzió)](../copy-activity-overview.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse [meg a másolási tevékenységet a v2-ben](../copy-activity-overview.md)című témakört.

## <a name="overview"></a>Áttekintés
Azure Data Factory a másolási tevékenységgel Adatmásolást végez a helyszíni és a Felhőbeli adattárak között. Az Adatmásolás után a rendszer tovább alakíthatja és elemezheti a fájlokat. A másolási tevékenység használatával is közzéteheti az üzleti intelligencia (BI) és az alkalmazások felhasználásának átalakítási és elemzési eredményeit.

![A másolási tevékenység szerepe](media/data-factory-data-movement-activities/copy-activity.png)

A másolási tevékenységet biztonságos, megbízható, méretezhető és [globálisan elérhető szolgáltatás](#global)működteti. Ez a cikk a Data Factory és a másolási tevékenység adatáthelyezési adatait ismerteti.

Először is lássuk, hogyan történik az adatáttelepítés két felhőalapú adattár, valamint egy helyszíni adattár és egy felhőalapú adattár között.

> [!NOTE]
> Az általános tevékenységekről a [folyamatok és tevékenységek ismertetése](data-factory-create-pipelines.md)című témakörben olvashat bővebben.
>
>

### <a name="copy-data-between-two-cloud-data-stores"></a>Az Adatmásolás két felhőalapú adattár között
Ha a forrás-és fogadó adattárak is a felhőben vannak, a másolási tevékenység a következő szakaszokon halad át az adatok forrásból a fogadóba való másolásához. Az a szolgáltatás, amely a másolási tevékenységet felruházza:

1. Adatokat olvas be a forrás adattárból.
2. Szerializálási/deszerializálási, tömörítési/kibontási, oszlop-hozzárendelési és átalakítási műveletet végez. Ezek a műveletek a bemeneti adatkészlet, a kimeneti adatkészlet és a másolási tevékenység konfigurációján alapulnak.
3. Beírja az adatot a célhely adattárba.

A szolgáltatás automatikusan kiválasztja az optimális régiót az adatok áthelyezéséhez. Ez a régió általában a fogadó adattárhoz legközelebb eső hely.

![Felhőből felhőbe másolás](./media/data-factory-data-movement-activities/cloud-to-cloud.png)

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Az Adatmásolás egy helyszíni adattár és egy felhőalapú adattár között
Egy helyszíni adattár és egy felhőalapú adattár közötti biztonságos áthelyezéshez telepítse adatkezelés átjárót a helyszíni gépre. Adatkezelés átjáró egy olyan ügynök, amely lehetővé teszi a hibrid adatáthelyezést és feldolgozást. Telepítheti ugyanarra a gépre, mint maga az adattár, vagy egy különálló gépen, amely hozzáféréssel rendelkezik az adattárhoz.

Ebben az esetben adatkezelés átjáró hajtja végre a szerializálási/deszerializálás, a tömörítés/kitömörítés, az oszlop-hozzárendelés és a típus konvertálása műveletet. Az adatforgalom nem a Azure Data Factory szolgáltatáson keresztül történik. Ehelyett adatkezelés átjáró közvetlenül a célhelyre írja az adatot.

![Helyszíni – Felhőbeli másolás](./media/data-factory-data-movement-activities/onprem-to-cloud.png)

Lásd: az [adatáthelyezés a helyszíni és a Felhőbeli adattárak között](data-factory-move-data-between-onprem-and-cloud.md) egy bevezetéshez és a bemutatóhoz. Az ügynökkel kapcsolatos részletes információkért tekintse meg [adatkezelés átjárót](data-factory-data-management-gateway.md) .

Az Azure IaaS Virtual Machines szolgáltatásban (VM) üzemeltetett támogatott adattárakba vagy adatkezelés átjáró használatával is áthelyezheti az adatait. Ebben az esetben a adatkezelés-átjárót telepítheti ugyanarra a virtuális gépre, mint maga az adattár, vagy egy különálló virtuális gépen, amely hozzáfér az adattárhoz.

## <a name="supported-data-stores-and-formats"></a>Támogatott adattárak és-formátumok
A Data Factory másolási tevékenysége adatokat másol egy forrásadattárból egy fogadó adattárba. A Data Factory a következő adattárakat támogatja. Az adatok bármilyen forrásból bármilyen fogadóba másolhatók. Az adattárra kattintva megtudhatja, hogy az adott tárolóba, illetve tárolóból hogyan másolhat adatokat.

> [!NOTE] 
> Ha olyan adattárból/adattárba szeretne adatokat továbbítani, amely nem támogatja a másolási tevékenységet, használjon egy **egyéni tevékenységet** a Data Factoryban az adatok saját logika szerinti másolásához/áthelyezéséhez. További információ az egyéni tevékenységek létrehozásával és használatával kapcsolatban: [Egyéni tevékenységek használata Azure Data Factory-folyamatban](data-factory-use-custom-activities.md).

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> A * jellel ellátott adattárak lehetnek helyszíniek vagy az Azure IaaS részei, és használatukhoz telepíteni kell az [Adatkezelési átjárót](data-factory-data-management-gateway.md) a helyszíni/Azure IaaS gépre.

### <a name="supported-file-formats"></a>Támogatott fájlformátumok
A másolási tevékenység használatával **fájlok másolását** végezheti el két fájl alapú adattár között, a bemeneti és a kimeneti adatkészlet definíciójában is kihagyhatja a [Format (formátum) szakaszt](data-factory-create-datasets.md) . Az Adatmásolás hatékonyan, szerializálás/deszerializálás nélkül történik.

A másolási tevékenység a megadott formátumú fájlokra is beolvassa és írja a fájlokat: **szöveg, JSON, Avro, ork és parketta**, valamint a tömörítési kodek **gzip, deflate, BZip2 és ZipDeflate** támogatott. A részleteket a [támogatott fájl-és Tömörítési formátumok](data-factory-supported-file-and-compression-formats.md) című részben tekintheti meg.

Például a következő másolási tevékenységeket végezheti el:

* Adatmásolás egy SQL Server-adatbázisban, és az Azure Data Lake Store az ork formátumban kell írni.
* A fájlok szövegfájlba (CSV) való másolása a helyszíni fájlrendszerből és az Azure Blobba való írás Avro formátumban.
* Másolja a tömörített fájlokat a helyszíni fájlrendszerből, majd bontsa ki a kibontást, majd Azure Data Lake Store.
* Adatok másolása GZip tömörített szöveg (CSV) formátumban az Azure Blobból, és írás a Azure SQL Databaseba.

## <a name="globally-available-data-movement"></a><a name="global"></a>Globálisan elérhető adatáthelyezés
Azure Data Factory csak az USA nyugati régiójában, az USA keleti régiójában és az észak-európai régióban érhető el. A másolási tevékenységet biztosító szolgáltatás azonban globálisan elérhető a következő régiókban és földrajzi területeken. A globálisan elérhető topológia biztosítja a hatékony adatáthelyezést, amely általában elkerüli a régiók közötti ugrásokat. Tekintse meg a [szolgáltatások régiónként](https://azure.microsoft.com/regions/#services) való elérhetőségét Data Factory és az adatáthelyezést egy adott régióban.

### <a name="copy-data-between-cloud-data-stores"></a>Az Adatmásolás a felhőalapú adattárak között
Ha a forrás-és fogadó adattárak is a felhőben vannak, Data Factory a szolgáltatás központi telepítését használja abban a régióban, amely az adott földrajzi helyen található fogadóhoz legközelebb esik az adatáthelyezéshez. A hozzárendeléseket a következő táblázatban tekintheti meg:

| A célhely adattárolóinak földrajza | A célként megadott adattár régiója | Az adatáthelyezéshez használt régió |
|:--- |:--- |:--- |
| Egyesült Államok | USA keleti régiója | USA keleti régiója |
| &nbsp; | USA 2. keleti régiója | USA 2. keleti régiója |
| &nbsp; | Az USA középső régiója | Az USA középső régiója |
| &nbsp; | USA északi középső régiója | USA északi középső régiója |
| &nbsp; | USA déli középső régiója | USA déli középső régiója |
| &nbsp; | USA nyugati középső régiója | USA nyugati középső régiója |
| &nbsp; | USA nyugati régiója | USA nyugati régiója |
| &nbsp; | USA 2. nyugati régiója | USA 2. nyugati régiója |
| Kanada | Kelet-Kanada | Közép-Kanada |
| &nbsp; | Közép-Kanada | Közép-Kanada |
| Brazília | Dél-Brazília | Dél-Brazília |
| Európa | Észak-Európa | Észak-Európa |
| &nbsp; | Nyugat-Európa | Nyugat-Európa |
| Egyesült Királyság | Az Egyesült Királyság nyugati régiója | Az Egyesült Királyság déli régiója |
| &nbsp; | Az Egyesült Királyság déli régiója | Az Egyesült Királyság déli régiója |
| Ázsia és a Csendes-óceáni térség | Délkelet-Ázsia | Délkelet-Ázsia |
| &nbsp; | Kelet-Ázsia | Délkelet-Ázsia |
| Ausztrália | Kelet-Ausztrália | Kelet-Ausztrália |
| &nbsp; | Délkelet-Ausztrália | Délkelet-Ausztrália |
| India | Közép-India | Közép-India |
| &nbsp; | Nyugat-India | Közép-India |
| &nbsp; | Dél-India | Közép-India |
| Japán | Kelet-Japán | Kelet-Japán |
| &nbsp; | Nyugat-Japán | Kelet-Japán |
| Dél-Korea | Dél-Korea középső régiója | Dél-Korea középső régiója |
| &nbsp; | Dél-Korea déli régiója | Dél-Korea középső régiója |

Azt is megteheti, hogy explicit módon megadhatja Data Factory szolgáltatás azon régióját, amelyet a másolási tevékenységben a tulajdonság megadásával kíván használni `executionLocation` `typeProperties` . A tulajdonsághoz tartozó támogatott értékek az **adatáthelyezési** oszlophoz használt fenti régióban vannak felsorolva. Vegye figyelembe, hogy az adatai a másolás során a hálózaton keresztül haladnak át az adott régióban. Például a Koreában található Azure-áruházak közötti másoláshoz megadhatja, `"executionLocation": "Japan East"` hogy az átirányítást a japán régión keresztül történjen (lásd a [JSON minta](#by-using-json-scripts) hivatkozásként című témakört).

> [!NOTE]
> Ha a célként megadott adattár régiója nem szerepel az előző listában, vagy nem észlelhető, a másolási tevékenység alapértelmezés szerint nem halad át egy másik régión, kivéve, ha meg `executionLocation` van adva. A támogatott régiók listája az idő múlásával ki lesz bővítve.
>

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Az Adatmásolás egy helyszíni adattár és egy felhőalapú adattár között
Ha az adatok a helyszíni (vagy az Azure Virtual Machines/IaaS) és a Cloud Stores között másolódnak át, [adatkezelés átjáró](data-factory-data-management-gateway.md) végzi az adatáthelyezést egy helyszíni gépen vagy virtuális gépen. Az adatforgalom nem a felhőben futó szolgáltatáson keresztül történik, kivéve, ha az [előkészített másolási](data-factory-copy-activity-performance.md#staged-copy) funkciót használja. Ebben az esetben az adatforgalom az átmeneti Azure Blob Storage-on keresztül zajlik a fogadó adattárba való írás előtt.

## <a name="create-a-pipeline-with-copy-activity"></a>Másolási tevékenységgel rendelkező folyamat létrehozása
A másolási tevékenységgel rendelkező folyamatokat többféleképpen is létrehozhatja:

### <a name="by-using-the-copy-wizard"></a>A másolás varázsló használatával
A Data Factory másolás varázsló segítséget nyújt egy másolási tevékenységgel rendelkező folyamat létrehozásához. Ez a folyamat lehetővé teszi, hogy adatokat másoljon a támogatott forrásokból a célhelyekre a társított szolgáltatások, adatkészletek és folyamatok JSON-definíciójának *írása nélkül* . A varázslóval kapcsolatos részletekért tekintse meg [Data Factory másolás varázslót](data-factory-copy-wizard.md) .  

### <a name="by-using-json-scripts"></a>JSON-parancsfájlok használatával
A Visual Studióban Data Factory szerkesztőt is használhat, vagy Azure PowerShell egy folyamat JSON-definíciójának létrehozásához (másolási tevékenység használatával). Ezután üzembe helyezheti a folyamatot Data Factoryban. Lásd [: oktatóanyag: másolási tevékenység használata Azure Data Factoryi folyamatokban](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) az oktatóanyaghoz részletes utasításokkal.    

A JSON-tulajdonságok (például a név, a leírás, a bemeneti és a kimeneti táblák és a házirendek) minden típusú tevékenység számára elérhetők. A tevékenység szakaszában elérhető tulajdonságok az `typeProperties` egyes tevékenységtípusok esetében eltérőek.

Másolási tevékenység esetén a `typeProperties` szakasz a források és a mosdók típusától függően változik. A [támogatott források és mosogatók](#supported-data-stores-and-formats) szakaszban kattintson a forrásra/fogadóra, és Ismerje meg, hogy milyen típusú tulajdonságokat támogat a másolási tevékenység az adott adattár számára.

Íme egy példa JSON-definíció:

```json
{
  "name": "ADFTutorialPipeline",
  "properties": {
    "description": "Copy data from Azure blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "type": "Copy",
        "inputs": [
          {
            "name": "InputBlobTable"
          }
        ],
        "outputs": [
          {
            "name": "OutputSQLTable"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
          },
          "executionLocation": "Japan East"          
        },
        "Policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ],
    "start": "2016-07-12T00:00:00Z",
    "end": "2016-07-13T00:00:00Z"
  }
}
```
A kimeneti adatkészletben definiált ütemterv meghatározza, hogy a tevékenység Mikor fusson (például: **napi**, gyakoriság, **nap**, intervallum, **1**). A tevékenység adatokat másol egy bemeneti adatkészletből (**forrás**) egy kimeneti adatkészletbe (**fogadó).**

Több bemeneti adatkészletet is megadhat a másolási tevékenységhez. Ezek a függőségek ellenőrzéséhez használatosak a tevékenység futtatása előtt. Azonban csak az első adatkészlet adatait másolja a rendszer a cél adatkészletbe. További információ: [Ütemezés és végrehajtás](data-factory-scheduling-and-execution.md).  

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás
Tekintse meg a [másolási tevékenység teljesítményére és hangolására vonatkozó útmutatót](data-factory-copy-activity-performance.md), amely az adatáthelyezés (másolási tevékenység) teljesítményét befolyásoló legfontosabb tényezőket ismerteti Azure Data Factoryban. A belső tesztelés során a megfigyelt teljesítményt is felsorolja, és a másolási tevékenységek teljesítményének optimalizálására szolgáló különböző módokat tárgyalja.

## <a name="fault-tolerance"></a>Hibatűrés
Alapértelmezés szerint a másolási tevékenység leállítja az adatok másolását, és sikertelenül tér vissza a forrás és a fogadó közötti inkompatibilis adatok észlelésekor. Habár a explicit módon konfigurálhatja a nem kompatibilis sorok kihagyását és naplózását, és csak a kompatibilis adatmásolt fájlokat másolja át a másolás sikerességének érdekében. További részletekért tekintse meg a [másolási tevékenység hibatűrését](data-factory-copy-activity-fault-tolerance.md) ismertető témakört.

## <a name="security-considerations"></a>Biztonsági szempontok
Tekintse meg a biztonsági [szempontokat](data-factory-data-movement-security-considerations.md), amelyek ismertetik, hogy az adatátviteli szolgáltatások milyen biztonsági infrastruktúrát biztosítanak a Azure Data Factory az adatai védelméhez.

## <a name="scheduling-and-sequential-copy"></a>Ütemezés és szekvenciális másolás
Tekintse meg az ütemezés és [végrehajtás](data-factory-scheduling-and-execution.md) című témakört, amely részletesen ismerteti, hogyan működik az ütemezés és a végrehajtás a Data Factory. Több másolási művelet is futtatható egymás után szekvenciális/rendezett módon. Tekintse meg a [Másolás szekvenciális](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) szakaszát.

## <a name="type-conversions"></a>Típuskonverziók
A különböző adattárak eltérő natív típusú rendszerekkel rendelkeznek. A másolási tevékenység a következő kétlépéses megközelítéssel hajtja végre az automatikus típus-konverziókat a forrás típusairól a fogadó típusokra:

1. Natív forrásból származó típusok konvertálása .NET-típusra.
2. .NET-típusról natív fogadó típusra konvertál.

A natív típusrendszer és az adattár .NET-típusra való leképezése a megfelelő adattár-cikkben található. (Kattintson az adott hivatkozásra a támogatott adattár táblában). Ezeket a leképezéseket a táblázatok létrehozásakor a megfelelő típusok meghatározására használhatja, így a másolási tevékenység a megfelelő konverziókat hajtja végre.

## <a name="next-steps"></a>Következő lépések
* További információ a másolási tevékenységről: [adatok másolása az Azure Blob Storage-ból a Azure SQL Databaseba](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
* Az adatok helyszíni adattárból Felhőbeli adattárba való áthelyezésével kapcsolatos további információkért lásd: [adatok áthelyezése a helyszínről a Felhőbeli adattárakba](data-factory-move-data-between-onprem-and-cloud.md).
