---
title: Tárfiókok áttekintése
titleSuffix: Azure Storage
description: Ismerje meg az Azure Storage-ban a különböző tárfióktípusokat. Tekintse át a fiókok elnevezését, a teljesítményszinteket, a hozzáférési szinteket, a redundanciát, a titkosítást, a végpontokat stb.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/19/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: d4874ad6688fa85f0c511632498938817bb218f7
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714199"
---
# <a name="storage-account-overview"></a>Tárfiókok áttekintése

Az Azure Storage-fiók az összes Azure Storage-adatobjektumot tartalmazza: blobokat, fájlokat, üzenetsorokat, táblákat és lemezeket. A tárfiók egyedi névteret biztosít az Azure Storage-adatok számára, amely a világon bárhonnan elérhető HTTP- vagy HTTPS-kapcsolatokon keresztül. Az Azure Storage-fiókban található adatok tartósak és magas rendelkezésre állnak, biztonságosak és nagymértékben skálázhatók.

Az Azure-tárfiókok létrehozásáról az [Azure-tárfiók létrehozása](storage-account-create.md) című oldalon tájékozódhat.

## <a name="types-of-storage-accounts"></a>A tárfiókok típusai

Az Azure Storage számos tárfióktípust kínál. Mindegyik típus különböző funkciókat támogat, és saját díjszabási modellel rendelkezik. Mielőtt létrehoz egy tárfiókot, vegye figyelembe ezeket a különbségeket az alkalmazásai számára legmegfelelőbb fióktípus meghatározásához.

Az alábbi táblázat a Microsoft által a legtöbb forgatókönyvhöz ajánlott tárfióktípusokat ismerteti:

