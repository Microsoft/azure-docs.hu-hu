---
title: DNS-továbbítás konfigurálása Azure Fileshoz | Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja a Azure Files DNS-továbbítását.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 3/19/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 9abe306668a4b20e42e45c498bf85b540dfaaee5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94630192"
---
# <a name="configuring-dns-forwarding-for-azure-files"></a>DNS-továbbítás konfigurálása az Azure Fileshoz
Azure Files lehetővé teszi, hogy saját végpontokat hozzon létre a fájlmegosztást tartalmazó Storage-fiókok számára. Bár számos különböző alkalmazás esetében hasznos, a privát végpontok különösen hasznosak az Azure-fájlmegosztás helyszíni hálózatról VPN-vagy ExpressRoute-kapcsolattal való összekapcsolásához. 

Ahhoz, hogy a Storage-fiók kapcsolata meghaladjon a hálózati alagúton, a Storage-fiók teljes tartománynevének (FQDN) fel kell oldania a privát végpont magánhálózati IP-címére. Ennek eléréséhez továbbítania kell a tárolási végpont utótagját ( `core.windows.net` a nyilvános felhő régiói esetében) a virtuális hálózatról elérhető Azure-beli magánhálózati DNS-szolgáltatásba. Ez az útmutató bemutatja, hogyan állíthatja be és konfigurálhatja a DNS-továbbítást a Storage-fiók magánhálózati végpontjának IP-címére való megfelelő feloldáshoz.

Javasoljuk, hogy olvassa el a [Azure Files központi telepítésének megtervezését](storage-files-planning.md) , és [Azure Files hálózati megfontolásokat](storage-files-networking-overview.md) a jelen cikkben ismertetett lépések végrehajtása előtt.

## <a name="overview"></a>Áttekintés
Azure Files két fő típusú végpontot biztosít az Azure-fájlmegosztás eléréséhez: 
- Nyilvános végpontok, amelyek nyilvános IP-címmel rendelkeznek, és bárhonnan elérhetők a világ bármely pontján.
- Saját végpontok, amelyek egy virtuális hálózaton belül találhatók, és rendelkeznek magánhálózati IP-címmel a virtuális hálózat címterület területén belülről.

A nyilvános és a privát végpontok az Azure Storage-fiókban találhatók. A Storage-fiók egy olyan felügyeleti szerkezet, amely egy megosztott tárolót jelöl, amelyben több fájlmegosztást is üzembe helyezhet, valamint más tárolási erőforrásokat, például blob-tárolókat vagy várólistákat.

Minden Storage-fióknak van egy teljes tartományneve (FQDN). A nyilvános Felhőbeli régiók esetében ez a teljes tartománynév azt a mintát követi, ahol a a `storageaccount.file.core.windows.net` `storageaccount` Storage-fiók neve. Ha ilyen nevű kérelmeket küld, például az SMB használatával csatlakoztatja a megosztást a munkaállomáson, az operációs rendszer DNS-lekérdezést hajt végre, hogy feloldja a teljes tartománynevet egy olyan IP-címen, amelyet az SMB-kérelmek küldésére használhat.

Alapértelmezés szerint `storageaccount.file.core.windows.net` a rendszer feloldja a nyilvános végpont IP-címét. A Storage-fiók nyilvános végpontja egy Azure Storage-fürtön található, amely számos más Storage-fiók nyilvános végpontját tárolja. Privát végpont létrehozásakor a rendszer egy privát DNS-zónát csatol ahhoz a virtuális hálózathoz, amelyhez hozzá lett adva, és egy CNAME rekord van társítva a `storageaccount.file.core.windows.net` Storage-fiók magánhálózati végpontjának magánhálózati IP-címéhez tartozó rekord-bejegyzéshez. Ez lehetővé teszi a `storageaccount.file.core.windows.net` virtuális hálózaton belüli FQDN használatát, és azt a magánhálózati végpont IP-címére oldja fel.

