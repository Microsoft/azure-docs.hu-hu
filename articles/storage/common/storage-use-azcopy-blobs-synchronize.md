---
title: Szinkronizálás azure blobtárolóval az AzCopy 10-es | Microsoft Docs
description: Ez a cikk az AzCopy példaparancsok gyűjteményét tartalmazza, amelyek segítségével szinkronizálhat az Azure Blob Storage-ral.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 8b3340c00d856b13edefc7728d5baa327399a441
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502929"
---
# <a name="synchronize-with-azure-blob-storage-by-using-azcopy"></a>Szinkronizálás az Azure Blob Storage-ral az AzCopy használatával

A helyi tárolót az Azure Blob Storage-ral szinkronizálhatja az AzCopy 10-es parancssori segédprogrammal. 

A helyi fájlrendszer tartalmát szinkronizálhatja egy blobtárolóval. A tárolókat és a virtuális könyvtárakat szinkronizálhatja is egymással. A szinkronizálás egy módszer. Más szóval ön dönti el, hogy a két végpont közül melyik a forrás, és melyik a cél. A szinkronizálás kiszolgáló és kiszolgáló API-kat is használ. Az ebben a szakaszban bemutatott példák hierarchikus névtéren lévő fiókokkal is működnek. 

> [!NOTE]
> Az AzCopy jelenlegi kiadása nem szinkronizál más források és célok között (például File Storage vagy Amazon Web Services (AWS) S3-gyűjtők között).

