---
title: BLOB Storage-alapú SSIS-összekötők áthelyezése a csoportos adatelemzési folyamattal
description: Megtudhatja, hogyan helyezheti át az Azure Blob Storageba vagy az Azure-ba való adatátvitelt az Azure SQL Server Integration Services Feature Pack használatával.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 9572d612e7ee8e2fd72850ba14447e8449f0f371
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "93322008"
---
# <a name="move-data-to-or-from-azure-blob-storage-using-ssis-connectors"></a>Adatok áthelyezése az Azure Blob Storageba vagy az SSIS-összekötők használatával
Az [azure SQL Server Integration Services Feature Pack](/sql/integration-services/azure-feature-pack-for-integration-services-ssis) az Azure-hoz való kapcsolódáshoz, az Azure-beli és a helyszíni adatforrások közötti adatátvitelhez, valamint az Azure-ban tárolt adatok feldolgozásához biztosít összetevőket.

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

Miután az ügyfelek áthelyezték a helyszíni adatait a felhőbe, hozzáférhetnek az adatokhoz bármely Azure-szolgáltatásból, hogy kihasználják az Azure-technológiák teljes funkcionalitását. Előfordulhat, hogy az ilyen típusú adathalmazok például Azure Machine Learning vagy egy HDInsight-fürtön használhatók.

Az Azure-erőforrások használatára vonatkozó példák az [SQL](sql-walkthrough.md) -és a [HDInsight](hive-walkthrough.md) -forgatókönyvekben találhatók.

