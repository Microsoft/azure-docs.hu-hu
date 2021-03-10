---
title: Migrálás Azure-fájlmegosztásokba
description: Ismerje meg az Azure-fájlmegosztás áttelepítését, és keresse meg az áttelepítési útmutatót.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 3/18/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 27056f39885949d52c9fcc0d1472033cfc8f9aa0
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102554870"
---
# <a name="migrate-to-azure-file-shares"></a>Migrálás Azure-fájlmegosztásokba

Ez a cikk az Azure-fájlmegosztás áttelepítésének alapvető szempontjait ismerteti.

Ez a cikk áttelepítési alapismereteket és az áttelepítési útmutatók táblázatát tartalmazza. Ezek az útmutatók segítenek a fájlok Azure-fájlmegosztásba való áthelyezésében. Az útmutatók az Ön adatai alapján vannak rendszerezve, és milyen üzembe helyezési modellt (csak felhőalapú vagy hibrid) kell áthelyezni.

## <a name="migration-basics"></a>Áttelepítési alapok

Az Azure több elérhető felhőalapú tárhelyet tartalmaz. Az Azure-ba történő Migrálás alapvető aspektusa annak meghatározása, hogy az Azure Storage melyik lehetőség van az adataihoz.

Az [Azure-fájlmegosztás](storage-files-introduction.md) alkalmas az általános célú fájlok adataira. Ez az adat magában foglalja a helyszíni SMB-vagy NFS-megosztások használatát. A [Azure file Sync](storage-sync-files-planning.md)használatával több Azure-fájlmegosztás tartalmát is gyorsítótárazhatja a helyszíni Windows Servert futtató kiszolgálókon.

Egy olyan alkalmazás esetében, amely jelenleg egy helyszíni kiszolgálón fut, a fájlok Azure-fájlmegosztás alapján történő tárolása jó választás lehet. Az alkalmazást áthelyezheti az Azure-ba, és megosztott tárolóként használhatja az Azure-fájlmegosztást. Ebben a forgatókönyvben az [Azure-lemezeket](../../virtual-machines/managed-disks-overview.md) is fontolóra veheti.

Egyes felhőalapú alkalmazások nem függnek az SMB-től, illetve a számítógép helyi adathozzáférésével vagy közös hozzáférésével. Ezekhez az alkalmazásokhoz, például az [Azure-blobokhoz](../blobs/storage-blobs-overview.md) , gyakran a legjobb választás.

A Migrálás kulcsa a fájloknak az aktuális tárolási helyről az Azure-ba való áthelyezéséhez szükséges összes fájl-hűség rögzítése. Az Azure Storage beállításának megbízhatósága, valamint a forgatókönyvhöz szükséges, hogy a megfelelő Azure Storage-t is kiválaszthatja. Az általános célú Fájlinformációk hagyományosan a fájl metaadataitól függenek. Előfordulhat, hogy az alkalmazásadatok nem.

Íme a fájl két alapvető összetevője:

- **Adatfolyam: egy** fájl adatfolyama tárolja a fájl tartalmát.
- **Fájl metaadatai**: a fájl metaadatai rendelkeznek a következő alösszetevőkkel:
   * Fájlattribútumok, például írásvédett
   * Fájlengedélyek, amelyek *NTFS-engedélyekként* , illetve *fájl-és mappa-ACL* -ként hívhatók.
   * Időbélyegek, a legtöbb esetben a létrehozás és az utolsó módosítás időbélyege
   * Egy alternatív adatfolyam, amely a nem szabványos tulajdonságok nagyobb mennyiségének tárolására szolgáló terület

Az áttelepítés során a fájlok hűségét a következőkre lehet definiálni:

- A forrás összes vonatkozó fájljának információit tárolja.
- Fájlok átvitele az áttelepítési eszközzel.
- Tárolja a fájlokat az áttelepítés cél tárolójában.

