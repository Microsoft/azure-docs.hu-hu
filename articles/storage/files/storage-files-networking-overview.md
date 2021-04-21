---
title: Azure Files hálózati megfontolások | Microsoft Docs
description: A hálózati beállítások áttekintése a Azure Files.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 02/22/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 1dce7795b8c62c36b80c51d5ba0dd8bc9b667e0e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107759686"
---
# <a name="azure-files-networking-considerations"></a>Azure Files hálózati szempontok 
Az Azure-fájlmegosztáshoz kétféleképpen csatlakozhat:

- A megosztás elérése közvetlenül a Kiszolgálói üzenetblokk (SMB), a hálózati fájlrendszer (NFS) (előzetes verzió) vagy a FileREST protokollok használatával. Ezt a hozzáférési mintát elsősorban akkor alkalmazzák, ha a lehető legtöbb helyszíni kiszolgálót ki kell küszöbölni.
- Gyorsítótár létrehozása az Azure-fájlmegosztáshoz egy helyszíni kiszolgálón (vagy egy Azure-beli virtuális gépen) az Azure File Sync-val, és a fájlmegosztás adatainak elérése a helyszíni kiszolgálóról az Ön által választott protokollal (SMB, NFS, FTPS stb.). Ez a hozzáférési minta azért hasznos, mert kombinálja a helyszíni teljesítmény és a felhőbeli méretezés, valamint a kiszolgáló nélküli csatolható szolgáltatások, például a Azure Backup.

Ez a cikk arra összpontosít, hogyan konfigurálhatja a hálózatot a számára, amikor a használat esete az Azure-fájlmegosztás közvetlen elérésére hívja meg a kapcsolatot a Azure File Sync. A központi telepítés hálózati szempontjaival kapcsolatos további Azure File Sync lásd: Azure File Sync [szempontok.](../file-sync/file-sync-networking-overview.md)

Az Azure-fájlmegosztások hálózati konfigurációja az Azure Storage-fiókban történik. A tárfiókok olyan felügyeleti szerkezetek, amelyek egy megosztott tárolókészletet képviselnek, amelyben több fájlmegosztást, valamint egyéb tárolási erőforrásokat, például blobtárolókat vagy üzenetsorokat helyezhet üzembe. A tárfiókok több beállítást is elérhetővé tenek, amelyek segítségével biztonságossá teszi a fájlmegosztások hálózati hozzáférését: hálózati végpontokat, tárfiók tűzfalbeállítását és átvitel közbeni titkosítást. 

Javasoljuk, hogy mielőtt elolvassa ezt a fogalmi útmutatót Azure Files, olvassa el a Planning [for an Azure Files deployment](storage-files-planning.md) (Tervezési terv üzembe helyezéshez) útmutatót.

:::row:::
    :::column:::
        <iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/jd49W33DxkQ" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    :::column-end:::
    :::column:::
        Ez a videó egy útmutató és bemutató arról, hogyan teheti biztonságossá az Azure-fájlmegosztásokat közvetlenül az információkkal dolgozó munkatársak és alkalmazások számára öt egyszerű lépésben. Az alábbi szakaszok hivatkozásokat és további kontextust biztosítanak a videóban hivatkozott dokumentációhoz.
   :::column-end:::
:::row-end:::

## <a name="accessing-your-azure-file-shares"></a>Az Azure-fájlmegosztások elérése
Amikor azure-fájlmegosztást helyez üzembe egy tárfiókon belül, a fájlmegosztás azonnal elérhető lesz a tárfiók nyilvános végpontjának használatával. Ez azt jelenti, hogy a hitelesített kérések, például a felhasználó bejelentkezési identitása által engedélyezett kérések, biztonságosan származnak az Azure-ból vagy azon kívül. 

Számos ügyfélkörnyezetben az Azure-fájlmegosztás kezdeti csatlakoztatása a helyszíni munkaállomáson sikertelen lesz, annak ellenére, hogy az Azure-beli virtuális gépekről való csatlakoztatás sikeres volt. Ennek az az oka, hogy számos szervezet és internetszolgáltató (ISP) blokkolja az SMB által a kommunikációhoz használt portot, a 445-ös portot. Az NFS-megosztások esetében nem ez a probléma. Ez a gyakorlat az SMB protokoll örökölt és elavult verzióival kapcsolatos biztonsági útmutatásból származik. Bár az SMB 3.0 egy internetre is biztonságos protokoll, az SMB régebbi verziói, különösen az SMB 1.0 régebbi verziói nem. Az Azure-fájlmegosztások csak az SMB 3.0-n és a FileREST protokollon (amely szintén biztonságos internetes protokollon) keresztül érhetők el kívülről a nyilvános végponton keresztül.

