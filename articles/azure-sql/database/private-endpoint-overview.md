---
title: Azure Private Link
description: A Private Endpoint szolgáltatás áttekintése.
author: rohitnayakmsft
ms.author: rohitna
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.service: sql-database
ms.topic: overview
ms.custom: sqldbrb=1, fasttrack-edit
ms.reviewer: vanto
ms.date: 03/09/2020
ms.openlocfilehash: 506982a22fd53dfb4f8f3d94735c11fc491469eb
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102097278"
---
# <a name="azure-private-link-for-azure-sql-database-and-azure-synapse-analytics"></a>Azure Private-hivatkozás Azure SQL Database és az Azure szinapszis Analyticshez
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

A privát hivatkozás lehetővé teszi, hogy egy **privát végponton** keresztül kapcsolódjon az Azure-beli különböző Pásti-szolgáltatásokhoz. A privát kapcsolati funkciót támogató Pásti-szolgáltatások listáját a [Private link dokumentációs oldalán találja](../../private-link/index.yml) . A privát végpont egy adott [VNet](../../virtual-network/virtual-networks-overview.md) és alhálózaton belüli magánhálózati IP-cím.

> [!IMPORTANT]
> Ez a cikk a Azure SQL Database és az Azure szinapszis Analytics szolgáltatásra is vonatkozik. Az egyszerűség kedvéért az "adatbázis" kifejezés a Azure SQL Database és az Azure szinapszis Analytics mindkét adatbázisára vonatkozik. Hasonlóképpen, a "Server"re mutató hivatkozások a Azure SQL Database és az Azure szinapszis Analytics szolgáltatást futtató [logikai SQL Serverre](logical-servers.md) hivatkoznak. Ez a cikk *nem* vonatkozik az **Azure SQL felügyelt példányaira**.

## <a name="how-to-set-up-private-link-for-azure-sql-database"></a>Privát hivatkozás beállítása Azure SQL Databasehoz 

### <a name="creation-process"></a>Létrehozási folyamat
A magánhálózati végpontokat a Azure Portal, a PowerShell vagy az Azure CLI használatával lehet létrehozni:
- [A portál](../../private-link/create-private-endpoint-portal.md)
- [PowerShell](../../private-link/create-private-endpoint-powershell.md)
- [Parancssori felület](../../private-link/create-private-endpoint-cli.md)

### <a name="approval-process"></a>Jóváhagyási folyamat
Miután a hálózati rendszergazda létrehozta a magánhálózati végpontot (PE), az SQL-rendszergazda felügyelheti a magánhálózati végponti kapcsolatokat (PEC) SQL Database.

1. Navigáljon a Azure Portal kiszolgáló-erőforrásához az alábbi képernyőképen látható lépések szerint

    - (1) válassza ki a privát végponti kapcsolatokat a bal oldali ablaktáblán
    - (2) az összes privát végponti kapcsolat (Pécs) listáját jeleníti meg
    - (3) a megfelelő privát végpont (PE) ![ az összes Pécsről készített képernyőképet][3]

1. Válasszon ki egy egyéni PEC-t a listából.
![A PEC kiválasztott képernyője][6]

1. Az SQL-rendszergazda dönthet úgy, hogy jóváhagyja vagy elutasítja a PEC-t, és opcionálisan egy rövid szöveges választ is hozzáadhat.
![A PEC jóváhagyásának képernyőképe][4]

1. Jóváhagyás vagy elutasítás után a lista a megfelelő állapotot jeleníti meg a válasz szövegével együtt.
![A jóváhagyás utáni összes Pécs képernyőképe][5]

## <a name="on-premises-connectivity-over-private-peering"></a>Helyi kapcsolat privát társhálózat-létesítésen keresztül

Amikor az ügyfelek a helyszíni gépekről csatlakoznak a nyilvános végponthoz, az IP-címüket [kiszolgálói szintű tűzfalszabály](firewall-create-server-level-portal-quickstart.md)használatával kell hozzáadni az IP-alapú tűzfalhoz. Habár ez a modell jól működik, hogy lehetővé tegye az egyes gépekhez való hozzáférést a fejlesztési vagy tesztelési feladatokhoz, nehéz felügyelni éles környezetben.