Mivel a végső célunk a helyszíni Storage-fiókban üzemeltetett Azure-fájlmegosztás hálózati alagút, például VPN-vagy ExpressRoute-kapcsolat használatával való elérése, konfigurálnia kell a helyszíni DNS-kiszolgálókat, hogy továbbítsák a Azure Files szolgáltatásnak küldött kéréseket az Azure Private DNS szolgáltatásnak. Ehhez be kell állítania a *feltételes továbbítást* `*.core.windows.net` (vagy a megfelelő tárolási végpont UTÓTAGJÁT az Egyesült Államok kormánya, Németország vagy Kína nemzeti felhők esetében) egy, az Azure-beli virtuális hálózaton belül üzemeltetett DNS-kiszolgálóra. A DNS-kiszolgáló ezt követően rekurzív módon továbbítja a kérést az Azure privát DNS-szolgáltatásához, amely feloldja a Storage-fiók teljes tartománynevét a megfelelő magánhálózati IP-címhez.

A DNS-továbbítás Azure Files konfigurálásakor a virtuális gépnek futnia kell egy DNS-kiszolgáló üzemeltetéséhez a kérések továbbításához, azonban ez a lépés a virtuális hálózaton belül üzemeltetett összes Azure-fájlmegosztás esetében egy alkalommal. Emellett ez nem kizárólagos követelmény, hogy Azure Files – bármely olyan Azure-szolgáltatás, amely támogatja a helyszíni eléréshez használni kívánt privát végpontokat, az útmutatóban konfigurált DNS-továbbítást is igénybe veheti: Azure Blob Storage, SQL Azure, Cosmos DB stb. 

Ez az útmutató a DNS-továbbítás konfigurálásának lépéseit mutatja be az Azure Storage-végpont számára, így a Azure Fileson kívül a DNS-névfeloldási kérelmeket a többi Azure Storage-szolgáltatáshoz (Azure Blob Storage, Azure Table Storage, Azure üzenetsor Storage stb.) továbbítjuk az Azure privát DNS-szolgáltatásához. További Azure-szolgáltatásokhoz további végpontok is hozzáadhatók, ha szükséges. A rendszer a helyi DNS-kiszolgálókra történő visszaküldést is konfigurálja, így a virtuális hálózaton (például egy DFS-N-kiszolgálón) a Felhőbeli erőforrásokat is felhasználhatja a helyszíni gépek nevének feloldásához. 

## <a name="prerequisites"></a>Előfeltételek
Mielőtt a DNS-továbbítást beállíthatja Azure Filesre, végre kell hajtania a következő lépéseket:

