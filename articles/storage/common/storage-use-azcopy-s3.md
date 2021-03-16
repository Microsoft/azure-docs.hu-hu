---
title: Adatok másolása az Amazon S3-ból az Azure Storage-ba a AzCopy használatával | Microsoft Docs
description: Az AzCopy használatával adatok másolhatók az Amazon S3-ból az Azure Storage-ba. Az AzCopy egy parancssori segédprogram, amellyel blobokat és fájlokat másolhat a tárfiókok között.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 07/27/2020
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 34f54bb30e959ecc2fa27fba5ab7392b9eddc68e
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103494512"
---
# <a name="copy-data-from-amazon-s3-to-azure-storage-by-using-azcopy"></a>Adatok másolása az Amazon S3-ból az Azure Storage-ba a AzCopy használatával

Az AzCopy egy parancssori segédprogram, amellyel blobokat és fájlokat másolhat a tárfiókok között. Ebből a cikkből megtudhatja, hogyan másolhat objektumokat, címtárakat és gyűjtőket Amazon Web Services (AWS) S3-ról az Azure Blob Storagera a AzCopy használatával.

## <a name="choose-how-youll-provide-authorization-credentials"></a>Az engedélyezési hitelesítő adatok megadására szolgáló mód kiválasztása

* Az Azure Storage-ban való engedélyezéshez használja a Azure Active Directory (AD) vagy egy közös hozzáférésű aláírás (SAS) tokent.

* Az AWS S3 engedélyezéséhez használjon AWS-hozzáférési kulcsot és egy titkos hozzáférési kulcsot.

### <a name="authorize-with-azure-storage"></a>Engedélyezés az Azure Storage-ban

A AzCopy letöltéséhez tekintse meg az [első lépések a AzCopy](storage-use-azcopy-v10.md) című cikket, és válassza ki, hogyan adja meg az engedélyezési hitelesítő adatokat a Storage szolgáltatás számára.

> [!NOTE]
> A cikkben szereplő példák azt feltételezik, hogy a parancs használatával hitelesítette a személyazonosságát `AzCopy login` . A AzCopy ezután az Azure AD-fiók használatával engedélyezi a blob Storage-beli adathozzáférését.
>
> Ha inkább SAS-tokent használ a blob-adathozzáférés engedélyezéséhez, akkor a tokent az erőforrás URL-címéhez is hozzáfűzheti az egyes AzCopy-parancsokban.
>
> Példa: `https://mystorageaccount.blob.core.windows.net/mycontainer?<SAS-token>`.

### <a name="authorize-with-aws-s3"></a>Engedélyezés az AWS S3-vel

Gyűjtse össze az AWS-hozzáférési kulcsot és a titkos hozzáférési kulcsot, majd állítsa be ezeket a környezeti változókat:

| Operációs rendszer | Parancs  |
|--------|-----------|
| **Windows** | `set AWS_ACCESS_KEY_ID=<access-key>`<br>`set AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Linux** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **macOS** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>`|

## <a name="copy-objects-directories-and-buckets"></a>Objektumok, könyvtárak és gyűjtők másolása

A AzCopy a [put blokkot használja az URL API-ból](/rest/api/storageservices/put-block-from-url) , így az adatok közvetlenül az AWS S3 és a Storage kiszolgálók között másolódnak át. Ezek a másolási műveletek nem használják a számítógép hálózati sávszélességét.

> [!TIP]
> Az ebben a szakaszban szereplő példák egyetlen idézőjelekkel (' ') rendelkeznek a Path argumentumokkal. Használjon egy idézőjelet az összes parancs-rendszerhéjon, kivéve a Windows parancs-rendszerhéjt (cmd.exe). Ha Windows parancs-rendszerhéjt (cmd.exe) használ, az idézőjelek ("") helyett idézőjelek ("") közé foglalja a Path argumentumokat ("").

 Ezek a példák olyan fiókokkal is működnek, amelyek hierarchikus névtérrel rendelkeznek. A [több protokollon keresztüli hozzáférés Data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) lehetővé teszi, hogy ugyanazt az URL-szintaxist () használja a `blob.core.windows.net` fiókokon. 

