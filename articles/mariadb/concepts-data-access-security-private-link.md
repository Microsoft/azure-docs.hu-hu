---
title: Privát hivatkozás – Azure Database for MariaDB
description: Megtudhatja, hogyan működik a privát hivatkozás a Azure Database for MariaDBhoz.
author: mksuni
ms.author: sumuth
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: ec43fa995fc9772964db399baa0b6f8c2a7db1db
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98665140"
---
# <a name="private-link-for-azure-database-for-mariadb"></a>Privát hivatkozás a Azure Database for MariaDB

A privát hivatkozás lehetővé teszi, hogy saját végpontokat hozzon létre a Azure Database for MariaDBhoz, és így az Azure-szolgáltatásokat a saját Virtual Network (VNet) belül hozza létre. A privát végpont egy magánhálózati IP-címet tesz elérhetővé, amely a VNet bármely más erőforrásához hasonlóan a Azure Database for MariaDB adatbázis-kiszolgálóhoz való kapcsolódáshoz használható.

A privát kapcsolati funkciót támogató Pásti-szolgáltatások listáját a privát hivatkozás [dokumentációjában](../private-link/index.yml)tekintheti meg. A privát végpont egy adott [VNet](../virtual-network/virtual-networks-overview.md) és alhálózaton belüli magánhálózati IP-cím.

> [!NOTE]
> A privát hivatkozás funkció csak a általános célú vagy a memória optimalizált árképzési szintjein Azure Database for MariaDB-kiszolgálókon érhető el. Győződjön meg arról, hogy az adatbázis-kiszolgáló ezen díjszabási szintek egyikében található.

## <a name="data-exfiltration-prevention"></a>Adatkiszivárgás megelőzése

Az adatok Azure Database for MariaDBban való szűrése akkor történik meg, amikor egy jogosult felhasználó, például egy adatbázis-rendszergazda képes az adatok kinyerésére egy rendszerből, és a szervezeten kívül más helyre vagy rendszerre helyezi át. A felhasználó például egy harmadik fél tulajdonában lévő Storage-fiókba helyezi át az adatátvitelt.

Vegye fontolóra egy olyan forgatókönyvet, amely egy MariaDB Workbench alkalmazást futtató felhasználón belül egy Azure Database for MariaDB-példányhoz csatlakozó Azure-beli virtuális gépen fut. Ez a MariaDB-példány az USA nyugati régiójának adatközpontjában található. Az alábbi példa bemutatja, hogyan korlátozható a hozzáférés nyilvános végpontokkal a Azure Database for MariaDB hálózati hozzáférés-vezérlés használatával.

* Tiltsa le a nyilvános végponton keresztül Azure Database for MariaDB összes Azure-szolgáltatás forgalmát az Azure-szolgáltatások kikapcsolásának engedélyezése beállítással. Győződjön meg arról, hogy az IP-címek vagy tartományok nem férhetnek hozzá a kiszolgálóhoz a [Tűzfalszabályok](concepts-firewall-rules.md) vagy a [virtuális hálózati szolgáltatás végpontján](concepts-data-access-security-vnet.md)keresztül.

* Csak a virtuális gép magánhálózati IP-címének használatával engedélyezze a Azure Database for MariaDB forgalmat. További információt a [szolgáltatás-végpont](concepts-data-access-security-vnet.md) és a [VNet-tűzfalszabályok](howto-manage-vnet-portal.md)című cikkben talál.

* Az Azure-beli virtuális gépen a következő módon Szűkítse le a kimenő kapcsolatok hatókörét hálózati biztonsági csoportok (NSG) és szolgáltatás-címkék használatával:

    * Olyan NSG-szabályt ad meg, amely engedélyezi a forgalmat a Service tag = SQL szolgáltatásban. WestUs – csak az USA nyugati régiójában lévő Azure Database for MariaDBhoz való kapcsolódás engedélyezése
    * NSG-szabály megadása (magasabb prioritással) a Service tag adatforgalmának megtagadásához = SQL – a MariaDB-adatbázishoz való csatlakozás megtagadása minden régióban</br></br>

A telepítés végén az Azure-beli virtuális gép csak az USA nyugati régiójában lévő Azure Database for MariaDBhoz tud csatlakozni. Azonban a kapcsolat nem korlátozódik egyetlen Azure Database for MariaDBra. A virtuális gép továbbra is csatlakozhat az USA nyugati régiójában található bármely Azure Database for MariaDBhoz, beleértve azokat az adatbázisokat is, amelyek nem részei az előfizetésnek. Noha a fenti forgatókönyvben lévő adatmennyiséget egy adott régióra csökkentették, nem kiszűrése el teljesen.</br>

