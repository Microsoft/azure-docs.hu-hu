---
title: HANA biztonsági mentés és visszaállítás az Azure-beli SAP HANAon (nagyméretű példányok) | Microsoft Docs
description: A HANA biztonsági mentésének és visszaállításának végrehajtása az Azure-beli SAP HANAon (nagyméretű példányok)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/16/2020
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3244cbca3df717d3432670a366147408cf2b2c0e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101669119"
---
# <a name="backup-and-restore-of-sap-hana-on-hana-large-instances"></a>SAP HANA biztonsági mentése és visszaállítása a HANA nagyméretű példányain

>[!IMPORTANT]
>Ez a cikk nem helyettesíti a SAP HANA adminisztrációs dokumentációt vagy az SAP-megjegyzéseket. Reméljük, hogy alapos ismeretekkel rendelkezik a SAP HANA adminisztrációval és műveletekkel kapcsolatban, különösen a biztonsági mentés, a visszaállítás, a magas rendelkezésre állás és a vész-helyreállítás terén. Ebben a cikkben a SAP HANA studióból származó képernyőképek jelennek meg. A tartalom, a struktúra és az SAP felügyeleti eszközeinek és az eszközöknek a tulajdonságai megváltozhatnak SAP HANA kiadásról a kiadásra.

Fontos, hogy a környezetében és a HANA verzióiban és kiadásaiban végrehajtott lépéseket és folyamatokat gyakorolja. A cikkben ismertetett egyes folyamatok egyszerűbben megismerhetik az általános ismereteket. Ezeket nem a végleges művelet-kézikönyvek részletes lépéseiként használják. Ha a konfigurációkhoz szeretne műveleti kézikönyveket létrehozni, tesztelje és gyakorolja a folyamatokat, és dokumentálja az adott konfigurációkhoz kapcsolódó folyamatokat. 

Az operációs adatbázisok egyik legfontosabb szempontja a katasztrofális események elleni védelem. Az események oka az lehet, hogy a természeti katasztrófák egyszerű felhasználói hibákhoz vezethetnek.

Egy adatbázis biztonsági mentése, amely bármely időpontra visszaállítható, például a kritikus fontosságú adatműveletek törlése előtt, lehetővé teszi a helyreállítást olyan állapotba, amely a lehető legkorábbi módon, a megszakadási folyamat előtt van.

A visszaállítási képesség eléréséhez két típusú biztonsági mentést kell végrehajtani:

- Adatbázis biztonsági mentései: teljes, növekményes vagy differenciált biztonsági másolatok
- Tranzakciónapló biztonsági mentései

A teljes adatbázisra vonatkozó biztonsági másolatok alkalmazási szinten történő végrehajtása mellett a tárolási pillanatképekkel rendelkező biztonsági mentéseket is végrehajthat. A tárolási Pillanatképek nem helyettesítik a tranzakciónapló biztonsági másolatait. A tranzakciónapló biztonsági mentései továbbra is fontosak maradnak az adatbázis egy adott időpontra való visszaállításához, vagy a már Véglegesített tranzakciók naplófájljainak kiürítéséhez. A tárolási Pillanatképek felgyorsítják a helyreállítást azáltal, hogy gyorsan továbbítják az adatbázis egy előre látható képét. 

SAP HANA az Azure-ban (nagyméretű példányok) két biztonsági mentési és visszaállítási lehetőséget kínál:

- **Saját magát (DIY).** Miután ellenőrizte, hogy van-e elég szabad lemezterület, hajtsa végre a teljes adatbázist és a naplók biztonsági mentését az alábbi lemezes biztonsági mentési módszerek egyikével. Biztonsági mentést készíthet közvetlenül a HANA nagyméretű példány-egységekhez vagy egy Azure-beli virtuális gépen (VM) beállított NFS-megosztásokhoz csatolt kötetekre. Az utóbbi esetben az ügyfelek egy Linux rendszerű virtuális gépet állítanak be az Azure-ban, csatlakoztatják az Azure Storage-t a virtuális géphez, és megosztják a tárolót az adott virtuális gépen konfigurált NFS-kiszolgálón keresztül. Ha a biztonsági mentést olyan köteteken végzi el, amelyek közvetlenül a HANA nagyméretű példány-egységekhez vannak csatolva, másolja a biztonsági másolatokat egy Azure Storage-fiókba. Ezt egy olyan Azure-beli virtuális gép beállítása után végezze el, amely az Azure Storage-on alapuló NFS-megosztásokat exportál. A Azure Backup-tárolót vagy az Azure-beli hideg tárterületet is használhatja. 

   Egy másik lehetőség, hogy egy külső gyártótól származó adatvédelmi eszköz használatával tárolja a biztonsági mentéseket az Azure Storage-fiókba való másolás után. Szükség lehet a DIY Backup lehetőségre is, ha olyan adatmennyiségre van szükség, amelyet hosszabb ideig kell tárolni a megfelelőségi és naplózási célokra. A biztonsági mentések minden esetben a virtuális gépen és az Azure Storage-on keresztül képviselt NFS-megosztásba másolódnak.

- **Infrastruktúra biztonsági mentési és visszaállítási funkciója.** Használhatja a biztonsági mentési és visszaállítási funkciót is, amely az Azure-beli SAP HANA mögöttes infrastruktúráját (nagyméretű példányok) biztosítja. Ez a beállítás megfelel a biztonsági mentések és a gyors visszaállítások szükségességének. Ennek a szakasznak a többi része a HANA Large instances által kínált biztonsági mentési és visszaállítási funkciókkal foglalkozik. Ez a szakasz azt is ismerteti, hogy a biztonsági mentés és a visszaállítás milyen kapcsolatban áll a nagy méretű HANA-példányok által kínált vész-helyreállítási funkciókkal.

> [!NOTE]
>   A HANA nagyméretű példányok mögöttes infrastruktúrája által használt pillanatkép-technológia SAP HANA pillanatképekkel való függőséggel rendelkezik. Ezen a ponton SAP HANA Pillanatképek nem működnek együtt SAP HANA több-bérlős adatbázis-tárolók több Bérlővel. Ha csak egy bérlő van telepítve, SAP HANA Pillanatképek működnek, és ezt a módszert használhatja.

## <a name="use-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Azure-beli SAP HANA tárolási Pillanatképek használata (nagyméretű példányok)

Az Azure-ban (nagyméretű példányokon) alapuló SAP HANA tárolási infrastruktúra támogatja a kötetek tárolási pillanatképeit. A kötetek biztonsági mentése és helyreállítása is támogatott, a következő szempontokkal:

- Az adatbázis teljes biztonsági mentése helyett a tárolási kötetek pillanatképeit gyakran vesszük figyelembe.
- Ha egy pillanatképet a/Hana/Data és a/Hana/Shared keresztül indít el, amely magában foglalja a/usr/SAP, a köteteket, a pillanatkép-technológia elindít egy SAP HANA pillanatképet, mielőtt futtatja a tárolási pillanatképet. Ez a SAP HANA pillanatkép a tárolási pillanatkép helyreállítását követően a telepítési pont a végleges naplók visszaállításához. Ahhoz, hogy egy HANA-pillanatkép sikeres legyen, aktív HANA-példányra van szüksége. Egy HSR-forgatókönyvben nem támogatott a tárolási pillanatkép olyan aktuális másodlagos csomóponton, ahol a HANA-pillanatkép nem hajtható végre.
- A tárolási pillanatkép sikeres futtatása után a rendszer törli a SAP HANA pillanatképet.
- A tranzakciós napló biztonsági mentései gyakran és a/Hana/logbackups-kötetben vagy az Azure-ban tárolódnak. A tranzakciós napló biztonsági másolatait tartalmazó/Hana/logbackups-kötetet elindíthatja külön pillanatkép készítéséhez. Ebben az esetben nem kell HANA-pillanatképet futtatnia.
- Ha egy adatbázist egy adott időpontra kell visszaállítani, éles leállás esetén kérje meg, hogy Microsoft Azure támogassa vagy SAP HANA az Azure Restore szolgáltatásban egy bizonyos tárolási pillanatképre. Ilyen például a homokozó rendszer tervezett visszaállítása az eredeti állapotába.
- A tárolási pillanatképben szereplő SAP HANA pillanatkép egy eltolási pont a tranzakciónaplók futtatására szolgáló, és a tárolási pillanatkép készítése után tárolt biztonsági másolatok alkalmazásához.
- Ezek a tranzakciónaplók biztonsági másolatokat készítenek az adatbázis visszaállítására egy adott időpontra.

Olyan tárolási pillanatképeket is végrehajthat, amelyek a kötetek három osztályát célozzák meg:

- A/Hana/Data és a/Hana/Shared együttes pillanatképe, amely magában foglalja A/usr/SAP. Ehhez a pillanatképhez szükség van egy SAP HANA pillanatkép létrehozására a tárolási pillanatkép előkészítéséhez. A SAP HANA pillanatkép biztosítja, hogy az adatbázis konzisztens állapotban legyen a tárolási szempontból. A visszaállítási folyamat esetében ez a beállítás egy pont.
- Külön pillanatkép A/Hana/logbackups.
- Operációs rendszer partíciója.

A pillanatképek legújabb parancsfájljainak és dokumentációjának lekéréséhez lásd: [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). Amikor letölti a pillanatkép-parancsfájl csomagot a [githubról](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md), három fájlt kap. Az egyik fájl dokumentálva van egy PDF-fájlban a megadott funkciókhoz. Az eszközkészlet letöltése után kövesse a "pillanatkép-eszközök beszerzése" című témakör utasításait.

## <a name="storage-snapshot-considerations"></a>Tárolási pillanatképgel kapcsolatos megfontolások

>[!NOTE]
>A tárolási Pillanatképek a HANA nagyméretű példány-egységekhez lefoglalt tárolóhelyet használják fel. Vegye figyelembe a tárolási Pillanatképek ütemezésének és a tárolási Pillanatképek számának a következő aspektusait. 

Az Azure-beli SAP HANA tároló-Pillanatképek (nagyméretű példányok) konkrét mechanikája a következők:

- Egy adott tárolási pillanatkép abban az időpontban, amikor a használat során kevés tárterületet használ fel.
- Az adattartalom változásai és a SAP HANA adatfájlok tartalmának változása megváltozik a tárolási köteten, a pillanatképnek az eredeti blokk tartalmát és az adatok változásait kell tárolnia.
- Ennek eredményeképpen a tárolási pillanatkép mérete növekszik. Minél hosszabb a pillanatkép, annál nagyobb lesz a tárolási pillanatkép.
- A tárolási Pillanatképek élettartama során a SAP HANA adatbázis-köteten végrehajtott módosítások nagyobbak lesznek, mint a tárolási Pillanatképek lemezterület-felhasználása.

