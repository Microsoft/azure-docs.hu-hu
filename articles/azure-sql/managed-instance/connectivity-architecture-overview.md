---
title: Kapcsolati architektúra
titleSuffix: Azure SQL Managed Instance
description: Ismerje meg az Azure SQL felügyelt példányának kommunikációs és kapcsolati architektúráját, valamint azt, hogy az összetevők hogyan irányítják át a felügyelt példányok forgalmát.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 10/22/2020
ms.openlocfilehash: c91b0231271c6cbcf9ec92c7ad6d25f1bc0f9136
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105960469"
---
# <a name="connectivity-architecture-for-azure-sql-managed-instance"></a>Felügyelt Azure SQL-példány kapcsolati architektúrája
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Ez a cikk a felügyelt Azure SQL-példányon folytatott kommunikációt ismerteti. Ismerteti továbbá a kapcsolati architektúrát, valamint azt, hogy az összetevők hogyan irányítják át a felügyelt példányok forgalmát.  

Az SQL felügyelt példánya az Azure-beli virtuális hálózaton és a felügyelt példányokhoz dedikált alhálózaton belül helyezkedik el. Ez az üzembe helyezés a következőket biztosítja:

- Biztonságos magánhálózati IP-cím.
- Helyszíni hálózat és a felügyelt SQL-példányok összekapcsolása.
- Az SQL felügyelt példányának csatolása egy csatolt kiszolgálóhoz vagy egy másik helyszíni adattárhoz.
- Az SQL felügyelt példányának Azure-erőforrásokhoz való kapcsolódásának lehetősége.

## <a name="communication-overview"></a>Kommunikáció áttekintése

A következő ábrán az SQL felügyelt példányhoz kapcsolódó entitások láthatók. A felügyelt példányokkal való kommunikációhoz szükséges erőforrásokat is megjeleníti. A diagram alján található kommunikációs folyamat azokat az ügyfeleket és eszközöket jelöli, amelyek adatforrásként csatlakoznak az SQL felügyelt példányához.  

![A kapcsolati architektúrában lévő entitások](./media/connectivity-architecture-overview/connectivityarch001.png)

Az SQL felügyelt példánya egy szolgáltatásként nyújtott platform. Az Azure a szolgáltatás telemetria-adatfolyamok alapján történő kezeléséhez automatizált ügynököket (felügyelet, üzembe helyezés és karbantartás) használ. Mivel az Azure felelős a felügyeletért, az ügyfelek nem férhetnek hozzá az SQL felügyelt példányok virtuális fürtjéhez RDP protokoll (RDP) használatával.

A végfelhasználók vagy az alkalmazások által indított egyes műveletekhez szükség lehet arra, hogy az SQL felügyelt példány működjön a platformmal. Az egyik esetben egy SQL felügyelt példány-adatbázis létrehozása. Ezt az erőforrást a Azure Portal, a PowerShell, az Azure CLI és a REST API teszi elérhetővé.

Az SQL felügyelt példánya olyan Azure-szolgáltatásoktól függ, mint például az Azure Storage for Backups, az Azure Event Hubs for telemetria, a Azure Active Directory (Azure AD) hitelesítéshez, a Azure Key Vault for transzparens adattitkosítás (TDE), valamint néhány olyan Azure platform-szolgáltatás, amely biztonsági és támogatási funkciókat biztosít. Az SQL felügyelt példánya kapcsolatot létesít ezekkel a szolgáltatásokkal.

Minden kommunikáció titkosított és tanúsítványokkal van aláírva. A kommunikáló felek megbízhatóságának ellenőrzéséhez az SQL felügyelt példánya folyamatosan ellenőrzi ezeket a tanúsítványokat a visszavont tanúsítványok listája alapján. Ha visszavonják a tanúsítványokat, az SQL felügyelt példánya lezárja a kapcsolatokat az adatvédelemhez.

## <a name="high-level-connectivity-architecture"></a>Magas szintű kapcsolati architektúra

