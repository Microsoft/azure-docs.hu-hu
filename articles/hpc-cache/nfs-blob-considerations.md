---
title: Az NFS blob Storage használata az Azure HPC cache használatával
description: A ADLS-NFS blob Storage és az Azure HPC cache használatával kapcsolatos eljárásokat és korlátozásokat ismerteti
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/29/2021
ms.author: v-erkel
ms.openlocfilehash: d861a41d8cdeac548729ff341b3ffe27c0aa8152
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259963"
---
# <a name="use-nfs-mounted-blob-storage-preview-with-azure-hpc-cache"></a>Az NFS-hez csatlakoztatott blob Storage (előzetes verzió) használata az Azure HPC cache használatával

A 2021. márciusi naptól kezdve az NFS-hez csatlakoztatott blob-tárolók az Azure HPC cache használatával használhatók. További információk az [NFS 3,0 protokoll támogatásáról az Azure Blob Storage-ban](../storage/blobs/network-file-system-protocol-support.md) a blob Storage dokumentációs webhelyén.

> [!NOTE]
> Az NFS 3,0 protokoll támogatása az Azure Blob Storage-ban előzetes verzióban érhető el, és nem használható éles környezetben. A frissítések keresése és további részletek az [NFS protokoll támogatási dokumentációjában olvashatók](../storage/blobs/network-file-system-protocol-support.md).

Az Azure HPC cache NFS-kompatibilis blob Storage-t használ a ADLS-NFS tárolási cél típusában. Ezek a tárolási célok hasonlók a normál NFS-tárolási célokhoz, de átfedésben vannak a normál Azure Blob-célokkal is.

Ez a cikk azokat a stratégiákat és korlátozásokat ismerteti, amelyeket a ADLS-NFS tárolási célok használatakor ismernie kell.

Olvassa el az NFS-blob dokumentációját is, különösen azokat a szakaszokat, amelyek a kompatibilis és nem kompatibilis forgatókönyveket írják le:

