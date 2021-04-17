---
title: Fájlok feltöltése az Azure Blob Storage-ba az AzCopy 10-es | Microsoft Docs
description: Ez a cikk az AzCopy példaparancsok gyűjteményét tartalmazza, amelyek segítségével fájlokat tölthet fel az Azure Blob Storage-ba.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 352497f0f4d23250abe9f84121f358589664002b
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502912"
---
# <a name="upload-files-to-azure-blob-storage-by-using-azcopy"></a>Fájlok feltöltése az Azure Blob Storage-ba az AzCopy használatával

Az AzCopy v10 parancssori segédprogrammal fájlokat és könyvtárakat tölthet fel a Blob Storage-be. 

Ha más típusú feladatokra, például a blobok letöltésére, a Blob Storage-tel való szinkronizálásra vagy [](#next-steps) a fiókok közötti másolásra vonatkozó példákat keres, tekintse meg a cikk Következő lépések szakaszában található hivatkozásokat.

## <a name="get-started"></a>Bevezetés

Az [AzCopy letöltését](storage-use-azcopy-v10.md) az Első lépések az AzCopyval cikkben olvashatja el, és megtudhatja, hogyan biztosítanak hitelesítési hitelesítő adatokat a tárolási szolgáltatásnak.

> [!NOTE] 
> A cikkben található példák feltételezik, hogy hitelesítő adatokat adott meg a Azure Active Directory (Azure AD) használatával.
>
> Ha inkább SAS-jogkivonatot használna a blobadatokhoz való hozzáféréshez, akkor minden AzCopy-parancsban hozzáfűzheti a jogkivonatot az erőforrás URL-címéhez. Példa: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="create-a-container"></a>Tároló létrehozása

A tároló létrehozásához [használhatja az azcopy make](storage-ref-azcopy-make.md) parancsot.

> [!TIP]
> Ez a példa az elérésiút-argumentumokat ad meg egyszeres idézőjelek ('') közé. Használjon egyszeres idézőjeleket az összes parancshéjban, kivéve a Windows parancshéjat (cmd.exe). Ha Windows parancshéjat (cmd.exe) használ, az elérésiút-argumentumokat idézőjelek ("") közé kell tenni a single quotes ('' helyett).

Ez egy példa a táblázatra:

**Syntax**

`azcopy make 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>'`

**Példa**

```azcopy
https://mystorageaccount.blob.core.windows.net/mycontainer
```

**Példa (hierarchikus névtér)**

```azcopy
https://mystorageaccount.dfs.core.windows.net/mycontainer
```

Részletes referencia-dokumentumokért lásd: [azcopy make.](storage-ref-azcopy-make.md)

## <a name="upload-a-file"></a>Fájl feltöltése

Töltsön fel egy fájlt az [azcopy copy paranccsal.](storage-ref-azcopy-copy.md)

> [!TIP]
> Ez a példa az elérésiút-argumentumokat ad meg egyszeres idézőjelek ('') közé. Használjon egyszeres idézőjeleket az összes parancshéjban, kivéve a Windows parancshéjat (cmd.exe). Ha Windows parancshéjat (cmd.exe) használ, az elérésiút-argumentumokat idézőjelek ("") közé kell tenni a single quotes ('' helyett).

**Syntax**

`azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'`

**Példa**

```azcopy
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'
```

**Példa (hierarchikus névtér)**

```azcopy
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt'
```

A fájlokat helyettesítő karakter (*) karakter használatával is feltöltheti a fájl elérési útjának vagy nevének bármely útjára. Például: `'C:\myDirectory\*.txt'` , vagy `C:\my*\*.txt` .

## <a name="upload-a-directory"></a>Könyvtár feltöltése

Töltsön fel egy könyvtárat az [azcopy copy paranccsal.](storage-ref-azcopy-copy.md) 

Ez a példa egy könyvtárat (és az abban lévő összes fájlt) másol egy blobtárolóba. Az eredmény egy azonos nevű könyvtár a tárolóban.

> [!TIP]
> Ez a példa az elérésiút-argumentumokat ad meg egyszeres idézőjelek ('') közé. Használjon egyszeres idézőjeleket az összes parancshéjban, kivéve a Windows parancshéjat (cmd.exe). Ha Windows parancshéjat (cmd.exe) használ, az elérésiút-argumentumokat idézőjelek ("") közé kell tenni a single quotes ('' helyett).

**Syntax**

`azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --recursive`

**Példa**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive
```

**Példa (hierarchikus névtér)**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --recursive
```

A tárolón belüli könyvtárba való másoláshoz egyszerűen adja meg a könyvtár nevét a parancssringben.

**Példa**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --recursive
```

**Példa (hierarchikus névtér)**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' --recursive
```

Ha olyan könyvtár nevét adja meg, amely nem létezik a tárolóban, az AzCopy létrehoz egy új könyvtárat ezen a néven.

## <a name="upload-directory-contents"></a>Könyvtár tartalmának feltöltése

Töltse fel egy könyvtár tartalmát az [azcopy copy paranccsal.](storage-ref-azcopy-copy.md) A helyettesítő karakter (*) szimbólummal anélkül töltheti fel a tartalmat, hogy kimásolódta volna magát a könyvtárat.

> [!TIP]
> Ez a példa az elérésiút-argumentumokat ad meg egyszeres idézőjelek ('') közé. Használjon egyszeres idézőjeleket az összes parancshéjban, kivéve a Windows parancshéjat (cmd.exe). Ha Windows parancshéjat (cmd.exe) használ, az elérésiút-argumentumokat idézőjelek ("") közé kell tenni a single quotes ('' helyett).

**Syntax**

`azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>'` 

**Példa**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory'
```

**Példa (hierarchikus névtér)**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory'
```

Fűzheti `--recursive` hozzá a jelzőt az összes alkönyvtárban lévő fájlok feltöltéséhez.

## <a name="upload-specific-files"></a>Adott fájlok feltöltése

Feltölthet adott fájlokat teljes fájlnevekkel, részleges névkkel és helyettesítő karakterekkel (*), vagy dátumokkal és időpontokkal.

> [!TIP]
> Ezek a példák az elérésiút-argumentumokat egyszeres idézőjelek ('' ) közé ékeik. Használjon egyszeres idézőjeleket az összes parancshéjban, kivéve a Windows parancshéjat (cmd.exe). Ha Windows parancshéjat (cmd.exe) használ, az elérésiút-argumentumokat idézőjelek ("") közé kell tenni a single quotes ('' helyett).

### <a name="specify-multiple-complete-file-names"></a>Több teljes fájlnév megadása

Használja az [azcopy copy](storage-ref-azcopy-copy.md) parancsot a `--include-path` kapcsolóval. Az egyes fájlneveket pontosvesszővel () válassza el `;` egymástól.

**Syntax** 

`azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-path <semicolon-separated-file-list>` 

**Példa**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive'
```

**Példa (hierarchikus névtér)**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive'
```

Ebben a példában az AzCopy továbbítja `C:\myDirectory\photos` a könyvtárat és a `C:\myDirectory\documents\myFile.txt` fájlt. Használja a `--recursive` könyvtárban lévő összes fájl átvitelének `C:\myDirectory\photos` beállítását.

A kapcsolóval fájlokat is `--exclude-path` kizárhat. További információt az azcopy copy reference docs [(Azcopy másolási](storage-ref-azcopy-copy.md) referencia-dokumentumok) tartalmaz.

### <a name="use-wildcard-characters"></a>Helyettesítő karakterek használata

Használja az [azcopy copy](storage-ref-azcopy-copy.md) parancsot a `--include-pattern` kapcsolóval. Adjon meg részleges neveket, amelyek tartalmazzák a helyettesítő karaktereket. A neveket szemikolin () használatával válassza el `;` egymástól. 

**Syntax**

`azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` 

**Példa**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'
```

**Példa (hierarchikus névtér)**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'
```

A kapcsolóval fájlokat is `--exclude-pattern` kizárhat. További információ: azcopy copy reference docs [(Azcopy másolási](storage-ref-azcopy-copy.md) referencia-dokumentumok).

A `--include-pattern` és a beállítás csak a `--exclude-pattern` fájlnevekre vonatkozik, az elérési útra nem.  Ha egy könyvtárfában található összes szövegfájlt ki szeretné másolni, a kapcsolóval le kell kapnia a teljes könyvtárfát, majd a és a használatával le kell kapnia az összes `–recursive` `–include-pattern` `*.txt` szövegfájlt.

### <a name="upload-files-that-were-modified-before-or-after-a-date-and-time"></a>Dátum és idő előtt vagy után módosított fájlok feltöltése 

Használja az [azcopy copy parancsot](storage-ref-azcopy-copy.md) a `--include-before` vagy a `--include-after` kapcsolóval. Adjon meg egy dátumot és egy időpontot ISO-8601 formátumban (például: `2020-08-19T15:04:00Z` ). 

Az alábbi példák a megadott dátumon vagy azt követően módosított fájlokat töltik fel.

**Syntax**

`azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>'  --include-after <Date-Time-in-ISO-8601-format>` 

**Példa**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory'  --include-after '2020-08-19T15:04:00Z'
```

**Példa (hierarchikus névtér)**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory'   --include-after '2020-08-19T15:04:00Z'
```

Részletes referencia: az azcopy copy reference docs [(Azcopy másolási](storage-ref-azcopy-copy.md) referencia-dokumentumok).

## <a name="upload-with-index-tags"></a>Feltöltés indexcímkék használatával

Feltölthet egy fájlt, és [hozzáadhat blobindexcímkéket (előzetes verzió)](../blobs/storage-manage-find-blobs.md) a célblobhoz.  

Ha Azure AD-engedélyezést használ, a rendszerbiztonsági taghoz a [Storage-blobadatok](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) tulajdonosa szerepkört kell hozzárendelni, vagy engedélyt kell adni az Azure-erőforrás-szolgáltatói művelethez egy egyéni `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` [](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) Azure-szerepkör használatával. Ha sas-jogkivonatot használ, a jogkivonatnak SAS-engedéllyel kell hozzáférést adnia a blob `t` címkéihez.

Címkék hozzáadásához használja a lehetőséget `--blob-tags` egy URL-kódolású kulcs-érték párral együtt. Ha például a kulcsot és a értéket szeretné hozzáadni, adja hozzá a értéket `my tag` a `my tag value` `--blob-tags='my%20tag=my%20tag%20value'` célparaméterhez. 

Több indexcímkét és () választ el `&` egymástól.  Ha például egy kulcsot és egy értéket szeretne hozzáadni, a teljes beállítási `my second tag` `my second tag value` sztring a következő lesz: `--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` .

Az alábbi példák a beállítás használatát `--blob-tags` mutatják be.

> [!TIP]
> Ez a példa a path argumentumokat ad meg a egyszeres idézőjelek ('') közé. Használjon egyszeres idézőjelet az összes parancshéjban, kivéve a Windows parancshéjat (cmd.exe). Ha Windows parancshéjat (cmd.exe használ, az elérésiút-argumentumokat dupla idézőjelek ("") közé kell tenni a single quotes ('' helyett).

**Fájl feltöltése**

```azcopy
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'
```

**Könyvtár feltöltése**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'
```

**Könyvtár tartalmának feltöltése**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'
```

> [!NOTE]
> Ha megad egy könyvtárat a forrás számára, akkor a célhelyre másolt összes blob ugyanazokkal a címkékkel fogni, mint a parancsban.

## <a name="upload-with-optional-flags"></a>Feltöltés választható jelzőkvel

A feltöltési műveletet opcionális jelzők használatával finomhangolásra használhatja. Az alábbiakban néhány példát talál.

|Eset|Jelölő|
|---|---|
|Fájlok feltöltése hozzáfűző blobként vagy lapblobként.|**--blob-type** = \[ BlockBlob \| PageBlob \| AppendBlob\]|
|Feltöltés egy adott hozzáférési szintre (például az archív szintre).|**--block-blob-tier** = \[ None \| Hot \| Cool \| Archive\]|

A teljes listát a beállításokat [lásd:](storage-ref-azcopy-copy.md#options).

## <a name="next-steps"></a>Következő lépések

További példákat ezekben a cikkekben talál:

- [Példák: Letöltés](storage-use-azcopy-blobs-download.md)
- [Példák: Másolás tárfiókok között](storage-use-azcopy-blobs-copy.md)
- [Példák: Szinkronizálás](storage-use-azcopy-blobs-synchronize.md)
- [Példák: Amazon S3-gyűjtők](storage-use-azcopy-s3.md)
- [Példák: Google Cloud Storage](storage-use-azcopy-google-cloud.md)
- [Példák: Azure Files](storage-use-azcopy-files.md)
- [Oktatóanyag: Helyszíni adatok migrálása felhőtárhelybe az AzCopyval](storage-use-azcopy-migrate-on-premises-data.md)

A következő cikkekben konfigurálhatja a beállításokat, optimalizálhatja a teljesítményt és elháríthatja a problémákat:

- [AzCopy konfigurációs beállításai](storage-ref-azcopy-configuration-settings.md)
- [Az AzCopy teljesítményének optimalizálása](storage-use-azcopy-optimize.md)
- [Az AzCopy 10-es hibák elhárítása az Azure Storage-ban naplófájlok használatával](storage-use-azcopy-configure.md)