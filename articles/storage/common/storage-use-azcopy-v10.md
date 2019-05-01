---
title: Másolja, vagy az Azure Storage-adatok áthelyezése az AzCopy v10 |} A Microsoft Docs
description: Az AzCopy parancssori segédprogram használatával helyezze át vagy másolhat blob, a data lake és a fájl tartalmát. Adatok másolása az Azure Storage a helyi fájlokból vagy adatmásolás belül vagy tárfiókok között. Egyszerűen migrálhatja az adatokat az Azure Storage.
services: storage
author: seguler
ms.service: storage
ms.topic: article
ms.date: 04/23/2019
ms.author: seguler
ms.subservice: common
ms.openlocfilehash: c1de40b6bf3bb4dc6854a11eca92902203d492c3
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64723186"
---
# <a name="transfer-data-with-azcopy-v10"></a>Adatok áthelyezése az AzCopy 10-es verziójával

Az AzCopy a parancssori segédprogram céljából, vagy a Microsoft Azure Blob- és File storage-ból. Az AzCopy egy áttervezett parancssori felületet kínál, és új architektúráját megbízható adatokat továbbítja. Az AzCopy használatával másolhat adatokat egy fájlrendszer és a egy tárfiókot, vagy tárfiókok között.

## <a name="whats-new-in-azcopy-v10"></a>Az AzCopy v10 újdonságai

- Szinkronizálja a fájlrendszereket az Azure Blob storage- vagy fordítva. Használat `azcopy sync <source> <destination>`. A növekményes másolási forgatókönyvek esetén ideális választás.
- Az Azure Data Lake Storage Gen2 API-kat támogatja. Használat `myaccount.dfs.core.windows.net` , egy URI-t a Data Lake Storage Gen2 API-k meghívására.
- Támogatja a teljes fiókra (csak Blob szolgáltatás) másolása egy másik fiókba.
- Az adatok másolása az Amazon Web Services S3 gyűjtő támogatja.
- Az új [URL-címről Blokkelraktározási](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) fiók-fiók másolási API-kkal. Az adatátvitel azért gyorsabb, mert az ügyfél az átvitel nem szükséges.
- Sorolja fel, vagy eltávolítja a fájlok, blobok és a egy adott elérési úton.
- Támogatja a helyettesítő karakterek mintái egy elérési utat, és – kizárási jelzők.
- A feladat rendelés és a egy kapcsolódó naplófájlt hoz létre minden egyes AzCopy-példány. Megtekintheti és korábbi feladatok újraindítása, és folytathatja a sikertelen feladatokat. Az AzCopy is automatikusan megkísérli egy átviteli hiba után.
- Szolgáltatások általános teljesítménnyel kapcsolatos fejlesztések.

## <a name="download-and-install-azcopy"></a>Töltse le és telepítse az Azcopyval

### <a name="latest-production-version-v10"></a>Éles legújabb (v10)

