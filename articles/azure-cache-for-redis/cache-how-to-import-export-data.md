---
title: Az Azure cache-ben lévő Redis importálása és exportálása
description: Ismerje meg, hogyan importálhat és exportálhat adatok blob Storage-ba és-ból a prémium szintű Azure cache-Redis-példányok esetében
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/31/2017
ms.author: yegu
ms.openlocfilehash: 9ee3b447b2b5f6dfa8972749c3c46ae01f79bfdc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96327508"
---
# <a name="import-and-export-data-in-azure-cache-for-redis"></a>Az Azure cache-ben lévő Redis importálása és exportálása
Az import/export egy Azure cache a Redis adatkezelési művelethez, amely lehetővé teszi az adatok importálását az Azure cache-be a Redis, vagy exportálja az Azure cache-ből a Redis-be adatok exportálását egy Azure cache for Redis Database (RDB) pillanatkép importálásával és exportálásával egy Azure Storage-fiókban található blobba.

- **Exportálás** – exportálhatja az Azure cache-t a Redis RDB-Pillanatképek egy oldal-blobba való exportálásához.
- **Importálás** – importálhatja az Azure cache-t a Redis RDB-pillanatképekhez egy oldal-blobból vagy egy blokk-blobból.

Az Importálás/exportálás lehetővé teszi, hogy áttelepítse a különböző Azure cache-t a Redis-példányok között, vagy a használat előtt feltöltse a gyorsítótárat az adatokkal.

Ez a cikk útmutatást nyújt az Azure cache-sel való adatimportáláshoz és-exportáláshoz az Redis-hez, és megadja a válaszokat a gyakran ismételt kérdésekre.

