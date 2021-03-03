---
title: Hálózatkezelés – áttekintés – Azure Database for PostgreSQL – rugalmas kiszolgáló
description: A kapcsolati és hálózati beállítások ismertetése a rugalmas kiszolgáló üzembe helyezési beállításában Azure Database for PostgreSQL
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/21/2021
ms.openlocfilehash: a6f049670a6860bbc195b92458945d1a53029b4f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101732802"
---
# <a name="networking-overview---azure-database-for-postgresql---flexible-server"></a>Hálózatkezelés – áttekintés – Azure Database for PostgreSQL – rugalmas kiszolgáló

> [!IMPORTANT]
> Azure Database for PostgreSQL – a rugalmas kiszolgáló előzetes verzióban érhető el

Ez a cikk a Azure Database for PostgreSQL rugalmas kiszolgáló kapcsolati és hálózatkezelési fogalmait ismerteti. 

## <a name="choosing-a-networking-option"></a>Hálózati beállítás kiválasztása
A Azure Database for PostgreSQL rugalmas kiszolgálójának két hálózati lehetősége van. A lehetőségek a következők: **privát hozzáférés (VNet-integráció)** és **nyilvános hozzáférés (engedélyezett IP-címek)**. A kiszolgáló létrehozásakor ki kell választania egy lehetőséget. 

> [!NOTE]
> A hálózati beállítás a kiszolgáló létrehozása után nem módosítható. 

* **Privát hozzáférés (VNet-integráció)** – a rugalmas kiszolgálót üzembe helyezheti az [Azure-Virtual Network](../../virtual-network/virtual-networks-overview.md). Az Azure Virtual Network privát és biztonságos hálózati kommunikációt biztosít. A virtuális hálózatok erőforrásai privát IP-címeken keresztül kommunikálhatnak.

   Ha a következő képességeket szeretné használni, válassza a VNet-integráció lehetőséget:
   * Kapcsolódás az azonos virtuális hálózatban lévő Azure-erőforrásokhoz a rugalmas kiszolgálóhoz magánhálózati IP-címek használatával
   * A VPN vagy a ExpressRoute használatával csatlakozhat a nem Azure-erőforrásokról a rugalmas kiszolgálóhoz
   * A rugalmas kiszolgálónak nincs nyilvános végpontja

* **Nyilvános hozzáférés (engedélyezett IP-címek)** – a rugalmas kiszolgáló nyilvános végponton keresztül érhető el. A nyilvános végpont egy nyilvánosan feloldható DNS-címe. Az "engedélyezett IP-címek" kifejezés számos olyan IP-címet jelöl, amelyet a kiszolgáló eléréséhez engedélyt ad. Ezeket az engedélyeket **Tűzfalszabályok** nevezzük. 

   Válassza ki a nyilvános hozzáférési módszert, ha a következő képességeket szeretné használni:
   * A virtuális hálózatokat nem támogató Azure-erőforrásokkal való kapcsolat
   * Kapcsolódás olyan Azure-on kívüli erőforrásokhoz, amelyek nem csatlakoznak VPN-vagy ExpressRoute 
   * A rugalmas kiszolgáló nyilvános végponttal rendelkezik