- Egy olyan Storage-fiók, amely tartalmazza a csatlakoztatni kívánt Azure-fájlmegosztást. További információ a Storage-fiókok és az Azure-fájlmegosztás létrehozásáról: [Azure-fájlmegosztás létrehozása](storage-how-to-create-file-share.md).
- A Storage-fiókhoz tartozó magánhálózati végpont. Ha meg szeretné tudni, hogyan hozhat létre saját végpontot a Azure Fileshoz, tekintse meg [a privát végpont létrehozása](storage-files-networking-endpoints.md#create-a-private-endpoint)című témakört.
- A Azure PowerShell modul [legújabb verziója](/powershell/azure/install-az-ps) .

> [!Important]  
> Ez az útmutató feltételezi, hogy a Windows Serveren belüli DNS-kiszolgálót használja a helyszíni környezetben. A jelen útmutatóban ismertetett lépések bármilyen DNS-kiszolgálóval lehetségesek, nem csak a Windows DNS-kiszolgáló.

## <a name="manually-configuring-dns-forwarding"></a>DNS-továbbítás manuális konfigurálása
Ha már rendelkezik DNS-kiszolgálókkal az Azure-beli virtuális hálózaton belül, vagy ha egyszerűen saját virtuális gépeket szeretne üzembe helyezni DNS-kiszolgálóként a szervezet által használt bármely módszer alapján, manuálisan is konfigurálhatja a DNS-t a beépített DNS-kiszolgáló PowerShell-parancsmagokkal.

A helyszíni DNS-kiszolgálókon hozzon létre egy feltételes továbbítót a használatával `Add-DnsServerConditionalForwarderZone` . Ezt a feltételes továbbítót az összes helyszíni DNS-kiszolgálón üzembe kell helyezni, hogy az az Azure-ba irányuló forgalom megfelelő továbbítására legyen érvényes. Ne felejtse el lecserélni `<azure-dns-server-ip>` a környezetet a megfelelő IP-címekre.

```powershell
$vnetDnsServers = "<azure-dns-server-ip>", "<azure-dns-server-ip>"

$storageAccountEndpoint = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty StorageEndpointSuffix

Add-DnsServerConditionalForwarderZone `
        -Name $storageAccountEndpoint `
        -MasterServers $vnetDnsServers
```

Az Azure-beli virtuális hálózaton belüli DNS-kiszolgálókon egy továbbítót is be kell állítani, hogy a Storage-fiók DNS-zónájára irányuló kérések a fenntartott IP-cím alá tartozó Azure Private DNS szolgáltatásra legyenek irányítva `168.63.129.16` . (Ne feledje a feltöltést, `$storageAccountEndpoint` Ha egy másik PowerShell-munkamenetben futtatja a parancsokat.)

```powershell
Add-DnsServerConditionalForwarderZone `
        -Name $storageAccountEndpoint `
        -MasterServers "168.63.129.16"
```

## <a name="using-the-azure-files-hybrid-module-to-configure-dns-forwarding"></a>A Azure Files hibrid modul használata a DNS-továbbítás konfigurálásához
Ahhoz, hogy a lehető legkönnyebben konfigurálja a DNS-továbbítást, az automatizálást a Azure Files hibrid modulban biztosítjuk. Az ebben a modulban a DNS manipulálására szolgáló parancsmagok segítséget nyújtanak a DNS-kiszolgálók üzembe helyezéséhez az Azure-beli virtuális hálózaton, és a helyszíni DNS-kiszolgálók frissítését is lehetővé teszik. 

Ha soha nem használta a Azure Files hibrid modult, először telepítenie kell a munkaállomásra. Töltse le a Azure Files Hybrid PowerShell-modul [legújabb verzióját](https://github.com/Azure-Samples/azure-files-samples/releases) :

```PowerShell
# Unzip the downloaded file
Expand-Archive -Path AzFilesHybrid.zip

# Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\AzFilesHybrid\CopyToPSPath.ps1 

# Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid
```

A DNS-továbbító megoldás üzembe helyezése két lépésből áll: egy DNS-továbbító szabálykészlet létrehozása, amely meghatározza, hogy mely Azure-szolgáltatások továbbítják a kérelmeket, valamint a DNS-továbbítók tényleges központi telepítését. 

A következő példa továbbítja a kéréseket a Storage-fióknak, az Azure Files, az Azure Blob Storage, az Azure Table Storage és az Azure üzenetsor-tárolásra vonatkozó kérelmeket. Ha kívánja, további Azure-szolgáltatás továbbítását is hozzáadhatja a szabályhoz a (z `-AzureEndpoints` ) parancsmag paraméterén keresztül `New-AzDnsForwardingRuleSet` . Ne felejtse el lecserélni a `<virtual-network-resource-group>` `<virtual-network-name>` `<subnet-name>` környezetet a megfelelő értékekre.

```PowerShell
# Create a rule set, which defines the forwarding rules
$ruleSet = New-AzDnsForwardingRuleSet -AzureEndpoints StorageAccountEndpoint

# Deploy and configure DNS forwarders
New-AzDnsForwarder `
        -DnsForwardingRuleSet $ruleSet `
        -VirtualNetworkResourceGroupName "<virtual-network-resource-group>" `
        -VirtualNetworkName "<virtual-network-name>" `
        -VirtualNetworkSubnetName "<subnet-name>"
```

Emellett hasznos/szükséges lehet a további paraméterek biztosításához:

| Paraméter neve | Típus | Description |
|----------------|------|-------------|
| `DnsServerResourceGroupName` | `string` | Alapértelmezés szerint a DNS-kiszolgálók a virtuális hálózattal megegyező erőforráscsoporthoz lesznek telepítve. Ha ez nem megfelelő, ez a paraméter lehetővé teszi, hogy kiválasszon egy másik erőforráscsoportot, amelybe telepíteni kívánja őket. |
| `DnsForwarderRootName` | `string` | Alapértelmezés szerint az Azure-ban üzembe helyezett DNS-kiszolgálóknak vannak a nevei `DnsFwder-*` , ahol a csillagot egy iteráció tölti ki. Ez a paraméter megváltoztatja a név gyökerét (azaz `DnsFwder` ). |
| `VmTemporaryPassword` | `SecureString` | Alapértelmezés szerint a virtuális gép a tartományhoz való csatlakozás előtt egy véletlenszerű jelszót választ az ideiglenes alapértelmezett fiókhoz. A tartományhoz való csatlakozás után az alapértelmezett fiók le van tiltva. |
| `DomainToJoin` | `string` | A tartomány, amelyhez csatlakoztatni kívánja a DNS-beli virtuális gépet (ka) t. Alapértelmezés szerint ez a tartomány a parancsmagokat futtató számítógép tartománya alapján van kiválasztva. |
| `DnsForwarderRedundancyCount` | `int` | A virtuális hálózathoz telepítendő DNS virtuális gépek száma. Alapértelmezés szerint `New-AzDnsForwarder` két DNS-kiszolgálót helyez üzembe az Azure-beli virtuális hálózaton a rendelkezésre állási csoportokban a redundancia biztosítása érdekében. Ez a szám szükség szerint módosítható. |
| `OnPremDnsHostNames` | `HashSet<string>` | A helyszíni DNS-állomásnevek manuálisan megadott listája a továbbítók létrehozásához. Ez a paraméter akkor lehet hasznos, ha nem kívánja alkalmazni a továbbítókat az összes helyszíni DNS-kiszolgálón, például ha több ügyfél is van, amelyek manuálisan megadott DNS-névvel rendelkeznek. |
| `Credential` | `PSCredential` | A DNS-kiszolgálók frissítésekor használandó hitelesítő adat. Ez akkor lehet hasznos, ha a felhasználó által bejelentkezett felhasználói fiók nem rendelkezik a DNS-beállítások módosítására vonatkozó engedélyekkel. |
| `SkipParentDomain` | `SwitchParameter` | Alapértelmezés szerint a DNS-továbbítók a környezetben létező legmagasabb szintű tartományra lesznek alkalmazva. Ha például a a `northamerica.corp.contoso.com` gyermektartomány `corp.contoso.com` , a továbbító a-hez társított DNS-kiszolgálókhoz lesz létrehozva `corp.contoso.com` . Ezzel a paraméterrel a továbbítókat fogja létrehozni a alkalmazásban `northamerica.corp.contoso.com` . |

## <a name="confirm-dns-forwarders"></a>DNS-továbbítók megerősítése
A tesztelés előtt ellenőrizze, hogy a DNS-továbbítók alkalmazása sikeresen megtörtént-e, javasoljuk, hogy törölje a DNS-gyorsítótárat a helyi munkaállomáson a használatával `Clear-DnsClientCache` . Annak ellenőrzéséhez, hogy sikeresen fel tudja-e oldani a Storage-fiók teljes tartománynevét, használja a (z) vagy a (z `Resolve-DnsName` `nslookup` ) lehetőséget.

```powershell
# Replace storageaccount.file.core.windows.net with the appropriate FQDN for your storage account.
# Note the proper suffix (core.windows.net) depends on the cloud your deployed in.
Resolve-DnsName -Name storageaccount.file.core.windows.net
```

Ha a névfeloldás sikeres, látnia kell a megoldott IP-címet, amely megfelel a Storage-fiók IP-címének.

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
```

Ha már beállított egy VPN-vagy ExpressRoute-kapcsolat használatát, azt is megtudhatja, hogy a TCP-kapcsolat sikeresen elvégezhető-e a `Test-NetConnection` Storage-fiókjában.

```PowerShell
Test-NetConnection -ComputerName storageaccount.file.core.windows.net -CommonTCPPort SMB
```

## <a name="see-also"></a>Lásd még
- [Azure Files üzembe helyezésének tervezése](storage-files-planning.md)
- [Azure Files hálózati megfontolások](storage-files-networking-overview.md)
- [Azure Files hálózati végpontok konfigurálása](storage-files-networking-endpoints.md)