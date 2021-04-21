---
title: Központi telepítési Azure Files tervezése | Microsoft Docs
description: Az üzembe helyezés megtervezésének Azure Files. Közvetlenül csatlakoztathat egy Azure-fájlmegosztást, vagy gyorsítótárazhatja az Azure-fájlmegosztást a Azure File Sync.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: be7e5b1f9721cc65c2f9b371becf8b4c82fb37b4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107759764"
---
# <a name="planning-for-an-azure-files-deployment"></a>Az Azure Files üzembe helyezésének megtervezése
[Azure Files](storage-files-introduction.md) kétféleképpen helyezhetők üzembe: közvetlenül a kiszolgáló nélküli Azure-fájlmegosztások csatlakoztatásával, vagy az Azure-fájlmegosztások helyszíni gyorsítótárazása a Azure File Sync. A választott üzembe helyezési lehetőség módosítja az üzembe helyezéshez megfontolni kívánt dolgokat. 

- Azure-fájlmegosztás közvetlen csatlakoztatása: Mivel a Azure Files kiszolgálói üzenetblokk (SMB) vagy hálózati fájlrendszer (NFS) hozzáférést biztosít, az Azure-fájlmegosztások a helyszínen vagy a felhőben is csatlakoztathatóak az operációs rendszeren elérhető standard SMB- vagy NFS-ügyfelek használatával. Mivel az Azure-fájlmegosztások kiszolgáló nélküliek, az éles környezetben való üzembe helyezéshez nincs szükség fájlkiszolgáló vagy NAS-eszköz kezelésére. Ez azt jelenti, hogy nem kell szoftverjavításokat alkalmaznia vagy fizikai lemezeket cserélnie. 

- Gyorsítótárazza a helyszíni Azure-fájlmegosztást a **Azure File Sync:** Azure File Sync segítségével központosíthatja a szervezet fájlmegosztását az Azure Files-ban, miközben a helyszíni fájlkiszolgálók rugalmasságát, teljesítményét és kompatibilitását is biztosítja. Azure File Sync helyszíni (vagy felhőbeli) Windows Servert alakít át az Azure SMB-fájlmegosztás gyors gyorsítótárába. 

Ez a cikk elsősorban a helyszíni vagy felhőbeli ügyfelek által közvetlenül csatlakoztatott Azure-fájlmegosztás üzembe helyezésének üzembe helyezésével kapcsolatos szempontokat tartalmazza. A központi telepítés [Azure File Sync: Planning for an Azure File Sync deployment](../file-sync/file-sync-planning.md)..

## <a name="available-protocols"></a>Elérhető protokollok

Azure Files két protokollt kínál, amelyek a fájlmegosztások, az SMB és a hálózati fájlrendszer (NFS) csatlakoztatásakor használhatók. A protokollokkal kapcsolatos részletekért lásd: [Azure-fájlmegosztási protokollok.](storage-files-compare-protocols.md)

> [!IMPORTANT]
> A cikk tartalmának nagy része csak SMB-megosztásra vonatkozik. Minden, az NFS-megosztásra érvényes, konkrétan azt fogja tudatni vele, hogy alkalmazható.

## <a name="management-concepts"></a>Felügyeleti fogalmak
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

Amikor Azure-fájlmegosztásokat helyez üzembe tárfiókban, a következőket javasoljuk:

- Csak azure-fájlmegosztások üzembe helyezése más Azure-fájlmegosztásokkal rendelkező tárfiókban. Bár a GPv2-tárfiókok lehetővé teszik, hogy vegyes célú tárfiókokat használjon, mivel az olyan tárolási erőforrások, mint az Azure-fájlmegosztások és a blobtárolók osztoznak a tárfiók korlátain, az erőforrások együttes használata megnehezítheti a teljesítményproblémák későbbi elhárítását. 

- Az Azure-fájlmegosztások üzembe helyezésekor figyelje meg a tárfiók IOPS-korlátait. Ideális esetben a fájlmegosztások 1:1-et leképezik tárfiókokkal, ez azonban nem mindig lehetséges a szervezet és az Azure különböző korlátai és korlátozásai miatt. Ha nem lehet egyetlen fájlmegosztást egyetlen tárfiókban üzembe helyezni, gondolja át, hogy mely megosztások lesznek rendkívül aktívak, és mely megosztások kevésbé aktívak, hogy a legaktívabb fájlmegosztások ne legyenek együtt ugyanabban a tárfiókban.

