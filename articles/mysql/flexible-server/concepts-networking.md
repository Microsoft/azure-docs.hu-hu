---
title: Hálózatkezelés áttekintése – Azure Database for MySQL rugalmas kiszolgáló
description: A kapcsolati és hálózati beállítások ismertetése a rugalmas kiszolgáló üzembe helyezési beállításában Azure Database for MySQL
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 9/23/2020
ms.openlocfilehash: 48265856e5e745e05f6625766f9cd0c9b15c90a5
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2020
ms.locfileid: "91708662"
---
# <a name="connectivity-and-networking-concepts-for-azure-database-for-mysql---flexible-server-preview"></a>Kapcsolati és hálózatkezelési fogalmak a Azure Database for MySQL rugalmas kiszolgálóhoz (előzetes verzió)

Ez a cikk a kiszolgáló nyilvános és magánhálózati kapcsolati beállításait ismerteti. Részletesen megismerheti a Azure Database for MySQL rugalmas kiszolgáló hálózati fogalmait, hogy biztonságosan hozzon létre egy kiszolgálót az Azure-ban.

> [!IMPORTANT]
> Azure Database for MySQL – a rugalmas kiszolgáló előzetes verzióban érhető el.

## <a name="choosing-a-networking-option"></a>Hálózati beállítás kiválasztása
A Azure Database for MySQL rugalmas kiszolgálójának két hálózati lehetősége van. A lehetőségek a következők: **privát hozzáférés (VNet-integráció)** és **nyilvános hozzáférés (engedélyezett IP-címek)**. A kiszolgáló létrehozásakor ki kell választania egy lehetőséget. 

> [!NOTE]
> A hálózati beállítás a kiszolgáló létrehozása után nem módosítható. 

* **Privát hozzáférés (VNet-integráció)** – a rugalmas kiszolgálót üzembe helyezheti az [Azure-Virtual Network](../../virtual-network/virtual-networks-overview.md). Az Azure Virtual Network privát és biztonságos hálózati kommunikációt biztosít. A virtuális hálózatok erőforrásai privát IP-címeken keresztül kommunikálhatnak.

   Ha a következő képességeket szeretné használni, válassza a VNet-integráció lehetőséget:
   * Kapcsolódás az azonos virtuális hálózatban lévő Azure-erőforrásokhoz a rugalmas kiszolgálóhoz magánhálózati IP-címek használatával
   * A VPN vagy a ExpressRoute használatával csatlakozhat a nem Azure-erőforrásokról a rugalmas kiszolgálóhoz
   * A rugalmas kiszolgálónak nincs nyilvános végpontja

* **Nyilvános hozzáférés (engedélyezett IP-címek)** – a rugalmas kiszolgáló nyilvános végponton keresztül érhető el. A nyilvános végpont egy nyilvánosan feloldható DNS-címe. Az "engedélyezett IP-címek" kifejezés számos olyan IP-címet jelöl, amelyet a kiszolgáló eléréséhez engedélyt ad. Ezeket az engedélyeket **Tűzfalszabályok**nevezzük. 

   Válassza ki a nyilvános hozzáférési módszert, ha a következő képességeket szeretné használni:
   * A virtuális hálózatokat nem támogató Azure-erőforrásokkal való kapcsolat
   * Kapcsolódás olyan Azure-on kívüli erőforrásokhoz, amelyek nem csatlakoznak VPN-vagy ExpressRoute 
   * A rugalmas kiszolgáló nyilvános végponttal rendelkezik

