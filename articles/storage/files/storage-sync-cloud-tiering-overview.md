---
title: Az Azure File Sync Cloud-rétegek megismerése | Microsoft Docs
description: A felhőalapú rétegek, a választható Azure File Sync funkció megismerése. A gyakran használt fájlokat a rendszer helyileg gyorsítótárazza a kiszolgálón; mások a Azure Filesra vannak bontva.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 1/4/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: dcd58e966da7ca596a14ca1b2839cbeb6399a855
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104576458"
---
# <a name="cloud-tiering-overview"></a>A felhőalapú rétegek áttekintése
A felhő-rétegek Azure File Sync választható funkciója csökkenti a szükséges helyi tárterület mennyiségét a helyszíni fájlkiszolgáló teljesítményének megőrzése mellett.

Ha engedélyezve van, ez a szolgáltatás csak a gyakran használt (forró) fájlokat tárolja a helyi kiszolgálón. A ritkán használt (cool) fájlok a névtér (fájl-és mappa-struktúra) és a fájl tartalmára vannak bontva. A névteret helyileg tárolják, és a fájl tartalmát egy Azure-fájlmegosztás tárolja a felhőben. 

Amikor egy felhasználó megnyit egy rétegű fájlt, Azure File Sync zökkenőmentesen visszahívja a fájlmegosztást az Azure-ban.

## <a name="how-cloud-tiering-works"></a>A felhő-rétegek működése

### <a name="cloud-tiering-policies"></a>Felhő-rétegű házirendek
A felhő-rétegek engedélyezésekor két házirendet állíthat be, amelyekkel a rendszer tájékoztatja Azure File Sync a lassú elérésű fájlok: a **kötet szabad területére vonatkozó házirend** és a **dátum házirendje**. 

#### <a name="volume-free-space-policy"></a>Kötet szabad területének szabályzata
A **kötet szabad területének házirendje** azt jelzi, hogy a rendszer a helyi lemezen bizonyos mennyiségű helyet foglal magába, hogy a rétegbeli lassú fájlok a felhőbe Azure file Sync. 

Ha például a helyi lemez kapacitása 200 GB, és azt szeretné, hogy a helyi lemez kapacitása legalább 40 GB legyen, mindig szabad maradjon, állítsa a kötet szabad területére vonatkozó házirendet 20%-ra. A kötet szabad területe a kötet szintjén érvényes, nem pedig az egyes címtárak vagy kiszolgálói végpontok szintjére. 

