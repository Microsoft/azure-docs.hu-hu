---
title: Fájlok feltöltése az Azure Blob Storage-ba a AzCopy v10 használatával | Microsoft Docs
description: Ez a cikk AzCopy-példákat tartalmaz, amelyek segítségével fájlokat tölthet fel az Azure Blob Storage-ba.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/11/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 7cdc10720b6fa93cf1893d2040fd1c1f3e9e1f02
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105728862"
---
# <a name="upload-files-to-azure-blob-storage-by-using-azcopy-v10"></a>Fájlok feltöltése az Azure Blob Storage-ba az AzCopy v10 használatával

A blob Storage-hoz a AzCopy v10 parancssori segédprogram használatával tölthet fel fájlokat és könyvtárakat. 

Ha más típusú feladatokra, például Blobok letöltésére, blob Storage-beli szinkronizálásra vagy Blobok a fiókok közötti másolására vonatkozó példákat szeretne látni, tekintse meg a jelen cikk [következő lépések](#next-steps) című szakaszának hivatkozásait.

## <a name="get-started"></a>Bevezetés

Tekintse meg az első [lépések a AzCopy](storage-use-azcopy-v10.md) című cikket a AzCopy letöltéséhez és a tárolási szolgáltatás engedélyezési hitelesítő adatainak megadásához szükséges módszerekről.

> [!NOTE] 
> A cikkben szereplő példák azt feltételezik, hogy Azure Active Directory (Azure AD) használatával adta meg az engedélyezési hitelesítő adatokat.
>
> Ha inkább SAS-tokent használ a blob-adathozzáférés engedélyezéséhez, akkor a tokent az erőforrás URL-címéhez is hozzáfűzheti az egyes AzCopy-parancsokban. Példa: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="create-a-container"></a>Tároló létrehozása

Tároló létrehozásához használhatja a [azcopy make](storage-ref-azcopy-make.md) parancsot.

> [!TIP]
> Ez a példa a Path argumentumokat szimpla idézőjelekkel (' ') fedi le. Használjon egy idézőjelet az összes parancs-rendszerhéjon, kivéve a Windows parancs-rendszerhéjt (cmd.exe). Ha Windows parancs-rendszerhéjt (cmd.exe) használ, az idézőjelek ("") helyett idézőjelek ("") közé foglalja a Path argumentumokat ("").

| Szintaxis/példa | Code |
|--------|-----------|
| **Syntax** | `azcopy make 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>'` |
| **Példa** | `azcopy make 'https://mystorageaccount.blob.core.windows.net/mycontainer'` |
| **Példa** (hierarchikus névtér) | `azcopy make 'https://mystorageaccount.dfs.core.windows.net/mycontainer'` |

A részletes dokumentációt a következő témakörben tekintheti meg: [azcopy make](storage-ref-azcopy-make.md).

## <a name="upload-a-file"></a>Fájl feltöltése

Töltsön fel egy fájlt a [azcopy Copy](storage-ref-azcopy-copy.md) paranccsal.

> [!TIP]
> Ez a példa a Path argumentumokat szimpla idézőjelekkel (' ') fedi le. Használjon egy idézőjelet az összes parancs-rendszerhéjon, kivéve a Windows parancs-rendszerhéjt (cmd.exe). Ha Windows parancs-rendszerhéjt (cmd.exe) használ, az idézőjelek ("") helyett idézőjelek ("") közé foglalja a Path argumentumokat ("").

| Szintaxis/példa  |  Code |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'` |
| **Példa** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt'` |

A fájl elérési útja vagy fájlneve tetszőleges helyettesítő karakter (*) használatával is feltölthet egy fájlt. Például: `'C:\myDirectory\*.txt'` , vagy `C:\my*\*.txt` .

## <a name="upload-a-directory"></a>Könyvtár feltöltése

Töltsön fel egy könyvtárat a [azcopy Copy](storage-ref-azcopy-copy.md) paranccsal. 

Ez a példa egy könyvtárat (és az abban a könyvtárban található összes fájlt) egy blob-tárolóba másol. Ennek eredményeképpen a tároló egyik könyvtára azonos néven szerepel.

> [!TIP]
> Ez a példa a Path argumentumokat szimpla idézőjelekkel (' ') fedi le. Használjon egy idézőjelet az összes parancs-rendszerhéjon, kivéve a Windows parancs-rendszerhéjt (cmd.exe). Ha Windows parancs-rendszerhéjt (cmd.exe) használ, az idézőjelek ("") helyett idézőjelek ("") közé foglalja a Path argumentumokat ("").

| Szintaxis/példa  |  Code |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --recursive` |
| **Példa** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --recursive` |

A tárolóban lévő könyvtárba való másoláshoz csak adja meg a könyvtár nevét a parancs karakterláncában.

| Szintaxis/példa  |  Code |
|--------|-----------|
| **Példa** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --recursive` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' --recursive` |

Ha egy olyan könyvtár nevét adja meg, amely nem szerepel a tárolóban, a AzCopy létrehoz egy új könyvtárat a név alapján.

## <a name="upload-directory-contents"></a>Könyvtár tartalmának feltöltése

Töltse fel egy könyvtár tartalmát a [azcopy Copy](storage-ref-azcopy-copy.md) paranccsal. A helyettesítő karaktert (*) használva feltöltheti a tartalmat anélkül, hogy magáról a tartalmazó könyvtárat másolja.

> [!TIP]
> Ez a példa a Path argumentumokat szimpla idézőjelekkel (' ') fedi le. Használjon egy idézőjelet az összes parancs-rendszerhéjon, kivéve a Windows parancs-rendszerhéjt (cmd.exe). Ha Windows parancs-rendszerhéjt (cmd.exe) használ, az idézőjelek ("") helyett idézőjelek ("") közé foglalja a Path argumentumokat ("").

| Szintaxis/példa  |  Code |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>'` |
| **Példa** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory'` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory'` |


Fűzze hozzá a `--recursive` jelölőt a fájlok minden alkönyvtárban való feltöltéséhez.

## <a name="upload-specific-files"></a>Adott fájlok feltöltése

Az adott fájlokat feltöltheti a teljes fájlnevekkel, a részleges nevekkel, helyettesítő karakterekkel (*), vagy dátum és idő használatával.

> [!TIP]
> Ezek a példák egyetlen idézőjelekkel (' ') foglalják el az elérésiút-argumentumokat. Használjon egy idézőjelet az összes parancs-rendszerhéjon, kivéve a Windows parancs-rendszerhéjt (cmd.exe). Ha Windows parancs-rendszerhéjt (cmd.exe) használ, az idézőjelek ("") helyett idézőjelek ("") közé foglalja a Path argumentumokat ("").

### <a name="specify-multiple-complete-file-names"></a>Több teljes fájlnevet kell megadni

Használja az [azcopy Copy](storage-ref-azcopy-copy.md) parancsot a `--include-path` kapcsolóval. Az egyes fájlneveket pontosvesszővel () válassza el egymástól `;` .

| Szintaxis/példa  |  Code |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-path <semicolon-separated-file-list>` |
| **Példa** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |

Ebben a példában a AzCopy átviszi a `C:\myDirectory\photos` könyvtárat és a `C:\myDirectory\documents\myFile.txt` fájlt. Adja `--recursive` meg a könyvtárban található összes fájl átvitelének lehetőségét `C:\myDirectory\photos` .

A fájlokat a lehetőség használatával is kizárhatja `--exclude-path` . További információért lásd: [azcopy másolási](storage-ref-azcopy-copy.md) dokumentáció.

### <a name="use-wildcard-characters"></a>Helyettesítő karakterek használata

Használja az [azcopy Copy](storage-ref-azcopy-copy.md) parancsot a `--include-pattern` kapcsolóval. Adjon meg olyan részleges neveket, amelyek tartalmazzák a helyettesítő karaktereket. A neveket a semicolin () használatával válassza el `;` . 

| Szintaxis/példa  |  Code |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Példa** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |

A fájlokat a lehetőség használatával is kizárhatja `--exclude-pattern` . További információért lásd: [azcopy másolási](storage-ref-azcopy-copy.md) dokumentáció.

A `--include-pattern` és a `--exclude-pattern` beállítások csak a fájlnevekre érvényesek, és nem az elérési útra.  Ha egy címtár fájában található összes szövegfájlt szeretné átmásolni, használja a `–recursive` teljes könyvtár fájának beolvasására szolgáló lehetőséget, majd a `–include-pattern` és a érték megadásával töltse le az `*.txt` összes szövegfájlt.

### <a name="upload-files-that-were-modified-before-or-after-a-date-and-time"></a>A dátum és idő előtt vagy után módosított fájlok feltöltése 

Használja az [azcopy Copy](storage-ref-azcopy-copy.md) parancsot a `--include-before` vagy a `--include-after` kapcsolóval. A dátumot és az időt ISO-8601 formátumban kell megadni (például: `2020-08-19T15:04:00Z` ). 

A következő példák a megadott dátumon vagy azt követően módosított fájlokat töltenek fel.

| Szintaxis/példa  |  Code |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>'  --include-after <Date-Time-in-ISO-8601-format>` |
| **Példa** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory'  --include-after '2020-08-19T15:04:00Z'` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory'   --include-after '2020-08-19T15:04:00Z'` |

Részletes információk: [azcopy másolási](storage-ref-azcopy-copy.md) dokumentáció.

## <a name="upload-with-index-tags"></a>Feltöltés tárgymutató-címkékkel

Feltölthet egy fájlt, és hozzáadhat [blob-index címkéket (előzetes verzió)](../blobs/storage-manage-find-blobs.md) a cél blobhoz.  

Ha Azure AD-hitelesítést használ, a rendszerbiztonsági tag számára a [Storage blob-adattulajdonosi](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) szerepkört kell hozzárendelni, vagy az `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` [Azure erőforrás-szolgáltatói művelethez](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) egy egyéni Azure-szerepkörön keresztül kell engedélyt adni. Ha közös hozzáférésű aláírási (SAS-) tokent használ, a tokennek a sas engedélyen keresztül kell hozzáférést biztosítania a blob címkéhez `t` .

Címkék hozzáadásához használja a `--blob-tags` kapcsolót egy URL-címmel kódolt kulcs-érték párokkal együtt. Például a kulcs `my tag` és egy érték hozzáadásához `my tag value` adja hozzá a ( `--blob-tags='my%20tag=my%20tag%20value'` z) paramétert. 

Több indexelési címkét is elkülönítheti egy jel ( `&` ) használatával.  Ha például egy kulcsot és egy értéket szeretne felvenni `my second tag` `my second tag value` , a teljes beállítás sztring lenne `--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` .

Az alábbi példák bemutatják, hogyan használhatja a `--blob-tags` kapcsolót.

> [!TIP]
> Ez a példa a Path argumentumokat szimpla idézőjelekkel (' ') fedi le. Használjon egy idézőjelet az összes parancs-rendszerhéjon, kivéve a Windows parancs-rendszerhéjt (cmd.exe). Ha Windows parancs-rendszerhéjt (cmd.exe) használ, az idézőjelek ("") helyett idézőjelek ("") közé foglalja a Path argumentumokat ("").

| Szintaxis/példa  |  Code |
|--------|-----------|
| **Fájl feltöltése** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **Könyvtár feltöltése** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'`|
| **Könyvtár tartalmának feltöltése** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |

> [!NOTE]
> Ha megad egy könyvtárat a forráshoz, a célhelyre másolt összes blob ugyanazokkal a címkékkel fog rendelkezni, mint a parancsban.

## <a name="upload-with-optional-flags"></a>Feltöltés opcionális jelzővel

A feltöltési műveletet opcionális jelzők használatával is megteheti. Íme néhány példa.

|Eset|Jelölő|
|---|---|
|Fájlok feltöltése hozzáfűző blobként vagy lapblobként.|**– blob típusú** = \[ BlockBlob \| PageBlob \| AppendBlob\]|
|Feltöltés egy adott hozzáférési szintre (például az archív szintre).|**--Block-blob-réteg** = \[ Nincs \| gyors elérésű \| \| Archívum\]|

A teljes listát itt tekintheti meg: [Beállítások](storage-ref-azcopy-copy.md#options).

## <a name="next-steps"></a>Következő lépések

További példákat a következő cikkekben talál:

- [Példák: Letöltés](storage-use-azcopy-blobs-download.md)
- [Példák: Másolás tárfiókok között](storage-use-azcopy-blobs-copy.md)
- [Példák: Szinkronizálás](storage-use-azcopy-blobs-synchronize.md)
- [Példák: Amazon S3-gyűjtők](storage-use-azcopy-s3.md)
- [Példák: Azure Files](storage-use-azcopy-files.md)
- [Oktatóanyag: Helyszíni adatok migrálása felhőtárhelybe az AzCopyval](storage-use-azcopy-migrate-on-premises-data.md)
- [AzCopy konfigurálása, optimalizálása és megoldása](storage-use-azcopy-configure.md)