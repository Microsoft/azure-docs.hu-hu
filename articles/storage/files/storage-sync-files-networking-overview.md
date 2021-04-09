---
title: Azure File Sync hálózatkezelési megfontolások | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhatja a hálózatkezelést úgy, hogy Azure File Sync használatával gyorsítótárazza a fájlokat a helyszínen.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 76d97e3312c1df51193d8a881f3ee07fcd155d75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94629359"
---
# <a name="azure-file-sync-networking-considerations"></a>Azure File Sync hálózati megfontolások
Az Azure-fájlmegosztás két módon is kapcsolódhat:

- A megosztás elérése közvetlenül az SMB-vagy a kiosztott protokollon keresztül. Ez a hozzáférési minta elsősorban akkor alkalmazható, ha a lehető legtöbb helyszíni kiszolgálót el szeretné távolítani.
- Az Azure-fájlmegosztás gyorsítótárának létrehozása egy helyszíni kiszolgálón (vagy egy Azure-beli virtuális gépen) Azure File Sync használatával, valamint a fájlmegosztás adatainak elérése a helyszíni kiszolgálóról az Ön által választott protokollal (SMB, NFS, FTPS stb.) a használati esethez. Ez a hozzáférési minta hasznos, mivel az a legjobbat ötvözi a helyszíni teljesítmény és a Felhőbeli skálázás, valamint a kiszolgáló nélküli (például Azure Backup) szolgáltatások számára.

Ez a cikk azt ismerteti, hogyan kell konfigurálni a hálózatkezelést, ha a használati eset a Azure File Sync a fájlok helyszíni gyorsítótárazására való használatát igényli, és nem közvetlenül csatlakoztatja az Azure-fájlmegosztást az SMB-kapcsolaton keresztül. A Azure Files központi telepítés hálózatkezelési szempontjaival kapcsolatos további információkért lásd: [Azure Files hálózatkezelési megfontolások](storage-files-networking-overview.md).

A Azure File Sync hálózati konfigurációja két különböző Azure-objektumot ölel fel: egy Storage Sync szolgáltatást és egy Azure Storage-fiókot. A Storage-fiók egy olyan felügyeleti szerkezet, amely egy megosztott tárolót jelöl, amelyben több fájlmegosztást is üzembe helyezhet, valamint más tárolási erőforrásokat, például blob-tárolókat vagy várólistákat. A tárolási szinkronizálási szolgáltatás olyan felügyeleti megoldás, amely a regisztrált kiszolgálókat jelöli, amelyek olyan Windows-fájlkiszolgálók, amelyeken a Azure File Sync és a szinkronizálási kapcsolat topológiája van meghatározva. 

## <a name="connecting-windows-file-server-to-azure-with-azure-file-sync"></a>A Windows-fájlkiszolgáló csatlakoztatása az Azure-hoz Azure File Sync 
Azure Files és Azure File Sync helyi Windows-fájlkiszolgálón való beállításához és használatához nem szükséges az Azure-hoz való speciális hálózatkezelés az alapszintű internetkapcsolaton túl. Azure File Sync telepítéséhez telepítenie kell az Azure File Sync ügynököt az Azure-ban szinkronizálni kívánt Windows-fájlkiszolgálón. A Azure File Sync ügynök két csatornán keresztül éri el a szinkronizálást egy Azure-fájlmegosztás használatával:

- A kiosztott protokoll, amely egy HTTPS-alapú protokoll az Azure-fájlmegosztás eléréséhez. Mivel a hitelesítő protokoll a szabványos HTTPS protokollt használja az adatátvitelhez, a 443-es portnak elérhetőnek kell lennie a kimenő forgalomnak. Azure File Sync nem használja az SMB protokollt az adatok átviteléhez a helyszíni Windows-kiszolgálókról az Azure-fájlmegosztást.
- A Azure File Sync szinkronizálási protokoll, amely egy HTTPS-alapú protokoll a szinkronizációs ismeretek cseréjére, azaz a környezetében lévő fájlokra és mappákra vonatkozó verziószámot, a környezetében lévő végpontok között. Ez a protokoll a környezetében található fájlokról és mappákról (például időbélyegek és hozzáférés-vezérlési listák (ACL-ek)) származó metaadatok cseréjére is használható. 

