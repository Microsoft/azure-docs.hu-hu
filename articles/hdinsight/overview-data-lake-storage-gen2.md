---
title: A HDInsight Azure Data Lake Storage Gen2 áttekintése
description: A HDInsight Data Lake Storage Gen2 áttekintése.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: e69838f18efc08d0f64dd9ea904f502617073a8f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98938865"
---
# <a name="azure-data-lake-storage-gen2-overview-in-hdinsight"></a>A HDInsight Azure Data Lake Storage Gen2 áttekintése

A Azure Data Lake Storage Gen2 a Azure Data Lake Storage Gen1 alapvető funkcióit veszi át, és integrálja őket az Azure Blob Storage-ba. Ezek a funkciók olyan fájlrendszert tartalmaznak, amely kompatibilis a Hadoop, a Azure Active Directory (Azure AD) és a POSIX-alapú hozzáférés-vezérlési listákkal (ACL-ekkel). Ez a kombináció lehetővé teszi, hogy kihasználhassa a Azure Data Lake Storage Gen1 teljesítményét. Emellett a blob Storage réteg-és adatéletciklus-kezelését is használja.

További információ a Azure Data Lake Storage Gen2ről: a [Azure Data Lake Storage Gen2 bemutatása](../storage/blobs/data-lake-storage-introduction.md).

## <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>A Azure Data Lake Storage Gen2 alapvető funkciói

* A **Hadoop kompatibilis hozzáférés:** A Azure Data Lake Storage Gen2ban ugyanúgy kezelheti és érheti el az adatelérést, mint egy Hadoop-elosztott fájlrendszer (HDFS). Az Azure Blob fájlrendszer (ABFS) illesztőprogramja minden Apache Hadoop környezetben elérhető, beleértve az Azure HDInsight és a Azure Databricks. A ABFS használatával férhet hozzá a Data Lake Storage Gen2 tárolt adatszolgáltatásokhoz.

* **POSIX-engedélyek felülbírálása:** A Data Lake Gen2 biztonsági modellje támogatja az ACL és a POSIX engedélyeket, valamint a Data Lake Storage Gen2ra vonatkozó további részletességet. A beállítások a felügyeleti eszközök vagy keretrendszerek, például a Apache Hive és a Apache Spark használatával konfigurálhatók.

* **Költséghatékonyság:** A Data Lake Storage Gen2 alacsony költséghatékonyságú tárolási kapacitást és tranzakciókat biztosít. Az Azure Blob Storage életciklusa csökkenti a költségeket a számlázási díjaknak az életcikluson keresztüli továbbításával.

* **A blob Storage-eszközökkel,-keretrendszerekkel és-alkalmazásokkal való kompatibilitás:** Data Lake Storage Gen2 továbbra is együttműködik számos eszközzel, keretrendszert és alkalmazást a blob Storage-hoz.

* **Optimalizált illesztőprogram:** A ABFS-illesztőprogram kifejezetten big data elemzésekhez van optimalizálva. A megfelelő REST API-k az elosztott fájlrendszer (DFS) végpontján (dfs.core.windows.net) vannak felszínben.

## <a name="whats-new-for-azure-data-lake-storage-gen-2"></a>A 2. generációs Azure Data Lake Storage újdonságai

### <a name="managed-identities-for-secure-file-access"></a>Felügyelt identitások biztonságos fájlokhoz való hozzáféréshez

Az Azure HDInsight felügyelt identitásokat használ a Azure Data Lake Storage Gen2 lévő fájlokhoz való hozzáférés biztonságossá tételéhez. A felügyelt identitások olyan Azure Active Directory funkciói, amelyek automatikusan felügyelt hitelesítő adatokkal rendelkező Azure-szolgáltatásokat biztosítanak. Ezek a hitelesítő adatok a Active Directory hitelesítést támogató bármely szolgáltatás hitelesítésére használhatók. A felügyelt identitások használatához nem szükséges a hitelesítő adatok tárolása a kódban vagy a konfigurációs fájlokban.

