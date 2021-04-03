---
title: Microsoft Azure Data Box blob Storage-követelmények | Microsoft Docs
description: Ismerje meg az API-k, SDK-k és a Azure Data Box blob Storage-hoz készült ügyféloldali kódtárak támogatott verzióit
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 10/05/2020
ms.author: alkohli
ms.openlocfilehash: ac5f2de383066d6ee399dac3b0ad8c365b2e72bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91744098"
---
# <a name="azure-data-box-blob-storage-requirements"></a>BLOB Storage-követelmények Azure Data Box

Ez a cikk felsorolja az Azure API-k, az Azure ügyféloldali kódtárak és a Data Box blob Storage által támogatott eszközök verzióját. A blob Storage Data Box Azure-konzisztens szemantikai funkciókat biztosít a Blobok kezeléséhez. Ez a cikk az Azure Storage-szolgáltatásokkal kapcsolatos ismert Azure Data Box blob Storage-különbségeket is összegzi.

Javasoljuk, hogy figyelmesen tekintse át az információkat, mielőtt csatlakozik a Data Box blob Storage-hoz, majd szükség szerint küldje vissza.


## <a name="storage-differences"></a>Tárolási különbségek

|     Szolgáltatás                                             |     Azure Storage                                     |     Data Box Blob Storage |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Azure File Storage                                   |    Felhőalapú SMB-fájlmegosztás támogatott              |    Nem támogatott      |
|    Szolgáltatás titkosítása inaktív adatok esetén                  |    256 bites AES-titkosítás                             |    256 bites AES-titkosítás |
|    Tárfiók típusa                                 |    Általános célú és Azure Blob Storage-fiókok    |    Csak általános célú v1|
|    A blob neve                                            |    1 024 karakter (2 048 bájt)                     |    880 karakter (1 760 bájt)|
|    BLOB maximális méretének letiltása                              |    4,75 TB (100 MB X 50 000 blokk)                   |    4,75 TB (100 MB x 50 000 blokk) Azure Data Box v 3,0-től.|
|    Oldal blobjának maximális mérete                               |    8 TB                                               |    1 TB                   |
|    Oldal blob-oldalának mérete                                  |    512 bájt                                          |    4 KB                   |

## <a name="supported-api-versions"></a>Támogatott API-verziók

Az Azure Storage Service API-k következő verziói támogatottak Data Box blob Storage-ban.

### <a name="azure-data-box-30-onwards"></a>Azure Data Box 3,0-tól

[!INCLUDE [data-box-rest-supported-api-versions](../../includes/data-box-rest-supported-api-versions.md)]

## <a name="supported-azure-client-libraries"></a>Támogatott Azure-ügyféloldali kódtárak

Data Box blob Storage esetében bizonyos ügyféloldali kódtárak és meghatározott végponti utótagok szükségesek. A Data Box blob Storage-végpontok nem rendelkeznek teljes paritással az Azure Blob Storage REST API legújabb verziójával. Tekintse [meg a Azure Data Box 3,0 támogatott verzióit](#supported-api-versions). A Storage ügyféloldali kódtárai esetében tisztában kell lennie azzal a verzióval, amely kompatibilis a REST APIával.

### <a name="azure-data-box-30-onwards"></a>Azure Data Box 3,0-tól

A blob Storage Data Box a következő Azure ügyféloldali függvénytár-verziókat támogatja.

[!INCLUDE [data-box-rest-supported-azure-client-libraries](../../includes/data-box-rest-supported-azure-client-libraries.md)]

### <a name="install-php-client-via-composer---current"></a>PHP-ügyfél telepítése a Zeneszerzőn keresztül – aktuális

A következőn keresztül történő telepítéshez: (blob készítése példaként).
1. Hozzon létre egy composer.jsnevű fájlt a projekt gyökérkönyvtárában a következő kóddal:

    ```
    {
    "require": {
    "Microsoft/azure-storage-blob":"1.2.0"
    }
    ```

2. Töltse le `composer.phar` a projekt gyökerét.

3. Futtatás: php zeneszerző. farmakovigilancia-telepítés.

### <a name="endpoint-declaration"></a>Végpont deklarációja

A Data Box blob Storage SDK-ban a végpont utótagja – `<device serial number>.microsoftdatabox.com` a Data Box tartományt azonosítja. A blob Service-végponttal kapcsolatos további információkért lépjen a [kapcsolódás Data Box blob Storage](data-box-deploy-copy-data-via-rest.md)-on keresztül.
 
## <a name="examples"></a>Példák

### <a name="net"></a>.NET

Data Box blob Storage esetében a végpont utótagja meg van adva a `app.config` fájlban:

```
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=<<serial no. of the device>.microsoftdatabox.com  />
```

### <a name="java"></a>Java

Data Box blob Storage esetében a végpont utótagját a rendszer a kapcsolatok karakterláncának beállításában adja meg:

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=<serial no. of the device>.microsoftdatabox.com ";
```

### <a name="nodejs"></a>Node.js

Data Box blob Storage esetében a végpont utótagja meg van adva a deklarációs példányban:

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob. <serial no. of the device>.microsoftdatabox.com ');
```

### <a name="c"></a>C++

Data Box blob Storage esetében a végpont utótagját a rendszer a kapcsolatok karakterláncának beállításában adja meg:

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com "));
```

### <a name="php"></a>PHP

Data Box blob Storage esetében a végpont utótagját a rendszer a kapcsolatok karakterláncának beállításában adja meg:

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.<serial no. of the device>.microsoftdatabox.com /;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Data Box blob Storage esetében a végpont utótagja meg van adva a deklarációs példányban:

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix=’<serial no. of the device>.microsoftdatabox.com’)
```

### <a name="ruby"></a>Ruby

Data Box blob Storage esetében a végpont utótagját a rendszer a kapcsolatok karakterláncának beállításában adja meg:

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com
```

## <a name="next-steps"></a>Következő lépések

* [A Azure Data Box üzembe helyezése](data-box-deploy-ordered.md)