A privát kapcsolat lehetővé teszi, hogy az ügyfelek az [ExpressRoute](../../expressroute/expressroute-introduction.md), a privát vagy a VPN-alagúton keresztül engedélyezzék a helyszíni hozzáférést a privát végponthoz. Az ügyfelek ezután letilthatják az összes hozzáférést a nyilvános végponton keresztül, és nem használhatják az IP-alapú tűzfalat az IP-címek engedélyezésére.

## <a name="use-cases-of-private-link-for-azure-sql-database"></a>Privát hivatkozás használata Azure SQL Database esetén 

Az ügyfelek ugyanahhoz a virtuális hálózathoz, azonos régióban lévő virtuális hálózatról vagy virtuális hálózaton keresztül csatlakozhatnak a privát végponthoz, és a virtuális hálózati kapcsolaton keresztül is elérhetők a régiók között. Emellett az ügyfelek a helyszíni ExpressRoute, a privát vagy a VPN-alagúton keresztül is csatlakozhatnak. Alább látható egy egyszerűsített diagram, amely a gyakori használati eseteket mutatja.

 ![Csatlakozási lehetőségek diagramja][1]

Emellett a nem közvetlenül a virtuális hálózaton futó szolgáltatások, de integrálva vannak vele (például App Service webalkalmazások vagy függvények) az adatbázishoz való magánhálózati kapcsolat elérésére is. Az adott használati esettel kapcsolatos további információkért tekintse meg a [webalkalmazás és az Azure SQL Database](https://docs.microsoft.com/azure/architecture/example-scenario/private-web-app/private-web-app) architektúrája című témakört.

## <a name="test-connectivity-to-sql-database-from-an-azure-vm-in-same-virtual-network"></a>Az Azure-beli virtuális gép SQL Databasei kapcsolatának tesztelése ugyanazon a virtuális hálózaton

Ebben az esetben tegyük fel, hogy létrehozott egy Windows Server 2016 rendszerű Azure-beli virtuális gépet (VM). 

1. [Indítsa el a távoli asztal (RDP) munkamenetet, és kapcsolódjon a virtuális géphez](../../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine). 
1. Ezt követően elvégezheti az alapszintű kapcsolat ellenőrzéseit, hogy a virtuális gép a következő eszközök használatával csatlakozzon a SQL Databasehoz a privát végponton keresztül:
    1. Telnet
    1. Psping
    1. Nmap
    1. SQL Server Management Studio (SSMS)

### <a name="check-connectivity-using-telnet"></a>Kapcsolódás ellenőrzése a Telnet használatával

A [Telnet-ügyfél](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754293%28v%3dws.10%29) egy Windows-szolgáltatás, amely a kapcsolat tesztelésére használható. A Windows operációs rendszer verziójától függően előfordulhat, hogy explicit módon engedélyeznie kell ezt a szolgáltatást. 

A Telnet telepítése után nyisson meg egy parancssorablakot. Futtassa a Telnet parancsot, és adja meg az adatbázis IP-címét és saját végpontját SQL Database.

```
>telnet 10.1.1.5 1433
```

Ha a Telnet sikeresen csatlakozik, egy üres képernyő jelenik meg a parancsablakban, például az alábbi képen:

 ![Telnet-diagram][2]

### <a name="check-connectivity-using-psping"></a>Kapcsolat ellenőrzése a Psping használatával

A [Psping](/sysinternals/downloads/psping) a következőképpen ellenőrizhető, hogy a magánhálózati végponti kapcsolat (PEC) figyeli-e a kapcsolatokat az 1433-as porton.

Futtassa a psping az alábbiak szerint a logikai SQL Server és a 1433-es port teljes tartománynevének megadásával:

```
>psping.exe mysqldbsrvr.database.windows.net:1433

PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
Copyright (C) 2012-2016 Mark Russinovich
Sysinternals - www.sysinternals.com

TCP connect to 10.6.1.4:1433:
5 iterations (warmup 1) ping test:
Connecting to 10.6.1.4:1433 (warmup): from 10.6.0.4:49953: 2.83ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49954: 1.26ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49955: 1.98ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49956: 1.43ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49958: 2.28ms
```

A kimenet azt mutatja, hogy a Psping pingelheti a PEC-hez társított magánhálózati IP-címet.

### <a name="check-connectivity-using-nmap"></a>Kapcsolat ellenőrzése az nmap használatával

Az nmap (Network Mapper) egy ingyenes és nyílt forráskódú eszköz, amely a hálózati felderítéshez és a biztonsági naplózáshoz használatos. További információért és a letöltési hivatkozásért keresse fel a következőt: https://nmap.org . Ezzel az eszközzel ellenőrizheti, hogy a magánhálózati végpont a 1433-es porton keresztül figyeli-e a kapcsolatokat.

A következőképpen futtathatja az nmap-t úgy, hogy megadja a magánhálózati végpontot futtató alhálózat címtartomány-tartományát.

```
>nmap -n -sP 10.1.1.0/24
...
...
Nmap scan report for 10.1.1.5
Host is up (0.00s latency).
Nmap done: 256 IP addresses (1 host up) scanned in 207.00 seconds
```

Az eredmény azt mutatja, hogy egy IP-cím működik; a magánhálózati végpont IP-címének felel meg.

### <a name="check-connectivity-using-sql-server-management-studio-ssms"></a>Kapcsolat ellenőrzése SQL Server Management Studio használatával (SSMS)
> [!NOTE]
> Használja a kiszolgáló teljes tartománynevét **(FQDN)** az ügyfelek () számára a kapcsolatok karakterláncában `<server>.database.windows.net` . A közvetlenül az IP-címhez vagy a privát kapcsolat FQDN () használatával végrehajtott bejelentkezési kísérletek `<server>.privatelink.database.windows.net` sikertelenek lesznek. Ez a viselkedés a tervezés szerint történik, mivel a magánhálózati végpont továbbítja a forgalmat a régión belüli SQL-átjáró felé, és a sikeres bejelentkezéshez meg kell adni a megfelelő teljes tartománynevet.

A [SQL Databasehoz való kapcsolódáshoz](connect-query-ssms.md)kövesse az alábbi lépéseket a SSMS használatához. Miután a SSMS használatával kapcsolódott a SQL Databasehoz, ellenőrizze, hogy az Azure-beli virtuális gép magánhálózati IP-címéről csatlakozik a következő lekérdezés futtatásával:

````
select client_net_address from sys.dm_exec_connections 
where session_id=@@SPID
````

## <a name="data-exfiltration-prevention"></a>Adatkiszivárgás megelőzése

A Azure SQL Databaseban lévő adatok kiszűrése, ha egy jogosult felhasználó, például egy adatbázis-rendszergazda képes az adatok kinyerésére egy rendszerből, és a szervezeten kívül más helyre vagy rendszerre helyezi át. A felhasználó például egy harmadik fél tulajdonában lévő Storage-fiókba helyezi át az adatátvitelt.

Vegyünk például egy olyan forgatókönyvet, amelyben SQL Server Management Studio (SSMS) fut egy Azure-beli virtuális gépen, amely egy SQL Database-adatbázishoz csatlakozik. Ez az adatbázis az USA nyugati régiójának adatközpontjában található. Az alábbi példa bemutatja, hogyan korlátozható a hozzáférés nyilvános végpontokkal a SQL Database hálózati hozzáférés-vezérlés használatával.

1. Tiltsa le a nyilvános végponton keresztül SQL Database összes Azure-szolgáltatás forgalmát az Azure-szolgáltatások **kikapcsolásának** engedélyezése beállítással. Győződjön meg arról, hogy a kiszolgáló és az adatbázis szintű tűzfalszabályok nem engedélyezettek az IP-címekben. További információ: [Azure SQL Database és Azure szinapszis Analytics hálózati hozzáférés-vezérlés](network-access-controls-overview.md).
1. Csak a virtuális gép magánhálózati IP-címének használatával engedélyezze a SQL Database-adatbázis felé irányuló forgalmat. További információt a [szolgáltatás-végpont](vnet-service-endpoint-rule-overview.md) és a [virtuális hálózati tűzfalszabályok](firewall-configure.md)cikkeiben talál.
1. Az Azure-beli virtuális gépen a következő módon Szűkítse le a kimenő kapcsolatok hatókörét [hálózati biztonsági csoportok (NSG)](../../virtual-network/manage-network-security-group.md) és szolgáltatás-címkék használatával.
    - Olyan NSG-szabályt ad meg, amely engedélyezi a forgalmat a Service tag = SQL szolgáltatásban. WestUs – csak az USA nyugati régiójában lévő SQL Databasehoz való kapcsolódás engedélyezése
    - NSG-szabály megadása ( **magasabb prioritással**) a Service tag által megtagadott forgalom megtagadásához = SQL – az összes régióban SQL Database kapcsolatok megtagadása

A telepítő végén az Azure-beli virtuális gép csak az USA nyugati régiójában lévő SQL Database adatbázisához tud csatlakozni. A kapcsolat azonban nem korlátozódik egyetlen adatbázisra sem a SQL Database-ben. A virtuális gép továbbra is csatlakozhat az USA nyugati régiójában található bármely adatbázishoz, beleértve azokat az adatbázisokat is, amelyek nem részei az előfizetésnek. Noha a fenti forgatókönyvben lévő adatmennyiséget egy adott régióra csökkentették, nem kiszűrése el teljesen.

Privát hivatkozással az ügyfelek mostantól olyan hálózati hozzáférés-vezérlőket állíthatnak be, mint például a NSG a privát végponthoz való hozzáférés korlátozására. Az egyes Azure-beli Pásti-erőforrások ezután adott privát végpontokra vannak leképezve. A rosszindulatú bennfentesek csak a leképezett (például SQL Database) adatbázishoz férnek hozzá, és nincs más erőforrás. 

## <a name="limitations"></a>Korlátozások 
A magánhálózati végponttal létesített kapcsolatok csak a **proxyt** támogatják a [kapcsolati házirendként](connectivity-architecture.md#connection-policy)


## <a name="connecting-from-an-azure-vm-in-peered-virtual-network"></a>Csatlakozás egy Azure-beli virtuális gépről a Virtual Network 

Konfigurálja a [virtuális hálózati](../../virtual-network/tutorial-connect-virtual-networks-powershell.md) társítást úgy, hogy kapcsolatot létesítsen egy Azure-beli virtuális hálózatban található SQL Database.

## <a name="connecting-from-an-azure-vm-in-virtual-network-to-virtual-network-environment"></a>Csatlakozás egy Azure-beli virtuális gépről virtuális hálózatról virtuális hálózati környezetre

Konfigurálja a [virtuális hálózatot virtuális hálózati VPN Gateway-kapcsolattal](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) úgy, hogy az egy másik régióban vagy előfizetésben lévő Azure-beli virtuális gépről kapcsolatot létesít SQL Database adatbázisával.

## <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Csatlakozás helyszíni környezetből VPN-kapcsolaton keresztül

A helyszíni környezet és a SQL Database-adatbázis közötti kapcsolat létesítéséhez válassza ki és implementálja a következő lehetőségek egyikét:
- [Pont – hely kapcsolat](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
- [Helyek közötti VPN-kapcsolat](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
- [ExpressRoute áramkör](../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)


## <a name="connecting-from-azure-synapse-analytics-to-azure-storage-using-polybase-and-the-copy-statement"></a>Csatlakozás az Azure-beli szinapszis Analytics-ből az Azure Storage-ba a Base és a COPY utasítás használatával

A Base és a COPY utasítást általában az Azure szinapszis Analytics szolgáltatásba való betöltésére használják az Azure Storage-fiókokból. Ha az Azure Storage-fiók, amelyről az adatok betöltésére vonatkozó korlátozások vonatkoznak, csak a virtuális hálózati alhálózatok egy készletét érheti el privát végpontokon, szolgáltatási végpontokon vagy IP-alapú tűzfalakon keresztül, akkor a rendszer megszakítja a kapcsolatot a Base és a MÁSOLÁSi utasítással. Az importálási és exportálási forgatókönyvek az Azure-beli virtuális hálózathoz védett Azure Storage-hoz való csatlakozásának engedélyezéséhez kövesse az [itt](vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage)ismertetett lépéseket. 

## <a name="next-steps"></a>Következő lépések

- A Azure SQL Database biztonság áttekintését lásd: [az adatbázis biztonságossá tétele](security-overview.md)
- Az Azure SQL Database kapcsolatok áttekintését lásd: [Azure SQL connectivity Architecture](connectivity-architecture.md)
- A webalkalmazás az [Azure SQL Database](https://docs.microsoft.com/azure/architecture/example-scenario/private-web-app/private-web-app) architektúrájának használatára is kíváncsi lehet, amely a virtuális hálózaton kívül egy webalkalmazást kapcsol össze egy adatbázis privát végpontján.

<!--Image references-->
[1]: media/quickstart-create-single-database/pe-connect-overview.png
[2]: media/quickstart-create-single-database/telnet-result.png
[3]: media/quickstart-create-single-database/pec-list-before.png
[4]: media/quickstart-create-single-database/pec-approve.png
[5]: media/quickstart-create-single-database/pec-list-after.png
[6]: media/quickstart-create-single-database/pec-select.png