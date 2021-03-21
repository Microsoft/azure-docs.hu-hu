---
title: Microsoft Azure Stack Edge blob Storage-követelmények | Microsoft Docs
description: Ismerje meg az API-k, SDK-k és a Azure Stack Edge blob Storage-hoz készült ügyféloldali kódtárak támogatott verzióit
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/21/2020
ms.author: alkohli
ms.openlocfilehash: 887aaf32592c74a5884916bc7ad63d79544fb554
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99575855"
---
# <a name="azure-stack-edge-blob-storage-requirements"></a>Azure Stack Edge blob Storage-követelmények

Ez a cikk felsorolja az Azure API-k, az Azure-ügyféloldali kódtárak és az Azure Stack Edge blob Storage által támogatott eszközök verzióját. Azure Stack Edge blob Storage az Azure-konzisztens szemantikai funkciókat biztosít a Blobok kezeléséhez. Ez a cikk az Azure Storage-szolgáltatásokkal kapcsolatos ismert Azure Stack Edge blob Storage-különbségeket is összegzi.

Javasoljuk, hogy figyelmesen tekintse át az információkat, mielőtt az Azure Stack Edge blob Storage-hoz csatlakozik, majd szükség szerint tekintse át újra.

## <a name="storage-differences"></a>Tárolási különbségek

|     Szolgáltatás                                             |     Azure Storage                                     |     Azure Stack Edge blob Storage |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Azure File Storage                                   |    Felhőalapú SMB-fájlmegosztás támogatott              |    Nem támogatott      |
|    Tárfiók típusa                                 |    Általános célú és Azure Blob Storage-fiókok    |    Csak általános célú v1|
|    A blob neve                                            |    1 024 karakter (2 048 bájt)                     |    880 karakter (1 760 bájt)|
|    BLOB maximális méretének letiltása                              |    4,75 TB (100 MB X 50 000 blokk)                   |    4,75 TB (100 MB x 50 000 blokk) Azure Stack Edge-hez|
|    Oldal blobjának maximális mérete                               |    8 TB                                               |    1 TB                   |
|    Oldal blob-oldalának mérete                                  |    512 bájt                                          |    4 KB                   |

## <a name="supported-api-versions"></a>Támogatott API-verziók

Az Azure Storage Service API-k következő verziói támogatottak Azure Stack Edge blob Storage-ban.

### <a name="azure-stack-edge-2113772170-onwards"></a>Azure Stack Edge 2.1.1377.2170-től

[!INCLUDE [data-box-rest-supported-api-versions](../../includes/data-box-rest-supported-api-versions.md)]

## <a name="supported-azure-client-libraries"></a>Támogatott Azure-ügyféloldali kódtárak

A Azure Stack Edge blob Storage esetében bizonyos ügyféloldali kódtárak és meghatározott végponti utótagok szükségesek. Az Azure Stack Edge blob Storage-végpontok nem rendelkeznek teljes paritással az Azure Blob Storage REST API legújabb verziójával. Tekintse [meg az Azure stack Edge által támogatott API-verziókat](#supported-api-versions). A Storage ügyféloldali kódtárai esetében tisztában kell lennie azzal a verzióval, amely kompatibilis a REST APIával.

### <a name="azure-stack-edge-2113772170-onwards"></a>Azure Stack Edge 2.1.1377.2170-től

A következő Azure ügyféloldali függvénytár-verziók támogatottak Azure Stack Edge blob Storage-hoz.

[!INCLUDE [data-box-rest-supported-azure-client-libraries](../../includes/data-box-rest-supported-azure-client-libraries.md)]

### <a name="install-the-php-client-via-composer---current"></a>A PHP-ügyfél telepítése a zeneszerző-current használatával

A PHP-ügyfél telepítése a zeneszerző használatával:

1. Hozzon létre egy composer.jsnevű fájlt a projekt gyökérkönyvtárában a következő kóddal (például Azure Storage Blob szolgáltatás használatával).

    ```
    {
    "require": {
    "Microsoft/azure-storage-blob":"1.2.0"
    }
    ```

2. Töltse le `composer.phar` a projekt gyökerét.

3. Futtatás: php zeneszerző. farmakovigilancia-telepítés.


## <a name="endpoint-declaration"></a>Végpont deklarációja

Az Azure Stack Edge blob Storage SDK-ban a végpont utótagja – a `<device serial number>.microsoftdatabox.com` Azure stack Edge-tartományt azonosítja. A blob Service-végponttal kapcsolatos további információkért nyissa meg az [adatok átvitele a Storage-fiókokkal Azure stack Edge Pro GPU](azure-stack-edge-j-series-deploy-add-storage-accounts.md)használatával című témakört.


## <a name="examples"></a>Példák

### <a name="net"></a>.NET

Azure Stack Edge blob Storage esetén a végpont utótagja a következő fájlban van megadva `app.config` :

```
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=<<serial no. of the device>.microsoftdatabox.com  />
```

### <a name="java"></a>Java

Azure Stack Edge blob Storage esetén a végpont utótagját a rendszer a kapcsolatok karakterláncának beállításában adja meg:

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=<serial no. of the device>.microsoftdatabox.com ";
```

### <a name="nodejs"></a>Node.js

A Azure Stack Edge blob Storage esetében a végpont utótagja a deklarációs példányban van megadva:

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob. <serial no. of the device>.microsoftdatabox.com ');
```

### <a name="c"></a>C++

A Azure Stack Edge blob Storage esetében a végpont utótagját a rendszer a kapcsolatok karakterláncának beállításában adja meg:

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com "));
```

### <a name="php"></a>PHP

A Azure Stack Edge blob Storage esetében a végpont utótagját a rendszer a kapcsolatok karakterláncának beállításában adja meg:

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.<serial no. of the device>.microsoftdatabox.com /;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

A Azure Stack Edge blob Storage esetében a végpont utótagja a deklarációs példányban van megadva:

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix=’<serial no. of the device>.microsoftdatabox.com’)
```

### <a name="ruby"></a>Ruby

A Azure Stack Edge blob Storage esetében a végpont utótagját a rendszer a kapcsolatok karakterláncának beállításában adja meg:

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com
```

## <a name="next-steps"></a>Következő lépések

* [Felkészülés a Azure Stack Edge Pro GPU-val való üzembe helyezésére](azure-stack-edge-gpu-deploy-prep.md)
