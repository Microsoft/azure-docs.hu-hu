---
title: Migrálás Azure-fájlmegosztásokba
description: Ismerje meg az Azure-fájlmegosztások migrálását, és keresse meg a migrálási útmutatót.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 3/18/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: a6335d90625f860984ccbfd224955a97a32b731f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785216"
---
# <a name="migrate-to-azure-file-shares"></a>Migrálás Azure-fájlmegosztásokba

Ez a cikk az Azure-fájlmegosztásokbe való migrálás alapvető aspektusait tartalmazza.

Ez a cikk a migrálás alapjait és a migrálási útmutatók táblázatát tartalmazza. Ezek az útmutatók segítenek a fájlok Azure-fájlmegosztásba való áthelyezésében. Az útmutatók az adatok helyének és az üzembe helyezési modellnek (csak felhőalapú vagy hibrid) alapján vannak rendezve.

## <a name="migration-basics"></a>A migrálás alapjai

Az Azure többféle felhőalapú tárolótípussal is rendelkezik. Az Azure-ba való fájláttelepítés egyik alapvető eleme annak meghatározása, hogy melyik Azure Storage-lehetőség megfelelő az adatokhoz.

[Az Azure-fájlmegosztások](storage-files-introduction.md) általános célú fájladatokhoz alkalmasak. Ezek az adatok mindent tartalmaznak, amihez helyszíni SMB- vagy NFS-megosztást használ. A [Azure File Sync](../file-sync/file-sync-planning.md)segítségével számos Azure-fájlmegosztás tartalmát gyorsítótárazhatja a helyszíni Windows Servert futtató kiszolgálókon.

A jelenleg helyszíni kiszolgálón futó alkalmazásokhoz érdemes azure-fájlmegosztásban tárolni a fájlokat. Áthelyezheti az alkalmazást az Azure-ba, és megosztott tárolóként használhatja az Azure-fájlmegosztásokat. Ebben a forgatókönyvben [az Azure Diskset](../../virtual-machines/managed-disks-overview.md) is érdemes megfontolni.

Egyes felhőalkalmazások nem függnek az SMB-től, a gép helyi adateléréstől vagy a megosztott hozzáféréstől. Ezekhez az alkalmazásokhoz gyakran az objektumtárolás, például az [Azure-blobok](../blobs/storage-blobs-overview.md) a legjobb választás.

Minden migrálás kulcsa az összes megfelelő fájlhűség rögzítése, amikor a fájlokat az aktuális tárolási helyről az Azure-ba mozgatja. Az Azure Storage-beállítás által támogatott, valamint a forgatókönyvhöz szükséges funkciók nagysága abban is segít, hogy a megfelelő Azure-tárolót válassza ki. Az általános célú fájladatok hagyományosan a fájl metaadataitól függnek. Előfordulhat, hogy az alkalmazásadatok nem.

A fájlok két alapvető összetevője a következő:

- **Adatfolyam:** A fájl adatfolyama tárolja a fájl tartalmát.
- **Fájl metaadatai:** A fájl metaadatai a következő alkomponensekkel adatokat tartalmaznak:
   * Fájlattribútumok, például csak olvasható
   * Fájlengedélyek, más néven *NTFS-engedélyek* vagy fájl- és *mappa ACL-ek*
   * Időbélyegek, különösen a létrehozás és az utolsó módosítás időbélyege
   * Alternatív adatfolyam, amely nagyobb mennyiségű nem szabványos tulajdonság tárolására való hely

A migrálásban a fájlhűség a következő képességként határozható meg:

- Tárolja az összes vonatkozó fájlinformációt a forráson.
- Fájlok átvitele a migrálási eszközzel.
- Tárolja a fájlokat az áttelepítés céltárolójában.

