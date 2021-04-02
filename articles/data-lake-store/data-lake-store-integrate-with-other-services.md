---
title: Data Lake Storage Gen1 integrálása más Azure-szolgáltatásokkal
description: Ismerje meg, hogyan integrálhatja Azure Data Lake Storage Gen1eit más Azure-szolgáltatásokkal.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 929853f4dbedca7034c8e2a51e6231651a2dd08f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96461650"
---
# <a name="integrating-azure-data-lake-storage-gen1-with-other-azure-services"></a>Azure Data Lake Storage Gen1 integrálása más Azure-szolgáltatásokkal
A Azure Data Lake Storage Gen1 más Azure-szolgáltatásokkal együtt is használható, így szélesebb körű forgatókönyvek engedélyezhetők. A következő cikk felsorolja azokat a szolgáltatásokat, amelyeket a Data Lake Storage Gen1 integrálhat.

## <a name="use-data-lake-storage-gen1-with-azure-hdinsight"></a>Data Lake Storage Gen1 használata az Azure HDInsight
Olyan [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) -fürtöt építhet ki, amely a Data Lake Storage Gen1t használja HDFS-kompatibilis tárolóként. Ebben a kiadásban a Windows-és Linux-alapú Hadoop-és Storm-fürtök esetében Data Lake Storage Gen1 csak további tárterületként használható. Az ilyen fürtök továbbra is az Azure Storage-t (WASB) használják alapértelmezett tárolóként. A Windows-és Linux-alapú HBase-fürtök esetében azonban használhatja a Data Lake Storage Gen1t alapértelmezett tárolóként, illetve további tárterületként vagy mindkettőként.

A HDInsight-fürtök Data Lake Storage Gen1 használatával történő kiépítésével kapcsolatos utasításokért lásd:

