---
title: 'REST API: fájlrendszer-műveletek Azure Data Lake Storage Gen1on | Microsoft Docs'
description: WebHDFS REST API-k használata a fájlrendszerbeli műveletek végrehajtásához Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 51e0fb2ffa7b573ecfeda163d9ad99597ff735a2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92109204"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-rest-api"></a>Fájlrendszer-műveletek Azure Data Lake Storage Gen1 a REST API használatával
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

Ebből a cikkből megtudhatja, hogyan használhatja a WebHDFS REST API-kat és Data Lake Storage Gen1 REST API-kat a fájlrendszerbeli műveletek végrehajtásához Azure Data Lake Storage Gen1on. A Data Lake Storage Gen1 a REST API használatával történő Fiókkezelés végrehajtásával kapcsolatos útmutatásért lásd: [Fiókkezelés a Data Lake Storage Gen1 REST API használatával](data-lake-store-get-started-rest-api.md).

## <a name="prerequisites"></a>Előfeltételek
* **Egy Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Azure Data Lake Storage Gen1 fiók**. Kövesse a [Azure Data Lake Storage Gen1 használatának első lépései a Azure Portal használatával](data-lake-store-get-started-portal.md)című témakör utasításait.

* **[curl](https://curl.haxx.se/)**. Ez a cikk a cURL használatával mutatja be, hogyan lehet REST API hívásokat kezdeményezni egy Data Lake Storage Gen1-fiókkal.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Hogyan végezhető el a hitelesítés az Azure Active Directory használatával?
Az Azure Active Directory használatával történő hitelesítést két módon végezheti el.

* Az alkalmazás végfelhasználói hitelesítéséhez (interaktív) lásd: [végfelhasználói hitelesítés a Data Lake Storage Gen1 .net SDK használatával](data-lake-store-end-user-authenticate-rest-api.md).
* Az alkalmazás szolgáltatások közötti hitelesítéséhez (nem interaktív) lásd: [szolgáltatások közötti hitelesítés a Data Lake Storage Gen1 .net SDK használatával](data-lake-store-service-to-service-authenticate-rest-api.md).


## <a name="create-folders"></a>Mappák létrehozása
Ez a művelet az [itt](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Make_a_Directory) definiált WebHDFS REST API-híváson alapul.

Használja a következő cURL-parancsot. Cserélje le a helyére **\<yourstorename>** a Data Lake Storage Gen1 fiók nevét.

```console
curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=MKDIRS'
```

Az előző parancsban cserélje le a \<`REDACTED`\> részt a korábban kapott hitelesítő jogkivonatra. Ez a parancs létrehoz egy **MyTempDir** nevű könyvtárat a Data Lake Storage Gen1 fiókjának gyökérkönyvtárában.

Ha a művelet sikeresen befejeződik, a következő kódrészlethez hasonló választ kell kapnia:

```output
{"boolean":true}
```

## <a name="list-folders"></a>Mappák listázása
Ez a művelet az [itt](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#List_a_Directory) definiált WebHDFS REST API-híváson alapul.

Használja a következő cURL-parancsot. Cserélje le a helyére **\<yourstorename>** a Data Lake Storage Gen1 fiók nevét.

```console
curl -i -X GET -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS'
```

Az előző parancsban cserélje le a \<`REDACTED`\> részt a korábban kapott hitelesítő jogkivonatra.

Ha a művelet sikeresen befejeződik, a következő kódrészlethez hasonló választ kell kapnia:

```output
{
"FileStatuses": {
    "FileStatus": [{
        "length": 0,
        "pathSuffix": "mytempdir",
        "type": "DIRECTORY",
        "blockSize": 268435456,
        "accessTime": 1458324719512,
        "modificationTime": 1458324719512,
        "replication": 0,
        "permission": "777",
        "owner": "<GUID>",
        "group": "<GUID>"
    }]
}
}
```

## <a name="upload-data"></a>Adatok feltöltése
Ez a művelet az [itt](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Create_and_Write_to_a_File) definiált WebHDFS REST API-híváson alapul.

Használja a következő cURL-parancsot. Cserélje le a helyére **\<yourstorename>** a Data Lake Storage Gen1 fiók nevét.

```console
curl -i -X PUT -L -T 'C:\temp\list.txt' -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/list.txt?op=CREATE'
```

Az előző szintaxisban a **-T** paraméter adja meg a feltöltendő fájl helyét.

A kimenet a következő kódrészlethez hasonló:
   
```output
HTTP/1.1 307 Temporary Redirect
...
Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/list.txt?op=CREATE&write=true
...
Content-Length: 0

HTTP/1.1 100 Continue

HTTP/1.1 201 Created
...
```

## <a name="read-data"></a>Adatok olvasása
Ez a művelet az [itt](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Open_and_Read_a_File) definiált WebHDFS REST API-híváson alapul.

Data Lake Storage Gen1 fiókból származó adatok beolvasása kétlépéses folyamat.

* Először küldenie kell egy GET kérelmet a `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN` végponthoz. Ez a hívás visszaadja azt a helyet, ahová a következő GET kérelmet kell küldenie.
* Ezután küldenie kell egy GET kérelmet a `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN&read=true` végponthoz. Ez a hívás megjeleníti a fájl tartalmát.

Mivel azonban az első és második lépésben nincs különbség a bemeneti paraméterek között, az első kérelem elküldéséhez használhatja az `-L` paramétert. A `-L` kapcsoló lényegében egyesít két kérelmet, és megismételteti a kérelmet a cURL-paranccsal az új helyen. Végül megjelenik az összes kérelemhívás kimenete az alábbi kódrészletben látható módon. Cserélje le a helyére **\<yourstorename>** a Data Lake Storage Gen1 fiók nevét.

```console
curl -i -L GET -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN'
```

Az alábbi kódrészlethez hasonló kimenetnek kell megjelennie:

```output
HTTP/1.1 307 Temporary Redirect
...
Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=OPEN&read=true
...

HTTP/1.1 200 OK
...

Hello, Data Lake Store user!
```

## <a name="rename-a-file"></a>Fájl átnevezése
Ez a művelet az [itt](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Rename_a_FileDirectory) definiált WebHDFS REST API-híváson alapul.

Fájl átnevezéséhez használja a következő cURL-parancsot. Cserélje le a helyére **\<yourstorename>** a Data Lake Storage Gen1 fiók nevét.

```console
curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=RENAME&destination=/mytempdir/myinputfile1.txt'
```

Az alábbi kódrészlethez hasonló kimenetnek kell megjelennie:

```output
HTTP/1.1 200 OK
...

{"boolean":true}
```

## <a name="delete-a-file"></a>Fájl törlése
Ez a művelet az [itt](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Delete_a_FileDirectory) definiált WebHDFS REST API-híváson alapul.

Fájl törléséhez használja a következő cURL-parancsot. Cserélje le a helyére **\<yourstorename>** a Data Lake Storage Gen1 fiók nevét.

```console
curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile1.txt?op=DELETE'
```

A következőhöz hasonló kimenetnek kell megjelennie:

```output
HTTP/1.1 200 OK
...

{"boolean":true}
```

## <a name="next-steps"></a>Következő lépések
* [Fiókkezelés Data Lake Storage Gen1 a REST API használatával](data-lake-store-get-started-rest-api.md).

## <a name="see-also"></a>Lásd még
* [Azure Data Lake Storage Gen1 REST API-hivatkozás](/rest/api/datalakestore/)
* [Azure Data Lake Storage Gen1-kompatibilis nyílt forráskódú Big adatalkalmazások](data-lake-store-compatible-oss-other-applications.md)