Töltse le az AzCopy legújabb verzióját:
- [Windows](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

### <a name="latest-azcopy-supporting-table-storage-service-v73"></a>Az AzCopy legújabb Table storage szolgáltatás (v7.3) támogatása

Töltse le a [másolhatja az adatokat és- tárolókról a Microsoft Azure Table storage szolgáltatás támogatása az AzCopy v7.3](https://aka.ms/downloadazcopynet).

## <a name="post-installation-steps"></a>Telepítés utáni lépések

Az AzCopy egy telepítése nem igényel. Nyissa meg az előnyben részesített parancssori alkalmazás, és keresse meg azt a mappát, `azcopy.exe` található. Ha szükséges, az AzCopy mappa helyét is hozzáadhat a használat megkönnyítése érdekében a rendszer elérési útjához.

## <a name="authentication-options"></a>Hitelesítési lehetőségek

Az AzCopy támogatja a következő beállításokat, hitelesítéséhez az Azure Storage:
- **Az Azure Active Directory** (támogatott **Blob és a Data Lake Storage Gen2 szolgáltatások**). Használat ```.\azcopy login``` bejelentkezni az Azure Active Directoryban.  A felhasználóknak rendelkezniük kell ahhoz ["Storage-Blobadatok Közreműködője" szerepkörrel](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac) írni a Blob storage-ban az Azure Active Directory-hitelesítést. Felügyelt identitások az Azure-erőforrások hitelesítésének használata `azcopy login --identity`.
- **Közös hozzáférésű jogosultságkódok jogkivonataival [támogatott Blobok és fájlok szolgáltatások]**. A közös hozzáférésű jogosultságkód (SAS) hozzáfűzni a blob elérési útja a parancssorban a használatára. Az Azure Portallal, SAS-tokeneket is létrehozhat [Tártallózó](https://blogs.msdn.microsoft.com/jpsanders/2017/10/12/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer/), [PowerShell](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageblobsastoken), vagy más tetszőleges eszközökkel. További információkért lásd: [példák](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2).

## <a name="getting-started"></a>Első lépések

> [!TIP]
> **Inkább grafikus felhasználói felület?**
>
> [Az Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/), egy asztali számítógépre, amely leegyszerűsíti az Azure Storage-adatokkal kezelését, most már használja az AzCopy felgyorsítása az adatátvitelt, és az Azure Storage-ból.
>
> A Storage Explorerben alatt AzCopy engedélyezése a **előzetes** menü.
> ![Az Azure Storage Explorer egy átviteli motorként AzCopy engedélyezése](media/storage-use-azcopy-v10/enable-azcopy-storage-explorer.jpg)

Az AzCopy v10 egy önálló dokumentált szintaxissal rendelkezik. Ha bejelentkezett az Azure Active Directory, az általános szintaxist írja le az alábbihoz hasonló:

```azcopy
.\azcopy <command> <arguments> --<flag-name>=<flag-value>

# Examples if you have logged into the Azure Active Directory:
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/container" --recursive=true
.\azcopy cp "C:\local\path\myfile" "https://account.blob.core.windows.net/container/myfile"
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/container"

# Examples if you're using SAS tokens to authenticate:
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/container?st=2019-04-05T04%3A10%3A00Z&se=2019-04-13T04%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=Qdihej%2Bsbg4AiuyLVyQZklm9pSuVGzX27qJ508wi6Es%3D" --recursive=true
.\azcopy cp "C:\local\path\myfile" "https://account.blob.core.windows.net/container/myfile?st=2019-04-05T04%3A10%3A00Z&se=2019-04-13T04%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=Qdihej%2Bsbg4AiuyLVyQZklm9pSuVGzX27qJ508wi6Es%3D"
```

Itt látható, hogyan kezdheti az elérhető parancsok listáját:

```azcopy
.\azcopy --help
# To use the alias instead
.\azcopy -h
```

A súgóoldalt és a egy bizonyos paranccsal kapcsolatos példák megtekintéséhez futtassa a következő parancsot:

```azcopy
.\azcopy <cmd> --help
# Example:
.\azcopy cp -h
```

## <a name="create-a-blob-container-or-file-share"></a>Hozzon létre egy blob tárolókhoz vagy fájlmegosztásokhoz 

**Blobtároló létrehozása**

```azcopy
.\azcopy make "https://account.blob.core.windows.net/container-name"
```

**Fájlmegosztás létrehozása**

```azcopy
.\azcopy make "https://account.file.core.windows.net/share-name"
```

**Blobtároló létrehozása az Azure Data Lake Storage Gen2**

Ha engedélyezte a hierarchikus névterek a Blob storage-fiókjában, az alábbi parancs segítségével hozzon létre egy új blobtárolót a fájlok feltöltése.

```azcopy
.\azcopy make "https://account.dfs.core.windows.net/top-level-resource-name"
```

## <a name="copy-data-to-azure-storage"></a>Adatok másolása az Azure Storage

A Másolás parancs segítségével adatátvitel a forrásból a célra. A forrás vagy cél lehet v:
- Helyi rendszer
- Az Azure Blob/virtuális könyvtár/tároló URI-t
- Az Azure fájl vagy könyvtár/fájlmegosztás URI
- Az Azure Data Lake Storage Gen2 fájlrendszer/Directory/fájl URI

```azcopy
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
# Using the alias instead 
.\azcopy cp <source path> <destination path> --<flag-name>=<flag-value>
```

A következő parancs a mappában található összes fájlt tölt fel `C:\local\path` rekurzív módon a tároló `mycontainer1`, létrehozásakor `path` könyvtárat a tárolóban. Amikor `--put-md5` jelző van megadva, az AzCopy rendszer kiszámítsa és tárolja az összes fájl md5-kivonat `Content-md5` tulajdonságát a megfelelő blob későbbi használatra.

```azcopy
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true --put-md5
```

A következő parancs a mappában található összes fájlt tölt fel `C:\local\path` (nélkül recursing alkönyvtárak be), a tároló `mycontainer1`:

```azcopy
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --put-md5
```

További példákat talál, használja a következő parancsot:

```azcopy
.\azcopy cp -h
```

## <a name="copy-blob-data-between-two-storage-accounts"></a>Másolja a Blobadatok két storage-fiókok között

Két storage-fiókok közötti másolást használja a [URL blokk Put](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API-t, és nem használja az ügyfél gépének hálózati sávszélességet. Közvetlenül, a két Azure Storage kiszolgálók közötti adatokat másolja az AzCopy egyszerűen hangolja össze a másolási művelet során. Ez a beállítás jelenleg csak a Blob storage számára érhető el.

A Blob típusú adatok között két tárfiókot az összes másolásához használja a következő parancsot:
```azcopy
.\azcopy cp "https://myaccount.blob.core.windows.net/<sastoken>" "https://myotheraccount.blob.core.windows.net/<sastoken>" --recursive=true
```

Egy Blob-tárolóba másolja egy másik Blob-tárolóba, használja a következő parancsot:
```azcopy
.\azcopy cp "https://myaccount.blob.core.windows.net/mycontainer/<sastoken>" "https://myotheraccount.blob.core.windows.net/mycontainer/<sastoken>" --recursive=true
```

## <a name="copy-a-vhd-image-to-a-storage-account"></a>Másolja egy VHD-rendszerképet egy tárfiókba

Alapértelmezés szerint az AzCopy adatait feltölti az a blokkblobok használatát támogatják. A jelző használható hozzáfűző Blobok és Lapblobok fájlok feltöltéséhez `--blob-type=[BlockBlob|PageBlob|AppendBlob]`.

```azcopy
.\azcopy cp "C:\local\path\mydisk.vhd" "https://myotheraccount.blob.core.windows.net/mycontainer/mydisk.vhd<sastoken>" --blob-type=PageBlob
```

## <a name="sync-incremental-copy-and-delete-blob-storage-only"></a>Szinkronizálás: növekményes másolása és törlése (csak Blob storage)

A szinkronizálási parancsot szinkronizálja ahhoz a könyvtárhoz, a cél fájlnevek összehasonlítása a forráskönyvtár tartalmát, és utolsó módosítás az időbélyegek. Ez a művelet célfájlok választható törlésének tartalmazza, ha azok nem léteznek a forrás során a `--delete-destination=prompt|true` jelző van megadva. Alapértelmezés szerint a törlési funkció le van tiltva. 

> [!NOTE] 
> Használja a `--delete-destination` jelző kellő körültekintéssel járjon el. Engedélyezze a [helyreállítható törlési](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) funkció törlési viselkedés a fiókjában véletlen törlés megelőzése érdekében a szinkronban engedélyezése előtt. 
>
> Amikor `--delete-destination` értéke igaz, az AzCopy fájlt töröl, amely nem létezik a cél bármilyen kérdés nélkül a felhasználónak a forrás. Ha azt szeretné, hogy a rendszer megerősítést kér, használja a `--delete-destination=prompt`.

Szinkronizálja a helyi fájlrendszerben egy tárfiókba, használja a következő parancsot:

```azcopy
.\azcopy sync "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true
```

Csak a helyi fájlrendszerben; egy blob-tárolóba is szinkronizálhatók:

```azcopy
# The SAS token isn't required for Azure Active Directory authentication.
.\azcopy sync "https://account.blob.core.windows.net/mycontainer1" "C:\local\path" --recursive=true
```

Ez a parancs növekményes szinkronizálja az adatforrás, a cél az utolsó módosítás időbélyegek alapján. Hozzáadásakor, vagy töröljön egy fájlt a forrás, az AzCopy fog végezze el ugyanezt a célhelyen. A törlés előtt meg kell erősítenie az AzCopy fogja kérni.

## <a name="copy-data-from-amazon-web-services-aws-s3"></a>Adatok másolása az Amazon Web Services (AWS) S3-ból

Az AWS S3 gyűjtőt hitelesítéshez, állítsa be az alábbi környezeti változókat:

```
# For Windows:
set AWS_ACCESS_KEY_ID=<your AWS access key>
set AWS_SECRET_ACCESS_KEY=<AWS secret access key>
# For Linux:
export AWS_ACCESS_KEY_ID=<your AWS access key>
export AWS_SECRET_ACCESS_KEY=<AWS secret access key>
# For MacOS
export AWS_ACCESS_KEY_ID=<your AWS access key>
export AWS_SECRET_ACCESS_KEY=<AWS secret access key>
```

Blob-tárolóba másolja ki a gyűjtőhöz, adja ki a következő parancsot:

```
.\azcopy cp "https://s3.amazonaws.com/mybucket" "https://myaccount.blob.core.windows.net/mycontainer?<sastoken>" --recursive
```

További információ az AWS S3, az AzCopy használatával adatait átmásolásához: a lap [Itt](https://github.com/Azure/azure-storage-azcopy/wiki/Copy-from-AWS-S3).

## <a name="advanced-configuration"></a>Speciális konfiguráció

### <a name="configure-proxy-settings"></a>Proxybeállítások konfigurálása

Konfigurálja a proxybeállításokat az AzCopy v10, állítsa be a környezeti változó https_proxy a következő paranccsal:

```cmd
# For Windows:
set https_proxy=<proxy IP>:<proxy port>
# For Linux:
export https_proxy=<proxy IP>:<proxy port>
# For MacOS
export https_proxy=<proxy IP>:<proxy port>
```

### <a name="optimize-throughput"></a>Teljesítmény optimalizálása

Állítsa be a környezeti változó konfigurálása az egyidejű kérelmek száma, valamint szabályozhatja az átviteli sebesség teljesítmény- és erőforrás-felhasználás AZCOPY_CONCURRENCY_VALUE. Az érték alapértelmezés szerint 300 értéke. Az érték csökkentése korlátozza a sávszélességet és CPU-használatának AzCopy v10.

```cmd
# For Windows:
set AZCOPY_CONCURRENCY_VALUE=<value>
# For Linux:
export AZCOPY_CONCURRENCY_VALUE=<value>
# For MacOS
export AZCOPY_CONCURRENCY_VALUE=<value>
# To check the current value of the variable on all the platforms
.\azcopy env
# If the value is blank then the default value is currently in use
```

### <a name="change-the-location-of-the-log-files"></a>A naplófájlok helyének módosítása

Módosíthatja a helyet, a naplófájlok, ha szükséges, vagy az operációsrendszer-lemez betelőben elkerülése érdekében.

```cmd
# For Windows:
set AZCOPY_LOG_LOCATION=<value>
# For Linux:
export AZCOPY_LOG_LOCATION=<value>
# For MacOS
export AZCOPY_LOG_LOCATION=<value>
# To check the current value of the variable on all the platforms
.\azcopy env
# If the value is blank, then the default value is currently in use
```
### <a name="change-the-default-log-level"></a>Az alapértelmezett naplózási szint módosításához 

Alapértelmezés szerint az AzCopy naplózási szint INFO értéke. Ha szeretné, hogy lemezterületet a napló részletességi csökkentésére, felülírja a beállítás használatával ``--log-level`` lehetőséget. Elérhető naplózási szintek a következők: HIBAKERESÉSI információ, figyelmeztetés, hiba, KILÉPJEN és végzetes.

### <a name="review-the-logs-for-errors"></a>Tekintse át a hibákat a naplók

A következő parancsot a 04dc9ca9-158f-7945-5933-564021086c79 naplóból UPLOADFAILED állapota az összes hiba jelenik meg:

```azcopy
cat 04dc9ca9-158f-7945-5933-564021086c79.log | grep -i UPLOADFAILED
```
## <a name="troubleshooting"></a>Hibaelhárítás

Az AzCopy hoz létre a naplófájlok és minden feladat csomag fájljait. A naplók segítségével megvizsgálhatja és az esetleges problémák elhárításában. A naplók tartalmazni fogja az állapotát, hiba (UPLOADFAILED COPYFAILED és DOWNLOADFAILED), a teljes elérési útja, és a hiba okát. A feladat-naplók és a terv fájlok találhatók, a % USERPROFILE\\Windows vagy $HOME .azcopy mappa\\.azcopy mappába a Mac és Linux rendszereken.

> [!IMPORTANT]
> Amikor egy kérés elküldése Support (vagy a hiba elhárításához bármely harmadik féltől származó használata esetén), megoszthatja a kivonatosan verzióját szeretné végrehajtani a parancsot. Ez biztosítja a SAS véletlenül ne oszthassák meg olyan bárki. A kivont verzió elején. a naplófájl található.

### <a name="view-and-resume-jobs"></a>Feladatok megtekintése és folytatása

Minden adatátviteli műveletet egy AzCopy feladatot hoz létre. A következő paranccsal-feladatok előzményeinek megtekintése:

```azcopy
.\azcopy jobs list
```

A feladat statisztikai adatok megtekintéséhez használja a következő parancsot:

```azcopy
.\azcopy jobs show <job-id>
```

Szűrés állapot szerint átvitelek, használja a következő parancsot:

```azcopy
.\azcopy jobs show <job-id> --with-status=Failed
```

A következő paranccsal egy sikertelen vagy megszakítva feladat folytatása. Állandó biztonsági okokból nem ezt a parancsot használja az azonosítót és a SAS-jogkivonat:

```azcopy
.\azcopy jobs resume <jobid> --source-sas="<sastokenhere>"
.\azcopy jobs resume <jobid> --destination-sas="<sastokenhere>"
```

## <a name="next-steps"></a>További lépések

Ha kérdése, problémák vagy általános visszajelzést, küldje el őket [a Githubon](https://github.com/Azure/azure-storage-azcopy).


