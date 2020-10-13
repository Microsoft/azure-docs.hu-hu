---
title: Ismert problémák a Azure Data Lake Storage Gen2kal | Microsoft Docs
description: Ismerje meg Azure Data Lake Storage Gen2 korlátozásait és ismert problémáit.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 08/26/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: c1e9e3d63e8a4f7fe461e2d33603da91d3d9bec6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91441777"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Ismert problémák a Azure Data Lake Storage Gen2

Ez a cikk a Azure Data Lake Storage Gen2 korlátozásait és ismert problémáit ismerteti.

## <a name="supported-blob-storage-features"></a>Támogatott Blob Storage-funkciók

A blob Storage-funkciók egyre több funkciója működik olyan fiókokkal, amelyek hierarchikus névtérrel rendelkeznek. A teljes listát lásd: [Blob Storage Azure Data Lake Storage Gen2 elérhető szolgáltatásai](data-lake-storage-supported-blob-storage-features.md).

## <a name="supported-azure-service-integrations"></a>Támogatott Azure-szolgáltatások integrációi

Azure Data Lake Storage Gen2 számos Azure-szolgáltatást támogat, amelyek segítségével adatok betöltésére, elemzések végrehajtására és vizuális ábrázolások létrehozására használható. A támogatott Azure-szolgáltatások listáját a [Azure Data Lake Storage Gen2t támogató Azure-szolgáltatások](data-lake-storage-supported-azure-services.md)című témakörben tekintheti meg.

Lásd: [Azure Data Lake Storage Gen2 támogató Azure-szolgáltatások](data-lake-storage-supported-azure-services.md).

## <a name="supported-open-source-platforms"></a>Támogatott nyílt forráskódú platformok

Számos nyílt forráskódú platform támogatja a Data Lake Storage Gen2. A teljes listát lásd: a [Azure Data Lake Storage Gen2t támogató nyílt forráskódú platformok](data-lake-storage-supported-open-source-platforms.md).

Lásd: [a Azure Data Lake Storage Gen2t támogató nyílt forráskódú platformok](data-lake-storage-supported-open-source-platforms.md).

## <a name="blob-storage-apis"></a>BLOB Storage API-k

A blob API-k és a Data Lake Storage Gen2 API-k ugyanazon az adatközponton működhetnek.

Ez a szakasz a blob API-k és a Data Lake Storage Gen2 API-k használatával kapcsolatos problémákat és korlátozásokat ismerteti ugyanazon az adatközponton.

