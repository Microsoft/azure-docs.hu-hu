---
title: Adatok másolása vagy áthelyezése az Azure Storage-ba az AzCopy 10-es | Microsoft Docs
description: Az AzCopy egy parancssori segédprogram, amely segítségével adatokat másolhat tárfiókok között vagy tárfiókok között. Ez a cikk segítséget nyújt az AzCopy letöltéséhez, a tárfiókhoz való csatlakozáshoz és a fájlok átviteléhez.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.custom: contperf-fy21q2
ms.openlocfilehash: 34d3bd45d2c0bf0260a4f8524cff6f8ac03b746c
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107501719"
---
# <a name="get-started-with-azcopy"></a>Bevezetés az AzCopy használatába

Az AzCopy egy parancssori segédprogram, amellyel blobokat és fájlokat másolhat a tárfiókok között. Ez a cikk segítséget nyújt az AzCopy letöltéséhez, a tárfiókhoz való csatlakozáshoz és a fájlok átviteléhez.

> [!NOTE]
> Az AzCopy **10-es** verziója az AzCopy jelenleg támogatott verziója.
>
> Ha az AzCopy egy korábbi verzióját kell használnia, tekintse meg a cikk [Az AzCopy](#previous-version) előző verziójának használata című szakaszát.

<a id="download-and-install-azcopy"></a>

## <a name="download-azcopy"></a>Az AzCopy letöltése

Először töltse le az AzCopy V10 végrehajtható fájlját a számítógép bármely könyvtárába. Az AzCopy V10 csak egy végrehajtható fájl, így semmit nem kell telepítenie.

- [Windows 64 bites](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Windows 32 bites](https://aka.ms/downloadazcopy-v10-windows-32bit) (zip)
- [Linux x86-64](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [macOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

Ezek a fájlok zip-fájlként (Windows és Mac) vagy tar-fájlként (Linux) vannak tömörítve. A tar-fájl Linux rendszeren való letöltéséhez és kibontáshoz tekintse meg a Linux-disztribúció dokumentációját.

> [!NOTE]
> Ha adatokat szeretne másolni az [Azure Table Storage](../tables/table-storage-overview.md) szolgáltatásba és onnan másba, telepítse az [AzCopy 7.3-as verzióját.](https://aka.ms/downloadazcopynet)

## <a name="run-azcopy"></a>Az AzCopy futtatása

Az egyszerű használat érdekében érdemes lehet hozzáadni az AzCopy végrehajtható fájljának helyét a rendszer elérési útjához. Így a rendszer `azcopy` bármely könyvtárában begépelhet.

Ha úgy dönt, hogy nem adja hozzá az AzCopy könyvtárat az elérési úthoz, a könyvtárakat át kell választania a végrehajtható AzCopy-fájl helyére, és be kell gépelje a vagy a Windows PowerShell `azcopy` `.\azcopy` a parancssorba.

Az Azure Storage-fiók tulajdonosaként nem kap automatikusan engedélyeket az adatok eléréséhez. Mielőtt bármi értelmeset is el tud tenni az AzCopyval, el kell döntenie, hogyan fogja megadni az engedélyezési hitelesítő adatokat a tárolási szolgáltatásnak. 

<a id="choose-how-youll-provide-authorization-credentials"></a>

## <a name="authorize-azcopy"></a>Az AzCopy jogosultsága

Az engedélyezési hitelesítő adatokat az Azure Active Directory (AD) vagy egy közös hozzáférésű jogosultsági jogosultságú (SAS) jogkivonattal használhatja.

Használja ezt a táblázatot útmutatóként:

| Tárolási típus | Jelenleg támogatott engedélyezési módszer |
|--|--|
|**Blob Storage** | Azure AD és SAS |
|**Blob Storage (hierarchikus névtér)** | Azure AD és SAS |
|**File Storage** | Csak SAS |

#### <a name="option-1-use-azure-active-directory"></a>1. lehetőség: A Azure Active Directory

Ez a lehetőség csak a Blob Storage-hez érhető el. A Azure Active Directory hitelesítő adatokat egyszer is meg lehet adni ahelyett, hogy SAS-jogkivonatot kell hozzáfűzni minden parancshoz.  

> [!NOTE]
> Ha a jelenlegi kiadásban blobokat tervez átmásolni a tárfiókok között, hozzá kell fűzni egy SAS-jogkivonatot minden forrás URL-címéhez. Az SAS-jogkivonatot csak a cél URL-címből hagyhatja ki. Példákért lásd: [Blobok másolása tárfiókok között.](#transfer-data)

A hozzáférés Azure AD-val való hozzáférésének az AzCopy és a Azure Active Directory [(Azure AD)](storage-use-azcopy-authorize-azure-active-directory.md)használatával való hozzáférésének engedélyével Azure Active Directory lásd: .

#### <a name="option-2-use-a-sas-token"></a>2. lehetőség: SAS-jogkivonat használata

Sas-jogkivonatot fűzhet az AzCopy-parancsokban használt forrás- vagy cél-URL-címekhez.

Ez a példaparancs rekurzív módon másol adatokat egy helyi könyvtárból egy blobtárolóba. A rendszer egy fiktív SAS-jogkivonatot fűz a tároló URL-címének végéhez.

```azcopy
azcopy copy "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

Az SAS-jogkivonatokkal és a lekért jogkivonatokkal kapcsolatos további információkért lásd: Közös hozzáférésű [jogosultságkivonatok (SAS) használata.](./storage-sas-overview.md)

> [!NOTE]
> A [tárfiók Biztonságos átvitelre](storage-require-secure-transfer.md) van szükség beállítás határozza meg, hogy a tárfiókhoz való csatlakozás biztonságos-Transport Layer Security (TLS) segítségével. Alapértelmezés szerint ez a beállítás engedélyezett.   

<a id="transfer-data"></a>

## <a name="transfer-data"></a>Adatátvitel

Miután engedélyezte az identitását, vagy beszerezte az SAS-jogkivonatot, megkezdheti az adatok átvitelét.

Példaparancsok keresését az alábbi cikkekben találja.

| Szolgáltatás | Cikk |
|--------|-----------|
|Azure Blob Storage|[Fájlok feltöltése Azure Blob Storage](storage-use-azcopy-blobs-upload.md) |
|Azure Blob Storage|[Blobok letöltése a Azure Blob Storage](storage-use-azcopy-blobs-download.md)|
|Azure Blob Storage|[Blobok másolása Azure Storage-fiókok között](storage-use-azcopy-blobs-copy.md)|
|Azure Blob Storage|[Szinkronizálás Azure Blob Storage](storage-use-azcopy-blobs-synchronize.md)|
|Azure Files |[Adatok átvitele az AzCopy használatával és fájltárolás](storage-use-azcopy-files.md)|
|Amazon S3|[Adatok másolása az Amazon S3-ból az Azure Storage-ba](storage-use-azcopy-s3.md)|
|Google Cloud Storage|[Adatok másolása a Google Cloud Storage-ból az Azure Storage-ba (előzetes verzió)](storage-use-azcopy-google-cloud.md)|
|Azure Stack storage|[Adatok átvitele az AzCopy és a Azure Stack használatával](/azure-stack/user/azure-stack-storage-transfer#azcopy)|

## <a name="get-command-help"></a>Segítség a parancshoz

A parancsok listájának megjelenik, ha begépeli a parancsot, `azcopy -h` majd nyomja le az ENTER billentyűt.

Ha többet szeretne megtudni egy adott parancsról, csak írja be a parancs nevét (például: `azcopy list -h` ).

> [!div class="mx-imgBorder"]
> ![Beágyazott súgó](media/storage-use-azcopy-v10/azcopy-inline-help.png)

### <a name="list-of-commands"></a>Parancsok listája

Az alábbi táblázat felsorolja az összes AzCopy v10-parancsot. Minden parancs egy referenciacikkre hivatkozik. 

|Parancs|Leírás|
|---|---|
|[azcopy bench](storage-ref-azcopy-bench.md?toc=/azure/storage/blobs/toc.json)|Teljesítménytesztet futtat tesztadatok feltöltésével vagy letöltésével egy adott helyre vagy helyről.|
|[azcopy copy](storage-ref-azcopy-copy.md?toc=/azure/storage/blobs/toc.json)|Forrásadatokat másol egy célhelyre|
|[azcopy doc](storage-ref-azcopy-doc.md?toc=/azure/storage/blobs/toc.json)|Markdown formátumú dokumentációt hoz létre az eszközhöz.|
|[azcopy env](storage-ref-azcopy-env.md?toc=/azure/storage/blobs/toc.json)|Megjeleníti az AzCopy viselkedését konfiguráló környezeti változókat.|
|[azcopy jobs](storage-ref-azcopy-jobs.md?toc=/azure/storage/blobs/toc.json)|A feladatok kezeléséhez kapcsolódó alparancsok.|
|[azcopy jobs clean](storage-ref-azcopy-jobs-clean.md?toc=/azure/storage/blobs/toc.json)|Távolítsa el az összes feladat napló- és tervfájlját.|
|[azcopy jobs list](storage-ref-azcopy-jobs-list.md?toc=/azure/storage/blobs/toc.json)|Az összes feladatra vonatkozó információkat jeleníti meg.|
|[azcopy jobs remove](storage-ref-azcopy-jobs-remove.md?toc=/azure/storage/blobs/toc.json)|Távolítsa el az adott feladatazonosítóhoz társított összes fájlt.|
|[azcopy jobs resume](storage-ref-azcopy-jobs-resume.md?toc=/azure/storage/blobs/toc.json)|Folytatja a meglévő feladatot a megadott feladatazonosítóval.|
|[azcopy jobs show](storage-ref-azcopy-jobs-show.md?toc=/azure/storage/blobs/toc.json)|Az adott feladatazonosító részletes információit jeleníti meg.|
|[azcopy load](storage-ref-azcopy-load.md)|Adott formátumban történő adatátvitelhez kapcsolódó alparancsok.|
|[azcopy load clfs](storage-ref-azcopy-load-avere-cloud-file-system.md?toc=/azure/storage/blobs/toc.json)|Helyi adatokat továbbít egy tárolóba, és a Microsoft Avere Cloud FileSystem (CLFS) formátumában tárolja őket.|
|[azcopy list](storage-ref-azcopy-list.md?toc=/azure/storage/blobs/toc.json)|Felsorolja az egy adott erőforrásban található entitásokat.|
|[azcopy login](storage-ref-azcopy-login.md?toc=/azure/storage/blobs/toc.json)|Az Azure Storage-Azure Active Directory eléréséhez bejelentkezik az adatbázisba.|
|[azcopy logout](storage-ref-azcopy-logout.md?toc=/azure/storage/blobs/toc.json)|Kijelentkezteti a felhasználót, és megszünteti az Azure Storage-erőforrásokhoz való hozzáférést.|
|[azcopy make](storage-ref-azcopy-make.md?toc=/azure/storage/blobs/toc.json)|Létrehoz egy tárolót vagy fájlmegosztást.|
|[azcopy remove](storage-ref-azcopy-remove.md?toc=/azure/storage/blobs/toc.json)|Blobok vagy fájlok törlése egy Azure Storage-fiókból.|
|[azcopy sync](storage-ref-azcopy-sync.md?toc=/azure/storage/blobs/toc.json)|Replikálja a forráshelyet a célhelyre.|

## <a name="use-in-a-script"></a>Használat szkriptben

#### <a name="obtain-a-static-download-link"></a>Statikus letöltési hivatkozás beszerzése

Idővel az AzCopy letöltési [hivatkozása](#download-and-install-azcopy) az AzCopy új verzióira mutat. Ha a szkript letölti az AzCopyt, előfordulhat, hogy a szkript nem fog tovább dolgozni, ha az AzCopy egy újabb verziója módosítja a szkript által használt szolgáltatásokat.

A problémák elkerülése érdekében szerezzen be egy statikus (változatlan) hivatkozást az AzCopy aktuális verziójára. Így a szkript minden futtatáskor letölti az AzCopy ugyanazon verzióját.

A hivatkozás beszerzéséhez futtassa az alábbi parancsot:

| Operációs rendszer  | Parancs |
|--------|-----------|
| **Linux** | `curl -s -D- https://aka.ms/downloadazcopy-v10-linux | grep ^Location` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).headers.location` |

> [!NOTE]
> Linux rendszeren a parancs eltávolítja a verziónevet tartalmazó legfelső szintű mappát, és ehelyett közvetlenül az aktuális mappába bontja `--strip-components=1` `tar` ki a bináris fájlt. Ez lehetővé teszi, hogy a szkript a új verziójával `azcopy` frissült, csak az `wget` URL-cím frissítésével.

Az URL-cím megjelenik a parancs kimenetében. A szkript ezután az URL-cím használatával letöltheti az AzCopyt.

| Operációs rendszer  | Parancs |
|--------|-----------|
| **Linux** | `wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

#### <a name="escape-special-characters-in-sas-tokens"></a>Különleges karakterek escape-karakterének kivédése SAS-jogkivonatok használatával

A kiterjesztésű batch-fájlokban meg kell szabadulni az SAS-jogkivonatok `.cmd` `%` karaktereitől. Ezt úgy teheti meg, ha egy további karaktert ad hozzá a `%` `%` meglévő karakterekhez az SAS-jogkivonat sztringjében.

#### <a name="run-scripts-by-using-jenkins"></a>Szkriptek futtatása a Jenkins használatával

Ha szkriptek futtatására [a Jenkinst](https://jenkins.io/) tervezi használni, mindenképpen helyezze a következő parancsot a szkript elejére.

```
/usr/bin/keyctl new_session
```

## <a name="use-in-azure-storage-explorer"></a>Használat a Azure Storage Explorer

[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) az AzCopy használatával végzi el az összes adatátviteli műveletet. Az Storage Explorer [](https://azure.microsoft.com/features/storage-explorer/) az AzCopy teljesítménybeli előnyeinek kihasználása érdekében, de inkább grafikus felhasználói felületet szeretne használni a parancssor helyett a fájlokhoz.

Storage Explorer a fiókkulcsot használja a műveletek végrehajtásához, így miután bejelentkezik a Storage Explorer, nem kell további hitelesítő adatokat megadnia.

<a id="previous-version"></a>

## <a name="configure-optimize-and-fix"></a>Konfigurálás, optimalizálás és javítás

Tekintse meg az alábbi erőforrások bármelyikét:

- [Az AzCopy konfigurációs beállításai](storage-ref-azcopy-configuration-settings.md)

- [Az AzCopy teljesítményének optimalizálása](storage-use-azcopy-optimize.md)

- [Az AzCopy 10-es hibák elhárítása az Azure Storage-ban naplófájlok használatával](storage-use-azcopy-configure.md)

## <a name="use-a-previous-version"></a>Korábbi verzió használata

Ha az AzCopy előző verzióját kell használnia, tekintse meg az alábbi hivatkozások valamelyikét:

- [AzCopy Windowson (8-as verzió)](/previous-versions/azure/storage/storage-use-azcopy)

- [AzCopy Linux rendszeren (v7)](/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="next-steps"></a>Következő lépések

Ha kérdése, problémái vagy általános visszajelzése van, küldje el őket [a GitHub-oldalon.](https://github.com/Azure/azure-storage-azcopy)
