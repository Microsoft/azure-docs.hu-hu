---
title: Keresés Azure Data Lake Storage Gen2 (előzetes verzió)
titleSuffix: Azure Cognitive Search
description: Megtudhatja, hogyan indexelheti a tartalmakat és a metaadatokat a Azure Data Lake Storage Gen2ban. Ez a szolgáltatás jelenleg nyilvános előzetes verzióban érhető el
manager: nitinme
author: markheff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/11/2020
ms.openlocfilehash: 51c32501015a16819ba9316272ed0d522e1083d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100545564"
---
# <a name="indexing-documents-in-azure-data-lake-storage-gen2"></a>Dokumentumok indexelése Azure Data Lake Storage Gen2

> [!IMPORTANT] 
> A Azure Data Lake Storage Gen2 támogatás jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók szolgáltatói szerződés nélkül érhetők el, és éles számítási feladatokhoz nem ajánlott. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Az előzetes verzióhoz való hozzáférést az [űrlap](https://aka.ms/azure-cognitive-search/indexer-preview)kitöltésével kérheti le. A [REST API 2020-06-30-es verziója – előzetes verzió](search-api-preview.md) és portál biztosítja ezt a funkciót. Jelenleg nincs .NET SDK-támogatás.


Azure Storage-fiók beállításakor lehetősége van a [hierarchikus névtér](../storage/blobs/data-lake-storage-namespace.md)engedélyezésére. Ez lehetővé teszi, hogy a fiókban lévő tartalmak gyűjteménye a címtárak és a beágyazott alkönyvtárak hierarchiájában legyen rendszerezve. A hierarchikus névtér engedélyezésével engedélyezheti a [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

Ez a cikk bemutatja, hogyan kezdheti meg a Azure Data Lake Storage Gen2-ban található dokumentumok indexelését.

## <a name="set-up-azure-data-lake-storage-gen2-indexer"></a>Azure Data Lake Storage Gen2 indexelő beállítása

A tartalom Data Lake Storage Gen2 való indexeléséhez néhány lépésre van szükség.

### <a name="step-1-sign-up-for-the-preview"></a>1. lépés: regisztráljon az előzetes verzióra

Regisztráljon a Data Lake Storage Gen2 indexelő előzetes verziójára az [űrlap](https://aka.ms/azure-cognitive-search/indexer-preview)kitöltésével. Az előzetes verzióra való elfogadás után egy megerősítő e-mailt fog kapni.

### <a name="step-2-follow-the-azure-blob-storage-indexing-setup-steps"></a>2. lépés: az Azure Blob Storage-indexelés telepítési lépéseinek követése

Miután megkapta a megerősítést, hogy az előzetes regisztráció sikeres volt, készen áll az indexelési folyamat létrehozására.

A tartalmat és a metaadatokat a [2020-06-30-es REST API-verzió](search-api-preview.md) vagy a portál használatával indexelheti Data Lake Storage Gen2ból. Jelenleg nincs .NET SDK-támogatás.

A Data Lake Storage Gen2 tartalmának indexelése azonos az Azure Blob Storage-ban található tartalom indexelésével. A Data Lake Storage Gen2 adatforrás, az index és az indexelő beállításának megismeréséhez tekintse meg a [dokumentumok Azure-beli blob Storage az azure Cognitive Search használatával történő indexelését ismertető témakört](search-howto-indexing-azure-blob-storage.md). A blob Storage-cikk a dokumentumok formátumait is tartalmazza, a blob metaadat-tulajdonságait kinyerve, növekményes indexeléssel és egyebekkel kapcsolatban. Ezek az információk a Data Lake Storage Gen2 esetében is megegyeznek.

## <a name="access-control"></a>Hozzáférés-vezérlés

Azure Data Lake Storage Gen2 olyan hozzáférés- [vezérlési modellt](../storage/blobs/data-lake-storage-access-control.md) valósít meg, amely támogatja az Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC) és a POSIX-hez hasonló hozzáférés-vezérlési listákat (ACL-eket). A Data Lake Storage Gen2 az Azure Cognitive Search nem nyeri ki az Azure-RBAC és az ACL-információkat a tartalomból. Ennek eredményeképpen ezek az információk nem kerülnek bele az Azure Cognitive Search indexbe.

Ha a hozzáférés-vezérlést az index minden dokumentuma esetében fontos fenntartani, akkor az alkalmazás fejlesztője a [biztonsági körülvágás](./search-security-trimming-for-azure-search.md)megvalósítására szolgál.

## <a name="change-detection"></a>Változás észlelése

A Data Lake Storage Gen2 indexelő támogatja a változások észlelését. Ez azt jelenti, hogy az indexelő futtatásakor a rendszer csak a blob időbélyegzője által meghatározott módosított blobokat indexeli `LastModified` .

> [!NOTE] 
> Data Lake Storage Gen2 engedélyezi a címtárak átnevezett nevét. Ha egy címtárat átneveznek, akkor az abban a könyvtárban lévő Blobok időbélyegei nem frissülnek. Ennek eredményeképpen az indexelő nem fogja újraindexelni ezeket a blobokat. Ha egy címtárban lévő Blobok újraindexelésére van szükség a címtár átnevezése után, mivel most már új URL-címekkel rendelkeznek, frissítenie kell a `LastModified` címtárban lévő összes blob időbélyegét, hogy az indexelő képes legyen újraindexelni őket egy későbbi futtatás során.