Mivel Azure Files a közvetlen SMB protokoll elérését kínálja az Azure-fájlmegosztás esetében, az ügyfelek gyakran csodálkoznak, ha speciális hálózatkezelést kell konfigurálniuk az Azure-fájlmegosztás SMB-vel való csatlakoztatásához a Azure File Sync-ügynök számára. Ez nem csak nem kötelező, de a rendszergazdai forgatókönyvek kivételével nem feltétlenül szükséges, de az Azure-fájlmegosztás által közvetlenül végzett módosítások észlelésének hiánya miatt (a módosítások nem észlelhetők 24 óránál hosszabb ideig, az Azure-fájlmegosztás méretétől és számától függően). Ha közvetlenül kívánja használni az Azure-fájlmegosztást, például nem használja Azure File Sync a helyszíni gyorsítótárba történő gyorsítótárazásra, további információt talál a hálózati szempontokról a közvetlen hozzáféréshez [Azure Files hálózatkezelés áttekintése című](storage-files-networking-overview.md)témakörben.

Bár a Azure File Sync nem igényel speciális hálózati konfigurációt, egyes ügyfelek speciális hálózati beállításokat kívánnak konfigurálni a következő forgatókönyvek engedélyezéséhez:

- Együttműködik a szervezet proxykiszolgáló-konfigurációjával.
- Nyissa meg a szervezet helyi tűzfalát a Azure Files és Azure File Sync Services szolgáltatásban.
- Az alagút Azure Files és Azure File Sync ExpressRoute vagy VPN-kapcsolaton keresztül.

### <a name="configuring-proxy-servers"></a>Proxykiszolgálók konfigurálása
Számos szervezet proxykiszolgálót használ a helyszíni hálózaton belüli és a hálózaton kívüli erőforrások, például az Azure-ban lévő erőforrások közötti közvetítőként. A proxykiszolgáló hasznos lehet számos alkalmazáshoz, például a hálózati elkülönítéshez és a biztonsághoz, valamint a figyeléshez és a naplózáshoz. A Azure File Sync teljes mértékben együttműködik a proxykiszolgálón, azonban a Azure File Sync használatával manuálisan kell konfigurálnia a környezet proxy-végpontjának beállításait. Ezt a PowerShellen keresztül kell elvégezni a Azure File Sync Server-parancsmagok használatával. 

A Azure File Sync proxykiszolgáló használatával történő konfigurálásával kapcsolatos további információkért lásd: [Azure file Sync konfigurálása proxykiszolgáló](storage-sync-files-firewall-and-proxy.md)használatával.

### <a name="configuring-firewalls-and-service-tags"></a>Tűzfalak és szolgáltatás-címkék konfigurálása
Biztonsági okokból elkülönítheti a fájlkiszolgálók a legtöbb internetes helyről. Ha Azure File Synct szeretne használni a környezetben, módosítania kell a tűzfalat úgy, hogy az Azure-szolgáltatások kiválasztásához megnyissa azt. Ezt úgy teheti meg, hogy beolvassa a szükséges szolgáltatások IP-címtartományt a [Service Tags](../../virtual-network/service-tags-overview.md)nevű mechanizmuson keresztül.

A Azure File Sync a következő szolgáltatások IP-címtartományok használatát igényli a szolgáltatásuk címkéi alapján azonosítva:

| Szolgáltatás | Leírás | Szolgáltatáscímke |
|---------|-------------|-------------|
| Azure File Sync | A tárolási szinkronizálási szolgáltatás objektuma által jelzett Azure File Sync szolgáltatás felelős az Azure-fájlmegosztás és a Windows-fájlkiszolgáló közötti adatszinkronizálás fő tevékenységének. | `StorageSyncService` |
| Azure Files | Az Azure File Syncon keresztül szinkronizált összes adatot az Azure-fájlmegosztás tárolja. A Windows-fájlkiszolgálók módosításait a rendszer replikálja az Azure-fájlmegosztás számára, és a helyszíni fájlkiszolgálón lévő fájlok zökkenőmentesen letöltődnek, amikor egy felhasználó kéri őket. | `Storage` |
| Azure Resource Manager | A Azure Resource Manager az Azure felügyeleti felülete. Minden felügyeleti hívás, beleértve a Azure File Sync kiszolgáló regisztrációját és a folyamatban lévő szinkronizálási kiszolgáló feladatait, a Azure Resource Manageron keresztül történik. | `AzureResourceManager` |
| Azure Active Directory | A Azure Active Directory vagy az Azure AD olyan felhasználói rendszerbiztonsági tagokat tartalmaz, amelyek a kiszolgáló regisztrálásának engedélyezéséhez szükségesek a Storage Sync szolgáltatáshoz, valamint a Azure File Sync számára szükséges egyszerű szolgáltatásokat a Felhőbeli erőforrások eléréséhez. | `AzureActiveDirectory` |

