---
title: A Azure File Sync rétegezésének | Microsoft Docs
description: A felhőbeli rétegezés, amely egy opcionális Azure File Sync funkció. A gyakran használt fájlok helyileg vannak gyorsítótárazva a kiszolgálón; mások rétegzetten vannak Azure Files.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 1f58aa4e2aa4637dfb9fc8b29c52209975e3e367
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797051"
---
# <a name="cloud-tiering-overview"></a>Felhőbeli rétegezés áttekintése
A felhőbeli rétegezés, amely a Azure File Sync opcionális funkciója, csökkenti a szükséges helyi tárterület mennyiségét, miközben a helyszíni fájlkiszolgáló teljesítményét is megtartja.

Ha engedélyezve van, ez a szolgáltatás csak a gyakran használt (gyakori adatokat tároló) fájlokat tárolja a helyi kiszolgálón. A ritkán használt (ritkán használt) fájlok névtérre (fájl- és mappastruktúra) és fájltartalomra vannak felosztva. A névtér helyileg, a fájl tartalma pedig egy Azure-fájlmegosztásban van tárolva a felhőben. 

Amikor egy felhasználó megnyit egy rétegzett fájlt, a Azure File Sync zökkenőmentesen visszahívja a fájladatokat az Azure-beli fájlmegosztásból.

## <a name="how-cloud-tiering-works"></a>A felhőbeli rétegezés működése

### <a name="cloud-tiering-policies"></a>Felhőbeli rétegezés szabályzata
Ha engedélyezi a felhőbeli rétegezést, két szabályzatot állíthat be, amelyek tájékoztatják  Azure File Sync a fájlok rétegezésének időpontját: a kötet szabad területére vonatkozó szabályzatot és a dátum **szabályzatot.** 

#### <a name="volume-free-space-policy"></a>Kötet szabad területének szabályzata
A **kötet szabad területére** vonatkozó szabályzat Azure File Sync, hogy rétegzhű fájlokat a felhőbe, ha a helyi lemezen egy bizonyos mennyiségű hely van eltűrve. 

Ha például a helyi lemezkapacitás 200 GB, és azt szeretné, hogy a helyi lemezkapacitás legalább 40 GB mindig szabad maradjon, a kötet szabad területére vonatkozó házirendet 20%-ra kell állítani. A kötet szabad tárhelye nem az egyes könyvtárak vagy kiszolgálóvégpontok szintjén, hanem kötetszinten érvényes. 

