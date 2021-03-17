---
title: Azure Files hálózatkezelési megfontolások | Microsoft Docs
description: A Azure Files hálózati beállításainak áttekintése.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 02/22/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: cec386b798b843a5badc9d52d9c71bd7df54b59a
ms.sourcegitcommit: 27cd3e515fee7821807c03e64ce8ac2dd2dd82d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103601933"
---
# <a name="azure-files-networking-considerations"></a>Azure Files hálózati megfontolások 
Az Azure-fájlmegosztás két módon is kapcsolódhat:

- A megosztás közvetlenül a Server Message Block (SMB), a Network File System (NFS) (előzetes verzió) vagy a FileR protokoll használatával érhető el. Ez a hozzáférési minta elsősorban akkor alkalmazható, ha a lehető legtöbb helyszíni kiszolgálót el szeretné távolítani.
- Az Azure-fájlmegosztás gyorsítótárának létrehozása egy helyszíni kiszolgálón (vagy egy Azure-beli virtuális gépen) Azure File Sync használatával, valamint a fájlmegosztás adatainak elérése a helyszíni kiszolgálóról az Ön által választott protokollal (SMB, NFS, FTPS stb.) a használati esethez. Ez a hozzáférési minta hasznos, mivel az a legjobbat ötvözi a helyszíni teljesítmény és a Felhőbeli skálázás, valamint a kiszolgáló nélküli (például Azure Backup) szolgáltatások számára.

Ez a cikk azt ismerteti, hogyan kell konfigurálni a hálózatkezelést, ha a használati eset a Azure File Sync használata helyett közvetlenül az Azure-fájlmegosztás elérését kéri. A Azure File Sync központi telepítés hálózatkezelési szempontjaival kapcsolatos további információkért lásd: [Azure file Sync hálózatkezelési megfontolások](storage-sync-files-networking-overview.md).

Az Azure-fájlmegosztás hálózati konfigurációja az Azure Storage-fiókon történik. A Storage-fiók egy olyan felügyeleti szerkezet, amely egy megosztott tárolót jelöl, amelyben több fájlmegosztást is üzembe helyezhet, valamint más tárolási erőforrásokat, például blob-tárolókat vagy várólistákat. A Storage-fiókok több olyan beállítást tesznek elérhetővé, amelyek segítenek a fájlmegosztás hálózati hozzáférésének biztonságossá tételében: hálózati végpontok, a Storage-fiók tűzfalának beállításai és az átvitel közbeni titkosítás. 

Javasoljuk, hogy a fogalmi útmutató elolvasása előtt olvassa el a [Azure Files központi telepítésének megtervezését](storage-files-planning.md) .

:::row:::
    :::column:::
        <iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/jd49W33DxkQ" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    :::column-end:::
    :::column:::
        Ebből a videóból megtudhatja, hogyan teheti lehetővé az Azure-fájlmegosztás közvetlen biztonságos kihelyezését az információkkal dolgozó szakemberek és alkalmazások számára öt egyszerű lépésben. Az alábbi szakasz hivatkozásokat és további kontextust biztosít a videóban hivatkozott dokumentációhoz.
   :::column-end:::
:::row-end:::

## <a name="accessing-your-azure-file-shares"></a>Az Azure-fájlmegosztás elérése
Amikor egy Azure-fájlmegosztást telepít egy Storage-fiókon belül, a fájlmegosztás azonnal elérhető lesz a Storage-fiók nyilvános végpontján keresztül. Ez azt jelenti, hogy a hitelesített kérések, például a felhasználó bejelentkezési identitása által engedélyezett kérések, biztonságosan származhatnak az Azure-on belül vagy kívül is. 

Számos felhasználói környezetben a helyszíni munkaállomás Azure-fájlmegosztás kezdeti csatlakoztatása sikertelen lesz, noha az Azure-beli virtuális gépekről való csatlakoztatások sikeresek lesznek. Ennek az az oka, hogy számos szervezet és internetszolgáltató (ISP) blokkolja az SMB által a kommunikációhoz használt portot, a 445-es portot. Az NFS-megosztások nem rendelkeznek ezzel a hibával. Ez a gyakorlat az SMB protokoll örökölt és elavult verzióira vonatkozó biztonsági útmutatásból származik. Bár az SMB 3,0 egy internet-biztonságos protokoll, az SMB régebbi verziói, különösen az SMB 1,0. Az Azure-fájlmegosztás csak az SMB 3,0-n keresztül érhető el, és a legbiztonságosabb protokoll (amely szintén egy internetes biztonságos protokoll) a nyilvános végponton keresztül.

