---
title: Szinkronizálás az Azure Blob Storage-nal a AzCopy v10 használatával | Microsoft Docs
description: Ez a cikk az Azure Blob Storage szolgáltatással való szinkronizálást segítő AzCopy-példákat tartalmaz.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/08/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: ab1da88899ba2b90e303da107631e3878b3a8b58
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102635875"
---
# <a name="synchronize-with-azure-blob-storage-by-using-azcopy-v10"></a>Szinkronizálás az Azure Blob Storage-nal a AzCopy v10 használatával

A AzCopy v10 parancssori segédprogrammal szinkronizálhatja a helyi tárolót az Azure Blob Storage szolgáltatással. 

A helyi fájlrendszer tartalmát egy blob-tárolóval szinkronizálhatja. A tárolókat és a virtuális könyvtárakat is szinkronizálhatja egymással. A szinkronizálás egy módszer. Más szóval kiválaszthatja, hogy a két végpont melyik a forrás, és melyik a cél. A szinkronizálás a kiszolgálót kiszolgáló API-kra is használja. Az ebben a szakaszban bemutatott példák a hierarchikus névtérrel rendelkező fiókokkal is működnek. 

> [!NOTE]
> A AzCopy jelenlegi kiadása nem szinkronizál más források és célhelyek között (például: file Storage vagy Amazon Web Services (AWS) S3 gyűjtők).