> [!IMPORTANT]
> Az import/export csak a [prémium szintű](cache-overview.md#service-tiers) csomagok gyorsítótárai esetében érhető el.
>
>

## <a name="import"></a>Importálás
Az importálással bármilyen felhőben vagy környezetben futó Redis-kiszolgálóról Redis kompatibilis RDB-fájlokat lehet használni, beleértve a Linuxon, a Windowson vagy bármely más felhőalapú szolgáltatón, például a Amazon Web Serviceson vagy más felhőben futó Redis. Az adatok importálása egyszerű módszer a gyorsítótár előre feltöltött adatokkal való létrehozására. Az importálási folyamat során az Azure cache for Redis betölti a RDB-fájlokat az Azure Storage-ból a memóriába, majd beszúrja a kulcsokat a gyorsítótárba.

> [!NOTE]
> Az importálási művelet megkezdése előtt győződjön meg arról, hogy a Redis adatbázis (RDB) fájlja vagy fájljai fel vannak töltve az Azure Storage-ba, ugyanabban a régióban és előfizetésben, mint a Redis-példány Azure-gyorsítótára. További információ: Ismerkedés [Az Azure Blob Storage](../storage/blobs/storage-quickstart-blobs-dotnet.md)szolgáltatással. Ha a RDB-fájlt az [Azure cache for Redis export](#export) szolgáltatással exportálta, a RDB-fájl már egy oldal blobban van tárolva, és készen áll az importálásra.
>
>

1. Egy vagy több exportált gyorsítótár-blob importálásához [tallózással keresse meg a gyorsítótárat](cache-configure.md#configure-azure-cache-for-redis-settings) a Azure Portalon, majd kattintson az **adatok importálása** elemre az **erőforrás menüben**.

    ![Adatok importálása](./media/cache-how-to-import-export-data/cache-import-data.png)
2. Kattintson a **blob (ok) kiválasztása** elemre, és válassza ki azt a Storage-fiókot, amely az importálandó adatkészletet tartalmazza.

    ![Storage-fiók kiválasztása](./media/cache-how-to-import-export-data/cache-import-choose-storage-account.png)
3. Kattintson arra a tárolóra, amely az importálandó adatkészletet tartalmazza.

    ![Tároló kiválasztása](./media/cache-how-to-import-export-data/cache-import-choose-container.png)
4. Válasszon ki egy vagy több importálni kívánt blobot a blob neve bal oldalán található területre kattintva, majd kattintson a **kiválasztás** elemre.

    ![Blobok kiválasztása](./media/cache-how-to-import-export-data/cache-import-choose-blobs.png)
5. Az importálási folyamat megkezdéséhez kattintson az **Importálás** elemre.

   > [!IMPORTANT]
   > Az importálási folyamat során a gyorsítótár-ügyfelek nem férhetnek hozzá a gyorsítótárhoz, és a gyorsítótárban lévő összes meglévő adattal törölve lesz.
   >
   >

    ![Importálás](./media/cache-how-to-import-export-data/cache-import-blobs.png)

    Az importálási művelet előrehaladását a Azure Portal értesítéseit követve, vagy [a napló](../azure-resource-manager/management/view-activity-logs.md)eseményeinek megtekintésével figyelheti.

    ![Importálási folyamat](./media/cache-how-to-import-export-data/cache-import-data-import-complete.png)

## <a name="export"></a>Exportálás
Az Exportálás lehetővé teszi az Azure cache-ben tárolt adatexportálást a Redis-Redis kompatibilis RDB-fájl (ok) hoz. Ezzel a szolgáltatással áthelyezheti az adatok egyik Azure-gyorsítótárból a Redis-példányról egy másikra vagy egy másik Redis-kiszolgálóra. Az exportálási folyamat során létrejön egy ideiglenes fájl az Azure cache-t futtató virtuális gépen a Redis Server-példányhoz, és a fájl fel lesz töltve a kijelölt Storage-fiókba. Ha az exportálási művelet sikeres vagy sikertelen állapottal fejeződött be, a rendszer törli az ideiglenes fájlt.

1. A gyorsítótár aktuális tartalmának a tárterületre való exportálásához [tallózással keresse meg a gyorsítótárat](cache-configure.md#configure-azure-cache-for-redis-settings) a Azure Portalban, majd kattintson az **adatok exportálása** elemre az **erőforrás menüben**.

    ![A contoso5premium navigációs ablaktábláján ki van jelölve az adatexportálási beállítás a felügyeleti listán.](./media/cache-how-to-import-export-data/cache-export-data-choose-storage-container.png)
2. Kattintson a **tároló kiválasztása** elemre, és válassza ki a kívánt Storage-fiókot. A Storage-fióknak a gyorsítótárral megegyező előfizetésben és régióban kell lennie.

   > [!IMPORTANT]
   > Az Exportálás az oldal Blobokkal működik, amelyeket a klasszikus és a Resource Manager-beli Storage-fiókok is támogatnak, de jelenleg nem támogatottak a blob Storage-fiókok. További információkat az [Azure Storage-fiókok áttekintésében](../storage/common/storage-account-overview.md) találhat.
   >

    ![Tárfiók](./media/cache-how-to-import-export-data/cache-export-data-choose-account.png)
3. Válassza ki a kívánt BLOB-tárolót, és kattintson a **kiválasztás** gombra. Ha új tárolót szeretne használni, kattintson a **tároló hozzáadása** lehetőségre az első hozzáadásához, majd válassza ki a listából.

    ![A contoso55 tárolóban a + Container beállítás ki van emelve. Egy tároló szerepel a listán, a cachesaves, és ki van választva és ki van emelve. A kiválasztási lehetőség ki van választva és ki van emelve.](./media/cache-how-to-import-export-data/cache-export-data-container.png)
4. Írja be a **blob nevének előtagját** , és kattintson az **Exportálás** elemre az exportálási folyamat elindításához. A blob nevének előtagja az exportálási művelet által generált fájlok nevének előtagja.

    ![Exportálás](./media/cache-how-to-import-export-data/cache-export-data.png)

    Az exportálási művelet előrehaladását a Azure Portal értesítéseinek követésével, vagy [a napló](../azure-resource-manager/management/view-activity-logs.md)eseményeinek megtekintésével figyelheti.

    ![Az adatexportálás befejeződött](./media/cache-how-to-import-export-data/cache-export-data-export-complete.png)

    A gyorsítótárak továbbra is használhatók az exportálási folyamat során.

## <a name="importexport-faq"></a>Importálási/exportálási GYIK
Ez a szakasz az importálási/exportálási funkcióval kapcsolatos gyakori kérdéseket tartalmazza.

* [Milyen díjszabási szintek használhatók Importálás/exportálás esetén?](#what-pricing-tiers-can-use-importexport)
* [Importálhatók bármilyen Redis-kiszolgálóról származó adatok?](#can-i-import-data-from-any-redis-server)
* [Milyen RDB-verziókat lehet importálni?](#what-rdb-versions-can-i-import)
* [Elérhető a gyorsítótár az importálási/exportálási művelet során?](#is-my-cache-available-during-an-importexport-operation)
* [Használhatok importálási/exportálási Redis-fürtöt?](#can-i-use-importexport-with-redis-cluster)
* [Hogyan működik az Importálás/exportálás egyéni adatbázis-beállításokkal?](#how-does-importexport-work-with-a-custom-databases-setting)
* [Miben különbözik az Importálás/exportálás a Redis-megőrzéstől?](#how-is-importexport-different-from-redis-persistence)
* [Automatizálható az Importálás/exportálás a PowerShell, a CLI vagy más felügyeleti ügyfelek használatával?](#can-i-automate-importexport-using-powershell-cli-or-other-management-clients)
* [Időtúllépési hiba érkezett az importálási/exportálási művelet során. Mit jelent?](#i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean)
* [Hibaüzenetet kaptam az adataim Azure Blob Storageba való exportálásakor. mi történt?](#i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened)

### <a name="what-pricing-tiers-can-use-importexport"></a>Milyen díjszabási szintek használhatók Importálás/exportálás esetén?
Az Importálás/Exportálás csak a prémium szintű díjszabási szinten érhető el.

### <a name="can-i-import-data-from-any-redis-server"></a>Importálhatók bármilyen Redis-kiszolgálóról származó adatok?
Igen, az Azure cache-ről az Redis-példányok számára exportált adatok importálása mellett bármilyen felhőben vagy környezetben futó Redis-kiszolgálóról, például a Linux-, Windows-és RDB, például a Amazon Web Services is importálhat fájlokat. Ehhez töltse fel a RDB-fájlt a kívánt Redis-kiszolgálóról egy oldalra, vagy tiltsa le a blobot egy Azure Storage-fiókban, majd importálja a prémium szintű Azure cache-be a Redis-példányhoz. Előfordulhat például, hogy exportálni kívánja az adatait az éles gyorsítótárból, és a teszteléshez vagy áttelepítéshez egy átmeneti környezet részeként használt gyorsítótárba importálja.

> [!IMPORTANT]
> Az Azure cache-ről az Redis-től eltérő Redis-kiszolgálókról exportált adatok sikeres importálásához az oldal blobjának használatakor az oldal blobjának méretét 512 bájtos határra kell igazítani. A szükséges bájtok kitöltéséhez használandó mintakód a [minta oldal blob feltöltése](https://github.com/JimRoberts-MS/SamplePageBlobUpload)című részben olvasható.
>
>

### <a name="what-rdb-versions-can-i-import"></a>Milyen RDB-verziókat lehet importálni?

Az Azure cache for Redis támogatja a RDB importálását a RDB 7-es verziójával.

### <a name="is-my-cache-available-during-an-importexport-operation"></a>Elérhető a gyorsítótár az importálási/exportálási művelet során?
* Az **export** -cache-gyorsítótárak továbbra is elérhetők maradnak, és az exportálási művelet során továbbra is használhatja a gyorsítótárat.
* Az **importálási** gyorsítótárak elérhetetlenné válnak az importálási művelet indításakor, és az importálási művelet befejezésekor elérhetővé válnak.

### <a name="can-i-use-importexport-with-redis-cluster"></a>Használhatok importálási/exportálási Redis-fürtöt?
Igen, és a fürtözött gyorsítótár és a nem fürtözött gyorsítótár közötti importálás/exportálás is elvégezhető. Mivel a Redis-fürt [csak a 0 adatbázis használatát támogatja](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering), a 0-tól eltérő adatbázisokban lévő összes adattal nem kerül importálásra. A fürtözött gyorsítótár-adatmennyiség importálásakor a kulcsok a fürt szegmensei között lesznek elosztva.

### <a name="how-does-importexport-work-with-a-custom-databases-setting"></a>Hogyan működik az Importálás/exportálás egyéni adatbázis-beállításokkal?
Bizonyos díjszabási [szintek eltérőek](cache-configure.md#databases), ezért a rendszer az importálás során némi megfontolást végez, ha egyéni értéket konfigurált a `databases` beállításhoz a gyorsítótár létrehozásakor.

* Ha olyan díjszabási csomagba importál, `databases` amely alacsonyabb korláttal rendelkezik, mint az exportált csomagra vonatkozó szintet:
  * Ha az alapértelmezett értéket használja `databases` , amely az összes díjszabási csomag esetében 16, az adatvesztés nem történik meg.
  * Ha olyan egyéni számot használ, amely az `databases` importálandó csomag korlátain belül esik, akkor a rendszer nem vesz fel adatvesztést.
  * Ha az exportált adatok olyan adatbázisban találhatók, amely túllépi az új csomag korlátait, a magasabb adatbázisokból származó adatok nem lesznek importálva.

### <a name="how-is-importexport-different-from-redis-persistence"></a>Miben különbözik az Importálás/exportálás a Redis-megőrzéstől?
Az Azure cache for Redis perzisztencia szolgáltatás lehetővé teszi a Redis-ben tárolt adatmegőrzést az Azure Storage-ban. Ha az adatmegőrzés konfigurálva van, az Azure cache for Redis megőrzi az Azure cache pillanatképét a Redis egy Redis bináris formátumban a lemezre a konfigurálható biztonsági mentés gyakorisága alapján. Ha olyan katasztrofális esemény következik be, amely letiltja az elsődleges és a replika gyorsítótárat is, a rendszer automatikusan visszaállítja a gyorsítótár-adatok visszaállítását a legfrissebb pillanatkép használatával. További információkért lásd: az [adatmegőrzés konfigurálása prémium szintű Azure cache-hez a Redis](cache-how-to-premium-persistence.md)-hez.

Az importálási/exportálási funkció lehetővé teszi, hogy a Redis az Azure cache-be vagy-ba exportálja az adatait. A Redis-megőrzéssel nem konfigurálja a biztonsági mentést és a visszaállítást.

### <a name="can-i-automate-importexport-using-powershell-cli-or-other-management-clients"></a>Automatizálható az Importálás/exportálás a PowerShell, a CLI vagy más felügyeleti ügyfelek használatával?
Igen, a PowerShell-utasításokért lásd: [Azure cache importálása a Redis](cache-how-to-manage-redis-cache-powershell.md#to-import-an-azure-cache-for-redis) és [a Redis készült Azure cache exportálása](cache-how-to-manage-redis-cache-powershell.md#to-export-an-azure-cache-for-redis).

### <a name="i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean"></a>Időtúllépési hiba érkezett az importálási/exportálási művelet során. Mit jelent?
Ha a művelet megkezdése előtt továbbra is 15 percnél hosszabb ideig marad az **importálási** vagy **exportálási** panel, hibaüzenet jelenik meg az alábbi példához hasonló hibaüzenettel:

```output
The request to import data into cache 'contoso55' failed with status 'error' and error 'One of the SAS URIs provided could not be used for the following reason: The SAS token end time (se) must be at least 1 hour from now and the start time (st), if given, must be at least 15 minutes in the past.
```

Ennek megoldásához indítsa el az importálási vagy exportálási műveletet 15 perc elteltével.

### <a name="i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened"></a>Hibaüzenetet kaptam az adataim Azure Blob Storageba való exportálásakor. Mi történt?
Az Exportálás csak a RDB tárolt fájlok esetében működik. Más blob-típusok jelenleg nem támogatottak, beleértve a gyors és a lassú elérésű csomagokat tartalmazó blob Storage-fiókokat is. További információkat az [Azure Storage-fiókok áttekintésében](../storage/common/storage-account-overview.md) találhat.

## <a name="next-steps"></a>Következő lépések
További információ az Azure cache Redis szolgáltatásairól.

* [Azure cache a Redis szolgáltatási szintjeihez](cache-overview.md#service-tiers)