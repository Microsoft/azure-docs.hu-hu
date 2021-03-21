---
title: Tárfiókok áttekintése
titleSuffix: Azure Storage
description: Ismerje meg a különböző típusú Storage-fiókokat az Azure Storage-ban. Tekintse át a fiókok elnevezését, a teljesítményszint, a hozzáférési szintek, a redundancia, a titkosítás, a végpontok és egyebek című fejezetet.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/09/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 24d955b0d1c53f57f5927f9e893b6ecd75fb3ca8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102561891"
---
# <a name="storage-account-overview"></a>Tárfiókok áttekintése

Egy Azure Storage-fiók tartalmazza az összes Azure Storage-adatobjektumot: blobokat, fájlokat, várólistákat, táblákat és lemezeket. A Storage-fiók egy egyedi névteret biztosít az Azure Storage-adatok számára, amely a világon bárhonnan elérhető HTTP-vagy HTTPS-kapcsolaton keresztül. Az Azure Storage-fiókban tárolt adatai tartósak, a biztonságos és a nagy mértékben méretezhetők.

Az Azure-tárfiókok létrehozásáról az [Azure-tárfiók létrehozása](storage-account-create.md) című oldalon tájékozódhat.

## <a name="types-of-storage-accounts"></a>A tárfiókok típusai

Az Azure Storage számos különböző típusú Storage-fiókot kínál. Mindegyik típus támogatja a különböző funkciókat, és saját díjszabási modellel rendelkezik. Vegye figyelembe ezeket a különbségeket, mielőtt létrehoz egy Storage-fiókot az alkalmazások számára legmegfelelőbb fiók típusának megállapításához. A Storage-fiókok típusai a következők:

- **Általános célú v2-fiókok**: a blobokhoz, fájlokhoz, várólistákhoz és táblákhoz tartozó alapszintű tárolási fióktípus. Az Azure Storage-t használó legtöbb forgatókönyv esetén ajánlott.
- **Általános célú v1-fiókok**: a Blobok, fájlok, várólisták és táblák örökölt fiókjának típusa. Ha lehetséges, használja az általános célú v2-fiókokat.
- **BlockBlobStorage-fiókok**: a blobok és a Blobok hozzáfűzésére szolgáló prémium szintű teljesítménnyel rendelkező Storage-fiókok. Ajánlott olyan forgatókönyvek esetén, amelyeknél magas a tranzakciós arány, vagy kisebb objektumokat használó forgatókönyvek, vagy ha konzisztensen alacsony tárolási késésre van szükség.
- **FileStorage fiókok**: csak a prémium szintű teljesítménnyel rendelkező Storage-fiókok. Nagyvállalati vagy nagy teljesítményű alkalmazások esetében ajánlott.
- **BlobStorage-fiókok**: örökölt blob Storage-fiókok. Ha lehetséges, használja az általános célú v2-fiókokat.

Az alábbi táblázat a Storage-fiókok, az általuk támogatott szolgáltatások és az egyes fióktípus támogatott üzemi modelljeinek típusait ismerteti:

| Tárfiók típusa | Támogatott szolgáltatások | Redundancia-beállítások | <sup>1</sup> . üzembe helyezési modell |
|--|--|--|--|
| Általános célú v2 | BLOB, fájl, üzenetsor, tábla, lemez és Data Lake Gen2<sup>2</sup> | LRS, GRS, RA-GRS, ZRS, GZRS, RA-GZRS<sup>3</sup> | Resource Manager |
| Általános célú v1 | BLOB, fájl, üzenetsor, tábla és lemez | LRS, GRS, RA-GRS | Resource Manager, klasszikus |
| BlockBlobStorage | BLOB (csak a Blobok letiltása és a Blobok hozzáfűzése) | LRS, ZRS<sup>3</sup> | Resource Manager |
| FileStorage | Csak fájl | LRS, ZRS<sup>3</sup> | Resource Manager |
| BlobStorage | BLOB (csak a Blobok letiltása és a Blobok hozzáfűzése) | LRS, GRS, RA-GRS | Resource Manager |

<sup>1</sup> A Azure Resource Manager üzembe helyezési modell használata ajánlott. A klasszikus üzemi modellt használó Storage-fiókok bizonyos helyszíneken is létrehozhatók, és a meglévő klasszikus fiókok továbbra is támogatottak. További információkért lásd: [Azure Resource Manager a klasszikus üzembe helyezéssel kapcsolatban: az üzembe helyezési modellek és az erőforrások állapotának ismertetése](../../azure-resource-manager/management/deployment-models.md).