Ha az Azure-ban Azure File Synct használ, még akkor is, ha ez egy másik régió, a szolgáltatási címke nevét közvetlenül a hálózati biztonsági csoportban használhatja, hogy engedélyezze a forgalmat a szolgáltatás felé. Ha többet szeretne megtudni Ennek módjáról, tekintse meg a [hálózati biztonsági csoportok](../../virtual-network/network-security-groups-overview.md)című témakört. 

Ha helyszíni Azure File Sync használ, a Service tag API-val adott IP-címtartományok beolvasása a tűzfal engedélyezési listájához. A következő két módszer használható az adatok lekéréséhez:

- A szolgáltatási címkéket támogató összes Azure-szolgáltatáshoz tartozó IP-címtartományok aktuális listája a Microsoft letöltőközpontban, egy JSON-dokumentum formájában, hetente van közzétéve. Mindegyik Azure-felhő saját JSON-dokumentummal rendelkezik, amely a felhőhöz kapcsolódó IP-címtartományt tartalmazza:
    - [Nyilvános Azure](https://www.microsoft.com/download/details.aspx?id=56519)
    - [Azure US Government](https://www.microsoft.com/download/details.aspx?id=57063)
    - [Azure China](https://www.microsoft.com/download/details.aspx?id=57062)
    - [Azure Germany](https://www.microsoft.com/download/details.aspx?id=57064)
- A Service tag Discovery API (előzetes verzió) lehetővé teszi az aktuális szolgáltatási címkék programozott lekérését. Az előzetes verzióban a Service tag Discovery API a Microsoft letöltőközpontból közzétett JSON-dokumentumokból származó információknál kevésbé naprakész adatokat adhat vissza. Az API felületét az automatizálási beállítások alapján használhatja:
    - [REST API](/rest/api/virtualnetwork/servicetags/list)
    - [Azure PowerShell](/powershell/module/az.network/Get-AzNetworkServiceTag)
    - [Azure CLI](/cli/azure/network#az-network-list-service-tags)

Ha többet szeretne megtudni arról, hogy a Service tag API hogyan használható a szolgáltatások címeinek lekérésére, tekintse meg a [Azure file Sync IP-címek engedélyezési listájának engedélyezése](storage-sync-files-firewall-and-proxy.md#allow-list-for-azure-file-sync-ip-addresses)című témakört.

### <a name="tunneling-traffic-over-a-virtual-private-network-or-expressroute"></a>Virtuális magánhálózat vagy ExpressRoute feletti forgalom bújtatása
Egyes szervezetek az Azure-hoz való kommunikációt igénylik egy hálózati alagút, például egy virtuális magánhálózati magánhálózat (VPN) vagy ExpressRoute egy további biztonsági réteghez való átirányításához, vagy az Azure-hoz való kommunikáció biztosításához a determinisztikus útvonalat követi. 

Amikor hálózati alagutat hoz létre a helyszíni hálózat és az Azure között, a helyszíni hálózatot egy vagy több Azure-beli virtuális hálózattal társítja. Egy [virtuális hálózat](../../virtual-network/virtual-networks-overview.md)vagy VNet hasonló egy hagyományos hálózathoz, amelyet a helyszínen fog üzemeltetni. Az Azure Storage-fiókokhoz vagy az Azure-beli virtuális gépekhez hasonlóan a VNet egy erőforráscsoport-ban üzembe helyezett Azure-erőforrás. 

Azure Files és File Sync a következő mechanizmusokat támogatja a helyszíni kiszolgálók és az Azure közötti adatforgalom bújtatásához:

- [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md): a VPN Gateway egy adott típusú virtuális hálózati átjáró, amely egy Azure-beli virtuális hálózat és egy másik hely (például a helyszíni) közötti titkosított forgalom küldésére szolgál az interneten keresztül. Az Azure VPN Gateway egy olyan Azure-erőforrás, amely egy, a Storage-fiók vagy más Azure-erőforrás melletti erőforráscsoporthoz is telepíthető. Mivel Azure File Sync egy helyszíni Windows-fájlkiszolgálón való használatra tervezték, általában [helyek közötti (S2S) VPN-](../../vpn-gateway/design.md#s2smulti)t használ, bár technikailag lehetséges [pont – hely (P2S) VPN](../../vpn-gateway/point-to-site-about.md)használata. 

    A helyek közötti (S2S) VPN-kapcsolatok az Azure-beli virtuális hálózatot és a szervezet helyszíni hálózatát kötik össze. A S2S VPN-kapcsolat lehetővé teszi, hogy egy VPN-kapcsolatot egyszer, a szervezet hálózatán tárolt VPN-kiszolgálóhoz vagy eszközhöz konfigurálja, és nem minden olyan eszközt, amely az Azure-fájlmegosztás eléréséhez szükséges. A S2S VPN-kapcsolatok üzembe helyezésének egyszerűbbé tételéhez tekintse meg a [helyek közötti (S2S) VPN konfigurálása Azure Fileshoz való használathoz](storage-files-configure-s2s-vpn.md)című témakört.

- [ExpressRoute](../../expressroute/expressroute-introduction.md), amely lehetővé teszi egy meghatározott útvonal létrehozását az Azure és a helyszíni hálózat között, amely nem haladja meg az internetet. Mivel a ExpressRoute egy dedikált útvonalat biztosít a helyszíni adatközpont és az Azure között, a ExpressRoute hasznos lehet, ha a hálózati teljesítmény megfontolásra kerül. A ExpressRoute akkor is jó megoldás, ha a szervezet házirend-vagy szabályozási követelményei determinisztikus elérési utat igényelnek a felhőben lévő erőforrásokhoz.

### <a name="private-endpoints"></a>Privát végpontok
Az alapértelmezett nyilvános végpontokon kívül Azure Files és File Sync biztosítanak a Storage-fiók és a Storage Sync szolgáltatáson keresztül, Azure Files és File Sync lehetővé teszi, hogy erőforráshoz egy vagy több privát végpont legyen. Amikor egy Azure-erőforráshoz hoz létre privát végpontot, a virtuális hálózat címterület a saját IP-címét kapja, hasonlóan ahhoz, hogy a helyszíni Windows-fájlkiszolgáló IP-címmel rendelkezik a helyszíni hálózat dedikált címén belül. 

> [!Important]  
> Ha privát végpontokat szeretne használni a Storage Sync Service-erőforráson, akkor a Azure File Sync Agent 10,1-es vagy újabb verzióját kell használnia. A 10,1 előtti ügynök-verziók nem támogatják a Storage Sync szolgáltatás magánhálózati végpontját. Az összes korábbi ügynök-verzió támogatja a Storage-fiók erőforrásához tartozó magánhálózati végpontokat.

Az egyes privát végpontok egy adott Azure-beli virtuális hálózati alhálózathoz vannak társítva. A Storage-fiókok és a Storage Sync Services több virtuális hálózatban is rendelkezhetnek privát végpontokkal.

A privát végpontok használata a következőket teszi lehetővé:
- Biztonságosan csatlakozhat az Azure-erőforrásokhoz a helyszíni hálózatokból VPN-vagy ExpressRoute-kapcsolaton keresztül.
- Az Azure-erőforrások biztonságossá tételéhez tiltsa le a Azure Files és File Sync nyilvános végpontját. Alapértelmezés szerint a privát végpontok létrehozása nem blokkolja a nyilvános végponthoz való kapcsolódást.
- Növelje a virtuális hálózat biztonságát azáltal, hogy letiltja a virtuális hálózat kiszűrése (és a társítási határokat).

Privát végpont létrehozásával kapcsolatban lásd: [magánhálózati végpontok konfigurálása Azure file Synchoz](storage-sync-files-networking-endpoints.md).

### <a name="private-endpoints-and-dns"></a>Magánhálózati végpontok és DNS
Ha létrehoz egy privát végpontot, a rendszer alapértelmezés szerint létrehoz egy (vagy egy meglévő) magánhálózati DNS-zónát is az `privatelink` altartománynak megfelelően. A nyilvános Felhőbeli régiók esetében ezek a DNS-zónák `privatelink.file.core.windows.net` a Azure Files és `privatelink.afs.azure.net` a Azure file Sync.

> [!Note]  
> Ez a cikk a Storage-fiók DNS-utótagját használja az Azure-beli nyilvános régiókban `core.windows.net` . Ez a kommentár az Azure szuverén felhőkre is vonatkozik, mint például az Azure US government Cloud és az Azure China Cloud – csak a megfelelő utótagokat helyettesíti a környezetében. 

Amikor privát végpontokat hoz létre egy Storage-fiókhoz és egy Storage Sync szolgáltatáshoz, a saját saját DNS-zónájában hozunk létre rekordokat. A nyilvános DNS-bejegyzést úgy is frissítjük, hogy a normál teljes tartománynevek a megfelelő privatelink-nevek CNAME-je legyenek. Ez lehetővé teszi a teljes tartománynevek számára, hogy a magánhálózati végpont IP-címeire mutassanak, amikor a kérelmező a virtuális hálózaton belül van, és a nyilvános végpont IP-címeire mutat, ha a kérelmező a virtuális hálózaton kívül esik. 

A Azure Files esetében minden egyes privát végpont egyetlen teljesen minősített tartománynévvel rendelkezik, a mintát követve, amely `storageaccount.privatelink.file.core.windows.net` a privát végponthoz tartozó magánhálózati IP-címekre van leképezve. A Azure File Sync esetében minden egyes privát végpontnak négy teljes tartományneve van: a következő négy különböző végpontnál, amelyek Azure File Sync teszik elérhetővé: felügyelet, szinkronizálás (elsődleges), szinkronizálás (másodlagos) és figyelés. A végpontok teljes tartományneve általában a Storage Sync szolgáltatás nevét fogja követni, kivéve, ha a név nem ASCII-karaktereket tartalmaz. Ha például a Storage Sync szolgáltatás neve az `mysyncservice` USA 2. nyugati régiójában található, az egyenértékű végpontok a következő:,, `mysyncservicemanagement.westus2.afs.azure.net` `mysyncservicesyncp.westus2.afs.azure.net` `mysyncservicesyncs.westus2.afs.azure.net` és `mysyncservicemonitoring.westus2.afs.azure.net` . A Storage Sync Service minden privát végpontja 4 különböző IP-címet fog tartalmazni. 

Mivel az Azure saját DNS-zónája csatlakoztatva van a privát végpontot tartalmazó virtuális hálózathoz, megfigyelheti a DNS-konfigurációt, ha a `Resolve-DnsName` PowerShell-parancsmagot egy Azure-beli virtuális gépen hívja meg a PowerShell-lel ( `nslookup` Windows és Linux rendszeren):

```powershell
Resolve-DnsName -Name "storageaccount.file.core.windows.net"
```

Ebben a példában a Storage-fiók a `storageaccount.file.core.windows.net` privát végpont magánhálózati IP-címére lesz feloldva, ami a következő: `192.168.0.4` .

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  29    Answer     csostoracct.privatelink.file.core.windows.net
net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 1769
Section    : Answer
IP4Address : 192.168.0.4


Name                   : privatelink.file.core.windows.net
QueryType              : SOA
TTL                    : 269
Section                : Authority
NameAdministrator      : azureprivatedns-host.microsoft.com
SerialNumber           : 1
TimeToZoneRefresh      : 3600
TimeToZoneFailureRetry : 300
TimeToExpiration       : 2419200
DefaultTTL             : 300
```

Ha ugyanezt a parancsot a helyszíni rendszerből futtatja, látni fogja, hogy a Storage-fiók neve a Storage-fiók nyilvános IP-címére lesz feloldva; a `storageaccount.file.core.windows.net` egy CNAME rekord a (z) rendszerhez `storageaccount.privatelink.file.core.windows.net` , amely a Storage-fiókot üzemeltető Azure Storage-fürt CNAME rekordja:

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  60    Answer     storageaccount.privatelink.file.core.windows.net
net
storageaccount.privatelink.file.c CNAME  60    Answer     file.par20prdstr01a.store.core.windows.net
ore.windows.net

Name       : file.par20prdstr01a.store.core.windows.net
QueryType  : A
TTL        : 60
Section    : Answer
IP4Address : 52.239.194.40
```

Ez azt tükrözi, hogy a Azure Files és a Azure File Sync a nyilvános végpontokat és egy vagy több privát végpontot is ki lehet tenni erőforrásként. Annak biztosítása érdekében, hogy az erőforrások teljes tartománynevei a privát végpontok magánhálózati IP-címeire legyenek feloldva, módosítania kell a helyszíni DNS-kiszolgálók konfigurációját. Ez többféle módon is elvégezhető:

- A gazdagépek fájljának módosítása az ügyfeleken, hogy a Storage-fiókok és a Storage Sync Services teljes tartománynevei a kívánt magánhálózati IP-címekre legyenek feloldva. Ez az éles környezetek esetében erősen ellenjavallt, mivel ezeket a módosításokat minden olyan ügyfélnek el kell végeznie, amelynek hozzá kell férnie a privát végpontokhoz. A privát végpontok/erőforrások (törlések, módosítások stb.) módosításai nem lesznek automatikusan kezelve.
- DNS-zónák létrehozása a helyszíni kiszolgálókon és az `privatelink.file.core.windows.net` `privatelink.afs.azure.net` Azure-erőforrások rekordjaival. Ennek előnye, hogy a helyszíni környezetben lévő ügyfelek automatikusan képesek lesznek feloldani az Azure-erőforrásokat anélkül, hogy konfigurálniuk kellene az egyes ügyfeleket, azonban ez a megoldás hasonlóan törékeny a gazdagépek fájljának módosításához, mert a módosítások nem tükröződnek. Bár ez a megoldás törékeny, lehetséges, hogy bizonyos környezetek számára a legjobb választás.
- Továbbítsa a `core.windows.net` és a zónákat a helyszíni DNS- `afs.azure.net` kiszolgálókról az Azure saját DNS-zónájába. Az Azure magánhálózati DNS-gazdagép egy speciális IP-címen () keresztül érhető el `168.63.129.16` , amely csak az Azure magánhálózati DNS-zónához csatolt virtuális hálózatokon belül érhető el. A korlátozás megkerülő megoldásához futtathat további DNS-kiszolgálókat a virtuális hálózaton belül, amelyek továbbítva lesznek `core.windows.net` `afs.azure.net` az egyenértékű Azure-beli magánhálózati DNS-zónákhoz. A beállítás egyszerűsítése érdekében olyan PowerShell-parancsmagokat kaptunk, amelyek az Azure-beli virtuális hálózatban automatikusan telepítik a DNS-kiszolgálókat, és szükség szerint konfigurálja azokat. A DNS-továbbítás beállításával kapcsolatos további tudnivalókért lásd: [a DNS konfigurálása Azure Files](storage-files-networking-dns.md)használatával.

## <a name="encryption-in-transit"></a>Átvitel közbeni titkosítás
A Azure File Sync ügynöktől az Azure-fájlmegosztás vagy a Storage Sync szolgáltatás felé irányuló kapcsolatok mindig titkosítva vannak. Bár az Azure Storage-fiókoknak van egy olyan beállítása, amely letiltja a titkosítást a Azure Files (és a Storage-fiókból felügyelt többi Azure Storage-szolgáltatás) felé irányuló kommunikációhoz, a beállítás letiltásával nem lesz hatással a Azure File Sync titkosítására a Azure Files való kommunikáció során. Alapértelmezés szerint az összes Azure Storage-fióknál engedélyezve van az átvitel titkosítása. 

További információ az átvitel közbeni titkosításról: [biztonságos átvitel megkövetelése az Azure Storage-ban](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="see-also"></a>Lásd még
- [Az Azure File Sync üzembe helyezésének megtervezése](storage-sync-files-planning.md)
- [Az Azure File Sync üzembe helyezése](storage-sync-files-deployment-guide.md)