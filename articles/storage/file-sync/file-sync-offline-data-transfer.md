---
title: Adatok áttelepítése Azure File Sync Azure Data Box
description: Az adatokkal kompatibilis tömeges adatokat offline is Azure File Sync. Kerülje a fájlütközéseket, és a szinkronizálás engedélyezése után őrizze meg a fájl- és mappa ACL-eket és időbélyegeket.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 453a6f9c83b992df62681f366fcf95a77cda9f25
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796843"
---
# <a name="migrate-bulk-data-to-azure-file-sync-with-azure-databox"></a>Tömeges adatmigrálás az Azure File Syncbe az Azure Data Boxszal
A tömeges adatokat kétféleképpen Azure File Sync át:

* **Töltse fel a fájlokat a Azure File Sync.** Ez a legegyszerűbb módszer. Helyezze át a fájlokat helyileg a Windows Server 2012 R2 vagy újabb verzióra, és telepítse a Azure File Sync ügynököt. A szinkronizálás beállítása után a fájlok fel lesznek töltve a kiszolgálóról. (Ügyfeleink jelenleg körülbelül két naponta átlagosan 1 TiB feltöltési sebességgel rendelkeznek.) Annak érdekében, hogy a kiszolgáló ne használja túl nagy sávszélességet az adatközpont számára, érdemes lehet sávszélesség-szabályozási [ütemezést beállítani.](file-sync-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter)
* **Fájlok offline átvitele.** Ha nincs elég sávszélessége, előfordulhat, hogy nem tud ésszerű időn belül fájlokat feltölteni az Azure-ba. A kihívás a teljes fájlkészlet kezdeti szinkronizálása. Ennek a kihívásnak a leküzdéséhez használjon offline tömeges migrálási eszközöket, például a [Azure Data Box család.](https://azure.microsoft.com/services/storage/databox) 

Ez a cikk azt ismerteti, hogyan lehet a fájlokat offline, azokkal kompatibilis módon Azure File Sync. Kövesse ezeket az utasításokat a fájlütközések elkerüléséhez, valamint a fájl- és mappa-hozzáférés-vezérlési listák (ACL-ek) és időbélyegek megőrzéséhez a szinkronizálás engedélyezése után.

## <a name="migration-tools"></a>Migrálási eszközök
Az ebben a cikkben ismertetjük folyamat nemcsak a Data Box, hanem más offline migrálási eszközök esetén is. Emellett olyan eszközökkel is működik, mint az AzCopy, a Robocopy vagy a partnereszközök és -szolgáltatások, amelyek közvetlenül az interneten keresztül működnek. A kezdeti feltöltési feladat leküzdéséhez azonban kövesse a cikkben található lépéseket, hogy ezeket az eszközöket olyan módon használja, amely kompatibilis a Azure File Sync.

Bizonyos esetekben át kell lépnie egy Windows Server-kiszolgálóról egy másikra, mielőtt Azure File Sync. [A Storage Migration Service](/windows-server/storage/storage-migration-service/overview) (SMS) segíthet ennek megoldásában. Függetlenül attól, hogy a Azure File Sync által támogatott kiszolgálói operációsrendszer-verzióra kell migrálnia (Windows Server 2012R2 és újabb verziók), vagy csak át kell migrálnia, mivel új rendszert vásárol az Azure File Sync-hoz, az SMS számos olyan funkciót és előnyt kínál, amelyek segítenek zökkenőmentesen eltolni a migrálást.

## <a name="benefits-of-using-a-tool-to-transfer-data-offline"></a>Az eszközök offline adatátvitelre való használatának előnyei
Az offline migráláshoz használt átviteli eszközök, például a Data Box fő előnyei:

- Nem kell feltöltenie az összes fájlt a hálózaton keresztül. Nagy névterek esetén ez az eszköz jelentős hálózati sávszélességet és időt takaríthat meg.
- A Azure File Sync függetlenül attól, hogy melyik átviteli eszközt használja (Data Box, Azure Import/Export szolgáltatás stb.), az élő kiszolgáló csak az adatokat az Azure-ba való áthelyezés után módosító fájlokat tölti fel.
- Azure File Sync akkor is szinkronizálja a fájl- és mappa ACL-eket, ha az offline tömeges migrálási eszköz nem szállít ACL-eket.
- Data Box és Azure File Sync állásidő nélkül. Ha a hálózati Data Box adatokat az Azure-ba, hatékonyan használja a hálózati sávszélességet, és megőrzi a fájlhűséget. A névtér naprakészen tarthatja, ha csak az adatokat az Azure-ba való áthelyezés után módosító fájlokat tölti fel.

## <a name="prerequisites-for-the-offline-data-transfer"></a>Az offline adatátvitel előfeltételei
Az offline adatátvitel befejezése előtt ne engedélyezze a szinkronizálást az áttért kiszolgálón. Mielőtt hozzákezd, az alábbiakat kell figyelembe vennie:

- Ha a tömeges migráláshoz Data Box használni, tekintse át az üzembe helyezés előfeltételeit [a](../../databox/data-box-deploy-ordered.md#prerequisites)Data Box.
- A végső Azure File Sync megterve: Terv a Azure File Sync [üzembe helyezéséhez](file-sync-planning.md)
- Válassza ki azokat az Azure Storage-fiókokat, amelyek a szinkronizálni kívánt fájlmegosztásokat fogják tartani. Győződjön meg arról, hogy a tömeges migrálás az átmeneti átmeneti megosztásokkal történik ugyanabban a tárfiókban vagy tárfiókban. A tömeges migrálás csak olyan végleges és átmeneti megosztással engedélyezhető, amely ugyanabban a tárfiókban található.
- A tömeges migrálás csak akkor használható, ha új szinkronizálási kapcsolatot hoz létre a kiszolgáló helyével. Meglévő szinkronizálási kapcsolattal nem engedélyezhető a tömeges migrálás.


## <a name="process-for-offline-data-transfer"></a>Offline adatátvitel folyamata
A következő módon állíthatja be a Azure File Sync olyan módon, amely kompatibilis az olyan tömeges migrálási eszközökkel, mint a Azure Data Box:

![Az adatbázis beállítását bemutató Azure File Sync](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| Lépés | Részletek |
|---|---------------------------------------------------------------------------------------|
| ![1. lépés](media/storage-sync-files-offline-data-transfer/bullet-1.png) | [Rendelje meg a Data Box.](../../databox/data-box-deploy-ordered.md) A Data Box család [számos terméket kínál az](https://azure.microsoft.com/services/storage/databox/data) igényeinek megfelelően. Amikor megkapja a Data Box, kövesse [](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box) annak dokumentációját, hogy az adatokat erre az UNC elérési útra másolja a következő Data Box: *\\<\> \<StorageAccountName_AzFile\> \<ShareName\> DeviceIPAddres*. Itt *a ShareName* az átmeneti megosztás neve. Küldje vissza a Data Box az Azure-ba. |
| ![2. lépés](media/storage-sync-files-offline-data-transfer/bullet-2.png) | Várjon, amíg a fájlok meg nem vesznek az átmeneti átmeneti megosztásként kiválasztott Azure-fájlmegosztásban. *Ne engedélyezze a szinkronizálást ezekkel a megosztásokkal.* |
| ![3. lépés](media/storage-sync-files-offline-data-transfer/bullet-3.png) | <ul><li>Hozzon létre egy új üres megosztást minden olyan fájlmegosztáshoz, Data Box létre az Ön számára. Ennek az új megosztásnak ugyanabban a tárfiókban kell lennie, mint a Data Box megosztásnak. [Új Azure-fájlmegosztás létrehozása.](../files/storage-how-to-create-file-share.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)</li><li>[Hozzon létre egy szinkronizálási csoportot](file-sync-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint) egy Storage Sync Service-szolgáltatásban. Hivatkozhat az üres megosztásra felhővégpontként. Ismételje meg ezt a lépést minden Data Box fájlmegosztáson. [Állítsa be a Azure File Sync.](file-sync-deployment-guide.md)</li></ul> |
| ![4. lépés](media/storage-sync-files-offline-data-transfer/bullet-4.png) | [Adja hozzá az élő kiszolgáló könyvtárát kiszolgálóvégpontként.](file-sync-deployment-guide.md#create-a-server-endpoint) A folyamat során adja meg, hogy áthelyezte a fájlokat az Azure-ba, és hivatkozik az előkészítési megosztásra. Szükség szerint engedélyezheti vagy letilthatja a felhőbeli rétegezést. Amikor kiszolgálóvégpontot hoz létre az élő kiszolgálón, hivatkozhat az előkészítési megosztásra. A **Kiszolgálói végpont hozzáadása** panel Offline adatátvitel lehetőség alatt válassza az Engedélyezve **lehetőséget,** majd válassza ki azt az átmeneti megosztást, amely ugyanabban a tárfiókban kell lennie, mint a felhőbeli végpont. Itt az elérhető megosztások listáját tárfiók és még nem szinkronizált megosztások alapján szűrjük. A táblázatot követő képernyőkép bemutatja, hogyan hivatkozhat a DataBox-megosztásra a kiszolgálóvégpont létrehozásakor a Azure Portal. |
| ![5. lépés](media/storage-sync-files-offline-data-transfer/bullet-5.png) | Miután hozzáadta a kiszolgálóvégpontot az előző lépésben, az adatok automatikusan elindulnak a megfelelő forrásból. A [Megosztás szinkronizálása szakasz](#syncing-the-share) ismerteti, hogy mikor történik adatfolyam a DataBox-megosztásból vagy a Windows Serverről |
| |

![Képernyőkép a Azure Portal felhasználói felületéről, amely bemutatja, hogyan engedélyezheti az offline adatátvitelt egy új kiszolgálóvégpont létrehozása során](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>A megosztás szinkronizálása
A kiszolgálóvégpont létrehozása után elindul a szinkronizálás. A szinkronizálási folyamat határozza meg, hogy a kiszolgálón lévő egyes fájlok is léteznek-e abban az átmeneti megosztásban, Data Box a fájlokat. Ha a fájl létezik, a szinkronizálási folyamat a kiszolgálóról való feltöltés helyett az előkészítési megosztásból másolja a fájlt. Ha a fájl nem létezik az átmeneti megosztásban, vagy ha a helyi kiszolgálón újabb verzió érhető el, a szinkronizálási folyamat feltölti a fájlt a helyi kiszolgálóról.

A megosztás szinkronizálásakor a szinkronizálás egyesíti a helyi kiszolgálón lévő fájlváltozatok hiányzó fájlattribútumokat, engedélyeket vagy időbélyegeket, és kombinálja azokat a DataBox-megosztásban lévő megfelelőivel. Ez biztosítja, hogy minden fájl és mappa minden lehetséges fájlhűséggel megérkezik az Azure-fájlmegosztásba.

> [!IMPORTANT]
> A tömeges áttelepítési módot csak akkor engedélyezheti, ha kiszolgálóvégpontot hoz létre. A kiszolgálóvégpont létrehozása után nem integrálhat tömegesen migrált adatokat egy már szinkronizált kiszolgálóról a névtérbe.

## <a name="acls-and-timestamps-on-files-and-folders"></a>Fájlok és mappák ACL-ek és időbélyegek
Azure File Sync biztosítja, hogy a fájlok és mappák ACL-ek szinkronizálva legyenek az élő kiszolgálóról akkor is, ha a használt tömeges áttelepítési eszköz eredetileg nem szállít ACL-eket. Emiatt az átmeneti megosztásnak nem kell ACL-eket tartalmaznia fájlokhoz és mappákhoz. Amikor új kiszolgálóvégpont létrehozásakor engedélyezi az offline adatáttelepítési funkciót, a rendszer az összes fájl ACL-t szinkronizálja a kiszolgálón. Az újonnan létrehozott és módosított időbélyegek is szinkronizálva vannak.

## <a name="shape-of-the-namespace"></a>A névtér alakja
Ha engedélyezi a szinkronizálást, a kiszolgáló tartalma határozza meg a névtér alakját. Ha a pillanatkép és a migrálás befejezése után töröl fájlokat a Data Box a helyi kiszolgálóról, ezek a fájlok nem kerülnek át az élő, szinkronizált névtérbe. Az átmeneti megosztásban maradnak, de nem másolják őket. Erre azért van szükség, mert a szinkronizálás az élő kiszolgálónak megfelelően tartja meg a névteret. A Data Box *pillanatkép* csak egy átmeneti terület a hatékony fájlmásolókhoz. Nem ez az élő névtér alakjának hitelesítése.

## <a name="cleaning-up-after-bulk-migration"></a>A tömeges migrálás utáni tisztítás 
Amikor a kiszolgáló befejezi a névtér kezdeti szinkronizálását, a Data Box áttelepített fájlok az átmeneti fájlmegosztást használják. A kiszolgáló **kiszolgálóvégpont** tulajdonságai panelének Azure Portal  Offline adatátvitel szakaszában az  állapot Folyamatban állapotról Befejezve **állapotra változik.** 

![Képernyőkép a Kiszolgálóvégpont tulajdonságai panelről, ahol az offline adatátviteli vezérlők állapota és letiltása látható](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

Most a költségek csökkentése érdekében megtisztíthatja az előkészítési megosztást:

1. Ha a **Kiszolgálóvégpont tulajdonságai panelen** az állapot **Befejezve,** válassza az **Offline adatátvitel letiltása lehetőséget.**
2. A költségek csökkentése érdekében érdemes lehet törölni az előkészítési megosztást. Az előkészítési megosztás valószínűleg nem tartalmaz fájl- és mappa ACL-eket, ezért valószínűleg nem lesz hasznos. Az időpontnak megfelelő biztonsági mentéshez hozzon létre egy valós pillanatképet a szinkronizált [Azure-fájlmegosztásról.](../files/storage-snapshots-files.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json) [Beállíthatja, Azure Backup ütemezett pillanatképeket]( ../../backup/backup-afs.md) készít.

Az offline adatátviteli módot csak akkor tiltsa le, ha az állapot **Befejezve,** vagy ha egy helytelen konfiguráció miatt le szeretné mondani. Ha az üzembe helyezés során letiltja a módot, a fájlok akkor is feltölthetők a kiszolgálóról, ha az átmeneti megosztás továbbra is elérhető.

> [!IMPORTANT]
> Az offline adatátviteli mód letiltása után nem engedélyezheti újra, még akkor sem, ha a tömeges migrálásból származó előkészítési megosztás továbbra is elérhető.

## <a name="azure-file-sync-and-pre-seeded-files-in-the-cloud"></a>Azure File Sync és előre áttolt fájlok a felhőben

Ha a DataBoxtól (például az AzCopyn, a RoboCopyn keresztül, felhőalapú biztonsági mentésből vagy más módszerrel) más módon is áttért fájlokat egy Azure-fájlmegosztásba, akkor továbbra is követnie kell az ebben a cikkben ismertetett [offline](#process-for-offline-data-transfer) adatátviteli folyamatot. A DataBoxot csak a fájlok felhőbe való áthelyezésének módjaként kell figyelmen kívül hagynia. Rendkívül fontos azonban, hogy továbbra is egy átmeneti megosztásba, és ne *az* utolsó, csatlakoztatott megosztásba Azure File Sync folyamattal.

> [!WARNING]
> **Kövesse a fájlok** átmeneti megosztásba való át történő vetésének folyamatát, és ne az utolsó , Azure File Sync megosztásra. Ha nem, fájlütközések fordulhatnak elő (mindkét fájlverziót tárolja a rendszer), valamint az élő kiszolgálón törölt fájlok is visszajöhetnek, ha még mindig léteznek a régebbi, áttolt fájlkészletben. Emellett a mappaváltozások egyesülnek egymással, így nagyon nehéz lesz elválasztani a névteret egy ilyen hiba után.

## <a name="next-steps"></a>Következő lépések
- [Az üzembe helyezés Azure File Sync meg](file-sync-planning.md)
- [Azure File Sync üzembe helyezése](file-sync-deployment-guide.md)