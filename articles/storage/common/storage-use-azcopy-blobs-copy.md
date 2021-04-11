---
title: Blobok másolása az Azure Storage-fiókok között a AzCopy v10-vel | Microsoft Docs
description: Ez a cikk olyan AzCopy-példákat tartalmaz, amelyek segítenek a Blobok közötti másolásban a Storage-fiókok között.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/08/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 2db19ee30314988d17eae62ae11ad7ff3c79d0cb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105728929"
---
# <a name="copy-blobs-between-azure-storage-accounts-by-using-azcopy-v10"></a>Blobok másolása az Azure Storage-fiókok között a AzCopy v10 használatával

A Blobok, könyvtárak és tárolók a AzCopy v10 parancssori segédprogram használatával másolhatók a Storage-fiókok között. 

Ha más típusú feladatok, például fájlok feltöltése, blobok letöltése és blob Storage használatával történő szinkronizálás céljából szeretne példákat látni, tekintse meg a jelen cikk [következő lépések](#next-steps) című szakaszának hivatkozásait.

A AzCopy [kiszolgálók](/rest/api/storageservices/put-block-from-url) közötti [API](/rest/api/storageservices/put-page-from-url)-kat használ, így az Adatmásolás közvetlenül a Storage-kiszolgálók között történik. Ezek a másolási műveletek nem használják a számítógép hálózati sávszélességét.

A AzCopy letöltéséhez és a tárolási szolgáltatás hitelesítési hitelesítő adatainak megadásával kapcsolatos további információkért lásd: Ismerkedés a [AzCopy](storage-use-azcopy-v10.md)használatával. 

## <a name="guidelines"></a>Irányelvek

Alkalmazza a következő irányelveket a AzCopy parancsaira. 

- Az ügyfélnek hálózati hozzáféréssel kell rendelkeznie mind a forrás-, mind a cél Storage-fiókhoz. Az egyes Storage-fiókok hálózati beállításainak konfigurálásáról az [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](storage-network-security.md?toc=/azure/storage/blobs/toc.json)című cikk nyújt tájékoztatást.

- Fűzze hozzá az SAS-tokent az egyes forrás URL-címekhez. 

  Ha Azure Active Directory (Azure AD) használatával ad meg hitelesítési hitelesítő adatokat, csak a cél URL-címről hagyhatja el az SAS-tokent. Győződjön meg arról, hogy beállította a megfelelő szerepköröket a célkiszolgálón. Lásd [az 1. lehetőséget: Azure Active Directory használata](storage-use-azcopy-v10.md?toc=/azure/storage/blobs/toc.json#option-1-use-azure-active-directory). 

  A cikkben szereplő példák azt feltételezik, hogy az Azure AD-vel hitelesítette az identitását, így a példák kihagyják az SAS-jogkivonatokat a cél URL-címről.

-  Ha egy prémium szintű blokk blob Storage-fiókba másol, hagyja ki a blob hozzáférési szintjét a másolási műveletből a `s2s-preserve-access-tier` (z) értékre való beállításával `false` (például: `--s2s-preserve-access-tier=false` ). A prémium szintű blokk blob Storage-fiókok nem támogatják a hozzáférési szinteket. 

- Ha egy hierarchikus névteret tartalmazó fiókból vagy egy olyan fiókból másol, amely az `blob.core.windows.net` `dfs.core.windows.net` URL-cím szintaxisa helyett használja. A [több protokollon keresztüli hozzáférés Data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) lehetővé teszi a használatát `blob.core.windows.net` , és ez az egyetlen támogatott szintaxis a fiók másolási forgatókönyvek esetében. 

- A környezeti változó értékének megadásával növelheti a másolási műveletek átviteli sebességét `AZCOPY_CONCURRENCY_VALUE` . További információ: az [átviteli sebesség optimalizálása](storage-use-azcopy-configure.md#optimize-throughput). 

- Ha a forrás Blobok indexelési címkékkel rendelkeznek, és meg szeretné őrizni ezeket a címkéket, újra kell alkalmaznia azokat a célhelyen lévő blobokra. További információ az indexelési címkék beállításáról: [Blobok másolása másik Storage-fiókba](#copy-between-accounts-and-add-index-tags) a jelen cikk index címkék szakaszában.

## <a name="copy-a-blob"></a>BLOB másolása

Másolja a blobot egy másik Storage-fiókba az [azcopy Copy](storage-ref-azcopy-copy.md) paranccsal. 

> [!TIP]
> Ez a példa a Path argumentumokat szimpla idézőjelekkel (' ') fedi le. Használjon egy idézőjelet az összes parancs-rendszerhéjon, kivéve a Windows parancs-rendszerhéjt (cmd.exe). Ha Windows parancs-rendszerhéjt (cmd.exe) használ, az idézőjelek ("") helyett idézőjelek ("") közé foglalja a Path argumentumokat ("").

| Szintaxis/példa  |  Code |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'` |
| **Példa** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |

A másolási művelet szinkron módon megy végbe, ezért a parancs visszatérése azt jelzi, hogy a fájlok másolása megtörtént. 

## <a name="copy-a-directory"></a>Könyvtár másolása

Másolja a könyvtárat egy másik Storage-fiókba az [azcopy Copy](storage-ref-azcopy-copy.md) paranccsal. 

> [!TIP]
> Ez a példa a Path argumentumokat szimpla idézőjelekkel (' ') fedi le. Használjon egy idézőjelet az összes parancs-rendszerhéjon, kivéve a Windows parancs-rendszerhéjt (cmd.exe). Ha Windows parancs-rendszerhéjt (cmd.exe) használ, az idézőjelek ("") helyett idézőjelek ("") közé foglalja a Path argumentumokat ("").

| Szintaxis/példa  |  Code |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Példa** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

A másolási művelet szinkron módon megy végbe, ezért a parancs visszatérése azt jelzi, hogy a fájlok másolása megtörtént.

## <a name="copy-a-container"></a>Tároló másolása

Másolja a tárolót egy másik Storage-fiókba az [azcopy Copy](storage-ref-azcopy-copy.md) paranccsal.

> [!TIP]
> Ez a példa a Path argumentumokat szimpla idézőjelekkel (' ') fedi le. Használjon egy idézőjelet az összes parancs-rendszerhéjon, kivéve a Windows parancs-rendszerhéjt (cmd.exe). Ha Windows parancs-rendszerhéjt (cmd.exe) használ, az idézőjelek ("") helyett idézőjelek ("") közé foglalja a Path argumentumokat ("").

| Szintaxis/példa  |  Code |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Példa** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

A másolási művelet szinkron módon megy végbe, ezért a parancs visszatérése azt jelzi, hogy a fájlok másolása megtörtént.

## <a name="copy-containers-directories-and-blobs"></a>Tárolók, könyvtárak és Blobok másolása

Másolja az összes tárolót, könyvtárat és blobot egy másik Storage-fiókba az [azcopy másolási](storage-ref-azcopy-copy.md) parancs használatával.

> [!TIP]
> Ez a példa a Path argumentumokat szimpla idézőjelekkel (' ') fedi le. Használjon egy idézőjelet az összes parancs-rendszerhéjon, kivéve a Windows parancs-rendszerhéjt (cmd.exe). Ha Windows parancs-rendszerhéjt (cmd.exe) használ, az idézőjelek ("") helyett idézőjelek ("") közé foglalja a Path argumentumokat ("").

| Szintaxis/példa  |  Code |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive` |
| **Példa** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |

A másolási művelet szinkron módon megy végbe, ezért a parancs visszatérése azt jelzi, hogy a fájlok másolása megtörtént.

<a id="copy-between-accounts-and-add-index-tags"></a>

## <a name="copy-blobs-and-add-index-tags"></a>Blobok másolása és tárgymutató-Címkék hozzáadása

Másolja a blobokat egy másik Storage-fiókba, és adja hozzá a [blob-index címkéit (előzetes verzió)](../blobs/storage-manage-find-blobs.md) a cél blobhoz.

Ha Azure AD-hitelesítést használ, a rendszerbiztonsági tag számára a [Storage blob-adattulajdonosi](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) szerepkört kell hozzárendelni, vagy az `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` [Azure erőforrás-szolgáltatói művelethez](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) egy egyéni Azure-szerepkörön keresztül kell engedélyt adni. Ha közös hozzáférésű aláírási (SAS-) tokent használ, a tokennek a sas engedélyen keresztül kell hozzáférést biztosítania a blob címkéhez `t` .

Címkék hozzáadásához használja a `--blob-tags` kapcsolót egy URL-címmel kódolt kulcs-érték párokkal együtt. 

Például a kulcs `my tag` és egy érték hozzáadásához `my tag value` adja hozzá a ( `--blob-tags='my%20tag=my%20tag%20value'` z) paramétert. 

Több indexelési címkét is elkülönítheti egy jel ( `&` ) használatával.  Ha például egy kulcsot és egy értéket szeretne felvenni `my second tag` `my second tag value` , a teljes beállítás sztring lenne `--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` .

Az alábbi példák bemutatják, hogyan használhatja a `--blob-tags` kapcsolót.

> [!TIP]
> Ezek a példák egyetlen idézőjelekkel (' ') foglalják el az elérésiút-argumentumokat. Használjon egy idézőjelet az összes parancs-rendszerhéjon, kivéve a Windows parancs-rendszerhéjt (cmd.exe). Ha Windows parancs-rendszerhéjt (cmd.exe) használ, az idézőjelek ("") helyett idézőjelek ("") közé foglalja a Path argumentumokat ("").

| Példa  |  Code |
|--------|-----------|
| **Blob** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **Címtár** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **Tároló** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags="--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **Fiók** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive --blob-tags="--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |

A másolási művelet szinkron módon megy végbe, ezért a parancs visszatérése azt jelzi, hogy a fájlok másolása megtörtént.

> [!NOTE]
> Ha megad egy könyvtárat, tárolót vagy fiókot a forráshoz, a célhelyre másolt összes blob ugyanazokkal a címkékkel fog rendelkezni, mint a parancsban.

## <a name="copy-with-optional-flags"></a>Másolás opcionális jelzővel

A másolási műveletet opcionális jelzők használatával is megadhatja. Íme néhány példa.

|Eset|Jelölő|
|---|---|
|Blobok másolása blokk, oldal vagy Hozzáfűzés Blobként.|**– blob típusú** = \[ BlockBlob \| PageBlob \| AppendBlob\]|
|Másolás adott hozzáférési szintre (például az archív szintre).|**--Block-blob-réteg** = \[ Nincs \| gyors elérésű \| \| Archívum\]|
|Fájlok automatikus kibontása.|**– Kibontás** = \[ gzip- \| kiengedés\]|

A teljes listát itt tekintheti meg: [Beállítások](storage-ref-azcopy-copy.md#options). 

## <a name="next-steps"></a>Következő lépések

További példákat a következő cikkekben talál:

- [Példák: Feltöltés](storage-use-azcopy-blobs-upload.md)
- [Példák: Letöltés](storage-use-azcopy-blobs-download.md)
- [Példák: Szinkronizálás](storage-use-azcopy-blobs-synchronize.md)
- [Példák: Amazon S3-gyűjtők](storage-use-azcopy-s3.md)
- [Példák: Azure Files](storage-use-azcopy-files.md)
- [Oktatóanyag: Helyszíni adatok migrálása felhőtárhelybe az AzCopyval](storage-use-azcopy-migrate-on-premises-data.md)
- [AzCopy konfigurálása, optimalizálása és megoldása](storage-use-azcopy-configure.md)