Ha meg szeretne ismerkedni vele, hogyan befolyásolja a kötet szabad területére [](#sync-policies-that-affect-cloud-tiering)vonatkozó házirend az új kiszolgálóvégpont hozzáadásakor eredetileg letöltött fájlokat, tekintse meg a felhőbeli rétegezést befolyásoló szinkronizálási szabályzatokat) című szakaszt.

#### <a name="date-policy"></a>Dátum szabályzat
A dátum **szabályzatával** a gyakran használt fájlok rétegezve vannak a felhőbe, ha x számú napig nem fértek hozzá (azaz nem olvasott vagy íródottak a fájlba). Ha például azt vette észre, hogy a 15 napnál több, anélkül elért fájlok általában archiválási fájlok, érdemes 15 napra állítani a dátum-szabályzatot. 

További példák a dátum- és kötetterület-szabályzatok együttes használatára: [Felhőbeli Azure File Sync szabályzatok kiválasztása.](file-sync-choose-cloud-tiering-policies.md)

### <a name="windows-server-data-deduplication"></a>Windows Server adatdeduplikáció
Az adatdeduplikáció olyan kötetek esetében támogatott, amelyeken engedélyezve van a felhőbeli rétegezés a Windows Server 2016-tól kezdve. További részletekért lásd: [Planning for an Azure File Sync deployment](file-sync-planning.md#data-deduplication)..

### <a name="cloud-tiering-heatmap"></a>Felhőbeli rétegezés hőtérképe
Azure File Sync figyeli a fájlelérést (olvasási és írási műveleteket) az idő során, és a legutóbbi és gyakori hozzáférés alapján minden fájlhoz hőpontot rendel. Ezekkel a pontszámokkal "hőtérképet" hoz létre a névtérről minden kiszolgálóvégponton. Ez az hőtérkép egy olyan helyen található összes szinkronizáló fájl listája, ahol engedélyezve van a felhőbeli rétegezés, az hőpontszámuk szerint rendezetten. A közelmúltban megnyitott gyakran használt fájlok gyakran használtnak minősülnek, míg azok a fájlok, amelyekhez egy ideje még nem fértek hozzá, gyakran használt fájloknak számítanak. 

Az adott hőtérképen található egyes fájlok relatív pozíciójának meghatározásához a rendszer a maximális időbélyeget használja a következő sorrendben: MAX(Utolsó hozzáférés ideje, Utolsó módosítás időpontja, Létrehozás ideje). 

Az utolsó hozzáférés idejét általában nyomon követi és elérhetővé válik. Ha azonban új kiszolgálóvégpontot hoz létre engedélyezett felhőbeli rétegezéssel, nem elég idő telt el a fájlelérés megfigyeléséhez. Ha nincs érvényes utolsó hozzáférési időpont, akkor a rendszer az utolsó módosítás idejét használja az hőtérkép relatív pozíciójának kiértékeléséhez.  

A dátum szabályzat ugyanúgy működik. Utolsó hozzáférési időpont nélkül a dátum-szabályzat az utolsó módosítás időpontja szerint fog viselkedni. Ha ez nem érhető el, a fájl létrehozási idejét váltja fel. Idővel a rendszer egyre több fájlelérési kérést fog megfigyelni, és automatikusan elkezdi használni az ön nyomon követhető utolsó hozzáférési időt.

> [!Note]
> A felhőbeli rétegezés nem függ az NTFS szolgáltatástól a legutóbbi hozzáférési idő nyomon követéséhez. Ez az NTFS-szolgáltatás alapértelmezés szerint ki van kapcsolva, és teljesítménybeli megfontolások miatt nem javasoljuk, hogy manuálisan engedélyezze ezt a funkciót. A felhőbeli rétegezés külön követi az utolsó hozzáférési időt.

### <a name="sync-policies-that-affect-cloud-tiering"></a>A felhőbeli rétegezést befolyásoló szinkronizálási szabályzatok

A Azure File Sync 11-es verziójával két további Azure File Sync állíthat be, amelyek hatással vannak  a felhőrétegezésre: a kezdeti letöltés és a proaktív **visszahívás.**

#### <a name="initial-download"></a>Kezdeti letöltés

Amikor egy kiszolgáló fájlokat tartalmaz az Azure-fájlmegosztáshoz csatlakozik, eldöntheti, hogy a kiszolgáló hogyan töltse le először a fájlmegosztási adatokat. Ha a felhőbeli rétegezés engedélyezve van, két lehetőség közül választhat. 

![Képernyőkép a kezdeti letöltésről, ha a felhőbeli rétegezés engedélyezve van](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-3.png)  

Az első lehetőség a névtér visszahívása, majd a fájl tartalmának visszahívása az utolsó módosítás időbélyegével, amíg el nem éri a helyi lemezkapacitást. Ha elegendő lemezterület áll rendelkezésre, és tudja, hogy a legutóbb módosított fájlokat helyben kell gyorsítótárazza, ez a lehetőség ideális választás. Azok a fájlok, amelyek a lemezterület hiánya miatt nem tárolhatók helyileg, rétegzettek lesznek.        

A második lehetőség, hogy kezdetben csak a névteret hívja vissza, és csak akkor hívja vissza a fájl tartalmát, ha hozzáférnek. Ez a lehetőség akkor a legjobb, ha minimalizálni szeretné a helyi lemezen használt kapacitást, és azt szeretné, hogy a felhasználók döntsék el, mely fájlokat érdemes helyben gyorsítótárazza. Ezzel a beállítással minden fájl rétegzett fájlként indul el.

![Képernyőkép a kezdeti letöltésről, amikor a felhőbeli rétegezés le van tiltva](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-4.png)

Ha a felhőbeli rétegezés le van tiltva, van egy harmadik lehetőség is, amely a rétegzett fájlok együttes elkerülését jelenti. Ebben az esetben a fájlok csak a teljes letöltés után jelennek meg a kiszolgálón. Ha olyan alkalmazásokkal dolgozik, amelyek teljes fájlokat igényelnek, és nem tolerálják a rétegzett fájlokat a névterében, ez ideális megoldás.      

#### <a name="proactive-recalling"></a>Proaktív visszahívás

A fájlok létrehozásakor vagy módosításakor proaktív módon visszahívhat egy fájlt a megadott kiszolgálókra. Ez lehetővé teszi, hogy az új vagy módosított fájl használatra kész legyen az egyes megadott kiszolgálókon. 

Egy globálisan elosztott vállalat például fiókirodákkal rendelkezik az Egyesült Államok és India területén. Reggel (amerikai idő szerint) az információs dolgozók létrehoznak egy új mappát és új fájlokat egy teljesen új projekthez, és egész nap ezen dolgoznak. Azure File Sync azure-fájlmegosztásba (felhőbeli végpont) szinkronizálja a mappát és a fájlokat. Az indiai információs dolgozók továbbra is a saját időzónában dolgoznak a projekten. Amikor reggel érkeznek, az indiai helyi Azure File Sync-kompatibilis kiszolgálónak helyileg elérhetővé kell tenni ezeket az új fájlokat, hogy az indiai csapat hatékonyan dolgoztata ki a helyi gyorsítótárat. Ennek a módnak az engedélyezése megakadályozza, hogy a kezdeti fájlelérés az igény szerinti visszahívás miatt lassabb legyen, és lehetővé teszi, hogy a kiszolgáló proaktívan visszahívja a fájlokat, amint azok módosultak vagy létrejöttek az Azure-fájlmegosztásban.

Ha a kiszolgálóra visszahívott fájlokra nincs szükség helyileg, akkor a szükségtelen visszahívás növelheti a bejövő forgalmat és a költségeket. Ezért csak akkor engedélyezze a proaktív visszahívást, ha tudja, hogy a kiszolgáló gyorsítótárának a felhőből végzett legutóbbi módosításokkal való előzetes felugrása pozitív hatással lesz a kiszolgálón lévő fájlokat használó felhasználókra vagy alkalmazásokra. 

A proaktív visszahívás engedélyezése a kiszolgáló nagyobb sávszélesség-használatát is eredményezheti, és a helyi kiszolgálón lévő egyéb, viszonylag új tartalmak agresszív rétegezését okozhatja a fájlok számának növekedése miatt. Ez azonban több visszahívási előhívást okozhat, ha a rétegzett fájlokat a kiszolgálók forrónak tekintik. 

:::image type="content" source="media/storage-sync-files-deployment-guide/proactive-download.png" alt-text="Kép az Azure-fájlmegosztás aktuálisan érvényben lévő kiszolgálóvégpont letöltési viselkedéséről, valamint egy olyan gombról, amely megnyit egy menüt, amely lehetővé teszi a módosítását.":::

További részletek a proaktív visszahívásról: [Deploy Azure File Sync](file-sync-deployment-guide.md#proactively-recall-new-and-changed-files-from-an-azure-file-share).

## <a name="tiered-vs-locally-cached-file-behavior"></a>Rétegzett és helyileg gyorsítótárazott fájlok viselkedése

A felhőbeli rétegezés a névtér (a fájl- és mappahierarchia, valamint a fájltulajdonságok) és a fájltartalom elkülönítése. 

#### <a name="tiered-file"></a>Rétegzett fájl

Rétegzett fájlok esetén a lemez mérete nulla, mivel maga a fájl tartalma nem helyileg van tárolva. Ha egy fájl rétegzett, Azure File Sync fájlrendszerszűrő (StorageSync.sys) helyileg lecseréli a fájlt egy mutatóra (újraelemzési pont). Az újraelemzési pont az Azure-fájlmegosztásban található fájl URL-címét jelöli. A rétegzett fájlok az "offline" és a FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS attribútumot is tartalmaznak az NTFS fájlrendszerben, így a harmadik féltől származó alkalmazások biztonságosan azonosítják a rétegzett fájlokat.   

![Képernyőkép egy fájl tulajdonságairól rétegzett – csak névtér.](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-2.png)    

#### <a name="locally-cached-file"></a>Helyileg gyorsítótárazott fájl

A helyszíni fájlkiszolgálón tárolt fájlok esetén azonban a lemez mérete körülbelül megegyezik a fájl logikai méretével, mivel a teljes fájl (fájlattribútumok és fájltartalom) helyileg van tárolva.     

![Képernyőkép egy fájl tulajdonságairól, ha az nincs rétegzve – névtér + fájltartalom.](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-1.png) 

A fájlok részlegesen rétegzettek (vagy részlegesen előhívhatóak) is. Egy részlegesen rétegzett fájlban a fájl egy része lemezen található. Ez akkor fordulhat elő, ha a fájlokat részlegesen beolvasnak olyan alkalmazások, mint a multimédia-lejátszók vagy a fájlokhoz való streamelést támogató zip-segédprogramok. Azure File Sync intelligens, és csak a csatlakoztatott Azure-fájlmegosztástól kért adatokat hívja vissza.

> [!NOTE]
> A Size (Méret) a fájl logikai méretét jelöli. A lemez mérete a lemezen tárolt fájlstream fizikai méretét jelöli.

## <a name="next-steps"></a>Következő lépések

* [Felhőbeli Azure File Sync rétegezésére vonatkozó szabályzatok kiválasztása](file-sync-choose-cloud-tiering-policies.md)
* [Az Azure File Sync üzembe helyezésének megtervezése](file-sync-planning.md)