### <a name="copy-an-object"></a>Objektum másolása

Használja ugyanazt az URL-szintaxist ( `blob.core.windows.net` ) olyan fiókokhoz, amelyek hierarchikus névtérrel rendelkeznek.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<object-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>'` |
| **Példa** | `azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |

> [!NOTE]
> A jelen cikkben szereplő példák az AWS S3-gyűjtők elérési útja stílusú URL-címeket használják (például: `http://s3.amazonaws.com/<bucket-name>` ). 
>
> Emellett használhatja a virtuálisan üzemeltetett stílusú URL-címeket is (például: `http://bucket.s3.amazonaws.com` ). 
>
> Ha többet szeretne megtudni a gyűjtők virtuális üzemeltetéséről, tekintse meg [a gyűjtők virtuális üzemeltetését](https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html)ismertető témakört.

### <a name="copy-a-directory"></a>Könyvtár másolása

Használja ugyanazt az URL-szintaxist ( `blob.core.windows.net` ) olyan fiókokhoz, amelyek hierarchikus névtérrel rendelkeznek.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<directory-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true` |
| **Példa** | `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |
| **Példa** (hierarchikus névtér)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

> [!NOTE]
> Ez a példa hozzáfűzi a `--recursive` jelzőt, hogy az összes alkönyvtárba másolja a fájlokat.

### <a name="copy-the-contents-of-a-directory"></a>Könyvtár tartalmának másolása

