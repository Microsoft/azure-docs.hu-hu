---
title: Adatok átvitele Azure Files-re a AzCopy v10 használatával | Microsoft Docs
description: Adatok átvitele a AzCopy és a file Storage szolgáltatással. A AzCopy egy parancssori eszköz, amely a blobokat vagy fájlokat egy Storage-fiókba másolja vagy onnan másolja. A AzCopy használata a Azure Files használatával.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/08/2020
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 2cca8a93330e5ddd965d27532895ed1d6702c123
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102179646"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>Adatok átvitele az AzCopy használatával és fájltárolás 

A AzCopy egy parancssori segédprogram, amellyel fájlokat másolhat egy Storage-fiókba, vagy onnan is. Ez a cikk a Azure Filesekkel használható példákat tartalmaz.

Mielőtt elkezdené, tekintse meg az első [lépések a AzCopy](storage-use-azcopy-v10.md) című cikket a AzCopy letöltéséhez és az eszköz megismeréséhez.

> [!TIP]
> A cikkben szereplő példák egyetlen idézőjelekkel (' ') rendelkeznek a Path argumentumokkal. Használjon egy idézőjelet az összes parancs-rendszerhéjon, kivéve a Windows parancs-rendszerhéjt (cmd.exe). Ha Windows parancs-rendszerhéjt (cmd.exe) használ, az idézőjelek ("") helyett idézőjelek ("") közé foglalja a Path argumentumokat ("").

## <a name="create-file-shares"></a>Fájlmegosztások létrehozása

A [azcopy make](storage-ref-azcopy-make.md) paranccsal fájlmegosztást hozhat létre. Az ebben a szakaszban szereplő példa egy nevű fájlmegosztást hoz létre `myfileshare` .

|    |     |
|--------|-----------|
| **Syntax** | `azcopy make 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>'` |
| **Példa** | `azcopy make 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

A részletes dokumentációt a következő témakörben tekintheti meg: [azcopy make](storage-ref-azcopy-make.md).

## <a name="upload-files"></a>Fájlok feltöltése

A [azcopy másolás](storage-ref-azcopy-copy.md) parancs használatával fájlokat és könyvtárakat tölthet fel a helyi számítógépről.

Ez a szakasz a következő példákat tartalmazza:

> [!div class="checklist"]
> * Fájl feltöltése
> * Könyvtár feltöltése
> * Könyvtár tartalmának feltöltése
> * Adott fájl feltöltése

> [!TIP]
> A feltöltési műveletet opcionális jelzők használatával is megteheti. Íme néhány példa.
>
> |Eset|Jelölő|
> |---|---|
> |A hozzáférés-vezérlési listák (ACL-ek) másolása a fájlokkal együtt.|**--megőrzése-SMB-engedélyek** = \[ igaz \| hamis\]|
> |Az SMB-tulajdonságok adatainak másolása a fájlokkal együtt.|**--az SMB-info megőrzése** = \[ igaz \| hamis\]|
> 
> A teljes listát itt tekintheti meg: [Beállítások](storage-ref-azcopy-copy.md#options).

> [!NOTE]
> A AzCopy nem számítja ki automatikusan a fájl MD5 kivonatoló kódját. Ha azt szeretné, hogy a AzCopy ezt a lehetőséget, fűzze hozzá a `--put-md5` jelölőt az egyes másolási parancsokhoz. Így a fájl letöltésekor a AzCopy kiszámítja a letöltött adatokat tartalmazó MD5-kivonatot, és ellenőrzi, hogy a fájl tulajdonságában tárolt MD5-kivonat megegyezik-e `Content-md5` a számított kivonattal.

### <a name="upload-a-file"></a>Fájl feltöltése

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name><SAS-token>'` |
| **Példa** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

A fájl elérési útja vagy fájlneve tetszőleges helyettesítő karakter (*) használatával is feltölthet egy fájlt. Például: `'C:\myDirectory\*.txt'` , vagy `C:\my*\*.txt` .

### <a name="upload-a-directory"></a>Könyvtár feltöltése

