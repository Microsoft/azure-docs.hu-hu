---
title: Az Azure Storage-ba másolhatja vagy áthelyezheti az AzCopy v10 használatával | Microsoft Docs
description: A AzCopy egy parancssori segédprogram, amellyel az adatok átmásolhatók a, a-ból vagy a Storage-fiókok között. Ez a cikk segítséget nyújt az AzCopy letöltéséhez, a tárfiókhoz való csatlakozáshoz és a fájlok átviteléhez.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/08/2020
ms.author: normesta
ms.subservice: common
ms.custom: contperf-fy21q2
ms.openlocfilehash: 826effb152d3f069e0a7c5b8e169f7083117ed22
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103494495"
---
# <a name="get-started-with-azcopy"></a>Bevezetés az AzCopy használatába

Az AzCopy egy parancssori segédprogram, amellyel blobokat és fájlokat másolhat a tárfiókok között. Ez a cikk segítséget nyújt az AzCopy letöltéséhez, a tárfiókhoz való csatlakozáshoz és a fájlok átviteléhez.

> [!NOTE]
> A AzCopy **v10** a AzCopy jelenleg támogatott verziója.
>
> Ha a AzCopy egy korábbi verzióját kell használnia, tekintse meg a jelen cikk a [AzCopy előző verziójának használata](#previous-version) című szakaszát.

<a id="download-and-install-azcopy"></a>

## <a name="download-azcopy"></a>Az AzCopy letöltése

Először töltse le a AzCopy V10-es végrehajtható fájlt a számítógép bármely könyvtárába. A AzCopy v10 csak egy végrehajtható fájl, ezért nem kell telepítenie.

- [Windows 64 bites](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Windows 32 bites](https://aka.ms/downloadazcopy-v10-windows-32bit) (zip)
- [Linux x86-64](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

Ezek a fájlok zip-fájlként (Windows és Mac) vagy tar-fájlként (Linux) vannak tömörítve. A kátrányos fájl Linux rendszeren való letöltéséhez és kibontásához tekintse meg a Linux-disztribúció dokumentációját.

> [!NOTE]
> Ha át szeretné másolni az adatait az [Azure Table Storage](../tables/table-storage-overview.md) szolgáltatásba és onnan, akkor telepítse az [AzCopy 7,3](https://aka.ms/downloadazcopynet)-es verzióját.


## <a name="run-azcopy"></a>Az AzCopy futtatása

Az egyszerű használat érdekében érdemes lehet hozzáadni az AzCopy végrehajtható fájljának helyét a rendszer elérési útjához. Így bármilyen könyvtárból beírhatja a `azcopy` rendszerét.

Ha úgy dönt, hogy nem adja hozzá a AzCopy könyvtárat az elérési úthoz, akkor módosítania kell a címtárakat a AzCopy végrehajtható fájljának és típusának, `azcopy` vagy `.\azcopy` a Windows PowerShell-parancssorban.

A parancsok listájának megtekintéséhez írja be a parancsot, `azcopy -h` majd nyomja le az ENTER billentyűt.

Egy adott parancs megismeréséhez egyszerűen adja meg a parancs nevét (például: `azcopy list -h` ).

> [!div class="mx-imgBorder"]
> ![Beágyazott Súgó](media/storage-use-azcopy-v10/azcopy-inline-help.png)


Az egyes parancsok és parancsok paramétereit részletesen ismertető dokumentációban találja: [azcopy](storage-ref-azcopy.md)

> [!NOTE] 
> Az Azure Storage-fiók tulajdonosaként nem rendel automatikusan hozzáférési engedélyeket az adateléréshez. Mielőtt bármit is AzCopy, el kell döntenie, hogyan adja meg az engedélyezési hitelesítő adatokat a Storage szolgáltatás számára. 

<a id="choose-how-youll-provide-authorization-credentials"></a>

## <a name="authorize-azcopy"></a>AzCopy engedélyezése

Az engedélyezési hitelesítő adatokat Azure Active Directory (AD) vagy közös hozzáférésű aláírási (SAS-) token használatával biztosíthatja.

A táblázat használata útmutatóként:

| Tárolási típus | Jelenleg támogatott hitelesítési módszer |
|--|--|
|**Blob Storage** | Azure AD és SAS |
|**BLOB Storage (hierarchikus névtér)** | Azure AD és SAS |
|**File Storage** | Csak SAS |

#### <a name="option-1-use-azure-active-directory"></a>1. lehetőség: a Azure Active Directory használata

Ez a beállítás csak a blob Storage esetében érhető el. Azure Active Directory használatával hitelesítő adatokat adhat meg, ahelyett, hogy SAS-tokent kellene hozzáfűzni az egyes parancsokhoz.  

> [!NOTE]
> Ha a jelenlegi kiadásban Blobok másolását tervezi a Storage-fiókok között, akkor minden forrás URL-címhez hozzá kell fűzni egy SAS-jogkivonatot. Az SAS-tokent csak a cél URL-címről hagyhatja ki. Példák: [Blobok másolása a Storage-fiókok között](#transfer-data).

Az Azure AD-vel való hozzáférés engedélyezéséhez lásd: [hozzáférés engedélyezése a blobokhoz a AzCopy és a Azure Active Directory (Azure ad)](storage-use-azcopy-authorize-azure-active-directory.md)használatával.

#### <a name="option-2-use-a-sas-token"></a>2. lehetőség: SAS-token használata

A AzCopy-parancsokban használt minden forráshoz vagy cél URL-címhez hozzáfűzhető SAS-token.

A példában szereplő parancs rekurzív módon másolja át az adatait egy helyi könyvtárból egy blob-tárolóba. A rendszer kitalált SAS-jogkivonatot fűz a tároló URL-címének végéhez.

```azcopy
azcopy copy "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

Ha többet szeretne megtudni az SAS-jogkivonatokról és a beszerzéséről, tekintse meg a [közös hozzáférésű aláírások (SAS) használata](./storage-sas-overview.md)című témakört.

<a id="transfer-data"></a>

## <a name="transfer-data"></a>Adatátvitel

Miután engedélyezte a személyazonosságát, vagy SAS-tokent kapott, megkezdheti az adatok átvitelét.

> [!NOTE]
> A Storage-fiók [biztonságos átviteléhez szükséges](storage-require-secure-transfer.md) beállítás határozza meg, hogy a Storage-fiókhoz való kapcsolódás TRANSPORT Layer Security (TLS) védelemmel van-e ellátva. Alapértelmezés szerint ez a beállítás engedélyezett.   

A parancsok megkereséséhez tekintse meg a cikkek bármelyikét.

| Szolgáltatás | Cikk |
|--------|-----------|
|Azure Blob Storage |[Fájlok feltöltése az Azure Blob Storageba](storage-use-azcopy-blobs-upload.md)<br><br>[Blobok letöltése az Azure-ból Blob Storage](storage-use-azcopy-blobs-download.md)<br><br>[Blobok másolása az Azure Storage-fiókok között](storage-use-azcopy-blobs-copy.md)<br><br>[Szinkronizálás az Azure Blob Storage](storage-use-azcopy-blobs-synchronize.md)|
|Azure Files |[Adatok átvitele az AzCopy használatával és fájltárolás](storage-use-azcopy-files.md)|
|Amazon S3|[Adatok másolása az Amazon S3-ból az Azure Storage-ba](storage-use-azcopy-s3.md)|
|Google Cloud Storage|[Adatok másolása a Google Cloud Storage-ból az Azure Storage-ba (előzetes verzió)](storage-use-azcopy-google-cloud.md)|
|Azure Stack Storage|[Adatok átvitele a AzCopy és a Azure Stack Storage szolgáltatással](/azure-stack/user/azure-stack-storage-transfer#azcopy)|

## <a name="use-in-a-script"></a>Használat parancsfájlokban

#### <a name="obtain-a-static-download-link"></a>Statikus letöltési hivatkozás beszerzése

Idővel a AzCopy [letöltési hivatkozása](#download-and-install-azcopy) a AzCopy új verzióira mutat. Ha a parancsfájl letölti a AzCopy-t, a parancsfájl leállhat, ha a AzCopy újabb verziója módosítja a parancsfájltól függő funkciókat.

A probléma elkerüléséhez szerezzen be egy statikus (változatlan) hivatkozást a AzCopy aktuális verziójára. Így a parancsfájl a AzCopy azonos pontos verzióját tölti le minden egyes futtatásakor.

A hivatkozás beszerzéséhez futtassa a következő parancsot:

| Operációs rendszer  | Parancs |
|--------|-----------|
| **Linux** | `curl -s -D- https://aka.ms/downloadazcopy-v10-linux | grep ^Location` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).headers.location` |

> [!NOTE]
> Linux esetén a `--strip-components=1` `tar` parancs eltávolítja a verziószámot tartalmazó legfelső szintű mappát, a bináris fájlt pedig közvetlenül az aktuális mappába bontja ki. Ez lehetővé teszi, hogy a parancsfájl a új verziójával frissüljön, és `azcopy` csak az `wget` URL-címet frissítse.

Az URL-cím megjelenik a parancs kimenetében. A szkript ezután letöltheti a AzCopy az adott URL-cím használatával.

| Operációs rendszer  | Parancs |
|--------|-----------|
| **Linux** | `wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

#### <a name="escape-special-characters-in-sas-tokens"></a>Speciális karakterek elmenekülése SAS-jogkivonatokban

A kiterjesztésű batch-fájlokban el kell `.cmd` menekülnie a `%` sas-jogkivonatokban megjelenő karaktereket. Ezt úgy teheti meg, hogy hozzáad egy további `%` karaktert `%` az SAS-jogkivonat karakterláncának meglévő karaktereinek mellett.

#### <a name="run-scripts-by-using-jenkins"></a>Parancsfájlok futtatása a Jenkins használatával

Ha azt tervezi, hogy a [Jenkins](https://jenkins.io/) használatával futtatja a parancsfájlokat, ügyeljen arra, hogy a parancsfájl elején helyezze el a következő parancsot.

```
/usr/bin/keyctl new_session
```

## <a name="use-in-azure-storage-explorer"></a>Használat Azure Storage Explorer

[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) a AzCopy használatával hajtja végre az összes adatátviteli műveletét. Ha a AzCopy teljesítménybeli előnyeit szeretné kihasználni, használhatja a [Storage Explorert](https://azure.microsoft.com/features/storage-explorer/) , de a parancssor helyett inkább grafikus felhasználói felületet szeretne használni a fájlokkal való kommunikációhoz.

Storage Explorer a fiók kulcsát használja a műveletek végrehajtásához, ezért a Storage Explorer való bejelentkezés után nem kell további hitelesítési hitelesítő adatokat megadnia.

<a id="previous-version"></a>

## <a name="configure-optimize-and-fix"></a>Konfigurálás, optimalizálás és javítás

Lásd: [AzCopy konfigurálása, optimalizálása és hibáinak megoldása](storage-use-azcopy-configure.md)

## <a name="use-a-previous-version"></a>Korábbi verzió használata

Ha a AzCopy előző verzióját kell használnia, tekintse meg az alábbi hivatkozások egyikét:

- [AzCopy Windowson (8-as verzió)](/previous-versions/azure/storage/storage-use-azcopy)

- [AzCopy Linux rendszeren (v7)](/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="next-steps"></a>Következő lépések

Ha kérdése van, problémája vagy általános visszajelzése van, küldje el őket [a GitHub](https://github.com/Azure/azure-storage-azcopy) oldalon.