A könyvtár tartalmát másolhatja anélkül, hogy a benne található könyvtárat a helyettesítő karakter (*) használatával másolja.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<directory-name>/*' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true` |
| **Példa** | `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory/*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |
| **Példa** (hierarchikus névtér)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory/*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-a-bucket"></a>Gyűjtő másolása

Használja ugyanazt az URL-szintaxist ( `blob.core.windows.net` ) olyan fiókokhoz, amelyek hierarchikus névtérrel rendelkeznek.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive=true` |
| **Példa** | `azcopy copy 'https://s3.amazonaws.com/mybucket' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive=true` |
| **Példa** (hierarchikus névtér)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive=true` |

### <a name="copy-all-buckets-in-all-regions"></a>Összes gyűjtő másolása minden régióban

Használja ugyanazt az URL-szintaxist ( `blob.core.windows.net` ) olyan fiókokhoz, amelyek hierarchikus névtérrel rendelkeznek.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://s3.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Példa** | `azcopy copy 'https://s3.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Példa** (hierarchikus névtér)| `azcopy copy 'https://s3.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |

### <a name="copy-all-buckets-in-a-specific-s3-region"></a>Az összes gyűjtő másolása egy adott S3 régióban

Használja ugyanazt az URL-szintaxist ( `blob.core.windows.net` ) olyan fiókokhoz, amelyek hierarchikus névtérrel rendelkeznek.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://s3-<region-name>.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Példa** | `azcopy copy 'https://s3-rds.eu-north-1.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Példa** (hierarchikus névtér)| `azcopy copy 'https://s3.amazonaws.com/mybucket' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

## <a name="handle-differences-in-object-naming-rules"></a>Az objektumok elnevezési szabályai közötti különbségek kezelése

Az AWS S3 az Azure Blob-tárolókkal összehasonlítva különböző elnevezési konvenciókat tartalmaz a gyűjtők neveihez. [Itt](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules)olvashat róluk. Ha a gyűjtők egy csoportját egy Azure Storage-fiókba másolja, a másolási művelet a névadási különbségek miatt sikertelen lehet.

A AzCopy két leggyakoribb problémát okoz, amelyek felmerülhetnek; az egymást követő kötőjeleket tartalmazó pontokat és gyűjtőket tartalmazó gyűjtők. Az AWS S3-gyűjtők neve tartalmazhat pontokat és egymást követő kötőjeleket, de az Azure-beli tárolók nem. A AzCopy lecseréli a kötőjelekkel és egymást követő kötőjelekkel rendelkező időszakokat egy számmal, amely az egymást követő kötőjelek számát jelöli (például: egy nevű gyűjtő `my----bucket` `my-4-bucket` . 

Továbbá, mivel a AzCopy fájlokba másolja a fájlokat, ellenőrzi a névadási ütközéseket, és megkísérli a megoldását. Ha például vannak olyan gyűjtők, amelyeknek a neve `bucket-name` és a `bucket.name` , a AzCopy először a nevű gyűjtőt oldja `bucket.name` fel, majd a (z `bucket-name` ) értékre `bucket-name-2` .

## <a name="handle-differences-in-object-metadata"></a>Az objektum metaadatai közötti különbségek kezelése

Az AWS S3 és az Azure különböző karakterkészleteket tesz lehetővé az objektumok kulcsainak neveiben. [Itt](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMetadata.html#object-keys)olvashat azokról a karakterekről, amelyeket az AWS S3 használ. Az Azure oldalon a blob-objektumok kulcsai megfelelnek a [C# azonosítók](/dotnet/csharp/language-reference/)elnevezési szabályainak.

Egy AzCopy parancs részeként megadhat `copy` egy értéket a nem kötelezően megadandó `s2s-handle-invalid-metadata` jelzőhöz, amely meghatározza, hogyan szeretné kezelni azokat a fájlokat, amelyekben a fájl metaadatai a nem kompatibilis kulcsok nevét tartalmazzák. A következő táblázat ismerteti az egyes jelző értékeket.

| Jelölő értéke | Leírás  |
|--------|-----------|
| **ExcludeIfInvalid** | (Alapértelmezett beállítás) A metaadatok nem szerepelnek az átvitt objektumban. A AzCopy egy figyelmeztetést naplóz. |
| **FailIfInvalid** | Az objektumok nem másolhatók. A AzCopy naplóz egy hibát, és tartalmazza azt a hibás darabszámot, amely megjelenik az átvitel összegzésében.  |
| **RenameIfInvalid**  | A AzCopy feloldja az érvénytelen metaadat-kulcsot, és átmásolja az objektumot az Azure-ba a megoldott metaadat-kulcs értéke pár használatával. Ha pontosan szeretné megtudni, hogy milyen lépések szükségesek az AzCopy átnevezéséhez, tekintse meg az alábbi [AzCopy átnevezése az objektumok kulcsairól](#rename-logic) című szakaszt. Ha a AzCopy nem tudja átnevezni a kulcsot, az objektum nem lesz átmásolva. |

<a id="rename-logic"></a>

### <a name="how-azcopy-renames-object-keys"></a>A AzCopy átnevezi az objektumok kulcsait

A AzCopy a következő lépéseket hajtja végre:

1. Az érvénytelen karaktereket az "_" karakter váltja fel.

2. Hozzáadja a karakterláncot `rename_` egy új érvényes kulcs elejéhez.

   Ezt a kulcsot fogja használni a rendszer az eredeti metaadat **értékének** mentéséhez.

3. Hozzáadja a karakterláncot `rename_key_` egy új érvényes kulcs elejéhez.
   Ezt a kulcsot fogja használni a rendszer az eredeti metaadatok érvénytelen **kulcsának** mentéséhez.
   Ezzel a kulccsal megpróbálhatja helyreállítani a metaadatokat az Azure-ban, mivel a metaadat-kulcsot a blob Storage szolgáltatás értékeként megőrzi a rendszer.

## <a name="next-steps"></a>Következő lépések

További példákat a következő cikkekben talál:

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)

- [Adatátvitel](storage-use-azcopy-v10.md#transfer-data)

- [AzCopy konfigurálása, optimalizálása és megoldása](storage-use-azcopy-configure.md)