A felügyelt SQL-példányok magas szinten a szolgáltatás-összetevők készletét jelentik. Ezek az összetevők az ügyfél virtuális hálózati alhálózatán belül futó elkülönített virtuális gépek dedikált készletén futnak. Ezek a gépek virtuális fürtöt alkotnak.

A virtuális fürtök több felügyelt példányt is tárolhatnak. Ha szükséges, a fürt automatikusan kibontja a vagy a szerződést, amikor az ügyfél megváltoztatja az alhálózat kiépített példányainak számát.

Az ügyfélalkalmazások csatlakozhatnak a felügyelt SQL-példányokhoz, és a virtuális hálózaton, a megosztott virtuális hálózaton vagy a VPN-vagy az Azure-ExpressRoute csatlakoztatott hálózaton belül is lekérhetik és frissíthetik az adatbázisokat. A hálózatnak végpontot és magánhálózati IP-címet kell használnia.  

![Kapcsolati architektúra diagramja](./media/connectivity-architecture-overview/connectivityarch002.png)

Az Azure felügyeleti és központi telepítési szolgáltatásai a virtuális hálózaton kívül futnak. A felügyelt SQL-példány és az Azure-szolgáltatások a nyilvános IP-címekkel rendelkező végpontokon keresztül csatlakoznak. Ha az SQL felügyelt példánya kimenő kapcsolatokat hoz létre, a fogadó végpont hálózati címfordításán (NAT) keresztül a Kapcsolódás a következő nyilvános IP-címről fog kinézni.

A felügyeleti forgalom az ügyfél virtuális hálózatán keresztül folyik. Ez azt jelenti, hogy a virtuális hálózat infrastruktúrájának elemei a példány meghibásodása és elérhetetlenné válása miatt kárt okozhatnak a felügyeleti forgalomban.

> [!IMPORTANT]
> A felhasználói élmény és a szolgáltatás rendelkezésre állásának javítása érdekében az Azure hálózati leképezési házirendet alkalmaz az Azure Virtual Network infrastruktúra elemeire. A házirend hatással lehet az SQL felügyelt példányainak működésére. Ez a platform-mechanizmus transzparens módon kommunikál a felhasználók hálózati követelményeivel. A szabályzat fő célja, hogy megakadályozza a hálózati helytelen konfigurálást, és gondoskodjon az SQL felügyelt példányok szokásos műveleteiről. Felügyelt példány törlésekor a hálózati leképezési házirend is törlődik.

## <a name="virtual-cluster-connectivity-architecture"></a>Virtuális fürt kapcsolati architektúrája

Vessünk egy mélyebb betekintést az SQL felügyelt példányának kapcsolati architektúrába. Az alábbi ábrán a virtuális fürt fogalmi elrendezése látható.

![A virtuális fürt kapcsolati architektúrája](./media/connectivity-architecture-overview/connectivityarch003.png)

