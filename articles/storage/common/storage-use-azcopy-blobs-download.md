---
title: Blobok letöltése az Azure Blob Storage-ból a AzCopy v10 használatával | Microsoft Docs
description: Ez a cikk az Azure Blob Storage-ból származó Blobok letöltését segítő AzCopy-példákat tartalmaz.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/11/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: aa9a415e7bf33409e804fb5503d7b608430098fb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105728912"
---
# <a name="download-blobs-from-azure-blob-storage-by-using-azcopy-v10"></a>Blobok letöltése az Azure Blob Storage-ból az AzCopy v10 használatával

A blob Storage-ból a AzCopy v10 parancssori segédprogram használatával tölthet le blobokat és könyvtárakat. 

Ha más típusú feladatokhoz, például fájlok feltöltéséhez, blob Storage-hoz való szinkronizáláshoz vagy Blobok a fiókok közötti másolásához szeretne példákat látni, tekintse meg a jelen cikk [következő lépések](#next-steps) című szakaszának hivatkozásait.

## <a name="get-started"></a>Bevezetés

Tekintse meg az első [lépések a AzCopy](storage-use-azcopy-v10.md) című cikket a AzCopy letöltéséhez és a tárolási szolgáltatás engedélyezési hitelesítő adatainak megadásához szükséges módszerekről.

> [!NOTE] 
> A cikkben szereplő példák azt feltételezik, hogy Azure Active Directory (Azure AD) használatával adta meg az engedélyezési hitelesítő adatokat.
>
> Ha inkább SAS-tokent használ a blob-adathozzáférés engedélyezéséhez, akkor a tokent az erőforrás URL-címéhez is hozzáfűzheti az egyes AzCopy-parancsokban. Példa: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="download-a-blob"></a>Blob letöltése

Töltsön le egy blobot a [azcopy Copy](storage-ref-azcopy-copy.md) paranccsal.

> [!TIP]
> Ez a példa a Path argumentumokat szimpla idézőjelekkel (' ') fedi le. Használjon egy idézőjelet az összes parancs-rendszerhéjon, kivéve a Windows parancs-rendszerhéjt (cmd.exe). Ha Windows parancs-rendszerhéjt (cmd.exe) használ, az idézőjelek ("") helyett idézőjelek ("") közé foglalja a Path argumentumokat ("").

| Szintaxis/példa  |  Code |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'` |
| **Példa** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |

> [!NOTE]
> Ha `Content-md5` egy blob tulajdonság értéke kivonatot tartalmaz, a AzCopy kiszámítja a letöltött adatokat tartalmazó MD5-kivonatot, és ellenőrzi, hogy a blob tulajdonságában tárolt MD5-kivonat megegyezik-e `Content-md5` a számított kivonattal. Ha ezek az értékek nem egyeznek, a letöltés meghiúsul, ha a hozzáfűzéssel `--check-md5=NoCheck` vagy a másolási paranccsal felülbírálja ezt a viselkedést `--check-md5=LogOnly` .

## <a name="download-a-directory"></a>Könyvtár letöltése

Töltsön le egy könyvtárat a [azcopy Copy](storage-ref-azcopy-copy.md) paranccsal.

> [!TIP]
> Ez a példa a Path argumentumokat szimpla idézőjelekkel (' ') fedi le. Használjon egy idézőjelet az összes parancs-rendszerhéjon, kivéve a Windows parancs-rendszerhéjt (cmd.exe). Ha Windows parancs-rendszerhéjt (cmd.exe) használ, az idézőjelek ("") helyett idézőjelek ("") közé foglalja a Path argumentumokat ("").

| Szintaxis/példa  |  Code |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>' '<local-directory-path>' --recursive` |
| **Példa** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |

Ez a példa egy nevű könyvtárat eredményez, `C:\myDirectory\myBlobDirectory` amely tartalmazza az összes letöltött blobot.

## <a name="download-directory-contents"></a>Könyvtár tartalmának letöltése

Anélkül töltheti le egy könyvtár tartalmát, hogy az adatokat tartalmazó könyvtárat magát átmásolná a csillag helyettesítő karakter (*) használatával.

> [!TIP]
> Ez a példa a Path argumentumokat szimpla idézőjelekkel (' ') fedi le. Használjon egy idézőjelet az összes parancs-rendszerhéjon, kivéve a Windows parancs-rendszerhéjt (cmd.exe). Ha Windows parancs-rendszerhéjt (cmd.exe) használ, az idézőjelek ("") helyett idézőjelek ("") közé foglalja a Path argumentumokat ("").

> [!NOTE]
> Ez a forgatókönyv jelenleg csak olyan fiókok esetében támogatott, amelyek nem rendelkeznek hierarchikus névtérrel.

| Szintaxis/példa  |  Code |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.blob.core.windows.net/<container-name>/*' '<local-directory-path>/'` |
| **Példa** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*' 'C:\myDirectory'` |

Az `--recursive` összes alkönyvtárban található fájlok letöltéséhez fűzze hozzá a jelölőt.

## <a name="download-specific-blobs"></a>Adott Blobok letöltése

Az adott blobokat letöltheti a teljes fájlnevekkel, a részleges neveket helyettesítő karakterekkel (*), vagy dátum és idő használatával. 

> [!TIP]
> Ezek a példák egyetlen idézőjelekkel (' ') foglalják el az elérésiút-argumentumokat. Használjon egy idézőjelet az összes parancs-rendszerhéjon, kivéve a Windows parancs-rendszerhéjt (cmd.exe). Ha Windows parancs-rendszerhéjt (cmd.exe) használ, az idézőjelek ("") helyett idézőjelek ("") közé foglalja a Path argumentumokat ("").

#### <a name="specify-multiple-complete-blob-names"></a>Több teljes blob-név megadására

Használja az [azcopy Copy](storage-ref-azcopy-copy.md) parancsot a `--include-path` kapcsolóval. Az egyes Blobok neveit semicolin () használatával válassza el `;` .

| Szintaxis/példa  |  Code |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Példa** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt'--recursive` |

Ebben a példában a AzCopy átviszi a `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` könyvtárat és a `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/documents/myFile.txt` fájlt. Adja `--recursive` meg az összes blob átadásának lehetőségét a `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` címtárban.

A blobokat a lehetőség használatával is kizárhatja `--exclude-path` . További információért lásd: [azcopy másolási](storage-ref-azcopy-copy.md) dokumentáció.

#### <a name="use-wildcard-characters"></a>Helyettesítő karakterek használata

Használja az [azcopy Copy](storage-ref-azcopy-copy.md) parancsot a `--include-pattern` kapcsolóval. Adjon meg olyan részleges neveket, amelyek tartalmazzák a helyettesítő karaktereket. A neveket a semicolin () használatával válassza el `;` .

| Szintaxis/példa  |  Code |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Példa** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

A blobokat a lehetőség használatával is kizárhatja `--exclude-pattern` . További információért lásd: [azcopy másolási](storage-ref-azcopy-copy.md) dokumentáció.

A `--include-pattern` és a `--exclude-pattern` beállítások csak a Blobok nevére érvényesek, nem az elérési útra.  Ha a címtár fájában található összes szövegfájlt (blobot) szeretné másolni, akkor a `–recursive` teljes könyvtár fájának beolvasásához használja a parancsot, majd a `–include-pattern` és a érték megadásával töltse le az `*.txt` összes szövegfájlt.

#### <a name="download-blobs-that-were-modified-before-or-after-a-date-and-time"></a>A dátum és idő előtt vagy után módosított Blobok letöltése 

Használja az [azcopy Copy](storage-ref-azcopy-copy.md) parancsot a `--include-before` vagy a `--include-after` kapcsolóval. A dátumot és az időt ISO-8601 formátumban kell megadni (például: `2020-08-19T15:04:00Z` ). 

A következő példák a megadott dátumon vagy azt követően módosított fájlokat töltenek le.

| Szintaxis/példa  |  Code |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>/*' '<local-directory-path>' --include-after <Date-Time-in-ISO-8601-format>` |
| **Példa** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'` |

Részletes információk: [azcopy másolási](storage-ref-azcopy-copy.md) dokumentáció.

#### <a name="download-previous-versions-of-a-blob"></a>BLOB korábbi verzióinak letöltése

Ha engedélyezte a [Blobok verziószámozását](../blobs/versioning-enable.md), letöltheti a Blobok egy vagy több korábbi verzióját is. 

Először hozzon létre egy szövegfájlt, amely tartalmazza a [verzió-azonosítók](../blobs/versioning-overview.md)listáját. Minden verzióazonosítónak külön sorban kell szerepelnie. Például: 

```
2020-08-17T05:50:34.2199403Z
2020-08-17T05:50:34.5041365Z
2020-08-17T05:50:36.7607103Z
```

Ezután használja a [azcopy Copy](storage-ref-azcopy-copy.md) parancsot a `--list-of-versions` kapcsolóval. Itt adhatja meg a verziók listáját tartalmazó szövegfájl helyét (például: `D:\\list-of-versions.txt` ).  

#### <a name="download-a-blob-snapshot"></a>BLOB-pillanatkép letöltése

A [blob-](../blobs/snapshots-overview.md) Pillanatképek a blob-pillanatkép **datetime** értékére hivatkozva tölthetők le. 

| Szintaxis/példa  |  Code |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>?sharesnapshot=<DateTime-of-snapshot>' '<local-file-path>'` |
| **Példa** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory\myTextFile.txt'` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt?sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory\myTextFile.txt'` |

> [!NOTE]
> Ha SAS-tokent használ a blob-adatokhoz való hozzáférés engedélyezéséhez, akkor az SAS-tokent követően adja hozzá a Snapshot **datetime** értéket. Példa: `'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z'`.

## <a name="download-with-optional-flags"></a>Letöltés opcionális jelzővel

A letöltési műveletet opcionális jelzők használatával is megteheti. Íme néhány példa.

|Eset|Jelölő|
|---|---|
|Fájlok automatikus kibontása.|**– Kibontás**|
|Adja meg, hogy a másolással kapcsolatos naplóbejegyzések milyen részletesek legyenek.|**– naplózási szint** = \[ FIGYELMEZTETÉSi \| hiba – \| \| nincs\]|
|Itt adhatja meg, hogy miként írja felül az ütköző fájlokat és blobokat a célhelyen.|**– felülírás** = \[ igaz \| hamis \| ifSourceNewer- \| kérés\]|

A teljes listát itt tekintheti meg: [Beállítások](storage-ref-azcopy-copy.md#options).

## <a name="next-steps"></a>Következő lépések

További példákat a következő cikkekben talál:

- [Példák: Feltöltés](storage-use-azcopy-blobs-upload.md)
- [Példák: másolás a fiók között](storage-use-azcopy-blobs-copy.md)
- [Példák: Szinkronizálás](storage-use-azcopy-blobs-synchronize.md)
- [Példák: Amazon S3-gyűjtők](storage-use-azcopy-s3.md)
- [Példák: Azure Files](storage-use-azcopy-files.md)
- [Oktatóanyag: Helyszíni adatok migrálása felhőtárhelybe az AzCopyval](storage-use-azcopy-migrate-on-premises-data.md)
- [AzCopy konfigurálása, optimalizálása és megoldása](storage-use-azcopy-configure.md)