Annak megismeréséhez, hogy a kötet szabad területének házirendje milyen hatással van az új kiszolgálói végpont hozzáadásakor letöltött fájlokra, (lásd a felhő-előírásokat [érintő szinkronizálási szabályzatokat](#sync-policies-that-affect-cloud-tiering)) című szakaszt.

#### <a name="date-policy"></a>Dátum házirend
A **Date Policy** használatával a lassú fájlok a felhőbe vannak bontva, ha nem férnek hozzá (azaz olvasás vagy írás) x számú napig. Ha például észrevette, hogy a 15 napnál régebben elküldött fájlok általában archiválási fájlok, akkor a házirendet 15 napig kell beállítania. 

További példák a dátum-és adatmennyiség-házirend együttes együttműködésének módjáról: [Azure file Sync felhőalapú rétegek kiválasztására vonatkozó szabályzatok kiválasztása](storage-sync-choose-cloud-tiering-policies.md).

### <a name="windows-server-data-deduplication"></a>A Windows Server-adatmásolási szolgáltatás
Az deduplikálás olyan köteteken támogatott, amelyeken engedélyezve van a felhőalapú rétegek használata a Windows Server 2016-es verziójától kezdve. További részletekért tekintse [meg a Azure file Sync központi telepítésének megtervezése](./storage-sync-files-planning.md#data-deduplication)című témakört.

### <a name="cloud-tiering-heatmap"></a>Felhőalapú rétegek hő
Azure File Sync figyeli a fájlokhoz való hozzáférést (olvasási és írási műveleteket) az idő múlásával, és a gyakori és a legutóbbi hozzáféréstől függően minden fájlhoz hozzárendel egy hő pontszámot. Ezeket a pontszámokat használja a névtér "hő" létrehozásához az egyes kiszolgálói végpontokon. Ez a hő egy olyan helyen található összes szinkronizált fájl listája, amelyen engedélyezve van a Felhőbeli rétegek használata, és amelyeket a hő pontszáma alapján rendeznek. A legutóbb megnyitott fájlok gyakran melegnek számítanak, míg a ritkán megérintett fájlok és a nem férnek hozzá egy ideig. 

Az adott hő lévő egyes fájlok relatív pozíciójának meghatározásához a rendszer a maximális időbélyegét használja a következő sorrendben: MAX (utolsó hozzáférés időpontja, utolsó módosítás időpontja, létrehozás időpontja). 

Általában a legutóbbi hozzáférési idő nyomon követhető és elérhető. Ha azonban új kiszolgálói végpont jön létre, a felhőalapú rétegek engedélyezve vannak, a fájlok hozzáférésének megfigyelése nem elegendő. Ha nincs érvényes utolsó hozzáférési idő, a rendszer az utolsó módosítás időpontját használja helyette, hogy kiértékelje a relatív pozíciót a hő.  

A dátumra vonatkozó házirend ugyanúgy működik. A legutóbbi hozzáférési idő nélkül a dátum-házirend az utolsó módosítás időpontjában fog működni. Ha ez nem érhető el, az egy fájl létrehozási idejére kerül vissza. Az idő múlásával a rendszer több és több fájl-hozzáférési kérelmet is betart, és automatikusan elkezdi használni a saját nyomon követett utolsó hozzáférési időt.

> [!Note]
> A felhő-rétegek nem függnek az NTFS-szolgáltatástól a legutóbbi hozzáférési idő nyomon követéséhez. Ez az NTFS-szolgáltatás alapértelmezés szerint ki van kapcsolva, és a teljesítménnyel kapcsolatos megfontolások miatt nem ajánlott manuálisan engedélyezni ezt a szolgáltatást. A felhő-rétegek a legutóbbi hozzáférési időt külön-külön figyelik.

### <a name="sync-policies-that-affect-cloud-tiering"></a>A felhőalapú rétegek befolyásolását befolyásoló szinkronizálási házirendek

A Azure File Sync Agent 11-es verziójában két további Azure File Sync szabályzatot is beállíthat, amelyek hatással vannak a Felhőbeli rétegek használatára: **kezdeti Letöltés** és **proaktív visszahívás**.

#### <a name="initial-download"></a>Kezdeti Letöltés

Ha egy kiszolgáló egy Azure-fájlmegosztás számára csatlakozik a fájlokhoz, most eldöntheti, hogyan szeretné a kiszolgáló először letölteni a fájlmegosztás-adatmegosztást. Ha engedélyezve van a felhőalapú réteg, két lehetőség közül választhat. 

![Képernyőkép a kezdeti letöltésről a felhőalapú rétegek engedélyezésekor](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-3.png)  

Az első lehetőség a névtér első felidézése, majd a fájl tartalmának visszahívása az utolsó módosítás időbélyegével, amíg a helyi lemez kapacitása el nem éri. Ha elegendő lemezterülettel rendelkezik, és tudja, hogy a legutóbb módosított fájlokat helyileg kell gyorsítótárazni, ez a lehetőség ideális. A helyileg nem tárolható fájlok a lemezterület hiánya miatt nem lesznek feldolgozva.        

A második lehetőség az, hogy először csak a névteret hívja fel, és csak akkor hívja fel a tartalmat, ha a fájl elérhető. Ez a lehetőség akkor a legjobb, ha csökkenteni szeretné a helyi lemezen használt kapacitást, és szeretné, hogy a felhasználók eldöntsék, mely fájlokat kell helyileg gyorsítótárazni. Ezzel a beállítással a rendszer az összes fájlt a lépcsőzetes fájlként fogja elindítani.

![Képernyőkép a kezdeti letöltésről a Felhőbeli rétegek letiltásakor](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-4.png)

Ha a felhőalapú rétegek letiltására van szükség, egy harmadik lehetőség is van, amely a rétegekből álló fájlok elkerülését teszi elérhetővé. Ebben az esetben a fájlok csak akkor jelennek meg a kiszolgálón, ha teljesen letöltődnek. Ha olyan alkalmazásokkal rendelkezik, amelyeknek teljes fájlra van szükségük, és a névterek nem tudják elviselni a többplatformos fájlokat, ez ideális.      

#### <a name="proactive-recalling"></a>Proaktív visszahívás

Fájl létrehozásakor vagy módosításakor proaktív módon felhívhat egy fájlt a megadott kiszolgálókra. Így az új vagy módosított fájl azonnal elérhetővé válik az egyes megadott kiszolgálókon. 

Egy globálisan elosztott vállalat például fiókirodával rendelkezik az Egyesült Államokban és Indiában. A délelőtti (amerikai idő) információkkal dolgozó szakemberek új mappát és új fájlokat hoznak létre egy új projekthez, és egész nap dolgozhatnak hozzájuk. Azure File Sync fogja szinkronizálni a mappát és a fájlokat az Azure-fájlmegosztás (Felhőbeli végpont) számára. Az indiai információs szakemberek továbbra is a projekten dolgoznak a saját időzónájában. Amikor reggel érkeznek, az indiai helyi Azure File Sync-kiszolgáló számára elérhetővé kell tennie ezeket az új fájlokat helyileg, így az indiai csapat hatékonyan dolgozhat ki helyi gyorsítótárból. Ha engedélyezi ezt a módot, azzal megakadályozza, hogy az igény szerinti visszahívás miatt a kezdeti fájlhoz való hozzáférés lassabb legyen, és lehetővé teszi a kiszolgáló számára, hogy az Azure-fájlmegosztás módosításakor vagy létrehozásakor proaktív módon felidézze a fájlokat.

Ha a fájlok visszahívása a kiszolgálóra nincs ténylegesen szükség helyileg, akkor a szükségtelen visszahívás növelheti a kimenő forgalom és a költségek mennyiségét. Ezért csak akkor engedélyezze az előjelzéses visszahívást, ha tudja, hogy a kiszolgáló gyorsítótárának a Felhőbeli legutóbbi változásaival való előzetes kitöltése pozitív hatással lesz az adott kiszolgálón található fájlokat használó felhasználókra vagy alkalmazásokra. 

Előfordulhat, hogy az előjelzéses visszahívások engedélyezése nagyobb sávszélesség-használatot eredményezhet a kiszolgálón, és más, viszonylag új tartalmat is okozhat a helyi kiszolgálón, hogy a visszahívásra kerülő fájlok növekedése miatt a rendszer agresszíven megtörténjen. Ez azt eredményezheti, hogy a rendszer több visszahívást eredményezhet, ha a többrétegű fájlokat a kiszolgálók használják. 

:::image type="content" source="media/storage-sync-files-deployment-guide/proactive-download.png" alt-text="Egy olyan kép, amely az Azure fájlmegosztás letöltési viselkedését mutatja be egy jelenleg érvényben lévő kiszolgálói végponton, és egy gombot egy olyan menü megnyitásához, amely lehetővé teszi a módosítását.":::

További információ az előjelzéses újrahívásról: [Azure file Sync üzembe helyezése](storage-sync-files-deployment-guide.md#proactively-recall-new-and-changed-files-from-an-azure-file-share).

## <a name="tiered-vs-locally-cached-file-behavior"></a>Lépcsőzetes és helyileg gyorsítótárazott fájl viselkedése

A felhő-rétegek a névtér (a fájl-és a mappa-hierarchia, valamint a fájl tulajdonságai) és a fájl tartalmának elkülönítése. 

#### <a name="tiered-file"></a>Többplatformos fájl

A többplatformos fájlok esetében a lemez mérete nulla, mivel a fájl tartalma nem helyileg tárolódik. Ha egy fájl többszintű, a Azure File Sync fájlrendszer-szűrő (StorageSync.sys) a fájlt helyileg váltja fel egy mutatóval (újraelemzési pont). Az újraelemzési pont az Azure-fájlmegosztás fájljának URL-címét jeleníti meg. A többrétegű fájlok "offline" attribútummal és az NTFS fájlrendszerrel beállított FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS attribútummal is rendelkeznek, így a harmadik féltől származó alkalmazások biztonságosan azonosíthatják a többrétegű fájlokat.   

![Képernyőfelvétel a fájl tulajdonságairól, ha az csak rétegbeli névtér.](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-2.png)    

#### <a name="locally-cached-file"></a>Helyileg gyorsítótárazott fájl

Másfelől a helyszíni fájlkiszolgálón tárolt fájlok esetében a lemezen lévő méret a fájl logikai méretével egyenlő lesz, mivel a teljes fájl (a fájl attribútumai és a fájl tartalma) helyileg tárolódik.     

![Képernyőfelvétel a fájl tulajdonságairól, ha nem rétegbeli – a névtér és a fájl tartalma.](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-1.png) 

A fájlok részlegesen is elhelyezhetők (vagy részben visszahívható). Egy részben rétegű fájlban a fájl egy része a lemezen található. Ez akkor fordulhat elő, ha a fájlok részben olvashatók olyan alkalmazások, mint például a multimédiás lejátszók vagy a zip-segédprogramok, amelyek támogatják a fájlokhoz való hozzáférést Azure File Sync intelligens, és csak a csatlakoztatott Azure-fájlmegosztás kért információit hívja meg.

> [!NOTE]
> A méret a fájl logikai méretét jelöli. A lemez mérete a lemezen tárolt fájl adatfolyamának fizikai méretét jelöli.

## <a name="next-steps"></a>Következő lépések
* [Válassza ki Azure File Sync a felhőre vonatkozó szintű szabályzatokat](storage-sync-choose-cloud-tiering-policies.md)
* [Az Azure File Sync üzembe helyezésének megtervezése](storage-sync-files-planning.md)