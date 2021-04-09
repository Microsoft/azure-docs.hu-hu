---
title: Tűzfalszabályok – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ez a cikk azt ismerteti, hogyan használhatók a tűzfalszabályok a Azure Database for PostgreSQL-egyetlen kiszolgálóhoz való kapcsolódáshoz.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: ba353cf41cf3876a681f8f18d4121401260ff4ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98877170"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---single-server"></a>Tűzfalszabályok Azure Database for PostgreSQL – egyetlen kiszolgáló
A Azure Database for PostgreSQL-kiszolgáló alapértelmezés szerint biztonságos, így nem kell megadnia az adatbázis-kiszolgáló elérését, amíg meg nem adja, hogy mely IP-állomások férhessenek hozzá. A tűzfal az egyes kérések származó IP-címe alapján engedélyezi a hozzáférést a kiszolgálóhoz.
A tűzfal konfigurálásakor olyan tűzfalszabályokat adhat meg, amelyek meghatározzák az elfogadható IP-címtartományokat. A tűzfalszabályok a kiszolgáló szintjén hozhatók létre.

**Tűzfalszabályok:** Ezek a szabályok lehetővé teszik az ügyfelek számára, hogy elérjék a teljes Azure Database for PostgreSQL-kiszolgálót, azaz az azonos logikai kiszolgálón belüli összes adatbázist. A kiszolgálói szintű tűzfalszabályok a Azure Portal vagy az Azure CLI-parancsok használatával konfigurálhatók. A kiszolgálói szintű tűzfalszabályok létrehozásához az előfizetés tulajdonosának vagy az előfizetés közreműködőinek kell lennie.

## <a name="firewall-overview"></a>Tűzfal áttekintése
A tűzfal alapértelmezés szerint letiltja a Azure Database for PostgreSQL-kiszolgálóhoz való hozzáférést. Ha egy másik számítógépről/ügyfélből vagy alkalmazásból kívánja elérni a kiszolgálót, meg kell adnia egy vagy több kiszolgálói szintű tűzfalszabályok a kiszolgálóhoz való hozzáférés engedélyezéséhez. A tűzfalszabályok segítségével megadhatja az engedélyezett nyilvános IP-címtartományt. A tűzfalszabályok nem érintik a Azure Portal webhelyhez való hozzáférést.
Az internetről és az Azure-ról érkező csatlakozási kísérleteknek előbb át kell haladnia a tűzfalon, mielőtt elérnék a PostgreSQL-adatbázist, ahogy az a következő ábrán is látható:

:::image type="content" source="media/concepts-firewall-rules/1-firewall-concept.png" alt-text="Példa a tűzfal működésének folyamatára":::

## <a name="connecting-from-the-internet"></a>Csatlakozás az internetről
A kiszolgálói szintű tűzfalszabályok a Azure Database for PostgreSQL kiszolgálón található összes adatbázisra érvényesek. Ha a kérelem forrás IP-címe a kiszolgálói szintű tűzfalszabályok megadott tartományán belül van, akkor a rendszer nem fogadja el a kapcsolatokat. Ha például az alkalmazás a PostgreSQL-hez készült JDBC-illesztőprogrammal csatlakozik, akkor ez a hiba akkor jelentkezhet, amikor a tűzfal blokkolja a kapcsolatot.
> java.util.concurrent.ExecutionException: Java. lang. RuntimeException: org. PostgreSQL. util. PSQLException: végzetes: nincs PG \_ HBA. conf bejegyzés a következő gazdagépre: "123.45.67.890", "adminuser", "", adatbázis: PostgreSQL, SSL

## <a name="connecting-from-azure"></a>Csatlakozás az Azure-ból
Javasoljuk, hogy keresse meg bármely alkalmazás vagy szolgáltatás kimenő IP-címét, és explicit módon engedélyezze az adott IP-címekhez vagy tartományokhoz való hozzáférést. Megkeresheti például egy Azure App Service kimenő IP-címét, vagy használhat egy virtuális géphez vagy más erőforráshoz kötött nyilvános IP-címet (lásd alább a virtuális gép magánhálózati IP-címével való csatlakozáshoz a szolgáltatási végpontokon). 

Ha az Azure-szolgáltatáshoz nem érhető el rögzített kimenő IP-cím, az összes Azure-adatközpont IP-címéről is engedélyezheti a kapcsolódást. Ezt a beállítást engedélyezheti a Azure Portal az **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** lehetőségre a **kapcsolat biztonsági** paneljén **, és** a **Mentés** gombra kattintva. Az Azure CLI-ből egy tűzfalszabály-beállítás, amely a 0.0.0.0 értékkel egyenlő, és ezzel egyenértékű. Ha a tűzfalszabályok elutasították a kapcsolódási kísérletet, az nem éri el a Azure Database for PostgreSQL-kiszolgálót.

> [!IMPORTANT]
> Az **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** beállítással konfigurálhatja a tűzfalat úgy, hogy az az Azure összes kapcsolatát engedélyezze, beleértve a más ügyfelek előfizetéseit is. Ezen beállítás kiválasztásakor győződjön meg arról, hogy a bejelentkezési és felhasználói engedélyei a hozzáféréseket az arra jogosult felhasználókra korlátozzák.
> 

