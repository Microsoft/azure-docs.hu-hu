---
title: Az Azure CLI használata fájlok & ACL-ekkel Azure Data Lake Storage Gen2
description: Az Azure CLI használatával kezelheti a könyvtárakat és a fájl-és címtár-hozzáférés-vezérlési listákat (ACL) a hierarchikus névtérrel rendelkező Storage-fiókokban.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 05/18/2020
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurecli
ms.openlocfilehash: ee461193be81297c6577ce4c264cabbf08e72417
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2020
ms.locfileid: "93359442"
---
# <a name="use-azure-cli-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Könyvtárak, fájlok és ACL-ek kezelése az Azure CLI használatával Azure Data Lake Storage Gen2

Ez a cikk bemutatja, hogyan hozhat létre és kezelhet könyvtárakat, fájlokat és engedélyeket a hierarchikus névtérrel rendelkező Storage-fiókokban az [Azure Command-Line Interface (CLI)](https://docs.microsoft.com/cli/azure/) használatával. 

[Példák](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)  |  [Visszajelzés küldése](https://github.com/Azure/azure-cli-extensions/issues)

## <a name="prerequisites"></a>Előfeltételek

> [!div class="checklist"]
> * Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
> * Olyan Storage-fiók, amelyen engedélyezve van a hierarchikus névtér (HNS). Az [alábbi](data-lake-storage-quickstart-create-account.md) útmutatást követve hozzon létre egyet.
> * Az Azure CLI verziója `2.6.0` vagy újabb.

## <a name="ensure-that-you-have-the-correct-version-of-azure-cli-installed"></a>Győződjön meg arról, hogy az Azure CLI megfelelő verziója van telepítve

1. Nyissa meg a [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), vagy ha helyileg [telepítette](https://docs.microsoft.com/cli/azure/install-azure-cli) az Azure CLI-t, nyisson meg egy parancssori alkalmazást, például a Windows PowerShellt.

2. A következő parancs használatával ellenőrizze, hogy a telepített Azure CLI `2.6.0` -verzió vagy magasabb-e.

   ```azurecli
    az --version
   ```
   Ha az Azure CLI verziója alacsonyabb, mint a `2.6.0` , telepítsen egy újabb verziót. Lásd: [Az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="connect-to-the-account"></a>Kapcsolódás a fiókhoz

1. Ha helyileg használja az Azure CLI-t, futtassa a login parancsot.

   ```azurecli
   az login
   ```

   Ha a parancssori felület megnyithatja az alapértelmezett böngészőt, akkor az egy Azure-beli bejelentkezési oldal betöltésével végezhető el.

   Ellenkező esetben nyisson meg egy böngészőt, [https://aka.ms/devicelogin](https://aka.ms/devicelogin) és adja meg a terminálon megjelenő engedélyezési kódot. Ezután jelentkezzen be a fiókja hitelesítő adataival a böngészőben.

   A különböző hitelesítési módszerekkel kapcsolatos további tudnivalókért lásd: [hozzáférés engedélyezése a blobhoz vagy az üzenetsor-kezeléshez az Azure CLI-vel](../common/authorize-data-operations-cli.md).

2. Ha az identitása egynél több előfizetéshez van társítva, akkor állítsa be az aktív előfizetést a statikus webhelyét futtató Storage-fiók előfizetésére.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Cserélje le a `<subscription-id>` helyőrző értékét az előfizetés azonosítójával.

> [!NOTE]
> A cikkben bemutatott példa Azure Active Directory (AD) engedélyezését mutatja. További információ az engedélyezési módszerekről: a [blob-vagy üzenetsor-hozzáférés engedélyezése az Azure CLI-vel](../common/authorize-data-operations-cli.md).

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

## <a name="manage-access-control-lists-acls"></a>Hozzáférés-vezérlési listák (ACL-ek) kezelése

Lekérheti, beállíthatja és frissítheti a címtárak és fájlok hozzáférési engedélyeit.

> [!NOTE]
> Ha Azure Active Directory (Azure AD) használatával engedélyezi a parancsokat, akkor győződjön meg arról, hogy a rendszerbiztonsági tag hozzá lett rendelve a [Storage blob-adat tulajdonosi szerepköréhez](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Ha többet szeretne megtudni az ACL-engedélyek alkalmazásáról és azok módosításának hatásairól, tekintse meg a  [Azure Data Lake Storage Gen2 hozzáférés-vezérlését](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)ismertető témakört.

### <a name="get-an-acl"></a>ACL beszerzése

A parancs használatával szerezze be a **CÍMTÁR** ACL-listáját `az storage fs access show` .

Ez a példa egy könyvtár ACL-listáját kéri le, majd kiírja az ACL-t a konzolra.

```azurecli
az storage fs access show -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

A parancs használatával szerezze be egy **fájl** hozzáférési engedélyeit `az storage fs access show` . 

Ez a példa egy fájl ACL-listáját kéri le, majd kiírja az ACL-t a konzolra.

```azurecli
az storage fs access show -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

Az alábbi képen egy könyvtár ACL-listájának beolvasása után a kimenet látható.

![ACL kimenetének beolvasása](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

Ebben a példában a tulajdonos felhasználó olvasási, írási és végrehajtási engedélyekkel rendelkezik. A tulajdonos csoport csak olvasási és végrehajtási engedélyekkel rendelkezik. A hozzáférés-vezérlési listával kapcsolatos további információkért lásd: [hozzáférés-vezérlés Azure Data Lake Storage Gen2ban](data-lake-storage-access-control.md).

### <a name="set-an-acl"></a>ACL beállítása

A `az storage fs access set` parancs használatával állítsa be a **címtár** ACL-listáját. 

Ez a példa a tulajdonos felhasználó, tulajdonos csoport vagy más felhasználók könyvtárának ACL-listáját állítja be, majd kinyomtatja az ACL-t a konzolra.

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Ez a példa a tulajdonos felhasználó, tulajdonos csoport vagy más felhasználók könyvtárának *alapértelmezett* ACL-listáját állítja be, majd kinyomtatja az ACL-t a konzolra.

```azurecli
az storage fs access set --acl "default:user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

A `az storage fs access set` parancs használatával állítsa be egy **fájl** ACL-listáját. 

Ez a példa egy fájl ACL-fájlját állítja be a tulajdonos felhasználó, tulajdonos csoport vagy más felhasználók számára, majd kinyomtatja az ACL-t a konzolra.

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

Az alábbi képen egy fájl ACL-listájának beállítása után a kimenet látható.

![A 2. ACL-kimenet beolvasása](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

Ebben a példában a tulajdonos felhasználó és a tulajdonos csoport csak olvasási és írási engedéllyel rendelkezik. Minden más felhasználó írási és végrehajtási engedélyekkel rendelkezik. A hozzáférés-vezérlési listával kapcsolatos további információkért lásd: [hozzáférés-vezérlés Azure Data Lake Storage Gen2ban](data-lake-storage-access-control.md).

### <a name="update-an-acl"></a>ACL frissítése

Ezt az engedélyt úgy is beállíthatja, hogy a `az storage fs access set` parancsot használja. 

Frissítse a címtár vagy fájl ACL-listáját úgy `-permissions` , hogy a paramétert egy ACL rövid formájára állítja be.

Ez a példa egy **könyvtár** ACL-listáját frissíti.

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Ez a példa egy **fájl** ACL-listáját frissíti.

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

Egy könyvtár vagy fájl tulajdonos felhasználóját és csoportját úgy is frissítheti, ha az `--owner` vagy a `group` paramétereket egy felhasználó entitás-azonosító vagy egyszerű felhasználónév (UPN) értékére állítja be. 

Ez a példa egy könyvtár tulajdonosát módosítja. 

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Ez a példa egy fájl tulajdonosát módosítja. 

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login

```

### <a name="set-an-acl-recursively"></a>ACL beállítása rekurzív módon

Az ACL-eket a szülő könyvtár meglévő alárendelt elemein is hozzáadhatja, frissítheti és eltávolíthatja anélkül, hogy ezeket a módosításokat egyenként el kellene végeznie az egyes alárendelt elemek esetében. További információ: [rekurzív hozzáférés-vezérlési listák (ACL-ek) beállítása Azure Data Lake Storage Gen2hoz](recursive-access-control-lists.md).

## <a name="see-also"></a>Lásd még

* [Példák](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)
* [Visszajelzés küldése](https://github.com/Azure/azure-cli-extensions/issues)
* [Ismert problémák](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)