- Csak akkor telepítsen GPv2- és FileStorage-fiókokat, és frissítse a GPv1- és a klasszikus tárfiókokat, ha azokat a környezetében találja. 

## <a name="identity"></a>Identitás
Egy Azure-fájlmegosztás eléréséhez a fájlmegosztás felhasználóját hitelesíteni kell, és engedéllyel kell rendelkezik a megosztás eléréséhez. Ez a fájlmegosztáshoz hozzáférő felhasználó identitása alapján történik. Azure Files három fő identitásszolgáltatóval integrálható:
- Helyszíni Active Directory Domain Services (AD DS vagy helyszíni AD DS): Az Azure-tárfiókok tartományhoz is csatlakoznak az ügyfél tulajdonában lévő Active Directory Domain Services, akárcsak a Windows Server-fájlkiszolgálók vagy **NAS-eszközök.** Üzembe helyezhet egy tartományvezérlőt a helyszínen, egy Azure-beli virtuális gépen, vagy akár egy másik felhőszolgáltató virtuális gépeként is; Azure Files független a tartományvezérlő helytől. A tárfiók tartományhoz csatlakoztatása után a végfelhasználó csatlakoztathat egy fájlmegosztást azzal a felhasználói fiókkal, amelyről bejelentkezett a számítógépére. Az AD-alapú hitelesítés a Kerberos hitelesítési protokollt használja.
- **Azure Active Directory Domain Services (Azure AD DS)**: Azure AD DS a Microsoft által felügyelt tartományvezérlőt biztosít, amely azure-erőforrásokhoz használható. A tárfiókhoz való csatlakozás hasonló előnyökkel jár Azure AD DS mint a tartomány és egy ügyfél tulajdonában lévő Active Directory. Ez a telepítési lehetőség az AD-alapú engedélyeket igénylő alkalmazások átemelhető forgatókönyvei esetében a leghasznosabb. Mivel Azure AD DS AD-alapú hitelesítést biztosít, ez a beállítás a Kerberos hitelesítési protokollt is használja.
- **Azure Storage-fiókkulcs:** Az Azure-fájlmegosztások Azure Storage-fiókk kulccsal is csatlakoztathatóak. A fájlmegosztás ezen a módon való csatlakoztatásához a rendszer a tárfiók nevét használja felhasználónévként, a tárfiókkulcsot pedig jelszóként. Az Azure-fájlmegosztás csatlakoztatása a tárfiókkulccsal lényegében rendszergazdai művelet, mivel a csatlakoztatott fájlmegosztás teljes körű engedélyekkel rendelkezik a megosztás összes fájljához és mappájához, még akkor is, ha ACL-ekkel rendelkezik. Ha a tárfiókkulcsot használja az SMB-n keresztüli csatlakoztatáshoz, a rendszer az NTLMv2 hitelesítési protokollt használja.

A helyszíni fájlkiszolgálókról mirating vagy az Azure Files-ban új fájlmegosztások létrehozása windowsos fájlkiszolgálókhoz vagy **NAS-berendezésekhez** hasonlóan viselkedő ügyfelek esetén a tartomány a tárfiók ügyfél által birtokolt Active Directory való csatlakozása az ajánlott megoldás. Ha többet szeretne megtudni arról, hogy a tartomány hogyan csatlakozik a tárfiókhoz egy ügyfél tulajdonában lévő Active Directory, tekintse meg a [Azure Files Active Directory áttekintését.](storage-files-active-directory-overview.md)

