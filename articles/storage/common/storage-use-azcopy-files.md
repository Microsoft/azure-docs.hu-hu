---
title: Adatok átvitele a Azure Files az AzCopy 10-es | Microsoft Docs
description: Adatok átvitele az AzCopy és a fájltároló használatával. Az AzCopy egy parancssori eszköz blobok vagy fájlok tárfiókba vagy tárfiókból való másolására. Az AzCopy használata Azure Files.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 04bf84c5dc8a63929900f1bc95d7274074ef9d5a
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107498356"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>Adatok átvitele az AzCopy használatával és fájltárolás 

Az AzCopy egy parancssori segédprogram, amely fájlok tárfiókba vagy tárfiókból való másolására használható. Ez a cikk példaparancsokat tartalmaz, amelyek a Azure Files.

Mielőtt hozzákezd, tekintse meg az AzCopy használatának első lépésekről cikket az [AzCopy](storage-use-azcopy-v10.md) letöltéséhez és az eszköz használatának megismeréséhez.

> [!TIP]
> Az ebben a cikkben található példák az elérésiút-argumentumokat akkénti idézőjelek ('' ) közé ékelik. Használjon egyszeres idézőjeleket az összes parancshéjban, kivéve a Windows parancshéjat (cmd.exe). Ha Windows parancshéjat (cmd.exe) használ, az elérésiút-argumentumokat idézőjelek ("") közé kell tenni a single quotes ('' helyett).

## <a name="create-file-shares"></a>Fájlmegosztások létrehozása

Az [azcopy make](storage-ref-azcopy-make.md) paranccsal hozhat létre fájlmegosztást. Az ebben a szakaszban található példa egy nevű fájlmegosztást hoz `myfileshare` létre.

**Syntax**

`azcopy make 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>'`

**Példa**

```azcopy
azcopy make 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'
```

Részletes referencia-dokumentumokért lásd: [azcopy make.](storage-ref-azcopy-make.md)

## <a name="upload-files"></a>Fájlok feltöltése

Az [azcopy copy](storage-ref-azcopy-copy.md) paranccsal fájlokat és könyvtárakat tölthet fel a helyi számítógépről.

Ez a szakasz a következő példákat tartalmazza:

> [!div class="checklist"]
> * Fájl feltöltése
> * Könyvtár feltöltése
> * Könyvtár tartalmának feltöltése
> * Adott fájl feltöltése