A hibrid adatintegrációs forgatókönyvekben a SSIS használatával közös üzleti igényeket biztosító kanonikus forgatókönyvek megvitatására a további tudnivalókat lásd: [SQL Server Integration Services Feature Pack for Azure](https://techcommunity.microsoft.com/t5/sql-server-integration-services/doing-more-with-sql-server-integration-services-feature-pack-for/ba-p/388238) blog.

> [!NOTE]
> Az Azure Blob Storage teljes körű bevezetéséhez tekintse meg az [Azure Blob alapjai](../../storage/blobs/storage-quickstart-blobs-dotnet.md) és az [Azure Blob Service](/rest/api/storageservices/Blob-Service-Concepts)témakört.
> 
> 

## <a name="prerequisites"></a>Előfeltételek
A cikkben ismertetett feladatok végrehajtásához rendelkeznie kell egy Azure-előfizetéssel és egy beállított Azure Storage-fiókkal. Az adatok feltöltéséhez és letöltéséhez az Azure Storage-fiók neve és a fiók kulcsa szükséges.

* **Azure-előfizetés** beállításához tekintse meg az [ingyenes egy hónapos próbaverziót](https://azure.microsoft.com/pricing/free-trial/).
* A **Storage-fiók** létrehozásával, valamint a fiók-és a kulcsfontosságú információk beszerzésével kapcsolatos útmutatásért lásd: [Tudnivalók az Azure Storage-fiókokról](../../storage/common/storage-account-create.md).

Az SSIS- **Összekötők** használatához le kell töltenie a következőket:

* **SQL Server 2014 vagy 2016 standard (vagy újabb)**: a telepítés magában foglalja a SQL Server Integration Services.
* **Microsoft SQL Server 2014 vagy 2016 Integration Services szolgáltatáscsomag az Azure**-hoz: ezek az összekötők a [SQL Server 2014 Integration services](https://www.microsoft.com/download/details.aspx?id=47366) és az [SQL Server 2016 Integration Services](https://www.microsoft.com/download/details.aspx?id=49492) oldalain tölthetők le.

> [!NOTE]
> A SSIS telepítve van SQL Server, de az expressz verzióban nem szerepel. További információ a SQL Server különböző kiadásaiban található alkalmazásokról: [SQL Server Editions](https://www.microsoft.com/en-us/server-cloud/products/sql-server-editions/)
> 
> 

A SSIS kapcsolatos tananyagért lásd: [Hands on Training for SSIS](https://www.microsoft.com/sql-server/training-certification)

Az egyszerű kinyerési, átalakítási és betöltési (ETL) csomagok létrehozásával kapcsolatban a SISS használatával történő használatáról további információt a [SSIS oktatóanyag: egyszerű ETL-csomag létrehozása](/sql/integration-services/ssis-how-to-create-an-etl-package)című témakörben talál.

## <a name="download-nyc-taxi-dataset"></a>A New York-i taxi-adatkészlet letöltése
Az itt leírt példa egy nyilvánosan elérhető adathalmazt használ – a [New York-i taxis](https://www.andresmh.com/nyctaxitrips/) adatkészletet. Az adatkészlet körülbelül 173 000 000 taxis utazást tartalmaz a New York-i évben, 2013-ben. Kétféle adat létezik: a Trip adatai és a díjszabási adatok. Mivel minden hónapban van egy fájl, a rendszer 24 fájlt tartalmaz, amelyek mindegyike körülbelül 2 GB-ot tömörít.

## <a name="upload-data-to-azure-blob-storage"></a>Adatok feltöltése az Azure Blob Storage-ba
Ha a helyszíni rendszerről az Azure Blob Storage-ba helyezi át az adatok áthelyezését a SSIS szolgáltatással, az [**Azure Blob feltöltési feladatának**](/sql/integration-services/control-flow/azure-blob-upload-task)egy példányát használjuk, amely itt látható:

![az adattudomány konfigurálása – virtuális gép](./media/move-data-to-azure-blob-using-ssis/ssis-azure-blob-upload-task.png)

A tevékenység által használt paraméterek a következők:

| Mező | Leírás |
| --- | --- |
| **AzureStorageConnection** |Megad egy meglévő Azure Storage-Csatlakozáskezelőt, vagy létrehoz egy újat, amely egy olyan Azure Storage-fiókra hivatkozik, amely a blob-fájlok tárolási helyét mutat. |
| **BlobContainer** |Annak a blob-tárolónak a nevét adja meg, amely blobként tárolja a feltöltött fájlokat. |
| **BlobDirectory** |Azt a blob könyvtárat adja meg, amelyben a feltöltött fájl blokk blobként van tárolva. A blob könyvtár egy virtuális hierarchikus struktúra. Ha a blob már létezik, az IT IA lecserélve. |
| **LocalDirectory** |A feltölteni kívánt fájlokat tartalmazó helyi könyvtárat adja meg. |
| **FileName** |Megadja azt a nevet, amellyel a megadott nevű fájl kiválasztható. Például a MySheet \* . xls olyan \* fájlokat tartalmaz, mint a MySheet001.xls és a MySheetABC.xlsx |
| **TimeRangeFrom/TimeRangeTo** |Megadja az időtartomány szűrőt. A *TimeRangeFrom* után módosított fájlok és a *TimeRangeTo* előtt is szerepelnek. |

> [!NOTE]
> A **AzureStorageConnection** hitelesítő adatait helyesnek kell lennie, és a **BlobContainer** az átvitel megkísérlése előtt léteznie kell.
> 
> 

## <a name="download-data-from-azure-blob-storage"></a>Adatok letöltése az Azure Blob Storage-ból
Az Azure Blob Storage-ból a helyszíni SSIS-be történő adatletöltéshez használja az [Azure Blob letöltési feladatának](/sql/integration-services/control-flow/azure-blob-download-task)egy példányát.

## <a name="more-advanced-ssis-azure-scenarios"></a>Fejlettebb SSIS-Azure forgatókönyvek
A SSIS szolgáltatás csomagja lehetővé teszi, hogy a csomagolási feladatok együttesen kezeljék az összetettebb folyamatokat. A blob-adatokat például közvetlenül egy HDInsight-fürtbe lehet etetni, amelynek kimenetét vissza lehet tölteni egy blobba, majd a helyszíni tárolóba. A SSIS további SSIS-összekötők használatával futtathat kaptár-és Pig-feladatokat egy HDInsight-fürtön:

* Ha SSIS-alapú Azure HDInsight-fürtön szeretné futtatni a kaptár-parancsfájlt, használja az [Azure HDInsight kaptár feladatot](/sql/integration-services/control-flow/azure-hdinsight-hive-task).
* Ha SSIS-alapú Azure HDInsight-fürtön szeretné futtatni a Pig-szkriptet, használja az [Azure HDInsight Pig feladatot](/sql/integration-services/control-flow/azure-hdinsight-pig-task).