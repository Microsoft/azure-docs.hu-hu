---
title: Az Azure Data Lake Storage Gen2t támogató Azure-szolgáltatások | Microsoft Docs
description: Ismerje meg, hogy mely Azure-szolgáltatások integrálva vannak Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: caa0390dfd3a98e6537a7a60a2895461e2efd52a
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879864"
---
# <a name="azure-services-that-support-azure-data-lake-storage-gen2"></a>Az Azure Data Lake Storage Gen2t támogató Azure-szolgáltatások

Az Azure-szolgáltatások az adatok betöltésére, az elemzések elvégzésére és a vizuális ábrázolások létrehozására használhatók. Ez a cikk felsorolja a támogatott Azure-szolgáltatásokat, felfedi a támogatási szintet, és olyan cikkekre mutató hivatkozásokat tartalmaz, amelyek segítenek ezeknek a szolgáltatásoknak a Azure Data Lake Storage Gen2 való használatához.

## <a name="supported-azure-services"></a>Támogatott Azure-szolgáltatások

Ez a táblázat felsorolja a Azure Data Lake Storage Gen2 használható Azure-szolgáltatásokat. A táblázatokban megjelenő elemek idővel változnak, ahogy a támogatás továbbra is bővül.

> [!NOTE]
> A támogatási szint csak arra vonatkozik, hogy a szolgáltatás milyen módon támogatott a Data Lake Storage Gen 2 használatával.

|Azure-szolgáltatás |Támogatási szint |Azure AD |Megosztott kulcsos| Kapcsolódó cikkek |
|---------------|-------------------|---|---|---|
|Azure Data Factory|Általánosan elérhető|Igen|Igen|[Betöltés az Azure Data Lake Storage Gen2ba Azure Data Factory](../../data-factory/load-azure-data-lake-storage-gen2.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Databricks|Általánosan elérhető|Igen|Igen|[Használat az Azure Databricksszel](https://docs.azuredatabricks.net/data/data-sources/azure/azure-datalake-gen2.html) <br> [Gyors útmutató: Azure Data Lake Storage Gen2ban lévő adatelemzés Azure Databricks használatával](data-lake-storage-quickstart-create-databricks-account.md) <br>[Oktatóanyag: Adatok kinyerése, átalakítása és betöltése az Azure Databricks használatával](/azure/databricks/scenarios/databricks-extract-load-sql-data-warehouse) <br>[Oktatóanyag: Data Lake Storage Gen2-adatelérés Azure Databricks a Spark használatával](data-lake-storage-use-databricks-spark.md)|
|Azure Event Hub|Általánosan elérhető|Nem|Igen|[Események rögzítése Azure-Event Hubs az Azure-ban Blob Storage vagy Azure Data Lake Storage](../../event-hubs/event-hubs-capture-overview.md)|
|Azure Event Grid|Általánosan elérhető|Igen|Igen|[Oktatóanyag: a Databricks-különbözeti tábla frissítéséhez a adattó rögzítési mintájának megvalósítása](data-lake-storage-events.md)|
|Azure Logic Apps|Általánosan elérhető|Nem|Igen|[Áttekintés – mi az Azure Logic Apps?](../../logic-apps/logic-apps-overview.md)|
|Azure Machine Learning|Általánosan elérhető|Igen|Igen|[Az Azure Storage-szolgáltatásokban tárolt adathozzáférés](../../machine-learning/how-to-access-data.md)|
|Azure Stream Analytics|Általánosan elérhető|Igen|Igen|[Útmutató: Stream Analytics-feladat létrehozása az Azure Portal használatával](../../stream-analytics/stream-analytics-quick-create-portal.md) <br> [Kimenő Azure Data Lake Gen2](../../stream-analytics/stream-analytics-define-outputs.md)|
|Data Box|Általánosan elérhető|Nem|Igen|[A helyszíni HDFS-tárolóból az Azure Storage-ba történő Migrálás Azure Data Box használata](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|
|HDInsight |Általánosan elérhető|Igen|Igen|[Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtökkel](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br>[A HDFS CLI használata Data Lake Storage Gen2](data-lake-storage-use-hdfs-data-lake-storage.md) <br>[Oktatóanyag: adatok kinyerése, átalakítása és betöltése az Azure HDInsight Apache Hive használatával](data-lake-storage-tutorial-extract-transform-load-hive.md)|
|IoT Hub |Általánosan elérhető|Igen|Igen|[Eszközről a felhőbe irányuló üzenetek küldése különböző végpontokra IoT Hub üzenet-útválasztás használatával](../../iot-hub/iot-hub-devguide-messages-d2c.md)|
|Power BI|Általánosan elérhető|Igen|Igen|[Data Lake Storage Gen2 adatai elemzése a Power BI használatával](/power-query/connectors/datalakestorage)|
|Azure Synapse Analytics (korábban SQL Data Warehouse)|Általánosan elérhető|Igen|Igen|[A Storage-fiókban tárolt adatelemzés](../../synapse-analytics/get-started-analyze-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Az SQL Server Integration Servicest (SSIS)|Általánosan elérhető|Igen|Igen|[Azure Storage-kapcsolatkezelő](/sql/integration-services/connection-manager/azure-storage-connection-manager)|
|Azure Data Explorer|Általánosan elérhető|Igen|Igen|[Azure Data Lake lekérdezése az Azure-Adatkezelő használatával](/azure/data-explorer/data-lake-query-data)|
|Azure Cognitive Search|Előnézet|Igen|Igen|[Azure Data Lake Storage Gen2 dokumentumok indexelése és keresése (előzetes verzió)](../../search/search-howto-index-azure-data-lake-storage.md)|
|Azure Content Delivery Network|Még nem támogatott|Nem alkalmazható|Nem alkalmazható|[Azure Data Lake Storage Gen2 dokumentumok indexelése és keresése (előzetes verzió)](../../cdn/cdn-overview.md)|
|Azure SQL Database|Még nem támogatott|Nem alkalmazható|Nem alkalmazható|[Mi az Azure SQL Database?](../../azure-sql/database/sql-database-paas-overview.md)|

## <a name="see-also"></a>Lásd még

- [Ismert problémák a Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [A blob Storage funkciói a Azure Data Lake Storage Gen2ban érhetők el](data-lake-storage-supported-blob-storage-features.md)
- [A Azure Data Lake Storage Gen2t támogató nyílt forráskódú platformok](data-lake-storage-supported-open-source-platforms.md)
- [Több protokollos hozzáférés Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)