Ha más típusú feladatokra, például fájlok feltöltésére, blobok letöltésére vagy fiókok közötti blobok másolására vonatkozó példákat keres, tekintse meg a cikk Következő lépések [szakaszában](#next-steps) található hivatkozásokat.

## <a name="get-started"></a>Bevezetés

Az [AzCopy letöltését](storage-use-azcopy-v10.md) az Első lépések az AzCopyval cikkben olvashatja el, és megtudhatja, hogyan biztosítanak hitelesítési hitelesítő adatokat a tárolási szolgáltatásnak.

> [!NOTE] 
> A cikkben található példák feltételezik, hogy hitelesítő adatokat adott meg a Azure Active Directory (Azure AD) használatával.
>
> Ha inkább SAS-jogkivonatot használna a blobadatokhoz való hozzáféréshez, akkor minden AzCopy-parancsban hozzáfűzheti a jogkivonatot az erőforrás URL-címéhez. Példa: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="guidelines"></a>Irányelvek

- A [szinkronizálási](storage-ref-azcopy-sync.md) parancs összehasonlítja a fájlneveket és a legutóbb módosított időbélyegeket. Állítsa a választható jelzőt a vagy értékre a célkönyvtárban lévő fájlok törléséhez, ha ezek a fájlok már nem léteznek a `--delete-destination` `true` `prompt` forráskönyvtárban.

- Ha a jelzőt a következőre adja meg: , az AzCopy kérés nélkül `--delete-destination` `true` töröl fájlokat. Ha azt szeretné, hogy egy üzenet jelenjen meg, mielőtt az AzCopy töröl egy fájlt, állítsa a `--delete-destination` jelzőt a következőre: `prompt` .

- Ha a jelzőt vagy -ként tervezi beállítani, fontolja meg a másolási parancs a szinkronizálási parancs helyett való használatát, és állítsa a paramétert a `--delete-destination` `prompt` `false` [](storage-ref-azcopy-copy.md) [](storage-ref-azcopy-sync.md) `--overwrite` következőre: `ifSourceNewer` . A [másolási](storage-ref-azcopy-copy.md) parancs kevesebb memóriát használ fel, és kevesebb számlázási költséget jelent, mert a másolási műveletnek nem kell indexelnie a forrást vagy a célt a fájlok mozgatása előtt. 

- A véletlen törlések elkerülése érdekében [](../blobs/soft-delete-blob-overview.md) a jelző használata előtt engedélyezze a törlési `--delete-destination=prompt|true` funkciót.

- A gépnek, amelyen a szinkronizálási parancsot futtatja, pontos rendszerórával kell lennie, mert az utolsó módosítás időpontja kritikus fontosságú annak meghatározásában, hogy át kell-e helyezni egy fájlt. Ha a rendszer jelentős órajel-eltolást tartalmaz, ne módosítsa a fájlokat a célhelyen ahhoz az időponthoz túl közel, amikor szinkronizálási parancsot tervez futtatni.

## <a name="update-a-container-with-changes-to-a-local-file-system"></a>Tároló frissítése a helyi fájlrendszer módosításaival

Ebben az esetben a cél a tároló, a forrás pedig a helyi fájlrendszer. 

> [!TIP]
> Ez a példa az elérésiút-argumentumokat ad meg egyszeres idézőjelek ('') közé. Használjon egyszeres idézőjeleket az összes parancshéjban, kivéve a Windows parancshéjat (cmd.exe). Ha Windows parancshéjat (cmd.exe) használ, az elérésiút-argumentumokat idézőjelek ("") közé kell tenni a single quotes ('' helyett).

**Syntax**

`azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive`

**Példa**

```azcopy
azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive
```

## <a name="update-a-local-file-system-with-changes-to-a-container"></a>Helyi fájlrendszer frissítése egy tároló módosításaival

Ebben az esetben a cél a helyi fájlrendszer, a tároló pedig a forrás.

> [!TIP]
> Ez a példa az elérésiút-argumentumokat ad meg egyszeres idézőjelek ('') közé. Használjon egyszeres idézőjeleket az összes parancshéjban, kivéve a Windows parancshéjat (cmd.exe). Ha Windows parancshéjat (cmd.exe) használ, az elérésiút-argumentumokat idézőjelek ("") közé kell tenni a single quotes ('' helyett).

**Syntax**

`azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive`

**Példa**

```azcopy
azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive
```

## <a name="update-a-container-with-changes-in-another-container"></a>Tároló frissítése egy másik tárolóban történt módosításokkal

A parancsban elsőként megjelenő tároló a forrás. A második a célhely.

> [!TIP]
> Ez a példa az elérésiút-argumentumokat ad meg egyszeres idézőjelek ('') közé. Használjon egyszeres idézőjeleket az összes parancshéjban, kivéve a Windows parancshéjat (cmd.exe). Ha Windows parancshéjat (cmd.exe) használ, az elérésiút-argumentumokat idézőjelek ("") közé kell tenni a single quotes ('' helyett).

**Syntax**

`azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive`

**Példa**

```azcopy
azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive
```

## <a name="update-a-directory-with-changes-to-a-directory-in-another-container"></a>Címtár frissítése egy másik tárolóban található címtár módosításaival

A parancsban elsőként megjelenő könyvtár a forrás. A második a célhely.

> [!TIP]
> Ez a példa az elérésiút-argumentumokat ad meg egyszeres idézőjelek ('') közé. Használjon egyszeres idézőjeleket az összes parancshéjban, kivéve a Windows parancshéjat (cmd.exe). Ha Windows parancshéjat (cmd.exe) használ, az elérésiút-argumentumokat idézőjelek ("") közé kell tenni a single quotes ('' helyett).

**Syntax**

`azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive`

**Példa**

```azcopy
azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive
```

## <a name="synchronize-with-optional-flags"></a>Szinkronizálás választható jelzőkvel

A szinkronizálási műveletet nem kötelező jelzők használatával finomhangolni tudja. Látható néhány példa.

|Eset|Jelölő|
|---|---|
|Adja meg, hogy a letöltéskor szigorúan az MD5-hashek érvényesítése milyen legyen.|**--check-md5** = \[ NoCheck \| LogOnly \| FailIfDifferent \| FailIfDifferentOrMissing\]|
|Fájlok kizárása minta alapján.|**--exclude-path**|
|Adja meg, hogy milyen részletes legyen a szinkronizálással kapcsolatos naplóbejegyzés.|**--log-level** = \[ FIGYELMEZTETÉSI \| HIBA \| – \| INFORMÁCIÓ: NINCS\]|

A jelzők teljes listájáért tekintse meg a [beállításokat.](storage-ref-azcopy-sync.md#options)

> [!NOTE]
> A `--recursive` jelző alapértelmezés szerint a `true` következőre van beállítva: . A és a jelző csak a fájlnevekre vonatkozik, a fájl elérési útjának más `--exclude-pattern` `--include-pattern` részeire nem. 

## <a name="next-steps"></a>Következő lépések

További példákat ezekben a cikkekben talál:

- [Példák: Feltöltés](storage-use-azcopy-blobs-upload.md)
- [Példák: Letöltés](storage-use-azcopy-blobs-download.md)
- [Példák: Másolás tárfiókok között](storage-use-azcopy-blobs-copy.md)
- [Példák: Amazon S3-gyűjtők](storage-use-azcopy-s3.md)
- [Példák: Google Cloud Storage](storage-use-azcopy-google-cloud.md)
- [Példák: Azure Files](storage-use-azcopy-files.md)
- [Oktatóanyag: Helyszíni adatok migrálása felhőtárhelybe az AzCopyval](storage-use-azcopy-migrate-on-premises-data.md)

A következő cikkekben konfigurálhatja a beállításokat, optimalizálhatja a teljesítményt és elháríthatja a problémákat:

- [AzCopy konfigurációs beállításai](storage-ref-azcopy-configuration-settings.md)
- [Az AzCopy teljesítményének optimalizálása](storage-use-azcopy-optimize.md)
- [Az AzCopy 10-es hibák elhárítása az Azure Storage-ban naplófájlok használatával](storage-use-azcopy-configure.md)