A következő jellemzők közül választhat, hogy használja-e a privát hozzáférést vagy a nyilvános hozzáférési lehetőséget:
* Az engedélyezett IP-címekkel létesített kapcsolatoknak hitelesíteniük kell magukat a MySQL-kiszolgálón érvényes hitelesítő adatokkal
* A hálózati forgalomhoz elérhető a [kapcsolatok titkosítása](#tls-and-ssl)
* A kiszolgáló teljes tartományneve (FQDN). A kapcsolatok karakterláncában a hostname tulajdonság esetében javasoljuk, hogy az IP-cím helyett a teljes tartománynevet használja.
* Mindkét beállítás vezérli a hozzáférést a kiszolgáló szintjén, nem az adatbázis vagy a tábla szintjén. Az adatbázis, a tábla és az objektumokhoz való hozzáférés szabályozásához a MySQL szerepkör-tulajdonságait fogja használni.


## <a name="private-access-vnet-integration"></a>Privát hozzáférés (VNet-integráció)
A Virtual Network (vnet) szolgáltatással való magánhálózati hozzáférés privát és biztonságos kommunikációt tesz lehetővé a MySQL rugalmas kiszolgáló számára.

### <a name="virtual-network-concepts"></a>Virtuális hálózati fogalmak
Az alábbiakban néhány, a MySQL-hez rugalmas kiszolgálókkal rendelkező virtuális hálózat használatakor felismert fogalmakat talál.

* **Virtual Network** – az Azure Virtual Network (VNet) a saját használatra konfigurált magánhálózati IP-címtartományt tartalmaz. Az Azure-beli virtuális hálózatkezeléssel kapcsolatos további információkért tekintse meg az [azure Virtual Network áttekintését](../../virtual-network/virtual-networks-overview.md) .

    A virtuális hálózatnak ugyanabban az Azure-régióban kell lennie, mint a rugalmas kiszolgálónak.


* **Delegált alhálózat** – A virtuális hálózat alhálózatokat (alhálózatokat) tartalmaz. Az alhálózatok lehetővé teszik, hogy a virtuális hálózatot kisebb címterület-területekre ossza. Az Azure-erőforrások üzembe helyezése egy adott alhálózaton történik egy virtuális hálózaton belül. 

   A MySQL rugalmas kiszolgálójának olyan alhálózaton kell lennie, amely csak a MySQL rugalmas kiszolgáló számára van **delegálva** . Ez a delegálás azt jelenti, hogy csak a rugalmas Azure Database for MySQL-kiszolgálók használhatják az alhálózatot. Az alhálózatra semmilyen más típusú Azure-erőforrás nem delegálható. Egy alhálózatot delegálhat úgy, hogy hozzárendeli a delegálási tulajdonságát a Microsoft. DBforMySQL/flexibleServers.

* **Hálózati biztonsági csoportok (NSG)** A hálózati biztonsági csoportokban található biztonsági szabályok lehetővé teszik a virtuális hálózati alhálózatok és hálózati adapterek közötti és onnan kimenő hálózati forgalom típusának szűrését. További információkért tekintse át a [hálózati biztonsági csoport áttekintését](../../virtual-network/network-security-groups-overview.md) .


### <a name="unsupported-virtual-network-scenarios"></a>Nem támogatott virtuális hálózati forgatókönyvek
* Nyilvános végpont (vagy nyilvános IP-cím vagy DNS) – a virtuális hálózatra központilag telepített rugalmas kiszolgáló nem rendelkezhet nyilvános végponttal
* A rugalmas kiszolgáló virtuális hálózatra és alhálózatra történő telepítése után nem helyezhető át másik virtuális hálózatra vagy alhálózatra. A virtuális hálózat nem helyezhető át másik erőforráscsoporthoz vagy előfizetésbe.
* Az alhálózat mérete (Címterület) nem növelhető, ha az alhálózat erőforrásai léteznek
* A különböző régiók közötti egyenrangú virtuális hálózatok nem támogatottak

Ismerje meg, hogyan engedélyezheti a privát hozzáférést (vnet-integráció) a [Azure Portal](how-to-manage-virtual-network-portal.md) vagy az [Azure CLI](how-to-manage-virtual-network-cli.md)használatával.


## <a name="public-access-allowed-ip-addresses"></a>Nyilvános hozzáférés (engedélyezett IP-címek)
A nyilvános hozzáférési módszer jellemzői a következők:
* Csak az engedélyezett IP-címek jogosultak a MySQL rugalmas kiszolgáló elérésére. Alapértelmezés szerint nem engedélyezett az IP-címek használata. A kiszolgáló létrehozásakor vagy utána is hozzáadhat IP-címeket.
* A MySQL-kiszolgáló nyilvánosan feloldható DNS-névvel rendelkezik
* A rugalmas kiszolgáló nem az egyik Azure-beli virtuális hálózatban van
* A kiszolgálóról érkező és onnan érkező hálózati forgalom nem halad át a magánhálózaton. A forgalom az általános internetes útvonalakat használja.

### <a name="firewall-rules"></a>Tűzfalszabályok
Az IP-címhez való engedélyek megadását tűzfalszabálynek nevezzük. Ha a kapcsolódási kísérlet olyan IP-címről érkezik, amelyet nem engedélyezett, a kezdeményező ügyfél hibaüzenetet fog látni.


### <a name="allowing-all-azure-ip-addresses"></a>Az összes Azure IP-cím engedélyezése
Ha az Azure-szolgáltatáshoz nem érhető el rögzített kimenő IP-cím, az összes Azure-adatközpont IP-címéről is engedélyezheti a kapcsolódást.

> [!IMPORTANT]
> Az Azure- **szolgáltatások és-erőforrások nyilvános hozzáférésének engedélyezése az Azure** -ban beállítással konfigurálhatja a tűzfalat az Azure összes kapcsolatának engedélyezéséhez, beleértve a más ügyfelek előfizetéseit is. Ezen beállítás kiválasztásakor győződjön meg arról, hogy a bejelentkezési és felhasználói engedélyei a hozzáféréseket az arra jogosult felhasználókra korlátozzák.

Megtudhatja, hogyan engedélyezheti és kezelheti a nyilvános hozzáférést (engedélyezett IP-címek) a [Azure Portal](how-to-manage-firewall-portal.md) vagy az [Azure CLI](how-to-manage-firewall-cli.md)használatával.


### <a name="troubleshooting-public-access-issues"></a>Nyilvános hozzáférési problémák elhárítása
Vegye figyelembe a következő szempontokat, amikor a MySQL-kiszolgáló szolgáltatáshoz való Microsoft Azure-adatbázishoz való hozzáférés nem a várt módon viselkedik:

* **Az engedélyezési lista módosításai még nem léptek érvénybe:** A Azure Database for MySQL-kiszolgáló tűzfal-konfigurációjának változásaira vonatkozóan akár öt perc is eltelhet.

* A **hitelesítés sikertelen volt:** Ha a felhasználó nem rendelkezik engedéllyel a Azure Database for MySQL-kiszolgálón, vagy helytelen a használt jelszó, a rendszer megtagadja a kapcsolódást a Azure Database for MySQL-kiszolgálóhoz. A tűzfalbeállítások létrehozása csak olyan ügyfelek számára biztosít lehetőséget, akik megpróbálnak csatlakozni a kiszolgálóhoz. Minden ügyfélnek továbbra is meg kell adnia a szükséges biztonsági hitelesítő adatokat.

* **Dinamikus ügyfél IP-címe:** Ha a dinamikus IP-címzéssel rendelkező internetkapcsolattal rendelkezik, és a tűzfalon keresztül nem sikerül bejutnia, akkor az alábbi megoldások valamelyikét kipróbálhatja:

   * Kérje meg az internetszolgáltatót (ISP) a Azure Database for MySQL-kiszolgálót elérő ügyfélszámítógépekhez hozzárendelt IP-címtartomány számára, majd adja hozzá az IP-címtartományt tűzfalszabályként.
   * Szerezze be a statikus IP-címzést az ügyfélszámítógépek helyett, majd adja hozzá a statikus IP-címet tűzfalszabályként.


## <a name="hostname"></a>Hostname (Gazdanév)
A választott hálózati beállítástól függetlenül ajánlott mindig teljes tartománynevet (FQDN) használni a rugalmas kiszolgálóhoz való csatlakozáskor. A kiszolgáló IP-címe nem garantált, hogy statikus maradjon. A teljes tartománynév használata segít elkerülni a kapcsolódási karakterlánc módosítását. 

Példa
* Ajánlott `hostname = servername.mysql.database.azure.com`
* Ha lehetséges, kerülje a `hostname = 10.0.0.4` (privát cím) vagy `hostname = 40.2.45.67` a (nyilvános IP) használatát



## <a name="tls-and-ssl"></a>TLS és SSL
Azure Database for MySQL rugalmas kiszolgáló a Transport Layer Security (TLS) használatával támogatja az ügyfélalkalmazások a MySQL szolgáltatáshoz való csatlakoztatását. A TLS egy iparági szabványnak megfelelő protokoll, amely biztosítja az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti titkosított hálózati kapcsolatokat. A TLS a SSL (SSL) frissített protokollja.

Azure Database for MySQL rugalmas kiszolgáló csak a titkosított kapcsolatokat támogatja Transport Layer Security használatával (TLS 1,2). A TLS 1,0 és a TLS 1,1 összes bejövő kapcsolata meg lesz tagadva. A TLS verziója nem tiltható le vagy nem módosítható Azure Database for MySQL rugalmas kiszolgálóhoz való csatlakozáshoz.


## <a name="next-steps"></a>Következő lépések
* Arra vonatkozó információk, hogyan engedélyezhető a privát hozzáférés (VNet-integráció) az [Azure Portalon](how-to-manage-virtual-network-portal.md) vagy az [Azure CLI-vel](how-to-manage-virtual-network-cli.md)
* Arra vonatkozó információk, hogyan engedélyezhető a nyilvános hozzáférés (engedélyezett IP-címek) az [Azure Portalon](how-to-manage-firewall-portal.md) vagy az [Azure CLI-vel](how-to-manage-firewall-cli.md)
* A [TLS használatának](how-to-connect-tls-ssl.md) ismertetése