* [HDInsight-fürt kiépítése Data Lake Storage Gen1 használatával Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
* [HDInsight-fürt kiépítése Data Lake Storage Gen1 alapértelmezett tárolóként Azure PowerShell használatával](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [HDInsight-fürt kiépítése Data Lake Storage Gen1 további tárterületként Azure PowerShell használatával](data-lake-store-hdinsight-hadoop-use-powershell.md)

## <a name="use-data-lake-storage-gen1-with-azure-data-lake-analytics"></a>Data Lake Storage Gen1 használata a Azure Data Lake Analytics
A [Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-overview.md) segítségével nagy mennyiségű adattal dolgozhat a felhőben. Dinamikusan kiépíti az erőforrásokat, és lehetővé teszi az elemzést terabájtos vagy akár olyan több exabájt is, amelyek számos támogatott adatforrásban tárolhatók, amelyek közül az egyik Data Lake Storage Gen1. A Data Lake Analytics kifejezetten a Data Lake Storage Gen1-hoz való együttműködésre lett optimalizálva, amely a legmagasabb szintű teljesítményt, adatátvitelt és párhuzamos biztosítja big data munkaterhelések esetén.

A Data Lake Analytics és a Data Lake Storage Gen1 használatával kapcsolatos utasításokért lásd: az [Data Lake Analytics használatának első lépései Data Lake Storage Gen1 használatával](../data-lake-analytics/data-lake-analytics-get-started-portal.md).

## <a name="use-data-lake-storage-gen1-with-azure-data-factory"></a>Data Lake Storage Gen1 használata a Azure Data Factory
Az Azure Tables, a Azure SQL Database, az Azure SQL DataWarehouse, az Azure Storage-blobok és a helyszíni adatbázisok adatainak betöltéséhez [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) is használhatja. Az Azure-ökoszisztéma első osztályú polgára, Azure Data Factory felhasználhatja az adatok betöltését a forrásból a Data Lake Storage Gen1ba.

A Azure Data Factory és az Data Lake Storage Gen1 használatával kapcsolatos utasításokért lásd: [adatok áthelyezése a Data Lake Storage Gen1 és a Data Factory használatával](../data-factory/connector-azure-data-lake-store.md).

## <a name="copy-data-from-azure-storage-blobs-into-data-lake-storage-gen1"></a>Adatok másolása az Azure Storage-Blobokból a Data Lake Storage Gen1ba
Azure Data Lake Storage Gen1 olyan parancssori eszközt biztosít, amely lehetővé teszi, hogy az Azure Blob Storageból egy Data Lake Storage Gen1-fiókba másolhatja az AdlCopy. További információ: [adatok másolása az Azure Storage-blobokból a Data Lake Storage Gen1ba](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="copy-data-between-azure-sql-database-and-data-lake-storage-gen1"></a>Az Adatmásolás Azure SQL Database és Data Lake Storage Gen1 között
Az Apache Sqoop használatával Azure SQL Database és Data Lake Storage Gen1 közötti adatimportálást és-exportálást is használhat. További információ: [adatok másolása Data Lake Storage Gen1 és Azure SQL Database között a Sqoop használatával](data-lake-store-data-transfer-sql-sqoop.md).

## <a name="use-data-lake-storage-gen1-with-stream-analytics"></a>Data Lake Storage Gen1 használata a Stream Analytics
A Azure Stream Analytics használatával adatfolyamként tárolt adatokat az egyik kimenetként Data Lake Storage Gen1 használhatja. További információ: [stream-adatok Azure Storage Blobból a Data Lake Storage Gen1 a Azure stream Analytics használatával](data-lake-store-stream-analytics.md).

## <a name="use-data-lake-storage-gen1-with-power-bi"></a>Data Lake Storage Gen1 használata a Power BI
Az adatok elemzéséhez és megjelenítéséhez Power BI használhat Data Lake Storage Gen1-fiókból származó adatok importálásához. További információ: [adatok elemzése Data Lake Storage Gen1 Power bi használatával](data-lake-store-power-bi.md).

## <a name="use-data-lake-storage-gen1-with-data-catalog"></a>Data Lake Storage Gen1 használata a Data Catalog
Az adatok a Data Lake Storage Gen1ba való regisztrálásával az adatok a szervezeten belül felderíthetővé tehetik a Azure Data Catalog. További információ: [adatok regisztrálása a Azure Data Catalog Data Lake Storage Gen1ban](data-lake-store-with-data-catalog.md).

## <a name="use-data-lake-storage-gen1-with-sql-server-integration-services-ssis"></a>Data Lake Storage Gen1 használata SQL Server Integration Services (SSIS)
A SSIS Data Lake Storage Gen1 Csatlakozáskezelőt használva csatlakoztathat egy SSIS-csomagot a Data Lake Storage Gen1hoz. További információ: [Data Lake Storage Gen1 használata a SSIS](/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager).

## <a name="use-data-lake-storage-gen1-with-azure-synapse-analytics"></a>Data Lake Storage Gen1 használata az Azure szinapszis Analytics használatával
A Data Lake Storage Gen1ból származó adatok az Azure szinapszis Analyticsbe való betöltéséhez használhat albase-t. További információ: [Data Lake Storage Gen1 használata az Azure szinapszis Analytics használatával](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md).

## <a name="use-data-lake-storage-gen1-with-azure-event-hubs"></a>Data Lake Storage Gen1 használata az Azure-ban Event Hubs
A Azure Data Lake Storage Gen1 az Azure Event Hubs által fogadott adatok archiválására és rögzítésére használható. További információ: [Data Lake Storage Gen1 használata az Azure Event Hubs](data-lake-store-archive-eventhub-capture.md).

## <a name="see-also"></a>Lásd még
* [A Azure Data Lake Storage Gen1 áttekintése](data-lake-store-overview.md)
* [Ismerkedés a Data Lake Storage Gen1 portál használatával](data-lake-store-get-started-portal.md)
* [Ismerkedés a Data Lake Storage Gen1 a PowerShell használatával](data-lake-store-get-started-powershell.md)