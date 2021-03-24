---
title: Privát hivatkozás – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Megtudhatja, hogyan működik a Private link Azure Database for PostgreSQL-Single Server esetében.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: e4b6a6090bf0e5e332a960cba8ec565df9dd55c2
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104872268"
---
# <a name="private-link-for-azure-database-for-postgresql-single-server"></a>Privát hivatkozás az Azure Database for PostgreSQL-Single Serverhez

A privát hivatkozás lehetővé teszi, hogy saját végpontokat hozzon létre a Azure Database for PostgreSQL-egyetlen kiszolgáló számára, hogy az a Virtual Networkon (VNet) belülre kerüljön. A privát végpont egy olyan alhálózaton belüli magánhálózati IP-címet tesz elérhetővé, amely az adatbázis-kiszolgálóhoz való kapcsolódáshoz használható, ugyanúgy, mint a VNet lévő többi erőforrás.

A privát kapcsolati funkciót támogató Pásti-szolgáltatások listáját a privát hivatkozás [dokumentációjában](../private-link/index.yml)tekintheti meg. A privát végpont egy adott [VNet](../virtual-network/virtual-networks-overview.md) és alhálózaton belüli magánhálózati IP-cím.

> [!NOTE]
> A privát hivatkozás funkció csak a általános célú vagy a memória optimalizált árképzési szintjein Azure Database for PostgreSQL-kiszolgálókon érhető el. Győződjön meg arról, hogy az adatbázis-kiszolgáló ezen díjszabási szintek egyikében található.

## <a name="data-exfiltration-prevention"></a>Adatkiszivárgás megelőzése

Az adatok Azure Database for PostgreSQL önálló kiszolgálón való szűrése olyankor történik, amikor egy jogosult felhasználó, például egy adatbázis-rendszergazda képes az adatok kinyerésére egy rendszerből, és a szervezeten kívül más helyre vagy rendszerre helyezi át. A felhasználó például egy harmadik fél tulajdonában lévő Storage-fiókba helyezi át az adatátvitelt.

Vegyünk egy olyan forgatókönyvet, amely egy PGAdmin-t futtató felhasználón belül fut egy olyan Azure-beli virtuális gépen (VM), amely az USA nyugati régiójában kiépített Azure Database for PostgreSQL egyetlen kiszolgálóhoz csatlakozik. Az alábbi példa bemutatja, hogyan korlátozható a hozzáférés nyilvános végpontokkal Azure Database for PostgreSQL egyetlen kiszolgálón a hálózati hozzáférés-vezérlés használatával.

* Az *Azure-szolgáltatások* kikapcsolásának engedélyezése beállítás megadásával tiltsa le az összes Azure-szolgáltatás forgalmát Azure Database for PostgreSQL egyetlen kiszolgálóra a nyilvános végponton keresztül. Győződjön meg arról, hogy az IP-címek vagy tartományok nem férhetnek hozzá a kiszolgálóhoz a [Tűzfalszabályok](./concepts-firewall-rules.md) vagy a [virtuális hálózati szolgáltatás végpontján](./concepts-data-access-and-security-vnet.md)keresztül.

* Csak a virtuális gép magánhálózati IP-címének használatával engedélyezze a Azure Database for PostgreSQL egyetlen kiszolgálóra irányuló forgalmat. További információt a [szolgáltatás-végpont](concepts-data-access-and-security-vnet.md) és a VNet-Tűzfalszabályok című cikkben talál [.](howto-manage-vnet-using-portal.md)

* Az Azure-beli virtuális gépen a következő módon Szűkítse le a kimenő kapcsolatok hatókörét hálózati biztonsági csoportok (NSG) és szolgáltatás-címkék használatával.

    * Olyan NSG-szabályt ad meg, amely engedélyezi a forgalmat a *Service tag = SQL szolgáltatásban. WestUS* – csak az USA nyugati régiójában Azure Database for PostgreSQL egyetlen kiszolgálóval létesített kapcsolat engedélyezése
    * NSG-szabály megadása (magasabb prioritással) a *Service tag* adatforgalmának megtagadásához – SQL – a PostgreSQL-adatbázishoz való csatlakozás megtagadása minden régióban</br></br>

A telepítés végén az Azure-beli virtuális gép csak az USA nyugati régiójában lévő Azure Database for PostgreSQL egyetlen kiszolgálóhoz tud csatlakozni. A kapcsolat azonban egyetlen Azure Database for PostgreSQL egyetlen kiszolgálóra korlátozódik. A virtuális gép továbbra is csatlakozhat az USA nyugati régiójában található bármely Azure Database for PostgreSQL egyetlen kiszolgálóhoz, beleértve azokat az adatbázisokat is, amelyek nem részei az előfizetésnek. Noha a fenti forgatókönyvben lévő adatmennyiséget egy adott régióra csökkentették, nem kiszűrése el teljesen.</br>