Ha a tárfiókkulcsot szeretné használni az Azure-fájlmegosztások eléréséhez, javasoljuk, hogy használjon szolgáltatásvégpontokat a [Hálózat szakaszban leírtak](#networking) szerint.

## <a name="networking"></a>Hálózatkezelés
Az Azure-fájlmegosztások bárhonnan elérhetők a tárfiók nyilvános végpontjának használatával. Ez azt jelenti, hogy a hitelesített kérések, például a felhasználó bejelentkezési identitása által engedélyezett kérések, biztonságosan származnak az Azure-ból vagy azon kívül. Számos ügyfélkörnyezetben az Azure-fájlmegosztás kezdeti csatlakoztatása a helyszíni munkaállomáson sikertelen lesz, annak ellenére, hogy az Azure-beli virtuális gépekről való csatlakoztatás sikeres volt. Ennek az az oka, hogy számos szervezet és internetszolgáltató (ISP) blokkolja az SMB által a kommunikációhoz használt portot, a 445-ös portot. A [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx) összefoglalja, hogy mely internetszolgáltatók engedélyezik vagy tiltják a 445-ös porton keresztüli hozzáférést.

Az Azure-fájlmegosztáshoz való hozzáférés letiltásának feloldásához két fő lehetőség áll rendelkezésre:

- Oldja fel a 445-ös port letiltásának feloldását a szervezet helyszíni hálózata számára. Az Azure-fájlmegosztások csak a nyilvános végponton keresztül érhetők el olyan internetes biztonságos protokollok használatával, mint az SMB 3.0 és a FileREST API. Ez a legegyszerűbb módja az Azure-fájlmegosztás helyszíni elérésének, mivel nem igényel speciális hálózati konfigurációt a szervezet kimenőport-szabályainak módosításán túl, azonban javasoljuk, hogy távolítsa el az SMB protokoll örökölt és elavult verzióit, nevezetesen az SMB 1.0-t. Ennek mikéntjéhez lásd: [A Windows/Windows Server biztonságossá](storage-how-to-use-files-windows.md#securing-windowswindows-server) tétele és [a Linux biztonságossá tétele.](storage-how-to-use-files-linux.md#securing-linux)

- Azure-fájlmegosztások elérése ExpressRoute- vagy VPN-kapcsolaton keresztül. Ha egy hálózati alagúton keresztül fér hozzá az Azure-fájlmegosztáshoz, az Azure-fájlmegosztást helyszíni fájlmegosztásként csatlakoztathatja, mivel az SMB-forgalom nem halad át a szervezet határain.   

Bár technikai szempontból jelentősen egyszerűbb az Azure-fájlmegosztások csatlakoztatása a nyilvános végponton keresztül, a legtöbb ügyfél úgy dönt, hogy az Azure-fájlmegosztásokat ExpressRoute- vagy VPN-kapcsolaton keresztül csatlakoztatja. Ezekkel a beállításokkal SMB- és NFS-megosztásokkal is lehetséges a csatlakoztatás. Ehhez a következőket kell konfigurálnia a környezethez:  

- Hálózati **bújtatás ExpressRoute,** hely–hely vagy pont–hely VPN használatával: A virtuális hálózatba való bújtatás lehetővé teszi az Azure-fájlmegosztások helyszíni hozzáférését, még akkor is, ha a 445-ös port le van tiltva.
- **Privát végpontok:** A privát végpontok dedikált IP-címet adnak a tárfióknak a virtuális hálózat címterületről. Ez lehetővé teszi a hálózati bújtatást anélkül, hogy meg kellene nyitnia a helyszíni hálózatokat az Azure-tárolófürtök tulajdonában lévő összes IP-címtartományig. 
- **DNS-továbbítás:** Konfigurálja a helyszíni DNS-t úgy, hogy feloldja a tárfiók nevét (a nyilvános felhőrégió esetében) a privát `storageaccount.file.core.windows.net` végpontok IP-címére való feloldás érdekében.

Az Azure-fájlmegosztások üzembe helyezéséhez kapcsolódó hálózatépítés tervezéséhez tekintse meg a hálózati [Azure Files kapcsolatos tudnivalókat.](storage-files-networking-overview.md)

## <a name="encryption"></a>Titkosítás
Azure Files titkosítás két különböző típust támogat: az átvitel közbeni titkosítást, amely az Azure-fájlmegosztás csatlakoztatásához/eléréséhez használt titkosításhoz, valamint az adatok lemezen való tároláskor történő titkosításához kapcsolódó titkosítást támogatja. 

### <a name="encryption-in-transit"></a>Titkosítás az átvitel során

> [!IMPORTANT]
> Ez a szakasz az SMB-megosztások átvitel közbeni titkosításának részleteit tartalmazza. Az NFS-megosztások átvitel közbeni titkosításával kapcsolatos részletekért lásd: [Biztonság.](storage-files-compare-protocols.md#security)

Alapértelmezés szerint minden Azure-tárfiókon engedélyezve van a titkosítás az átvitel során. Ez azt jelenti, hogy ha SMB-n keresztül csatlakoztat egy fájlmegosztást, vagy a FileREST protokollon keresztül fér hozzá (például az Azure Portal, a PowerShell/CLI vagy az Azure SDK-k segítségével), a Azure Files csak akkor engedélyezi a kapcsolatot, ha az SMB 3.0+ titkosítással vagy HTTPS-kapcsolattal készült. Az SMB 3.0-t nem támogató ügyfelek vagy az SMB 3.0-t támogató, de SMB-titkosítást nem támogató ügyfelek nem tudják csatlakoztatni az Azure-fájlmegosztást, ha az átvitel közbeni titkosítás engedélyezve van. Arról, hogy mely operációs rendszerek támogatják az SMB 3.0-t titkosítással, tekintse meg a [Windows,](storage-how-to-use-files-windows.md) [macOS](storage-how-to-use-files-mac.md)és Linux rendszerek részletes [dokumentációját.](storage-how-to-use-files-linux.md) A PowerShell, a parancssori felület és az SDK-k összes jelenlegi verziója támogatja a HTTPS-t.  

Letilthatja az átvitel közbeni titkosítást egy Azure Storage-fiókhoz. Ha a titkosítás le van tiltva, Azure Files SMB 2.1, SMB 3.0 titkosítás nélkül, valamint titkosítatlan FileREST API-hívások használatát is engedélyezi HTTP-n keresztül. Az átvitel közbeni titkosítás letiltásának elsődleges oka egy régebbi alkalmazás támogatása, amely régebbi operációs rendszeren, például Windows Server 2008 R2 vagy régebbi Linux-disztribúción fut. Azure Files SMB 2.1-kapcsolatokat csak az Azure-fájlmegosztással azonos Azure-régióban engedélyezi; az Azure-fájlmegosztás Azure-régióján kívül (például a helyszínen vagy egy másik Azure-régióban) található SMB 2.1-ügyfél nem fog tudni hozzáférni a fájlmegosztáshoz.

Határozottan javasoljuk, hogy az átvitel közbeni adatok titkosítása engedélyezve legyen.

További információ az átvitel közbeni titkosításról: Biztonságos [átvitel megkövetelve az Azure Storage-ban.](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

### <a name="encryption-at-rest"></a>Titkosítás inaktív állapotban
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="data-protection"></a>Adatvédelem
Azure Files többrétegű megközelítéssel gondoskodik arról, hogy az adatokról biztonsági adatokat kell biztonsági adatokat biztonsági adatokat vissza lehet állítani, és védelmet kell biztosítani a biztonsági fenyegetések ellen.

### <a name="soft-delete"></a>Helyreállítható törlés
A fájlmegosztások helyreállítható törlése (előzetes verzió) egy tárfiókszintű beállítás, amely lehetővé teszi a fájlmegosztás véletlen törlésekor való helyreállítását. Egy fájlmegosztás törlésekor a fájl a végleges törlés helyett a törölt állapotra áll át. Beállíthatja, hogy a helyreállítható módon törölt adatok mennyi ideig állíthatók helyre a végleges törlés előtt, és a megőrzési időszak alatt bármikor visszaállíthatja a megosztást. 

Javasoljuk, hogy a legtöbb fájlmegosztás esetében kapcsolja be a soft delete (soft delete) adatokat. Ha olyan munkafolyamattal dolgozik, amelyben a megosztások törlése gyakori és várható, dönthet úgy, hogy rövid megőrzési időszakot használ, vagy egyáltalán nincs engedélyezve a soft delete.

A nem végleges törléssel kapcsolatos további információkért lásd: [Véletlen adattörlés megakadályozása.](./storage-files-prevent-file-share-deletion.md)

### <a name="backup"></a>Backup
Az Azure-fájlmegosztásról megosztási pillanatképekkel készíthet biztonsági másolatot, amelyek a megosztás csak olvasható, adott időpontban készült másolatai. [](./storage-snapshots-files.md) A pillanatképek növekményesek, ami azt jelenti, hogy csak annyi adatot tartalmaznak, amennyit az előző pillanatkép óta módosított. Fájlmegosztásonként legfeljebb 200 pillanatképet készíthet, és akár 10 évig is megőrizheti őket. Ezeket a pillanatképeket manuálisan is készítheti a Azure Portal a PowerShell vagy parancssori felület (CLI) használatával, vagy használhatja a [Azure Backup.](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) A pillanatképek a fájlmegosztásban vannak tárolva, ami azt jelenti, hogy ha törli a fájlmegosztást, a pillanatképek is törlődnek. A pillanatképek biztonsági másolatai véletlen törlés elleni védelme érdekében győződjön meg arról, hogy a megosztáson engedélyezve van a törlés.

[Azure Backup Azure-fájlmegosztások esetében a](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) pillanatképek ütemezését és megőrzését kezeli. A nagyapa-gyermek (GFS) képességek azt jelentik, hogy napi, heti, havi és éves pillanatképeket készíthet, amelyek mindegyikét külön megőrzési időtartammal tudja készíteni. Azure Backup a soft delete engedélyezését is, és törlési zárolást ad a tárfiókon, amint a benne található fájlmegosztások biztonsági mentésre vannak konfigurálva. Végül pedig a Azure Backup biztosít bizonyos kulcsfontosságú monitorozási és riasztási képességeket, amelyek lehetővé teszik az ügyfelek számára, hogy összevont nézetben tekintsenek meg a biztonsági másolatokat.

Az elemszintű és a megosztásszintű visszaállítást is elvégezheti a Azure Portal a Azure Backup. Mindössze ki kell választania a visszaállítási pontot (egy adott pillanatképet), szükség esetén az adott fájlt vagy könyvtárat, majd a helyet (eredeti vagy alternatív) a visszaállításhoz. A biztonsági mentési szolgáltatás kezeli a pillanatkép-adatok másolását, és megjeleníti a visszaállítási folyamat előrehaladását a portálon.

További információ a biztonsági mentésről: Tudnivalók az [Azure-fájlmegosztások biztonsági mentésről.](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

### <a name="azure-defender-for-azure-files"></a>Azure Defender a Azure Files 
Azure Defender azure storage-hoz (korábban Advanced Threat Protection for Azure Storage) a biztonsági intelligencia egy további rétegét biztosítja, amely riasztásokat biztosít, ha rendellenes tevékenységet észlel a tárfiókon, például szokatlan hozzáférési kísérleteket. Futtatja a kártevő-kivonatok jó hírnevének elemzését is, és riasztást küld az ismert kártevőkről. Az előfizetési Azure Defender tárfiók szintjén konfigurálhatja a fiókokat a Azure Security Center. 

További információ: [A Storage Azure Defender bemutatása.](../../security-center/defender-for-storage-introduction.md)

## <a name="storage-tiers"></a>Tárolási szintek
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>Standard fájlmegosztások engedélyezése legfeljebb 100 TiB-ra
Alapértelmezés szerint a standard fájlmegosztások legfeljebb 5 TiB-ra is átívelnek, de a megosztási korlátot 100 TiB-ra növelheti. A megosztási korlát növelésével lásd: Nagyméretű fájlmegosztások [engedélyezése és létrehozása.](storage-files-how-to-create-large-file-share.md)


#### <a name="limitations"></a>Korlátozások
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="redundancy"></a>Redundancia
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

## <a name="migration"></a>Áttelepítés
Sok esetben nem fog új fájlmegosztást létesítni a szervezet számára, hanem egy meglévő fájlmegosztást egy helyszíni fájlkiszolgálóról vagy NAS-eszközről a Azure Files. A migrálás sikerességéhez fontos kiválasztani a forgatókönyvnek megfelelő migrálási stratégiát és eszközt. 

A [migrálás áttekintését](storage-files-migration-overview.md) ismertető cikk röviden ismerteti az alapokat, és egy táblázatot tartalmaz, amely a forgatókönyvet valószínűleg ismertető migrálási útmutatókhoz vezet.

## <a name="next-steps"></a>Következő lépések
* [Központi telepítés Azure File Sync tervezése](../file-sync/file-sync-planning.md)
* [Üzembe helyezés Azure Files](./storage-how-to-create-file-share.md)
* [Üzembe helyezés Azure File Sync](../file-sync/file-sync-deployment-guide.md)
* [A migrálás áttekintését ismertető cikkben megtalálja a forgatókönyvéhez használható migrálási útmutatót](storage-files-migration-overview.md)