Annak érdekében, hogy a migrálás zökkenőmentesen folytatódjon, azonosítsa az igényeinek leginkább megfelelő másolási eszközt, és egy tárolási célt a forráshoz. [](#migration-toolbox)

Az előző információkat figyelembe véve láthatja, hogy az Azure általános célú fájljainak céltárolója az [Azure-fájlmegosztások.](storage-files-introduction.md)

Az Azure-blobok objektumtárolóitól eltérően az Azure-fájlmegosztások natív módon tárolnak fájlmetaadatokat. Az Azure-fájlmegosztások megőrzik a fájl- és mappahierarchiát, az attribútumokat és az engedélyeket is. Az NTFS-engedélyek tárolhatók fájlokon és mappákon, mert azok a helyszínen találhatók.

A virtuális Active Directory, amely a helyszíni tartományvezérlője, natív módon hozzáférhet egy Azure-fájlmegosztáshoz. A felhasználók is így Azure Active Directory Domain Services (Azure AD DS). Mindegyik a jelenlegi identitását használja a hozzáféréshez a megosztási engedélyek, valamint a fájl- és mappa ACL-ek alapján. Ez a viselkedés hasonló a helyszíni fájlmegosztáshoz csatlakozó felhasználókhoz.

Az alternatív adatstream a fájlhűség elsődleges aspektusa, amely jelenleg nem tárolható egy Azure-fájlmegosztásban lévő fájlban. A helyszíni környezetben marad, ha Azure File Sync használja.

További információ az [Azure AD-hitelesítésről](storage-files-identity-auth-active-directory-enable.md) és Azure AD DS Azure-fájlmegosztások hitelesítésről. [](storage-files-identity-auth-active-directory-domain-service-enable.md)

## <a name="migration-guides"></a>Migrálási útmutatók

Az alábbi táblázat részletes áttelepítési útmutatókat tartalmaz.

A tábla használata:

1. Keresse meg annak a forrásrendszernek a sorát, amely a fájlokat jelenleg tárolja.

1. Válasszon egyet a következő célok közül:

   - Hibrid üzembe helyezés Azure File Sync a helyszíni Azure-fájlmegosztások tartalmának gyorsítótárazása érdekében
   - Azure-fájlmegosztások a felhőben

   Válassza ki a választott céloszlopot.

1. A forrás és a cél metszetében egy táblázatcella felsorolja az elérhető migrálási forgatókönyveket. Válassza ki az egyiket a részletes migrálási útmutatóra mutató közvetlen hivatkozáshoz.

A hivatkozás nélküli forgatókönyvekhez még nincs közzétett migrálási útmutató. Időnként ellenőrizze ezt a táblázatot frissítésekért. Az új útmutatók akkor lesznek közzétéve, amikor elérhetők.

| Forrás | Cél: </br>Hibrid üzembe helyezés | Cél: </br>Csak felhőalapú üzembe helyezés |
|:---|:--|:--|
| | Eszközkombináció:| Eszközkombináció: |
| Windows Server 2012 R2 és újabb verziók | <ul><li>[Azure File Sync](../file-sync/file-sync-deployment-guide.md)</li><li>[Azure File Sync and Azure DataBox](../file-sync/file-sync-offline-data-transfer.md)</li></ul> | <ul><li>RoboCopyval csatlakoztatott Azure-fájlmegosztáshoz</li><li>A Azure File Sync</li></ul> |
| Windows Server 2012 és korábbi verziók | <ul><li>DataBoxon keresztül és Azure File Sync a legutóbbi kiszolgáló operációs rendszeréhez</li><li>A Storage Migration Service-en keresztül a legutóbbi kiszolgálóra a Azure File Sync, majd feltöltés</li></ul> | <ul><li>A Storage Migration Service-en keresztül a legutóbbi kiszolgálóra a Azure File Sync</li><li>RoboCopyval csatlakoztatott Azure-fájlmegosztáshoz</li></ul> |
| Hálózati tároló (NAS) | <ul><li>[Feltöltés Azure File Sync keresztül](storage-files-migration-nas-hybrid.md)</li><li>[A DataBox + Azure File Sync](storage-files-migration-nas-hybrid-databox.md)</li></ul> | <ul><li>[A DataBoxon keresztül](storage-files-migration-nas-cloud-databox.md)</li><li>RoboCopyval csatlakoztatott Azure-fájlmegosztáshoz</li></ul> |
| Linux / Samba | <ul><li>[Azure File Sync és RoboCopy](storage-files-migration-linux-hybrid.md)</li></ul> | <ul><li>RoboCopyval csatlakoztatott Azure-fájlmegosztáshoz</li></ul> |
| Microsoft Azure StorSimple Cloud Appliance 8100 vagy StorSimple Cloud Appliance 8600 | <ul><li>[Dedikált adatáttelepítési felhőszolgáltatáson keresztül](storage-files-migration-storsimple-8000.md)</li></ul> | |
| StorSimple Cloud Appliance 1200 | <ul><li>[A Azure File Sync](storage-files-migration-storsimple-1200.md)</li></ul> | |

## <a name="migration-toolbox"></a>Migrálási eszközkészlet

### <a name="file-copy-tools"></a>Fájlmásoló eszközök

Számos fájlmásoló eszköz érhető el a Microsofttól és másoktól. A migrálási forgatókönyvhöz megfelelő eszköz kiválasztásához figyelembe kell vennie az alábbi alapvető kérdéseket:

* Támogatja az eszköz a fájlmásoló forrás- és célhelyeit?

* Támogatja az eszköz a hálózati útvonalat vagy az elérhető protokollokat (például REST, SMB vagy NFS) a forrás- és céltárolóhelyek között?

* Megőrzi az eszköz a forrás- és célhelyek által támogatott szükséges fájlhűséget?

    Bizonyos esetekben a céltároló nem támogatja ugyanazt a hűséget, mint a forrás. Ha a céltároló elegendő az Ön igényeinek, az eszköznek csak a cél fájlhűségi képességeivel kell megegyeznie.

* Rendelkezik az eszköz olyan funkciókkal, amelyek illeszkednek a migrálási stratégiához?

    Gondolja át például, hogy az eszköz lehetővé teszi-e az állásidő minimalizálását.
    
    Ha egy eszköz támogatja a forrás célhoz való tükrözésének beállítását, azt gyakran többször is futtathatja ugyanazon a forráson és célon, amíg a forrás elérhető marad.

    Az eszköz első futtatásakor a rendszer átmásolja az adatok nagy részét. Ez a kezdeti futtatás egy ideig is előfordulhat. Ez gyakran hosszabb ideig tart, mint amit az adatforrásnak az üzleti folyamatokhoz való offline állapotba való váltása kíván.

    A forrás célként való tükrözésével (ahogyan a **robocopy /MIRROR** esetén) újra futtathatja az eszközt ugyanazon a forráson és célon. A futtatás sokkal gyorsabb, mert csak az előző futtatás után végrehajtott forrásváltozásokat kell áthozni. A másolási eszköz ilyen módon való újrafuttatása jelentősen csökkentheti az állásidőt.

Az alábbi táblázat a Microsoft-eszközöket és azok aktuális alkalmasságát mutatja be az Azure-fájlmegosztások esetében:

| Ajánlott | Eszköz | Azure-fájlmegosztások támogatása | A fájlhűség megőrzése |
| :-: | :-- | :---- | :---- |
|![Igen, ajánlott](media/storage-files-migration-overview/circle-green-checkmark.png)| Robocopy | Támogatott. Az Azure-fájlmegosztások hálózati meghajtóként csatlakoztathatóak. | Teljes hűség.* |
|![Igen, ajánlott](media/storage-files-migration-overview/circle-green-checkmark.png)| Azure File Sync | Natív módon integrálva van az Azure-fájlmegosztásokbe. | Teljes hűség.* |
|![Igen, ajánlott](media/storage-files-migration-overview/circle-green-checkmark.png)| Storage Migration Service | Közvetetten támogatott. Az Azure-fájlmegosztások hálózati meghajtóként csatlakoztathatóak az SMS-célkiszolgálókra. | Teljes hűség.* |
|![Igen, ajánlott](media/storage-files-migration-overview/circle-green-checkmark.png)| AzCopy </br>10.6-os verzió | Támogatott. | Nem támogatja a forrás gyökerének ACL-nek a másolását, ellenkező esetben teljes hűséget.* </br>[Megtudhatja, hogyan használhatja az AzCopyt Azure-fájlmegosztásokkal](../common/storage-use-azcopy-files.md) |
|![Igen, ajánlott](media/storage-files-migration-overview/circle-green-checkmark.png)| Data Box | Támogatott. | A DataBox teljes mértékben támogatja a metaadatokat. |
|![Nem ajánlott teljes mértékben](media/storage-files-migration-overview/triangle-yellow-exclamation.png)| Azure Storage Explorer </br>1.14-es verzió | Támogatott. | Nem másolja az ACL-eket. Támogatja az időbélyegeket.  |
|![Nem ajánlott](media/storage-files-migration-overview/circle-red-x.png)| Azure Data Factory | Támogatott. | Nem másol metaadatokat. |
|||||

*\* Teljes hűség: teljesíti vagy meghaladja az Azure-fájlmegosztási képességeket.*

### <a name="migration-helper-tools"></a>Migrálási segítő eszközök

Ez a szakasz a migrálások tervezését és futtatását segítő eszközöket ismerteti.

#### <a name="robocopy-from-microsoft-corporation"></a>RoboCopy a Microsoft Corporation

A RoboCopy a fájláttelepítésre leginkább alkalmazható eszközök egyike. Ez a Windows része. A [RoboCopy fő dokumentációja](/windows-server/administration/windows-commands/robocopy) hasznos forrás az eszköz számos beállításához.

#### <a name="treesize-from-jam-software-gmbh"></a>TreeSize from JAM Software GmbH

Azure File Sync elsősorban az elemek (fájlok és mappák) számával skáláz, nem pedig a teljes tárterület mennyiséggel. A TreeSize eszközzel meghatározhatja a Windows Server-kötetek elemeinek számát.

Az eszközzel perspektívát hozhat létre a központi [telepítés Azure File Sync előtt.](../file-sync/file-sync-deployment-guide.md) Akkor is használhatja, ha a felhőbeli rétegezés az üzembe helyezés után történik. Ebben a forgatókönyvben láthatja az elemek számát, és hogy mely könyvtárak használják a legtöbbet a kiszolgáló gyorsítótárát.

Az eszköz tesztelt verziója 4.4.1-es. Kompatibilis a felhőbeli rétegzett fájlokkal. Az eszköz normál működése során nem idéz elő rétegzett fájlokat.

## <a name="next-steps"></a>Következő lépések

1. Hozzon létre egy tervet, amelyhez üzembe szeretné hozni az Azure-fájlmegosztásokat (csak felhőalapú vagy hibrid).
1. Tekintse át az elérhető migrálási útmutatók listáját, és keresse meg az Azure-fájlmegosztások forrásának és üzembe helyezésének megfelelő részletes útmutatót.

További információ a cikkben említett Azure Files technológiákról:

* [Az Azure-fájlmegosztások áttekintése](storage-files-introduction.md)
* [Az Azure File Sync üzembe helyezésének megtervezése](../file-sync/file-sync-planning.md)
* [Azure File Sync: Felhőbeli rétegezés](../file-sync/file-sync-cloud-tiering-overview.md)