Az ügyfelek az SQL által felügyelt példányhoz az űrlapot tartalmazó állomásnév használatával csatlakoznak `<mi_name>.<dns_zone>.database.windows.net` . Ez az állomásnév a magánhálózati IP-címekre lesz feloldva, bár egy nyilvános tartománynévrendszer-(DNS-) zónában van regisztrálva, és nyilvánosan feloldható. A `zone-id` automatikusan létrejön a fürt létrehozásakor. Ha egy újonnan létrehozott fürt egy másodlagos felügyelt példányt futtat, akkor a zóna AZONOSÍTÓját megosztja az elsődleges fürttel. További információ: [automatikus feladatátvételi csoportok használata több adatbázis átlátható és koordinált feladatátvételének engedélyezéséhez](../database/auto-failover-group-overview.md#enabling-geo-replication-between-managed-instances-and-their-vnets).

Ez a magánhálózati IP-cím az SQL felügyelt példányának belső terheléselosztó része. A terheléselosztó átirányítja a forgalmat az SQL felügyelt példány átjárójának. Mivel több felügyelt példány futhat ugyanazon a fürtön belül, az átjáró az SQL felügyelt példány állomásneve használatával irányítja át a forgalmat a megfelelő SQL Engine-szolgáltatásba.

A felügyeleti és központi telepítési szolgáltatások az SQL felügyelt példányához kapcsolódnak egy olyan [felügyeleti végpont](#management-endpoint) használatával, amely egy külső terheléselosztó számára van leképezve. A rendszer csak akkor irányítja a forgalmat a csomópontokhoz, ha egy előre meghatározott porton érkezik, amely csak a felügyelt SQL-példányok felügyeleti összetevőit használja. A csomópontokon a beépített tűzfal úgy van beállítva, hogy csak a Microsoft IP-címtartományok forgalmát engedélyezze. A tanúsítványok kölcsönösen hitelesítik a felügyeleti összetevők és a felügyeleti sík közötti összes kommunikációt.

## <a name="management-endpoint"></a>Felügyeleti végpont

Az Azure felügyeleti végpont használatával kezeli az SQL felügyelt példányát. Ez a végpont egy példány virtuális fürtjén belül van. A felügyeleti végpontot egy beépített tűzfal védi a hálózati szinten. Az alkalmazás szintjén a kölcsönös tanúsítvány-ellenőrzés védi. A végpont IP-címének megkereséséhez tekintse meg [a felügyeleti végpont IP-címének megállapítása](management-endpoint-find-ip-address.md)című témakört.

Ha a kapcsolatok a felügyelt SQL-példányon belül kezdődnek (mint a biztonsági mentések és a naplók), a forgalom úgy tűnik, hogy a felügyeleti végpont nyilvános IP-címéről indul el. A nyilvános szolgáltatásokhoz való hozzáférést korlátozhatja az SQL felügyelt példányáról úgy, hogy a tűzfalszabályok beállításával csak az SQL felügyelt példány IP-címét engedélyezi. További információ: [az SQL felügyelt példány beépített tűzfalának ellenőrzése](management-endpoint-verify-built-in-firewall.md).

> [!NOTE]
> A felügyelt SQL-példányok régiójába tartozó Azure-szolgáltatásokra áthaladó forgalom optimalizálva van, ezért a felügyeleti végponthoz tartozó nyilvános IP-címhez nem. Emiatt, ha az IP-alapú tűzfalszabályok használatát igényli, általában a tároláshoz, a szolgáltatásnak egy másik régióban kell lennie a felügyelt SQL-példánytól.

## <a name="service-aided-subnet-configuration"></a>Szolgáltatással segített alhálózat-konfiguráció

Az ügyfél biztonsági és kezelhetőségi követelményeinek kezeléséhez az SQL felügyelt példánya a manuálistól a szolgáltatással támogatott alhálózat-konfigurációra vált.

A szolgáltatással segített alhálózat konfigurálásával a felhasználó teljes mértékben felügyeli az adatok (TDS) forgalmát, míg az SQL felügyelt példány feladata, hogy biztosítsa a felügyeleti forgalom zavartalan áramlását az SLA teljesítése érdekében.

A szolgáltatással segített alhálózat konfigurációja a virtuális hálózati alhálózati [delegálási](../../virtual-network/subnet-delegation-overview.md) szolgáltatásra épül, és lehetővé teszi az automatikus hálózati konfiguráció felügyeletét és a szolgáltatási végpontok engedélyezését. 

A szolgáltatás-végpontok a virtuális hálózati tűzfalszabályok konfigurálására használhatók a biztonsági mentéseket és naplókat tároló fiókoknál. A szolgáltatás-végpontok engedélyezése esetén az ügyfeleknek javasoljuk, hogy olyan [privát hivatkozást](../../private-link/private-link-overview.md) használjanak, amely további biztonságot nyújt a szolgáltatási végpontokon.

> [!IMPORTANT]
> A felügyeleti sík konfigurációjának sajátosságai miatt a szolgáltatással segített alhálózat konfigurációja nem engedélyezte a szolgáltatási végpontokat az országos felhőkben. 

### <a name="network-requirements"></a>A hálózatra vonatkozó követelmények

Telepítse az SQL felügyelt példányt egy dedikált alhálózatban a virtuális hálózaton belül. Az alhálózatnak a következő jellemzőkkel kell rendelkeznie:

- **Dedikált alhálózat:** Az SQL felügyelt példány alhálózata nem tartalmazhat más, hozzá társított felhőalapú szolgáltatást, és nem lehet átjáró-alhálózat. Az alhálózat nem tartalmazhat erőforrást, de az SQL felügyelt példányát, és később nem adhat hozzá más típusú erőforrásokat az alhálózatban.
- **Alhálózat delegálása:** Az SQL felügyelt példányának alhálózatát delegálni kell az `Microsoft.Sql/managedInstances` erőforrás-szolgáltatóhoz.
- **Hálózati biztonsági csoport (NSG):** Egy NSG kell társítani az SQL felügyelt példány alhálózatához. A NSG használatával szabályozhatja az SQL felügyelt példány adatvégponthoz való hozzáférést úgy, hogy az 1433-es és a 11000-11999-es porton lévő forgalmat szűri, ha az SQL felügyelt példánya átirányítási kapcsolatok használatára van konfigurálva. A szolgáltatás automatikusan kiépíti és megtartja a felügyeleti forgalom zavartalan áramlását lehetővé tevő aktuális [szabályokat](#mandatory-inbound-security-rules-with-service-aided-subnet-configuration) .
- **Felhasználó által megadott útvonal (UDR) tábla:** UDR táblát kell társítani az SQL felügyelt példány alhálózatához. Hozzáadhat bejegyzéseket az útválasztási táblához, hogy a virtuális hálózati átjárón vagy a virtuális hálózati berendezésen (NVA) keresztül átirányítsa a helyszíni magánhálózati IP-tartományokkal rendelkező forgalmat. A szolgáltatás automatikusan kiépíti és megtartja a jelenlegi, a felügyeleti forgalom zavartalan áramlását lehetővé tevő [bejegyzéseket](#user-defined-routes-with-service-aided-subnet-configuration).
- **Elegendő IP-cím:** Az SQL felügyelt példány alhálózatának legalább 32 IP-címmel kell rendelkeznie. További információ: az [alhálózat méretének meghatározása az SQL felügyelt példányhoz](vnet-subnet-determine-size.md). Miután konfigurálta a felügyelt példányokat [a meglévő hálózatban](vnet-existing-add-subnet.md) , a konfigurálása után meg kell felelnie [az SQL felügyelt példányának hálózati követelményeinek](#network-requirements). Egyéb esetben hozzon létre egy [új virtuális hálózatot és alhálózatot](virtual-network-subnet-create-arm-template.md).

> [!IMPORTANT]
> Felügyelt példány létrehozásakor a rendszer egy hálózati leképezési házirendet alkalmaz az alhálózaton, hogy megakadályozza a nem megfelelő módosításokat a hálózatkezelés beállításában. Miután az utolsó példányt eltávolította az alhálózatból, a rendszer eltávolítja a hálózati leképezési házirendet is. Az alábbi szabályok csak tájékoztatási célokat szolgálnak, és az ARM template/PowerShell/CLI használatával nem telepíthetik őket. Ha a legújabb hivatalos sablont szeretné használni, bármikor [lekérheti azt a portálról](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

### <a name="mandatory-inbound-security-rules-with-service-aided-subnet-configuration"></a>Kötelező bejövő biztonsági szabályok a szolgáltatással segített alhálózat konfigurációjával

| Name       |Port                        |Protokoll|Forrás           |Cél|Művelet|
|------------|----------------------------|--------|-----------------|-----------|------|
|felügyelet  |9000, 9003, 1438, 1440, 1452|TCP     |SqlManagement    |MI ALHÁLÓZAT  |Engedélyezés |
|            |9000, 9003                  |TCP     |CorpnetSaw       |MI ALHÁLÓZAT  |Engedélyezés |
|            |9000, 9003                  |TCP     |CorpnetPublic    |MI ALHÁLÓZAT  |Engedélyezés |
|mi_subnet   |Bármelyik                         |Bármelyik     |MI ALHÁLÓZAT        |MI ALHÁLÓZAT  |Engedélyezés |
|health_probe|Bármelyik                         |Bármelyik     |AzureLoadBalancer|MI ALHÁLÓZAT  |Engedélyezés |

### <a name="mandatory-outbound-security-rules-with-service-aided-subnet-configuration"></a>Kötelező kimenő biztonsági szabályok a szolgáltatással segített alhálózat konfigurációjával

| Name       |Port          |Protokoll|Forrás           |Cél|Művelet|
|------------|--------------|--------|-----------------|-----------|------|
|felügyelet  |443, 12000    |TCP     |MI ALHÁLÓZAT        |AzureCloud |Engedélyezés |
|mi_subnet   |Bármelyik           |Bármelyik     |MI ALHÁLÓZAT        |MI ALHÁLÓZAT  |Engedélyezés |

### <a name="user-defined-routes-with-service-aided-subnet-configuration"></a>A felhasználó által megadott útvonalak a szolgáltatással segített alhálózat konfigurációjával

|Name|Címelőtag|Következő ugrás|
|----|--------------|-------|
|alhálózat – vnetlocal|MI ALHÁLÓZAT|Virtuális hálózat|
|Mi-azurecloud – régió – Internet|AzureCloud. REGION|Internet|
|Mi-azurecloud-REGION_PAIR-Internet|AzureCloud.REGION_PAIR|Internet|
|Mi – azuremonitor – Internet|AzureMonitor|Internet|
|Mi – corpnetpublic – Internet|CorpNetPublic|Internet|
|Mi – corpnetsaw – Internet|CorpNetSaw|Internet|
|Mi-eventhub – régió – Internet|EventHub. REGION|Internet|
|Mi-eventhub-REGION_PAIR-Internet|EventHub.REGION_PAIR|Internet|
|Mi – sqlmanagement – Internet|SqlManagement|Internet|
|Mi – Storage – Internet|Tárolás|Internet|
|Mi – Storage – régió – Internet|Storage. REGION|Internet|
|Mi-Storage-REGION_PAIR-Internet|Storage.REGION_PAIR|Internet|
||||

\* A MI ALHÁLÓZAT az alhálózat IP-címtartományt jelöli x. x. x. x/y formátumban. Ezeket az információkat az alhálózati tulajdonságok Azure Portaljában találja.

\** Ha a cél címe az Azure egyik szolgáltatására vonatkozik, az Azure a forgalmat az Azure gerinc hálózatán keresztül irányítja át, és nem irányítja át az internetre. Az Azure-szolgáltatások közötti forgalom attól függetlenül sem halad át az interneten, hogy melyik Azure-régióban van a virtuális hálózat, vagy hogy mely Azure-régióban van üzembe helyezve az Azure-szolgáltatás példánya. További részletekért olvassa el a [UDR dokumentációs oldalát](../../virtual-network/virtual-networks-udr-overview.md).

Emellett hozzáadhat bejegyzéseket az útválasztási táblázathoz, hogy átirányítsa a helyszíni magánhálózati IP-tartományokat a virtuális hálózati átjáró vagy a virtuális hálózati berendezés (NVA) használatával.

Ha a virtuális hálózat egyéni DNS-t tartalmaz, az egyéni DNS-kiszolgálónak képesnek kell lennie a nyilvános DNS-rekordok feloldására. Az Azure AD-hitelesítéshez hasonló további funkciókkal további teljes tartománynevek feloldására lehet szükség. További információt az [Egyéni DNS beállítása](custom-dns-configure.md)című témakörben talál.

### <a name="networking-constraints"></a>Hálózati megkötések

**A tls 1,2 a kimenő kapcsolatokon van kikényszerítve**: a január 2020 Microsoft által kényszerített TLS 1,2 az összes Azure-szolgáltatáson belüli forgalomhoz. Az Azure SQL felügyelt példányai esetében ez a TLS 1,2-t eredményezte a replikáláshoz használt kimenő kapcsolatokon és a SQL Serverhoz csatolt kiszolgáló kapcsolatain. Ha 2016-nál régebbi, SQL felügyelt példánnyal rendelkező SQL Server-verziót használ, győződjön meg arról, hogy a [TLS 1,2-specifikus frissítések](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) lettek alkalmazva.

A következő virtuális hálózati szolgáltatások jelenleg *nem támogatottak* a FELÜGYELt SQL-példányok esetében:

- **Microsoft-partneri** kapcsolat: a [Microsoft-partneri](../../expressroute/expressroute-faqs.md#microsoft-peering) kapcsolatok engedélyezése a ExpressRoute-áramkörökhöz közvetlenül vagy tranzitívnak olyan virtuális hálózattal, amelyben az SQL felügyelt példánya a virtuális hálózatban található SQL felügyelt példány-összetevők, valamint a rendelkezésre állási problémák okozta szolgáltatások közötti forgalmat érinti. Az SQL felügyelt példányok virtuális hálózatra való központi telepítése már engedélyezve van a Microsoft társközi szolgáltatásával.
- **Globális virtuális hálózati** társítás: az Azure-régiók közötti [virtuális hálózati](../../virtual-network/virtual-network-peering-overview.md) társítási kapcsolat nem működik a 9/22/2020 előtti alhálózatokban létrehozott SQL felügyelt példányok esetében.
- **AzurePlatformDNS**: a AzurePlatformDNS [szolgáltatás címkéjének](../../virtual-network/service-tags-overview.md) használata a platform DNS-feloldásának letiltásához az SQL felügyelt példánya nem érhető el. Bár az SQL felügyelt példánya támogatja az ügyfél által meghatározott DNS-feloldást a motoron belül, a platformon futó DNS-függőség függ a platform működéséhez.
- **NAT-átjáró**: az [Azure Virtual Network NAT](../../virtual-network/nat-overview.md) használata az adott nyilvános IP-címmel rendelkező kimenő kapcsolatok vezérléséhez az SQL felügyelt példánya nem érhető el. A felügyelt SQL-példány szolgáltatás jelenleg olyan alapszintű terheléselosztó használatára korlátozódik, amely nem biztosítja a bejövő és kimenő folyamatok egyidejű használatát Virtual Network NAT-ban.
- **IPv6 for Azure Virtual Network**: az SQL felügyelt példányának telepítése [kettős verem IPv4-/IPv6-alapú virtuális hálózatokra](../../virtual-network/ipv6-overview.md) várhatóan sikertelen lesz. Olyan hálózati biztonsági csoport (NSG) vagy útválasztási tábla (UDR) társítása, amely IPv6-címeket tartalmaz az SQL felügyelt példány alhálózatához, vagy IPv6-cím előtagokat ad hozzá a felügyelt példány alhálózatához már társított NSG-vagy UDR, az SQL felügyelt példánya nem érhető el. Az SQL felügyelt példányainak központi telepítése olyan NSG-és UDR rendelkező alhálózatra, amelyeken már van IPv6-előtag.

## <a name="next-steps"></a>Következő lépések

- Az áttekintést lásd: [Mi az az Azure SQL felügyelt példánya?](sql-managed-instance-paas-overview.md).
- Ismerje meg, hogyan [állíthat be egy új Azure-beli virtuális hálózatot](virtual-network-subnet-create-arm-template.md) vagy egy [meglévő Azure-beli virtuális hálózatot](vnet-existing-add-subnet.md) , ahol telepítheti az SQL felügyelt példányát.
- A felügyelt SQL-példány telepítéséhez használandó [alhálózat méretének kiszámítása](vnet-subnet-determine-size.md) .
- Ismerje meg, hogyan hozhat létre felügyelt példányt:
  - A [Azure Portal](instance-create-quickstart.md).
  - A [PowerShell](scripts/create-configure-managed-instance-powershell.md)használatával.
  - [Azure Resource Manager sablon](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/)használatával.
  - [Egy Azure Resource Manager-sablon használatával (az JumpBox használatával, a SSMS tartalmazza)](https://azure.microsoft.com/resources/templates/201-sqlmi-new-vnet-w-jumpbox/).