* [A funkciók áttekintése](../storage/blobs/network-file-system-protocol-support.md#applications-and-workloads-suited-for-this-feature)
* [A funkciók még nem támogatottak](../storage/blobs/network-file-system-protocol-support.md#azure-storage-features-not-yet-supported)
* [A teljesítménnyel kapcsolatos megfontolások](../storage/blobs/network-file-system-protocol-support-performance.md)

## <a name="understand-consistency-requirements"></a>Konzisztencia-követelmények ismertetése

A HPC-gyorsítótár működéséhez erős konzisztencia szükséges a ADLS-NFS tárolási célokhoz. Alapértelmezés szerint az NFS-kompatibilis blob Storage nem frissíti szigorúan a fájl-metaadatokat, ami megakadályozza a HPC-gyorsítótárnak a fájlok verziószámának pontos összehasonlítását.

A különbség megkerüléséhez az Azure HPC cache automatikusan letiltja az NFS-attribútumok gyorsítótárazását a tárolási célként használt bármely NFS-kompatibilis blob-tárolón.

Ez a beállítás továbbra is fennáll a tároló élettartama, még akkor is, ha eltávolítja a gyorsítótárból.

## <a name="preload-data-with-nfs-protocol"></a>Az adattovábbítás az NFS protokollal

Egy NFS-kompatibilis blob-tárolón a *fájl csak a létrehozáskor használt protokollal szerkeszthető*. Ha az Azure REST API használatával tölt fel egy tárolót, az NFS használatával nem frissítheti ezeket a fájlokat. Mivel az Azure HPC cache csak az NFS-t használja, nem szerkesztheti az Azure REST API használatával létrehozott fájlokat.

Nem probléma a gyorsítótárban, ha a tároló üres, vagy ha a fájlokat az NFS használatával hozták létre.

Ha a tárolóban lévő fájlok az NFS helyett az Azure Blob REST API lettek létrehozva, az Azure HPC cache ezen műveletekre korlátozódik az eredeti fájlokon:

* Egy könyvtárban lévő fájl listázása.
* Olvassa el a fájlt (és tartsa azt a gyorsítótárban az ezt követő olvasások esetében).
* Törölje a fájlt.
* Ürítse ki a fájlt (0-ra csonkítva).
* Mentse a fájl másolatát. A másolás NFS által létrehozott fájlként van megjelölve, és az NFS használatával szerkeszthető.

Az Azure HPC-gyorsítótár **nem tudja** SZERKESZTENI a REST használatával létrehozott fájl tartalmát. Ez azt jelenti, hogy nem tudja menteni a módosított fájlt az ügyfélről a tárolási célra.

Fontos megérteni ezt a korlátozást, mert az adatintegritási problémákhoz vezethet, ha az írási/olvasási gyorsítótárazási használati modelleket használja az NFS-sel nem létrehozott fájlokhoz.

> [!TIP]
> További információ az olvasási és írási gyorsítótárazásról: [gyorsítótár-használati modellek](cache-usage-models.md)ismertetése.

### <a name="write-caching-scenarios"></a>Írási gyorsítótárazási forgatókönyvek

Ezek a gyorsítótár-használati modellek tartalmazzák az írási gyorsítótárazást:

* **15%-nál nagyobb írások**
* **15%-nál nagyobb írások, a biztonsági másolat kiszolgálójának ellenőrzése 30 másodpercenként**
* **15%-nál nagyobb írások, a biztonsági mentést kiszolgáló ellenőrzésének ellenőrzése minden 60 másodpercenként**
* **15%-nál nagyobb írások esetén 30 másodpercenként írja vissza a kiszolgálót**

Ezeket a használati modelleket csak az NFS-sel létrehozott fájlok szerkesztésére használja.

Ha a REST-alapú fájlokhoz írási gyorsítótárazást próbál használni, a fájl módosításai elvesznek. Ennek az az oka, hogy a gyorsítótár azonnal nem kísérli meg a tárolóban lévő módosítások mentését a Storage-tárolóba.

Itt láthatja, hogyan lehet az írásokat a REST által létrehozott fájlokra kipróbálni, és az adatok veszélyben vannak:

1. A gyorsítótár fogadja az ügyfelektől érkező módosításokat, és minden módosításkor sikeres üzenetet ad vissza.
1. A gyorsítótár megőrzi a módosított fájlt a tárolóban, és megvárja a további módosításokat.
1. Némi idő elteltével a gyorsítótár megpróbálja menteni a módosított fájlt a háttér-tárolóba. Ezen a ponton hibaüzenet jelenik meg, mert egy REST-alapú, NFS-t tartalmazó fájlba próbál írni.

   Túl késő ahhoz, hogy tájékoztassa az ügyfélszámítógépet arról, hogy a módosítások nem lettek elfogadva, és a gyorsítótárnak nincs módja az eredeti fájl frissítésére. Így az ügyfelek módosításai elvesznek.

### <a name="read-caching-scenarios"></a>Gyorsítótárazási forgatókönyvek olvasása

Az olvasási gyorsítótárazási forgatókönyvek megfelelőek az NFS vagy az Azure Blob REST API által létrehozott fájlokhoz.

Ezek a használati modellek csak olvasási gyorsítótárazást használnak:

* **Súlyos, ritka írások olvasása**
* **Az ügyfelek az NFS-célhelyre írnak, és megkerülik a gyorsítótárat**
* **Nagy mennyiségű, a kiszolgáló biztonsági mentése 3 óránként**

Ezeket a használati modelleket REST API vagy NFS által létrehozott fájlokkal is használhatja. Az ügyfélről a háttér-tárolóba küldött NFS-írások továbbra is sikertelenek lesznek, de azonnal sikertelenek lesznek, és hibaüzenetet adnak vissza az ügyfélnek.

Az olvasási gyorsítótárazási munkafolyamat továbbra is tartalmazhatja a fájl módosításait, feltéve, hogy ezek nincsenek gyorsítótárazva. Előfordulhat például, hogy az ügyfelek a tárolóból férnek hozzá a fájlokhoz, de új fájlként írják vissza a módosításokat, vagy egy másik helyen menthetik a módosított fájlokat.

## <a name="recognize-network-lock-manager-nlm-limitations"></a>Hálózati zárolási kezelő (NLM) korlátainak felismerése

Az NFS-kompatibilis blob-tárolók nem támogatják a hálózati zárolási kezelőt (NLM), amely egy gyakran használt NFS-protokoll a fájlok ütközések elleni védelemmel való ellátásához.

Ha az NFS-munkafolyamatot eredetileg hardveres tárolási rendszerekhez írták, az ügyfélalkalmazások tartalmazhatnak NLM-kérelmeket. Ha a folyamatot az NFS-alapú blob Storage-ba helyezi át, ellenőrizze, hogy az ügyfelek letiltották-e a NLM a gyorsítótár csatlakoztatásakor.

A NLM letiltásához használja az ``-o nolock`` ügyfelek ``mount`` parancsának kapcsolóját. Ez a beállítás megakadályozza, hogy az ügyfelek NLM-zárolásokat kérjenek, és válaszként fogadják a hibákat.

Néhány esetben az Azure HPC cache felismeri a NLM kérelmeket az ügyféltől. A "READ Heavy" nevű gyorsítótár-használati modell **, a ritkán használt írások** visszaigazolják a NLM kérelmeket a háttérbeli tároló nevében. Ez a rendszer megakadályozza az ügyfél hibáit, de valójában nem hoz létre zárolást a ADLS-NFS-tárolón.

Ha egy ADLS-NFS tárolási cél használati modelljét bekapcsolja a vagy az **olvasási nehéz, ritkán** használt írási értékre, akkor az összes ügyfelet újra kell csatlakoztatnia a ``nolock`` kapcsolóval.

A NLM, a HPC cache és a használati modellekről további információt a [gyorsítótár-használati modellek ismertetése](cache-usage-models.md#know-when-to-remount-clients-for-nlm)című témakör tartalmaz.

## <a name="streamline-writes-to-nfs-enabled-containers-with-hpc-cache"></a>Az NFS-kompatibilis tárolók és a HPC-gyorsítótár használatával történő írások egyszerűsítése

Az Azure HPC cache segítségével javítható a teljesítmény olyan munkaterhelésben, amely magában foglalja a ADLS-NFS tárolási cél módosításának megírását.

> [!NOTE]
> Ha az Azure HPC cache-n keresztül szeretné módosítani a fájljait, az NFS használatával töltse fel a ADLS-NFS Storage-tárolót.

Az NFS-t támogató blob [teljesítményével kapcsolatos megfontolásokban](../storage/blobs/network-file-system-protocol-support-performance.md) ismertetett korlátozások egyike az, hogy a ADLS-NFS-tároló nem nagyon hatékony a meglévő fájlok felülírásakor. Ha az Azure HPC cache-t az NFS-hez csatlakoztatott blob Storage-mel használja, a gyorsítótár az időszakos újraírásokat kezeli, mivel az ügyfelek módosítanak egy aktív fájlt. A fájlnak a háttérbeli tárolóba való írásának késése rejtve marad az ügyfelektől.

Ne feledje, hogy a fent ismertetett korlátozások az [NFS protokollal](#preload-data-with-nfs-protocol)történő előtöltéssel kapcsolatosak.

## <a name="next-steps"></a>Következő lépések

* A [ADLS-NFS tárolási cél előfeltételeinek](hpc-cache-prerequisites.md#nfs-mounted-blob-adls-nfs-storage-requirements-preview) megismerése
* [NFS-kompatibilis blob Storage-fiók](../storage/blobs/network-file-system-protocol-support-how-to.md) létrehozása
