---
title: Azure File Sync megfontolandó szempontok | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhatja úgy a hálózatokat, hogy Azure File Sync használjanak a fájlok helyszíni gyorsítótárazása érdekében.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 6c761edec571f404a538025c868750bc5712eced
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796991"
---
# <a name="azure-file-sync-networking-considerations"></a>Azure File Sync hálózati szempontok
Az Azure-fájlmegosztáshoz kétféleképpen csatlakozhat:

- A megosztás közvetlen elérése az SMB vagy a FileREST protokollon keresztül. Ezt a hozzáférési mintát elsősorban akkor alkalmazzák, ha a lehető legtöbb helyszíni kiszolgálót ki kell küszöbölni.
- Gyorsítótár létrehozása az Azure-fájlmegosztáshoz egy helyszíni kiszolgálón (vagy egy Azure-beli virtuális gépen) az Azure File Sync-val, és a fájlmegosztás adatainak elérése a helyszíni kiszolgálóról az Ön által választott protokollal (SMB, NFS, FTPS stb.). Ez a hozzáférési minta azért hasznos, mert kombinálja a helyszíni teljesítmény és a felhőbeli méretezés, valamint a kiszolgáló nélküli csatolható szolgáltatások, például a Azure Backup.

Ez a cikk a hálózat konfigurálásával foglalkozik, amikor a Azure File Sync-t használja a fájlok helyszíni gyorsítótárazása érdekében az Azure-fájlmegosztás SMB-kapcsolaton keresztüli közvetlen csatlakoztatása helyett. Az üzembe helyezési környezetek hálózati szempontjaival kapcsolatos további Azure Files lásd: Azure Files [szempontok.](../files/storage-files-networking-overview.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)

A szolgáltatás hálózati Azure File Sync két különböző Azure-objektumra is kitér: egy Storage Sync Service-szolgáltatásra és egy Azure Storage-fiókra. A tárfiókok olyan felügyeleti szerkezetek, amelyek egy megosztott tárolókészletet képviselnek, amelyben több fájlmegosztást, valamint egyéb tárolási erőforrásokat, például blobtárolókat vagy üzenetsorokat helyezhet üzembe. A tárolószinkronizálási szolgáltatás egy olyan felügyeleti szerkezet, amely a regisztrált kiszolgálókat jelöli, amelyek a Azure File Sync-val létrehozott megbízhatósági kapcsolattal és szinkronizálási csoportokkal, amelyek meghatározzák a szinkronizálási kapcsolat topológiáját. 

## <a name="connecting-windows-file-server-to-azure-with-azure-file-sync"></a>Windows-fájlkiszolgáló csatlakoztatása az Azure-hoz Azure File Sync 
Ha helyszíni Windows Azure Files kiszolgálón kell beállítania és használnia a Azure File Sync és az szolgáltatásokat, az alapszintű internetkapcsolaton kívül nincs szükség az Azure-ba történő speciális hálózatra. A Azure File Sync üzembe helyezéséhez telepítse a Azure File Sync ügynököt arra a Windows-fájlkiszolgálóra, amelyről szinkronizálni szeretne az Azure-ral. A Azure File Sync ügynök két csatornán keresztül szinkronizál egy Azure-fájlmegosztással:

- A FileREST protokoll, amely egy HTTPS-alapú protokoll az Azure-fájlmegosztás eléréséhez. Mivel a FileREST protokoll szabványos HTTPS-t használ az adatátvitelhez, csak a 443-as portnak kell elérhetőnek lennie kimenő forgalomhoz. Azure File Sync SMB protokoll használatával nem továbbít adatokat a helyszíni Windows-kiszolgálókról az Azure-fájlmegosztásba.
- A Azure File Sync szinkronizálási protokoll, amely egy HTTPS-alapú protokoll, amely a szinkronizálással kapcsolatos ismeretek, azaz a környezetben lévő fájlok és mappák verzióinformációit cseréli a környezet végpontjai között. Ez a protokoll a környezetben található fájlok és mappák metaadatainak, például az időbélyegek és a hozzáférés-vezérlési listák (ACL-ek) cseréjére is használható. 