Ebben a példában egy könyvtárat (és az abban a könyvtárban található összes fájlt) másolunk egy fájlmegosztásba. Az eredmény egy azonos nevű könyvtár lesz a fájlmegosztásban.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Példa** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

Ha a fájlmegosztást tartalmazó könyvtárba szeretne másolni, csak adja meg a könyvtár nevét a parancs karakterláncában.

|    |     |
|--------|-----------|
| **Példa** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

Ha egy olyan könyvtár nevét adja meg, amely nem szerepel a fájlmegosztást, a AzCopy létrehoz egy új könyvtárat az adott névvel.

### <a name="upload-the-contents-of-a-directory"></a>Könyvtár tartalmának feltöltése

A könyvtár tartalmát feltöltheti anélkül, hogy a benne foglalt könyvtárat a helyettesítő karakter (*) használatával másolja.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>/*' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>` |
| **Példa** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> Fűzze hozzá a `--recursive` jelölőt az összes alkönyvtárban található fájlok feltöltéséhez.

### <a name="upload-specific-files"></a>Adott fájlok feltöltése

Az adott fájlokat feltöltheti a teljes fájlnevekkel, a részleges nevekkel, helyettesítő karakterekkel (*), vagy dátum és idő használatával.

#### <a name="specify-multiple-complete-file-names"></a>Több teljes fájlnevet kell megadni

Használja az [azcopy Copy](storage-ref-azcopy-copy.md) parancsot a `--include-path` kapcsolóval. Az egyes fájlneveket pontosvesszővel () válassza el egymástól `;` .

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-path <semicolon-separated-file-list>` |
| **Példa** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-path 'photos;documents\myFile.txt'` |

Ebben a példában a AzCopy átviszi a `C:\myDirectory\photos` könyvtárat és a `C:\myDirectory\documents\myFile.txt` fájlt. Meg kell adnia a `--recursive` könyvtárban található összes fájl átvitelének lehetőségét `C:\myDirectory\photos` .

A fájlokat a lehetőség használatával is kizárhatja `--exclude-path` . További információért lásd: [azcopy másolási](storage-ref-azcopy-copy.md) dokumentáció.

#### <a name="use-wildcard-characters"></a>Helyettesítő karakterek használata

Használja az [azcopy Copy](storage-ref-azcopy-copy.md) parancsot a `--include-pattern` kapcsolóval. Adjon meg olyan részleges neveket, amelyek tartalmazzák a helyettesítő karaktereket. A neveket pontosvesszővel () válassza el `;` .

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Példa** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-pattern 'myFile*.txt;*.pdf*'` |

A fájlokat a lehetőség használatával is kizárhatja `--exclude-pattern` . További információért lásd: [azcopy másolási](storage-ref-azcopy-copy.md) dokumentáció.

A `--include-pattern` és a `--exclude-pattern` beállítások csak a fájlnevekre érvényesek, és nem az elérési útra.  Ha egy címtár fájában található összes szövegfájlt szeretné átmásolni, használja a `--recursive` teljes könyvtár fájának beolvasására szolgáló lehetőséget, majd a `--include-pattern` és a érték megadásával töltse le az `*.txt` összes szövegfájlt.

#### <a name="upload-files-that-were-modified-after-a-date-and-time"></a>A dátum és idő után módosított fájlok feltöltése 

Használja az [azcopy Copy](storage-ref-azcopy-copy.md) parancsot a `--include-after` kapcsolóval. A dátumot és az időt ISO 8601 formátumban kell megadni (például: `2020-08-19T15:04:00Z` ). 

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>'  --include-after <Date-Time-in-ISO-8601-format>` |
| **Példa** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-after '2020-08-19T15:04:00Z'` |

Részletes információk: [azcopy másolási](storage-ref-azcopy-copy.md) dokumentáció.

## <a name="download-files"></a>Fájlok letöltése

A [azcopy másolás](storage-ref-azcopy-copy.md) parancs használatával fájlokat, címtárakat és fájlmegosztást tölthet le a helyi számítógépre.

Ez a szakasz a következő példákat tartalmazza:

> [!div class="checklist"]
> * Fájl letöltése
> * Könyvtár letöltése
> * Könyvtár tartalmának letöltése
> * Adott fájlok letöltése

> [!TIP]
> A letöltési műveletet opcionális jelzők használatával is megteheti. Íme néhány példa.
>
> |Eset|Jelölő|
> |---|---|
> |A hozzáférés-vezérlési listák (ACL-ek) másolása a fájlokkal együtt.|**--megőrzése-SMB-engedélyek** = \[ igaz \| hamis\]|
> |Az SMB-tulajdonságok adatainak másolása a fájlokkal együtt.|**--az SMB-info megőrzése** = \[ igaz \| hamis\]|
> |Fájlok automatikus kibontása.|**– Kibontás**|
> 
> A teljes listát itt tekintheti meg: [Beállítások](storage-ref-azcopy-copy.md#options).

> [!NOTE]
> Ha `Content-md5` egy fájl tulajdonságérték kivonatot tartalmaz, a AzCopy kiszámítja a letöltött adatokat tartalmazó MD5-kivonatot, és ellenőrzi, hogy a fájl tulajdonságában tárolt MD5-kivonat megegyezik-e `Content-md5` a számított kivonattal. Ha ezek az értékek nem egyeznek, a letöltés meghiúsul, ha a hozzáfűzéssel `--check-md5=NoCheck` vagy a másolási paranccsal felülbírálja ezt a viselkedést `--check-md5=LogOnly` .

### <a name="download-a-file"></a>Fájl letöltése

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' '<local-file-path>'` |
| **Példa** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>Könyvtár letöltése

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' '<local-directory-path>' --recursive` |
| **Példa** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'  --recursive` |

Ez a példa egy nevű könyvtárat eredményez, `C:\myDirectory\myFileShareDirectory` amely az összes letöltött fájlt tartalmazza.

### <a name="download-the-contents-of-a-directory"></a>Könyvtár tartalmának letöltése

Anélkül töltheti le egy könyvtár tartalmát, hogy az adatokat tartalmazó könyvtárat magát átmásolná a csillag helyettesítő karakter (*) használatával.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/*<SAS-token>' '<local-directory-path>/'` |
| **Példa** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'` |

> [!NOTE]
> Az `--recursive` összes alkönyvtárban lévő fájlok letöltéséhez fűzze hozzá a jelölőt.

### <a name="download-specific-files"></a>Adott fájlok letöltése

Az adott fájlokat letöltheti a teljes fájlnevekkel, a részleges nevekkel, helyettesítő karakterekkel (*), vagy dátum és idő használatával.

#### <a name="specify-multiple-complete-file-names"></a>Több teljes fájlnevet kell megadni

Használja az [azcopy Copy](storage-ref-azcopy-copy.md) parancsot a `--include-path` kapcsolóval. Az egyes fájlneveket pontosvesszővel () válassza el egymástól `;` .

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Példa** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |

Ebben a példában a AzCopy átviszi a `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` könyvtárat és a `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/documents/myFile.txt` fájlt. Adja `--recursive` meg a könyvtárban található összes fájl átvitelének lehetőségét `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` .

A fájlokat a lehetőség használatával is kizárhatja `--exclude-path` . További információért lásd: [azcopy másolási](storage-ref-azcopy-copy.md) dokumentáció.

#### <a name="use-wildcard-characters"></a>Helyettesítő karakterek használata

Használja az [azcopy Copy](storage-ref-azcopy-copy.md) parancsot a `--include-pattern` kapcsolóval. Adjon meg olyan részleges neveket, amelyek tartalmazzák a helyettesítő karaktereket. A neveket pontosvesszővel () válassza el `;` .

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Példa** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

A fájlokat a lehetőség használatával is kizárhatja `--exclude-pattern` . További információért lásd: [azcopy másolási](storage-ref-azcopy-copy.md) dokumentáció.

A `--include-pattern` és a `--exclude-pattern` beállítások csak a fájlnevekre érvényesek, és nem az elérési útra.  Ha egy címtár fájában található összes szövegfájlt szeretné átmásolni, használja a `--recursive` teljes könyvtár fájának beolvasására szolgáló lehetőséget, majd a `--include-pattern` és a érték megadásával töltse le az `*.txt` összes szövegfájlt.

#### <a name="download-files-that-were-modified-after-a-date-and-time"></a>A dátum és idő után módosított fájlok letöltése 

Használja az [azcopy Copy](storage-ref-azcopy-copy.md) parancsot a `--include-after` kapcsolóval. A dátumot és az időt ISO-8601 formátumban kell megadni (például: `2020-08-19T15:04:00Z` ). 

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name>/*<SAS-token>' '<local-directory-path>'  --include-after <Date-Time-in-ISO-8601-format>` |
| **Példa** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/*?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory' --include-after '2020-08-19T15:04:00Z'` |


Részletes információk: [azcopy másolási](storage-ref-azcopy-copy.md) dokumentáció.

#### <a name="download-from-a-share-snapshot"></a>Letöltés megosztási pillanatképből

Egy fájl vagy könyvtár adott verzióját a megosztási pillanatkép **datetime** értékére hivatkozva töltheti le. További információ a megosztási pillanatképekről: [Azure Files-megosztási Pillanatképek áttekintése](../files/storage-snapshots-files.md). 

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path-or-directory-name><SAS-token>&sharesnapshot=<DateTime-of-snapshot>' '<local-file-or-directory-path>'` |
| **Példa** (fájl letöltése) | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory\myTextFile.txt'` |
| **Példa** (könyvtár letöltése) | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory'  --recursive`|


## <a name="copy-files-between-storage-accounts"></a>Fájlok másolása tárfiókok között

A AzCopy használatával fájlokat másolhat más Storage-fiókokba. A másolási művelet szinkron módon megy végbe, ezért a parancs visszatérése azt jelzi, hogy a fájlok másolása megtörtént.

A AzCopy [kiszolgálók](/rest/api/storageservices/put-block-from-url) közötti [API](/rest/api/storageservices/put-page-from-url)-kat használ, így az Adatmásolás közvetlenül a Storage-kiszolgálók között történik. Ezek a másolási műveletek nem használják a számítógép hálózati sávszélességét. A környezeti változó értékének megadásával növelheti a műveletek átviteli sebességét `AZCOPY_CONCURRENCY_VALUE` . További információ: az [átviteli sebesség optimalizálása](storage-use-azcopy-configure.md#optimize-throughput).

A fájlok adott verzióit a megosztási pillanatkép **datetime** értékére hivatkozva is másolhatja. További információ a megosztási pillanatképekről: [Azure Files-megosztási Pillanatképek áttekintése](../files/storage-snapshots-files.md). 

Ez a szakasz a következő példákat tartalmazza:

> [!div class="checklist"]
> * Fájl másolása másik Storage-fiókba
> * Könyvtár másolása másik Storage-fiókba
> * Fájlmegosztás másolása másik Storage-fiókba
> * Az összes fájlmegosztás, könyvtár és fájl másolása egy másik tárfiókba

> [!TIP]
> A másolási műveletet opcionális jelzők használatával is megadhatja. Íme néhány példa.
>
> |Eset|Jelölő|
> |---|---|
> |A hozzáférés-vezérlési listák (ACL-ek) másolása a fájlokkal együtt.|**--megőrzése-SMB-engedélyek** = \[ igaz \| hamis\]|
> |Az SMB-tulajdonságok adatainak másolása a fájlokkal együtt.|**--az SMB-info megőrzése** = \[ igaz \| hamis\]|
> 
> A teljes listát itt tekintheti meg: [Beállítások](storage-ref-azcopy-copy.md#options).

### <a name="copy-a-file-to-another-storage-account"></a>Fájl másolása másik Storage-fiókba

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>'` |
| **Példa** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D'` |
| **Példa** (pillanatkép megosztása) | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D'` |

### <a name="copy-a-directory-to-another-storage-account"></a>Könyvtár másolása másik Storage-fiókba

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Példa** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/myFileShare/myFileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |
| **Példa** (pillanatkép megosztása) | `azcopy copy 'https://mysourceaccount.file.core.windows.net/myFileShare/myFileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |


### <a name="copy-a-file-share-to-another-storage-account"></a>Fájlmegosztás másolása másik Storage-fiókba

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Példa** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |
| **Példa** (pillanatkép megosztása) | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |


### <a name="copy-all-file-shares-directories-and-files-to-another-storage-account"></a>Az összes fájlmegosztás, könyvtár és fájl másolása egy másik tárfiókba

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<SAS-token>' --recursive'` |
| **Példa** | `azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |
| **Példa** (pillanatkép megosztása) | `azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |


## <a name="synchronize-files"></a>Fájlok szinkronizálása

Egy fájlmegosztás tartalmát egy másik fájlmegosztást is szinkronizálhatja. Egy fájlmegosztás könyvtárának tartalmát is szinkronizálhatja egy olyan könyvtár tartalmával, amely egy másik fájlmegosztást tartalmaz. A szinkronizálás egy módszer. Más szóval kiválaszthatja, hogy a két végpont melyik a forrás, és melyik a cél. A szinkronizálás a kiszolgálót kiszolgáló API-kra is használja.

> [!NOTE]
> Ez a forgatókönyv jelenleg csak olyan fiókok esetében támogatott, amelyek nem rendelkeznek hierarchikus névtérrel. A AzCopy jelenlegi kiadása nem szinkronizál Azure Files és Blob Storage között.

A [szinkronizálási](storage-ref-azcopy-sync.md) parancs összehasonlítja a fájlneveket és az utolsó módosítás időbélyegét. Állítsa a `--delete-destination` választható jelzőt értékre, `true` vagy `prompt` törölje a fájlokat a célhely könyvtárában, ha ezek a fájlok már nem léteznek a forrás-címtárban.

Ha a `--delete-destination` jelölőt a értékre állítja `true` , a AzCopy a fájlokat a parancssor megadása nélkül törli. Ha azt szeretné, hogy a AzCopy törlése előtt megjelenjen egy üzenet, állítsa a jelölőt a következőre: `--delete-destination` `prompt` .

> [!TIP]
> A szinkronizálási műveletet opcionális jelzők használatával is megadhatja. Íme néhány példa.
>
> |Eset|Jelölő|
> |---|---|
> |A hozzáférés-vezérlési listák (ACL-ek) másolása a fájlokkal együtt.|**--megőrzése-SMB-engedélyek** = \[ igaz \| hamis\]|
> |Az SMB-tulajdonságok adatainak másolása a fájlokkal együtt.|**--az SMB-info megőrzése** = \[ igaz \| hamis\]|
> |Fájlok kizárása mintázat alapján.|**--kizárás – elérési út**|
> |Adja meg, hogy a szinkronizálással kapcsolatos naplóbejegyzések milyen részletesek legyenek.|**– naplózási szint** = \[ FIGYELMEZTETÉSi \| hiba – \| \| nincs\]|
> 
> A teljes listát itt tekintheti meg: [Beállítások](storage-ref-azcopy-sync.md#options).

### <a name="update-a-file-share-with-changes-to-another-file-share"></a>Fájlmegosztás frissítése egy másik fájlmegosztás módosításaival

Az ebben a parancsban megjelenő első fájlmegosztás a forrás. A második a cél.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Példa** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Könyvtár frissítése egy másik fájlmegosztás könyvtárának módosításaival

A parancsban megjelenő első könyvtár a forrás. A második a cél.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' --recursive` |
| **Példa** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="update-a-file-share-to-match-the-contents-of-a-share-snapshot"></a>Fájlmegosztás frissítése a megosztási pillanatkép tartalmának megfelelően

Az ebben a parancsban megjelenő első fájlmegosztás a forrás. Az URI végén fűzze hozzá a karakterláncot, `&sharesnapshot=` majd a pillanatkép **datetime** értékét. 

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>&sharesnapsot<snapshot-ID>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Példa** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-03-03T20%3A24%3A13.0000000Z' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

További információ a megosztási pillanatképekről: [Azure Files-megosztási Pillanatképek áttekintése](../files/storage-snapshots-files.md).

## <a name="next-steps"></a>Következő lépések

További példákat a következő cikkekben talál:

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)

- [Adatátvitel](storage-use-azcopy-v10.md#transfer-data)

- [AzCopy konfigurálása, optimalizálása és megoldása](storage-use-azcopy-configure.md)
