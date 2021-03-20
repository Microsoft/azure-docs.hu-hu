---
title: Azure Files központi telepítésének tervezése | Microsoft Docs
description: Azure Files központi telepítés tervezésének megismerése. Közvetlenül csatlakoztathat egy Azure-fájlmegosztást, vagy a helyszínen gyorsítótárazhatja az Azure-fájlmegosztást a Azure File Sync használatával.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 8a96b44a280e0aea15a6d0843f02f4ed16f8fcf4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98879847"
---
# <a name="planning-for-an-azure-files-deployment"></a>Az Azure Files üzembe helyezésének megtervezése
[Azure Files](storage-files-introduction.md) kétféleképpen helyezhető üzembe: a kiszolgáló nélküli Azure-fájlmegosztás közvetlen csatlakoztatásával vagy az Azure-fájlmegosztás helyszíni gyorsítótárazásával Azure file Sync használatával. Az üzembe helyezési lehetőségek közül válassza ki azokat a beállításokat, amelyeket figyelembe kell vennie az üzemelő példány tervezésekor. 

- **Azure-fájlmegosztás közvetlen csatlakoztatása**: mivel Azure Files kiszolgáló-üzenetblokk (SMB) vagy hálózati fájlrendszer (NFS) hozzáférést biztosít, az Azure-fájlmegosztás a helyszínen vagy a felhőben is csatlakoztatható az operációs rendszerében elérhető szabványos SMB-vagy NFS-ügyfelek használatával. Mivel az Azure-fájlmegosztás kiszolgáló nélküli, az éles környezetekben való üzembe helyezéshez nem szükséges fájlkiszolgáló vagy NAS-eszköz kezelése. Ez azt jelenti, hogy nem kell szoftverfrissítéseket alkalmaznia vagy fizikai lemezeket cserélnie. 

- **A helyszíni Azure-fájlmegosztás gyorsítótárazása a Azure file Sync** használatával: a Azure file Sync lehetővé teszi a szervezete fájlmegosztás megszervezését Azure Filesban, miközben megtartja a helyszíni fájlkiszolgáló rugalmasságát, teljesítményét és kompatibilitását. Azure File Sync átalakítja a helyszíni (vagy Felhőbeli) Windows Servert az Azure SMB-fájlmegosztás gyors gyorsítótárba. 

Ez a cikk elsősorban a helyszíni vagy a Felhőbeli ügyfelek által közvetlenül csatlakoztatott Azure-fájlmegosztás üzembe helyezési szempontjait tárgyalja. Azure File Sync központi telepítésének megtervezéséhez tekintse meg a [Azure file Sync központi telepítés tervezése](storage-sync-files-planning.md)című témakört.

## <a name="available-protocols"></a>Elérhető protokollok

Azure Files két olyan protokollt kínál, amelyek a fájlmegosztás, az SMB és a hálózati fájlrendszer (NFS) csatlakoztatásakor használhatók. A protokollok részletes ismertetését lásd: [Azure fájlmegosztás protokollok](storage-files-compare-protocols.md).

> [!IMPORTANT]
> A cikk tartalmának túlnyomó része csak az SMB-megosztásokra vonatkozik. Az NFS-megosztásokra vonatkozó minden egyes esetben a megfelelő állapotot kell alkalmazni.

## <a name="management-concepts"></a>Felügyeleti fogalmak
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

Az Azure-fájlmegosztás Storage-fiókokban való telepítésekor a következőket javasoljuk:

- Az Azure file-megosztásokat csak a Storage-fiókokba helyezheti üzembe más Azure-fájlmegosztás esetén. Bár a GPv2 Storage-fiókok lehetővé teszik vegyes célú tárolási fiókok használatát, mivel a tárolási erőforrások, például az Azure-fájlmegosztás és a blob-tárolók megosztják a tárolási fiók korlátait, az erőforrások összekeverése pedig nehezebbé teszi a teljesítménnyel kapcsolatos problémák későbbi hibaelhárítását. 

- Ügyeljen arra, hogy a Storage-fiók IOPS korlátozásait az Azure-fájlmegosztás telepítésekor. Ideális esetben a Storage-fiókokkal rendelkező 1:1-es fájlmegosztás leképezhető, azonban a különböző korlátok és korlátozások miatt a szervezettől és az Azure-tól is nem mindig lehetséges. Ha egyetlen Storage-fiókban nem lehet egyetlen fájlmegosztást telepíteni, gondolja át, hogy mely megosztások lesznek aktívak, és hogy mely megosztások kevésbé lesznek aktívak, hogy a legforróbb fájlmegosztás ne legyen ugyanazon a Storage-fiókban.