* A blob API-k és a Data Lake Storage API-k nem használhatók a fájlok ugyanazon példányára való íráshoz. Ha Data Lake Storage Gen2 API-k használatával ír fájlba egy fájlt, a rendszer nem fogja látni a fájl blokkokat a [letiltási lista](https://docs.microsoft.com/rest/api/storageservices/get-block-list) blob API-jának meghívásához. A fájlokat Data Lake Storage Gen2 API-k vagy blob API-k használatával írhatja felül. Ez nem befolyásolja a fájl tulajdonságait.

* Ha a [lista Blobok](https://docs.microsoft.com/rest/api/storageservices/list-blobs) műveletet határolójel megadása nélkül használja, az eredmények a címtárakat és a blobokat is tartalmazzák. Ha elválasztót választ, csak egy perjelet ( `/` ) használjon. Ez az egyetlen támogatott elválasztó karakter.

* Ha a [blob törlése](https://docs.microsoft.com/rest/api/storageservices/delete-blob) API-t használja egy könyvtár törléséhez, akkor a rendszer csak akkor törli a könyvtárat, ha üres. Ez azt jelenti, hogy a blob API rekurzív törlése nem végezhető el.

Ezek a blob REST API-k nem támogatottak:

* [BLOB (oldal) elhelyezése](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Oldal elhelyezése](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [Oldalak tartományának beolvasása](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [Növekményes másolási blob](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [Oldal elhelyezése az URL-címről](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [Blokk hozzáfűzése URL-címről](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

A nem felügyelt virtuálisgép-lemezek nem támogatottak olyan fiókoknál, amelyek hierarchikus névtérrel rendelkeznek. Ha egy hierarchikus névteret szeretne engedélyezni egy Storage-fiókban, helyezze a nem felügyelt virtuális gépek lemezeit olyan Storage-fiókba, amelyben nincs engedélyezve a hierarchikus névtér funkció.

<a id="api-scope-data-lake-client-library"></a>

## <a name="support-for-setting-access-control-lists-acls-recursively"></a>Hozzáférés-vezérlési listák (ACL-ek) rekurzív beállításának támogatása

Az ACL-változások rekurzív módon történő alkalmazása a szülő könyvtárból a alárendelt elemekre [nyilvános előzetes](recursive-access-control-lists.md)verzióban érhető el. Ennek a funkciónak a jelenlegi kiadásában a PowerShell, a .NET SDK és a Python SDK használatával is alkalmazhatja az ACL-módosításokat. A támogatás még nem érhető el a Java SDK, az Azure CLI, a Azure Portal vagy a Azure Storage Explorer számára.

<a id="known-issues-tools"></a>

## <a name="azcopy"></a>AzCopy

Csak a AzCopy legújabb verzióját használja ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)).A AzCopy korábbi verziói (például a AzCopy v 8.1) nem támogatottak.

<a id="storage-explorer"></a>

## <a name="azure-storage-explorer"></a>Azure Storage Explorer

Csak a vagy újabb verziókat használja  `1.6.0`   .

<a id="explorer-in-portal"></a>

## <a name="storage-explorer-in-the-azure-portal"></a>Storage Explorer a Azure Portal

Az ACL-ek még nem támogatottak.

<a id="third-party-apps"></a>

## <a name="thirdpartyapplications"></a>Harmadik féltől származó alkalmazások

A REST API-kat használó harmadik féltől származó alkalmazások továbbra is működni fognak, ha a blob API-kat hívó Data Lake Storage Gen2 alkalmazásokkal valószínűleg működni fognak.

## <a name="access-control-lists-acl-and-anonymous-read-access"></a>Hozzáférés-vezérlési listák (ACL) és névtelen olvasási hozzáférés

Ha a tárolóhoz [Névtelen olvasási hozzáférés](storage-manage-access-to-resources.md) van megadva, akkor az ACL-ek nincsenek hatással a tárolóra vagy a tárolóban lévő fájlokra.

## <a name="premium-performance-blockblobstorage-storage-accounts"></a>Prémium teljesítményű BlockBlobStorage Storage-fiókok

### <a name="diagnostic-logs"></a>Diagnosztikai naplók

A diagnosztikai naplók még nem engedélyezhetők a Azure Portal használatával. A PowerShell használatával is engedélyezheti őket. Példa:

```powershell
#To login
Connect-AzAccount

#Set default block blob storage account.
Set-AzCurrentStorageAccount -Name premiumGen2Account -ResourceGroupName PremiumGen2Group

#Enable logging
Set-AzStorageServiceLoggingProperty -ServiceType Blob -LoggingOperations read,write,delete -RetentionDays 14
```

A megőrzési napok beállítása még nem támogatott, de a naplókat manuálisan is törölheti bármilyen támogatott eszközzel, például a Azure Storage Explorer, a REST vagy az SDK használatával.

### <a name="lifecycle-management-policies"></a>Életciklus-kezelési szabályzatok

- Az életciklus-kezelési házirendek csak az általános célú v2-fiókok esetében támogatottak. A Premium BlockBlobStorage Storage-fiókokban még nem támogatottak.
- Az adatok nem helyezhetők át a prémium szintről az alacsonyabb szintekre.


### <a name="hdinsight-support"></a>HDInsight-támogatás

N HDInsight-fürt létrehozásakor még nem választhat olyan BlockBlobStorage-fiókot, amelyen engedélyezve van a hierarchikus névtér funkció. A fiókot azonban a létrehozása után is csatlakoztathatja a fürthöz.

### <a name="dremio-support"></a>Dremio-támogatás

A Dremio még nem csatlakozik olyan BlockBlobStorage-fiókhoz, amelyen engedélyezve van a hierarchikus névtér funkció. 

## <a name="windows-azure-storage-blob-wasb-driver-unsupported-with-data-lake-storage-gen2"></a>Windows Azure Storage Blob (WASB) illesztőprogram (nem támogatott a Data Lake Storage Gen2)

Jelenleg a WASB-illesztőprogram, amely csak a blob API-val való együttműködésre lett tervezve, problémákba ütközik néhány gyakori forgatókönyvben. Pontosabban, ha az ügyfél egy hierarchikus névteret használó Storage-fiók. A multi-Protocol hozzáférés Data Lake Storage nem fogja enyhíteni ezeket a problémákat. 

Egyelőre (és a várható jövőbeli jövőben) az ügyfelek nem támogatják a WASB-illesztőprogram ügyfélként való használatát egy hierarchikus névteret használó Storage-fiókhoz. Ehelyett azt javasoljuk, hogy az [Azure Blob File System (ABFS)](data-lake-storage-abfs-driver.md) illesztőprogramját használja a Hadoop-környezetben. Ha olyan helyszíni Hadoop-környezetből próbál áttelepíteni, amely a Hadoop ág-3-nál korábbi verziónál régebbi, akkor nyisson meg egy Azure-támogatási jegyet, hogy kapcsolatba lépjen Önnel az Ön és a szervezete számára elérhető megfelelő elérési úton.