| Tárfiók típusa | Támogatott szolgáltatások | Redundanciabeállítások | Üzembehelyezési modell | Használat |
|--|--|--|--|--|
| Standard általános célú v2 | Blob, File, Queue, Table és Data Lake Storage<sup>1</sup> | LRS/GRS/RA-GRS<br /><br />ZRS/GZRS/RA-GZRS<sup>2</sup> | Resource Manager<sup>3.</sup> | Alapszintű tárfióktípus blobokhoz, fájlokhoz, üzenetsorokhoz és táblákhoz. A legtöbb Azure Storage-forgatókönyvhöz ajánlott. |
| Prémium szintű blokkblobok<sup>4</sup> | Csak blokkblobok | LRS<br /><br />ZRS<sup>2</sup> | Resource Manager<sup>3.</sup> | A blokkblobok és hozzáfűző blobok prémium teljesítményjellemzőivel rendelkező tárfiókok. Nagy tranzakciós arányú forgatókönyvekhez, illetve kisebb objektumokat vagy következetesen alacsony tárolási késést igénylő forgatókönyvekhez ajánlott.<br />[tudj meg többet...](../blobs/storage-blob-performance-tiers.md) |
| Prémium fájlmegosztások<sup>4</sup> | Csak fájlmegosztások | LRS<br /><br />ZRS<sup>2</sup> | Resource Manager<sup>3.</sup> | Csak fájlokra vonatkozó tárfiókok prémium teljesítményjellemzővel. Nagyvállalati vagy nagy teljesítményű alkalmazásokhoz ajánlott.<br />[tudj meg többet...](../files/storage-files-planning.md#management-concepts) |
| Prémium lapblobok<sup>4</sup> | Csak lapblobok | LRS | Resource Manager<sup>3.</sup> | Prémium szintű tárfiók típusa csak lapblobok esetén.<br />[tudj meg többet...](../blobs/storage-blob-pageblob-overview.md) |

<sup>1</sup> Azure Data Lake Storage Azure Blob Storage-on big data dedikált képességek készlete. Data Lake Storage V2-tárfiókok csak olyan általános célú tárfiókok esetében támogatottak, amelyeken engedélyezve van egy hierarchikus névtér. További információ a [Data Lake Storage Gen2: Bevezetés a Data Lake Storage Gen2.](../blobs/data-lake-storage-introduction.md)

<sup>2</sup> A zónaredundáns tárolás (ZRS) és a geo-zónaredundáns tárolás (GZRS/RA-GZRS) csak bizonyos régiókban standard általános célú v2-, prémium szintű blokkblob- és prémium fájlmegosztási fiókokhoz érhető el. Az Azure Storage redundanciával kapcsolatos további információkért lásd: [Azure Storage redundancia.](storage-redundancy.md)

<sup>3</sup> Azure Resource Manager az Azure-erőforrások, köztük a tárfiókok ajánlott üzembe helyezési modellje. További információkért lásd a Resource Manager [áttekintését.](../../azure-resource-manager/management/overview.md)

<sup>4</sup> A prémium teljesítményszinten lévő tárfiókok SSD-ket (SSD-ket) használnak az alacsony késés és a nagy átviteli sebesség érdekében.

Az örökölt tárfiókok is támogatottak. További információ: [Örökölt tárfióktípusok.](#legacy-storage-account-types)

## <a name="storage-account-endpoints"></a>Tárfiókvégpontok

A tárfiók egyedi névteret biztosít az Adatok számára az Azure-ban. Az Azure Storage-ban tárolt összes objektum rendelkezik egy címmel, amely tartalmazza az Ön egyedi fióknevét. A tárfiók végpontjai a fióknév és az Azure Storage szolgáltatásvégpont kombinációja.

Ne feledje ezeket a szabályokat a tárfiók elnevezésekor:

- A tárfiókok neve 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat.
- A tárfiók nevének egyedinek kell lennie az Azure rendszerben. Két tárfióknak nem lehet azonos neve.

Az alábbi táblázat az egyes Azure Storage-szolgáltatások végpontjainak formátumát sorolja fel.

| Storage szolgáltatás | Végpont |
|--|--|
| Blob Storage | `https://<storage-account>.blob.core.windows.net` |
| Data Lake Storage Gen2 | `https://<storage-account>.dfs.core.windows.net` |
| Azure Files | `https://<storage-account>.file.core.windows.net` |
| Queue Storage | `https://<storage-account>.queue.core.windows.net` |
| Table Storage | `https://<storage-account>.table.core.windows.net` |

Az URL-cím tárfiókban található objektum eléréséhez fűzheti hozzá az objektum helyét a tárfiókban a végponthoz. Egy blob URL-címe például a következő lesz:

`http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*`

A tárfiókot konfigurálhatja úgy is, hogy egyéni tartományt használjon a blobok számára. További információ: [Egyéni tartománynév konfigurálása az Azure Storage-fiókhoz.](../blobs/storage-custom-domain-name.md)  

## <a name="migrating-a-storage-account"></a>Tárfiók áttelepítése

Az alábbi táblázat összefoglalja a tárfiókok áthelyezésével, frissítésével vagy áttelepítésével kapcsolatos útmutatást, és mutat rá:

| Migrálási forgatókönyv | Részletek |
|--|--|
| Tárfiók áthelyezése másik előfizetésbe | Azure Resource Manager lehetőséget kínál az erőforrások másik előfizetésbe való áthelyezéséhez. További információ: [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe.](../../azure-resource-manager/management/move-resource-group-and-subscription.md) |
| Tárfiók áthelyezése másik erőforráscsoportba | Azure Resource Manager az erőforrások másik erőforráscsoportba való áthelyezésének lehetőségeit. További információ: [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe.](../../azure-resource-manager/management/move-resource-group-and-subscription.md) |
| Tárfiók áthelyezése másik régióba | A tárfiókok áthelyezéséhez létre kell hozni egy másolatot a tárfiókról egy másik régióban. Ezután helyezze át az adatokat a fiókba az AzCopy vagy egy másik választott eszköz használatával. További információ: [Azure Storage-fiók áthelyezése egy másik régióba.](storage-account-move.md) |
| Frissítés általános célú v2-tárfiókra | Általános célú v1-tárfiókot vagy Blob Storage-fiókot általános célú v2-fiókra frissíthet. Vegye figyelembe, hogy ez a művelet nem vonható vissza. További információ: [Frissítés általános célú v2 tárfiókra.](storage-account-upgrade.md) |
| Klasszikus tárfiók áttelepítése a Azure Resource Manager | A Azure Resource Manager modell jobb a klasszikus üzembe helyezési modellnél a funkciók, a méretezhetőség és a biztonság szempontjából. A klasszikus tárfiókok áttelepítésével kapcsolatos további Azure Resource Manager: [Tárfiókok](../../virtual-machines/migration-classic-resource-manager-overview.md#migration-of-storage-accounts) áttelepítése az **IaaS-erőforrások** klasszikusról Azure Resource Manager. |

## <a name="transferring-data-into-a-storage-account"></a>Adatok átvitele tárfiókba

A Microsoft szolgáltatásokat és segédprogramokat biztosít az adatok helyszíni tárolóeszközökről vagy külső felhőtárszolgáltatókról történő importálásához. A használt megoldás az átvitel során használt adatok mennyiségétől függ. További információ: [Az Azure Storage migrálásának áttekintése.](storage-migration-overview.md)

## <a name="storage-account-encryption"></a>Tárfiók-titkosítás

A tárfiókban található összes adat automatikusan titkosítva lesz a szolgáltatás oldalán. További információ a titkosításról és a kulcskezelésről: [Azure Storage encryption for data at rest (Az Azure Storage titkosítása az adattároláshoz).](storage-service-encryption.md)

## <a name="storage-account-billing"></a>Tárfiókok számlázása

Az Azure Storage a tárfiók használatán alapul. A tárfiókban lévő összes objektum számlázása együtt, csoportosan történik. A tárolási költségeket a következő tényezők alapján számítjuk ki:

- **A régió** azt a földrajzi régiót jelenti, amelyben a fiókja alapul.
- **A fiók** típusa a használt tárfiók típusát jelenti.
- **A hozzáférési szint** az általános célú v2- vagy Blob Storage-fiókhoz megadott adathasználati mintára utal.
- **A** kapacitás azt jelenti, hogy a tárfiók mekkora felhasználásával tárol adatokat.
- **A redundancia** határozza meg, hogy egyszerre hány példányban és milyen helyeken vannak megőrizve az adatok.
- **A** tranzakciók az összes olvasási és írási műveletet az Azure Storage-ba vonatkoznak.
- **A kivitt adat** az Azure-régióból átvitt adatokra vonatkozik. Ha a tárfiókban található adatokat egy olyan alkalmazás használja, amely nem ugyanabban a régióban fut, a ki- és behozott adatokért díjat számítunk fel. További információ az adatok és szolgáltatások erőforráscsoportok használatával való csoportosításához ugyanabban a régióban a bejövő forgalom díjának korlátozása érdekében: Mi [az az Azure-erőforráscsoport?](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group).

Az [Azure Storage díjszabási oldala](https://azure.microsoft.com/pricing/details/storage/) részletes díjszabási információkat tartalmaz a fiók típusa, a tárkapacitás, a replikáció és a tranzakciók alapján. Az [Adatátvitelek díjszabása részletes](https://azure.microsoft.com/pricing/details/data-transfers/) díjszabási információkat tartalmaz a ki- és behozott adatokról. Az Azure [Storage díjkalkulátorának használatával](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) megbecsülheti költségeit.

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="legacy-storage-account-types"></a>Örökölt tárfióktípusok

Az alábbi táblázat az örökölt tárfióktípusokat ismerteti. Ezeket a fióktípusokat a Microsoft nem javasolta, de bizonyos esetekben használhatók:

| Az örökölt tárfiók típusa | Támogatott szolgáltatások | Redundanciabeállítások | Üzembehelyezési modell | Használat |
|--|--|--|--|--|
| Standard általános célú v1 | Blob, File, Queue, Table és Data Lake Storage | LRS/GRS/RA-GRS | Resource Manager, klasszikus | Előfordulhat, hogy az általános célú v1-fiókok nem a legújabb funkciókkal vagy a legalacsonyabb gigabájtonkénti díjszabást kínálják. Az alábbi esetekben érdemes megfontolni a használatot:<br /><ul><li>Az alkalmazásokhoz szükség van a klasszikus Azure-beli üzembe helyezési modellre.</li><li>Az alkalmazások tranzakcióigényesak, vagy jelentős georeplikációs sávszélességet igényelnek, de nem igényelnek nagy kapacitást. Ebben az esetben az általános célú v1 a leggazdaságosabb választás.</li><li>Az Azure Storage REST API 2014. 02. 14-esnél korábbi verzióját vagy 4.x-esnél korábbi verziójú ügyféloldali kódtárat használ, és nem tudja frissíteni az alkalmazást.</li></ul> |
| Standard Blob Storage | Blob (csak blokkblobok és hozzáfűző blobok) | LRS/GRS/RA-GRS | Resource Manager | A Microsoft a standard általános célú v2-fiókok használatát javasolja, amikor csak lehetséges. |

## <a name="next-steps"></a>Következő lépések

- [Tárfiók létrehozása](storage-account-create.md)
- [Frissítés általános célú v2-tárfiókra](storage-account-upgrade.md)
- [Törölt tárfiók helyreállítása](storage-account-recover.md)