> [!TIP]
> A feltöltési műveletet nem kötelező jelzők használatával finomhangolással is finomhangolni tudja. Látható néhány példa.
>
> |Eset|Jelölő|
> |---|---|
> |Másolja a hozzáférés-vezérlési listákat (ACL-eket) a fájlokkal együtt.|**--preserve-smb-permissions** = \[ true \| false (igaz hamis)\]|
> |Másolja ki az SMB-tulajdonságinformációkat a fájlokkal együtt.|**--preserve-smb-info** = \[ true \| false (igaz hamis)\]|
> 
> A teljes listát a beállításokat [lásd:](storage-ref-azcopy-copy.md#options).

> [!NOTE]
> Az AzCopy nem számítja ki és tárolja automatikusan a fájl md5 kivonatkódját. Ha azt szeretné, hogy az AzCopy ezt tegye, fűzheti hozzá a `--put-md5` jelzőt az egyes másolási parancsokhoz. Így a fájl letöltésekor az AzCopy kiszámítja a letöltött adatok MD5-kivonatát, és ellenőrzi, hogy a fájl tulajdonságában tárolt MD5-kivonat megfelel-e a számított `Content-md5` kivonatnak.

### <a name="upload-a-file"></a>Fájl feltöltése

**Syntax**

`azcopy copy '<local-file-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name><SAS-token>'`

**Példa**

```azcopy
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'
```

A fájlokat helyettesítő karakter (*) karakter használatával is feltöltheti a fájl elérési útjának vagy nevének bármely útjára. Például: `'C:\myDirectory\*.txt'` , vagy `C:\my*\*.txt` .

### <a name="upload-a-directory"></a>Könyvtár feltöltése

Ebben a példában egy könyvtárat (és az abban a könyvtárban található összes fájlt) másolunk egy fájlmegosztásba. Az eredmény egy azonos nevű könyvtár lesz a fájlmegosztásban.

**Syntax**

`azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive`

**Példa**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive
```

A fájlmegosztáson belüli könyvtárba való másoláshoz egyszerűen adja meg a könyvtár nevét a parancssringben.

**Példa**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive
```

Ha olyan könyvtár nevét adja meg, amely nem létezik a fájlmegosztásban, az AzCopy létrehoz egy új könyvtárat ezen a néven.

### <a name="upload-the-contents-of-a-directory"></a>Könyvtár tartalmának feltöltése

A könyvtár tartalmát anélkül töltheti fel, hogy az azt tartalmazó könyvtárat lemásolja a helyettesítő karakteres szimbólum (*)használatával.

**Syntax**

`azcopy copy '<local-directory-path>/*' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>`

**Példa**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"
```

> [!NOTE]
> Fűzheti hozzá a jelzőt az összes al `--recursive` könyvtárban lévő fájlok feltöltéséhez.

### <a name="upload-specific-files"></a>Adott fájlok feltöltése

Feltölthet adott fájlokat teljes fájlnevekkel, részleges névkkel és helyettesítő karakterekkel (*), vagy dátumokkal és időpontokkal.

#### <a name="specify-multiple-complete-file-names"></a>Több teljes fájlnév megadása

Használja az [azcopy copy](storage-ref-azcopy-copy.md) parancsot a `--include-path` kapcsolóval. Az egyes fájlneveket pontosvesszővel () válassza el `;` egymástól.

**Syntax**

`azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-path <semicolon-separated-file-list>`

**Példa**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-path 'photos;documents\myFile.txt'
```

Ebben a példában az AzCopy a `C:\myDirectory\photos` könyvtárat és a fájlt `C:\myDirectory\documents\myFile.txt` továbbítja. Meg kell tartalmaznia a könyvtárban lévő `--recursive` összes fájl átvitelének `C:\myDirectory\photos` beállítását.

A kapcsolóval fájlokat is `--exclude-path` kizárhat. További információ: azcopy copy reference docs [(Azcopy másolási](storage-ref-azcopy-copy.md) referencia-dokumentumok).

#### <a name="use-wildcard-characters"></a>Helyettesítő karakterek használata

Használja az [azcopy copy parancsot](storage-ref-azcopy-copy.md) a `--include-pattern` kapcsolóval. Adjon meg részleges neveket, amelyek tartalmazzák a helyettesítő karaktereket. A neveket pontosvesszővel () válassza el `;` egymástól.

**Syntax**

`azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>`

**Példa**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-pattern 'myFile*.txt;*.pdf*'
```

A kapcsolóval fájlokat is `--exclude-pattern` kizárhat. További információ: azcopy copy reference docs [(Azcopy másolási](storage-ref-azcopy-copy.md) referencia-dokumentumok).

A `--include-pattern` és a beállítás csak a `--exclude-pattern` fájlnevekre vonatkozik, az elérési útra nem.  Ha egy könyvtárfában található összes szövegfájlt ki szeretné másolni, a kapcsolóval le kell kapnia a teljes könyvtárfát, majd a és a használatával le kell kapnia az összes `--recursive` `--include-pattern` `*.txt` szövegfájlt.

#### <a name="upload-files-that-were-modified-after-a-date-and-time"></a>Dátum és idő után módosított fájlok feltöltése 

Használja az [azcopy copy parancsot](storage-ref-azcopy-copy.md) a `--include-after` kapcsolóval. Adjon meg egy dátumot és egy időpontot ISO 8601 formátumban (például: `2020-08-19T15:04:00Z` ). 

**Syntax**

`azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>'  --include-after <Date-Time-in-ISO-8601-format>`

**Példa**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-after '2020-08-19T15:04:00Z'
```

Részletes referencia: az azcopy copy reference docs [(Azcopy másolási](storage-ref-azcopy-copy.md) referencia-dokumentumok).

## <a name="download-files"></a>Fájlok letöltése

Az [azcopy copy](storage-ref-azcopy-copy.md) paranccsal fájlokat, könyvtárakat és fájlmegosztásokat tölthet le a helyi számítógépre.

Ez a szakasz a következő példákat tartalmazza:

> [!div class="checklist"]
> * Fájl letöltése
> * Könyvtár letöltése
> * Könyvtár tartalmának letöltése
> * Adott fájlok letöltése

> [!TIP]
> A letöltési műveletet nem kötelező jelzők használatával finomhangolással is finomhangolni tudja. Látható néhány példa.
>
> |Eset|Jelölő|
> |---|---|
> |Másolja a hozzáférés-vezérlési listákat (ACL-eket) a fájlokkal együtt.|**--preserve-smb-permissions** = \[ true \| false (igaz hamis)\]|
> |Másolja ki az SMB-tulajdonságinformációkat a fájlokkal együtt.|**--preserve-smb-info** = \[ true \| false (igaz hamis)\]|
> |Fájlok automatikus kibontása.|**--decompress**|
> 
> A teljes listát a beállításokat [lásd:](storage-ref-azcopy-copy.md#options).

> [!NOTE]
> Ha egy fájl tulajdonságértéke kivonatot tartalmaz, az AzCopy kiszámítja a letöltött adatok MD5-kivonatát, és ellenőrzi, hogy a fájl tulajdonságában tárolt MD5-kivonat megfelel-e a számított `Content-md5` `Content-md5` kivonatnak. Ha ezek az értékek nem egyeznek, a letöltés sikertelen lesz, kivéve, ha felülírja ezt a viselkedést a vagy a `--check-md5=NoCheck` `--check-md5=LogOnly` másolási parancs hozzáfűzével.

### <a name="download-a-file"></a>Fájl letöltése

**Syntax**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' '<local-file-path>'`

**Példa**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory\myTextFile.txt'
```

### <a name="download-a-directory"></a>Könyvtár letöltése

**Syntax**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' '<local-directory-path>' --recursive`

**Példa**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'  --recursive
```

Ez a példa egy nevű könyvtárat ad `C:\myDirectory\myFileShareDirectory` vissza, amely az összes letöltött fájlt tartalmazza.

### <a name="download-the-contents-of-a-directory"></a>Könyvtár tartalmának letöltése

Anélkül töltheti le egy könyvtár tartalmát, hogy az adatokat tartalmazó könyvtárat magát átmásolná a csillag helyettesítő karakter (*) használatával.

**Syntax**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/*<SAS-token>' '<local-directory-path>/'`

**Példa**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'
```

> [!NOTE]
> Fűzheti hozzá a jelzőt az összes al `--recursive` könyvtárban lévő fájlok letöltéséhez.

### <a name="download-specific-files"></a>Adott fájlok letöltése

Adott fájlokat teljes fájlnevek, helyettesítő karaktereket (*)vagy dátumok és időpontok használatával tölthet le.

#### <a name="specify-multiple-complete-file-names"></a>Több teljes fájlnév megadása

Használja az [azcopy copy parancsot](storage-ref-azcopy-copy.md) a `--include-path` kapcsolóval. Az egyes fájlneveket pontosvesszővel () válassza el `;` egymástól.

**Syntax**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>`

**Példa**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive
```

Ebben a példában az AzCopy a `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` könyvtárat és a fájlt `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/documents/myFile.txt` továbbítja. Használja a `--recursive` könyvtárban található összes fájl átvitelének `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` beállítását.

A kapcsolóval fájlokat is `--exclude-path` kizárhat. További információ: azcopy copy reference docs [(Azcopy másolási](storage-ref-azcopy-copy.md) referencia-dokumentumok).

#### <a name="use-wildcard-characters"></a>Helyettesítő karakterek használata

Használja az [azcopy copy parancsot](storage-ref-azcopy-copy.md) a `--include-pattern` kapcsolóval. Adjon meg részleges neveket, amelyek tartalmazzák a helyettesítő karaktereket. A neveket pontosvesszővel () válassza el `;` egymástól.

**Syntax**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>`

**Példa**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'
```

A kapcsolóval fájlokat is `--exclude-pattern` kizárhat. További információ: azcopy copy reference docs [(Azcopy másolási](storage-ref-azcopy-copy.md) referencia-dokumentumok).

A `--include-pattern` és a beállítás csak a `--exclude-pattern` fájlnevekre vonatkozik, az elérési útra nem.  Ha egy könyvtárfában található összes szövegfájlt ki szeretné másolni, a kapcsolóval le kell kapnia a teljes könyvtárfát, majd a és a használatával le kell kapnia az összes `--recursive` `--include-pattern` `*.txt` szövegfájlt.

#### <a name="download-files-that-were-modified-after-a-date-and-time"></a>Dátum és idő után módosított fájlok letöltése 

Használja az [azcopy copy parancsot](storage-ref-azcopy-copy.md) a `--include-after` kapcsolóval. Adjon meg egy dátumot és egy időpontot ISO-8601 formátumban (például: `2020-08-19T15:04:00Z` ). 

**Syntax**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name>/*<SAS-token>' '<local-directory-path>'  --include-after <Date-Time-in-ISO-8601-format>`

**Példa**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/*?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory' --include-after '2020-08-19T15:04:00Z'
```

Részletes referencia: az azcopy copy reference docs [(Azcopy másolási](storage-ref-azcopy-copy.md) referencia-dokumentumok).

#### <a name="download-from-a-share-snapshot"></a>Letöltés megosztási pillanatképből

Egy fájl vagy könyvtár egy adott verziójának letöltéséhez hivatkozhat egy megosztási pillanatkép **DateTime** értékére. A megosztási pillanatképekkel kapcsolatos további információkért lásd: A megosztási pillanatképek [áttekintése Azure Files.](../files/storage-snapshots-files.md) 

**Syntax**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path-or-directory-name><SAS-token>&sharesnapshot=<DateTime-of-snapshot>' '<local-file-or-directory-path>'`

**Példa (Fájl letöltése)**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory\myTextFile.txt'
```

**Példa (Könyvtár letöltése)**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory'  --recursive
```

## <a name="copy-files-between-storage-accounts"></a>Fájlok másolása tárfiókok között

Az AzCopyval fájlokat másolhat más tárfiókba. A másolási művelet szinkron módon megy végbe, ezért a parancs visszatérése azt jelzi, hogy a fájlok másolása megtörtént.

Az AzCopy [kiszolgálók közötti API-kat](/rest/api/storageservices/put-block-from-url) [használ,](/rest/api/storageservices/put-page-from-url)így az adatok másolása közvetlenül a tárolókiszolgálók között történik. Ezek a másolási műveletek nem használják a számítógép hálózati sávszélességét. Ezeknek a műveleteknek az átviteli sebességét a környezeti változó értékének beállításával `AZCOPY_CONCURRENCY_VALUE` növelheti. További információ: [Az egyidejűség növelése.](storage-use-azcopy-optimize.md#increase-concurrency)

A fájlok adott verzióit úgy is másolhatja, ha a megosztási pillanatkép **DateTime** értékére hivatkozik. A megosztási pillanatképekkel kapcsolatos további információkért lásd: A megosztási pillanatképek [áttekintése Azure Files.](../files/storage-snapshots-files.md) 

Ez a szakasz a következő példákat tartalmazza:

> [!div class="checklist"]
> * Fájl másolása másik tárfiókba
> * Címtár másolása másik tárfiókba
> * Fájlmegosztás másolása másik tárfiókba
> * Az összes fájlmegosztás, könyvtár és fájl másolása egy másik tárfiókba

> [!TIP]
> A másolási műveletet igény szerint jelzők használatával is finomhangolásra használhatja. Látható néhány példa.
>
> |Eset|Jelölő|
> |---|---|
> |Másolja a hozzáférés-vezérlési listákat (ACL-eket) a fájlokkal együtt.|**--preserve-smb-permissions** = \[ true \| false (igaz hamis)\]|
> |Másolja ki az SMB-tulajdonságinformációkat a fájlokkal együtt.|**--preserve-smb-info** = \[ true \| false (igaz hamis)\]|
> 
> A teljes listát a beállításokat [lásd:](storage-ref-azcopy-copy.md#options).

### <a name="copy-a-file-to-another-storage-account"></a>Fájl másolása másik tárfiókba

**Syntax**

`azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>'`

**Példa**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D'
```

**Példa (megosztási pillanatkép)**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D'
```

### <a name="copy-a-directory-to-another-storage-account"></a>Címtár másolása másik tárfiókba

**Syntax**

`azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive`

**Példa**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/myFileShare/myFileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive
```

**Példa (megosztási pillanatkép)**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/myFileShare/myFileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive
```

### <a name="copy-a-file-share-to-another-storage-account"></a>Fájlmegosztás másolása másik tárfiókba

**Syntax**

`azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive`

**Példa**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D
```

**Példa (megosztási pillanatkép)**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive
```

### <a name="copy-all-file-shares-directories-and-files-to-another-storage-account"></a>Az összes fájlmegosztás, könyvtár és fájl másolása egy másik tárfiókba

**Syntax**

`azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<SAS-token>' --recursive'`

**Példa**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive
```

**Példa (megosztási pillanatkép)**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive
```

## <a name="synchronize-files"></a>Fájlok szinkronizálása

A fájlmegosztás tartalmát szinkronizálhatja egy másik fájlmegosztással. A fájlmegosztások könyvtárának tartalmát egy másik fájlmegosztásban található könyvtár tartalmával is szinkronizálhatja. A szinkronizálás az egyik módszer. Más szóval ön dönti el, hogy a két végpont közül melyik a forrás, és melyik a cél. A szinkronizálás kiszolgálói API-kat is használ.

> [!NOTE]
> Ez a forgatókönyv jelenleg csak hierarchikus névtéren nem rendelkező fiókok esetében támogatott. Az AzCopy aktuális kiadása nem szinkronizál a Azure Files és Blob Storage.

A [szinkronizálási](storage-ref-azcopy-sync.md) parancs összehasonlítja a fájlneveket és a legutóbb módosított időbélyegeket. Állítsa a választható jelzőt a vagy értékre a célkönyvtárban lévő fájlok törléséhez, ha ezek a fájlok már nem léteznek `--delete-destination` a `true` `prompt` forráskönyvtárban.

Ha a jelzőt a `--delete-destination` következőre adja meg: , az AzCopy kérés `true` nélkül törli a fájlokat. Ha azt szeretné, hogy egy üzenet azelőtt jelenjen meg, hogy az AzCopy töröl egy fájlt, állítsa a `--delete-destination` jelzőt a `prompt` következőre: .

> [!TIP]
> A szinkronizálási műveletet nem kötelező jelzők használatával finomhangolni tudja. Látható néhány példa.
>
> |Eset|Jelölő|
> |---|---|
> |Másolja a hozzáférés-vezérlési listákat (ACL-eket) a fájlokkal együtt.|**--preserve-smb-permissions** = \[ true \| false (igaz hamis)\]|
> |Másolja ki az SMB-tulajdonságinformációkat a fájlokkal együtt.|**--preserve-smb-info** = \[ true \| false (igaz hamis)\]|
> |Fájlok kizárása minta alapján.|**--exclude-path**|
> |Adja meg, hogy milyen részletes legyen a szinkronizálással kapcsolatos naplóbejegyzés.|**--log-level** = \[ FIGYELMEZTETÉSI \| HIBA \| – \| INFORMÁCIÓ: NINCS\]|
> 
> A teljes listát a beállításokat [lásd:](storage-ref-azcopy-sync.md#options).

### <a name="update-a-file-share-with-changes-to-another-file-share"></a>Fájlmegosztás frissítése egy másik fájlmegosztás módosításaival

A parancsban elsőként megjelenő fájlmegosztás a forrás. A második a célhely.

**Syntax**

`azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive`

**Példa**

```azcopy
azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive
```

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Könyvtár frissítése egy másik fájlmegosztásban található könyvtár módosításaival

A parancsban elsőként megjelenő könyvtár a forrás. A második a célhely.

**Syntax**

`azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' --recursive`

**Példa**

```azcopy
azcopy sync 'https://mysourceaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive
```

### <a name="update-a-file-share-to-match-the-contents-of-a-share-snapshot"></a>Fájlmegosztás frissítése a megosztási pillanatkép tartalmának megfelelőre

A parancsban elsőként megjelenő fájlmegosztás a forrás. Az URI végén fűzheti hozzá a sztringet, majd a `&sharesnapshot=` **pillanatkép DateTime** értékét.

**Syntax**

`azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>&sharesnapsot<snapshot-ID>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive`

**Példa**

```azcopy
azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-03-03T20%3A24%3A13.0000000Z' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive
``` 

A megosztási pillanatképekkel kapcsolatos további információkért lásd a megosztási pillanatképek áttekintését [a](../files/storage-snapshots-files.md)Azure Files.

## <a name="next-steps"></a>Következő lépések

További példákat az alábbi cikkekben találhat:

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)
- [Adatátvitel](storage-use-azcopy-v10.md#transfer-data)

A következő cikkekben konfigurálhatja a beállításokat, optimalizálhatja a teljesítményt és elháríthatja a problémákat:

- [AzCopy konfigurációs beállításai](storage-ref-azcopy-configuration-settings.md)
- [Az AzCopy teljesítményének optimalizálása](storage-use-azcopy-optimize.md)
- [Az AzCopy 10-es hibák elhárítása az Azure Storage-ban naplófájlok használatával](storage-use-azcopy-configure.md)

