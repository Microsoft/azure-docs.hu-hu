---
title: Azure Data Lake Storage Gen2 Bemutatkozás
description: Olvassa el a Azure Data Lake Storage Gen2 bevezetését. Ismerje meg a legfontosabb funkciókat. Tekintse át a támogatott blob Storage-szolgáltatásokat, az Azure-szolgáltatások integrációját és a platformokat.
author: normesta
ms.service: storage
ms.topic: overview
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 1c4d04e25bf8f7d981c998baafb468f04b66eaf1
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879898"
---
# <a name="introduction-to-azure-data-lake-storage-gen2"></a>Az Azure Data Lake Storage Gen2 bemutatása

A Azure Data Lake Storage Gen2 az [Azure Blob Storage](storage-blobs-introduction.md)-ra épülő, Big Data Analytics szolgáltatáshoz dedikált képességek összessége. 

A Data Lake Storage Gen2 a [Azure Data Lake Storage Gen1](../../data-lake-store/index.yml) képességeit az Azure Blob Storage szolgáltatással konvergál. A Data Lake Storage Gen2 például a fájlrendszer szemantikai, a fájl szintű biztonságot és a skálázást biztosítja. Mivel ezek a képességek a blob Storage-ra épülnek, a magas rendelkezésre állású/vész-helyreállítási funkciók mellett alacsony költséghatékonyságú, többrétegű tárterület is elérhető.

## <a name="designed-for-enterprise-big-data-analytics"></a>Nagyvállalati big data elemzésekhez tervezve

A Data Lake Storage Gen2 lehetővé teszi az Azure Storage számára, hogy a vállalati adattavakat az Azure-ban hozza létre. Több petabájt-szolgáltatás megkezdése és kiszolgálása mellett a Data Lake Storage Gen2 segítségével könnyedén kezelhet nagy mennyiségű adatot.

Data Lake Storage Gen2 alapvető része egy [hierarchikus névtér](data-lake-storage-namespace.md) hozzáadása a blob Storage-hoz. A hierarchikus névtér objektumokat vagy fájlokat rendez a címtárak hierarchiájában a hatékony adateléréshez. A közös objektum-tároló elnevezési konvenció a név perjelét használja a hierarchikus címtár-struktúra kiépítéséhez. Ez a struktúra Data Lake Storage Gen2. Az olyan műveletek, mint például a címtár átnevezése vagy törlése, egyetlen atomi metaadat-műveletvé válnak a címtárban. Nem kell enumerálni és feldolgozni az összes olyan objektumot, amely megosztja a könyvtár nevének előtagját.

A Data Lake Storage Gen2 a blob Storage-ra épül, és a következő módokon javítja a teljesítményt, a felügyeletet és a biztonságot:

-   A **teljesítmény** optimalizálva van, mert nem szükséges az adatok másolása és átalakítása előfeltételként az elemzéshez. A blob Storage-beli sík névtérhez képest a hierarchikus névtér nagy mértékben javítja a címtár-felügyeleti műveletek teljesítményét, ami javítja a feladatok általános teljesítményét.

-   A **felügyelet** egyszerűbb, mivel a fájlokat címtárakban és Alkönyvtárakban is rendszerezheti és kezelheti.

-   A **Biztonság** kényszeríthető, mert POSIX-engedélyeket adhat meg a címtárakhoz vagy az egyes fájlokhoz.

Emellett a Data Lake Storage Gen2 nagyon költséghatékony, mert az [Azure Blob Storage](storage-blobs-introduction.md)-ra épül. A további funkciók tovább csökkentik az Azure-ban futó big data-elemzések teljes tulajdonlási költségeit.

## <a name="key-features-of-data-lake-storage-gen2"></a>A Data Lake Storage Gen2 főbb funkciói