A privát hivatkozással mostantól olyan hálózati hozzáférés-vezérlőket állíthat be, mint például a NSG a privát végponthoz való hozzáférés korlátozására. Az egyes Azure-beli Pásti-erőforrások ezután adott privát végpontokra vannak leképezve. A rosszindulatú bennfentesek csak a leképezett (például egy Azure Database for MariaDB) erőforráshoz férnek hozzá, és nincs más erőforrás.

## <a name="on-premises-connectivity-over-private-peering"></a>Helyi kapcsolat privát társhálózat-létesítésen keresztül

Amikor a helyi gépekről csatlakozik a nyilvános végponthoz, az IP-címet egy kiszolgálói szintű tűzfalszabály használatával kell hozzáadnia az IP-alapú tűzfalhoz. Habár ez a modell jól működik, hogy lehetővé tegye az egyes gépekhez való hozzáférést a fejlesztési vagy tesztelési feladatokhoz, nehéz felügyelni éles környezetben.

Privát hivatkozással engedélyezheti a létesítmények közötti hozzáférést a privát végponthoz [Express Route](https://azure.microsoft.com/services/expressroute/) (er), privát vagy [VPN-alagút](../vpn-gateway/index.yml)használatával. Ezt követően a nyilvános végponton keresztül is letilthatják az összes hozzáférést, és nem használják az IP-alapú tűzfalat.

> [!NOTE]
> Bizonyos esetekben a Azure Database for MariaDB és a VNet különböző előfizetésekben találhatók. Ezekben az esetekben a következő konfigurációkat kell biztosítania:
> - Győződjön meg arról, hogy mindkét előfizetés regisztrálva van a **Microsoft. DBforMariaDB** erőforrás-szolgáltatónál. További információ: [Resource-Manager-regisztráció][resource-manager-portal]

## <a name="configure-private-link-for-azure-database-for-mariadb"></a>Privát hivatkozás konfigurálása Azure Database for MariaDBhoz

### <a name="creation-process"></a>Létrehozási folyamat

Privát végpontok szükségesek a privát kapcsolat engedélyezéséhez. Ezt a következő útmutatók segítségével végezheti el.

* [Azure Portal](howto-configure-privatelink-portal.md)
* [Parancssori felület](howto-configure-privatelink-cli.md)

### <a name="approval-process"></a>Jóváhagyási folyamat

Miután a hálózati rendszergazda létrehozta a magánhálózati végpontot (PE), a rendszergazda felügyelheti a magánhálózati végponti kapcsolatokat (PEC) Azure Database for MariaDB. A hálózati rendszergazda és a DBA közötti feladatok elkülönítése hasznos lehet az Azure Database for MariaDB-kapcsolat kezeléséhez. 

* A Azure Portalban navigáljon a Azure Database for MariaDB Server-erőforráshoz. 
    * Válassza ki a privát végponti kapcsolatokat a bal oldali ablaktáblán
    * Az összes privát végponti kapcsolat (Pécs) listáját jeleníti meg
    * Megfelelő titkos végpont (PE) létrehozva

![Válassza ki a privát végpont portált](media/concepts-data-access-and-security-private-link/select-private-link-portal.png)

* Válasszon ki egy egyéni PEC-t a listából.

![Válassza ki a privát végpont jóváhagyását.](media/concepts-data-access-and-security-private-link/select-private-link.png)

* A MariaDB-kiszolgáló rendszergazdája dönthet úgy, hogy jóváhagyja vagy elutasítja a PEC-t, és opcionálisan egy rövid szöveges választ is hozzáadhat.

![Válassza ki a privát végpont üzenetet](media/concepts-data-access-and-security-private-link/select-private-link-message.png)

* Jóváhagyás vagy elutasítás után a lista a megfelelő állapotot jeleníti meg a válasz szövegével együtt.

![Válassza ki a privát végpont végső állapotát](media/concepts-data-access-and-security-private-link/show-private-link-approved-connection.png)

## <a name="use-cases-of-private-link-for-azure-database-for-mariadb"></a>Privát hivatkozás használata Azure Database for MariaDB esetén

Az ügyfelek csatlakozhatnak a privát végponthoz ugyanarról a VNet, egymással azonos régióban lévő VNet, illetve a régiók közötti VNet-VNet kapcsolaton keresztül. Emellett az ügyfelek a helyszíni ExpressRoute, a privát vagy a VPN-alagúton keresztül is csatlakozhatnak. Alább látható egy egyszerűsített diagram, amely a gyakori használati eseteket mutatja.

![Válassza ki a privát végpont áttekintését](media/concepts-data-access-and-security-private-link/show-private-link-overview.png)

### <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Csatlakozás egy Azure-beli virtuális gépről a Virtual Network (VNet)
Konfigurálja a [VNet](../virtual-network/tutorial-connect-virtual-networks-powershell.md) -társítást úgy, hogy az Azure-beli virtuális gép Azure Database for MariaDB kapcsolatot hozzon létre egy egyenrangú VNet.

### <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>Csatlakozás egy Azure-beli virtuális gépről a VNet-VNet-környezetben
A [VNet-to-VNET VPN Gateway-kapcsolat](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) konfigurálása egy másik régióban vagy előfizetésben lévő Azure-beli virtuális gépről létesített Azure Database for MariaDB kapcsolat létesítéséhez.

### <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Csatlakozás helyszíni környezetből VPN-kapcsolaton keresztül
A helyszíni környezet és a Azure Database for MariaDB közötti kapcsolat létesítéséhez válassza ki és implementálja a következő lehetőségek egyikét:

* [Pont – hely kapcsolat](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
* [Helyek közötti VPN-kapcsolat](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [ExpressRoute áramkör](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)

## <a name="private-link-combined-with-firewall-rules"></a>Privát hivatkozás a tűzfalszabályok együttes használatával

A következő helyzetek és eredmények akkor lehetségesek, ha a privát hivatkozásokat a tűzfalszabályok kombinálva használják:

* Ha nem konfigurálja a tűzfalszabályok egyikét sem, akkor a rendszer alapértelmezés szerint nem fér hozzá a Azure Database for MariaDBhoz.

* Ha nyilvános forgalmat vagy szolgáltatási végpontot állít be, és privát végpontokat hoz létre, akkor a megfelelő tűzfalszabály engedélyezi a bejövő forgalom különböző típusait.

* Ha nem állít be nyilvános forgalmat vagy szolgáltatási végpontot, és privát végpontokat hoz létre, akkor a Azure Database for MariaDB csak a privát végpontokon keresztül érhető el. Ha nem konfigurálja a nyilvános forgalmat vagy a szolgáltatási végpontot, az összes jóváhagyott privát végpont elutasítása vagy törlése után sem lesz elérhető forgalom a Azure Database for MariaDB.

## <a name="deny-public-access-for-azure-database-for-mariadb"></a>Azure Database for MariaDB nyilvános hozzáférésének megtagadása

Ha csak privát végpontokon szeretné használni a Azure Database for MariaDB elérését, letilthatja az összes nyilvános végpont ([Tűzfalszabályok](concepts-firewall-rules.md) és [VNet-végpontok](concepts-data-access-security-vnet.md)) beállítását az adatbázis-kiszolgálón a **nyilvános hálózati hozzáférési konfiguráció megtagadása** beállítás megadásával. 

Ha ezt a beállítást az *Igen* értékre állítja, csak a magánhálózati végpontokon keresztül létesített kapcsolatok engedélyezettek a Azure Database for MariaDB. Ha ez a beállítás *nem* értékre van állítva, akkor az ügyfelek a tűzfal vagy a VNet szolgáltatás végpontjának beállításai alapján kapcsolódhatnak a Azure Database for MariaDBhoz. Emellett, ha a magánhálózati hozzáférés értéke be van állítva, az ügyfelek nem tudják felvenni és/vagy frissíteni a meglévő "tűzfalszabályok" és "VNet szolgáltatás végpontjának szabályait".

> [!Note]
> Ez a funkció minden olyan Azure-régióban elérhető, ahol a Azure Database for PostgreSQL-Single Server támogatja a általános célú és a memóriára optimalizált díjszabási szintet.
>
> Ez a beállítás nem befolyásolja a Azure Database for MariaDB SSL-és TLS-konfigurációit.

Ha meg szeretné tudni, hogyan állíthatja be a Azure Database for MariaDB számára a **nyilvános hálózati hozzáférés Megtagadását** Azure Portal, tekintse meg a [nyilvános hálózati hozzáférés megtagadásának konfigurálása](howto-deny-public-network-access.md)című témakört.

## <a name="next-steps"></a>Következő lépések

A Azure Database for MariaDB biztonsági funkcióival kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

* Ha Azure Database for MariaDB tűzfalat szeretne konfigurálni, tekintse meg a [tűzfal támogatása](concepts-firewall-rules.md)című témakört.

* Ha meg szeretné tudni, hogyan konfigurálhatja a virtuális hálózati szolgáltatás végpontját a Azure Database for MariaDB számára, tekintse meg a [virtuális hálózatok elérésének konfigurálása](concepts-data-access-security-vnet.md)című témakört.

* A Azure Database for MariaDB kapcsolatok áttekintését lásd: [Azure Database for MariaDB kapcsolati architektúra](concepts-connectivity-architecture.md)

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