<sup>2</sup> A Azure Data Lake Storage Gen2 az Azure Blob Storage-ra épülő, big data Analytics szolgáltatáshoz dedikált képességek összessége. A Data Lake Storage Gen2 csak olyan általános célú v2-es Storage-fiókok esetében támogatott, amelyeken engedélyezve van a hierarchikus névtér. További információ a Data Lake Storage Gen2ről: a [Azure Data Lake Storage Gen2 bemutatása](../blobs/data-lake-storage-introduction.md).

<sup>3</sup> A Zone-redundáns tárolás (ZRS) és a Geo-Zone-redundáns tárolás (GZRS/RA-GZRS) csak a standard általános célú v2-, BlockBlobStorage-és FileStorage-fiókok esetében érhető el bizonyos régiókban. További információ az Azure Storage-redundancia lehetőségeiről: [Azure Storage redundancia](storage-redundancy.md).

### <a name="storage-account-redundancy"></a>Storage-fiók redundancia

A Storage-fiók redundancia-beállításai a következők:

- **Helyileg redundáns tárolás (LRS)**: egyszerű, alacsony költséghatékonyságú redundancia-stratégia. Az Adatmásolást az elsődleges régió egyetlen fizikai helyén egyidejűleg háromszor másolja a rendszer.
- **Zone-redundáns tárolás (ZRS)**: a magas rendelkezésre állást igénylő forgatókönyvek redundancia. Az Adatmásolás szinkron módon történik az elsődleges régió három Azure-beli rendelkezésre állási zónájában.
- **Geo-redundáns tárolás (GRS)**: régiók közötti redundancia a regionális kimaradások elleni védelemhez. Az Adatmásolást az elsődleges régióban háromszor szinkronizálja a rendszer, majd aszinkron módon másolja át a másodlagos régióba. A másodlagos régióban lévő adatolvasási hozzáférés engedélyezéséhez engedélyezze az olvasási hozzáférésű geo-redundáns tárolást (RA-GRS).
- **Geo-Zone-redundáns tárolás (GZRS)**: a magas rendelkezésre állást és a maximális tartósságot igénylő forgatókönyvek redundancia. Az Adatmásolást a rendszer szinkron módon másolja az elsődleges régió három Azure-beli rendelkezésre állási zónájában, majd aszinkron módon másolja át a másodlagos régióba. Ha olvasási hozzáférést szeretne a másodlagos régióban lévő adathozzáféréshez, engedélyezze az olvasási hozzáférésű geo-Zone-redundáns tárolást (RA-GZRS).

Az Azure Storage redundancia-lehetőségeivel kapcsolatos további információkért lásd: [Azure Storage redundancia](storage-redundancy.md).

### <a name="general-purpose-v2-accounts"></a>Általános célú v2-fiókok

Az általános célú v2 Storage-fiókok támogatják az Azure Storage legújabb funkcióit, és az általános célú v1-és blob Storage-fiókok összes funkcióját beépítik. Az általános célú v2-fiókok az Azure Storage-ban a legalacsonyabb/GB-os kapacitást biztosítják, valamint az iparágban versenyképes tranzakciós árakat. Az általános célú v2 Storage-fiókok támogatják ezeket az Azure Storage-szolgáltatásokat:

- Blobok (az összes típus: letiltás, Hozzáfűzés, lap)
- Data Lake Gen2
- Fájlok
- Lemezek
- Üzenetsorok
- Táblák

> [!NOTE]
> A Microsoft az általános célú v2 Storage-fiók használatát javasolja a legtöbb forgatókönyvhöz. Az általános célú v1-vagy blob Storage-fiókok egyszerűen, leállás nélkül frissíthetők egy általános célú v2-fiókra, anélkül, hogy adatmásolásra lenne szükség. A frissítés azonban nem vonható vissza.
>
> Az általános célú v2-fiókra való frissítéssel kapcsolatos további információkért lásd: [frissítés általános célú v2 Storage-fiókra](storage-account-upgrade.md).

