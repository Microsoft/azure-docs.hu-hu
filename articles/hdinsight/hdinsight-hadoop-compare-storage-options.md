---
title: A tárolási lehetőségek összehasonlítása az Azure HDInsight-fürtökkel való használathoz
description: Áttekintést nyújt a tárolási típusokról, valamint arról, hogyan működnek együtt az Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: b6dd0fd95280a65615d38ab11a2f9814f58586f5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98945845"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>A tárolási lehetőségek összehasonlítása az Azure HDInsight-fürtökkel való használathoz

HDInsight-fürtök létrehozásakor több különböző Azure Storage-szolgáltatás közül választhat:

* [Azure Blob Storage és HDInsight](./overview-azure-storage.md)
* [Azure Data Lake Storage Gen2 HDInsight](./overview-data-lake-storage-gen2.md)
* [Azure Data Lake Storage Gen1 HDInsight](./overview-data-lake-storage-gen1.md)

Ez a cikk áttekintést nyújt ezekről a tárolási típusokról és azok egyedi funkcióiról.

## <a name="storage-types-and-features"></a>Tárolási típusok és szolgáltatások

A következő táblázat összefoglalja a HDInsight különböző verziói által támogatott Azure Storage-szolgáltatásokat:

| Tárolási szolgáltatás | Fiók típusa | Névtér típusa | Támogatott szolgáltatások | Támogatott teljesítményszint | Támogatott hozzáférési szintek | HDInsight verziója | Fürt típusa |
|---|---|---|---|---|---|---|---|
|2. generációs Azure Data Lake Storage| Általános célú v2 | Hierarchikus (fájlrendszer) | Blob | Standard | Gyors elérésű, ritka elérésű Archívum | 3.6 + | Mind a Spark 2,1, mind a 2,2 kivételével|
|Azure Storage| Általános célú v2 | Objektum | Blob | Standard | Gyors elérésű, ritka elérésű Archívum | 3.6 + | Mind |
|Azure Storage| Általános célú v1 | Objektum | Blob | Standard | N/A | Mind | Mind |
|Azure Storage| Blob Storage * * | Objektum | BLOB letiltása | Standard | Gyors elérésű, ritka elérésű Archívum | Mind | Mind |
|1. generációs Azure Data Lake Storage| N/A | Hierarchikus (fájlrendszer) | N.A. | N.A. | N.A. | 3,6 csak | A HBase kivételével |
|Azure Storage| BLOB letiltása| Objektum | BLOB letiltása | Prémium | N/A| 3.6 + | Csak a gyorsított írási HBase|
|2. generációs Azure Data Lake Storage| BLOB letiltása| Hierarchikus (fájlrendszer) | BLOB letiltása | Prémium | N/A| 3.6 + | Csak a gyorsított írási HBase|

* * A HDInsight-fürtök esetében csak a másodlagos Storage-fiókok lehetnek BlobStorage, és az oldal Blobja nem támogatott tárolási lehetőség.

Az Azure Storage-fiókok típusairól további információt az [Azure Storage-fiók áttekintése](../storage/common/storage-account-overview.md) című témakörben talál.

További információ az Azure Storage hozzáférési szintjeiről [: Azure Blob Storage: Premium (előzetes verzió), gyakori, ritka elérésű és archív tárolási szintek](../storage/blobs/storage-blob-storage-tiers.md)

A fürtöket az elsődleges és választható másodlagos tárolók szolgáltatásainak kombinációi segítségével hozhatja létre. A következő táblázat összefoglalja a HDInsight által jelenleg támogatott fürtök tárolási konfigurációit:

| HDInsight verziója | Elsődleges tárterület | Másodlagos tároló | Támogatott |
|---|---|---|---|
| 3,6 & 4,0 | Általános célú v1, általános célú v2 | Általános célú v1, általános célú v2, BlobStorage (blokk Blobok) | Yes |
| 3,6 & 4,0 | Általános célú v1, általános célú v2 | Data Lake Storage Gen2 | No |
| 3,6 & 4,0 | Data Lake Storage Gen2 * | Data Lake Storage Gen2 | Yes |
| 3,6 & 4,0 | Data Lake Storage Gen2 * | Általános célú v1, általános célú v2, BlobStorage (blokk Blobok) | Yes |
| 3,6 & 4,0 | Data Lake Storage Gen2 | Data Lake Storage Gen1 | No |
| 3,6 | Data Lake Storage Gen1 | Data Lake Storage Gen1 | Yes |
| 3,6 | Data Lake Storage Gen1 | Általános célú v1, általános célú v2, BlobStorage (blokk Blobok) | Yes |
| 3,6 | Data Lake Storage Gen1 | Data Lake Storage Gen2 | No |
| 4,0 | Data Lake Storage Gen1 | Bármelyik | No |
| 4,0 | Általános célú v1, általános célú v2 | Data Lake Storage Gen1 | No |

* = Ez lehet egy vagy több Data Lake Storage Gen2, feltéve, hogy minden beállítás ugyanazt a felügyelt identitást használja a fürt eléréséhez.

> [!NOTE]
> Data Lake Storage Gen2 elsődleges tároló nem támogatott a Spark 2,1 vagy 2,2 fürtök esetében.

## <a name="data-replication"></a>Adatreplikáció

Az Azure HDInsight nem tárolja az ügyféladatokat. A fürt elsődleges tárolási módja a társított Storage-fiók. A fürtöt egy meglévő Storage-fiókhoz csatolhatja, vagy létrehozhat egy új Storage-fiókot a fürt létrehozási folyamata során. Új fiók létrehozásakor a rendszer helyileg redundáns tárolási (LRS-) fiókként hozza létre, és kielégíti a régión belüli adattárolási követelményeket, beleértve a [megbízhatósági központban](https://azuredatacentermap.azurewebsites.net)megadott feltételeket is.

Ellenőrizheti, hogy a HDInsight megfelelően van-e konfigurálva egyetlen régióban tárolt adattároláshoz, és gondoskodjon arról, hogy a HDInsight társított Storage-fiók LRS vagy egy másik tárolási beállítás legyen megemlítve a [megbízhatósági központban](https://azuredatacentermap.azurewebsites.net).
 
## <a name="next-steps"></a>Következő lépések

* [Az Azure Storage áttekintése a HDInsight-ben](./overview-azure-storage.md)
* [A HDInsight Azure Data Lake Storage Gen1 áttekintése](./overview-data-lake-storage-gen1.md)
* [A HDInsight Azure Data Lake Storage Gen2 áttekintése](./overview-data-lake-storage-gen2.md)
* [Az Azure Data Lake Storage Gen2 bemutatása](../storage/blobs/data-lake-storage-introduction.md)
* [A Microsoft Azure Storage bemutatása](../storage/common/storage-introduction.md)