A privát hivatkozással mostantól olyan hálózati hozzáférés-vezérlőket állíthat be, mint például a NSG a privát végponthoz való hozzáférés korlátozására. Az egyes Azure-beli Pásti-erőforrások ezután adott privát végpontokra vannak leképezve. A rosszindulatú bennfentesek csak a leképezett (például egy Azure Database for PostgreSQL kiszolgáló) erőforráshoz férnek hozzá, és nincs más erőforrás.

## <a name="on-premises-connectivity-over-private-peering"></a>Helyi kapcsolat privát társhálózat-létesítésen keresztül

Amikor a helyi gépekről csatlakozik a nyilvános végponthoz, az IP-címet egy kiszolgálói szintű tűzfalszabály használatával kell hozzáadnia az IP-alapú tűzfalhoz. Habár ez a modell jól működik, hogy lehetővé tegye az egyes gépekhez való hozzáférést a fejlesztési vagy tesztelési feladatokhoz, nehéz felügyelni éles környezetben.

Privát hivatkozással engedélyezheti a létesítmények közötti hozzáférést a privát végponthoz [Express Route](https://azure.microsoft.com/services/expressroute/) (er), privát vagy [VPN-alagút](../vpn-gateway/index.yml)használatával. Ezt követően a nyilvános végponton keresztül is letilthatják az összes hozzáférést, és nem használják az IP-alapú tűzfalat.

> [!NOTE]
> Bizonyos esetekben a Azure Database for PostgreSQL és a VNet különböző előfizetésekben találhatók. Ezekben az esetekben a következő konfigurációkat kell biztosítania:
> - Győződjön meg arról, hogy mindkét előfizetés regisztrálva van a **Microsoft. DBforPostgreSQL** erőforrás-szolgáltatónál. További információ: [Resource-Manager-regisztráció][resource-manager-portal]

## <a name="configure-private-link-for-azure-database-for-postgresql-single-server"></a>Privát hivatkozás konfigurálása Azure Database for PostgreSQL egyetlen kiszolgálóhoz

### <a name="creation-process"></a>Létrehozási folyamat

Privát végpontok szükségesek a privát kapcsolat engedélyezéséhez. Ezt a következő útmutatók segítségével végezheti el.

* [Azure Portal](./howto-configure-privatelink-portal.md)
* [Parancssori felület](./howto-configure-privatelink-cli.md)

### <a name="approval-process"></a>Jóváhagyási folyamat
Miután a hálózati rendszergazda létrehozta a magánhálózati végpontot (PE), a PostgreSQL-rendszergazda felügyelheti a magánhálózati végponti kapcsolatokat (PEC) Azure Database for PostgreSQL. A hálózati rendszergazda és a DBA közötti feladatok elkülönítése hasznos lehet az Azure Database for PostgreSQL-kapcsolat kezeléséhez. 

* A Azure Portalban navigáljon a Azure Database for PostgreSQL Server-erőforráshoz. 
    * Válassza ki a privát végponti kapcsolatokat a bal oldali ablaktáblán
    * Az összes privát végponti kapcsolat (Pécs) listáját jeleníti meg
    * Megfelelő titkos végpont (PE) létrehozva

:::image type="content" source="media/concepts-data-access-and-security-private-link/select-private-link-portal.png" alt-text="Válassza ki a privát végpont portált":::

* Válasszon ki egy egyéni PEC-t a listából.

:::image type="content" source="media/concepts-data-access-and-security-private-link/select-private-link.png" alt-text="Válassza ki a privát végpont jóváhagyását.":::

* A PostgreSQL-kiszolgáló rendszergazdája dönthet úgy, hogy jóváhagyja vagy elutasítja a PEC-t, és opcionálisan egy rövid szöveges választ is hozzáadhat.

:::image type="content" source="media/concepts-data-access-and-security-private-link/select-private-link-message.png" alt-text="Válassza ki a privát végpont üzenetet":::

* Jóváhagyás vagy elutasítás után a lista a megfelelő állapotot jeleníti meg a válasz szövegével együtt.

:::image type="content" source="media/concepts-data-access-and-security-private-link/show-private-link-approved-connection.png" alt-text="Válassza ki a privát végpont végső állapotát":::

## <a name="use-cases-of-private-link-for-azure-database-for-postgresql"></a>Privát hivatkozás használata Azure Database for PostgreSQL esetén


Az ügyfelek csatlakozhatnak a privát végponthoz ugyanarról a VNet, azonos régióban vagy régiókban található [VNet](../virtual-network/virtual-network-peering-overview.md) , illetve a régiók közötti [VNet-VNet kapcsolaton](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) keresztül. Emellett az ügyfelek a helyszíni ExpressRoute, a privát vagy a VPN-alagúton keresztül is csatlakozhatnak. Alább látható egy egyszerűsített diagram, amely a gyakori használati eseteket mutatja.

:::image type="content" source="media/concepts-data-access-and-security-private-link/show-private-link-overview.png" alt-text="Válassza ki a privát végpont áttekintését":::

### <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Csatlakozás egy Azure-beli virtuális gépről a Virtual Network (VNet)
Konfigurálja a [VNet](../virtual-network/tutorial-connect-virtual-networks-powershell.md) -társítást úgy, hogy kapcsolatot létesítsen a Azure Database for PostgreSQL egyetlen kiszolgálóval egy Azure-beli virtuális gépről egy társ VNet.

### <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>Csatlakozás egy Azure-beli virtuális gépről a VNet-VNet-környezetben
Konfigurálja a [VNet-to-VNET VPN Gateway-kapcsolatot](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) úgy, hogy az egy másik régióban vagy előfizetésben lévő Azure-beli virtuális gépről kapcsolatot hozzon létre egy Azure Database for PostgreSQL egyetlen kiszolgálóval.

### <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Csatlakozás helyszíni környezetből VPN-kapcsolaton keresztül
Ha helyszíni környezetből kíván kapcsolatot létesíteni a Azure Database for PostgreSQL-egyetlen kiszolgálóval, válasszon ki és implementáljon egy lehetőséget:

* [Pont – hely kapcsolat](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
* [Helyek közötti VPN-kapcsolat](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [ExpressRoute áramkör](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)

## <a name="private-link-combined-with-firewall-rules"></a>Privát hivatkozás a tűzfalszabályok együttes használatával

A következő helyzetek és eredmények akkor lehetségesek, ha a privát hivatkozásokat a tűzfalszabályok kombinálva használják:

* Ha nem konfigurálja a tűzfalszabályok egyikét sem, akkor a rendszer alapértelmezés szerint nem fogja tudni elérni a Azure Database for PostgreSQL egyetlen kiszolgálóval.

* Ha nyilvános forgalmat vagy szolgáltatási végpontot állít be, és privát végpontokat hoz létre, akkor a megfelelő tűzfalszabály engedélyezi a bejövő forgalom különböző típusait.

* Ha nem állít be nyilvános forgalmat vagy szolgáltatási végpontot, és privát végpontokat hoz létre, akkor a Azure Database for PostgreSQL egyetlen kiszolgáló csak a privát végpontokon keresztül érhető el. Ha nem konfigurálja a nyilvános forgalmat vagy a szolgáltatási végpontot, az összes jóváhagyott privát végpont elutasítása vagy törlése után sem lesz elérhető forgalom a Azure Database for PostgreSQL egyetlen kiszolgálóval.

## <a name="deny-public-access-for-azure-database-for-postgresql-single-server"></a>Azure Database for PostgreSQL egyetlen kiszolgáló nyilvános hozzáférésének megtagadása

Ha csak privát végpontokat szeretne használni a Azure Database for PostgreSQL egyetlen kiszolgálóhoz való hozzáféréshez, letilthatja az összes nyilvános végpont ([Tűzfalszabályok](concepts-firewall-rules.md) és [VNet-végpontok](concepts-data-access-and-security-vnet.md)) beállítását úgy, hogy **megtagadja a nyilvános hálózati hozzáférési konfiguráció megtagadását** az adatbázis-kiszolgálón. 

Ha a beállítás értéke *Igen* , akkor csak a magánhálózati végpontokon keresztül létesített kapcsolatok engedélyezettek a Azure Database for PostgreSQL. Ha ezt a beállítást úgy állítja be, hogy *egyetlen ügyfél sem* tud csatlakozni a Azure Database for PostgreSQL a tűzfal vagy a VNet szolgáltatás végpont-beállítása alapján. Emellett, ha a magánhálózati hozzáférés értéke be van állítva, az ügyfelek nem tudják felvenni és/vagy frissíteni a meglévő "tűzfalszabályok" és "VNet szolgáltatás végpontjának szabályait".

> [!Note]
> Ez a funkció minden olyan Azure-régióban elérhető, ahol a Azure Database for PostgreSQL-Single Server támogatja a általános célú és a memóriára optimalizált díjszabási szintet.
>
> Ez a beállítás nem befolyásolja az Azure Database for PostgreSQL egyetlen kiszolgáló SSL-és TLS-konfigurációit.

Ha meg szeretné tudni, hogyan állíthatja be a **nyilvános hálózati hozzáférés megtagadása** a Azure Database for PostgreSQL egyetlen kiszolgáló Azure Portal-ról, tekintse meg a [nyilvános hálózati hozzáférés megtagadásának beállítása](howto-deny-public-network-access.md)című témakört.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a Azure Database for PostgreSQL egyetlen kiszolgáló biztonsági funkcióival kapcsolatban, tekintse meg a következő cikkeket:

* Ha tűzfalat szeretne konfigurálni Azure Database for PostgreSQL egyetlen kiszolgálóhoz, tekintse meg a [tűzfal támogatása](./concepts-firewall-rules.md)című témakört.

* Ha meg szeretné tudni, hogyan konfigurálhatja a virtuális hálózati szolgáltatás végpontját a Azure Database for PostgreSQL egyetlen kiszolgálóhoz, tekintse meg a [virtuális hálózatok elérésének konfigurálása](./concepts-data-access-and-security-vnet.md)című témakört.

* Az egykiszolgálós kapcsolatok Azure Database for PostgreSQLának áttekintését lásd: [Azure Database for PostgreSQL kapcsolati architektúra](./concepts-connectivity-architecture.md)

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