SAP HANA az Azure-ban (nagyméretű példányok) a SAP HANA-és a naplózási kötetek számára rögzített méretű köteteket tartalmaz. Ezeknek a köteteknek a pillanatképei megeszik a kötet területére. A következőket kell tennie:

- A tárolási Pillanatképek ütemezett időpontjának meghatározása.
- A tárolási kötetek lemezterület-felhasználásának figyelése. 
- Kezelheti a tárolt Pillanatképek számát. 

A tárolási pillanatképeket letilthatja, ha adattömeget importál vagy egyéb jelentős változtatásokat hajt végre a HANA-adatbázisban. 


A következő szakaszokban a pillanatképek végrehajtásával kapcsolatos információkat talál, és általános javaslatokat is tartalmaz:

- Bár a hardver 255 pillanatképet képes fenntartani köteten, a szám alatt jóval többet szeretne tudni. Az ajánlás 250 vagy kevesebb.
- A tárolási Pillanatképek elvégzése előtt figyelje meg és tartsa nyomon a szabad területet.
- Csökkentse a tárolási Pillanatképek számát a szabad terület alapján. Csökkentheti a megmaradt Pillanatképek számát, vagy kiterjesztheti a köteteket. A további tárterület 1 terabájtos egységekben is megrendelhető.
- Olyan tevékenységek során, mint például az adatok áthelyezése a SAP HANAba az SAP platform áttelepítési eszközeivel (R3load) vagy a SAP HANA adatbázisok biztonsági másolatokból való visszaállításával, tiltsa le a tárolási pillanatképeket a/Hana/Data köteten 
- SAP HANA táblázatok nagyobb újraszervezése során Kerülje a tárolási pillanatképeket, ha lehetséges.
- A tárolási Pillanatképek előfeltétele, hogy kihasználhassa az Azure-beli SAP HANA vész-helyreállítási képességeit (nagyméretű példányok).

## <a name="prerequisites-for-using-self-service-storage-snapshots"></a>Az önkiszolgáló tárolási pillanatképek használatának előfeltételei

Győződjön meg arról, hogy a pillanatkép-parancsfájl futtatása sikeresen megtörtént, ellenőrizze, hogy a Perl a HANA Large instances Server Linux operációs rendszerére van-e telepítve. A Perl előre telepítve van a HANA nagyméretű példányának egységén. A Perl verziójának vizsgálatához használja a következő parancsot:

`perl -v`