Az általános célú v2 Storage-fiókok több hozzáférési szintet is kínálnak az adatok használati minták alapján történő tárolásához. További információ: [hozzáférési szintek a Blobok adatainak blokkolásához](#access-tiers-for-block-blob-data).

### <a name="general-purpose-v1-accounts"></a>Általános célú v1-fiókok

Az általános célú v1 Storage-fiókok hozzáférést biztosítanak az összes Azure Storage-szolgáltatáshoz, de nem rendelkezhetnek a legújabb szolgáltatásokkal, illetve a legalacsonyabb/gigabájt díjszabással. Az általános célú v1 Storage-fiókok támogatják ezeket az Azure Storage-szolgáltatásokat:

- Blobok (az összes típus)
- Fájlok
- Lemezek
- Üzenetsorok
- Táblák

Az alábbi forgatókönyvekhez használhatja az általános célú v1-fiókokat:

- Alkalmazásaihoz a klasszikus Azure-telepítési modell szükséges. Az általános célú v2-fiókok és a blob Storage-fiókok csak a Azure Resource Manager telepítési modellt támogatják.

- Az alkalmazások tranzakció-igényesek, vagy jelentős földrajzi replikációs sávszélességet használnak, de nem igényelnek nagy kapacitást. Ebben az esetben az általános célú v1 lehet a leggazdaságosabb választás.

- A [Storage REST API Services](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) olyan verzióját használja, amely a 2014-02-14-nál korábbi, vagy a 4. x-nél alacsonyabb verziószámú ügyféloldali kódtár. Az alkalmazás nem frissíthető.

> [!NOTE]
> Bár a Microsoft a legtöbb esetben javasolja az általános célú v2-fiókok használatát, a Microsoft továbbra is támogatja az általános célú v1-fiókokat az új és a meglévő ügyfelek számára. Általános célú v1-es Storage-fiókokat hozhat létre új régiókban, amikor az Azure Storage elérhető ezekben a régiókban. A Microsoft jelenleg nem rendelkezik az általános célú v1-fiókok támogatásának elavulttá tételével, és legalább egy éves előzetes értesítést fog biztosítani az Azure Storage funkciójának elavulttá tételéhez. A Microsoft továbbra is biztosítja az általános célú v1-fiókok biztonsági frissítéseit, de ez a fióktípus nem vár új szolgáltatás-fejlesztést.
>
> Az új Azure Storage-régiókban az általános célú v1-fiókok díjszabása 2020. október 1-től megegyezik az általános célú v2-fiókok díjszabásával ezekben a régiókban. A meglévő Azure Storage-régiók díjszabása nem változott. Az általános célú v1-fiókok egy adott régióban való díjszabásáról az Azure Storage díjszabását ismertető oldalon tájékozódhat. Válassza ki a régiót, majd a **díjszabás** mellett válassza az **egyéb** lehetőséget.

### <a name="blockblobstorage-accounts"></a>BlockBlobStorage-fiókok

A BlockBlobStorage-fiók egy speciális Storage-fiók a prémium szintű teljesítmény szintjén a strukturálatlan objektumok adatai blokk Blobok vagy hozzáfűzési Blobok tárolására. Az általános célú v2-és BlobStorage-fiókokkal összehasonlítva a BlockBlobStorage-fiókok alacsony, konzisztens késést és magasabb tranzakciós sebességet biztosítanak.

A BlockBlobStorage-fiókok jelenleg nem támogatják a leválasztást a gyakori, ritka elérésű vagy archív hozzáférési szintekre. Ez a típusú tárolási fiók nem támogatja a Blobok, táblák vagy várólisták oldalát.

### <a name="filestorage-accounts"></a>FileStorage-fiókok

A FileStorage-fiók a prémium fájlmegosztás tárolására és létrehozására szolgáló speciális Storage-fiók. Ez a Storage-fióktípus támogatja a fájlokat, de nem blokkolja a blobokat, hozzáfűzi a blobokat, a blobokat, a táblákat és a várólistákat

A FileStorage-fiókok egyedi teljesítmény-dedikált jellemzőket kínálnak, például a IOPS-kitörést. A jellemzőkkel kapcsolatos további információkért tekintse meg a fájlok tervezési útmutató [fájlmegosztás tárolási szintjei](../files/storage-files-planning.md#storage-tiers) című szakaszát.

## <a name="naming-storage-accounts"></a>Tárolási fiókok elnevezése

Ne feledje ezeket a szabályokat a tárfiók elnevezésekor:

- A tárfiókok neve 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat.
- A tárfiók nevének egyedinek kell lennie az Azure rendszerben. Két tárfióknak nem lehet azonos neve.

## <a name="performance-tiers"></a>Teljesítményszintek

A létrehozott Storage-fiók típusától függően választhat a standard és a prémium szintű teljesítményszint közül is. A következő táblázat összefoglalja, hogy milyen teljesítményszint érhető el, hogy milyen típusú Storage-fiókhoz.

| Tárfiók típusa | Támogatott teljesítményszint |
|--|--|
| Általános célú v2 | Standard, prémium<sup>1</sup> |
| Általános célú v1 | Standard, prémium<sup>1</sup> |
| BlockBlobStorage | Prémium |
| FileStorage | Prémium |
| BlobStorage | Standard |

<sup>1</sup> Az általános célú v2 és az általános célú v1-fiókok prémium szintű teljesítménye csak a lemezes és az oldal blobja esetében érhető el. A blokk-és hozzáfűző Blobok prémium szintű teljesítménye csak a BlockBlobStorage-fiókokon érhető el. A fájlok prémium szintű teljesítménye csak a FileStorage-fiókokon érhető el.

### <a name="general-purpose-storage-accounts"></a>Általános célú tárfiókok

Az általános célú Storage-fiókok a következő teljesítményszint-szintek bármelyikéhez konfigurálhatók:

- Szabványos teljesítményszint Blobok, fájlok, táblák, várólisták és Azure-beli virtuális gépek lemezei tárolásához. A standard szintű Storage-fiókok méretezhetőségi céljaival kapcsolatos további információkért lásd [a standard szintű Storage-fiókok méretezhetőségi céljait](scalability-targets-standard-account.md)ismertető témakört.
- Prémium szintű teljesítményszint a nem felügyelt virtuális gépek lemezeinek tárolásához. A Microsoft azt javasolja, hogy felügyelt lemezeket használjon az Azure Virtual Machines szolgáltatással nem felügyelt lemezek helyett. A prémium szintű teljesítményszint skálázhatósági céljaival kapcsolatos további információkért lásd: a [prémium lap blob Storage-fiókok méretezhetőségi célpontjai](../blobs/scalability-targets-premium-page-blobs.md).

### <a name="blockblobstorage-storage-accounts"></a>BlockBlobStorage

A BlockBlobStorage Storage-fiókok prémium szintű teljesítményt biztosítanak a blokkos Blobok tárolásához és a Blobok hozzáfűzéséhez. További információ: [a prémium szintű blokk blob Storage-fiókok méretezhetőségi célpontjai](../blobs/scalability-targets-premium-block-blobs.md).

### <a name="filestorage-storage-accounts"></a>FileStorage

Az FileStorage Storage-fiókok prémium szintű teljesítményt biztosítanak az Azure-fájlmegosztás számára. További információ: [Azure Files skálázhatósági és teljesítménybeli célok](../files/storage-files-scale-targets.md).

## <a name="access-tiers-for-block-blob-data"></a>Hozzáférési szintek a blob-adatblokkok számára

Az Azure Storage különböző lehetőségeket biztosít a Blobok adatok használati minták alapján történő eléréséhez. Az Azure Storage-ban az egyes hozzáférési szintek egy adott adattípusra vannak optimalizálva. Az igényeinek megfelelő hozzáférési réteg kiválasztásával a legtöbb költséghatékony módon tárolhatja a blokk blob-adatait.

Az elérhető hozzáférési szintek a következők:

- A **gyors** elérési szint. Ez a szintet a Storage-fiókban lévő objektumok gyakori elérésére optimalizáltuk. A gyors elérésű rétegben az adatok elérése a leghatékonyabb, a tárolási költségek pedig magasabbak. Alapértelmezés szerint a rendszer új Storage-fiókokat hoz létre a gyors elérésű rétegben.
- A **lassú** elérési szint. Ez a csomag olyan nagy mennyiségű, ritkán használt és tárolt adatok tárolására van optimalizálva, amelyek legalább 30 napig vannak tárolva. Az adatok lassú elérésű szinten való tárolása költséghatékonyabb, de az adatokhoz való hozzáférés drágább lehet, mint a gyors elérésű rétegben lévő adatok elérése.
- Az **archív tárolási** szint. Ez a szintet csak az egyes blokkos Blobok esetében érhető el. Az archiválási szint olyan adatokra van optimalizálva, amelyek több órányi lekérési késést tudnak tolerálni, és amelyek legalább 180 napig maradnak az archiválási szinten. Az archiválási szint az adatok tárolására leginkább költséghatékony megoldás. Azonban az adatokhoz való hozzáférés drágább, mint az adatokhoz való hozzáférés a gyors vagy a lassú elérésű szinteken.

Ha módosul az adatok használati mintája, akkor bármikor válthat a hozzáférési szintek között. A hozzáférési szintekkel kapcsolatos további információkért lásd [: Azure Blob Storage: gyakori, ritka elérésű és archív hozzáférési szintek](../blobs/storage-blob-storage-tiers.md).

A következő táblázat bemutatja, hogy mely hozzáférési rétegek érhetők el a blobokhoz az egyes típusú Storage-fiókokban.

| Tárfiók típusa | Támogatott hozzáférési szintek |
|--|--|
| Általános célú v2 | Gyors, ritka, archív<sup>1</sup> |
| Általános célú v1 | N/A |
| BlockBlobStorage | N/A |
| FileStorage | N/A |
| BlobStorage | Gyors, ritka, archív<sup>1</sup> |

<sup>1</sup> az Archive Storage és a blob szintű rétegek csak a blokk blobokat támogatják. Az archív szint csak egy egyedi blob szintjén érhető el, nem pedig a Storage-fiók szintjén. További információ: [Az Azure Blob Storage elérési szintjei – gyakori, ritka és archív](../blobs/storage-blob-storage-tiers.md).

> [!IMPORTANT]
> Egy meglévő Storage-fiók vagy blob hozzáférési rétegének módosítása további díjakat eredményezhet. További információkat a Storage- [fiók számlázása](#storage-account-billing)című témakörben talál.

## <a name="encryption"></a>Titkosítás

A Storage-fiókban lévő összes adat titkosítva van a szolgáltatás oldalán. A titkosítással kapcsolatos további információkért lásd: [Azure-Storage Service encryption a REST-adatokhoz](storage-service-encryption.md).

## <a name="storage-account-endpoints"></a>Tárfiókvégpontok

A Storage-fiók egyedi névteret biztosít az Azure-ban az adataihoz. Az Azure Storage-ban tárolt összes objektumhoz tartozik egy olyan címe, amely tartalmazza az egyedi fióknevet. A fiók neve és az Azure Storage szolgáltatás végpontja a Storage-fiókhoz tartozó végpontokat képezi le.

A következő táblázat felsorolja az egyes Azure Storage-szolgáltatások végpontait.

| Tárolási szolgáltatás | Végpont |
|--|--|
| Blob Storage | `https://<storage-account>.blob.core.windows.net` |
| 2. generációs Azure Data Lake Storage | `https://<storage-account>.dfs.core.windows.net` |
| Azure Files | `https://<storage-account>.file.core.windows.net` |
| Queue Storage | `https://<storage-account>.queue.core.windows.net` |
| Table Storage | `https://<storage-account>.table.core.windows.net` |

> [!NOTE]
> A blob-és blob Storage-fiókok csak a Blob service végpontot teszik elérhetővé.

Hozzon létre egy objektum elérésére szolgáló URL-címet egy Storage-fiókban úgy, hogy hozzáfűzi az objektum helyét a Storage-fiókban a végponthoz. Például egy blobcím formátuma lehet a következő: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

A Storage-fiók a Blobok egyéni tartományának használatára is konfigurálható. További információ: [Egyéni tartománynév beállítása az Azure Storage-fiókhoz](../blobs/storage-custom-domain-name.md).  

## <a name="control-access-to-account-data"></a>Fiókadatok hozzáférésének szabályozása

Alapértelmezés szerint a fiókban tárolt adatok csak Ön, azaz a fiók tulajdonosa számára érhetőek el. Ön szabályozhatja, hogy ki férhet hozzá az adataihoz, és milyen engedélyekkel rendelkezik.

Minden, a Storage-fiókra vonatkozó kérelmet engedélyezni kell. A szolgáltatás szintjén a kérelemnek tartalmaznia kell egy érvényes *engedélyezési* fejlécet. Pontosabban, ez a fejléc tartalmazza az összes olyan információt, amely ahhoz szükséges, hogy a szolgáltatás a végrehajtás előtt érvényesítse a kérést.

A következő módszerek bármelyikével biztosíthat hozzáférést a Storage-fiókban található adathoz:

- **Azure Active Directory:** Használjon Azure Active Directory (Azure AD) hitelesítő adatokat egy felhasználó, csoport vagy más identitás hitelesítéséhez a blob-és üzenetsor-adatokhoz való hozzáféréshez. Ha az identitás hitelesítése sikeres, az Azure AD visszaadja az Azure Blob Storage-ba vagy a várólista-tárolóba való kérelem engedélyezéséhez használandó tokent. További információ: az [Azure Storage hozzáférésének hitelesítése Azure Active Directory használatával](storage-auth-aad.md).
- **Megosztott kulcs engedélyezése:** Használja a Storage-fiók hozzáférési kulcsát olyan kapcsolati karakterlánc létrehozásához, amelyet az alkalmazás a futásidőben használ az Azure Storage eléréséhez. A kapcsolódási karakterláncban szereplő értékek az Azure Storage-nak átadott *engedélyezési* fejléc összeállítására szolgálnak. További információ: az [Azure Storage-beli kapcsolatok karakterláncának konfigurálása](storage-configure-connection-string.md).
- **Közös hozzáférési aláírás:** A közös hozzáférésű aláírás (SAS) egy olyan token, amely lehetővé teszi az erőforrásokhoz való delegált hozzáférést a Storage-fiókban. Az SAS-jogkivonat az URL-címen az Azure Storage-ba irányuló kérés engedélyezéséhez szükséges összes információt beágyazza. SAS létrehozásakor megadhatja, hogy az SAS milyen engedélyeket biztosítson az erőforrásnak, és az engedélyek érvényességi időtartamát. SAS-tokent az Azure AD hitelesítő adataival vagy megosztott kulccsal lehet aláírni. További információ: [korlátozott hozzáférés engedélyezése az Azure Storage-erőforrásokhoz közös hozzáférésű aláírások (SAS) használatával](storage-sas-overview.md).

> [!NOTE]
> Az Azure AD-beli hitelesítő adatokkal rendelkező felhasználók vagy alkalmazások hitelesítése kiváló biztonságot és könnyű használatot biztosít más engedélyezési módokon. Habár továbbra is használhatja a megosztott kulcsos engedélyezést az alkalmazásokkal, az Azure AD megkerülésével megkerüli a fiók hozzáférési kulcsának a kóddal való tárolásának szükségességét. Továbbra is használhatja a közös hozzáférésű aláírásokat (SAS) a Storage-fiók erőforrásaihoz való részletes hozzáférés biztosításához, de az Azure AD hasonló képességeket kínál, anélkül, hogy az SAS-tokeneket kellene kezelnie, vagy nem kell aggódnia a sérült SAS visszavonásával kapcsolatban.
>
> A Microsoft az Azure AD-hitelesítés használatát javasolja az Azure Storage blob-és üzenetsor-alkalmazásaihoz, ha lehetséges.

## <a name="copying-data-into-a-storage-account"></a>Adatok másolása egy Storage-fiókba

A Microsoft segédprogramokat és könyvtárakat biztosít az adatok helyszíni tárolóeszközökből vagy külső felhőalapú tárolási szolgáltatóktól történő importálásához. Az Ön által használt megoldás az átvitt adatok mennyiségétől függ.

Ha egy általános célú v1-vagy blob Storage-fiókból egy általános célú v2-fiókra frissít, az adatok automatikusan áttelepülnek. A Microsoft ezt az utat javasolja a fiók frissítéséhez. Ha azonban úgy dönt, hogy egy általános célú v1-fiók adatait egy blob Storage-fiókba helyezi át, akkor manuálisan kell áttelepítenie az adatait az alább ismertetett eszközök és kódtárak használatával.

### <a name="azcopy"></a>AzCopy

Az AzCopy egy Windows parancssori segédprogram, amely az adatok az Azure Storage szolgáltatásba vagy onnan máshová való nagyteljesítményű másolására lett kifejlesztve. A AzCopy használatával egy meglévő általános célú Storage-fiókból másolhatja át az adatait egy blob Storage-fiókba, vagy feltölthet adatokkal a helyszíni tárolóeszközökből. További információt [az AzCopy parancssori segédprogrammal történő adatátvitelt](./storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) ismertető szakaszban talál.

### <a name="data-movement-library"></a>Adatátviteli könyvtár

Az Azure Storage .NET-keretrendszerhez készült adatátviteli kódtára az AzCopyt működtető alapvető adatátviteli keretrendszeren alapul. A könyvtár az AzCopyhoz hasonló nagy teljesítményű, megbízható és könnyű adatátviteli műveletekhez készült. Az adatáthelyezési függvénytár segítségével natív módon kihasználhatja a AzCopy funkcióit. További információ: [Azure Storage adatátviteli függvénytár a .net-hez](https://github.com/Azure/azure-storage-net-data-movement)

### <a name="rest-api-or-client-library"></a>REST API vagy ügyfélkódtár

Létrehozhat egy egyéni alkalmazást, amellyel áttelepítheti az adatait egy általános célú v1 Storage-fiókból egy blob Storage-fiókba. Használja az egyik Azure-ügyfél kódtárat vagy az Azure Storage Services REST API. Az Azure Storage gazdag ügyfélkódtárakat biztosít több nyelvhez és platformhoz is, beleértve a következőket: .NET, Java, C++, Node.JS, PHP, Ruby és Python. Az ügyfélkódtárak olyan fejlett képességeket biztosítanak, mint az újrapróbálkozási logika, a naplózás vagy a párhuzamos feltöltések. Fejleszthet közvetlenül a REST API-n is, amely minden, HTTP-/HTTPS-kérelmek létrehozására alkalmas nyelven meghívható.

Az Azure Storage REST APIával kapcsolatos további információkért lásd: az [Azure Storage Services REST API referenciája](/rest/api/storageservices/).

> [!IMPORTANT]
> Az ügyféloldali titkosítással titkosított blobok a titkosítással kapcsolatos metaadatokat a blobbal együtt tárolják. Ha ügyféloldali titkosítással titkosított blobot másol, ügyeljen arra, hogy a másolási művelet megőrizze a blob metaadatait, különös tekintettel a titkosítással kapcsolatos metaadatokra. Ha a blobokat a titkosítási metaadatok nélkül másolja, a blob tartalma nem kérhető le újból. További információt a titkosítással kapcsolatos metaadatokról [az Azure Storage ügyféloldali titkosítását](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) ismertető szakaszban talál.

## <a name="storage-account-billing"></a>Tárfiókok számlázása

Az Azure Storage-számlák a Storage-fiók használata alapján. A tárfiókban lévő összes objektum számlázása együtt, csoportosan történik. A tárolási költségek kiszámítása a következő tényezők szerint történik:

- A **régió** a fiók alapjául szolgáló földrajzi régióra utal.
- A **fióktípus** a használt Storage-fiók típusára utal.
- A **hozzáférési réteg** az általános célú v2-vagy blob Storage-fiókhoz megadott adathasználati mintára vonatkozik.
- A **kapacitás** arra utal, hogy mekkora a Storage-fiók kiosztása, amelyet az adattároláshoz használ.
- A **replikáció** meghatározza, hogy a rendszer egyszerre hány példányt tart fenn az adataiban, és milyen helyeken.
- A **tranzakciók** az Azure Storage-ba irányuló összes olvasási és írási műveletre vonatkoznak.
- A **kimenő adatforgalom** az Azure-régióból kivitt összes adatforgalomra vonatkozik. Ha egy olyan alkalmazás fér hozzá a Storage-fiókban lévő adataihoz, amely nem ugyanabban a régióban fut, akkor a kimenő adatforgalomért kell fizetnie. További információ arról, hogyan lehet erőforráscsoportok használatával csoportosítani az adatokat és szolgáltatásokat ugyanabban a régióban a kimenő forgalomra vonatkozó díjak csökkentése érdekében: [Mi az az Azure-erőforráscsoport?](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group).

Az [Azure Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage/) lap részletes információkat biztosít a fióktípussal, a tárolási kapacitással, a replikálással és a tranzakciókkal kapcsolatban. Az [Adatforgalmi díjszabás](https://azure.microsoft.com/pricing/details/data-transfers/) tartalmazza a kimenő adatforgalommal kapcsolatos részletes díjszabási információkat. Az [Azure Storage-díjkalkulátor](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) használatával megbecsülheti költségeit.

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="next-steps"></a>Következő lépések

- [Tárfiók létrehozása](storage-account-create.md)
- [Blokkblob-tárfiók létrehozása](../blobs/storage-blob-create-account-block-blob.md)
- [Frissítés általános célú v2-tárfiókra](storage-account-upgrade.md)
- [Törölt tárfiók helyreállítása](storage-account-recover.md)