Ha más típusú feladatokra, például fájlok feltöltésére, blobok letöltésére vagy Blobok a fiókok közötti másolására szeretne példákat látni, tekintse meg a jelen cikk [következő lépések](#next-steps) című szakaszának hivatkozásait.

## <a name="get-started"></a>Bevezetés

Tekintse meg az első [lépések a AzCopy](storage-use-azcopy-v10.md) című cikket a AzCopy letöltéséhez és a tárolási szolgáltatás engedélyezési hitelesítő adatainak megadásához szükséges módszerekről.

> [!NOTE] 
> A cikkben szereplő példák azt feltételezik, hogy Azure Active Directory (Azure AD) használatával adta meg az engedélyezési hitelesítő adatokat.
>
> Ha inkább SAS-tokent használ a blob-adathozzáférés engedélyezéséhez, akkor a tokent az erőforrás URL-címéhez is hozzáfűzheti az egyes AzCopy-parancsokban. Példa: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="guidelines"></a>Irányelvek

- A [szinkronizálási](storage-ref-azcopy-sync.md) parancs összehasonlítja a fájlneveket és az utolsó módosítás időbélyegét. Állítsa a `--delete-destination` választható jelzőt értékre, `true` vagy `prompt` törölje a fájlokat a célhely könyvtárában, ha ezek a fájlok már nem léteznek a forrás-címtárban.

- Ha a `--delete-destination` jelölőt a értékre állítja `true` , a AzCopy a fájlokat a parancssor megadása nélkül törli. Ha azt szeretné, hogy a AzCopy törlése előtt megjelenjen egy üzenet, állítsa a jelölőt a következőre: `--delete-destination` `prompt` .

- A véletlen törlés megelőzése érdekében ügyeljen arra, hogy a jelző használata előtt engedélyezze a [Soft delete](../blobs/soft-delete-blob-overview.md) funkciót `--delete-destination=prompt|true` .

## <a name="update-a-container-with-changes-to-a-local-file-system"></a>Tároló frissítése egy helyi fájlrendszer módosításaival

Ebben az esetben a cél a tároló, a forrás pedig a helyi fájlrendszer. 

> [!TIP]
> Ez a példa a Path argumentumokat szimpla idézőjelekkel (' ') fedi le. Használjon egy idézőjelet az összes parancs-rendszerhéjon, kivéve a Windows parancs-rendszerhéjt (cmd.exe). Ha Windows parancs-rendszerhéjt (cmd.exe) használ, az idézőjelek ("") helyett idézőjelek ("") közé foglalja a Path argumentumokat ("").

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Példa** | `azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |

## <a name="update-a-local-file-system-with-changes-to-a-container"></a>Helyi fájlrendszer frissítése egy tároló módosításaival

Ebben az esetben a helyi fájlrendszer a cél, és a tároló a forrás.

> [!TIP]
> Ez a példa a Path argumentumokat szimpla idézőjelekkel (' ') fedi le. Használjon egy idézőjelet az összes parancs-rendszerhéjon, kivéve a Windows parancs-rendszerhéjt (cmd.exe). Ha Windows parancs-rendszerhéjt (cmd.exe) használ, az idézőjelek ("") helyett idézőjelek ("") közé foglalja a Path argumentumokat ("").

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive` |
| **Példa** | `azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive` |

## <a name="update-a-container-with-changes-in-another-container"></a>Tároló frissítése egy másik tároló módosításaival

A parancsban megjelenő első tároló a forrás. A második a cél.

> [!TIP]
> Ez a példa a Path argumentumokat szimpla idézőjelekkel (' ') fedi le. Használjon egy idézőjelet az összes parancs-rendszerhéjon, kivéve a Windows parancs-rendszerhéjt (cmd.exe). Ha Windows parancs-rendszerhéjt (cmd.exe) használ, az idézőjelek ("") helyett idézőjelek ("") közé foglalja a Path argumentumokat ("").

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Példa** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

## <a name="update-a-directory-with-changes-to-a-directory-in-another-container"></a>Könyvtár frissítése egy másik tárolóban lévő könyvtár módosításaival

A parancsban megjelenő első könyvtár a forrás. A második a cél.

> [!TIP]
> Ez a példa a Path argumentumokat szimpla idézőjelekkel (' ') fedi le. Használjon egy idézőjelet az összes parancs-rendszerhéjon, kivéve a Windows parancs-rendszerhéjt (cmd.exe). Ha Windows parancs-rendszerhéjt (cmd.exe) használ, az idézőjelek ("") helyett idézőjelek ("") közé foglalja a Path argumentumokat ("").

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive` |
| **Példa** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive` |

## <a name="synchronize-with-optional-flags"></a>Szinkronizálás opcionális jelzővel

A szinkronizálási műveletet opcionális jelzők használatával is megadhatja. Íme néhány példa.

|Eset|Jelölő|
|---|---|
|Itt adhatja meg, hogy a letöltéskor a szigorúan MD5-kivonatokat kell-e érvényesíteni.|**--ellenőrzési-MD5** = \[ Nincs \| bejelentkezett bejelentkezési \| FailIfDifferent \| FailIfDifferentOrMissing\]|
|Fájlok kizárása mintázat alapján.|**--kizárás – elérési út**|
|Adja meg, hogy a szinkronizálással kapcsolatos naplóbejegyzések milyen részletesek legyenek.|**– naplózási szint** = \[ FIGYELMEZTETÉSi \| hiba – \| \| nincs\]|

A teljes listát itt tekintheti meg: [Beállítások](storage-ref-azcopy-sync.md#options).

## <a name="next-steps"></a>Következő lépések

További példákat a következő cikkekben talál:

- [Példák: Feltöltés](storage-use-azcopy-blobs-upload.md)
- [Példák: Letöltés](storage-use-azcopy-blobs-download.md)
- [Példák: Másolás tárfiókok között](storage-use-azcopy-blobs-copy.md)
- [Példák: Amazon S3-gyűjtők](storage-use-azcopy-s3.md)
- [Példák: Azure Files](storage-use-azcopy-files.md)
- [Oktatóanyag: Helyszíni adatok migrálása felhőtárhelybe az AzCopyval](storage-use-azcopy-migrate-on-premises-data.md)
- [AzCopy konfigurálása, optimalizálása és megoldása](storage-use-azcopy-configure.md)