Mivel Azure Files szolgáltatás közvetlen SMB protokoll-hozzáférést biztosít az Azure-fájlmegosztások számára, az ügyfelek gyakran azon gondolkodnak, hogy speciális hálózatokat kell-e konfigurálni az Azure-fájlmegosztások SMB-hez való csatlakoztatásához, hogy az Azure File Sync-ügynök hozzáfér. Ez nem csupán szükséges, de nem ajánlott, kivéve a rendszergazdai forgatókönyveket, mivel nem történt gyors módosításészlelés a közvetlenül az Azure-fájlmegosztáson végrehajtott módosításokon (előfordulhat, hogy a módosítások az Azure-fájlmegosztás méretétől és számától függően 24 óránál tovább nem lesznek felderítve). Ha közvetlenül szeretné használni az Azure-fájlmegosztást, azaz nem a Azure File Sync-t szeretné használni a helyszíni gyorsítótárazáshoz, a közvetlen hozzáférés hálózati szempontjairól a hálózati áttekintés áttekintésében Azure Files [olvashat.](../files/storage-files-networking-overview.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)

Bár Azure File Sync nincs szükség speciális hálózati konfigurációra, egyes ügyfelek speciális hálózati beállításokat is konfigurálhatnak a következő forgatókönyvek engedélyezéséhez:

- A szervezet proxykiszolgáló-konfigurációjának interaktivitása.
- Nyissa meg a szervezet helyszíni tűzfalát a Azure Files és Azure File Sync számára.
- ExpressRoute Azure Files VPN Azure File Sync kapcsolaton keresztüli alagút- és hálózati Azure File Sync keresztül.

### <a name="configuring-proxy-servers"></a>Proxykiszolgálók konfigurálása
Számos szervezet proxykiszolgálót használ közvetítőként a helyszíni hálózatban lévő erőforrások és a hálózaton kívüli, például az Azure-ban lévő erőforrások között. A proxykiszolgálók számos alkalmazás, például a hálózatelszigetelés és -biztonság, valamint a figyelés és naplózás esetében hasznosak. Azure File Sync proxykiszolgálóval teljes mértékben együttműködhet, azonban manuálisan kell konfigurálnia a proxyvégpont beállításait a környezethez a Azure File Sync. Ezt a PowerShellen keresztül kell Azure File Sync kiszolgáló parancsmagokkal. 

További információ a proxykiszolgálóval Azure File Sync konfigurálásával kapcsolatban: [Configuring Azure File Sync with a proxy server](file-sync-firewall-and-proxy.md)(Proxykiszolgálóval való kapcsolat konfigurálása).

### <a name="configuring-firewalls-and-service-tags"></a>Tűzfalak és szolgáltatáscímkék konfigurálása
Biztonsági okokból elkülönítheti a fájlkiszolgálókat a legtöbb internetes helyről. Ha a Azure File Sync használni a környezetében, úgy kell beállítania a tűzfalat, hogy az azure-szolgáltatások kiválasztásához megnyitja azt. Ezt a szükséges szolgáltatások IP-címtartományának a szolgáltatáscímkék nevű mechanizmussal való [lekért értékével teheti meg.](../../virtual-network/service-tags-overview.md)

Azure File Sync szolgáltatáscímkéik alapján a következő szolgáltatások IP-címtartományaira van szükség:

| Szolgáltatás | Leírás | Szolgáltatáscímke |
|---------|-------------|-------------|
| Azure File Sync | A Azure File Sync szolgáltatás felelős az Azure-fájlmegosztások és a Windows-fájlkiszolgálók közötti adatszinkronizálás alapvető tevékenységéért. | `StorageSyncService` |
| Azure Files | Az azure-fájlmegosztás Azure File Sync összes adat azure-fájlmegosztásban van tárolva. A Windows-fájlkiszolgálón módosított fájlok replikálva vannak az Azure-fájlmegosztásba, és a helyszíni fájlkiszolgálón rétegzett fájlok zökkenőmentesen letöltődnek, amikor egy felhasználó kéri őket. | `Storage` |
| Azure Resource Manager | A Azure Resource Manager az Azure felügyeleti felülete. Az összes felügyeleti hívás, beleértve Azure File Sync kiszolgáló regisztrációját és a szinkronizálási kiszolgáló folyamatban lévő feladatait is, az Azure Resource Manager. | `AzureResourceManager` |
| Azure Active Directory | Azure Active Directory vagy Az Azure AD tartalmazza azokat a rendszerbiztonsági tagokat, amelyek a kiszolgálói regisztrációnak a Storage Sync Service-be való jogosultságához szükségesek, valamint azokat a szolgáltatásnéveket, amelyek ahhoz szükségesek, hogy az Azure File Sync jogosult legyen a felhőbeli erőforrások eléréséhez. | `AzureActiveDirectory` |

Ha az Azure-Azure File Sync használja, akkor a szolgáltatáscímke nevét közvetlenül a hálózati biztonsági csoportban is használhatja, hogy engedélyezze a szolgáltatáshoz való forgalmat. További információ erről: Hálózati [biztonsági csoportok.](../../virtual-network/network-security-groups-overview.md) 

Ha helyszíni Azure File Sync használ, a szolgáltatáscímke API-jának használatával lekért konkrét IP-címtartományokat a tűzfala engedélyező listájához. Ezt az információt két módszerrel lehet lekérte:

