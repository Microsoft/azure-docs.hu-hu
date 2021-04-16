---
title: Másolás a Google Cloud Storage-ból az Azure Storage-ba az AzCopy | Microsoft Docs
description: Az AzCopy használatával másolhatja az adatokat a Google Cloud Storage-ból az Azure Storage-ba. Az AzCopy egy parancssori segédprogram, amellyel blobokat és fájlokat másolhat a tárfiókok között.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 3b2ad11abb7d1a3e64deef1ca49d9f84f03e5879
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107498339"
---
# <a name="copy-data-from-google-cloud-storage-to-azure-storage-by-using-azcopy-preview"></a>Adatok másolása a Google Cloud Storage-ból az Azure Storage-ba az AzCopy (előzetes verzió) használatával

Az AzCopy egy parancssori segédprogram, amellyel blobokat és fájlokat másolhat a tárfiókok között. Ez a cikk segítséget nyújt az objektumok, könyvtárak és gyűjtők Másolásához a Google Cloud Storage-ból Azure Blob Storage AzCopy használatával.

> [!IMPORTANT]
> Az adatok Google Cloud Storage-ból Azure Storage-ba történő másolása jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="choose-how-youll-provide-authorization-credentials"></a>Az engedélyezési hitelesítő adatok megadására szolgáló mód kiválasztása

* Az Azure Storage-ban való jogosultsághoz használjon Azure Active Directory (AD) vagy egy közös hozzáférésű jogosultság jogosultsági (SAS) jogkivonatot.

* A Google Cloud Storage-ban való jogosultsághoz használjon szolgáltatásfiókkulcsot.

### <a name="authorize-with-azure-storage"></a>Jogosultság az Azure Storage-ban

Az [AzCopy letöltéséhez](storage-use-azcopy-v10.md) tekintse meg az Ismerkedés az AzCopyval cikket, és ismerje meg, hogyan biztosítanak hitelesítési hitelesítő adatokat a tárolási szolgáltatásnak.

> [!NOTE] 
> A cikkben található példák feltételezik, hogy hitelesítő adatokat adott meg a Azure Active Directory (Azure AD) használatával.
>
> Ha inkább SAS-jogkivonatot használna a blobadatokhoz való hozzáféréshez, akkor minden AzCopy-parancsban hozzáfűzheti ezt a jogkivonatot az erőforrás URL-címéhez. Példa: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

### <a name="authorize-with-google-cloud-storage"></a>Jogosultság a Google Cloud Storage-ban

