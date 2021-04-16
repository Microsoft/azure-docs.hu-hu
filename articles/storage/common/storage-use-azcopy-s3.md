---
title: Adatok másolása az Amazon S3-ból az Azure Storage-ba az AzCopy | Microsoft Docs
description: Az AzCopy használatával adatokat másol az Amazon S3-ból az Azure Storage-ba. Az AzCopy egy parancssori segédprogram, amellyel blobokat és fájlokat másolhat a tárfiókok között.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: ac73d0e57377a8922691ea06c8de3df5ef577680
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502436"
---
# <a name="copy-data-from-amazon-s3-to-azure-storage-by-using-azcopy"></a>Adatok másolása az Amazon S3-ból az Azure Storage-ba az AzCopy használatával

Az AzCopy egy parancssori segédprogram, amellyel blobokat és fájlokat másolhat a tárfiókok között. Ez a cikk segítséget nyújt az objektumok, könyvtárak és gyűjtők másolásában a Amazon Web Services (AWS) S3-ból a Azure Blob Storage az AzCopy használatával.

## <a name="choose-how-youll-provide-authorization-credentials"></a>Az engedélyezési hitelesítő adatok megadására szolgáló mód kiválasztása

* Az Azure Storage-ban való jogosultsághoz használjon Azure Active Directory (AD) vagy egy közös hozzáférésű jogosultság jogosultsági (SAS) jogkivonatot.

* Az AWS S3-ban való jogosultsághoz használjon egy AWS hozzáférési kulcsot és egy titkos hozzáférési kulcsot.

### <a name="authorize-with-azure-storage"></a>Engedély az Azure Storage-ban

Az [AzCopy letöltéséhez](storage-use-azcopy-v10.md) tekintse meg az Első lépések az AzCopyval cikket, és válassza ki, hogyan fogja megadni az engedélyezési hitelesítő adatokat a tárolási szolgáltatásnak.

> [!NOTE]
> A cikkben látható példák feltételezik, hogy az paranccsal hitelesítette az `AzCopy login` identitását. Az AzCopy ezután az Azure AD-fiókjával engedélyezi a blobtárolóban lévő adatokhoz való hozzáférést.
>
> Ha inkább SAS-jogkivonatot használna a blobadatokhoz való hozzáféréshez, akkor minden AzCopy-parancsban hozzáfűzheti ezt a jogkivonatot az erőforrás URL-címéhez.
>
> Példa: `https://mystorageaccount.blob.core.windows.net/mycontainer?<SAS-token>`.

### <a name="authorize-with-aws-s3"></a>Authorize with AWS S3

Gyűjtse össze az AWS hozzáférési kulcsát és titkos hozzáférési kulcsát, majd állítsa be a következő környezeti változókat:

| Operációs rendszer | Parancs  |
|--------|-----------|
| **Windows** | `set AWS_ACCESS_KEY_ID=<access-key>`<br>`set AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Linux** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **macOS** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>`|

## <a name="copy-objects-directories-and-buckets"></a>Objektumok, könyvtárak és gyűjtők másolása

Az AzCopy a [Put Block From URL API-t](/rest/api/storageservices/put-block-from-url) használja, így az adatok másolása közvetlenül az AWS S3 és a tárolókiszolgálók között történik. Ezek a másolási műveletek nem használják a számítógép hálózati sávszélességét.