További információ: [felügyelt identitások az Azure-erőforrásokhoz](../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-blob-file-system-driver"></a>Azure Blob fájlrendszer-illesztőprogram

Apache Hadoop alkalmazások natívan várnak a helyi lemezes tárolóból származó adatok olvasására és írására. A Hadoop fájlrendszer-illesztőprogram, például a ABFS lehetővé teszi, hogy a Hadoop alkalmazások működjenek a felhőalapú tárolással. Úgy működik, hogy a hagyományos Hadoop-fájlrendszeri műveleteket emulálja. Az illesztőprogram átalakítja az alkalmazásból kapott parancsokat olyan műveletekre, amelyeket az aktuális felhőalapú tárolási platform megértett.

Korábban a Hadoop fájlrendszer-illesztőprogram az összes fájlrendszerbeli műveletet átalakította az Azure Storage-REST API a hívások az ügyféloldali oldalon. Ezután meghívja a REST API. Ez az ügyféloldali átalakítás azonban több REST API hívását eredményezte egyetlen fájlrendszer-művelethez, például egy fájl átnevezéséhez. A ABFS áthelyezte a Hadoop-fájlrendszer logikáját az ügyfél oldaláról a kiszolgáló oldalára. A Azure Data Lake Storage Gen2 API mostantól párhuzamosan fut a blob API-val. Ez a Migrálás javítja a teljesítményt, mivel a Hadoop-fájlrendszerrel kapcsolatos gyakori műveletek egy REST API hívással hajthatók végre.

További információkért lásd [Az Azure Blob fájlrendszer-illesztőprogramot (ABFS): a Hadoop dedikált Azure Storage-illesztőprogramját](../storage/blobs/data-lake-storage-abfs-driver.md).

### <a name="uri-scheme-for-azure-data-lake-storage-gen-2"></a>URI-séma a Azure Data Lake Storage Gen 2

Azure Data Lake Storage Gen2 egy új URI-sémát használ az Azure Storage-ban lévő fájlok eléréséhez a HDInsight-ből:

`abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

Az URI-séma SSL-titkosítású hozzáférést biztosít.

`<FILE_SYSTEM_NAME>` a fájlrendszer Data Lake Storage Gen2ének elérési útját azonosítja.

`<ACCOUNT_NAME>` Az Azure Storage-fiók nevét azonosítja. Szükség van a teljes tartománynévre (FQDN-re).

`<PATH>` a fájl vagy a könyvtár HDFS elérési útja.

Ha `<FILE_SYSTEM_NAME>` a és a `<ACCOUNT_NAME>` nem értéket adja meg, a rendszer az alapértelmezett fájlrendszert használja. Az alapértelmezett fájlrendszer fájljaihoz használjon relatív elérési útvonalat vagy abszolút elérési utat. Például a `hadoop-mapreduce-examples.jar` HDInsight-fürtökhöz tartozó fájl a következő elérési utak egyikének használatával hívható meg:

```
abfs://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfs:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]
> A fájlnév a 2,1-es `hadoop-examples.jar` és a 1,6-es HDInsight-verzióban található. Ha a HDInsight-en kívül található fájlokkal dolgozik, a legtöbb segédprogram nem ismeri fel a ABFS formátumát, hanem egy alapszintű elérésiút-formátumot vár, például: `example/jars/hadoop-mapreduce-examples.jar` .

További információ: [a Azure Data Lake Storage GEN2 URI használata](../storage/blobs/data-lake-storage-introduction-abfs-uri.md).

## <a name="next-steps"></a>Következő lépések

* [Az Azure Data Lake Storage Gen2 bemutatása](../storage/blobs/data-lake-storage-introduction.md)
* [A Microsoft Azure Storage bemutatása](../storage/common/storage-introduction.md)
* [Az Azure Data Lake Storage Gen1 áttekintése](./overview-data-lake-storage-gen1.md)