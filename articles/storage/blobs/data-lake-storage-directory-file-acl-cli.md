---
title: Az Azure CLI használata az adatkezeléshez (Azure Data Lake Storage Gen2)
description: Az Azure CLI-vel kezelheti azokat a címtárakat és fájlokat, amelyek hierarchikus névteret tartalmazó Storage-fiókokban vannak.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3e9afd4617eb7445ba83948d46eef0890832e2be
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100650354"
---
# <a name="use-azure-cli-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Könyvtárak és fájlok kezelése az Azure CLI használatával Azure Data Lake Storage Gen2

Ez a cikk bemutatja, hogyan hozhat létre és kezelhet olyan könyvtárakat és fájlokat az [Azure Command-Line felületen (CLI)](/cli/azure/) , amelyek hierarchikus névtérrel rendelkeznek a Storage-fiókokban.

A címtárak és fájlok hozzáférés-vezérlési listái (ACL) beszerzésével, beállításával és frissítésével kapcsolatos további információkért lásd: az [ACL-ek kezelése az Azure Data Lake Storage Gen2-ban](data-lake-storage-acl-cli.md).

[Példák](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)  |  [Visszajelzés küldése](https://github.com/Azure/azure-cli-extensions/issues)

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

- Olyan Storage-fiók, amelyen engedélyezve van a hierarchikus névtér. Az [alábbi](create-data-lake-storage-account.md) útmutatást követve hozzon létre egyet.

- Az Azure CLI verziója `2.6.0` vagy újabb.

## <a name="ensure-that-you-have-the-correct-version-of-azure-cli-installed"></a>Győződjön meg arról, hogy az Azure CLI megfelelő verziója van telepítve

1. Nyissa meg a [Azure Cloud Shell](../../cloud-shell/overview.md), vagy ha helyileg [telepítette](/cli/azure/install-azure-cli) az Azure CLI-t, nyisson meg egy parancssori alkalmazást, például a Windows PowerShellt.

2. A következő parancs használatával ellenőrizze, hogy a telepített Azure CLI `2.6.0` -verzió vagy magasabb-e.

   ```azurecli
    az --version
   ```

   Ha az Azure CLI verziója alacsonyabb, mint a `2.6.0` , telepítsen egy újabb verziót. Lásd: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="connect-to-the-account"></a>Kapcsolódás a fiókhoz

1. Ha helyileg használja az Azure CLI-t, futtassa a login parancsot.

   ```azurecli
   az login
   ```

   Ha a parancssori felület megnyithatja az alapértelmezett böngészőt, akkor az egy Azure-beli bejelentkezési oldal betöltésével végezhető el.

   Ellenkező esetben nyisson meg egy böngészőt, [https://aka.ms/devicelogin](https://aka.ms/devicelogin) és adja meg a terminálon megjelenő engedélyezési kódot. Ezután jelentkezzen be a fiókja hitelesítő adataival a böngészőben.

   A különböző hitelesítési módszerekkel kapcsolatos további tudnivalókért lásd: [hozzáférés engedélyezése a blobhoz vagy az üzenetsor-kezeléshez az Azure CLI-vel](./authorize-data-operations-cli.md).

2. Ha az identitása egynél több előfizetéshez van társítva, akkor állítsa be az aktív előfizetést a statikus webhelyét futtató Storage-fiók előfizetésére.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Cserélje le a `<subscription-id>` helyőrző értékét az előfizetés azonosítójával.

> [!NOTE]
> A cikkben bemutatott példa Azure Active Directory (Azure AD) engedélyezését mutatja be. További információ az engedélyezési módszerekről: a [blob-vagy üzenetsor-hozzáférés engedélyezése az Azure CLI-vel](./authorize-data-operations-cli.md).

## <a name="create-a-container"></a>Tároló létrehozása

A tároló fájlrendszerként működik a fájlok számára. A parancs használatával létrehozhat egyet `az storage fs create` . 

Ez a példa egy nevű tárolót hoz létre `my-file-system` .

```azurecli
az storage fs create -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-container-properties"></a>Tároló tulajdonságainak megjelenítése

A tároló tulajdonságait kinyomtathatja a konzolra a `az storage fs show` parancs használatával.

```azurecli
az storage fs show -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="list-container-contents"></a>Tároló tartalmának listázása

Egy könyvtár tartalmának listázása a parancs használatával `az storage fs file list` .

Ez a példa egy nevű tároló tartalmát sorolja fel `my-file-system` .

```azurecli
az storage fs file list -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-container"></a>Tároló törlése

Törölje a tárolót a `az storage fs delete` parancs használatával.

Ez a példa törli a nevű tárolót `my-file-system` . 

```azurecli
az storage fs delete -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="create-a-directory"></a>Könyvtár létrehozása

Hozzon létre egy címtár-referenciát a `az storage fs directory create` parancs használatával. 

Ez a példa egy nevű könyvtárat vesz `my-directory` fel egy nevű tárolóba, `my-file-system` amely egy nevű fiókban található `mystorageaccount` .

```azurecli
az storage fs directory create -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-directory-properties"></a>Könyvtár tulajdonságainak megjelenítése

A parancs használatával kinyomtathatja egy könyvtár tulajdonságait a konzolra `az storage fs directory show` .

```azurecli
az storage fs directory show -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-directory"></a>Címtár átnevezése vagy áthelyezése

Nevezze át vagy helyezze át a könyvtárat a `az storage fs directory move` parancs használatával.

Ez a példa átnevez egy könyvtárat a nevéből a `my-directory` `my-new-directory` tárolóban található névre.

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

Ez a példa egy könyvtárat helyez át egy nevű tárolóba `my-second-file-system` .

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-second-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-directory"></a>Könyvtár törlése

Törölje a könyvtárat a parancs használatával `az storage fs directory delete` .

Ez a példa törli a nevű könyvtárat `my-directory` . 

```azurecli
az storage fs directory delete -n my-directory -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="check-if-a-directory-exists"></a>Annak ellenőrzése, hogy létezik-e könyvtár

Annak megállapítása, hogy egy adott könyvtár létezik-e a tárolóban a `az storage fs directory exists` parancs használatával.

Ez a példa azt mutatja, hogy létezik-e egy nevű könyvtár `my-directory` a `my-file-system` tárolóban. 

```azurecli
az storage fs directory exists -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login 
```

## <a name="download-from-a-directory"></a>Letöltés egy címtárból

Töltse le a fájlt egy könyvtárból a `az storage fs file download` parancs használatával.

Ez a példa egy nevű könyvtárból tölt le egy nevű fájlt `upload.txt` `my-directory` . 

```azurecli
az storage fs file download -p my-directory/upload.txt -f my-file-system -d "C:\myFolder\download.txt" --account-name mystorageaccount --auth-mode login
```

## <a name="list-directory-contents"></a>Könyvtár tartalmának listázása

Egy könyvtár tartalmának listázása a parancs használatával `az storage fs file list` .

Ez a példa egy nevű könyvtár tartalmát sorolja fel, `my-directory` amely `my-file-system` egy nevű Storage-fiók tárolójában található `mystorageaccount` . 

```azurecli
az storage fs file list -f my-file-system --path my-directory --account-name mystorageaccount --auth-mode login
```

## <a name="upload-a-file-to-a-directory"></a>Fájl feltöltése könyvtárba

Töltse fel a fájlt egy könyvtárba a parancs használatával `az storage fs directory upload` .

Ez a példa egy nevű fájlt tölt fel egy nevű `upload.txt` könyvtárba `my-directory` . 

```azurecli
az storage fs file upload -s "C:\myFolder\upload.txt" -p my-directory/upload.txt  -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-file-properties"></a>Fájl tulajdonságainak megjelenítése

A parancs használatával kinyomtathatja egy fájl tulajdonságait a konzolra `az storage fs file show` .

```azurecli
az storage fs file show -p my-file.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-file"></a>Fájl átnevezése vagy áthelyezése

Nevezze át vagy helyezze át a fájlt a `az storage fs file move` parancs használatával.

Ez a példa átnevez egy fájlt a név `my-file.txt` alapján `my-file-renamed.txt` .

```azurecli
az storage fs file move -p my-file.txt -f my-file-system --new-path my-file-system/my-file-renamed.txt --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-file"></a>Fájl törlése

Törölje a fájlt a parancs használatával `az storage fs file delete` .

Ez a példa töröl egy nevű fájlt. `my-file.txt`

```azurecli
az storage fs file delete -p my-directory/my-file.txt -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="see-also"></a>Lásd még

- [Példák](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)
- [Visszajelzés küldése](https://github.com/Azure/azure-cli-extensions/issues)
- [Ismert problémák](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Az ACL-ek kezelése az Azure CLI használatával Azure Data Lake Storage Gen2](data-lake-storage-acl-cli.md)