> [!TIP]
> Az ebben a szakaszban található példák az elérésiút-argumentumokat egyszeres idézőjelek ('') közé ékeik. Használjon egyszeres idézőjelet az összes parancshéjban, kivéve a Windows parancshéjat (cmd.exe). Ha Windows parancshéjat (cmd.exe használ, az elérésiút-argumentumokat dupla idézőjelek ("") közé kell tenni a single quotes ('' helyett).

 Ezek a példák hierarchikus névtéren keresztüli fiókokkal is működnek. [A több protokollos hozzáférés Data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) ugyanezen URL-szintaxis ( ) használatát `blob.core.windows.net` teszi lehetővé az ilyen fiókokon. 

### <a name="copy-an-object"></a>Objektum másolása

Használja ugyanazt az URL-szintaxist ( ) a hierarchikus névtérhez `blob.core.windows.net` tartozó fiókokhoz.

**Syntax**

`azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<object-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>'`

**Példa**

```azcopy
azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'
```

> [!NOTE]
> A cikkben található példák elérésiút-stílusú URL-címeket használnak az AWS S3-gyűjtőkben (például: `http://s3.amazonaws.com/<bucket-name>` ). 
>
> Virtuális üzemeltetett URL-címeket is használhat (például: `http://bucket.s3.amazonaws.com` ). 
>
> További információ a gyűjtők virtuális üzemeltetésről: [Gyűjtők virtuális üzemeltetése.](https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html)

### <a name="copy-a-directory"></a>Könyvtár másolása

Használja ugyanazt az URL-szintaxist ( ) a hierarchikus névtérhez `blob.core.windows.net` tartozó fiókokhoz.

**Syntax**

`azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<directory-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true`

**Példa**

```azcopy
azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true
```

> [!NOTE]
> Ez a példa hozzáfűzi a jelzőt az összes al könyvtárban lévő `--recursive` fájlok másoláshoz.

### <a name="copy-the-contents-of-a-directory"></a>Könyvtár tartalmának másolása

A könyvtár tartalmát az azt tartalmazó könyvtár másolása nélkül is másolhatja a helyettesítő karakteres szimbólum (*)használatával.

**Syntax**

`azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<directory-name>/*' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true`

**Példa**

```azcopy
azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory/*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true
```

### <a name="copy-a-bucket"></a>Gyűjtő másolása

Használja ugyanazt az URL-szintaxist ( ) a hierarchikus névtérhez `blob.core.windows.net` tartozó fiókokhoz.

**Syntax**

`azcopy copy 'https://s3.amazonaws.com/<bucket-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive=true`

**Példa**

```azcopy
azcopy copy 'https://s3.amazonaws.com/mybucket' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive=true
```

### <a name="copy-all-buckets-in-all-regions"></a>Az összes gyűjtő másolása az összes régióban

Használja ugyanazt az URL-szintaxist ( ) a hierarchikus névtérhez `blob.core.windows.net` tartozó fiókokhoz.

**Syntax**

`azcopy copy 'https://s3.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true`

**Példa**

```azcopy
azcopy copy 'https://s3.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true
```

### <a name="copy-all-buckets-in-a-specific-s3-region"></a>Egy adott S3-régióban található összes gyűjtő másolása

Használja ugyanazt az URL-szintaxist ( ) a hierarchikus névtérhez `blob.core.windows.net` tartozó fiókokhoz.

**Syntax**

`azcopy copy 'https://s3-<region-name>.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true`

**Példa**

```azcopy
azcopy copy 'https://s3-rds.eu-north-1.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true
```

## <a name="handle-differences-in-object-naming-rules"></a>Az objektumelnevezési szabályok különbségének kezelhetők

Az AWS S3 más elnevezési konvenciókat is használhat a tárolónevekhez, mint az Azure Blob-tárolókhoz. Erről itt [olvashat.](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules) Ha gyűjtők egy csoportját egy Azure-tárfiókba másolja, a másolási művelet az elnevezési különbségek miatt meghiúsulhat.

Az AzCopy a leggyakrabban felmerülő problémák közül kettőt kezel; egymást követő kötőjeleket tartalmazó pontokat és gyűjtőket tartalmazó gyűjtők. Az AWS S3-gyűjtők neve pontokat és egymást követő kötőjeleket tartalmazhat, az Azure-beli tárolók azonban nem. Az AzCopy a pontokat kötőjelekkel és egymást követő kötőjelekkel cseréli le egy számra, amely az egymást követő kötőjelek számát jelöli (például egy nevű gyűjtő `my----bucket` `my-4-bucket` lesz. 

Emellett a fájlok másolásával az AzCopy névütközéseket keres, és megkísérli feloldani őket. Ha például vannak és nevű gyűjtők, az AzCopy először a következőre oldja fel a nevű gyűjtőt: , majd `bucket-name` a `bucket.name` `bucket.name` következőre: `bucket-name` `bucket-name-2` .

## <a name="handle-differences-in-object-metadata"></a>Az objektummetaadatok eltérésének kezelésével

Az AWS S3 és az Azure különböző karakterkészleteket engedélyez az objektumkulcsok neveiben. Az AWS S3 által használt karakterekről itt [olvashat.](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMetadata.html#object-keys) Az Azure oldalán a blobobjektumok kulcsai követik a [C#-azonosítók elnevezési szabályait.](/dotnet/csharp/language-reference/)

Az AzCopy-parancs részeként meg kell adnia egy értéket a választható jelző számára, amely meghatározza, hogyan szeretné kezelni azokat a fájlokat, amelyek metaadatai nem kompatibilis kulcsneveket `copy` `s2s-handle-invalid-metadata` tartalmaznak. Az alábbi táblázat az egyes jelzőértékeket ismerteti.

| Jelző értéke | Leírás  |
|--------|-----------|
| **ExcludeIfInvalid (Kizárási érték)** | (Alapértelmezett beállítás) A metaadatok nem szerepelnek az átvitt objektumban. Az AzCopy egy figyelmeztetést naplóz. |
| **FailIfInvalid (Hibaazonosító)** | A rendszer nem másolja az objektumokat. Az AzCopy hibát naplóz, és a hibát az átadás összegzésében megjelenő sikertelen számba foglalja.  |
| **RenameIfInvalid (Név átnevezése)**  | Az AzCopy feloldja az érvénytelen metaadatkulcsot, és a feloldott metaadatkulcs-pár használatával átmásolja az objektumot az Azure-ba. Az AzCopy objektumkulcsok átnevezésének lépéseit az alábbi [How AzCopy renames object keys](#rename-logic) (Az Objektumkulcsok átnevezése az AzCopyban) című szakaszban olvashatja el. Ha az AzCopy nem tudja átnevezni a kulcsot, akkor az objektum nem lesz átmásolva. |

<a id="rename-logic"></a>

### <a name="how-azcopy-renames-object-keys"></a>Hogyan nevezi át az AzCopy az objektumkulcsokat?

Az AzCopy a következő lépéseket végzi el:

1. Érvénytelen karaktereket cserél le a "_" karakterre.

2. Hozzáadja a `rename_` sztringet egy új érvényes kulcs elejéhez.

   Ezzel a kulccsal mentheti az eredeti **metaadatértéket.**

3. Hozzáadja a `rename_key_` sztringet egy új érvényes kulcs elejéhez.
   A rendszer ezt a kulcsot fogja használni az eredeti metaadatok érvénytelen kulcsának **mentésére.**
   Ezzel a kulccsal megpróbálhatja helyreállítani a metaadatokat az Azure oldalán, mivel a metaadatok kulcsa értékként marad meg a Blob Storage szolgáltatásban.

## <a name="next-steps"></a>Következő lépések

További példákat ezekben a cikkekben talál:

- [Példák: Feltöltés](storage-use-azcopy-blobs-upload.md)
- [Példák: Letöltés](storage-use-azcopy-blobs-download.md)
- [Példák: Másolás tárfiókok között](storage-use-azcopy-blobs-copy.md)
- [Példák: Szinkronizálás](storage-use-azcopy-blobs-synchronize.md)
- [Példák: Google Cloud Storage](storage-use-azcopy-google-cloud.md)
- [Példák: Azure Files](storage-use-azcopy-files.md)
- [Oktatóanyag: Helyszíni adatok migrálása felhőtárhelybe az AzCopyval](storage-use-azcopy-migrate-on-premises-data.md)

A következő cikkekben konfigurálhatja a beállításokat, optimalizálhatja a teljesítményt és elháríthatja a problémákat:

- [Az AzCopy konfigurációs beállításai](storage-ref-azcopy-configuration-settings.md)
- [Az AzCopy teljesítményének optimalizálása](storage-use-azcopy-optimize.md)
- [Az AzCopy 10-es hibák elhárítása az Azure Storage-ban naplófájlok használatával](storage-use-azcopy-configure.md)