:::image type="content" source="media/concepts-firewall-rules/allow-azure-services.png" alt-text="Az Azure-szolgáltatásokhoz való hozzáférés engedélyezése a portálon":::

## <a name="connecting-from-a-vnet"></a>Csatlakozás VNet
Ha biztonságosan szeretne csatlakozni a Azure Database for PostgreSQL-kiszolgálóhoz egy VNet, vegye fontolóra a [VNet szolgáltatás-végpontok](./concepts-data-access-and-security-vnet.md)használatát. 

## <a name="programmatically-managing-firewall-rules"></a>Tűzfalszabályok szoftveres kezelése
A Azure Portalon kívül a tűzfalszabályok programozott módon is kezelhetők az Azure CLI használatával.
Lásd még: [Azure Database for PostgreSQL tűzfalszabályok létrehozása és kezelése az Azure CLI-](howto-manage-firewall-using-cli.md) vel

## <a name="troubleshooting-firewall-issues"></a>Tűzfalakkal kapcsolatos problémák elhárítása
Vegye figyelembe a következő szempontokat, amikor a PostgreSQL-kiszolgáló Microsoft Azure adatbázisához való hozzáférés nem a várt módon viselkedik:

* **Az engedélyezési lista módosításai még nem léptek érvénybe:** A Azure Database for PostgreSQL-kiszolgáló tűzfal-konfigurációjának változásaira vonatkozóan akár öt perc is eltelhet.

* **A bejelentkezési azonosító nincs engedélyezve, vagy helytelen jelszót használt:** Ha a bejelentkezési azonosító nem rendelkezik engedéllyel a Azure Database for PostgreSQL-kiszolgálón, vagy helytelen a használt jelszó, a rendszer megtagadja a kapcsolódást a Azure Database for PostgreSQL-kiszolgálóhoz. A tűzfalbeállítások létrehozása csak olyan ügyfelek számára biztosít lehetőséget, akik megpróbálnak csatlakozni a kiszolgálóhoz; minden ügyfélnek továbbra is meg kell adnia a szükséges biztonsági hitelesítő adatokat.

   Ha például egy JDBC-ügyfelet használ, a következő hibaüzenet jelenhet meg.
   > java.util.concurrent.ExecutionException: Java. lang. RuntimeException: org. PostgreSQL. util. PSQLException: végzetes: a jelszó-hitelesítés sikertelen volt a (z) "Felhasználónév" felhasználó számára

* **Dinamikus IP-cím**: Ha az internetkapcsolata dinamikus IP-címkezeléssel rendelkezik, és problémákat okoz a tűzfalon való átjutás, próbálja ki a következő megoldások valamelyikét:

   * Kérje meg az internetszolgáltatót (ISP) a Azure Database for PostgreSQL-kiszolgálót elérő ügyfélszámítógépekhez hozzárendelt IP-címtartomány számára, majd adja hozzá az IP-címtartományt tűzfalszabályként.

   * Szerezze be a statikus IP-címzést az ügyfélszámítógépek helyett, majd adja hozzá a statikus IP-címet tűzfalszabályként.

* **Úgy tűnik, hogy a kiszolgáló IP-címe nyilvános:** A Azure Database for PostgreSQL-kiszolgálóval létesített kapcsolatok egy nyilvánosan elérhető Azure-átjárón keresztül irányíthatók. A kiszolgáló tényleges IP-címét azonban tűzfal védi. További információért tekintse meg a [kapcsolati architektúráról szóló cikket](concepts-connectivity-architecture.md).

* **Nem lehet kapcsolatot létesíteni az Azure-erőforrással az engedélyezett IP-címmel:** Győződjön meg arról, hogy engedélyezve van-e a **Microsoft. SQL** szolgáltatási végpontja azon az alhálózaton, amelyhez csatlakozik. Ha a **Microsoft. SQL** engedélyezve van, az azt jelzi, hogy csak a [VNet-szolgáltatás végponti szabályait](concepts-data-access-and-security-vnet.md) szeretné használni az adott alhálózaton.

   Előfordulhat például, hogy a következő hiba jelenik meg, ha egy Azure-beli virtuális gépről csatlakozik egy olyan alhálózaton, amelyhez engedélyezve van a **Microsoft. SQL** , de nincs megfelelő VNet-szabálya:  `FATAL: Client from Azure Virtual Networks is not allowed to access the server`

* **A tűzfalszabály IPv6 formátum esetén nem érhető el:** A tűzfalszabályok IPv4-formátumúnak kell lenniük. Ha a tűzfalszabályok IPv6-formátumban vannak megadva, az érvényesítési hiba jelenik meg.


## <a name="next-steps"></a>Következő lépések
* [Azure Database for PostgreSQL tűzfalszabályok létrehozása és kezelése a Azure Portal használatával](howto-manage-firewall-using-portal.md)
* [Azure Database for PostgreSQL tűzfalszabályok létrehozása és kezelése az Azure CLI-vel](howto-manage-firewall-using-cli.md)
* [VNet szolgáltatásbeli végpontok a Azure Database for PostgreSQL](./concepts-data-access-and-security-vnet.md)