- Csak a GPv2-és FileStorage-fiókok telepítése, valamint a GPv1 és a klasszikus tárolási fiókok frissítése a környezetben való kereséskor. 

## <a name="identity"></a>Identitás
Egy Azure-fájlmegosztás eléréséhez a fájlmegosztás felhasználójának hitelesítenie kell, és engedéllyel kell rendelkeznie a megosztás eléréséhez. Ez a fájlmegosztást elérő felhasználó identitása alapján történik. A Azure Files három fő identitás-szolgáltatóval integrálódik:
- Helyszíni **Active Directory Domain Services (AD DS vagy helyszíni AD DS)**: az Azure Storage-fiókok tartományhoz, Active Directory Domain Serviceshoz, például Windows Server FÁJLKISZOLGÁLÓ vagy NAS-eszközhöz csatlakoztathatók. Üzembe helyezhet egy tartományvezérlőt a helyszínen, egy Azure-beli virtuális gépen, vagy akár egy másik felhőalapú szolgáltató virtuális gépén is. Azure Files a tartományvezérlő üzemeltetéséhez szükséges agnosztikus. Miután a Storage-fiók tartományhoz csatlakozik, a végfelhasználó csatlakoztathat egy fájlmegosztást a számítógéphez bejelentkezett felhasználói fiókhoz. Az AD-alapú hitelesítés a Kerberos hitelesítési protokollt használja.
- **Azure Active Directory Domain Services (azure AD DS)**: az Azure AD DS egy Microsoft által felügyelt tartományvezérlőt biztosít, amely Azure-erőforrásokhoz használható. A Storage-fiók Azure AD DShoz való csatlakoztatása hasonló előnyökkel jár a tartományhoz való csatlakozáshoz az ügyfél tulajdonában lévő Active Directoryhoz. Ez az üzembe helyezési lehetőség az olyan alkalmazások esetében hasznos, amelyek AD-alapú engedélyeket igényelnek. Mivel az Azure AD DS AD-alapú hitelesítést biztosít, ez a beállítás a Kerberos hitelesítési protokollt is használja.
- **Azure Storage-fiók kulcsa**: az Azure-fájlmegosztás egy Azure Storage-fiók kulcsával is csatlakoztatható. Fájlmegosztás ily módon történő csatlakoztatásához a rendszer a Storage-fiók nevét használja felhasználónévként, és a Storage-fiók kulcsa jelszóként van használatban. Ha a Storage-fiók kulcsát használja az Azure-fájlmegosztás csatlakoztatására, akkor a felügyeleti művelet hatékony, mivel a csatlakoztatott fájlmegosztás teljes jogosultsággal rendelkezik a megosztás összes fájljához és mappájához, még akkor is, ha ACL-ekkel rendelkezik. Ha a Storage-fiók kulcsát használja az SMB protokollon keresztül történő csatlakoztatásra, a rendszer az NTLMv2 hitelesítési protokollt használja.

A helyszíni fájlkiszolgálók áttelepítésére vagy a Azure Files a Windows-fájlkiszolgálók vagy a NAS-készülékek működéséhez szükséges új fájlmegosztás létrehozásához az ajánlott lehetőség a Storage-fiókhoz való csatlakozás az **ügyfél tulajdonában lévő Active Directory** . Ha többet szeretne megtudni a Storage-fiók ügyfél által birtokolt Active Directoryhoz való csatlakoztatásáról, tekintse meg a [Azure Files Active Directory áttekintése](storage-files-active-directory-overview.md)című témakört.