Mivel az Azure SMB-fájlmegosztás a helyszínen való elérésének legegyszerűbb módja a helyszíni hálózat megnyitása a 445-es portra, a Microsoft a következő lépéseket javasolja az SMB 1,0 eltávolításához a környezetből:

1. Győződjön meg arról, hogy az SMB 1,0 el lett távolítva vagy le van tiltva a szervezet eszközein. A Windows és a Windows Server összes jelenleg támogatott verziója támogatja az SMB 1,0 eltávolítását vagy letiltását, és a Windows 10-es verziótól kezdődően a 1709-es verziójától kezdve az SMB 1,0 nincs telepítve a Windows rendszerben alapértelmezés szerint. Ha többet szeretne megtudni az SMB 1,0 letiltásáról, tekintse meg az operációs rendszerre vonatkozó oldalakat:
    - [A Windows/Windows Server védelme](storage-how-to-use-files-windows.md#securing-windowswindows-server)
    - [A Linux biztonságossá tétele](storage-how-to-use-files-linux.md#securing-linux)
1. Győződjön meg arról, hogy a szervezeten belül egyetlen termék sem igényel SMB 1,0-t, és távolítsa el azokat. Egy SMB1- [adatközpontot](https://aka.ms/stillneedssmb1)tartunk fenn, amely tartalmazza az összes, a Microsoft számára ismert, az SMB 1,0-t megkövetelő, első és harmadik féltől származó terméket. 
1. Választható Külső gyártótól származó tűzfalat használhat a szervezete helyszíni hálózatán, hogy megakadályozza az SMB 1,0-forgalomnak a szervezeti határának elhagyását.

Ha a szervezete megköveteli a 445-as port használatát házirend vagy szabályozás miatt, vagy ha a szervezete az Azure-ba irányuló adatforgalmat igényli a determinisztikus elérési útjának követéséhez, használhatja az Azure VPN Gateway vagy a ExpressRoute-t az Azure-fájlmegosztás forgalmának bújtatásához. Az NFS-megosztások nem igénylik ezt, mert nincs szükségük a 445-es portra.

> [!Important]  
> Még ha úgy dönt, hogy alternatív módszert használ az Azure-fájlmegosztás eléréséhez, a Microsoft továbbra is javasolja az SMB 1,0 eltávolítását a környezetből.

### <a name="tunneling-traffic-over-a-virtual-private-network-or-expressroute"></a>Virtuális magánhálózat vagy ExpressRoute feletti forgalom bújtatása
Amikor hálózati alagutat hoz létre a helyszíni hálózat és az Azure között, a helyszíni hálózatot egy vagy több Azure-beli virtuális hálózattal társítja. Egy [virtuális hálózat](../../virtual-network/virtual-networks-overview.md)vagy VNet hasonló egy hagyományos hálózathoz, amelyet a helyszínen fog üzemeltetni. Az Azure Storage-fiókokhoz vagy az Azure-beli virtuális gépekhez hasonlóan a VNet egy erőforráscsoport-ban üzembe helyezett Azure-erőforrás. 

A Azure Files a következő mechanizmusokat támogatja a helyszíni munkaállomások és-kiszolgálók, valamint az Azure SMB/NFS-fájlmegosztás közötti forgalom bújtatásához:

- [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md): a VPN Gateway egy adott típusú virtuális hálózati átjáró, amely egy Azure-beli virtuális hálózat és egy másik hely (például a helyszíni) közötti titkosított forgalom küldésére szolgál az interneten keresztül. Az Azure VPN Gateway egy olyan Azure-erőforrás, amely egy, a Storage-fiók vagy más Azure-erőforrás melletti erőforráscsoporthoz is telepíthető. A VPN-átjárók két különböző típusú kapcsolatot tesznek elérhetővé:
    - [Pont – hely (P2S) VPN](../../vpn-gateway/point-to-site-about.md) Gateway-kapcsolatok, amelyek az Azure és az egyes ügyfelek közötti VPN-kapcsolatok. Ez a megoldás elsősorban olyan eszközök esetében hasznos, amelyek nem részei a szervezete helyszíni hálózatának, például a távmunkát, akik az Azure-fájlmegosztás otthonról, kávézóból vagy hotelből való csatlakoztatására szeretnének. Ha a P2S VPN-kapcsolatot Azure Files használatával szeretné használni, konfigurálnia kell egy P2S VPN-kapcsolatot minden olyan ügyfél számára, amelyhez csatlakozni szeretne. A P2S VPN-kapcsolat központi telepítésének egyszerűsítése érdekében tekintse meg a [pont – hely (P2S) VPN konfigurálása a Azure Files Windows](storage-files-configure-p2s-vpn-windows.md) rendszeren, és [konfigurálja a pont – hely (P2S) VPN-t a Linux rendszeren a Azure Files használatával való használatra](storage-files-configure-p2s-vpn-linux.md).
    - [Helyek közötti (S2S) VPN](../../vpn-gateway/design.md#s2smulti), amely az Azure és a szervezet hálózata közötti VPN-kapcsolat. A S2S VPN-kapcsolat lehetővé teszi, hogy egy VPN-kapcsolatot egyszer, a szervezet hálózatán tárolt VPN-kiszolgálóhoz vagy eszközhöz konfigurálja, és nem minden olyan eszközt, amely az Azure-fájlmegosztás eléréséhez szükséges. A S2S VPN-kapcsolatok üzembe helyezésének egyszerűbbé tételéhez tekintse meg a [helyek közötti (S2S) VPN konfigurálása Azure Fileshoz való használathoz](storage-files-configure-s2s-vpn.md)című témakört.
- [ExpressRoute](../../expressroute/expressroute-introduction.md), amely lehetővé teszi egy meghatározott útvonal létrehozását az Azure és a helyszíni hálózat között, amely nem haladja meg az internetet. Mivel a ExpressRoute egy dedikált útvonalat biztosít a helyszíni adatközpont és az Azure között, a ExpressRoute hasznos lehet, ha a hálózati teljesítmény megfontolásra kerül. A ExpressRoute akkor is jó megoldás, ha a szervezet házirend-vagy szabályozási követelményei determinisztikus elérési utat igényelnek a felhőben lévő erőforrásokhoz.

Függetlenül attól, hogy melyik bújtatási módszert használja az Azure-fájlmegosztás eléréséhez, olyan mechanizmusra van szükség, amely biztosítja, hogy a Storage-fiók felé irányuló forgalom a normál internetkapcsolat helyett az alagúton haladjon át. Technikailag lehetséges a Storage-fiók nyilvános végpontjának átirányítása, azonban ehhez az szükséges, hogy az Azure Storage-fürtök összes IP-címének kódolása egy adott régióban történjen, mivel a Storage-fiókok bármikor áthelyezhetők a Storage-fürtök között. Ehhez az IP-címek leképezésének folyamatos frissítésére is szükség van, mivel az új fürtöket a rendszer mindig hozzáadja.

A Storage-fiókok IP-címének a VPN-útválasztási szabályokba való kódolása helyett azt javasoljuk, hogy használjon privát végpontokat, amelyek lehetővé teszik, hogy a Storage-fiók egy Azure-beli virtuális hálózat címterület IP-címét adja meg. Mivel az Azure-hoz létrehozott alagút létrehozza a helyszíni hálózat és egy vagy több virtuális hálózat közötti társítást, ez lehetővé teszi a megfelelő útválasztást tartós módon.

### <a name="private-endpoints"></a>Privát végpontok
A Storage-fiók alapértelmezett nyilvános végpontján kívül Azure Files biztosít egy vagy több privát végpontot. A privát végpontok olyan végpontok, amelyek csak egy Azure-beli virtuális hálózaton belül érhetők el. Amikor létrehoz egy privát végpontot a Storage-fiókjához, a Storage-fiók egy magánhálózati IP-címet kap a virtuális hálózat címterület-címéről, hasonlóan ahhoz, hogy a helyszíni fájlkiszolgáló vagy a NAS-eszköz IP-címet kapjon a helyszíni hálózat dedikált címterület területén. 

Az egyes privát végpontok egy adott Azure-beli virtuális hálózati alhálózathoz vannak társítva. A Storage-fiók több virtuális hálózatban is rendelkezhet privát végpontokkal.

A Azure Files a privát végpontok használata lehetővé teszi a következőket:
- Az Azure-fájlmegosztás biztonságos csatlakoztatása a helyszíni hálózatokból VPN-vagy ExpressRoute-kapcsolaton keresztül.
- Az Azure-fájlmegosztás biztonságossá tételéhez konfigurálja a Storage-fiók tűzfalát a nyilvános végponton lévő összes kapcsolat blokkolásához. Alapértelmezés szerint a privát végpontok létrehozása nem blokkolja a nyilvános végponthoz való kapcsolódást.
- Növelje a virtuális hálózat biztonságát azáltal, hogy letiltja a virtuális hálózat kiszűrése (és a társítási határokat).

Privát végpont létrehozásával kapcsolatban lásd: [magánhálózati végpontok konfigurálása Azure Fileshoz](storage-files-networking-endpoints.md).

### <a name="private-endpoints-and-dns"></a>Magánhálózati végpontok és DNS
Ha létrehoz egy privát végpontot, a rendszer alapértelmezés szerint létrehoz egy (vagy egy meglévő) magánhálózati DNS-zónát is az `privatelink` altartománynak megfelelően. Szigorúan véve a privát DNS-zónák létrehozásához nem szükséges privát végpontot használni a Storage-fiókhoz, de általában kifejezetten ajánlott és explicit módon szükséges, amikor az Azure-fájlmegosztást egy Active Directory felhasználó rendszerbiztonsági tagjával csatlakoztatja, vagy a legszigorúbb API-val fér hozzá.

> [!Note]  
> Ez a cikk a Storage-fiók DNS-utótagját használja az Azure-beli nyilvános régiókban `core.windows.net` . Ez a kommentár az Azure szuverén felhőkre is vonatkozik, mint például az Azure US government Cloud és az Azure China Cloud – csak a megfelelő utótagokat helyettesíti a környezetében. 

A saját DNS-zónában létrehozunk egy rekordot a `storageaccount.privatelink.file.core.windows.net` és egy CNAME-rekord számára a Storage-fiók normál neveként, amely a mintát követi `storageaccount.file.core.windows.net` . Mivel az Azure saját DNS-zónája csatlakoztatva van a privát végpontot tartalmazó virtuális hálózathoz, megfigyelheti a DNS-konfigurációt, ha a `Resolve-DnsName` PowerShell-parancsmagot egy Azure-beli virtuális gépen hívja meg a PowerShell-lel ( `nslookup` Windows és Linux rendszeren):

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

Ez azt a tényt mutatja be, hogy a Storage-fiók a nyilvános végpontot és egy vagy több privát végpontot is képes kitenni. Annak biztosítása érdekében, hogy a Storage-fiók neve a privát végpont magánhálózati IP-címére legyen feloldva, módosítania kell a helyszíni DNS-kiszolgálók konfigurációját. Ez többféle módon is elvégezhető:

- A gazdagépek fájljának módosítása az ügyfeleken a `storageaccount.file.core.windows.net` kívánt privát végpont magánhálózati IP-címének feloldásához. Ez az éles környezetek esetében nem ajánlott, mivel ezeket a módosításokat minden olyan ügyfélnek el kell végeznie, amely csatlakoztatni kívánja az Azure-fájlmegosztást, és a Storage-fiók vagy a privát végpont módosításait nem fogja automatikusan kezelni.
- Rekord létrehozása a helyszíni `storageaccount.file.core.windows.net` DNS-kiszolgálókhoz. Ennek előnye, hogy a helyszíni környezetben lévő ügyfelek automatikusan feloldják a Storage-fiókot anélkül, hogy konfigurálniuk kellene az egyes ügyfeleket, azonban ez a megoldás hasonlóan törékeny a gazdagépek fájljának módosításához, mert a módosítások nem tükröződnek. Bár ez a megoldás törékeny, lehetséges, hogy bizonyos környezetek számára a legjobb választás.
- Továbbítsa a `core.windows.net` zónát a helyszíni DNS-kiszolgálókról az Azure saját DNS-zónájába. Az Azure magánhálózati DNS-gazdagép egy speciális IP-címen () keresztül érhető el `168.63.129.16` , amely csak az Azure magánhálózati DNS-zónához csatolt virtuális hálózatokon belül érhető el. A korlátozás megkerülő megoldásához futtathat további DNS-kiszolgálókat a virtuális hálózaton belül, amelyek továbbítva lesznek `core.windows.net` Az Azure-beli magánhálózati DNS-zónába. A beállítás egyszerűsítése érdekében olyan PowerShell-parancsmagokat kaptunk, amelyek az Azure-beli virtuális hálózatban automatikusan telepítik a DNS-kiszolgálókat, és szükség szerint konfigurálja azokat. A DNS-továbbítás beállításával kapcsolatos további tudnivalókért lásd: [a DNS konfigurálása Azure Files](storage-files-networking-dns.md)használatával.

## <a name="storage-account-firewall-settings"></a>A Storage-fiók tűzfalának beállításai
A tűzfal egy hálózati házirend, amely azt szabályozza, hogy mely kérések férhetnek hozzá a nyilvános végponthoz egy Storage-fiókhoz. A Storage-fiók tűzfala segítségével a Storage-fiók nyilvános végpontján bizonyos IP-címekhez, tartományokhoz vagy virtuális hálózatokhoz is korlátozhatja a hozzáférést. Általánosságban elmondható, hogy egy Storage-fiókhoz a legtöbb tűzfal-házirend korlátozza a hálózati hozzáférést egy vagy több virtuális hálózathoz. 

A Storage-fiókokhoz való hozzáférés korlátozásának két módja van:
- Hozzon létre egy vagy több privát végpontot a Storage-fiókhoz, és korlátozza a nyilvános végponthoz való hozzáférést. Ez biztosítja, hogy csak a kívánt virtuális hálózatokból származó forgalom férhessenek hozzá az Azure-fájlmegosztás számára a Storage-fiókon belül.
- Korlátozza a nyilvános végpontot egy vagy több virtuális hálózatra. Ez a *szolgáltatás végpontok* nevű virtuális hálózat funkciójának használatával működik. Ha egy szolgáltatási végponton keresztül korlátozza a forgalmat egy Storage-fiókra, akkor továbbra is a nyilvános IP-cím használatával fér hozzá a Storage-fiókhoz.

> [!NOTE]
> Az NFS-megosztások a nyilvános IP-címen keresztül nem férhetnek hozzá a Storage-fiók nyilvános végpontja számára, a virtuális hálózatok használatával csak a Storage-fiók nyilvános végpontját érhetik el. Az NFS-megosztások privát végpontok használatával is hozzáférhetnek a Storage-fiókhoz.

A Storage-fiók tűzfala konfigurálásával kapcsolatos további információkért lásd: [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="encryption-in-transit"></a>Titkosítás az átvitel során

> [!IMPORTANT]
> Ez a szakasz az SMB-megosztások továbbítási adatainak titkosítását ismerteti. Az NFS-megosztásokkal történő átvitel titkosításával kapcsolatos részletekért lásd: [Biztonság](storage-files-compare-protocols.md#security).

Alapértelmezés szerint az összes Azure Storage-fióknál engedélyezve van az átvitel titkosítása. Ez azt jelenti, hogy amikor az SMB-n keresztül csatlakoztat egy fájlmegosztást, vagy a kiosztott protokollon keresztül éri el (például a Azure Portal, a PowerShell/CLI vagy az Azure SDK-k használatával), Azure Files csak akkor engedélyezi a kapcsolatot, ha az SMB 3.0 + titkosítással vagy HTTPS-vel van ellátva. Azok az ügyfelek, amelyek nem támogatják az SMB 3,0-et vagy az SMB 3,0-et támogató ügyfeleket, de az SMB-titkosítást nem, nem fogják tudni csatlakoztatni az Azure-fájlmegosztást, ha engedélyezve van az átvitel titkosítása. Ha további információt szeretne arról, hogy mely operációs rendszerek támogatják az SMB 3,0 titkosítást, tekintse meg a [Windows](storage-how-to-use-files-windows.md), a [MacOS](storage-how-to-use-files-mac.md)és a [Linux](storage-how-to-use-files-linux.md)részletes dokumentációját. A PowerShell, CLI és SDK összes jelenlegi verziója támogatja a HTTPS-t.  

Egy Azure Storage-fiók esetében letilthatja a titkosítást az átvitel során. Ha a titkosítás le van tiltva, akkor a Azure Files az SMB 2,1, az SMB 3,0 titkosítás nélkül, és nem titkosított, nem titkosított API-hívásokat tesz lehetővé HTTP-n keresztül. Az átvitel közbeni titkosítás letiltásának elsődleges oka az olyan örökölt alkalmazások támogatása, amelyeknek régebbi operációs rendszeren kell futniuk, például Windows Server 2008 R2 vagy régebbi Linux-disztribúció. Azure Files csak az Azure-fájlmegosztás azonos Azure-régiójában lévő SMB 2,1-kapcsolatokat engedélyezi. Az Azure-fájlmegosztás (például a helyszíni vagy egy másik Azure-régió) Azure-régióján kívüli SMB 2,1-ügyfél nem fog tudni hozzáférni a fájlmegosztás eléréséhez.

További információ az átvitel közbeni titkosításról: [biztonságos átvitel megkövetelése az Azure Storage-ban](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="see-also"></a>Lásd még
- [Az Azure Files áttekintése](storage-files-introduction.md)
- [Azure Files üzembe helyezésének tervezése](storage-files-planning.md)