A következő jellemzők közül választhat, hogy használja-e a privát hozzáférést vagy a nyilvános hozzáférési lehetőséget:
* Az engedélyezett IP-címek kapcsolatait hitelesíteni kell a PostgreSQL-kiszolgálón érvényes hitelesítő adatokkal
* A hálózati forgalomhoz elérhető a [kapcsolatok titkosítása](#tls-and-ssl)
* A kiszolgáló teljes tartományneve (FQDN). A kapcsolatok karakterláncában a hostname tulajdonság esetében javasoljuk, hogy az IP-cím helyett a teljes tartománynevet használja.
* Mindkét beállítás vezérli a hozzáférést a kiszolgáló szintjén, nem az adatbázis vagy a tábla szintjén. A PostgreSQL szerepkör-tulajdonságainak használatával szabályozhatja az adatbázis, a tábla és az egyéb objektumok elérését.


## <a name="private-access-vnet-integration"></a>Privát hozzáférés (VNet-integráció)
A Virtual Network (vnet) szolgáltatással való privát hozzáférés privát és biztonságos kommunikációt biztosít a PostgreSQL rugalmas kiszolgálója számára.

### <a name="virtual-network-concepts"></a>Virtuális hálózati fogalmak
Az alábbiakban néhány olyan fogalmat talál, amely ismeri a PostgreSQL rugalmas kiszolgálóival rendelkező virtuális hálózatok használatát.

* **Virtual Network** – az Azure Virtual Network (VNet) a saját használatra konfigurált magánhálózati IP-címtartományt tartalmaz. Az Azure-beli virtuális hálózatkezeléssel kapcsolatos további információkért tekintse meg az [azure Virtual Network áttekintését](../../virtual-network/virtual-networks-overview.md) .

    A virtuális hálózatnak ugyanabban az Azure-régióban kell lennie, mint a rugalmas kiszolgálónak.


* **Delegált alhálózat** – A virtuális hálózat alhálózatokat (alhálózatokat) tartalmaz. Az alhálózatok lehetővé teszik, hogy a virtuális hálózatot kisebb címterület-területekre ossza. Az Azure-erőforrások üzembe helyezése egy adott alhálózaton történik egy virtuális hálózaton belül. 

   A PostgreSQL rugalmas kiszolgálójának olyan alhálózaton kell lennie, amely a PostgreSQL rugalmas kiszolgáló számára van **delegálva** . Ez a delegálás azt jelenti, hogy csak Azure Database for PostgreSQL rugalmas kiszolgálók használhatják ezt az alhálózatot. Az alhálózatra semmilyen más típusú Azure-erőforrás nem delegálható. Egy alhálózatot delegálhat úgy, hogy hozzárendeli a delegálási tulajdonságát a Microsoft. DBforPostgreSQL/flexibleServers.

   Adja hozzá a `Microsoft.Storage` szolgáltatás végpontját a rugalmas kiszolgálókra delegált alhálózat számára. 

* **Hálózati biztonsági csoportok (NSG)** A hálózati biztonsági csoportokban található biztonsági szabályok lehetővé teszik a virtuális hálózati alhálózatok és hálózati adapterek közötti és onnan kimenő hálózati forgalom típusának szűrését. További információkért tekintse át a [hálózati biztonsági csoport áttekintését](../../virtual-network/network-security-groups-overview.md) .


### <a name="unsupported-virtual-network-scenarios"></a>Nem támogatott virtuális hálózati forgatókönyvek
* Nyilvános végpont (vagy nyilvános IP-cím vagy DNS) – a virtuális hálózatra központilag telepített rugalmas kiszolgáló nem rendelkezhet nyilvános végponttal
* A rugalmas kiszolgáló virtuális hálózatra és alhálózatra történő telepítése után nem helyezhető át másik virtuális hálózatra vagy alhálózatra. A virtuális hálózat nem helyezhető át másik erőforráscsoporthoz vagy előfizetésbe.
* Az alhálózat mérete (Címterület) nem növelhető, ha az alhálózat erőforrásai léteznek
* A különböző régiók közötti egyenrangú virtuális hálózatok nem támogatottak

Megtudhatja, hogyan hozhat létre a [Azure Portal](how-to-manage-virtual-network-portal.md) vagy [Az Azure CLI](how-to-manage-virtual-network-cli.md)-vel egy rugalmas, privát hozzáféréssel (VNet-integrációval) rendelkező kiszolgálót.

> [!NOTE]
> Ha az egyéni DNS-kiszolgálót használja, akkor a DNS-továbbítót kell használnia a Azure Database for PostgreSQL-rugalmas kiszolgáló teljes tartománynevének feloldásához. További információért tekintse meg a névfeloldást [, amely a saját DNS-kiszolgálóját használja](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) .

## <a name="public-access-allowed-ip-addresses"></a>Nyilvános hozzáférés (engedélyezett IP-címek)
A nyilvános hozzáférési módszer jellemzői a következők:
* Csak az engedélyezett IP-címek rendelkeznek engedéllyel a PostgreSQL rugalmas kiszolgálójának eléréséhez. Alapértelmezés szerint nem engedélyezett az IP-címek használata. A kiszolgáló létrehozásakor vagy utána is hozzáadhat IP-címeket.
* A PostgreSQL-kiszolgáló nyilvánosan feloldható DNS-névvel rendelkezik
* A rugalmas kiszolgáló nem az egyik Azure-beli virtuális hálózatban van
* A kiszolgálóról érkező és onnan érkező hálózati forgalom nem halad át a magánhálózaton. A forgalom az általános internetes útvonalakat használja.

### <a name="firewall-rules"></a>Tűzfalszabályok
Az IP-címhez való engedélyek megadását tűzfalszabálynek nevezzük. Ha a kapcsolódási kísérlet olyan IP-címről érkezik, amelyet nem engedélyezett, a kezdeményező ügyfél hibaüzenetet fog látni.

Megtudhatja, hogyan hozhat létre egy rugalmas kiszolgálót nyilvános hozzáféréssel (engedélyezett IP-címekkel) [a Azure Portal](how-to-manage-firewall-portal.md) vagy [Az Azure CLI](how-to-manage-firewall-cli.md)-ben.


### <a name="allowing-all-azure-ip-addresses"></a>Az összes Azure IP-cím engedélyezése
Ha az Azure-szolgáltatáshoz nem érhető el rögzített kimenő IP-cím, az összes Azure-adatközpont IP-címéről is engedélyezheti a kapcsolódást.

> [!IMPORTANT]
> Az Azure- **szolgáltatások és-erőforrások nyilvános hozzáférésének engedélyezése az Azure** -ban beállítással konfigurálhatja a tűzfalat az Azure összes kapcsolatának engedélyezéséhez, beleértve a más ügyfelek előfizetéseit is. Ezen beállítás kiválasztásakor győződjön meg arról, hogy a bejelentkezési és felhasználói engedélyei a hozzáféréseket az arra jogosult felhasználókra korlátozzák.

### <a name="troubleshooting-public-access-issues"></a>Nyilvános hozzáférési problémák elhárítása
Vegye figyelembe a következő szempontokat, amikor a PostgreSQL-kiszolgáló Microsoft Azure adatbázisához való hozzáférés nem a várt módon viselkedik:

* **Az engedélyezési lista módosításai még nem léptek érvénybe:** A Azure Database for PostgreSQL-kiszolgáló tűzfal-konfigurációjának változásaira vonatkozóan akár öt perc is eltelhet.

* A **hitelesítés sikertelen volt:** Ha a felhasználó nem rendelkezik engedéllyel a Azure Database for PostgreSQL-kiszolgálón, vagy helytelen a használt jelszó, a rendszer megtagadja a kapcsolódást a Azure Database for PostgreSQL-kiszolgálóhoz. A tűzfalbeállítások létrehozása csak olyan ügyfelek számára biztosít lehetőséget, akik megpróbálnak csatlakozni a kiszolgálóhoz. Minden ügyfélnek továbbra is meg kell adnia a szükséges biztonsági hitelesítő adatokat.

* **Dinamikus ügyfél IP-címe:** Ha a dinamikus IP-címzéssel rendelkező internetkapcsolattal rendelkezik, és a tűzfalon keresztül nem sikerül bejutnia, akkor az alábbi megoldások valamelyikét kipróbálhatja:

   * Kérje meg az internetszolgáltatót (ISP) a Azure Database for PostgreSQL-kiszolgálót elérő ügyfélszámítógépekhez hozzárendelt IP-címtartomány számára, majd adja hozzá az IP-címtartományt tűzfalszabályként.
   * Szerezze be a statikus IP-címzést az ügyfélszámítógépek helyett, majd adja hozzá a statikus IP-címet tűzfalszabályként.

* **A tűzfalszabály IPv6 formátum esetén nem érhető el:** A tűzfalszabályok IPv4-formátumúnak kell lenniük. Ha a tűzfalszabályok IPv6-formátumban vannak megadva, az érvényesítési hiba jelenik meg.

## <a name="hostname"></a>Hostname (Gazdanév)
A választott hálózati beállítástól függetlenül ajánlott mindig teljes tartománynevet (FQDN) használni a rugalmas kiszolgálóhoz való csatlakozáskor. A kiszolgáló IP-címe nem garantált, hogy statikus maradjon. A teljes tartománynév használata segít elkerülni a kapcsolódási karakterlánc módosítását. 

Példa
* Ajánlott `hostname = servername.postgres.database.azure.com`
* Ha lehetséges, kerülje a `hostname = 10.0.0.4` (privát címek) vagy `hostname = 40.2.45.67` (nyilvános címek) használatát


## <a name="tls-and-ssl"></a>TLS és SSL
Azure Database for PostgreSQL – a rugalmas kiszolgáló támogatja az ügyfélalkalmazások csatlakoztatását a PostgreSQL szolgáltatáshoz Transport Layer Security (TLS) használatával. A TLS egy iparági szabványnak megfelelő protokoll, amely biztosítja az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti titkosított hálózati kapcsolatokat. A TLS az SSL (SSL) frissített protokollja.

Azure Database for PostgreSQL – a rugalmas kiszolgáló csak Transport Layer Security használatával támogatja a titkosított kapcsolatokat. A TLS 1,0 és a TLS 1,1 összes bejövő kapcsolata meg lesz tagadva. 

## <a name="next-steps"></a>Következő lépések
* Megtudhatja, hogyan hozhat létre a [Azure Portal](how-to-manage-virtual-network-portal.md) vagy [Az Azure CLI](how-to-manage-virtual-network-cli.md)-vel egy rugalmas, **privát hozzáféréssel (VNet-integrációval)** rendelkező kiszolgálót.
* Megtudhatja, hogyan hozhat létre egy rugalmas kiszolgálót **nyilvános hozzáféréssel (engedélyezett IP-címekkel)** [a Azure Portal](how-to-manage-firewall-portal.md) vagy [Az Azure CLI](how-to-manage-firewall-cli.md)-ben.