- A szolgáltatáscímkéket támogató Azure-szolgáltatások jelenlegi IP-címtartományai hetente megjelenik a Microsoft letöltőközpontban egy JSON-dokumentum formájában. Minden Azure-felhő saját JSON-dokumentummal rendelkezik az adott felhőhöz kapcsolódó IP-címtartományokkal:
    - [Nyilvános Azure](https://www.microsoft.com/download/details.aspx?id=56519)
    - [Azure US Government](https://www.microsoft.com/download/details.aspx?id=57063)
    - [Azure China](https://www.microsoft.com/download/details.aspx?id=57062)
    - [Azure Germany](https://www.microsoft.com/download/details.aspx?id=57064)
- A szolgáltatáscímke-felderítési API (előzetes verzió) lehetővé teszi a szolgáltatáscímkék aktuális listájának programozott lekérését. Előzetes verzióban a szolgáltatáscímke-felderítési API olyan információkat is visszaadhat, amelyek kevésbé aktuálisak, mint a Microsoft letöltőközpontban közzétett JSON-dokumentumokból visszaadott adatok. Az API-felületet az automatizálási beállítások alapján használhatja:
    - [REST API](/rest/api/virtualnetwork/servicetags/list)
    - [Azure PowerShell](/powershell/module/az.network/Get-AzNetworkServiceTag)
    - [Azure CLI](/cli/azure/network#az_network_list_service_tags)

Ha többet szeretne megtudni arról, hogyan használhatja a szolgáltatáscímke API-t a szolgáltatások címeinek lekérésére, tekintse meg az Ip-címek [listájának Azure File Sync cikkét.](file-sync-firewall-and-proxy.md#allow-list-for-azure-file-sync-ip-addresses)

### <a name="tunneling-traffic-over-a-virtual-private-network-or-expressroute"></a>Forgalom bújtatása virtuális magánhálózaton vagy ExpressRoute-hálózaton keresztül
Egyes szervezeteknek az Azure-ral való kommunikációra van szükségük ahhoz, hogy egy hálózati alagúton, például virtuális magánhálózaton (VPN) vagy ExpressRoute-kapcsolaton haladva egy további biztonsági réteget használjanak, vagy hogy az Azure-ral való kommunikáció egy determinisztikus útvonalat követ. 

Amikor hálózati alagutat hoz létre a helyszíni hálózat és az Azure között, társviszonyt létesít a helyszíni hálózattal egy vagy több Azure-beli virtuális hálózattal. A [virtuális hálózatok](../../virtual-network/virtual-networks-overview.md)( vagy virtuális hálózatok) hasonlóak a hagyományos hálózatokhoz, amelyek a helyszínen üzemelnek. Az Azure Storage-fiókhoz vagy egy Azure-beli virtuális géphez hasonló virtuális hálózat egy erőforráscsoportban üzembe helyezett Azure-erőforrás. 

Azure Files és File Sync a következő mechanizmusokat a helyszíni kiszolgálók és az Azure közötti forgalom bújtatásához:

- [Azure VPN Gateway:](../../vpn-gateway/vpn-gateway-about-vpngateways.md)A VPN-átjáró egy adott típusú virtuális hálózati átjáró, amely titkosított adatforgalmat küld egy Azure-beli virtuális hálózat és egy másik hely (például helyszíni) között az interneten keresztül. Az Azure VPN Gateway egy olyan Azure-erőforrás, amely üzembe helyezhető egy erőforráscsoportban egy tárfiók vagy más Azure-erőforrás mellett. Mivel a Azure File Sync helyszíni Windows-fájlkiszolgálóval való használatra szolgál, általában egy pont–hely [(S2S) VPN-t](../../vpn-gateway/design.md#s2smulti)használna, bár technikailag pont–hely [(P2S) VPN](../../vpn-gateway/point-to-site-about.md)is használható. 

    A hely–hely (S2S) VPN-kapcsolatok összekapcsolják az Azure-beli virtuális hálózatot és a szervezet helyszíni hálózatát. A S2S VPN-kapcsolat lehetővé teszi, hogy egyszer konfigurálja a VPN-kapcsolatot a szervezet hálózatán üzemeltetett VPN-kiszolgálóhoz vagy eszközhöz ahelyett, hogy minden olyan ügyféleszközt használ, amely hozzáfér az Azure-fájlmegosztáshoz. A virtuális hálózati kapcsolatok üzembe helyezésének egyszerűsítéséhez S2S VPN a Hely–hely [(S2S) VPN](../files/storage-files-configure-s2s-vpn.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)konfigurálása a Azure Files.

- [ExpressRoute,](../../expressroute/expressroute-introduction.md)amely lehetővé teszi egy meghatározott útvonal létrehozására az Azure és a helyszíni hálózat között, amely nem áthalad az interneten. Mivel az ExpressRoute dedikált útvonalat biztosít a helyszíni adatközpont és az Azure között, az ExpressRoute hasznos lehet, ha figyelembe veszi a hálózati teljesítményt. Az ExpressRoute akkor is jó választás, ha a szervezet szabályzati vagy szabályozási követelményei determinisztikus útvonalat igényelnek a felhőben található erőforrásokhoz.

### <a name="private-endpoints"></a>Privát végpontok
Az alapértelmezett nyilvános végpontok Azure Files és File Sync a tárfiókon és a Szinkronizálási szolgáltatáson keresztül biztosítanak hozzáférést, a Azure Files és a File Sync erőforrásonként egy vagy több privát végpontot is biztosít. Amikor privát végpontot hoz létre egy Azure-erőforráshoz, az egy magánhálózati IP-címet kap a virtuális hálózat címterületén belül, akárcsak a helyszíni Windows-fájlkiszolgáló IP-címe a helyszíni hálózat dedikált címtere alatt. 

> [!Important]  
> Ha privát végpontokat kell használnia a Storage Sync Service-erőforráson, a Azure File Sync 10.1-es vagy újabb verzióját kell használnia. A 10.1-es előtti ügynökverziók nem támogatják a privát végpontokat a Storage Sync Service-ben. Az összes korábbi ügynökverzió támogatja a privát végpontokat a tárfiók-erőforráson.

Egy adott privát végpont egy adott Azure-beli virtuális hálózat alhálózatához van társítva. A tárfiókok és a Társzinkronizálási szolgáltatások több virtuális hálózat privát végpontjaival is lehetnek.

A privát végpontok használatával a következőt teszi lehetővé:
- Biztonságosan csatlakozhat Azure-erőforrásaihoz a helyszíni hálózatokról VPN- vagy ExpressRoute-kapcsolattal privát társviszony-létesítés használatával.
- Az Azure-erőforrások biztonságának biztosítása érdekében tiltsa le a nyilvános végpontokat a Azure Files és File Sync. A privát végpontok létrehozása alapértelmezés szerint nem tiltja le a nyilvános végponttal való kapcsolatokat.
- A virtuális hálózat biztonságának növelése azáltal, hogy lehetővé teszi a virtuális hálózatból (és a társviszony-létesítés határain) származó adatok kiszivárgásának blokkolását.

Privát végpont létrehozásához lásd: [Configuring private endpoints for Azure File Sync.](file-sync-networking-endpoints.md)

### <a name="private-endpoints-and-dns"></a>Privát végpontok és DNS
Privát végpont létrehozásakor alapértelmezés szerint egy, az altartománynak megfelelő privát DNS-zónát is létrehozunk (vagy `privatelink` frissítünk egy meglévőt). A nyilvános felhőrégiók esetében ezek a DNS-zónák az Azure Files és a `privatelink.file.core.windows.net` `privatelink.afs.azure.net` Azure File Sync.

> [!Note]  
> Ez a cikk a tárfiók DNS-utótagját használja a nyilvános Azure-régiókhoz(). `core.windows.net` Ez a megjegyzés a szuverén Azure-felhőkre, például az USA kormányzati felhőjére és az Azure China-felhőre is vonatkozik– csak helyettesítse be a környezetének megfelelő utótagokat. 

Amikor privát végpontokat hoz létre egy tárfiókhoz és egy Storage Sync Service-hez, A rekordokat hozunk létre számukra a saját DNS-zónáikban. A nyilvános DNS-bejegyzést is frissítjük úgy, hogy a normál teljes tartománynevek a megfelelő privát hivatkozásnévhez tartozó CNAME-k. Ez lehetővé teszi, hogy a teljes tartománynevek a privát végpont IP-címére mutasson, amikor a kérelmező a virtuális hálózaton belül van, és hogy a nyilvános végpont IP-címére mutasson, amikor a kérelmező a virtuális hálózaton kívül van. 

A Azure Files esetén minden privát végpont egyetlen teljes tartománynévvel rendelkezik, amely a mintát követi, és a magánhálózati végpont egy magánhálózati `storageaccount.privatelink.file.core.windows.net` IP-címére van leképezve. A Azure File Sync esetén minden privát végpont négy teljes tartománynévvel rendelkezik, a Azure File Sync által elérhető négy végponthoz: felügyelet, szinkronizálás (elsődleges), szinkronizálás (másodlagos) és figyelés. A végpontok teljes tartománynevei általában a Storage Sync Service nevét követik, kivéve, ha a név nem ASCII-karaktereket tartalmaz. Ha például a Társzinkronizálási szolgáltatás neve az USA 2. nyugati régiójában van, az egyenértékű végpontok a `mysyncservice` következőek lehetnek: `mysyncservicemanagement.westus2.afs.azure.net` , , és `mysyncservicesyncp.westus2.afs.azure.net` `mysyncservicesyncs.westus2.afs.azure.net` `mysyncservicemonitoring.westus2.afs.azure.net` . A Tárolószinkronizálási szolgáltatás minden privát végpontja 4 különböző IP-címet fog tartalmazni. 

Mivel az Azure privát DNS-zóna a privát végpontot tartalmazó virtuális hálózathoz csatlakozik, a DNS-konfigurációt a PowerShell-parancsmag Azure-beli virtuális gépen való hívásával figyelheti meg (alternatív módon Windows és `Resolve-DnsName` `nslookup` Linux rendszeren):

```powershell
Resolve-DnsName -Name "storageaccount.file.core.windows.net"
```

Ebben a példában a tárfiók a privát végpont magánhálózati IP-címére lesz `storageaccount.file.core.windows.net` feloldva, amely véletlenül a `192.168.0.4` következő: .

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

Ha ugyanezt a parancsot a helyszínen futtatja, láthatja, hogy ugyanaz a tárfióknév feloldódik a tárfiók nyilvános IP-címére; A a CNAME rekordja, amely pedig a tárfiókot üzemeltető `storageaccount.file.core.windows.net` `storageaccount.privatelink.file.core.windows.net` Azure Storage-fürt CNAME rekordja:

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

Ez azt a tényt tükrözi, Azure Files Azure File Sync nyilvános végpontokat és erőforrásonként egy vagy több privát végpontot is elérhetővé tudnak tenni. Ahhoz, hogy az erőforrások teljes tartományneveit a privát végpontok magánhálózati IP-címeire oldja fel, módosítania kell a konfigurációt a helyszíni DNS-kiszolgálókon. Ez többféleképpen is meg lehet valósítható:

- Módosítsa az ügyfelek gazdagépfájlját úgy, hogy a tárfiókok és a Storage Sync Services teljes tartományneveit a kívánt magánhálózati IP-címekre oldja fel. Ez éles környezetekben erősen ajánlott, mivel ezeket a módosításokat minden olyan ügyfélen végre kell majd tenni, amely hozzá fog férni a privát végpontjaihoz. A privát végpontok/erőforrások (törlés, módosítások stb.) módosításait a rendszer nem kezeli automatikusan.
- DNS-zónák létrehozása a helyszíni kiszolgálókon az `privatelink.file.core.windows.net` Azure-erőforrások A rekordjainak `privatelink.afs.azure.net` használatával. Ennek az az előnye, hogy a helyszíni környezetben található ügyfelek képesek lesznek automatikusan feloldani az Azure-erőforrásokat anélkül, hogy mindegyik ügyfelet konfigurálni kellene, azonban ez a megoldás hasonlóképpen nem tudja módosítani a gazdagépfájlt, mert a módosítások nem jelennek meg. Bár ez a megoldás igöreg, egyes környezetekben ez lehet a legjobb választás.
- Továbbítja a és a zónát a helyszíni `core.windows.net` `afs.azure.net` DNS-kiszolgálókról az Azure privát DNS-zónába. Az Azure privát DNS-gazdagép egy speciális IP-címen ( ) keresztül érhető el, amely csak az Azure privát DNS-zónához kapcsolt virtuális `168.63.129.16` hálózatokon belül érhető el. A korlátozás megkerülése érdekében további DNS-kiszolgálókat futtathat a virtuális hálózaton belül, amelyek továbbítják és továbbítják a megfelelő Azure privát `core.windows.net` `afs.azure.net` DNS-zónákat. A beállítás leegyszerűsítése érdekében Olyan PowerShell-parancsmagokat biztosítunk, amelyek automatikusan üzembe helyeznek DNS-kiszolgálókat az Azure-beli virtuális hálózatban, és szükség szerint konfigurálják azokat. A DNS-továbbítás beállításával kapcsolatos információkért lásd: A DNS konfigurálása [a Azure Files.](../files/storage-files-networking-dns.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)

## <a name="encryption-in-transit"></a>Átvitel közbeni titkosítás
Az azure Azure File Sync-fájlmegosztással vagy a Storage Sync Service-sel létesített kapcsolatok mindig titkosítva vannak. Bár az Azure Storage-fiókokban van egy beállítás, amely letiltja a titkosítás megkövetelását az Azure Files-val (és a tárfiókon keresztül kezelt többi Azure Storage-szolgáltatáshoz) való kommunikációhoz, a beállítás letiltása nem befolyásolja a Azure File Sync titkosítását az Azure Files-val való kommunikáció során. Alapértelmezés szerint minden Azure Storage-fiókon engedélyezve van az átvitel során történő titkosítás. 

További információ az átvitel közbeni titkosításról: Biztonságos [átvitel megkövetelve az Azure Storage-ban.](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

## <a name="see-also"></a>Lásd még
- [Az Azure File Sync üzembe helyezésének megtervezése](file-sync-planning.md)
- [Az Azure File Sync üzembe helyezése](file-sync-deployment-guide.md)