Ha a Storage-fiók kulcsát szeretné használni az Azure-fájlmegosztás eléréséhez, javasoljuk, hogy használja a szolgáltatás-végpontokat a [hálózatkezelés](#networking) szakaszban leírtak szerint.

## <a name="networking"></a>Hálózatkezelés
Az Azure-fájlmegosztás bárhonnan elérhető a Storage-fiók nyilvános végpontján keresztül. Ez azt jelenti, hogy a hitelesített kérések, például a felhasználó bejelentkezési identitása által engedélyezett kérések, biztonságosan származhatnak az Azure-on belül vagy kívül is. Számos felhasználói környezetben a helyszíni munkaállomás Azure-fájlmegosztás kezdeti csatlakoztatása sikertelen lesz, noha az Azure-beli virtuális gépekről való csatlakoztatások sikeresek lesznek. Ennek az az oka, hogy számos szervezet és internetszolgáltató (ISP) blokkolja az SMB által a kommunikációhoz használt portot, a 445-es portot. A [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx) összefoglalja, hogy mely internetszolgáltatók engedélyezik vagy tiltják a 445-ös porton keresztüli hozzáférést.

Az Azure-fájlmegosztás elérésének feloldásához két fő lehetőség közül választhat:

- A szervezet helyszíni hálózatának 445-as portjának feloldása. Az Azure-fájlmegosztás csak az internetes biztonságos protokollok, például az SMB 3,0 és a kiszolgált API használatával lehet külsőleg hozzáférni a nyilvános végponton keresztül. Ez a legegyszerűbb módszer az Azure-fájlmegosztás helyszíni elérésére, mivel nem igényel speciális hálózatkezelési konfigurációt a szervezet kimenő portokra vonatkozó szabályainak módosítása után azonban javasoljuk, hogy távolítsa el az SMB protokoll örökölt és elavult verzióit, azaz az SMB 1,0-et. Ennek megismeréséhez tekintse meg a [Windows/Windows Server biztonságossá tétele](storage-how-to-use-files-windows.md#securing-windowswindows-server) és a [Linux biztonságossá tétele](storage-how-to-use-files-linux.md#securing-linux)című témakört.

- Azure-fájlmegosztás elérése ExpressRoute vagy VPN-kapcsolaton keresztül. Amikor hálózati alagúton keresztül éri el az Azure-fájlmegosztást, csatlakoztathatja az Azure-fájlmegosztást, például egy helyszíni fájlmegosztást, mivel az SMB-forgalom nem haladja meg a szervezeti határt.   

Bár a technikai szempontból jóval egyszerűbb az Azure-fájlmegosztás nyilvános végponton keresztüli csatlakoztatása, a legtöbb ügyfelünk úgy dönt, hogy az Azure-fájlmegosztást egy ExpressRoute vagy VPN-kapcsolaton keresztül csatlakoztatja. Ezekkel a beállításokkal az SMB-és NFS-megosztások is elérhetők. Ehhez a következőt kell konfigurálnia a környezetéhez:  

- **Hálózati bújtatás ExpressRoute, helyek közötti vagy pont – hely típusú VPN használatával**: a virtuális hálózatba való bújtatás lehetővé teszi az Azure-fájlmegosztás helyszíni elérését, még akkor is, ha az 445-es port le van tiltva.
- **Privát végpontok**: a magánhálózati végpontok dedikált IP-címet biztosítanak a Storage-fióknak a virtuális hálózat címterület területén. Ez lehetővé teszi a hálózati bújtatást anélkül, hogy az Azure Storage-fürtök által birtokolt összes IP-címtartományt meg kellene nyitni a helyszíni hálózatokat. 
- **DNS-továbbítás**: konfigurálja a helyszíni DNS-t úgy, hogy feloldja a Storage-fiók (azaz `storageaccount.file.core.windows.net` a nyilvános felhő régiói) nevét a privát végpontok IP-címére való feloldáshoz.

Az Azure-fájlmegosztás üzembe helyezéséhez kapcsolódó hálózatkezelés megtervezéséhez tekintse meg [Azure Files hálózatkezelési megfontolásokat](storage-files-networking-overview.md).

## <a name="encryption"></a>Titkosítás
A Azure Files két különböző titkosítási típust támogat: az átvitel közbeni titkosítást, amely az Azure-fájlmegosztás csatlakoztatása/elérése és a nyugalmi állapotban lévő titkosítás használatakor használt titkosításhoz kapcsolódik, amely az adatok lemezen történő tárolásának módjára vonatkozik. 

### <a name="encryption-in-transit"></a>Titkosítás az átvitel során

> [!IMPORTANT]
> Ez a szakasz az SMB-megosztások továbbítási adatainak titkosítását ismerteti. Az NFS-megosztásokkal történő átvitel titkosításával kapcsolatos részletekért lásd: [Biztonság](storage-files-compare-protocols.md#security).

Alapértelmezés szerint az összes Azure Storage-fióknál engedélyezve van az átvitel titkosítása. Ez azt jelenti, hogy amikor az SMB-n keresztül csatlakoztat egy fájlmegosztást, vagy a kiosztott protokollon keresztül éri el (például a Azure Portal, a PowerShell/CLI vagy az Azure SDK-k használatával), Azure Files csak akkor engedélyezi a kapcsolatot, ha az SMB 3.0 + titkosítással vagy HTTPS-vel van ellátva. Azok az ügyfelek, amelyek nem támogatják az SMB 3,0-et vagy az SMB 3,0-et támogató ügyfeleket, de az SMB-titkosítást nem, nem fogják tudni csatlakoztatni az Azure-fájlmegosztást, ha engedélyezve van az átvitel titkosítása. Ha további információt szeretne arról, hogy mely operációs rendszerek támogatják az SMB 3,0 titkosítást, tekintse meg a [Windows](storage-how-to-use-files-windows.md), a [MacOS](storage-how-to-use-files-mac.md)és a [Linux](storage-how-to-use-files-linux.md)részletes dokumentációját. A PowerShell, CLI és SDK összes jelenlegi verziója támogatja a HTTPS-t.  

Egy Azure Storage-fiók esetében letilthatja a titkosítást az átvitel során. Ha a titkosítás le van tiltva, a Azure Files az SMB 2,1, a titkosítás nélküli SMB 3,0 és a titkosítatlan, nem titkosított API-hívások HTTP-n keresztüli használatát is lehetővé teszi. Az átvitel közbeni titkosítás letiltásának elsődleges oka az olyan örökölt alkalmazások támogatása, amelyeknek régebbi operációs rendszeren kell futniuk, például Windows Server 2008 R2 vagy régebbi Linux-disztribúció. Azure Files csak az Azure-fájlmegosztás azonos Azure-régiójában lévő SMB 2,1-kapcsolatokat engedélyezi. Az Azure-fájlmegosztás (például a helyszíni vagy egy másik Azure-régió) Azure-régióján kívüli SMB 2,1-ügyfél nem fog tudni hozzáférni a fájlmegosztás eléréséhez.

Javasoljuk, hogy engedélyezze a továbbítást a forgalomban lévő adatátvitel titkosításának biztosításához.

További információ az átvitel közbeni titkosításról: [biztonságos átvitel megkövetelése az Azure Storage-ban](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="encryption-at-rest"></a>Titkosítás inaktív állapotban
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="data-protection"></a>Adatvédelem
A Azure Files többrétegű megközelítést biztosít az adatok biztonsági mentésére, helyreállítására és a biztonsági fenyegetésekkel szembeni védelemre.

### <a name="soft-delete"></a>Helyreállítható törlés
A fájlmegosztás (előzetes verzió) helyreállítható törlése olyan tárolási fiók szintű beállítás, amely lehetővé teszi a fájlmegosztás helyreállítását véletlenül törölt állapotba. Egy fájlmegosztás törlésekor a rendszer a véglegesen törölt állapotba helyezi az átmeneti törlés helyett. Beállíthatja, hogy a rendszer a véglegesen törölt adatok mennyiségét helyreállítsa, és bármikor törölje a megosztást a megőrzési időszak alatt. 

Javasoljuk, hogy a legtöbb fájlmegosztás esetében a Soft delete bekapcsolását javasolja. Ha olyan munkafolyamattal rendelkezik, amelyben a megosztás törlése gyakori és várt, akkor dönthet úgy, hogy nagyon rövid megőrzési időtartammal rendelkezik, vagy ha egyáltalán nem engedélyezte a törlést.

A helyreállítható törléssel kapcsolatos további információkért lásd: a [véletlen adattörlés megakadályozása](./storage-files-prevent-file-share-deletion.md).

### <a name="backup"></a>Backup
Az Azure-fájlmegosztás biztonsági mentését a megosztási [Pillanatképek](./storage-snapshots-files.md)segítségével végezheti el, amelyek csak olvasható, a megosztás időponthoz tartozó példányai. A pillanatképek növekményes, ami azt jelenti, hogy csak annyi adatmennyiséget tartalmaznak, mint az előző pillanatkép óta. Fájlmegosztás esetén akár 200 pillanatképet is megadhat, és akár 10 évig is megtarthatja őket. Manuálisan is elvégezheti ezeket a pillanatképeket a Azure Portal, a PowerShell vagy a parancssori felület (CLI) segítségével, vagy használhatja a [Azure Backup](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). A pillanatképek tárolása a fájlmegosztás részeként történik, ami azt jelenti, hogy ha törli a fájlmegosztást, a pillanatképek is törlődni fognak. Ha a pillanatképek biztonsági mentését véletlen törléssel szeretné biztosítani, győződjön meg arról, hogy a megosztáshoz engedélyezve van-e a helyreállított törlés.

Az [Azure-fájlmegosztás Azure Backup](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) kezeli a pillanatképek ütemezését és megőrzését. A nagyapa-Atya-Son (GFS) képességei azt jelentik, hogy napi, heti, havi és éves pillanatképeket készíthet, amelyek mindegyike saját eltérő megőrzési időtartammal rendelkezik. A Azure Backup a helyreállítható törlés engedélyezését is lehetővé teszi, és azonnal törli a Storage-fiók törlési zárolását, amint a benne lévő fájlmegosztás a biztonsági mentéshez van konfigurálva. Végül Azure Backup biztosít bizonyos kulcsfontosságú monitorozási és riasztási képességeket, amelyek lehetővé teszik, hogy az ügyfelek összevont képet készítsenek a biztonsági mentési hagyatékról.

A Azure Portalban elemszintű és megosztási szintű visszaállításokat is végrehajthat a Azure Backup használatával. Mindössze annyit kell tennie, hogy kiválasztja a visszaállítási pontot (egy adott pillanatképet), az adott fájlt vagy könyvtárat, ha szükséges, majd azt a helyet (eredeti vagy másodlagos), amelyet vissza szeretne állítani. A biztonsági mentési szolgáltatás kezeli a pillanatkép-adatok másolását, és megjeleníti a visszaállítási folyamatot a portálon.

További információ a biztonsági mentésről: [Tudnivalók az Azure-fájlmegosztás biztonsági mentéséről](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="advanced-threat-protection-for-azure-files-preview"></a>A Azure Files komplex veszélyforrások elleni védelme (előzetes verzió)
Az Azure Storage komplex veszélyforrások elleni védelme egy további biztonsági intelligenciát biztosít, amely riasztásokat biztosít, amikor rendellenes tevékenységeket észlel a Storage-fiókjában, például szokatlan kísérletet tesz a Storage-fiók elérésére. Az ATP a kártevő-kivonatok hírnevének elemzését is futtatja, és az ismert kártevő szoftver riasztást küld. Az ATP-t az előfizetések vagy a Storage-fiókok szintjén Azure Security Center használatával konfigurálhatja. 

További információ: [Az Azure Storage komplex veszélyforrások elleni védelme](../common/azure-defender-storage-configure.md).

## <a name="storage-tiers"></a>Tárolási szintek
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>A standard fájlmegosztás engedélyezése akár 100 TiB-re is terjedhet
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="limitations"></a>Korlátozások
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="redundancy"></a>Redundancia
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

## <a name="migration"></a>Áttelepítés
Sok esetben nem fog létrehozni nettó új fájlmegosztást a szervezet számára, hanem inkább egy meglévő fájlmegosztást telepít át egy helyszíni fájlkiszolgálón vagy NAS-eszközről a Azure Filesra. A Migrálás sikerességéhez fontos a megfelelő áttelepítési stratégia és eszköz kiválogatása a forgatókönyvhöz. 

Az [áttelepítést áttekintő cikk](storage-files-migration-overview.md) röviden ismerteti az alapokat, és tartalmaz egy táblázatot, amely a forgatókönyvét valószínűleg magában foglaló áttelepítési útmutatókba vezet.

## <a name="next-steps"></a>Következő lépések
* [Azure File Sync központi telepítésének tervezése](storage-sync-files-planning.md)
* [Azure Files üzembe helyezése](./storage-how-to-create-file-share.md)
* [Azure File Sync üzembe helyezése](storage-sync-files-deployment-guide.md)
* [Tekintse át az áttelepítési áttekintést ismertető cikket a forgatókönyv áttelepítési útmutatójának megkereséséhez](storage-files-migration-overview.md)