---
title: ACL-ek beállítása az Azure Data Lake Storage Gen2-ben az Azure CLI használatával
description: Az Azure CLI-vel kezelheti a hozzáférés-vezérlési listákat (ACL) olyan Storage-fiókokban, amelyek hierarchikus névteret használnak.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9814dc06e7e570a923ba3ea5b3b0df7ade99bb28
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654244"
---
# <a name="use-azure-cli-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Az ACL-ek kezelése az Azure CLI használatával Azure Data Lake Storage Gen2

Ez a cikk bemutatja, hogyan használható az [Azure Command-Line Interface (CLI)](/cli/azure/) a címtárak és fájlok hozzáférés-vezérlési listájainak beolvasásához, beállításához és frissítéséhez.

Az ACL öröklése már elérhető az új alárendelt elemekhez, amelyeket a rendszer a szülő címtárban hozott létre. Az ACL-eket rekurzív módon is hozzáadhatja, frissítheti és eltávolíthatja a szülő könyvtár meglévő alárendelt elemein anélkül, hogy ezeket a módosításokat külön kellene elvégeznie az egyes alárendelt elemek esetében.

[Hivatkozás](/cli/azure/storage/fs/access)  |  [Példák](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)  |  [Visszajelzés küldése](https://github.com/Azure/azure-cli-extensions/issues)

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

- Olyan Storage-fiók, amelyen engedélyezve van a hierarchikus névtér. Az [alábbi](create-data-lake-storage-account.md) útmutatást követve hozzon létre egyet.

- Az Azure CLI verziója `2.6.0` vagy újabb.

- A következő biztonsági engedélyek egyike:

  - Egy kiépített Azure Active Directory (Azure AD) [rendszerbiztonsági tag](../../role-based-access-control/overview.md#security-principal) , amely a [tároló blob-adattulajdonosi](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) szerepkört rendelte hozzá a cél tároló, a szülő erőforráscsoport vagy az előfizetés hatókörében.  

  - Annak a tárolónak vagy könyvtárnak a tulajdonosa, amelyre az ACL-beállításokat alkalmazni kívánja. Az ACL-ek rekurzív beállításához a cél tárolóban vagy a címtárban található összes alárendelt elem szerepel.
  
  - Storage-fiók kulcsa.

## <a name="ensure-that-you-have-the-correct-version-of-azure-cli-installed"></a>Győződjön meg arról, hogy az Azure CLI megfelelő verziója van telepítve

1. Nyissa meg a [Azure Cloud Shell](../../cloud-shell/overview.md), vagy ha helyileg [telepítette](/cli/azure/install-azure-cli) az Azure CLI-t, nyisson meg egy parancssori alkalmazást, például a Windows PowerShellt.

2. A következő parancs használatával ellenőrizze, hogy a telepített Azure CLI `2.14.0` -verzió vagy magasabb-e.

   ```azurecli
    az --version
   ```

   Ha az Azure CLI verziója alacsonyabb, mint a `2.14.0` , telepítsen egy újabb verziót. Lásd: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

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

## <a name="get-acls"></a>ACL-ek lekérése

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

## <a name="set-acls"></a>ACL-ek beállítása

Az ACL *beállításakor* a teljes ACL-t **lecseréli** , beleértve az összes bejegyzését. Ha módosítani szeretné egy rendszerbiztonsági tag hozzáférési szintjét, vagy egy új rendszerbiztonsági tag hozzáadása az ACL-hez anélkül, hogy ez hatással lenne a többi meglévő bejegyzésre, *frissítenie* kell az ACL-t. Ha az ACL-t nem a helyett szeretné frissíteni, tekintse meg a jelen cikk [frissítési ACL](#update-acls) -EK című szakaszát.  

Ha úgy dönt, hogy *Beállítja* az ACL-t, hozzá kell adnia egy bejegyzést a tulajdonos felhasználóhoz, egy bejegyzést a tulajdonos csoport számára, valamint egy bejegyzést az összes többi felhasználó számára. Ha többet szeretne megtudni a tulajdonos felhasználóról, a tulajdonos csoportról és az összes többi felhasználóról, tekintse meg a [felhasználók és az identitások](data-lake-storage-access-control.md#users-and-identities)című témakört.

Ez a szakasz a következőket mutatja be:

- ACL beállítása
- ACL-ek rekurzív beállítása

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

### <a name="set-acls-recursively"></a>ACL-ek rekurzív beállítása

Állítsa be az ACL-eket rekurzív módon az az [Storage FS Access set-rekurzív](/cli/azure/storage/fs/access#az_storage_fs_access_set_recursive) parancs használatával.

Ez a példa egy nevű könyvtár ACL-listáját állítja be `my-parent-directory` . Ezek a bejegyzések az olvasási, írási és végrehajtási engedélyeket adják meg a tulajdonos számára, és csak olvasási és végrehajtási engedélyeket biztosítanak a tulajdonos csoportnak, és minden más felhasználónak nincs hozzáférése. Ebben a példában az utolsó ACL-bejegyzés egy adott felhasználót ad meg az "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" AZONOSÍTÓJÚ objektumhoz olvasási és végrehajtási engedélyekkel.

```azurecli
az storage fs access set-recursive --acl "user::rwx,group::r-x,other::---,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Ha **alapértelmezett** ACL-bejegyzést kíván beállítani, adja hozzá az előtagot az `default:` egyes bejegyzésekhez. Például `default:user::rwx` vagy `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x`.

## <a name="update-acls"></a>ACL-ek frissítése

*Ha módosít egy ACL* -t, akkor az ACL helyett módosítania kell az ACL-t. Hozzáadhat például egy új rendszerbiztonsági tag-t az ACL-hez anélkül, hogy ez hatással lenne az ACL-ben felsorolt többi rendszerbiztonsági tagra is.  Ha a frissítés helyett az ACL-t szeretné cserélni, tekintse meg a jelen cikk ACL-ek [beállítása](#set-acls) című szakaszát.

Az ACL frissítéséhez hozzon létre egy új ACL-objektumot a frissíteni kívánt ACL-bejegyzéssel, majd használja ezt az objektumot az ACL frissítése műveletben. Ne szerezze be a meglévő ACL-t, csak adja meg a frissítendő ACL-bejegyzéseket.

Ez a szakasz a következőket mutatja be:

- ACL frissítése
- Az ACL-ek rekurzív frissítése

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

### <a name="update-acls-recursively"></a>Az ACL-ek rekurzív frissítése

Az ACL-ek rekurzív frissítése az az  [Storage FS Access Update-rekurzív](/cli/azure/storage/fs/access#az_storage_fs_access_update_recursive) parancs használatával.

Ez a példa egy írási engedéllyel rendelkező ACL-bejegyzést frissít.

```azurecli
az storage fs access update-recursive --acl "user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Ha frissíteni szeretné az **alapértelmezett** ACL-bejegyzést, adja hozzá az előtagot az `default:` egyes bejegyzésekhez. Például: `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x`.

## <a name="remove-acl-entries-recursively"></a>ACL-bejegyzések rekurzív eltávolítása

Egy vagy több ACL-bejegyzést rekurzív módon eltávolíthat. Az ACL-bejegyzések eltávolításához hozzon létre egy új ACL-objektumot az ACL-bejegyzés eltávolításához, majd használja ezt az objektumot az ACL eltávolítása műveletben. Ne szerezze be a meglévő ACL-t, csak adja meg az eltávolítandó ACL-bejegyzéseket.

Távolítsa el az ACL-bejegyzéseket az az [Storage FS Access Remove-rekurzív](/cli/azure/storage/fs/access#az_storage_fs_access_remove_recursive) parancs használatával.

Ez a példa egy ACL-bejegyzést távolít el a tároló gyökérkönyvtárában.  

```azurecli
az storage fs access remove-recursive --acl "user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Ha el szeretné távolítani az **alapértelmezett** ACL-bejegyzést, adja hozzá az előtagot az `default:` egyes bejegyzésekhez. Például: `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

## <a name="recover-from-failures"></a>Helyreállítás hibákból

A hozzáférés-vezérlési listák rekurzív módosításakor előfordulhatnak futásidejű vagy engedélyezési hibák. Futásidejű hibák esetén indítsa újra a folyamatot az elejétől. Engedélyekkel kapcsolatos hibák akkor fordulhatnak elő, ha a rendszerbiztonsági tag nem rendelkezik megfelelő engedélyekkel egy olyan könyvtár vagy fájl hozzáférés-vezérlési listájának módosításához, amely a címtár-hierarchiában van módosítva. Oldja meg az engedélyekkel kapcsolatos problémát, majd a folytatási token használatával folytassa a folyamatot a meghibásodási pontról, vagy indítsa újra a folyamatot az elejétől. A folytatási tokent nem kell használnia, ha az elejéről szeretne újraindulni. Az ACL-bejegyzéseket a negatív hatás nélkül is újra alkalmazhatja.

Hiba esetén visszaállíthat egy folytatási tokent a paraméter értékre állításával `--continue-on-failure` `false` . A hibák elhárítása után folytathatja a folyamatot a meghibásodási pontról, ha újra futtatja a parancsot, majd a `--continuation` paramétert a folytatási tokenre állítja be.

```azurecli
az storage fs access set-recursive --acl "user::rw-,group::r-x,other::---" --continue-on-failure false --continuation xxxxxxx -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login  
```

Ha azt szeretné, hogy a folyamat az engedélyekkel kapcsolatos hibák mellett befejeződjön, megadhatja a következőt:.

A folyamat megszakítás nélküli befejezésének biztosításához állítsa a paramétert a következőre: `--continue-on-failure` `true` .

```azurecli
az storage fs access set-recursive --acl "user::rw-,group::r-x,other::---" --continue-on-failure true --continuation xxxxxxx -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login  
```

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Lásd még

- [Példák](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)
- [Visszajelzés küldése](https://github.com/Azure/azure-cli-extensions/issues)
- [Ismert problémák](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Hozzáférés-vezérlési modell Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [Hozzáférés-vezérlési listák (ACL-ek) Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)