![A nyilvános kulcsot a parancs futtatásával másolhatja.](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


## <a name="set-up-storage-snapshots"></a>Tárolási Pillanatképek beállítása

A nagyméretű HANA-példányokkal rendelkező tárolási Pillanatképek beállításához kövesse az alábbi lépéseket.
1. Győződjön meg arról, hogy a Perl a HANA Large instances-kiszolgálón a Linux operációs rendszerre van telepítve.
1. Módosítsa a/etc/ssh/SSH \_ konfigurációját, és adja hozzá a következő sort: _Mac HMAC-SHA1_.
1. Hozzon létre egy SAP HANA biztonsági mentési felhasználói fiókot a főcsomóponton minden egyes futtatott SAP HANA-példányhoz, ha van ilyen.
1. Telepítse a SAP HANA HDB-ügyfelet az összes SAP HANA Large Instances-kiszolgálóra.
1. Az egyes régiók első SAP HANA Large Instances kiszolgálóján hozzon létre egy nyilvános kulcsot a pillanatkép-készítést vezérlő mögöttes tárolási infrastruktúra eléréséhez.
1. Másolja a szkripteket és a konfigurációs fájlt a [githubról](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md) a **hdbsql** helyére a SAP HANA telepítésben.
1. Módosítsa a megfelelő ügyfél-specifikációk szerint a *HANABackupDetails.txt* fájlt.

Szerezze be a legfrissebb pillanatkép-szkripteket és dokumentációt a [githubról](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md). A korábban felsorolt lépésekért lásd: [Microsoft Snapshot Tools for SAP HANA on Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

### <a name="consideration-for-mcod-scenarios"></a>MCOD-forgatókönyvek megfontolása
Ha egy HANA nagyméretű példány-egységen több SAP HANA példánnyal rendelkező [MCOD-forgatókönyvet](https://launchpad.support.sap.com/#/notes/1681092) futtat, külön tárolási köteteket kell kiépíteni az egyes SAP HANA példányokhoz. A MDC és egyéb szempontokkal kapcsolatos további információkért tekintse meg az Azure-beli [SAP HANA Microsoft pillanatkép-eszközeinek](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)"fontos tudnivalók" című témakörét.
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>1. lépés: a SAP HANA HDB-ügyfél telepítése

Az Azure-on SAP HANA telepített Linux operációs rendszer (nagyméretű példányok) tartalmazza azokat a mappákat és parancsfájlokat, amelyek szükségesek SAP HANA tárolási Pillanatképek futtatásához a biztonsági mentéshez és a vész-helyreállítási célokra. Újabb verziók a [githubban](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md). 

Az Ön felelőssége, hogy a SAP HANA HDB-ügyfelet a HANA nagyméretű példányok egységére telepítse a SAP HANA telepítésekor.

### <a name="step-2-change-the-etcsshssh_config"></a>2. lépés: a/etc/ssh/SSH \_ konfigurációjának módosítása

Ez a lépés a [Microsoft Snapshot Tools for SAP HANA on Azure-on](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)való kommunikáció engedélyezése a Storage szolgáltatással című témakörben található.


### <a name="step-3-create-a-public-key"></a>3. lépés: nyilvános kulcs létrehozása

Ha engedélyezni szeretné a HANA nagyméretű példányának bérlője számára a Storage pillanatkép-interfészek elérését, hozzon létre egy bejelentkezési eljárást egy nyilvános kulcson keresztül. 

A bérlőben az Azure-beli (nagyméretű példányok) kiszolgáló első SAP HANA hozzon létre egy nyilvános kulcsot a tárolási infrastruktúra eléréséhez. Nyilvános kulccsal nem kell jelszót bejelentkezni a Storage pillanatkép-felületekre. Nem kell nyilvános kulccsal megtartania a jelszó hitelesítő adatait is. 

Nyilvános kulcs létrehozásához tekintse meg az Azure-beli [SAP HANAhez készült Microsoft pillanatkép-eszközök](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)"a tárolóval való kommunikáció engedélyezése" címűét.


### <a name="step-4-create-an-sap-hana-user-account"></a>4. lépés: SAP HANA felhasználói fiók létrehozása

SAP HANA Pillanatképek létrehozásának megkezdéséhez hozzon létre egy felhasználói fiókot SAP HANA, hogy a tárolási pillanatkép parancsfájljai használhatók. Hozzon létre egy SAP HANA felhasználói fiókot a SAP HANA Studióban erre a célra. A felhasználót a SYSTEMDB alatt kell létrehozni, *nem* a MDC SID-adatbázisában. Az egytárolós környezetben a felhasználó létrehozása a bérlői adatbázisban történik. Ennek a fióknak a **Backup admin** és a **Catalog olvasási** jogosultsággal kell rendelkeznie. 

Felhasználói fiók beállításához és használatához tekintse meg a "kommunikáció engedélyezése SAP HANA használatával" című részt a [githubon](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).


### <a name="step-5-authorize-the-sap-hana-user-account"></a>5. lépés: a SAP HANA felhasználói fiók engedélyezése

Ebben a lépésben engedélyezi a létrehozott SAP HANA felhasználói fiókot, hogy a parancsfájlok ne kelljen a jelszavakat futásidőben elküldeni. Az SAP HANA parancs `hdbuserstore` lehetővé teszi egy SAP HANA felhasználói kulcs létrehozását. A kulcsot egy vagy több SAP HANA csomópont tárolja. A felhasználói kulcs lehetővé teszi a felhasználó hozzáférését SAP HANA anélkül, hogy a jelszavakat a parancsfájl-kezelési folyamaton belül kellene kezelnie. A parancsfájlkezelési folyamatot a cikk későbbi részében tárgyaljuk.

>[!IMPORTANT]
>Futtassa ezeket a konfigurációs parancsokat ugyanazzal a felhasználói környezettel, mint amelyen a pillanatkép-parancsok futnak. Ellenkező esetben a pillanatkép-parancsok nem fognak megfelelően működni.


### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>6. lépés: a pillanatkép-parancsfájlok beszerzése, a pillanatképek konfigurálása és a konfiguráció és a kapcsolat tesztelése

Töltse le a szkriptek legújabb verzióját a [githubról](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md). A parancsfájlok telepítésének módja a parancsfájlok 4,1-es kiadásával módosult. További információ: "a kommunikáció engedélyezése SAP HANAvel" a [Microsoft Snapshot Tools for SAP HANA on Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)-ban.

A parancsok pontos sorrendjének megtekintéséhez tekintse meg az Azure-beli [SAP HANAhoz készült Microsoft Snapshot](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)Tools (alapértelmezett) a pillanatkép-eszközök egyszerű telepítését ismertető témakört. Az alapértelmezett telepítés használatát javasoljuk. 

A 3. x verzióról a 4,1-re való frissítéshez tekintse meg a "meglévő telepítés frissítése" című részt a [Microsoft pillanatkép-eszközök SAP HANA az Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)-ban című témakörben. Az 4,1 eszközkészlet eltávolításához tekintse meg az Azure-beli [SAP HANA Microsoft pillanatkép](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)-eszközeinek "a pillanatkép-eszközök eltávolítása" című részét.

Ne felejtse el futtatni a "pillanatkép-eszközök telepítésének befejezése" című témakörben leírt lépéseket az Azure-beli [SAP HANAhez készült Microsoft Snapshot Tools eszközben](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

A különböző parancsfájlok és fájlok telepítésének célját a "Mik ezek a pillanatkép-eszközök?" című cikk ismerteti. a [Microsoft Snapshot Tools for SAP HANA az Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)-ban.

A pillanatkép-eszközök konfigurálása előtt győződjön meg arról, hogy a HANA biztonsági mentési helyei és beállításai helyesen vannak konfigurálva. További információkért tekintse meg az Azure-beli [SAP HANA Microsoft pillanatkép-eszközök](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)"SAP HANA konfigurálása" című témakörét.

A pillanatkép-eszközkészlet konfigurációját az Azure-beli [SAP HANA Microsoft pillanatkép-eszközök](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)"konfigurációs fájl-HANABackupCustomerDetails.txt" című részében találja.

#### <a name="test-connectivity-with-sap-hana"></a>Kapcsolat tesztelése SAP HANA

Miután a konfigurációs adatok bekerültek a *HANABackupCustomerDetails.txt* fájlba, győződjön meg arról, hogy a konfigurációk megfelelőek-e a HANA-példányok számára. Használja a parancsfájlt `testHANAConnection` , amely független egy SAP HANA vertikális Felskálázási vagy kibővíthető konfigurációtól.

További információkért tekintse meg a "kapcsolat ellenőrzése SAP HANA-testHANAConnection" című részt az Azure-beli [SAP HANA Microsoft Snapshot-eszközökben](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

#### <a name="test-storage-connectivity"></a>Tárolási kapcsolat tesztelése

A következő lépés a tárolóhoz való kapcsolódás ellenőrzése a *HANABackupCustomerDetails.txt* konfigurációs fájlba helyezett adathalmazok alapján. Ezután futtasson egy teszt pillanatképet. A parancs futtatása előtt `azure_hana_backup` futtatnia kell ezt a tesztet. A teszt parancsainak sorozata a [Microsoft Snapshot Tools for SAP HANA on Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)-ban című témakör "kapcsolat ellenőrzése a Storage-testStorageSnapshotConnection" című részében található.

A tárolási virtuálisgép-felületek sikeres bejelentkezését követően a parancsfájl a 2. fázissal folytatódik, és egy teszt pillanatképet hoz létre. A kimenet itt látható a SAP HANA három csomópontos kibővíthető konfigurációjában.

Ha a teszt pillanatképe sikeresen lefut a parancsfájllal, a tényleges tárolási Pillanatképek ütemezhetők. Ha nem sikerül, a továbblépés előtt vizsgálja meg a problémákat. A teszt pillanatképe csak az első valódi Pillanatképek elvégzése után marad.


### <a name="step-7-perform-snapshots"></a>7. lépés: Pillanatképek elvégzése

Az előkészítési lépések elvégzése után megkezdheti a tényleges tárolási Pillanatképek konfigurálását és beosztását. Az ütemezni kívánt szkript SAP HANA Felskálázási és kibővíthető konfigurációkkal működik. A biztonsági mentési parancsfájl rendszeres és rendszeres végrehajtásához a cron segédprogram használatával ütemezze a parancsfájlt. 

A parancs szintaxisának és működésének pontos szintaxisát lásd: "a pillanatképek biztonsági mentésének elvégzése azure_hana_backup" a [Microsoft Snapshot Tools for SAP HANA on Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)-ban. 

A szkript `azure_hana_backup` futtatásakor a a következő három fázisban hozza létre a tárolási pillanatképet:

1. SAP HANA pillanatképet futtat.
1. Egy tárolási pillanatképet futtat.
1. Eltávolítja a tárolási pillanatkép futtatása előtt létrehozott SAP HANA pillanatképet.

A parancsfájl futtatásához hívja meg azt a HDB végrehajtható mappából, amelybe másolták. 

A megőrzési időszak a parancsfájl futtatásakor paraméterként elküldött Pillanatképek számával lesz felügyelve. A tárolási Pillanatképek által lefedett idő a végrehajtás időszakának függvénye, valamint a parancsfájl futtatásakor paraméterként elküldött Pillanatképek száma. 

Ha a megőrzött Pillanatképek száma meghaladja a parancsfájl hívásában paraméterként megnevezett számot, a rendszer az új pillanatkép futtatása előtt törli ugyanazon címke legrégebbi tárolási pillanatképét. A hívás utolsó paramétereként megadott szám a megőrzött Pillanatképek számának szabályozására használható szám. Ezzel a számmal a pillanatképekhez használt lemezterületet is vezérelheti, közvetve is. 


## <a name="snapshot-strategies"></a>Pillanatkép-stratégiák
A különböző típusú Pillanatképek gyakorisága attól függ, hogy a HANA nagyméretű példány vész-helyreállítási funkcióját használja-e. Ez a funkció a tárolási pillanatképekre támaszkodik, amelyekre speciális javaslatok szükségesek a tárolási Pillanatképek gyakoriságára és végrehajtási idejére. 

Az ezt követő megfontolások és javaslatok alapján feltételezhető, hogy *nem* használja fel a nagy számú, nagyméretű példányok által kínált vész-helyreállítási funkciót. Ehelyett a tárolási Pillanatképek segítségével biztonsági másolatokat készíthet, és az elmúlt 30 napban biztosíthatja az időponthoz tartozó helyreállítást. A pillanatképek és a tárhely számának korlátai miatt vegye figyelembe a következő követelményeket:

- Az időponthoz tartozó helyreállítás helyreállítási ideje.
- A felhasznált terület.
- A helyreállítási pontra és a helyreállítási időre vonatkozó célkitűzések a katasztrófák lehetséges helyreállításához.
- A HANA teljes adatbázis biztonsági mentésének végleges végrehajtása a lemezeken. Ha a teljes adatbázis biztonsági mentést végez a lemezekről vagy a **backint** felületről, a tárolási Pillanatképek végrehajtása sikertelen lesz. Ha a teljes adatbázis biztonsági mentését tervezi a tárolási Pillanatképek tetején, győződjön meg arról, hogy a tárolási Pillanatképek végrehajtása ebben az időszakban le van tiltva.
- A pillanatképek másodpercenkénti száma, amely a 250-ra korlátozódik.

<!-- backint is term for a SAP HANA interface and not a spelling error not spelling errors -->

Ha nem használja a HANA Large-példányok vész-helyreállítási funkcióját, a pillanatkép időtartama ritkább. Ilyen esetekben végezze el a/Hana/Data és a/Hana/Shared együttes pillanatképeit, amelyek 12 órás vagy 24 órás időszakokban tartalmazzák a/usr/SAP-t. A pillanatképek megtartása egy hónapig. Ugyanez vonatkozik a napló biztonsági mentési kötetének pillanatképére is. SAP HANA tranzakciónaplók biztonsági mentésének végrehajtása a napló biztonsági másolati kötetén 5 – 15 perces időszakra történik.

Az ütemezett tárolási Pillanatképek a cron használatával érhetők el legjobban. Ugyanazt a parancsfájlt használja az összes biztonsági mentéshez és a vész-helyreállítási igényekhez. Módosítsa a parancsfájl bemeneteit, hogy azok megfeleljenek a különböző kért biztonsági mentési időpontoknak. Ezek a pillanatképek az összes, a cron-ben ütemezett módon, a végrehajtásuk idejétől függően változnak. Ez lehet óránként, 12 óránként, naponta vagy hetente. 

A következő példa egy cron-ütemtervet mutat be a/etc/crontab-ben:
```
00 1-23 * * * ./azure_hana_backup --type=hana --prefix=hourlyhana --frequency=15min --retention=46
10 00 * * *  ./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
00,05,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
22 12 * * *  ./azure_hana_backup --type=logs --prefix=dailylogback --frequncy=3min --retention=28
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeI --prefix=dailyboot --frequncy=15min --retention=28
```
Az előző példában egy óránként összevont pillanatkép tartalmazza azokat a köteteket, amelyek tartalmazzák a/Hana/Data és a/hana/shared/SID, amely magában foglalja a/usr/SAP, a helyszíneket. Használja ezt a pillanatképet az elmúlt két napban egy gyorsabb, időponthoz való helyreállításhoz. Ezen kötetek napi pillanatképe is rendelkezésre áll. Tehát két napos lefedettséggel rendelkezik óránkénti pillanatképekkel, valamint a napi Pillanatképek négy hetes lefedettséggel. A tranzakciós napló biztonsági másolati kötete is naponta biztonsági mentést készít. Ezek a biztonsági másolatok négy hétig tartanak. 

Ahogy a crontab harmadik sorában is látható, a HANA-tranzakciónapló biztonsági másolata 5 percenként fut. A tárolási pillanatképeket futtató különböző cron-feladatok indítási időpontjai lépcsőzetesen jelennek meg. Így a pillanatképek nem futnak egyszerre egy adott időpontban. 

A következő példában egy kombinált pillanatképet fog végrehajtani, amely tartalmazza a/Hana/Data és a/hana/shared/SID tartalmazó köteteket, beleértve a/usr/SAP, az óránkénti tárolóhelyeket. Ezeket a pillanatképeket két napig őrzi meg. A tranzakciónapló biztonsági mentési köteteinek pillanatképei 5 percenként futnak, és négy órán keresztül vannak tárolva. Ahogy korábban is, a HANA-tranzakciós naplófájl biztonsági mentése 5 percenként fut. 

A tranzakciónapló biztonsági mentési kötetének pillanatképét 2 perces késleltetéssel hajtja végre a tranzakciónapló biztonsági mentésének megkezdése után. Normális körülmények között a SAP HANA tranzakciónapló biztonsági mentése a 2 percen belül befejeződik. Ahogy korábban is, a rendszerindító LUN-ot tartalmazó kötetről naponta egyszer biztonsági mentés készül, és a tárolási Pillanatképek egy része négy hétig tart.

```
10 0-23 * * * ./azure_hana_backup --type=hana ==prefix=hourlyhana --frequency=15min --retention=48
0,5,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup --type=logs --prefix=logback --frequency=3min --retention=48
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeII --prefix=dailyboot --frequency=15min --retention=28
```

Az alábbi ábra az előző példában szereplő folyamatokat szemlélteti. A rendszerindító LUN ki van zárva.

![A biztonsági másolatok és a pillanatképek közötti kapcsolat](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA rendszeres írásokat végez a/Hana/log köteten, hogy dokumentálja az adatbázis véglegesített módosításait. A SAP HANA rendszeresen ír egy mentésipont a/Hana/Data kötetre. A crontabban megadott módon egy SAP HANA tranzakciós napló biztonsági mentése 5 percenként fut. 

Azt is láthatja, hogy egy SAP HANA pillanatkép óránként fut a/Hana/Data-és/hana/shared/SID-kötetek összesített tárolási pillanatképének elindításának eredményeképpen. Miután a HANA-pillanatkép sikeres volt, a kombinált tároló pillanatképe fut. A crontab utasításai szerint a/Hana/logbackup köteten lévő tárolási pillanatkép 5 percenként fut, körülbelül 2 perccel a HANA-tranzakciónapló biztonsági mentése után.

> 

>[!IMPORTANT]
> A SAP HANA biztonsági másolatokhoz tartozó tárolási Pillanatképek használata csak akkor hasznos, ha a pillanatképeket SAP HANA tranzakciónaplók biztonsági mentésével együtt hajtják végre. Ezeknek a tranzakciónaplóknak a biztonsági mentéseknek le kell terjedniük a tárolási Pillanatképek közötti időszakokra. 

Ha egy 30 napos időpontra vonatkozó helyreállítási időszakra vonatkozó kötelezettségvállalást állított be a felhasználók számára, a következőket kell tennie:

- A/Hana/Data és a 30 napos/hana/shared/SID, szélsőséges esetekben elérheti a közös tárolási pillanatképet. 
- Olyan összefüggő tranzakciós naplókból álló biztonsági másolatokkal rendelkezik, amelyek a kombinált tároló-Pillanatképek közötti időt fedik le. Így a tranzakciónapló biztonsági mentési kötetének legrégebbi pillanatképének 30 naposnak kell lennie. Ez nem igaz, ha a tranzakciós napló biztonsági másolatait az Azure Storage-ban található másik NFS-megosztásra másolja. Ebben az esetben előfordulhat, hogy lekéri a régi tranzakciós napló biztonsági másolatait az NFS-megosztásról.

A tárolási Pillanatképek kihasználása és a tranzakciónaplók biztonsági mentésének végleges tárolási replikálása érdekében módosítsa azt a helyet, ahová a SAP HANA a tranzakciónapló biztonsági másolatait írja. Ezt a módosítást a HANA Studióban teheti meg. 

Bár a SAP HANA automatikusan biztonsági másolatot készít a teljes naplókról, a napló biztonsági mentési intervallumát determinisztikus kell megadni. Ez különösen akkor igaz, ha a vész-helyreállítási beállítást használja, mert általában a determinisztikus-időszak használatával szeretné futtatni a naplók biztonsági másolatait. A következő esetben a rendszer 15 percet állít be a napló biztonsági mentési időközéként.

![SAP HANA biztonsági mentési naplók ütemezett SAP HANA Studióban](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

A 15 percenként gyakoribb biztonsági mentéseket is kiválaszthatja. A gyakori beállítást gyakran használják a HANA Large-példányok vész-helyreállítási funkcióival együtt. Néhány ügyfél 5 percenként hajtja végre a tranzakciónapló biztonsági mentését.

Ha az adatbázisról még soha nem készült biztonsági másolat, az utolsó lépés egy fájl alapú adatbázis biztonsági másolatának elvégzése, amely egy olyan biztonsági mentési bejegyzést hoz létre, amelynek léteznie kell a biztonsági mentési katalógusban. Ellenkező esetben a SAP HANA nem tudja elindítani a megadott naplók biztonsági másolatait.

![Fájl alapú biztonsági másolat készítése egyetlen biztonsági mentési bejegyzés létrehozásához](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


Az első sikeres tárolási Pillanatképek futtatása után törölje a 6. lépésben futtatott teszt-pillanatképet. További információ: "test Snapshots-removeTestStorageSnapshot eltávolítása" a [Microsoft Snapshot Tools for SAP HANA on Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 


### <a name="monitor-the-number-and-size-of-snapshots-on-the-disk-volume"></a>A lemez kötetén található Pillanatképek számának és méretének figyelése

Egy adott tárolási köteten figyelheti a pillanatképek számát és a pillanatképek tárolási felhasználását. A `ls` parancs nem jeleníti meg a pillanatképek könyvtárát vagy fájljait. A Linux operációs rendszer parancs `du` megjeleníti a tárolási Pillanatképek részleteit, mivel azok ugyanazon a köteten vannak tárolva. Használja a parancsot a következő beállításokkal:

- `du –sh .snapshot`: Ez a beállítás a pillanatképek könyvtárában lévő összes pillanatképet tartalmazza.
- `du –sh --max-depth=1`: Ez a lehetőség felsorolja a **. Snapshot** mappában mentett összes pillanatképet, valamint az egyes Pillanatképek méretét.
- `du –hc`: Ez a beállítás megadja az összes pillanatkép által használt teljes méretet.

Ezekkel a parancsokkal gondoskodhat arról, hogy a végrehajtott és tárolt Pillanatképek ne használják a kötetek összes tárolóját.

>[!NOTE]
>A rendszerindító LUN pillanatképei nem láthatók az előző parancsokkal.

### <a name="get-details-of-snapshots"></a>Pillanatképek részleteinek beolvasása
A pillanatképekkel kapcsolatos további részletekért használja a parancsfájlt `azure_hana_snapshot_details` . Ezt a parancsfájlt bármelyik helyen futtathatja, ha van aktív kiszolgáló a vész-helyreállítási helyen. A parancsfájl a következő kimenetet adja meg, amely a pillanatképeket tartalmazó kötetek szerinti bontásban található: 
   * Egy kötet összes pillanatképének mérete
   * Az alábbi részletek az adott kötet minden pillanatképében: 
      - Pillanatkép neve 
      - Létrehozás ideje 
      - A pillanatkép mérete
      - A pillanatkép gyakorisága
      - Az adott pillanatképhez társított HANA biztonsági mentési azonosító, ha van ilyen

A parancsok és kimenetek szintaxisát lásd: "Pillanatképek listázása azure_hana_snapshot_details" a [Microsoft Snapshot Tools for SAP HANA on Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)-ban. 



### <a name="reduce-the-number-of-snapshots-on-a-server"></a>A pillanatképek számának csökkentése egy kiszolgálón

Az előzőekben leírtak szerint csökkenthető a tárolt Pillanatképek egyes címkéjének száma. A parancs utolsó két paramétere a pillanatkép elindításához a címkét és a megtartani kívánt Pillanatképek számát jelöli.

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
```

Az előző példában a pillanatkép címkéje **dailyhana**. Az ezzel a címkével megőrzött Pillanatképek száma **28**. A lemezterület-felhasználásra adott válaszként érdemes csökkenteni a tárolt Pillanatképek számát. Egy egyszerű módszer a pillanatképek számának 15-re való csökkentésére, például az, hogy a szkriptet az utolsó paraméterrel állítsa **15**-re:

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=15
```

Ha ezzel a beállítással futtatja a parancsfájlt, a pillanatképek száma, amely tartalmazza az új tárolási pillanatképet, 15. A 15 legutóbbi Pillanatképek megmaradnak, és a 15 régebbi pillanatkép törlődik.

 >[!NOTE]
 > Ez a szkript csak akkor csökkenti a pillanatképek számát, ha a pillanatképek száma több, mint egy órával korábbi. A parancsfájl nem törli az egy óránál régebbi pillanatképeket. Ezek a korlátozások a választható vész-helyreállítási funkciókkal kapcsolatosak.

Ha már nem szeretné, hogy a **dailyhana** található biztonsági mentési előtaggal rendelkező Pillanatképek ne legyenek karbantartva, futtassa a szkriptet **0** értékkel a megőrzési számként. Ezután eltávolítja a címkének megfelelő összes pillanatképet. Az összes pillanatkép eltávolítása hatással lehet a HANA nagy példányszámú vész-helyreállítási funkció képességeire.

Az adott Pillanatképek törlésére szolgáló második lehetőség a szkript használata `azure_hana_snapshot_delete` . Ez a szkript egy pillanatkép vagy Pillanatképek készletének törlésére szolgál a Hana Studióban található HANA Backup-azonosító használatával vagy maga a pillanatkép-névvel. Jelenleg a biztonsági mentési azonosító csak a **Hana** pillanatkép-típushoz létrehozott pillanatképekhez van kötve. A **naplók** és a **rendszerindítás** pillanatképes biztonsági mentései nem végeznek SAP HANA pillanatképet, így nem található biztonsági mentési azonosító a pillanatképekhez. Ha a pillanatkép neve meg van adva, a rendszer az összes pillanatképet keresi a különböző köteteken, amelyek megfelelnek a megadott pillanatkép nevének. 

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->

A parancsfájllal kapcsolatos további információkért tekintse meg a "pillanatkép törlése – azure_hana_snapshot_delete" című részt a [Microsoft Snapshot Tools for SAP HANA on Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)-ban.

Futtassa a parancsfájlt felhasználói **gyökérként**.

>[!IMPORTANT]
>Ha vannak olyan adatok, amelyek csak a törölni kívánt pillanatképen szerepelnek, a pillanatkép törlése után az adatok örökre elvesznek.


## <a name="file-level-restore-from-a-storage-snapshot"></a>Fájl szintű visszaállítás tárolási pillanatképből

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->
A **Hana** és a **logs** típusú Pillanatképek esetében közvetlenül a **. Snapshot** könyvtár kötetein érheti el a pillanatképeket. Mindegyik pillanatképhez van egy alkönyvtár. Másolja az összes fájlt abba az állapotba, amely az adott alkönyvtárból származó pillanatkép pontján volt a tényleges címtár-struktúrába. 

A parancsfájl aktuális verziójában *nincs* olyan visszaállítási parancsfájl, amely a pillanatkép-visszaállításhoz önkiszolgálóként van megadva. A pillanatkép-visszaállítás az önkiszolgáló vész-helyreállítási parancsfájlok részeként is elvégezhető a vész-helyreállítási helyen a feladatátvétel során. Ha vissza szeretne állítani egy kívánt pillanatképet a meglévő elérhető pillanatképekről, a szolgáltatási kérelem megnyitásával kapcsolatba kell lépnie a Microsoft operatív csapatával.

>[!NOTE]
>Az egyetlen fájl visszaállítása nem működik a nagyméretű HANA-példány-egységek típusától független rendszerindító LUN pillanatképei esetében. A **. Snapshot** könyvtár nincs kitéve a rendszerindító LUN-ban. 
 

## <a name="recover-to-the-most-recent-hana-snapshot"></a>Helyreállítás a legutóbbi HANA-pillanatképre

Éles környezetben a tárolási Pillanatképek helyreállításának folyamata Microsoft Azure támogatással rendelkező ügyféli incidensként indítható el. Ez nagy sürgősséget jelent, ha az adatfeldolgozási rendszer törölve lett, és az egyetlen módja annak, hogy visszaállítsa a termelési adatbázist.

Egy másik helyzetben az időponthoz tartozó helyreállítás alacsony sürgősséggel és tervezett nappal előre is elvégezhető. A helyreállítást az Azure-SAP HANA is megtervezheti a magas prioritású jelzők növelése helyett. Előfordulhat például, hogy egy új javító csomag alkalmazásával tervezi frissíteni az SAP-szoftvert. Ezután egy olyan pillanatképre kell visszaállítania, amely a javító csomag frissítése előtt állapotot jelöl.

A kérelem elküldése előtt elő kell készítenie a következőt:. A SAP HANA az Azure csapata képes kezelni a kérést, és megadhatja a visszaállított köteteket. Ezt követően visszaállíthatja a HANA-adatbázist a pillanatképek alapján.

Az új eszközkészlettel visszaállított Pillanatképek lekérésének lehetőségeiről "pillanatkép visszaállítása" című részében talál további információt az [Azure-beli SAP HANAról a tárolási pillanatképből](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

A kérelem előkészítéséhez kövesse az alábbi lépéseket.

1. Döntse el, hogy melyik pillanatképet szeretné visszaállítani. Csak a Hana/az adatmennyiség állítható vissza, hacsak nem utasítja másképpen. 

1. Állítsa le a HANA-példányt.

   ![A HANA-példány leállítása](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. Válassza le az adatköteteket az egyes HANA-adatbázisok csomópontjain. Ha az adatkötetek továbbra is csatlakoztatva vannak az operációs rendszerhez, a pillanatkép visszaállítása sikertelen lesz.

   ![Az adatkötetek leválasztása az egyes HANA-adatbázisok csomópontjain](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. Nyisson meg egy Azure-támogatási kérést, és adjon meg egy adott pillanatkép visszaállítására vonatkozó utasításokat:

   - A helyreállítás során: SAP HANA az Azure szolgáltatásban megkérheti, hogy vegyen részt egy konferenciahívásban, hogy koordinálja, ellenőrizze és erősítse meg, hogy a megfelelő tárolási pillanatkép vissza lett-e állítva. 

   - A visszaállítást követően a SAP HANA az Azure szolgáltatásban értesítést küld, amikor visszaállítja a tárolási pillanatképet.

1. A visszaállítási folyamat befejezése után csatlakoztassa újra az összes adatkötetet.

   ![Az összes adatkötet újracsatlakoztatása](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)



Egy másik lehetőség a tárolási pillanatképből visszaállított adatfájlok beszerzésére, például az Azure-beli SAP HANA- [re vonatkozó kézi helyreállítási útmutató](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)7. lépésében, a tárolási pillanatképből való SAP HANA.

A pillanatképek biztonsági másolatból való visszaállításáról további információt [Az Azure-beli SAP HANA manuális helyreállítási útmutatójában talál a tárolási pillanatképből](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 

>[!Note]
>Ha a pillanatképet a Microsoft műveletei visszaállították, nem kell elvégeznie a 7. lépést.


### <a name="recover-to-another-point-in-time"></a>Helyreállítás másik időpontra
Egy adott időpontra való visszaállításhoz tekintse meg a "adatbázis helyreállítása a következő időpontra" című részt a [manuális helyreállítási útmutatóban SAP HANA Azure-ban egy tárolási pillanatképből](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 





## <a name="snapcenter-integration-in-sap-hana-large-instances"></a>SnapCenter-integráció SAP HANA nagyméretű példányokban

Ez a szakasz azt ismerteti, hogy az ügyfelek hogyan használhatják a NetApp SnapCenter szoftvert a Microsoft Azure HANA Large instances (HLI) szolgáltatásban üzemeltetett adatbázisok pillanatképének, biztonsági mentésének és SAP HANA visszaállításának elvégzéséhez. 

A SnapCenter megoldásokat kínál olyan forgatókönyvekhez, mint például a biztonsági mentés/helyreállítás, a vész-helyreállítási (DR) aszinkron tárolási replikáció, a rendszer replikálása és a rendszer klónozása. Az Azure-beli SAP HANA Large Instancesekkel integráltan a SnapCenter a biztonsági mentési és helyreállítási műveletekhez is használható.

További referenciák: NetApp TR-4614 és TR-4646 a SnapCenter-on.

- [SAP HANA biztonsági mentés/helyreállítás a SnapCenter (TR-4614)](https://www.netapp.com/us/media/tr-4614.pdf)
- [SAP HANA vész-helyreállítás tárolási replikálással (TR-4646)](https://www.netapp.com/us/media/tr-4646.pdf)
- [SAP HANA HSR SnapCenter (TR-4719)](https://www.netapp.com/us/media/tr-4719.pdf)
- [SAP-klónozás a SnapCenter-ből (TR-4667)](https://www.netapp.com/us/media/tr-4667.pdf)

### <a name="system-requirements-and-prerequisites"></a>Rendszerkövetelmények és előfeltételek

A SnapCenter Azure HLI való futtatásához a rendszerkövetelmények a következők:
* SnapCenter-kiszolgáló az Azure Windows 2016-es vagy újabb verziójával 4 vCPU, 16 GB RAM-mal és legalább 650 GB-os felügyelt prémium SSD-tárolóval.
* 1,5 TB-os rendszer – 24 TB RAM. SAP HANA Large Instances Javasoljuk, hogy használjon két SAP HANA nagyméretű példány-rendszert a klónozási műveletek és tesztek végrehajtásához.

A SnapCenter SAP HANAba való integrálásának lépései a következők: 

1. A felhasználó által generált nyilvános kulcs Microsoft Ops-csapattal való kommunikációjának támogatási jegyre vonatkozó kérése. Erre azért van szükség, hogy beállítsa a SnapCenter-felhasználó számára a tárolási rendszer elérését.
1. Hozzon létre egy virtuális gépet a VNET, amely hozzáféréssel rendelkezik a HLI; Ez a virtuális gép a SnapCenter használatos. 
1. Töltse le és telepítse a SnapCenter. 
1. Biztonsági mentési és helyreállítási műveletek. 

### <a name="create-a-support-ticket-for-user-role-storage-setup"></a>Támogatási jegy létrehozása a felhasználói szerepkörű tárolás telepítéséhez

1. Nyissa meg a Azure Portal, és navigáljon az **előfizetések** lapra. Az "előfizetések" lapon válassza ki a SAP HANA-előfizetését az alábbi piros színnel.

   :::image type="content" source="./media/snapcenter/create-support-case-for-user-role-storage-setup.png" alt-text="Támogatási eset létrehozása a felhasználói tároló beállításához":::

1. A SAP HANA-előfizetés lapon válassza ki az **erőforráscsoportok** allapot.

   :::image type="content" source="./media/snapcenter/solution-lab-subscription-resource-groups.png" alt-text="Megoldás labor előfizetési erőforráscsoport" lightbox="./media/snapcenter/solution-lab-subscription-resource-groups.png":::

1. Válasszon ki egy megfelelő erőforráscsoportot egy régióban.

   :::image type="content" source="./media/snapcenter/select-appropriate-resource-group-in-region.png" alt-text="Válassza ki a megfelelő erőforráscsoportot a régióban" lightbox="./media/snapcenter/select-appropriate-resource-group-in-region.png":::

1. Válassza ki az Azure Storage-ban SAP HANA megfelelő SKU-bejegyzést.

   :::image type="content" source="./media/snapcenter/select-sku-entry-corresponding-to-sap-hana.png" alt-text="Válassza ki a SAP HANA megfelelő SKU-bejegyzést." lightbox="./media/snapcenter/select-sku-entry-corresponding-to-sap-hana.png":::

1. Nyisson meg egy **új támogatási jegyet** , amely a vörös színnel van lefoglalva.

   :::image type="content" source="./media/snapcenter/open-new-support-ticket-request.png" alt-text="Új támogatási jegy kérésének megnyitása":::

1. Az **alapok** lapon adja meg a következő adatokat a jegyhez:

   * **Probléma típusa:** Technikai
   * **Előfizetés:** Az előfizetése
   * **Szolgáltatás:** SAP HANA nagyméretű példány
   * **Erőforrás:** Az erőforráscsoport
   * **Összefoglalás:** A felhasználó által létrehozott nyilvános kulcs megadása
   * **Probléma típusa:** Konfigurálás és beállítás
   * **Probléma altípusa:** SnapCenter beállítása a HLI


1. A támogatási jegy **leírásában** a **részletek** lapon adja meg a következőt: 
   
   * SnapCenter beállítása a HLI
   * A SnapCenter-felhasználó nyilvános kulcsa (SnapCenter. PEM) – tekintse meg a nyilvános kulcs létrehozása alább példát

     :::image type="content" source="./media/snapcenter/new-support-request-details.png" alt-text="Új támogatási kérelem – Részletek lap" lightbox="./media/snapcenter/new-support-request-details.png":::

1. A támogatási jegy áttekintéséhez válassza a **felülvizsgálat + létrehozás** elemet. 

1. Tanúsítvány létrehozása a SnapCenter felhasználónévhez a HANA nagyméretű példányon vagy bármely Linux-kiszolgálón.

   A SnapCenter felhasználónévre és jelszóra van szükség a Storage virtuális gép (SVM) eléréséhez és a HANA-adatbázis Pillanatképek létrehozásához. A Microsoft a nyilvános kulcs használatával lehetővé teszi, hogy (az ügyfél) beállítsa a jelszót a tárolási rendszer eléréséhez.

   ```bash
   openssl req -x509 -nodes -days 1095 -newkey rsa:2048 -keyout snapcenter.key -out snapcenter.pem -subj "/C=US/ST=WA/L=BEL/O=NetApp/CN=snapcenter"
   Generating a 2048 bit RSA private key
   ................................................................................................................................................+++++
   ...............................+++++
   writing new private key to 'snapcenter.key'
   -----

   sollabsjct31:~ # ls -l cl25*
   -rw-r--r-- 1 root root 1704 Jul 22 09:59 snapcenter.key
   -rw-r--r-- 1 root root 1253 Jul 22 09:59 snapcenter.pem

   ```

1. Csatlakoztassa a snapcenter. PEM fájlt a támogatási jegyhez, majd válassza a **Létrehozás** lehetőséget.

   A nyilvános kulcsú tanúsítvány elküldése után a Microsoft beállítja a bérlő SnapCenter-felhasználónevét a SVM IP-címével együtt.   

1. Miután megkapta a SVM IP-címet, állítsa be a jelszót a SVM eléréséhez, amelyet Ön vezérel.

   A következőkben egy példa látható a nagy méretű Hana-példányról vagy virtuális hálózatról származó REST-HÍVÁSra (dokumentációra), amely hozzáférést biztosít a HANA nagyméretű példány-környezetéhez, és a jelszó megadására lesz felhasználva.

   ```bash
   curl --cert snapcenter.pem --key snapcenter.key -X POST -k "https://10.0.40.11/api/security/authentication/password" -d '{"name":"snapcenter","password":"test1234"}'
   ```

   Győződjön meg arról, hogy a HANA DB rendszeren nincs aktív proxy-változó.

   ```bash
   sollabsjct31:/tmp # unset http_proxy
   sollabsjct31:/tmp # unset https_proxy
   ```

### <a name="download-and-install-snapcenter"></a>A SnapCenter letöltése és telepítése
Most, hogy a Felhasználónév be van állítva a SnapCenter való hozzáféréshez, a SnapCenter felhasználónevet használva konfigurálja a SnapCenter a telepítése után. 

A SnapCenter telepítése előtt tekintse át [SAP HANA biztonsági mentést vagy helyreállítást a SnapCenter](https://www.netapp.com/us/media/tr-4614.pdf) -mel a biztonsági mentési stratégia definiálásához. 

1. Jelentkezzen be a [NetApp](https://mysupport.netapp.com) -be a SnapCenter legújabb verziójának [letöltéséhez](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fmysupport.netapp.com%2Fsite%2Fproducts%2Fall%2Fdetails%2Fsnapcenter%2Fdownloads-tab&data=02%7C01%7Cmadhukan%40microsoft.com%7Ca53f5e2f245a4e36933008d816efbb54%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637284566603265503&sdata=TOANWNYoAr1q5z1opu70%2FUDPHjluvovqR9AKplYpcpk%3D&reserved=0) .

1. Telepítse a SnapCenter-t a Windows Azure virtuális gépre.

   A telepítő ellenőrzi a virtuális gép előfeltételeit. 

   >[!IMPORTANT]
   >Figyeljen a virtuális gép méretére, különösen nagyobb környezetekben.

1. Konfigurálja a SnapCenter tartozó felhasználói hitelesítő adatokat. Alapértelmezés szerint feltölti az alkalmazás telepítéséhez használt Windows felhasználói hitelesítő adatokat. 

   :::image type="content" source="media/snapcenter/installation-user-inputs-dialog.png" alt-text="Felhasználói bemenetek telepítése párbeszédpanel"::: 

1. A munkamenet indításakor mentse a biztonsági kivételt, és elindul a grafikus felhasználói felület.

1. Jelentkezzen be a SnapCenter a virtuális gépen ( https://snapcenter-vm:8146) a Windows hitelesítő adataival konfigurálja a környezetet.


### <a name="set-up-the-storage-system"></a>A tárolási rendszerek beállítása

1. A SnapCenter területen válassza a **tárolási rendszer**, majd az **+ új** lehetőséget. 

   :::image type="content" source="./media/snapcenter/snapcenter-storage-connections-window.png" alt-text="SnapCenter" lightbox="./media/snapcenter/snapcenter-storage-connections-window.png":::

   Az alapértelmezett érték a bérlők egy SVM. Ha az ügyfél több Bérlővel vagy HLIs rendelkezik több régióban, a javaslat az összes SVMs konfigurálása a SnapCenter-ben

1. A tárolási rendszer hozzáadása lapon adja meg a hozzáadni kívánt tárolási rendszer adatait, a SnapCenter felhasználónevét és jelszavát, majd válassza a **Submit (Küldés**) lehetőséget.

   :::image type="content" source="./media/snapcenter/new-storage-connection.png" alt-text="Új tárolási kapcsolatok":::

   >[!NOTE]
   >Az alapértelmezett érték a bérlők egy SVM.  Ha több bérlő is van, akkor a javaslat a SnapCenter összes SVMs konfigurálására szolgál. 

1. A SnapCenter-ben válassza a **gazdagépek** lehetőséget, majd a kiválasztás **+ Hozzáadás** elemet a Hana beépülő modul és a Hana db-gazdagépek beállításához.  A SnapCenter legújabb verziója automatikusan észleli a HANA-adatbázist a gazdagépen.

   :::image type="content" source="media/snapcenter/managed-hosts-new-host.png" alt-text="A SnapCenter területen válassza a gazdagépek lehetőséget, majd válassza a Hozzáadás lehetőséget." lightbox="media/snapcenter/managed-hosts-new-host.png":::

1. Adja meg az új gazdagép adatait:
   1. Válassza ki a gazdagép típusának operációs rendszerét.
   1. Adja meg a SnapCenter virtuális gép állomásnevét.
   1. Adja meg a használni kívánt hitelesítő adatokat.
   1. Válassza ki a **Microsoft Windows** és **SAP HANA** beállításokat, majd válassza a **Submit (Küldés**) lehetőséget.

   :::image type="content" source="media/snapcenter/add-new-host-operating-system-credentials.png" alt-text="Információk az új gazdagépről":::

   >[!IMPORTANT]
   >Az első csomópont telepítése előtt a SnapCenter lehetővé teszi, hogy a nem root felhasználó telepítse a beépülő modulokat az adatbázisba.  A nem legfelső szintű felhasználók engedélyezésével kapcsolatos információkért lásd: [nem gyökérszintű felhasználó hozzáadása és sudo-jogosultságok konfigurálása](https://library.netapp.com/ecmdocs/ECMLP2590889/html/GUID-A3EEB5FC-242B-4C2C-B407-510E48A8F131.html).

1. Tekintse át a gazdagép adatait, és válassza a **Submit (Küldés** ) lehetőséget a beépülő modul telepítéséhez a SnapCenter-kiszolgálón.

1. A beépülő modul telepítése után a SnapCenter-ben válassza a **gazdagépek** lehetőséget, majd a **+ Hozzáadás** elemet a HANA-csomópont hozzáadásához.

   :::image type="content" source="media/snapcenter/add-hana-node.png" alt-text="HANA-csomópont hozzáadása" lightbox="media/snapcenter/add-hana-node.png":::

1. Adja meg a HANA-csomópont adatait:
   1. Válassza ki a gazdagép típusának operációs rendszerét.
   1. Adja meg a HANA DB hostname vagy az IP-címet.
   1. Válassza ki a **+** HANA db gazda operációs rendszeren konfigurált hitelesítő adatok hozzáadását, majd kattintson **az OK gombra**.
   1. Válassza a **SAP HANA** , majd a **Küldés** lehetőséget.

   :::image type="content" source="media/snapcenter/add-hana-node-details.png" alt-text="SAP HANA csomópont részletei":::

1. Erősítse meg az ujjlenyomatot, és válassza a **jóváhagyás és küldés** lehetőséget.

   :::image type="content" source="media/snapcenter/confirm-submit-fingerprint.png" alt-text="Ujjlenyomat megerősítése és elküldése":::

1. A HANA csomóponton a rendszeradatbázis területen válassza a **biztonsági**  >  **felhasználók**  >  **SNAPCENTER** elemet a SNAPCENTER-felhasználó létrehozásához.

   :::image type="content" source="media/snapcenter/create-snapcenter-user-hana-system-db.png" alt-text="A SnapCenter-felhasználó létrehozása a HANA-ban (System db)":::



### <a name="auto-discovery"></a>Automatikus észlelés
A SnapCenter 4,3 alapértelmezés szerint engedélyezi az automatikus észlelési funkciót.  A HANA rendszer-replikációval (HSR) konfigurált HANA-példányok nem támogatják az automatikus észlelést. A példányt manuálisan kell hozzáadnia a SnapCenter-kiszolgálóhoz.


### <a name="hana-setup-manual"></a>HANA-telepítés (manuális)
Ha a HSR-t konfigurálta, akkor manuálisan kell konfigurálnia a rendszerkonfigurációt.  

1. A SnapCenter területen válassza az **erőforrások** és a **San HANA** (felül) lehetőséget, majd válassza a **+ SAP HANA adatbázis hozzáadása** lehetőséget (a jobb oldalon).

   :::image type="content" source="media/snapcenter/manual-hana-setup.png" alt-text="A HANA kézi beállítása" lightbox="media/snapcenter/manual-hana-setup.png":::

1. Megadhatja a HANA rendszergazda felhasználó által a Linux-gazdagépen konfigurált erőforrás-adatokat, vagy azon a gazdagépen, amelyen a beépülő modulok telepítve vannak. A biztonsági mentés a Linux rendszer beépülő moduljából lesz felügyelve.

   :::image type="content" source="media/snapcenter/provide-resource-details-sap-hana-database.png" alt-text="A Linux-gazdagépen konfigurált HANA rendszergazda felhasználó erőforrás-adatainak megadása.":::

1. Válassza ki azt az adatmennyiséget, amelyhez pillanatképeket szeretne készíteni, válassza a **Mentés** , majd a **Befejezés** lehetőséget.

   :::image type="content" source="media/snapcenter/provide-storage-footprint.png" alt-text="Válassza ki azt az adatmennyiséget, amelyhez pillanatképeket szeretne készíteni, válassza a mentés, majd a Befejezés lehetőséget.":::

### <a name="create-a-snapshot-policy"></a>Pillanatkép-szabályzat létrehozása

Mielőtt SnapCenter használ a SAP HANA adatbázis-erőforrások biztonsági mentéséhez, létre kell hoznia egy biztonsági mentési szabályzatot ahhoz az erőforráshoz vagy erőforráscsoporthoz, amelyről biztonsági másolatot szeretne készíteni. A pillanatkép-szabályzat létrehozásának folyamata során megadhatja a Pre/post parancsok és a speciális SSL-kulcsok konfigurálásának lehetőségét. A pillanatkép-szabályzatok létrehozásával kapcsolatos információkért lásd: [biztonsági mentési házirendek létrehozása SAP HANA adatbázisokhoz](http://docs.netapp.com/ocsc-43/index.jsp?topic=%2Fcom.netapp.doc.ocsc-dpg-sap-hana%2FGUID-246C0810-4F0B-4BF7-9A35-B729AD69954A.html).

1. A SnapCenter területen válassza az **erőforrások** lehetőséget, majd válasszon ki egy adatbázist.

   :::image type="content" source="media/snapcenter/select-database-create-policy.png" alt-text="A SnapCenter területen válassza az erőforrások lehetőséget, majd válasszon ki egy adatbázist.":::

1. A pillanatkép-ütemező konfigurálásához kövesse a konfigurációs varázsló munkafolyamatát.

   :::image type="content" source="media/snapcenter/follow-workflow-configuration-wizard.png" alt-text="A pillanatkép-ütemező konfigurálásához kövesse a konfigurációs varázsló munkafolyamatát." lightbox="media/snapcenter/follow-workflow-configuration-wizard.png":::

1. Adja meg az előre/post parancsok és a speciális SSL-kulcsok konfigurálásának beállításait.  Ebben a példában nem használunk speciális beállításokat.

   :::image type="content" source="media/snapcenter/configuration-options-pre-post-commands.png" alt-text="Adja meg a Pre-post parancsok és a speciális SSL-kulcsok konfigurálásának beállításait." lightbox="media/snapcenter/configuration-options-pre-post-commands.png":::

1. A **Hozzáadás** gombra kattintva létrehozhat egy pillanatkép-szabályzatot, amely más HANA-adatbázisokhoz is használható. 

   :::image type="content" source="media/snapcenter/select-one-or-more-policies.png" alt-text="A Hozzáadás gombra kattintva létrehozhat egy pillanatkép-szabályzatot, amely más HANA-adatbázisokhoz is használható.":::

1. Adja meg a szabályzat nevét és leírását.

   :::image type="content" source="media/snapcenter/new-sap-hana-backup-policy.png" alt-text="Adja meg a szabályzat nevét és leírását.":::


1. Válassza ki a biztonsági mentés típusát és gyakoriságát.

   :::image type="content" source="media/snapcenter/new-sap-hana-backup-policy-settings.png" alt-text="Válassza ki a biztonsági mentés típusát és gyakoriságát.":::

1. Konfigurálja az **igény szerinti biztonsági mentés megőrzési beállításait**.  A példánkban megtartjuk a megőrzést három pillanatkép-másolattal.

   :::image type="content" source="media/snapcenter/new-sap-hana-backup-policy-retention-settings.png" alt-text="Konfigurálja az igény szerinti biztonsági mentés megőrzési beállításait.":::

1. Az **óránkénti adatmegőrzési beállítások** konfigurálása. 

   :::image type="content" source="media/snapcenter/new-sap-hana-backup-policy-hourly-retention-settings.png" alt-text="Az óránkénti adatmegőrzési beállítások konfigurálása.":::

1. Ha a SnapMirror beállítása konfigurálva van, válassza **a snapmirror frissítése lehetőséget a helyi pillanatkép-másolat létrehozása után**.

   :::image type="content" source="media/snapcenter/new-sap-hana-backup-policy-snapmirror.png" alt-text="Ha SnapMirror van szükség, válassza a SnapMirror frissítése lehetőséget a helyi pillanatkép-másolat létrehozása után.":::

1. Válassza a **Befejezés** lehetőséget az új biztonsági mentési szabályzat összefoglalásának áttekintéséhez. 
1. Az **ütemterv konfigurálása** területen válassza a **Hozzáadás** lehetőséget.

   :::image type="content" source="media/snapcenter/configure-schedules-for-selected-policies.png" alt-text="Az ütemterv konfigurálása területen válassza a Hozzáadás lehetőséget.":::

1. Válassza ki a **kezdési dátumot**, a **lejárat** dátumát és a gyakoriságot.

   :::image type="content" source="media/snapcenter/add-schedules-for-policy.png" alt-text="Válassza ki a kezdési dátumot, a lejárat dátumát és a gyakoriságot.":::

1. Adja meg az értesítések e-mail-címét.

   :::image type="content" source="media/snapcenter/backup-policy-notification-settings.png" alt-text="Adja meg az értesítések e-mail-címét.":::

1.  A biztonsági mentési szabályzat létrehozásához válassza a **Befejezés** lehetőséget.

### <a name="disable-ems-message-to-netapp-autosupport"></a>EMS-üzenet letiltása a NetApp-támogatáshoz
Alapértelmezés szerint az EMS-adatgyűjtés engedélyezve van, és a telepítési dátum után hét naponta fut.  Az adatgyűjtés a PowerShell-parancsmaggal is letiltható `Disable-SmDataCollectionEms` .

1. A PowerShellben hozzon létre egy munkamenetet a SnapCenter.

   ```powershell
   Open-SmConnection
   ```

1. Jelentkezzen be a hitelesítő adataival.
1. Az EMS-üzenetek gyűjteményének letiltása.

   ```powershell
   Disable-SmCollectionEms
   ```

### <a name="restore-database-after-crash"></a>Adatbázis visszaállítása összeomlás után
A SnapCenter segítségével visszaállíthatja az adatbázist.  Ebben a szakaszban részletesen ismertetjük a lépéseket, de további információért lásd: [SAP HANA biztonsági mentés/helyreállítás a SnapCenter](https://www.netapp.com/us/media/tr-4614.pdf)-mel.


1. Állítsa le az adatbázist, és törölje az összes adatbázisfájlt.

   ```
   su - h31adm
   > sapcontrol -nr 00 -function StopSystem
   StopSystem
   OK
   > sapcontrol -nr 00 -function GetProcessList
   OK
   name, description, dispstatus, textstatus, starttime, elapsedtime, pid
   hdbdaemon, HDB Daemon, GRAY, Stopped, , , 35902
 
   ```

1. Válassza le az adatbázis kötetét.

   ```bash
   unmount /hana/data/H31/mnt00001
   ```


1. Az adatbázisfájlok visszaállítása a SnapCenter-on keresztül.  Válassza ki az adatbázist, majd válassza a **visszaállítás** lehetőséget.  

   :::image type="content" source="media/snapcenter/restore-database-via-snapcenter.png" alt-text="Válasszon ki egy adatbázist, és válassza a visszaállítás lehetőséget." lightbox="media/snapcenter/restore-database-via-snapcenter.png":::

1. Válassza ki a visszaállítási típust.  A példánkban visszaállítjuk a teljes erőforrást. 

   :::image type="content" source="media/snapcenter/restore-database-select-restore-type.png" alt-text="Válassza ki a visszaállítási típust.":::

   >[!NOTE]
   >Alapértelmezett beállításként nem kell megadnia a helyi visszaállításhoz szükséges parancsokat a lemezen lévő pillanatképből. 

   >[!TIP]
   >Ha egy adott LUN-t szeretne visszaállítani a köteten belül, válassza a **fájl szintje** lehetőséget.

1. Kövesse a munkafolyamatot a konfigurációs varázsló segítségével.
   
   A SnapCenter visszaállítja az adattárolást az eredeti helyre, így elindíthatja a helyreállítási folyamatot a HANA-ban. Mivel a SnapCenter nem tudja módosítani a biztonsági mentési katalógust (az adatbázis le van állítva), a rendszer figyelmeztetést jelenít meg.

   :::image type="content" source="media/snapcenter/restore-database-job-details-warning.png" alt-text="Mivel a SnapCenter nem tudja módosítani a biztonsági mentési katalógust, a rendszer figyelmeztetést jelenít meg. ":::

1. Mivel a rendszer az összes adatbázisfájlt visszaállítja, indítsa el a helyreállítási folyamatot a HANA-ban. A HANA Studio **rendszerek** területén kattintson a jobb gombbal a rendszeradatbázisra, majd válassza a **biztonsági mentés és helyreállítás**  >  **rendszeradatbázis helyreállítása** lehetőséget.

   :::image type="content" source="media/snapcenter/hana-studio-backup-recovery.png" alt-text="Indítsa el a helyreállítási folyamatot a HANA-ban.":::

1. Válasszon egy helyreállítási típust.

   :::image type="content" source="media/snapcenter/restore-database-select-recovery-type.png" alt-text="Válassza ki a helyreállítási típust.":::

1. Válassza ki a biztonsági mentési katalógus helyét.

   :::image type="content" source="media/snapcenter/restore-database-select-location-backup-catalog.png" alt-text="Válassza ki a biztonsági mentési katalógus helyét.":::

1. Válasszon ki egy biztonsági másolatot a SAP HANA-adatbázis helyreállításához.

   :::image type="content" source="media/snapcenter/restore-database-select-backup.png" alt-text="Válasszon ki egy biztonsági másolatot a SAP HANA-adatbázis helyreállításához.":::

   Az adatbázis helyreállítását követően egy üzenet jelenik meg, amely helyreállt az **idő** , és a rendszer visszaállítja a **napló pozíciójának** bélyegzőjét.

1. A **rendszerek** területen kattintson a jobb gombbal a rendszeradatbázisra, és válassza a **biztonsági mentés és helyreállítás** a  >  **bérlői adatbázis helyreállítása** lehetőséget.
1. A bérlői adatbázis helyreállításának befejezéséhez kövesse a varázsló munkafolyamatát. 

Az adatbázisok visszaállításával kapcsolatos további információkért lásd: [SAP HANA biztonsági mentés/helyreállítás a SnapCenter-](https://www.netapp.com/us/media/tr-4614.pdf)mel.


### <a name="non-database-backups"></a>Adatbázison kívüli biztonsági másolatok
Visszaállíthatja a nem adatköteteket, például a hálózati fájlmegosztás (/Hana/Shared) vagy az operációs rendszer biztonsági másolatát.  A nem adatkötetek visszaállításával kapcsolatos további információkért tekintse meg [SAP HANA biztonsági mentés/helyreállítás a SnapCenter](https://www.netapp.com/us/media/tr-4614.pdf)-mel című témakört.

### <a name="sap-hana-system-cloning"></a>Rendszerklónozás SAP HANA

A klónozás előtt ugyanazzal a HANA-verzióval kell telepíteni a forrás-adatbázist. A SID és az azonosító eltérő lehet. 

:::image type="content" source="media/snapcenter/system-cloning-diagram.png" alt-text="Rendszerklónozás SAP HANA" lightbox="media/snapcenter/system-cloning-diagram.png" border="false":::

1. HANA-adatbázis felhasználói tárolójának létrehozása a H34-adatbázishoz a/usr/sap/H34/HDB40.

   ```
   hdbuserstore set H34KEY sollabsjct34:34013 system manager
   ```
 
1. Tiltsa le a tűzfalat.

   ```bash
   systemctl disable SuSEfirewall2
   systemctl stop  SuSEfirewall2
   ```

1. Telepítse a Java SDK-t.

   ```bash
   zypper in java-1_8_0-openjdk
   ```

1. A SnapCenter adja hozzá a cél gazdagépet, amelyen a klón csatlakoztatva lesz. További információ: [gazdagépek hozzáadása és beépülőmodul-csomagok telepítése távoli gazdagépeken](http://docs.netapp.com/ocsc-43/index.jsp?topic=%2Fcom.netapp.doc.ocsc-dpg-sap-hana%2FGUID-246C0810-4F0B-4BF7-9A35-B729AD69954A.html).
   1. Adja meg a hozzáadni kívánt futtató hitelesítő adatokkal kapcsolatos információkat. 
   1. Válassza ki a gazdagép operációs rendszerét, és adja meg a gazdagép adatait.
   1. A **telepítendő beépülő modulok** alatt válassza ki a verziót, adja meg a telepítési útvonalat, és válassza a **SAP HANA** lehetőséget.
   1. A telepítés előtti ellenőrzések futtatásához válassza az **Érvényesítés** lehetőséget.

1. Állítsa le a HANA-t, és válassza le a régi adatmennyiséget.  A klónt a SnapCenter fogja csatlakoztatni.  

   ```bash
   sapcontrol -nr 40 -function StopSystem
   umount /hana/data/H34/mnt00001

   ```
 1. Hozza létre a cél konfigurációs és rendszerhéji parancsfájl-fájljait.
 
    ```bash
    mkdir /NetApp
    chmod 777 /NetApp
    cd NetApp
    chmod 777 sc-system-refresh-H34.cfg
    chmod 777 sc-system-refresh.sh

    ```

    >[!TIP]
    >A szkripteket átmásolhatja az [SAP-klónozásból a SnapCenter-ből](https://www.netapp.com/us/media/tr-4667.pdf).

1. Módosítsa a konfigurációs fájlt. 

   ```bash
   vi sc-system-refresh-H34.cfg
   ```

   * HANA_ARCHITECTURE = "MDC_single_tenant"
   * KULCS = "H34KEY"
   * TIME_OUT_START = 18
   * TIME_OUT_STOP = 18
   * INSTANCENO = "40"
   * STORAGE = "10.250.101.33"

1. Módosítsa a rendszerhéj parancsfájlját.

   ```bash
   vi sc-system-refresh.sh
   ```  

   * VERBOSE = nem
   * MY_NAME = " `basename $0` "
   * BASE_SCRIPT_DIR = " `dirname $0` "
   * MOUNT_OPTIONS = "RW, vers = 4, Hard, Timeo = 600, rsize = 1048576, wsize = 1048576, intr, noatime, delock"

1. Indítsa el a klónt egy biztonsági mentési folyamatból. Válassza ki a gazdagépet a klón létrehozásához. 

   >[!NOTE]
   >További információ: [klónozás biztonsági mentésből](https://docs.netapp.com/ocsc-43/index.jsp?topic=%2Fcom.netapp.doc.ocsc-dpg-cpi%2FGUID-F6E7FF73-0183-4B9F-8156-8D7DA17A8555.html).

1. A **parancsfájlok** területen adja meg a következőket:

   * **Csatlakoztatási parancs:** /NetApp/SC-System-refresh.sh csatlakoztatási H34% hana_data_h31_mnt00001_t250_vol_Clone
   * **Klónozás utáni parancs:** /NetApp/SC-System-refresh.sh Recover H34

1. Tiltsa le (zárolja) az automatikus csatlakoztatást az/etc/fstab-ben, mivel az előre telepített adatbázis adatmennyisége nem szükséges. 

   ```bash
   vi /etc/fstab
   ```

### <a name="delete-a-clone"></a>Klón törlése

Ha már nincs rá szükség, törölheti a klónozást. További információ: [klónozások törlése](https://docs.netapp.com/ocsc-43/index.jsp?topic=%2Fcom.netapp.doc.ocsc-dpg-cpi%2FGUID-F6E7FF73-0183-4B9F-8156-8D7DA17A8555.html).

A klónozott törlés előtti végrehajtáshoz használt parancsok a következők:
* **Klónozás előtti törlés:** /NetApp/SC-System-refresh.sh leállítása H34
* **Leválasztás:** /NetApp/SC-System-refresh.sh umount H34

Ezek a parancsok lehetővé teszik a SnapCenter számára az adatbázis leterítését, a kötet leválasztását és az fstab-bejegyzés törlését.  Ezt követően a FlexClone törölve lett. 

### <a name="cloning-database-logfile"></a>Klónozási adatbázis naplófájlja

```   
20190502025323###sollabsjct34###sc-system-refresh.sh: Adding entry in /etc/fstab.
20190502025323###sollabsjct34###sc-system-refresh.sh: 10.250.101.31:/Sc21186309-ee57-41a3-8584-8210297f791d /hana/data/H34/mnt00001 nfs rw,vers=4,hard,timeo=600,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
20190502025323###sollabsjct34###sc-system-refresh.sh: Mounting data volume.
20190502025323###sollabsjct34###sc-system-refresh.sh: mount /hana/data/H34/mnt00001
20190502025323###sollabsjct34###sc-system-refresh.sh: Data volume mounted successfully.
20190502025323###sollabsjct34###sc-system-refresh.sh: chown -R h34adm:sapsys /hana/data/H34/mnt00001
20190502025333###sollabsjct34###sc-system-refresh.sh: Recover system database.
20190502025333###sollabsjct34###sc-system-refresh.sh: /usr/sap/H34/HDB40/exe/Python/bin/python /usr/sap/H34/HDB40/exe/python_support/recoverSys.py --command "RECOVER DATA USING SNAPSHOT CLEAR LOG"
[140278542735104, 0.005] >> starting recoverSys (at Thu May  2 02:53:33 2019)
[140278542735104, 0.005] args: ()
[140278542735104, 0.005] keys: {'command': 'RECOVER DATA USING SNAPSHOT CLEAR LOG'}
recoverSys started: ============2019-05-02 02:53:33 ============
testing master: sollabsjct34
sollabsjct34 is master
shutdown database, timeout is 120
stop system
stop system: sollabsjct34
stopping system: 2019-05-02 02:53:33
stopped system: 2019-05-02 02:53:33
creating file recoverInstance.sql
restart database
restart master nameserver: 2019-05-02 02:53:38
start system: sollabsjct34
2019-05-02T02:53:59-07:00  P010976      16a77f6c8a2 INFO    RECOVERY state of service: nameserver, sollabsjct34:34001, volume: 1, RecoveryPrepared
recoverSys finished successfully: 2019-05-02 02:54:00
[140278542735104, 26.490] 0
[140278542735104, 26.490] << ending recoverSys, rc = 0 (RC_TEST_OK), after 26.485 secs
20190502025400###sollabsjct34###sc-system-refresh.sh: Wait until SAP HANA database is started ....
20190502025400###sollabsjct34###sc-system-refresh.sh: Status:  YELLOW
20190502025410###sollabsjct34###sc-system-refresh.sh: Status:  YELLOW
20190502025420###sollabsjct34###sc-system-refresh.sh: Status:  YELLOW
20190502025430###sollabsjct34###sc-system-refresh.sh: Status:  YELLOW
20190502025440###sollabsjct34###sc-system-refresh.sh: Status:  YELLOW
20190502025451###sollabsjct34###sc-system-refresh.sh: Status:  GREEN
20190502025451###sollabsjct34###sc-system-refresh.sh: SAP HANA database is started.
20190502025451###sollabsjct34###sc-system-refresh.sh: Recover tenant database H34.
20190502025451###sollabsjct34###sc-system-refresh.sh: /usr/sap/H34/SYS/exe/hdb/hdbsql -U H34KEY RECOVER DATA FOR H34 USING SNAPSHOT CLEAR LOG
0 rows affected (overall time 69.584135 sec; server time 69.582835 sec)
20190502025600###sollabsjct34###sc-system-refresh.sh: Checking availability of Indexserver for tenant H34.
20190502025601###sollabsjct34###sc-system-refresh.sh: Recovery of tenant database H34 succesfully finished.
20190502025601###sollabsjct34###sc-system-refresh.sh: Status: GREEN
Deleting the DB Clone – Logfile
20190502030312###sollabsjct34###sc-system-refresh.sh: Stopping HANA database.
20190502030312###sollabsjct34###sc-system-refresh.sh: sapcontrol -nr 40 -function StopSystem HDB

02.05.2019 03:03:12
StopSystem
OK
20190502030312###sollabsjct34###sc-system-refresh.sh: Wait until SAP HANA database is stopped ....
20190502030312###sollabsjct34###sc-system-refresh.sh: Status:  GREEN
20190502030322###sollabsjct34###sc-system-refresh.sh: Status:  GREEN
20190502030332###sollabsjct34###sc-system-refresh.sh: Status:  GREEN
20190502030342###sollabsjct34###sc-system-refresh.sh: Status:  GRAY
20190502030342###sollabsjct34###sc-system-refresh.sh: SAP HANA database is stopped.
20190502030347###sollabsjct34###sc-system-refresh.sh: Unmounting data volume.
20190502030347###sollabsjct34###sc-system-refresh.sh: Junction path: Sc21186309-ee57-41a3-8584-8210297f791d
20190502030347###sollabsjct34###sc-system-refresh.sh: umount /hana/data/H34/mnt00001
20190502030347###sollabsjct34###sc-system-refresh.sh: Deleting /etc/fstab entry.
20190502030347###sollabsjct34###sc-system-refresh.sh: Data volume unmounted successfully.

```

### <a name="uninstall-snapcenter-plug-ins-package-for-linux"></a>A Linux rendszerhez készült SnapCenter beépülő moduljainak eltávolítása

A Linux beépülőmodul-csomagot a parancssorból távolíthatja el. Mivel az automatikus központi telepítés egy új rendszerre vár, egyszerűen eltávolíthatja a beépülő modult.  

>[!NOTE]
>Előfordulhat, hogy manuálisan kell eltávolítania a beépülő modul egy régebbi verzióját. 

Távolítsa el a beépülő modulokat.

```bash
cd /opt/NetApp/snapcenter/spl/installation/plugins
./uninstall
```

Most már telepítheti a legújabb HANA beépülő modult az új csomóponton a **Submit (Küldés** ) SnapCenter lehetőség választásával. 




## <a name="next-steps"></a>Következő lépések
- Lásd: vész [-helyreállítási alapelvek és előkészítés](hana-concept-preparation.md).
