---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/19/2021
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 0914cf9515930e23e4134181ffe8332e36eacffe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98612905"
---
Az Azure Blob Storage a Microsoft felhőalapú objektumtárolási megoldása. A Blob Storage nagy mennyiségű strukturálatlan adat tárolására van optimalizálva. A strukturálatlan adatok olyan adatok, amelyek nem felelnek meg egy adott adatmodellnek vagy definíciónak, például szöveges vagy bináris adatoknak.

## <a name="about-blob-storage"></a>Az Azure Blob Storage névjegye

A Blob Storage használati lehetőségei:

* Képek vagy dokumentumok közvetlen szolgáltatása a böngészők számára.
* Fájlok tárolása megosztott hozzáféréshez.
* Video- és hangtartalom streamelése.
* Írás naplófájlokba.
* Adattárolás biztonsági mentésekhez és helyreállításhoz, vészhelyreállításhoz és archiváláshoz.
* Adattárolás helyszíni vagy az Azure-ban üzemeltetett szolgáltatásban való elemzéshez.

A felhasználók vagy ügyfélalkalmazások a blob Storage-ban lévő objektumokat HTTP/HTTPS használatával érhetik el a világ bármely pontján. A blob Storage-ban lévő objektumok az [Azure storage REST API](/rest/api/storageservices/blob-service-rest-api), az [Azure PowerShell](/powershell/module/az.storage), az [Azure CLI](/cli/azure/storage)vagy egy Azure Storage ügyféloldali kódtár használatával érhetők el. Az ügyféloldali kódtárak különböző nyelvekhez érhetők el, beleértve a következőket:

* [.NET](/dotnet/api/overview/azure/storage)
* [Java](/java/api/overview/azure/storage)
* [Node.js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage)
* [Python](../articles/storage/blobs/storage-quickstart-blobs-python.md)
* [Ugrás](https://github.com/azure/azure-storage-blob-go/)
* [PHP](https://azure.github.io/azure-storage-php/)
* [Ruby](https://azure.github.io/azure-storage-ruby)

## <a name="about-azure-data-lake-storage-gen2"></a>Tudnivalók Azure Data Lake Storage Gen2

A Blob Storage támogatja az Azure Data Lake Storage Gen2-t, amely a Microsoft felhőbeli, vállalati adatok big data koncepción alapuló elemzési megoldása. Azure Data Lake Storage Gen2 hierarchikus fájlrendszert biztosít, valamint a blob Storage előnyeit, beleértve a következőket:

* Alacsony árú, többrétegű tárolás
* Magas rendelkezésre állás
* Erős konzisztencia
* Vész-helyreállítási lehetőségek

További információ a Data Lake Storage Gen2ről: a [Azure Data Lake Storage Gen2 bemutatása](../articles/storage/blobs/data-lake-storage-introduction.md).