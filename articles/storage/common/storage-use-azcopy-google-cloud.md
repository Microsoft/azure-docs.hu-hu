---
title: Adatok másolása a Google Cloud Storage-ból az Azure Storage-ba a AzCopy használatával | Microsoft Docs
description: Adatok másolása a Google Cloud Storage-ból az Azure Storage-ba a AzCopy használatával. Az AzCopy egy parancssori segédprogram, amellyel blobokat és fájlokat másolhat a tárfiókok között.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 03/09/2021
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: c0f030683954ede013f769bf8584e6cf82bab69f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103555421"
---
# <a name="copy-data-from-google-cloud-storage-to-azure-storage-by-using-azcopy-preview"></a>Adatok másolása a Google Cloud Storage-ból az Azure Storage-ba a AzCopy (előzetes verzió) használatával

Az AzCopy egy parancssori segédprogram, amellyel blobokat és fájlokat másolhat a tárfiókok között. Ebből a cikkből megtudhatja, hogyan másolhat objektumokat, címtárakat és gyűjtőket a Google Cloud Storage szolgáltatásból az Azure Blob Storageba a AzCopy használatával.

> [!IMPORTANT]
> Az adatok Google Cloud Storage-ból Azure Storage-ba való másolása jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="choose-how-youll-provide-authorization-credentials"></a>Az engedélyezési hitelesítő adatok megadására szolgáló mód kiválasztása

* Az Azure Storage-ban való engedélyezéshez használja a Azure Active Directory (AD) vagy egy közös hozzáférésű aláírás (SAS) tokent.

* A Google Cloud Storage szolgáltatásban való engedélyezéshez használjon szolgáltatásfiók-kulcsot.

### <a name="authorize-with-azure-storage"></a>Engedélyezés az Azure Storage-ban

Tekintse meg az első [lépések a AzCopy](storage-use-azcopy-v10.md) című cikket a AzCopy letöltéséhez és a tárolási szolgáltatás engedélyezési hitelesítő adatainak megadásához szükséges módszerekről.

> [!NOTE] 
> A cikkben szereplő példák azt feltételezik, hogy Azure Active Directory (Azure AD) használatával adta meg az engedélyezési hitelesítő adatokat.
>
> Ha inkább SAS-tokent használ a blob-adathozzáférés engedélyezéséhez, akkor a tokent az erőforrás URL-címéhez is hozzáfűzheti az egyes AzCopy-parancsokban. Példa: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

### <a name="authorize-with-google-cloud-storage"></a>Engedélyezés a Google Cloud Storage-ban