Mivel az Azure SMB-fájlmegosztás helyszíni környezetből való elérésének legegyszerűbb módja, ha megnyitja a helyszíni hálózatot a 445-ös porton, a Microsoft a következő lépéseket javasolja az SMB 1.0 eltávolításához a környezetből:

1. Győződjön meg arról, hogy az SMB 1.0 el van távolítva vagy le van tiltva a szervezet eszközein. A Windows és a Windows Server jelenleg támogatott verziói támogatják az SMB 1.0 eltávolítását vagy letiltását, és az Windows 10 1709-es verziójától kezdve az SMB 1.0 alapértelmezés szerint nincs telepítve a Windows rendszeren. Az SMB 1.0 letiltására vonatkozó további információkért tekintse meg az operációs rendszerre vonatkozó oldalakat:
    - [A Windows/Windows Server védelme](storage-how-to-use-files-windows.md#securing-windowswindows-server)
    - [A Linux biztonságossá tétele](storage-how-to-use-files-linux.md#securing-linux)
1. Győződjön meg arról, hogy a szervezeten belül egyetlen termék sem igényel SMB 1.0-t, és távolítsa el azokat, amelyek igen. Egy [SMB1](https://aka.ms/stillneedssmb1)terméktörlési központot tartunk fenn, amely a Microsoft által ismert összes első és harmadik féltől származó terméket tartalmazza, és megköveteli az SMB 1.0 használatát. 
1. (Nem kötelező) Használjon külső tűzfalat a szervezet helyszíni hálózatával annak megakadályozására, hogy az SMB 1.0-forgalom elhagyja a szervezeti határt.

Ha a szervezet megköveteli, hogy a 445-ös port szabályzatonként vagy szabályozásonként le legyen tiltva, vagy ha a szervezet megköveteli az Azure-ba vezető forgalmat egy determinisztikus útvonalon való követéshez, az Azure VPN Gateway vagy az ExpressRoute használatával bújtathatja a forgalmat az Azure-fájlmegosztások felé. Az NFS-megosztások nem igénylik ezek bármelyikét, mivel nincs szükségük a 445-ös portra.

> [!Important]  
> A Microsoft akkor is javasolja az SMB 1.0 eltávolítását a környezetből, ha más módszert használ az Azure-fájlmegosztások eléréséhez.

### <a name="tunneling-traffic-over-a-virtual-private-network-or-expressroute"></a>Forgalom bújtatása virtuális magánhálózaton vagy ExpressRoute-kapcsolaton keresztül
Amikor hálózati alagutat hoz létre a helyszíni hálózat és az Azure között, társviszonyt létesít a helyszíni hálózattal egy vagy több Azure-beli virtuális hálózattal. A [virtuális hálózatok](../../virtual-network/virtual-networks-overview.md)( vagy virtuális hálózatok) hasonlóak a hagyományos hálózatokhoz, amelyek a helyszínen üzemelnek. Az Azure Storage-fiókhoz vagy egy Azure-beli virtuális géphez hasonló virtuális hálózat egy erőforráscsoportban üzembe helyezett Azure-erőforrás. 

Azure Files a következő mechanizmusokat támogatja a helyszíni munkaállomások és kiszolgálók és az Azure SMB/NFS-fájlmegosztások közötti forgalom bújtatása érdekében:

- [Azure VPN Gateway:](../../vpn-gateway/vpn-gateway-about-vpngateways.md)A VPN-átjáró egy adott típusú virtuális hálózati átjáró, amely titkosított adatforgalmat küld egy Azure-beli virtuális hálózat és egy másik hely (például helyszíni) között az interneten keresztül. Az Azure VPN Gateway egy olyan Azure-erőforrás, amely üzembe helyezhető egy erőforráscsoportban egy tárfiók vagy más Azure-erőforrás mellett. A VPN-átjárók két különböző típusú kapcsolatot fednek fel:
    - [Pont–hely (P2S)](../../vpn-gateway/point-to-site-about.md) VPN-átjárókapcsolatok, amelyek az Azure és egy különálló ügyfél közötti VPN-kapcsolatok. Ez a megoldás elsősorban olyan eszközökhöz hasznos, amelyek nem részei a szervezet helyszíni hálózatának, például távmunkások számára, akik otthonról, egy kávézóból vagy egy kávézóból szeretnék csatlakoztatni az Azure-fájlmegosztásukat útközben. Ha P2S VPN-kapcsolatot szeretne használni a Azure Files, minden csatlakoztatni kívánó ügyfélhez konfigurálni kell egy P2S VPN-kapcsolatot. A P2S VPN-kapcsolat üzembe helyezésének egyszerűsítéséhez [lásd: Pont–hely (P2S) VPN](storage-files-configure-p2s-vpn-windows.md) konfigurálása Windows rendszeren a Azure Files-val való használatra és Pont–hely [(P2S) VPN](storage-files-configure-p2s-vpn-linux.md)konfigurálása Linux rendszeren a Azure Files-
    - [Hely–hely (S2S) VPN,](../../vpn-gateway/design.md#s2smulti)amely az Azure és a szervezet hálózata közötti VPN-kapcsolatok. A S2S VPN-kapcsolat lehetővé teszi, hogy egyszer konfigurálja a VPN-kapcsolatot a szervezet hálózatán üzemeltetett VPN-kiszolgálóhoz vagy eszközhöz ahelyett, hogy minden olyan ügyféleszközt használ, amely hozzá kell férni az Azure-fájlmegosztáshoz. A virtuális hálózati kapcsolatok üzembe helyezésének egyszerűsítéséhez S2S VPN a Hely–hely [(S2S) VPN](storage-files-configure-s2s-vpn.md)konfigurálása az Azure Files.
- [ExpressRoute,](../../expressroute/expressroute-introduction.md)amely lehetővé teszi, hogy egy meghatározott útvonalat hozzon létre az Azure és a helyszíni hálózat között, amely nem áthalad az interneten. Mivel az ExpressRoute dedikált útvonalat biztosít a helyszíni adatközpont és az Azure között, az ExpressRoute hasznos lehet, ha figyelembe veszi a hálózati teljesítményt. Az ExpressRoute akkor is jó választás, ha a szervezet szabályzati vagy szabályozási követelményei determinisztikus útvonalat igényelnek a felhőben található erőforrásokhoz.

Függetlenül attól, hogy melyik bújtatási módszert használja az Azure-fájlmegosztások eléréséhez, olyan mechanizmusra van szüksége, amely biztosítja, hogy a tárfiókba beforgalma a normál internetkapcsolat helyett az alagúton halad át. Technikailag lehetséges a tárfiók nyilvános végpontjára való útválasztás, ehhez azonban az Azure-tárolófürtök összes IP-címének szoftveres kódolása szükséges egy régióban, mivel a tárfiókok bármikor áthelyezheti a tárfiókokat a tárolófürtök között. Ehhez az IP-címleképezések folyamatos frissítésére is szükség van, mivel az új fürtök hozzáadása folyamatosan történik.

Ahelyett, hogy a tárfiókok IP-címét bekódolta volna a VPN-útválasztási szabályokba, azt javasoljuk, hogy privát végpontokat használ, amelyek egy Azure-beli virtuális hálózat címterületének IP-címét adják meg a tárfióknak. Mivel az Azure-hoz való alagút létrehozása társviszonyt létesít a helyszíni hálózat és egy vagy több virtuális hálózat között, ez tartós útválasztást tesz lehetővé.

### <a name="private-endpoints"></a>Privát végpontok
A tárfiók alapértelmezett nyilvános végpontja mellett a Azure Files egy vagy több privát végpontot is biztosít. A privát végpontok olyan végpontok, amelyek csak egy Azure-beli virtuális hálózaton belül érhetők el. Amikor létrehoz egy privát végpontot a tárfiókhoz, a tárfiók magánhálózati IP-címet kap a virtuális hálózat címterületéről, akárcsak az, ahogy egy helyszíni fájlkiszolgáló vagy NAS-eszköz egy IP-címet kap a helyszíni hálózat dedikált címterületén belül. 

Egy egyéni privát végpont egy adott Azure-beli virtuális hálózat alhálózatához van társítva. Egy tárfiók több virtuális hálózat privát végpontjaival is lehet.

A privát végpontok Azure Files a következőt teszi lehetővé:
- Biztonságosan csatlakozhat azure-fájlmegosztásaihoz helyszíni hálózatokról VPN- vagy ExpressRoute-kapcsolattal privát társviszony-létesítés használatával.
- Az Azure-fájlmegosztásokat úgy biztosíthatja, hogy a tárfiók tűzfalát úgy konfigurálja, hogy minden kapcsolatot blokkoljon a nyilvános végponton. Alapértelmezés szerint a privát végpontok létrehozása nem blokkolja a nyilvános végponttal való kapcsolatokat.
- A virtuális hálózat biztonságának növelése azáltal, hogy lehetővé teszi a virtuális hálózatból (és a társviszony-létesítés határain) származó adatok kiszivárgásának blokkolását.

Privát végpont létrehozásához lásd: [Configuring private endpoints for Azure Files.](storage-files-networking-endpoints.md)

### <a name="private-endpoints-and-dns"></a>Privát végpontok és DNS
Privát végpont létrehozásakor alapértelmezés szerint az altartománynak megfelelő privát DNS-zónát is létrehozunk (vagy `privatelink` frissítünk egy meglévőt). Szigorú értelemben véve a privát DNS-zóna létrehozása nem szükséges a tárfiók privát végpontjának létrehozásához, de általánosságban erősen ajánlott, és kifejezetten szükséges, ha az Azure-fájlmegosztást egy Active Directory-felhasználó egyszerű felhasználójával vagy a FileREST API-val való hozzáféréshez használja.

> [!Note]  
> Ez a cikk a tárfiók DNS-utótagját használja a nyilvános Azure-régiókhoz(). `core.windows.net` Ez a megjegyzés az Olyan Azure szuverén felhőkre is vonatkozik, mint az Azure US Government-felhő és az Azure China-felhő – csak helyettesítse be a környezetének megfelelő utótagokat. 

A privát DNS-zónában létrehozunk egy A rekordot a számára, valamint egy CNAME rekordot a tárfiók normál nevéhez, amely a `storageaccount.privatelink.file.core.windows.net` következő mintát `storageaccount.file.core.windows.net` követi: . Mivel az Azure-beli privát DNS-zóna a privát végpontot tartalmazó virtuális hálózathoz csatlakozik, a DNS-konfigurációt a PowerShell-parancsmag Azure-beli virtuális gépen való hívásával figyelheti meg (alternatív módon Windows és `Resolve-DnsName` `nslookup` Linux rendszeren):

```powershell
Resolve-DnsName -Name "storageaccount.file.core.windows.net"
```

Ebben a példában a tárfiók a privát végpont magánhálózati IP-címére oldódik `storageaccount.file.core.windows.net` fel, amely véletlenül a következő: `192.168.0.4` .

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

Ez azt a tényt tükrözi, hogy a tárfiók a nyilvános végpontot és egy vagy több privát végpontot is elérhetővé tud tenni. Annak biztosításához, hogy a tárfiók neve a privát végpont magánhálózati IP-címére legyen feloldva, módosítania kell a konfigurációt a helyszíni DNS-kiszolgálókon. Ez többféleképpen is meg lehet valósítható:

- Az ügyfelek gazdagépfájljának módosítása a kívánt privát végpont magánhálózati `storageaccount.file.core.windows.net` IP-címére való feloldás érdekében. Ez éles környezetekben erősen ajánlott, mivel ezeket a módosításokat minden olyan ügyfélen végre kell majd vennie, amely csatlakoztatni szeretné az Azure-fájlmegosztásokat, és a tárfiók vagy a privát végpont módosításait nem kezeli automatikusan a rendszer.
- A rekord létrehozása a helyszíni `storageaccount.file.core.windows.net` DNS-kiszolgálókon. Ennek az az előnye, hogy a helyszíni környezet ügyfelei képesek lesznek automatikusan feloldani a tárfiókot anélkül, hogy mindegyik ügyfelet konfigurálni kellene, azonban ez a megoldás hasonlóképpen nem tudja módosítani a gazdagépfájlt, mert a módosítások nem jelennek meg. Bár ez a megoldás nem túl gyors, egyes környezetekben ez a legjobb választás.
- Továbbítja `core.windows.net` a zónát a helyszíni DNS-kiszolgálókról az Azure privát DNS-zónába. Az Azure privát DNS-gazdagép egy speciális IP-címen ( ) keresztül érhető el, amely csak az Azure privát DNS-zónához kapcsolt virtuális `168.63.129.16` hálózatokon belül érhető el. A korlátozás megkerülése érdekében további DNS-kiszolgálókat futtathat a virtuális hálózaton belül, amelyek továbbítva lesznek `core.windows.net` az Azure privát DNS-zónába. A beállítás leegyszerűsítése érdekében Olyan PowerShell-parancsmagokat biztosítunk, amelyek automatikusan üzembe helyeznek DNS-kiszolgálókat az Azure-beli virtuális hálózatban, és szükség szerint konfigurálják azokat. A DNS-továbbítás beállításával kapcsolatos információkért lásd: A DNS konfigurálása [a Azure Files.](storage-files-networking-dns.md)

## <a name="storage-account-firewall-settings"></a>Tárfiók tűzfalbeállítása
A tűzfal egy hálózati szabályzat, amely azt szabályozza, hogy mely kérések férhetnek hozzá a tárfiók nyilvános végpontjaihoz. A tárfiók tűzfalával korlátozhatja a tárfiók nyilvános végpontjának hozzáférését bizonyos IP-címekre, tartományokra vagy egy virtuális hálózatra. Általánosságban elmondható, hogy a tárfiókok legtöbb tűzfal-szabályzata egy vagy több virtuális hálózat hálózati hozzáférését korlátozza. 

A tárfiókhoz való hozzáférést kétféle módszer létezik egy virtuális hálózatra korlátozva:
- Hozzon létre egy vagy több privát végpontot a tárfiókhoz, és korlátozza a nyilvános végponthoz való hozzáférést. Ez biztosítja, hogy csak a kívánt virtuális hálózatokról származó forgalom fér hozzá a tárfiókon belüli Azure-fájlmegosztáshoz.
- Korlátozza a nyilvános végpontot egy vagy több virtuális hálózatra. Ez a szolgáltatásvégpontnak nevezett virtuális hálózat *képességével működik.* Ha egy szolgáltatásvégponton keresztül egy tárfiókra korlátozza a forgalmat, akkor is a nyilvános IP-címen keresztül fér hozzá a tárfiókhoz.

> [!NOTE]
> Az NFS-megosztások nem tudják elérni a tárfiók nyilvános végpontját a nyilvános IP-címen keresztül, csak a tárfiók nyilvános végpontját tudják elérni virtuális hálózatokkal. Az NFS-megosztások privát végpontok használatával is hozzáférhetnek a tárfiókhoz.

További információ a tárfiók tűzfalának konfigurálásról: Azure Storage-tűzfalak és [virtuális hálózatok konfigurálása.](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

## <a name="encryption-in-transit"></a>Titkosítás az átvitel során

> [!IMPORTANT]
> Ez a szakasz az SMB-megosztások átvitel közbeni titkosításának részleteit tartalmazza. Az NFS-megosztások átvitel közbeni titkosításával kapcsolatos részletekért lásd: [Biztonság.](storage-files-compare-protocols.md#security)

Alapértelmezés szerint minden Azure-tárfiókon engedélyezve van a titkosítás az átvitel során. Ez azt jelenti, hogy ha SMB-n keresztül csatlakoztat egy fájlmegosztást, vagy a FileREST protokollon keresztül fér hozzá (például az Azure Portal, a PowerShell/CLI vagy az Azure SDK-k segítségével), a Azure Files csak akkor engedélyezi a kapcsolatot, ha az SMB 3.0+ titkosítással vagy HTTPS-kapcsolattal készült. Az SMB 3.0-t nem támogató ügyfelek vagy az SMB 3.0-t támogató, de SMB-titkosítást nem támogató ügyfelek nem tudják csatlakoztatni az Azure-fájlmegosztást, ha az átvitel közbeni titkosítás engedélyezve van. Arról, hogy mely operációs rendszerek támogatják az SMB 3.0-t titkosítással, tekintse meg a [Windows,](storage-how-to-use-files-windows.md) [macOS](storage-how-to-use-files-mac.md)és Linux rendszerek részletes [dokumentációját.](storage-how-to-use-files-linux.md) A PowerShell, a parancssori felület és az SDK-k összes jelenlegi verziója támogatja a HTTPS-t.  

Letilthatja az átvitel közbeni titkosítást egy Azure Storage-fiókhoz. Ha a titkosítás le van tiltva, Azure Files SMB 2.1, SMB 3.0 titkosítás nélkül, valamint titkosítatlan FileREST API-hívások is engedélyezve lesznek HTTP-n keresztül. Az átvitel közbeni titkosítás letiltásának elsődleges oka egy régebbi alkalmazás támogatása, amely régebbi operációs rendszeren, például Windows Server 2008 R2 vagy régebbi Linux-disztribúción fut. Azure Files SMB 2.1-kapcsolatokat csak az Azure-fájlmegosztással azonos Azure-régióban engedélyezi; az Azure-fájlmegosztás Azure-régióján kívül (például a helyszínen vagy egy másik Azure-régióban) található SMB 2.1-ügyfél nem fog tudni hozzáférni a fájlmegosztáshoz.

További információ az átvitel közbeni titkosításról: Biztonságos [átvitel megkövetelve az Azure Storage-ban.](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

## <a name="see-also"></a>Lásd még
- [Az Azure Files áttekintése](storage-files-introduction.md)
- [Azure Files üzembe helyezésének tervezése](storage-files-planning.md)