-   **Hadoop-kompatibilis hozzáférés**: a Data Lake Storage Gen2 lehetővé teszi az adatkezelést és az adathozzáférést ugyanúgy, mint a [HADOOP elosztott fájlrendszer (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Az új [ABFS-illesztőprogram](data-lake-storage-abfs-driver.md) (az adateléréshez használatos) minden Apache Hadoop környezeten belül elérhető. Ezek a környezetek közé tartoznak az [Azure HDInsight](../../hdinsight/index.yml)*,* a [Azure Databricks](/azure/databricks/)és az [Azure szinapszis Analytics](../../synapse-analytics/index.yml).

-   **A POSIX-engedélyek felülbírálása**: a Data Lake Gen2 biztonsági modellje támogatja az ACL és a POSIX engedélyeket, valamint a Data Lake Storage Gen2ra vonatkozó további részletességet. A beállítások a Storage Exploreron vagy olyan keretrendszereken keresztül konfigurálhatók, mint a kaptár és a Spark.

-   **Költséghatékony**: Data Lake Storage Gen2 alacsony költséghatékonyságú tárolási kapacitást és tranzakciókat biztosít. Az [Azure Blob Storage életciklusának](storage-lifecycle-management-concepts.md) olyan funkciói, mint például az adatváltások életciklusán keresztül.

-   **Optimalizált illesztőprogram**: a ABFS-illesztőprogram kifejezetten Big Data elemzésekhez van [optimalizálva](data-lake-storage-abfs-driver.md) . A megfelelő REST API-k a végponton keresztül vannak felszínen `dfs.core.windows.net` .

### <a name="scalability"></a>Méretezhetőség

Az Azure Storage a Data Lake Storage Gen2-vagy blob Storage-felületeken keresztüli hozzáférésének megtervezésével méretezhető. *Számos több exabájt* képes tárolni és kiszolgálni. Ez a tárterület a másodpercenkénti bemeneti/kimeneti műveletek (IOPS) magas szintjein mért Gigabit/másodperc (GB/s) értékkel érhető el. A feldolgozás a szolgáltatás, a fiók és a fájl szintjein mért, a kérelmekre vonatkozó késések mellett történik.

### <a name="cost-effectiveness"></a>Költséghatékonyság

Mivel Data Lake Storage Gen2 az Azure Blob Storage-ra épül, a tárterület kapacitása és a tranzakciós költségek alacsonyabbak. A többi felhőalapú tárolási szolgáltatástól eltérően nem kell áthelyeznie vagy átalakítania az adatait, mielőtt elemezni tudja azokat. A díjszabással kapcsolatos további információkért lásd: az [Azure Storage díjszabása](https://azure.microsoft.com/pricing/details/storage).

Emellett a [hierarchikus névtérrel](data-lake-storage-namespace.md) rendelkező funkciók jelentős mértékben javítják a számos elemzési feladat általános teljesítményét. A teljesítmény fokozása azt jelenti, hogy kevesebb számítási teljesítményre van szükség az azonos mennyiségű adat feldolgozásához, így a végpontok közötti elemzési feladathoz tartozó teljes tulajdonlási költség (TCO) alacsonyabb.

### <a name="one-service-multiple-concepts"></a>Egy szolgáltatás, több fogalom

Mivel Data Lake Storage Gen2 az Azure Blob Storage-ra épül, több fogalom is lemondhatja ugyanazokat a megosztott dolgokat.

A következők az egyenértékű entitások, amelyeket a különböző fogalmak ismertetnek. Ha másként nincs megadva, akkor ezek az entitások közvetlenül szinonimák:

| Fogalom                                | Legfelső szintű szervezet | Alacsonyabb szintű szervezet                                            | Adattároló |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| Blobok – általános célú objektumok tárolója | Tároló              | Virtuális könyvtár (csak SDK – nem biztosít atomi manipulációt) | Blob           |
| Azure Data Lake Storage Gen2 – elemzési tár          | Tároló            | Címtár                                                           | Fájl           |

## <a name="supported-blob-storage-features"></a>Támogatott Blob Storage-funkciók

A fiókja számára elérhetők a blob Storage-funkciók, például a [diagnosztikai naplózás](../common/storage-analytics-logging.md), a [hozzáférési szintek](storage-blob-storage-tiers.md)és a [blob Storage életciklus-kezelési szabályzatok](storage-lifecycle-management-concepts.md) . 

A támogatott blob Storage-funkciók listáját a [Azure Data Lake Storage Gen2 elérhető blob Storage szolgáltatások](data-lake-storage-supported-blob-storage-features.md)című részben tekintheti meg.

## <a name="supported-azure-service-integrations"></a>Támogatott Azure-szolgáltatások integrációi

Data Lake Storage a Gen2 számos Azure-szolgáltatást támogat. Felhasználhatja őket az adatok betöltésére, az elemzések elvégzésére és a vizuális ábrázolások létrehozására. A támogatott Azure-szolgáltatások listáját a [Azure Data Lake Storage Gen2t támogató Azure-szolgáltatások](data-lake-storage-supported-azure-services.md)című témakörben tekintheti meg.

## <a name="supported-open-source-platforms"></a>Támogatott nyílt forráskódú platformok

Számos nyílt forráskódú platform támogatja a Data Lake Storage Gen2. A teljes listát lásd: a [Azure Data Lake Storage Gen2t támogató nyílt forráskódú platformok](data-lake-storage-supported-open-source-platforms.md).

## <a name="see-also"></a>Lásd még

- [Ismert problémák a Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Több protokollos hozzáférés Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)