A Google Cloud Storage szolgáltatással való engedélyezéshez szolgáltatásfiók-kulcsot fog használni. A szolgáltatásfiók-kulcsok létrehozásával kapcsolatos további információkért lásd: [szolgáltatásfiók-kulcsok létrehozása és kezelése](https://cloud.google.com/iam/docs/creating-managing-service-account-keys).

A szolgáltatási kulcs beszerzése után állítsa a `GOOGLE_APPLICATION_CREDENTIALS` környezeti változót a szolgáltatásfiók kulcsfájl abszolút elérési útjára:

| Operációs rendszer | Parancs  |
|--------|-----------|
| **Windows** | `set GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |
| **Linux** | `export GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |
| **macOS** | `export GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |

## <a name="copy-objects-directories-and-buckets"></a>Objektumok, könyvtárak és gyűjtők másolása

A AzCopy a [put blokkot használja az URL API-ból](/rest/api/storageservices/put-block-from-url) , így az adatok közvetlenül a Google Cloud Storage és a Storage kiszolgálók között lesznek másolva. Ezek a másolási műveletek nem használják a számítógép hálózati sávszélességét.

> [!TIP]
> Az ebben a szakaszban szereplő példák egyetlen idézőjelekkel (' ') rendelkeznek a Path argumentumokkal. Használjon egy idézőjelet az összes parancs-rendszerhéjon, kivéve a Windows parancs-rendszerhéjt (cmd.exe). Ha Windows parancs-rendszerhéjt (cmd.exe) használ, az idézőjelek ("") helyett idézőjelek ("") közé foglalja a Path argumentumokat ("").

 Ezek a példák olyan fiókokkal is működnek, amelyek hierarchikus névtérrel rendelkeznek. A [több protokollon keresztüli hozzáférés Data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) lehetővé teszi, hogy ugyanazt az URL-szintaxist () használja a `blob.core.windows.net` fiókokon. 

### <a name="copy-an-object"></a>Objektum másolása

Használja ugyanazt az URL-szintaxist ( `blob.core.windows.net` ) olyan fiókokhoz, amelyek hierarchikus névtérrel rendelkeznek.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<object-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>'` |
| **Példa** | `azcopy copy 'https://storage.cloud.google.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'https://storage.cloud.google.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |


### <a name="copy-a-directory"></a>Könyvtár másolása

Használja ugyanazt az URL-szintaxist ( `blob.core.windows.net` ) olyan fiókokhoz, amelyek hierarchikus névtérrel rendelkeznek.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<directory-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true` |
| **Példa** | `azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |
| **Példa** (hierarchikus névtér)| `azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

> [!NOTE]
> Ez a példa hozzáfűzi a `--recursive` jelzőt, hogy az összes alkönyvtárba másolja a fájlokat.

### <a name="copy-the-contents-of-a-directory"></a>Könyvtár tartalmának másolása

A könyvtár tartalmát másolhatja anélkül, hogy a benne található könyvtárat a helyettesítő karakter (*) használatával másolja.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<directory-name>/*' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true` |
| **Példa** | `azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory/*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |
| **Példa** (hierarchikus névtér)| `azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory/*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-a-cloud-storage-bucket"></a>Cloud Storage-gyűjtő másolása

Használja ugyanazt az URL-szintaxist ( `blob.core.windows.net` ) olyan fiókokhoz, amelyek hierarchikus névtérrel rendelkeznek.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://storage.cloud.google.com/<bucket-name>' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Példa** | `azcopy copy 'https://storage.cloud.google.com/mybucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Példa** (hierarchikus névtér)| `azcopy copy 'https://storage.cloud.google.com/mybucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |

### <a name="copy-all-buckets-in-a-google-cloud-project"></a>Egy Google Cloud-projektben lévő összes gyűjtő másolása 

Először állítsa be a `GOOGLE_CLOUD_PROJECT` Google Cloud Project projekt-azonosítóját.

Használja ugyanazt az URL-szintaxist ( `blob.core.windows.net` ) olyan fiókokhoz, amelyek hierarchikus névtérrel rendelkeznek.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://storage.cloud.google.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Példa** | `azcopy copy 'https://storage.cloud.google.com/' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Példa** (hierarchikus névtér)| `azcopy copy 'https://storage.cloud.google.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |

### <a name="copy-a-subset-of-buckets-in-a-google-cloud-project"></a>Gyűjtők egy részhalmazának másolása Google Cloud-projektben 

Először állítsa be a `GOOGLE_CLOUD_PROJECT` Google Cloud Project projekt-azonosítóját.

Másolja a gyűjtők egy részhalmazát egy helyettesítő karakter (*) szimbólum használatával a gyűjtő nevében. Használja ugyanazt az URL-szintaxist ( `blob.core.windows.net` ) olyan fiókokhoz, amelyek hierarchikus névtérrel rendelkeznek.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://storage.cloud.google.com/<bucket*name>' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Példa** | `azcopy copy 'https://storage.cloud.google.com/my*bucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Példa** (hierarchikus névtér)| `azcopy copy 'https://storage.cloud.google.com/my*bucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |

## <a name="handle-differences-in-bucket-naming-rules"></a>A gyűjtők elnevezési szabályai közötti különbségek kezelése

A Google Cloud Storage különböző elnevezési konvenciókat tartalmaz a gyűjtők neveihez az Azure Blob-tárolókkal szemben. [Itt](https://cloud.google.com/storage/docs/naming-buckets)olvashat róluk. Ha a gyűjtők egy csoportját egy Azure Storage-fiókba másolja, a másolási művelet a névadási különbségek miatt sikertelen lehet.

A AzCopy az esetlegesen felmerülő leggyakoribb problémák közül hármat kezel; olyan gyűjtők, amelyek olyan pontokat tartalmaznak, amelyek egymást követő kötőjeleket tartalmaznak, valamint az aláhúzásokat tartalmazó gyűjtőket. A Google Cloud Storage-gyűjtők nevei időszakokat és egymást követő kötőjeleket tartalmazhatnak, de az Azure-beli tárolók nem. A AzCopy lecseréli a kötőjelekkel és egymást követő kötőjelekkel rendelkező időszakokat egy számmal, amely az egymást követő kötőjelek számát jelöli (például: egy nevű gyűjtő `my----bucket` `my-4-bucket` . Ha a gyűjtő neve aláhúzásjel ( `_` ), akkor a AzCopy lecseréli az aláhúzást egy kötőjelre (például: egy nevű `my_bucket` gyűjtő `my-bucket` . 

## <a name="handle-differences-in-object-naming-rules"></a>Az objektumok elnevezési szabályai közötti különbségek kezelése

A Google Cloud Storage különböző elnevezési konvenciókat tartalmaz az objektumok neveihez az Azure-blobokhoz képest. [Itt](https://cloud.google.com/storage/docs/naming-objects)olvashat róluk.

Az Azure Storage nem engedélyezi az objektumok nevét (vagy a virtuális könyvtár elérési útjának bármelyik szegmensét) záró pontok (például) végére `my-bucket...` . A másolási művelet végrehajtásakor a rendszer a záró pontokat is kivágja. 

## <a name="handle-differences-in-object-metadata"></a>Az objektum metaadatai közötti különbségek kezelése

A Google Cloud Storage és az Azure különböző karakterkészleteket tesz lehetővé az Object Keys neveiben. [Itt](https://cloud.google.com/storage/docs/metadata)olvashat a metaadatokról a Google Cloud Storage szolgáltatásban. Az Azure oldalon a blob-objektumok kulcsai megfelelnek a [C# azonosítók](/dotnet/csharp/language-reference/)elnevezési szabályainak.

Egy AzCopy parancs részeként megadhat `copy` egy értéket a nem kötelezően megadandó `s2s-handle-invalid-metadata` jelzőhöz, amely meghatározza, hogyan szeretné kezelni azokat a fájlokat, amelyekben a fájl metaadatai a nem kompatibilis kulcsok nevét tartalmazzák. A következő táblázat ismerteti az egyes jelző értékeket.

| Jelölő értéke | Description  |
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