A Google Cloud Storage-ban való jogosultsághoz egy szolgáltatásfiókkulcsot kell használnia. A szolgáltatásfiókkulcsok létrehozásával kapcsolatos információkért lásd: [Szolgáltatásfiókkulcsok létrehozása és kezelése.](https://cloud.google.com/iam/docs/creating-managing-service-account-keys)

Miután beszerezt egy szolgáltatáskulcsot, állítsa a környezeti változót a szolgáltatásfiók kulcsfájljára vezető abszolút `GOOGLE_APPLICATION_CREDENTIALS` elérési útra:

| Operációs rendszer | Parancs  |
|--------|-----------|
| **Windows** | `set GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |
| **Linux** | `export GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |
| **macOS** | `export GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |

## <a name="copy-objects-directories-and-buckets"></a>Objektumok, könyvtárak és gyűjtők másolása

Az AzCopy a [Put Block From URL API-t](/rest/api/storageservices/put-block-from-url) használja, így az adatok másolása közvetlenül a Google Cloud Storage és a tárolási kiszolgálók között történik. Ezek a másolási műveletek nem használják a számítógép hálózati sávszélességét.

> [!TIP]
> Az ebben a szakaszban található példák az elérésiút-argumentumokat egyszeres idézőjelek ('') közé ékeik. Használjon egyszeres idézőjelet az összes parancshéjban, kivéve a Windows parancshéjat (cmd.exe). Ha Windows parancshéjat (cmd.exe) használ, az elérésiút-argumentumokat idézőjelek ("") közé kell tenni a single quotes ('' helyett).

 Ezek a példák hierarchikus névtéren keresztüli fiókokkal is működnek. [A több protokollos hozzáférés Data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) ugyanezen URL-szintaxis ( ) használatát `blob.core.windows.net` teszi lehetővé az ilyen fiókokon. 

### <a name="copy-an-object"></a>Objektum másolása

Használja ugyanazt az URL-szintaxist ( ) a hierarchikus névtérhez `blob.core.windows.net` tartozó fiókokhoz.

**Syntax**

`azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<object-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>'`

**Példa**

```azcopy
azcopy copy 'https://storage.cloud.google.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'
```


### <a name="copy-a-directory"></a>Könyvtár másolása

Használja ugyanazt az URL-szintaxist ( ) a hierarchikus névtérhez `blob.core.windows.net` tartozó fiókokhoz.

**Syntax**

`azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<directory-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true`

**Példa**

```azcopy
azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true
```

> [!NOTE]
> Ez a példa hozzáfűzi a jelzőt az összes al könyvtárban lévő `--recursive` fájlok másoláshoz.

### <a name="copy-the-contents-of-a-directory"></a>Könyvtár tartalmának másolása

A könyvtár tartalmát az azt tartalmazó könyvtár másolása nélkül is másolhatja a helyettesítő karakteres szimbólum (*)használatával.

**Syntax**

`azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<directory-name>/*' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true`

**Példa**

```azcopy
azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory/*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true
```

### <a name="copy-a-cloud-storage-bucket"></a>Felhőalapú tároló másolása

Használja ugyanazt az URL-szintaxist ( ) a hierarchikus névtérhez `blob.core.windows.net` tartozó fiókokhoz.

**Syntax**

`azcopy copy 'https://storage.cloud.google.com/<bucket-name>' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true`

**Példa**

```azcopy
azcopy copy 'https://storage.cloud.google.com/mybucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true
```

### <a name="copy-all-buckets-in-a-google-cloud-project"></a>Egy Google Cloud-projekt összes gyűjtője másolása 

Először állítsa be a `GOOGLE_CLOUD_PROJECT` projektazonosítót a Google Cloud-projekthez.

Használja ugyanazt az URL-szintaxist ( ) a hierarchikus névtérhez `blob.core.windows.net` tartozó fiókokhoz.

**Syntax**

`azcopy copy 'https://storage.cloud.google.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true`

**Példa**

```azcopy
azcopy copy 'https://storage.cloud.google.com/' 'https://mystorageaccount.blob.core.windows.net' --recursive=true
```

### <a name="copy-a-subset-of-buckets-in-a-google-cloud-project"></a>Gyűjtők egy részkészletének másolása Egy Google Cloud-projektben 

Először állítsa be a `GOOGLE_CLOUD_PROJECT` projektazonosítót a Google Cloud-projekthez.

A gyűjtők egy részkészletét egy helyettesítő karakter (*) szimbólummal másolja ki a gyűjtő nevébe. Használja ugyanazt az URL-szintaxist ( ) a hierarchikus névtérhez `blob.core.windows.net` tartozó fiókokhoz.

**Syntax**

`azcopy copy 'https://storage.cloud.google.com/<bucket*name>' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true`

**Példa**

```azcopy
azcopy copy 'https://storage.cloud.google.com/my*bucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true
```

## <a name="handle-differences-in-bucket-naming-rules"></a>A gyűjtőelnevezési szabályok különbségének kezelhetők

A Google Cloud Storage más elnevezési konvenciókat használ a gyűjtőnevekhez, mint az Azure Blob-tárolókhoz. Erről itt [olvashat.](https://cloud.google.com/storage/docs/naming-buckets) Ha gyűjtők egy csoportját egy Azure-tárfiókba másolja, a másolási művelet az elnevezési különbségek miatt meghiúsulhat.

Az AzCopy a három leggyakoribb felmerülő problémát kezeli; pontokat, egymást követő kötőjeleket tartalmazó gyűjtőket és aláhúzásjeleket tartalmazó gyűjtők. A Google Cloud Storage-tárolók nevei pontokat és egymást követő kötőjeleket tartalmazhatnak, az Azure-beli tárolók azonban nem. Az AzCopy a pontokat kötőjelekkel és egymást követő kötőjelekkel cseréli le egy számra, amely az egymást követő kötőjelek számát jelöli (például egy nevű gyűjtő `my----bucket` `my-4-bucket` lesz. Ha a gyűjtő neve aláhúzásjellel ( ), akkor az AzCopy kötőjelre cseréli az aláhúzásjelet (például egy nevű gyűjtő `_` `my_bucket` `my-bucket` lesz. 

## <a name="handle-differences-in-object-naming-rules"></a>Az objektumelnevezési szabályok különbségének kezelhetők

A Google Cloud Storage más elnevezési konvenciókat használ az objektumnevekhez, mint az Azure-blobok. Erről itt [olvashat.](https://cloud.google.com/storage/docs/naming-objects)

Az Azure Storage nem engedélyezi az objektumnevek (vagy a virtuális könyvtár elérési útjának bármely szegmense) végződését záró pontokkal (például `my-bucket...` ). A másolási művelet végrehajtásához a záró pontokat a rendszer levágja. 

## <a name="handle-differences-in-object-metadata"></a>Az objektummetaadatok eltérésének kezelésével

A Google Cloud Storage és az Azure különböző karakterkészleteket engedélyez az objektumkulcsok neveiben. A Google Cloud Storage metaadatairól itt [olvashat.](https://cloud.google.com/storage/docs/metadata) Az Azure oldalán a blobobjektumok kulcsai követik a [C#-azonosítók elnevezési szabályait.](/dotnet/csharp/language-reference/)

Az AzCopy-parancs részeként meg kell adnia egy értéket a választható jelző számára, amely meghatározza, hogyan szeretné kezelni azokat a fájlokat, amelyek metaadatai nem kompatibilis kulcsneveket `copy` `s2s-handle-invalid-metadata` tartalmaznak. Az alábbi táblázat az egyes jelzőértékeket ismerteti.

| Jelző értéke | Leírás  |
|--------|-----------|
| **ExcludeIfInvalid (Kizárási érték)** | (Alapértelmezett beállítás) A metaadatok nem szerepelnek az átvitt objektumban. Az AzCopy egy figyelmeztetést naplóz. |
| **FailIfInvalid (FailIfInvalid)** | A rendszer nem másolja az objektumokat. Az AzCopy hibát naplóz, és a hibát az átviteli összegzésben megjelenő sikertelen számba foglalja bele.  |
| **RenameIfInvalid (Névazonosító átnevezése)**  | Az AzCopy feloldja az érvénytelen metaadatkulcsot, és a feloldott metaadatkulcs-érték pár használatával átmásolja az objektumot az Azure-ba. Ha meg szeretne ismerkedni az AzCopy objektumkulcsok átnevezésének lépéseivel, tekintse meg az alábbi [How AzCopy renames object keys](#rename-logic) (Az Objektumkulcsok átnevezése az AzCopy használatával) című szakaszt. Ha az AzCopy nem tudja átnevezni a kulcsot, akkor az objektum nem lesz átmásolva. |

<a id="rename-logic"></a>

### <a name="how-azcopy-renames-object-keys"></a>Hogyan nevezi át az AzCopy az objektumkulcsokat?

Az AzCopy a következő lépéseket végzi el:

1. Lecseréli az érvénytelen karaktereket a "_" karakterre.

2. Hozzáadja a `rename_` sztringet egy új érvényes kulcs elejéhez.

   Ezzel a kulccsal mentheti az eredeti **metaadatértéket.**

3. Hozzáadja a `rename_key_` sztringet egy új érvényes kulcs elejéhez.
   A rendszer ezt a kulcsot fogja használni az eredeti metaadatok érvénytelen kulcsának **mentésére.**
   Ezzel a kulccsal megpróbálhatja helyreállítani a metaadatokat az Azure oldalán, mivel a metaadatkulcs értékként megmarad a Blob Storage szolgáltatásban.

## <a name="next-steps"></a>Következő lépések

További példákat ezekben a cikkekben talál:

- [Példák: Feltöltés](storage-use-azcopy-blobs-upload.md)
- [Példák: Letöltés](storage-use-azcopy-blobs-download.md)
- [Példák: Másolás tárfiókok között](storage-use-azcopy-blobs-copy.md)
- [Példák: Szinkronizálás](storage-use-azcopy-blobs-synchronize.md)
- [Példák: Amazon S3-gyűjtők](storage-use-azcopy-s3.md)
- [Példák: Azure Files](storage-use-azcopy-files.md)
- [Oktatóanyag: Helyszíni adatok migrálása felhőtárhelybe az AzCopyval](storage-use-azcopy-migrate-on-premises-data.md)

A következő cikkekben konfigurálhatja a beállításokat, optimalizálhatja a teljesítményt és elháríthatja a problémákat:

- [AzCopy konfigurációs beállításai](storage-ref-azcopy-configuration-settings.md)
- [Az AzCopy teljesítményének optimalizálása](storage-use-azcopy-optimize.md)
- [Az AzCopy 10-es hibák elhárítása az Azure Storage-ban naplófájlok használatával](storage-use-azcopy-configure.md)