Az áttelepítés zökkenőmentes működésének biztosítása érdekében azonosítsa [a legmegfelelőbb másolási eszközt az igényeinek megfelelően](#migration-toolbox) , és egyezzen a forrás tárolási céljával.

Az előző információk figyelembe vétele után láthatja, hogy az Azure-beli általános célú fájlok célként megadott tárterülete [Azure-fájlmegosztás](storage-files-introduction.md).

Az Azure-blobokban lévő Object Storage szolgáltatástól eltérően az Azure-fájlmegosztás natív módon képes tárolni a fájl metaadatait. Az Azure-fájlmegosztás emellett megőrzi a fájl-és mappa-hierarchiát, az attribútumokat és az engedélyeket. Az NTFS-engedélyek fájlok és mappák tárolására használhatók, mert azok a helyszínen vannak.

Active Directory, amely a helyszíni tartományvezérlő, natív módon hozzáférhet egy Azure-fájlmegosztást. Így Azure Active Directory Domain Services (Azure AD DS) felhasználója lehet. Mindegyik a jelenlegi identitását használja a megosztási engedélyek és a fájl-és mappa ACL-ek alapján való hozzáféréshez. Ez a viselkedés hasonló a helyszíni fájlmegosztást összekötő felhasználóhoz.

Az alternatív adatfolyam a fájl hűségének elsődleges eleme, amely jelenleg nem tárolható Azure-fájlmegosztás fájljain. Azure File Sync használatakor a rendszer megőrzi a helyszínen.

További információ az Azure [ad-hitelesítésről](storage-files-identity-auth-active-directory-enable.md) és az azure-AD DS Azure-fájlmegosztás [hitelesítéséről](storage-files-identity-auth-active-directory-domain-service-enable.md) .

## <a name="migration-guides"></a>Migrálási útmutatók

A következő táblázat felsorolja a részletes áttelepítési útmutatókat.

A tábla használata:

1. Keresse meg a forrásrendszer azon sorát, amelyben a fájlok jelenleg vannak tárolva.

1. Válasszon egyet a következő célok közül:

   - Hibrid üzembe helyezés a Azure File Sync használatával a helyszíni Azure-fájlmegosztás tartalmának gyorsítótárazásához
   - Azure-fájlmegosztás a felhőben

   Válassza ki az Ön által választott cél oszlopot.

1. A forrás és a cél szakaszán belül egy táblázatcella listázza az elérhető áttelepítési forgatókönyveket. Válasszon egyet a részletes áttelepítési útmutatóhoz való közvetlen hivatkozáshoz.

A hivatkozás nélküli forgatókönyv még nem rendelkezik közzétett áttelepítési útmutatóval. A táblázatot időnként érdemes megkeresni a frissítésekhez. Az új útmutatók közzé lesznek téve, ha elérhetők.

| Forrás | Cél: </br>Hibrid üzembe helyezés | Cél: </br>Csak felhőalapú központi telepítés |
|:---|:--|:--|
| | Eszköz kombinációja:| Eszköz kombinációja: |
| Windows Server 2012 R2 és újabb verziók | <ul><li>[Azure File Sync](storage-sync-files-deployment-guide.md)</li><li>[Azure File Sync és az Azure DataBox](storage-sync-offline-data-transfer.md)</li></ul> | <ul><li>A RoboCopy segítségével egy csatlakoztatott Azure-fájlmegosztás</li><li>Azure File Sync keresztül</li></ul> |
| Windows Server 2012 és korábbi verziók | <ul><li>A DataBox és a Azure File Sync a legutóbbi kiszolgálói operációs rendszerre</li><li>Tárterület-áttelepítési szolgáltatás és a legutóbbi kiszolgáló között Azure File Sync, majd feltöltés</li></ul> | <ul><li>A Storage áttelepítési szolgáltatás és a legutóbbi kiszolgáló között Azure File Sync</li><li>A RoboCopy segítségével egy csatlakoztatott Azure-fájlmegosztás</li></ul> |
| Hálózatra csatlakoztatott tároló (NAS) | <ul><li>[Azure File Sync feltöltéssel](storage-files-migration-nas-hybrid.md)</li><li>[DataBox + Azure File Sync](storage-files-migration-nas-hybrid-databox.md)</li></ul> | <ul><li>A RoboCopy segítségével egy csatlakoztatott Azure-fájlmegosztás</li></ul> |
| Linux/Samba | <ul><li>[Azure File Sync és RoboCopy](storage-files-migration-linux-hybrid.md)</li></ul> | <ul><li>A RoboCopy segítségével egy csatlakoztatott Azure-fájlmegosztás</li></ul> |
| Microsoft Azure StorSimple Cloud Appliance 8100 vagy StorSimple Cloud Appliance 8600 | <ul><li>[Dedikált adatáttelepítési felhőalapú szolgáltatás](storage-files-migration-storsimple-8000.md)</li></ul> | |
| StorSimple Cloud Appliance 1200 | <ul><li>[Azure File Sync keresztül](storage-files-migration-storsimple-1200.md)</li></ul> | |

## <a name="migration-toolbox"></a>Áttelepítési eszközkészlet

### <a name="file-copy-tools"></a>Fájlmásolás eszközei

Több fájlmásolás-eszköz is elérhető a Microsofttól és másoktól. Az áttelepítési forgatókönyvhöz megfelelő eszköz kiválasztásához az alábbi alapvető kérdéseket kell figyelembe vennie:

* Támogatja az eszköz a fájlmásolás forrás-és célhelyét?

* Támogatja az eszköz a hálózati elérési utat vagy az elérhető protokollokat (például a REST, az SMB vagy az NFS) a forrás-és a cél tárolási helyei között?

* Megőrzi az eszköz a forrás-és a célhelyek által támogatott fájl-hűséget?

    Bizonyos esetekben a cél tárterülete nem támogatja ugyanazt a hűséget, mint a forrás. Ha a cél tárterület elegendő az igényeinek kielégítéséhez, az eszköznek csak a cél fájl-megbízhatósági képességeinek kell megegyeznie.

* Az eszköz rendelkezik olyan funkciókkal, amelyek illeszkednek az áttelepítési stratégiába?

    Tegyük fel például, hogy az eszköz lehetővé teszi, hogy csökkentse az állásidőt.
    
    Ha egy eszköz támogatja a forrás egy célhoz való tükrözésének lehetőségét, gyakran többször is futtathatja ugyanazt a forrást és célt, amíg a forrás elérhető marad.

    Amikor első alkalommal futtatja az eszközt, az átmásolja az adatmennyiséget. Ez a kezdeti Futtatás akár egy darabig is tarthat. Gyakran hosszabb ideig tart, mint amennyire szeretné, hogy az adatforrást offline állapotba vegyék az üzleti folyamatok számára.

    Ha egy forrást egy célhelyre tükröz (a **Robocopy/Mir** hasonlóan), az eszközt ugyanezen a forráson és a célon is futtathatja. A Futtatás sokkal gyorsabb, mert csak az előző Futtatás után megjelenő források módosításait kell továbbítania. A másolási eszköz újbóli futtatása így jelentősen csökkentheti az állásidőt.

Az alábbi táblázat a Microsoft-eszközöket és az Azure-fájlmegosztás jelenlegi alkalmazhatóságát sorolja fel:

| Ajánlott | Eszköz | Azure-fájlmegosztás támogatása | A fájl hűségének megőrzése |
| :-: | :-- | :---- | :---- |
|![Igen, ajánlott](media/storage-files-migration-overview/circle-green-checkmark.png)| RoboCopy | Támogatott. Az Azure-fájlmegosztás hálózati meghajtóként is csatlakoztatható. | Teljes hűség. * |
|![Igen, ajánlott](media/storage-files-migration-overview/circle-green-checkmark.png)| Azure File Sync | Natívan integrálva van az Azure-fájlmegosztásba. | Teljes hűség. * |
|![Igen, ajánlott](media/storage-files-migration-overview/circle-green-checkmark.png)| Storage áttelepítési szolgáltatás | Közvetetten támogatott. Az Azure-fájlmegosztás hálózati meghajtóként is csatlakoztatható SMS-célkiszolgálón. | Teljes hűség. * |
|![Igen, ajánlott](media/storage-files-migration-overview/circle-green-checkmark.png)| AzCopy </br>10,6-es verzió | Támogatott. | Nem támogatja a forrás legfelső szintű ACL másolatát, ellenkező esetben teljes hűséget. * </br>[Ismerje meg, hogyan használhatja a AzCopy-t az Azure-fájlmegosztás használatával](../common/storage-use-azcopy-files.md) |
|![Igen, ajánlott](media/storage-files-migration-overview/circle-green-checkmark.png)| Data Box | Támogatott. | A DataBox teljes mértékben támogatja a metaadatokat. |
|![Nem teljes mértékben ajánlott](media/storage-files-migration-overview/triangle-yellow-exclamation.png)| Azure Storage Explorer </br>1,14-es verzió | Támogatott. | Nem másolja át az ACL-eket. Az időbélyegeket támogatja.  |
|![Nem ajánlott](media/storage-files-migration-overview/circle-red-x.png)| Azure Data Factory | Támogatott. | Nem másolja a metaadatokat. |
|||||

*\* Teljes hűség: megfelel az Azure-fájlmegosztás képességeinek, vagy meghaladja azokat.*

### <a name="migration-helper-tools"></a>Áttelepítési segítő eszközök

Ez a szakasz azokat az eszközöket ismerteti, amelyek segítségével megtervezheti és futtathatja az áttelepítést.

#### <a name="robocopy-from-microsoft-corporation"></a>RoboCopy a Microsoft Corporation-től

A RoboCopy a fájlok áttelepítésére leginkább alkalmazható eszközök egyike. A Windows részét képezi. A [Robocopy fő dokumentációja](/windows-server/administration/windows-commands/robocopy) az eszköz számos lehetőségének hasznos forrása.

#### <a name="treesize-from-jam-software-gmbh"></a>TreeSize a JAM Software GmbH-ból

A Azure File Sync elsősorban az elemek (fájlok és mappák) számával, és nem a teljes tárterület mennyiségével méretezi a méretezést. A TreeSize eszköz segítségével meghatározhatja a Windows Server-köteteken található elemek számát.

Az eszköz használatával perspektívát hozhat létre [Azure file Sync központi telepítés](storage-sync-files-deployment-guide.md)előtt. Azt is megteheti, hogy az üzembe helyezés után is felveszi a Felhőbeli rétegek használatát. Ebben a forgatókönyvben láthatja az elemek számát, és azt, hogy mely könyvtárak használják a kiszolgáló gyorsítótárát.

Az eszköz tesztelt verziója a 4.4.1-es verzió. Kompatibilis a Felhőbeli rétegbeli fájlokkal. Az eszköz a normál működés közben nem idézi elő a rétegekből származó fájlok felidézését.

## <a name="next-steps"></a>Következő lépések

1. Hozzon létre egy tervet, amely az Azure-fájlmegosztás (csak felhőalapú vagy hibrid) telepítését kívánja használni.
1. Tekintse át az elérhető áttelepítési útmutatók listáját, és keresse meg az Azure-fájlmegosztás forrására és üzembe helyezésére vonatkozó részletes útmutatót.

További információ a cikkben említett Azure Files technológiákról:

* [Az Azure file share áttekintése](storage-files-introduction.md)
* [Az Azure File Sync üzembe helyezésének megtervezése](storage-sync-files-planning.md)
* [Azure File Sync: felhőalapú rétegek](storage-sync-cloud-tiering-overview.md)