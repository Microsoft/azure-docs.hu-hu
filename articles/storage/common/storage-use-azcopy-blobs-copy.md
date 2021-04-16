---
title: Blobok másolása Azure Storage-fiókok között az AzCopy 10-es | Microsoft Docs
description: Ez a cikk az AzCopy példaparancsok gyűjteményét tartalmazza, amelyek segítségével blobokat másolhat tárfiókok között.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: bfdc91ac8f4ce618052cc78e76b27e8bdeabeb77
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502980"
---
# <a name="copy-blobs-between-azure-storage-accounts-by-using-azcopy"></a>Blobok másolása Azure-tárfiókok között az AzCopy használatával

Blobokat, könyvtárakat és tárolókat másolhat tárfiókok között az AzCopy 10-es parancssori segédprogrammal. 

Ha más típusú feladatokra, például fájlok feltöltésére, blobok letöltésére és Blob Storage-szinkronizálásra vonatkozó példákat keres, tekintse meg a cikk További lépések [szakaszában](#next-steps) található hivatkozásokat.

Az AzCopy [kiszolgálók közötti API-kat](/rest/api/storageservices/put-block-from-url) [használ,](/rest/api/storageservices/put-page-from-url)így az adatok másolása közvetlenül a tárolókiszolgálók között történik. Ezek a másolási műveletek nem használják a számítógép hálózati sávszélességét.

Az AzCopy letöltéséhez és az engedélyezési hitelesítő adatok társzolgáltatáshoz való megadásának módjairól lásd: [Az AzCopy](storage-use-azcopy-v10.md)első lépések. 

## <a name="guidelines"></a>Irányelvek

Alkalmazza az alábbi irányelveket az AzCopy-parancsokra. 

- Az ügyfélnek hálózati hozzáféréssel kell lennie a forrás- és a céltárfiókhoz is. További információ az egyes tárfiókok hálózati beállításainak konfigurálásról: Azure Storage-tűzfalak és [virtuális hálózatok konfigurálása.](storage-network-security.md?toc=/azure/storage/blobs/toc.json)

- Sas-jogkivonat hozzáfűzése minden forrás URL-címhez. 

  Ha hitelesítő adatokat ad meg Azure Active Directory (Azure AD) használatával, az SAS-jogkivonatot csak a cél URL-címről hagyhatja ki. Győződjön meg arról, hogy beállította a megfelelő szerepköröket a célfiókban. Lásd: [1. lehetőség: A Azure Active Directory.](storage-use-azcopy-v10.md?toc=/azure/storage/blobs/toc.json#option-1-use-azure-active-directory) 

  A cikkben található példák feltételezik, hogy az Azure AD használatával hitelesítette az identitását, így a példák kihagyják a SAS-jogkivonatokat a cél URL-címből.

-  Ha prémium szintű blokkblob-tárfiókba másol, a másolási műveletből kihagyhatja a blob hozzáférési rétegét a (például: ) érték `s2s-preserve-access-tier` `false` `--s2s-preserve-access-tier=false` beállításával. A prémium szintű blokkblob-tárfiókok nem támogatják a hozzáférési szinteket. 

- Ha hierarchikus névtérhez tartozó fiókba vagy fiókból másol, az URL-szintaxis helyett használja a `blob.core.windows.net` `dfs.core.windows.net` következőt: . [A több protokollos hozzáférés Data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) lehetővé teszi a használatát, és ez az egyetlen támogatott szintaxis a fiókmásoló `blob.core.windows.net` forgatókönyvek esetében. 

- A másolási műveletek átviteli sebességét a környezeti változó értékének beállításával `AZCOPY_CONCURRENCY_VALUE` növelheti. További információ: [Az egyidejűség növelése.](storage-use-azcopy-optimize.md#increase-concurrency) 

- Ha a forrásblobok indexcímkéket tartalmaznak, és meg szeretné őrizni őket, akkor újra kell azokat a célblobban is alkalmazásramal látnia. Az indexcímkék beállításával kapcsolatos információkért lásd a cikk Blobok másolása egy másik tárfiókba [indexcímkével](#copy-between-accounts-and-add-index-tags) című szakaszát.

## <a name="copy-a-blob"></a>Blob másolása

Másol egy blobot egy másik tárfiókba az [azcopy copy paranccsal.](storage-ref-azcopy-copy.md) 

> [!TIP]
> Ez a példa az elérésiút-argumentumokat ad meg egyszeres idézőjelek ('') közé. Használjon egyszeres idézőjeleket az összes parancshéjban, kivéve a Windows parancshéjat (cmd.exe). Ha Windows parancshéjat (cmd.exe) használ, az elérésiút-argumentumokat idézőjelek ("") közé kell tenni a single quotes ('' helyett).

**Syntax**

`azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'`

**Példa**

```azcopy
azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'
```

A másolási művelet szinkron módon megy végbe, ezért a parancs visszatérése azt jelzi, hogy a fájlok másolása megtörtént. 

## <a name="copy-a-directory"></a>Könyvtár másolása

Másolja a könyvtárat egy másik tárfiókba az [azcopy copy paranccsal.](storage-ref-azcopy-copy.md) 

> [!TIP]
> Ez a példa az elérésiút-argumentumokat ad meg egyszeres idézőjelek ('') közé. Használjon egyszeres idézőjeleket az összes parancshéjban, kivéve a Windows parancshéjat (cmd.exe). Ha Windows parancshéjat (cmd.exe) használ, az elérésiút-argumentumokat idézőjelek ("") közé kell tenni a single quotes ('' helyett).

**Syntax**

`azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive`

**Példa**

```azcopy
azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive
```

A másolási művelet szinkron módon megy végbe, ezért a parancs visszatérése azt jelzi, hogy a fájlok másolása megtörtént.

## <a name="copy-a-container"></a>Tároló másolása

Az azcopy copy paranccsal másolhatja a tárolót egy másik [tárfiókba.](storage-ref-azcopy-copy.md)

> [!TIP]
> Ez a példa az elérésiút-argumentumokat ad meg egyszeres idézőjelek ('') közé. Használjon egyszeres idézőjeleket az összes parancshéjban, kivéve a Windows parancshéjat (cmd.exe). Ha Windows parancshéjat (cmd.exe) használ, az elérésiút-argumentumokat idézőjelek ("") közé kell tenni a single quotes ('' helyett).

**Syntax**

`azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive`

**Példa**

```azcopy
azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive
```

A másolási művelet szinkron módon megy végbe, ezért a parancs visszatérése azt jelzi, hogy a fájlok másolása megtörtént.

## <a name="copy-containers-directories-and-blobs"></a>Tárolók, könyvtárak és blobok másolása

Az azcopy copy paranccsal másolja át az összes tárolót, könyvtárat és blobot egy másik [tárfiókba.](storage-ref-azcopy-copy.md)

> [!TIP]
> Ez a példa az elérésiút-argumentumokat ad meg egyszeres idézőjelek ('') közé. Használjon egyszeres idézőjeleket az összes parancshéjban, kivéve a Windows parancshéjat (cmd.exe). Ha Windows parancshéjat (cmd.exe) használ, az elérésiút-argumentumokat idézőjelek ("") közé kell tenni a single quotes ('' helyett).

**Syntax**

`azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive`

**Példa**

```azcopy
azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive
```

A másolási művelet szinkron módon megy végbe, ezért a parancs visszatérése azt jelzi, hogy a fájlok másolása megtörtént.

<a id="copy-between-accounts-and-add-index-tags"></a>

## <a name="copy-blobs-and-add-index-tags"></a>Blobok másolása és indexcímkék hozzáadása

Blobok másolása egy másik tárfiókba, és [blobindex-címkék (előzetes verzió)](../blobs/storage-manage-find-blobs.md) hozzáadása a célblobhoz.

Ha Azure AD-hitelesítést használ, a rendszerbiztonsági taghoz a [Storage-blobadatok](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) tulajdonosa szerepkört kell hozzárendelni, vagy engedélyt kell adni számára az Azure-erőforrás-szolgáltatói művelethez egy egyéni `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` [](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) Azure-szerepkör használatával. Ha sas-jogkivonatot használ, a jogkivonatnak SAS-engedéllyel kell hozzáférést adnia a blob `t` címkéihez.

Címkék hozzáadásához használja a lehetőséget `--blob-tags` egy URL-kódolású kulcs-érték párral együtt. 

Ha például a kulcsot és a értéket szeretné hozzáadni, adja hozzá a értéket `my tag` a `my tag value` `--blob-tags='my%20tag=my%20tag%20value'` célparaméterhez. 

Több indexcímkét kell elkülönítenünk egy és ( `&` ) használatával.  Ha például egy kulcsot és egy értéket szeretne hozzáadni, a teljes beállítási sztring `my second tag` `my second tag value` a következő lesz: `--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` .

Az alábbi példák a beállítás használatát `--blob-tags` mutatják be.

> [!TIP]
> Ezek a példák az elérésiút-argumentumokat egyszeres idézőjelek ('' ) közé ékeik. Használjon egyszeres idézőjeleket az összes parancshéjban, kivéve a Windows parancshéjat (cmd.exe). Ha Windows parancshéjat (cmd.exe) használ, az elérésiút-argumentumokat idézőjelek ("") közé kell tenni a single quotes ('' helyett).

**Példa blobra**

```azcopy

`azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'`
```

**Példa címtárra**

```azcopy
`azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'`
```

 **Példa tárolóra**

```azcopy
`azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags="--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'`
```

**Példa fiókra**

```azcopy
`azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive --blob-tags="--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'`
```

A másolási művelet szinkron módon megy végbe, ezért a parancs visszatérése azt jelzi, hogy a fájlok másolása megtörtént.

> [!NOTE]
> Ha címtárat, tárolót vagy fiókot ad meg a forráshoz, a célhelyre másolt összes blob ugyanazokkal a címkékkel fogni, mint a parancsban.

## <a name="copy-with-optional-flags"></a>Másolás választható jelzőkvel

A másolási műveletet opcionális jelzők használatával finomhangolásra használhatja. Az alábbiakban néhány példát talál.

|Eset|Jelölő|
|---|---|
|Blobok másolása blokk-, lap- vagy hozzáfűző blobként.|**--blob-type** = \[ BlockBlob \| PageBlob \| AppendBlob\]|
|Másolja egy adott hozzáférési szintre (például az archív szintre).|**--block-blob-tier** = \[ None \| Hot \| Cool \| Archive\]|
|Fájlok automatikus kibontása.|**--decompress** = \[ gzip \| leeredezett\]|

A teljes listát a beállításokat [lásd:](storage-ref-azcopy-copy.md#options). 

## <a name="next-steps"></a>Következő lépések

További példákat ezekben a cikkekben talál:

- [Példák: Feltöltés](storage-use-azcopy-blobs-upload.md)
- [Példák: Letöltés](storage-use-azcopy-blobs-download.md)
- [Példák: Szinkronizálás](storage-use-azcopy-blobs-synchronize.md)
- [Példák: Amazon S3-gyűjtők](storage-use-azcopy-s3.md)
- [Példák: Google Cloud Storage](storage-use-azcopy-google-cloud.md)
- [Példák: Azure Files](storage-use-azcopy-files.md)
- [Oktatóanyag: Helyszíni adatok migrálása felhőtárhelybe az AzCopyval](storage-use-azcopy-migrate-on-premises-data.md)

A következő cikkekben konfigurálhatja a beállításokat, optimalizálhatja a teljesítményt és elháríthatja a problémákat:

- [AzCopy konfigurációs beállításai](storage-ref-azcopy-configuration-settings.md)
- [Az AzCopy teljesítményének optimalizálása](storage-use-azcopy-optimize.md)
- [Az AzCopy 10-es hibák elhárítása az Azure Storage-ban naplófájlok használatával](storage-use-azcopy-configure.md)