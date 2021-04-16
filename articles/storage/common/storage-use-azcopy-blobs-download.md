---
title: Blobok letöltése az Azure Blob Storage-ból az AzCopy 10-es | Microsoft Docs
description: Ez a cikk az AzCopy példaparancsok gyűjteményét tartalmazza, amelyek segítségével blobokat tölthet le az Azure Blob Storage-ból.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 56bb36cfda9d0cf1a8882950c862a73ad1e77898
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502946"
---
# <a name="download-blobs-from-azure-blob-storage-by-using-azcopy"></a>Blobok letöltése az Azure Blob Storage-ból az AzCopy használatával

A blobokat és könyvtárakat a Blob Storage-ból az AzCopy v10 parancssori segédprogrammal töltheti le. 

Ha más típusú feladatokra, például fájlok feltöltésére, Blob Storage-tárterülettel való szinkronizálásra vagy blobok fiókok közötti másolására vonatkozó példákat keres, tekintse meg a cikk Következő lépések [szakaszában](#next-steps) található hivatkozásokat.

## <a name="get-started"></a>Bevezetés

Az [AzCopy letöltését](storage-use-azcopy-v10.md) az Első lépések az AzCopyval cikkben olvashatja el, és megtudhatja, hogyan biztosítanak hitelesítési hitelesítő adatokat a tárolási szolgáltatásnak.

> [!NOTE] 
> A cikkben található példák feltételezik, hogy hitelesítő adatokat adott meg a Azure Active Directory (Azure AD) használatával.
>
> Ha inkább SAS-jogkivonatot használna a blobadatokhoz való hozzáféréshez, akkor minden AzCopy-parancsban hozzáfűzheti a jogkivonatot az erőforrás URL-címéhez. Példa: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="download-a-blob"></a>Blob letöltése

Töltsön le egy blobot az [azcopy copy paranccsal.](storage-ref-azcopy-copy.md)

> [!TIP]
> Ez a példa az elérésiút-argumentumokat ad meg egyszeres idézőjelek ('') közé. Használjon egyszeres idézőjeleket az összes parancshéjban, kivéve a Windows parancshéjat (cmd.exe). Ha Windows parancshéjat (cmd.exe) használ, az elérésiút-argumentumokat idézőjelek ("") közé kell tenni a single quotes ('' helyett).

**Syntax**

``azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'``

**Példa**

```azcopy
azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'
```

**Példa (hierarchikus névtér)**

```azcopy
azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'
```

> [!NOTE]
> Ha egy blob tulajdonságértéke kivonatot tartalmaz, az AzCopy kiszámít egy MD5-kivonatot a letöltött adatokhoz, és ellenőrzi, hogy a blob tulajdonságában tárolt MD5-kivonat megegyezik-e a számított `Content-md5` `Content-md5` kivonatkal. Ha ezek az értékek nem egyeznek, a letöltés sikertelen lesz, kivéve, ha felülírja ezt a viselkedést a vagy a `--check-md5=NoCheck` `--check-md5=LogOnly` másolási parancs hozzáfűzével.

## <a name="download-a-directory"></a>Könyvtár letöltése

Töltsön le egy könyvtárat az [azcopy copy paranccsal.](storage-ref-azcopy-copy.md)

> [!TIP]
> Ez a példa az elérésiút-argumentumokat ad meg egyszeres idézőjelek ('') közé. Használjon egyszeres idézőjeleket az összes parancshéjban, kivéve a Windows parancshéjat (cmd.exe). Ha Windows parancshéjat (cmd.exe) használ, az elérésiút-argumentumokat idézőjelek ("") közé kell tenni a single quotes ('' helyett).

**Syntax**

`azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>' '<local-directory-path>' --recursive`

**Példa**

```azcopy
azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive
```

**Példa (hierarchikus névtér)**

```azcopy
azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive
```

Ez a példa egy nevű könyvtárat ad vissza, amely az összes `C:\myDirectory\myBlobDirectory` letöltött blobot tartalmazza.

## <a name="download-directory-contents"></a>Könyvtár tartalmának letöltése

Anélkül töltheti le egy könyvtár tartalmát, hogy az adatokat tartalmazó könyvtárat magát átmásolná a csillag helyettesítő karakter (*) használatával.

> [!TIP]
> Ez a példa az elérésiút-argumentumokat ad meg egyszeres idézőjelek ('') közé. Használjon egyszeres idézőjeleket az összes parancshéjban, kivéve a Windows parancshéjat (cmd.exe). Ha Windows parancshéjat (cmd.exe) használ, az elérésiút-argumentumokat idézőjelek ("") közé kell tenni a single quotes ('' helyett).

> [!NOTE]
> Ez a forgatókönyv jelenleg csak olyan fiókok esetében támogatott, amelyek nem hierarchikus névtérhez vannak használva.

**Syntax**

`azcopy copy 'https://<storage-account-name>.blob.core.windows.net/<container-name>/*' '<local-directory-path>/'`

**Példa**

```azcopy
azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*' 'C:\myDirectory'
```

Fűzheti hozzá a jelzőt az összes alkönyvtár `--recursive` fájljainak letöltéséhez.

## <a name="download-specific-blobs"></a>Adott blobok letöltése

Adott blobokat teljes fájlnevek, helyettesítő karaktereket (*)vagy dátumok és időpontok használatával tölthet le. 

> [!TIP]
> Ezek a példák az elérésiút-argumentumokat egyszeres idézőjelek ('' ) közé ékeik. Használjon egyszeres idézőjeleket az összes parancshéjban, kivéve a Windows parancshéjat (cmd.exe). Ha Windows parancshéjat (cmd.exe) használ, az elérésiút-argumentumokat idézőjelek ("") közé kell tenni a single quotes ('' helyett).

#### <a name="specify-multiple-complete-blob-names"></a>Több teljes blobnév megadása

Használja az [azcopy copy](storage-ref-azcopy-copy.md) parancsot a `--include-path` kapcsolóval. Különálló blobnevek elválasztva szemikolin `;` () használatával.

**Syntax**

`azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>`

**Példa**

```azcopy
azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive
```

**Példa (hierarchikus névtér)**

```azcopy
azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt'--recursive
```

Ebben a példában az AzCopy továbbítja `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` a könyvtárat és a `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/documents/myFile.txt` fájlt. Használja a `--recursive` címtárban lévő összes blob átvitelének `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` beállítását.

A blobokat a beállítással is `--exclude-path` kizárhatja. További információ: azcopy copy reference docs [(Azcopy másolási](storage-ref-azcopy-copy.md) referencia-dokumentumok).

#### <a name="use-wildcard-characters"></a>Helyettesítő karakterek használata

Használja az [azcopy copy parancsot](storage-ref-azcopy-copy.md) a `--include-pattern` kapcsolóval. Adjon meg részleges neveket, amelyek tartalmazzák a helyettesítő karaktereket. A neveket szemikolin () használatával válassza el `;` egymástól.

**Syntax**

`azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>`

**Példa**

```azcopy
azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'
```

**Példa (hierarchikus névtér)**

```azcopy
azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'
```

A blobokat a kapcsolóval is `--exclude-pattern` kizárhatja. További információ: azcopy copy reference docs [(Azcopy másolási](storage-ref-azcopy-copy.md) referencia-dokumentumok).

A `--include-pattern` és a beállítás csak a `--exclude-pattern` blobnevekre vonatkozik, az elérési útra nem.  Ha egy könyvtárfában található összes szövegfájlt (blobot) ki szeretné másolni, használja a teljes könyvtárfát lekért beállítást, majd használja a és a értéket az összes szövegfájl lekért `–recursive` `–include-pattern` `*.txt` értékéhez.

#### <a name="download-blobs-that-were-modified-before-or-after-a-date-and-time"></a>Dátum és idő előtt vagy után módosított blobok letöltése 

Használja az [azcopy copy parancsot](storage-ref-azcopy-copy.md) a `--include-before` vagy a `--include-after` kapcsolóval. Adjon meg egy dátumot és egy időpontot ISO-8601 formátumban (például: `2020-08-19T15:04:00Z` ). 

Az alábbi példák a megadott dátumon vagy azt követően módosított fájlokat töltik le.

**Syntax**

`azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>/*' '<local-directory-path>' --include-after <Date-Time-in-ISO-8601-format>`

**Példa**

```azcopy
azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'
```

**Példa (hierarchikus névtér)**

```azcopy
azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'
```

Részletes referencia: az azcopy copy reference docs [(Azcopy másolási](storage-ref-azcopy-copy.md) referencia-dokumentumok).

#### <a name="download-previous-versions-of-a-blob"></a>Blob korábbi verzióinak letöltése

Ha engedélyezte a [blobok verziószámozását,](../blobs/versioning-enable.md)a blobok egy vagy több korábbi verzióját is letöltheti. 

Először hozzon létre egy szövegfájlt, amely tartalmazza a [verziószámok listáját.](../blobs/versioning-overview.md) Minden verzióazonosítónak külön sorban kell megjelennie. Például: 

```
2020-08-17T05:50:34.2199403Z
2020-08-17T05:50:34.5041365Z
2020-08-17T05:50:36.7607103Z
```

Ezután használja az [azcopy copy](storage-ref-azcopy-copy.md) parancsot a `--list-of-versions` kapcsolóval. Adja meg a verziók listáját tartalmazó szövegfájl helyét (például: `D:\\list-of-versions.txt` ).  

#### <a name="download-a-blob-snapshot"></a>Blob-pillanatkép letöltése

A [blob-pillanatképek letöltéséhez](../blobs/snapshots-overview.md) hivatkozhat a blob-pillanatkép **DateTime** értékére. 

**Syntax**

`azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>?sharesnapshot=<DateTime-of-snapshot>' '<local-file-path>'`

**Példa**

```azcopy
azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory\myTextFile.txt'
```

**Példa (hierarchikus névtér)**

```azcopy
azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt?sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory\myTextFile.txt'
```

> [!NOTE]
> Ha SAS-jogkivonatot használ a blobadatokhoz való hozzáféréshez, fűzheti hozzá a pillanatkép **DateTime** dátumát az SAS-jogkivonat után. Példa: `'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z'`.

## <a name="download-with-optional-flags"></a>Letöltés választható jelzőkvel

A letöltési műveletet nem kötelező jelzők használatával finomhangolással is finomhangolni tudja. Látható néhány példa.

|Eset|Jelölő|
|---|---|
|Fájlok automatikus kibontása.|**--decompress**|
|Adja meg, hogy milyen részletes legyen a másolással kapcsolatos naplóbejegyzés.|**--log-level** = \[ FIGYELMEZTETÉSI \| HIBA \| – \| INFORMÁCIÓ: NINCS\]|
|Adja meg, hogy felülírja-e az ütköző fájlokat és blobokat a célhelyen.|**--overwrite** = \[ true \| false \| ifSourceNewer \| prompt\]|

A teljes listát a beállításokat [lásd:](storage-ref-azcopy-copy.md#options).

## <a name="next-steps"></a>Következő lépések

További példákat ezekben a cikkekben talál:

- [Példák: Feltöltés](storage-use-azcopy-blobs-upload.md)
- [Példák: Másolás fiók között](storage-use-azcopy-blobs-copy.md)
- [Példák: Szinkronizálás](storage-use-azcopy-blobs-synchronize.md)
- [Példák: Amazon S3-gyűjtők](storage-use-azcopy-s3.md)
- [Példák: Google Cloud Storage](storage-use-azcopy-google-cloud.md)
- [Példák: Azure Files](storage-use-azcopy-files.md)
- [Oktatóanyag: Helyszíni adatok migrálása felhőtárhelybe az AzCopyval](storage-use-azcopy-migrate-on-premises-data.md)

A következő cikkekben konfigurálhatja a beállításokat, optimalizálhatja a teljesítményt és elháríthatja a problémákat:

- [AzCopy konfigurációs beállításai](storage-ref-azcopy-configuration-settings.md)
- [Az AzCopy teljesítményének optimalizálása](storage-use-azcopy-optimize.md)
- [Az AzCopy 10-es hibák elhárítása az Azure Storage-ban naplófájlok használatával](storage-use-azcopy-configure.md)
