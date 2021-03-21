---
title: Kapcsolati architektúra – Azure Database for MariaDB
description: A Azure Database for MariaDB-kiszolgáló kapcsolati architektúráját ismerteti.
author: Bashar-MSFT
ms.author: bahusse
ms.service: mariadb
ms.topic: conceptual
ms.date: 2/11/2021
ms.openlocfilehash: 50aaae9e71ac9de366ee4db1981e633491094946
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103199973"
---
# <a name="connectivity-architecture-in-azure-database-for-mariadb"></a>Kapcsolati architektúra a Azure Database for MariaDBban
Ez a cikk ismerteti a Azure Database for MariaDB kapcsolati architektúrát, valamint azt, hogy a forgalom hogyan legyen átirányítva a Azure Database for MariaDB-példányra az Azure-on belüli és kívüli ügyfelektől.

## <a name="connectivity-architecture"></a>Kapcsolati architektúra

A Azure Database for MariaDB kapcsolata egy olyan átjárón keresztül jön létre, amely felelős a bejövő kapcsolatok útválasztásához a fürtben lévő kiszolgáló fizikai helyén. A következő ábra a forgalmat mutatja be.

![A kapcsolati architektúra áttekintése](./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png)


Ahogy az ügyfél csatlakozik az adatbázishoz, a kiszolgáló kapcsolati karakterlánca feloldódik az átjáró IP-címével. Az átjáró az IP-címet figyeli az 3306-as porton. Az adatbázis-fürtön belül a rendszer a megfelelő Azure Database for MariaDB továbbítja a forgalmat. Ezért ahhoz, hogy csatlakozni tudjon a kiszolgálóhoz, például a vállalati hálózatokból, meg kell nyitni az **ügyféloldali tűzfalat, hogy a kimenő forgalom elérje az átjárókat**. Az alábbiakban megtalálhatja az átjárók által régiónként használt IP-címek teljes listáját.

## <a name="azure-database-for-mariadb-gateway-ip-addresses"></a>Átjáró IP-címeinek Azure Database for MariaDB

Az átjáró szolgáltatás az állapot nélküli számítási csomópontok olyan csoportjában fut, amely egy IP-cím mögött található, amelyet az ügyfél a Azure Database for MariaDB kiszolgálóhoz való kapcsolódáskor először elérhet. 

A szolgáltatás folyamatos karbantartásának részeként rendszeresen frissítjük az átjárókat üzemeltető számítási hardvereket, hogy biztosítsák a legbiztonságosabb és a teljesítményre vonatkozó élményt. Az átjáró hardverének frissítésekor a rendszer először a számítási csomópontok új gyűrűjét építi ki. Ez az új gyűrű az újonnan létrehozott Azure Database for MariaDB-kiszolgálók forgalmát szolgálja ki, és más IP-címmel fog rendelkezni a régebbi átjáró-gyűrűktől az adott régióban, hogy megkülönböztesse a forgalmat. Ha az új gyűrű teljesen működőképes, a rendszer a meglévő kiszolgálókat kiszolgáló régebbi hardverek leszerelését tervezi. Az átjáró hardverének leszerelése előtt a kiszolgálókat futtató és a régebbi átjárós gyűrűkhöz csatlakozó ügyfelek értesítést kapnak e-mailben és a Azure Portal, három hónappal korábban, a leszerelés előtt. Az átjárók leszerelése hatással lehet a kiszolgálókhoz való kapcsolatra, ha 

* Az átjáró IP-címeinek kódolása az alkalmazáshoz tartozó kapcsolatok karakterláncában végezhető el. **Nem ajánlott**. A kiszolgáló teljes tartománynevét (FQDN) a (z). mariadb.database.azure.com formátumban kell használni az <servername> alkalmazáshoz tartozó kapcsolatok karakterláncában. 
* Az új átjáró IP-címeit nem frissíti az ügyféloldali tűzfalon, hogy a kimenő forgalom elérhetővé váljon az új átjáró-gyűrűkhöz.

A következő táblázat az összes adatterület Azure Database for MariaDB átjárójának átjáró IP-címeit sorolja fel. Az átjáró IP-címeinek legnaprakészebb információi az egyes régiókban az alábbi táblázatban láthatók. Az alábbi táblázatban az oszlopok az alábbiakat jelölik:

* **Átjáró IP-címei:** Ez az oszlop a hardver legújabb generációjában üzemeltetett átjárók aktuális IP-címeit sorolja fel. Ha új kiszolgálót épít ki, javasoljuk, hogy nyissa meg az ügyféloldali tűzfalat az ebben az oszlopban felsorolt IP-címek kimenő forgalmának engedélyezéséhez.
* **Átjáró IP-címei (leszerelés):** Ez az oszlop felsorolja azon átjárók IP-címeit, amelyeken jelenleg le van szerelve a hardver régebbi generációja. Ha új kiszolgálót épít ki, akkor figyelmen kívül hagyhatja ezeket az IP-címeket. Ha rendelkezik meglévő kiszolgálóval, továbbra is megtarthatja a tűzfal kimenő szabályát ezen IP-címek esetében, mivel még nem szerelik le. Ha eldobja ezeket az IP-címekre vonatkozó tűzfalszabályok, csatlakozási hibákat kaphat. Ehelyett az átjáró IP-címei oszlopban felsorolt új IP-címeket proaktív módon kell hozzáadni a kimenő tűzfalszabályok számára, amint megkapja a leszerelési értesítést. Ez biztosítja, hogy a kiszolgáló a legújabb átjáró hardverre való áttelepítése után ne szakítsa meg a kiszolgálóval létesített kapcsolatot.
* **Átjáró IP-címei (leszerelt):** Ez az oszlop felsorolja az átjáró-gyűrűk azon IP-címeit, amelyek leszereltek, és már nincsenek működésben. Ezeket az IP-címeket biztonságosan távolíthatja el a kimenő tűzfalszabály alól. 


| **Régió neve** | **Átjáró IP-címei** |**Átjáró IP-címei (leszerelés)** | **Átjáró IP-címei (leszerelt)** |
|:----------------|:-------------------------|:-------------------------------------------|:------------------------------------------|
| Ausztrália középső régiója| 20.36.105.0  | | |
| Ausztráliai Central2     | 20.36.113.0  | | |
| Kelet-Ausztrália | 13.75.149.87, 40.79.161.1     |  | |
| Délkelet-Ausztrália |191.239.192.109, 13.73.109.251   |  | |
| Dél-Brazília |191.233.201.8, 191.233.200.16    |  | 104.41.11.5|
| Közép-Kanada |40.85.224.249  | | |
| Kelet-Kanada | 40.86.226.166    | | |
| Az USA középső régiója | 23.99.160.139, 52.182.136.37, 52.182.136.38 | 13.67.215.62 | |
| Kelet-Kína | 139.219.130.35    | | |
| Kelet-Kína 2 | 40.73.82.1  | | |
| Észak-Kína | 139.219.15.17    | | |
| Észak-Kína 2 | 40.73.50.0     | | |
| Kelet-Ázsia | 191.234.2.139, 52.175.33.150, 13.75.33.20, 13.75.33.21     | | |
| USA keleti régiója |40.71.8.203, 40.71.83.113 |40.121.158.30|191.238.6.43 |
| USA 2. keleti régiója | 40.70.144.38, 52.167.105.38  | 52.177.185.181 | |
| Közép-Franciaország | 40.79.137.0, 40.79.129.1  | | |
| Dél-Franciaország | 40.79.177.0     | | |
| Közép-Németország | 51.4.144.100     | | |
| Észak-Németország | 51.116.56.0 | |
| Kelet-Észak-Németország | 51.5.144.179  | | |
| Középnyugat-Németország | 51.116.152.0 | |
| Közép-India | 104.211.96.159     | | |
| Dél-India | 104.211.224.146  | | |
| Nyugat-India | 104.211.160.80    | | |
| Kelet-Japán | 40.79.192.23, 40.79.184.8 | 13.78.61.196 | |
| Nyugat-Japán | 191.238.68.11, 40.74.96.6, 40.74.96.7     | 104.214.148.156 | |
| Dél-Korea középső régiója | 52.231.17.13   | 52.231.32.42 | |
| Dél-Korea déli régiója | 52.231.145.3     | 52.231.200.86 | |
| USA északi középső régiója | 52.162.104.35, 52.162.104.36    | 23.96.178.199 | |
| Észak-Európa | 52.138.224.6, 52.138.224.7  | 40.113.93.91 |191.235.193.75 |
| Dél-Afrika északi régiója  | 102.133.152.0    | | |
| Dél-Afrika nyugati régiója | 102.133.24.0   | | |
| USA déli középső régiója |104.214.16.39, 20.45.120.0  |13.66.62.124  |23.98.162.75 |
| Délkelet-Ázsia | 40.78.233.2, 23.98.80.12     | 104.43.15.0 | |
| Észak-Svájc | 51.107.56.0 ||
| Nyugat-Svájc | 51.107.152.0| ||
| UAE középső régiója | 20.37.72.64  | | |
| Észak-Egyesült Arab | 65.52.248.0    | | |
| Az Egyesült Királyság déli régiója | 51.140.184.11   | | |
| Az Egyesült Királyság nyugati régiója | 51.141.8.11  | | |
| USA nyugati középső régiója | 13.78.145.25     | | |
| Nyugat-Európa |13.69.105.208, 104.40.169.187 | 40.68.37.158 | 191.237.232.75 |
| USA nyugati régiója |13.86.216.212, 13.86.217.212 |104.42.238.205  | 23.99.34.75|
| USA 2. nyugati régiója | 13.66.226.202  | | |
||||

## <a name="connection-redirection"></a>Kapcsolatok átirányítása

Azure Database for MariaDB támogatja a további kapcsolati házirendet, az **átirányítást**, amely segít csökkenteni a hálózati késést az ügyfélalkalmazások és a MariaDB-kiszolgálók között. Ezzel a szolgáltatással, miután a kezdeti TCP-munkamenet létrejött a Azure Database for MariaDB-kiszolgálón, a kiszolgáló visszaadja a MariaDB-kiszolgálót üzemeltető csomópont háttérbeli címeit az ügyfélnek. Ezt követően az összes további csomag közvetlenül a kiszolgálóra áramlik, és megkerüli az átjárót. Mivel a csomagok közvetlenül a kiszolgálóra áramlanak, a késés és az átviteli sebesség jobb teljesítményt biztosít.

Ez a funkció a 10,2-es és a 10,3-es motorral rendelkező Azure Database for MariaDB-kiszolgálókon támogatott.

Az átirányítás támogatása a Microsoft által fejlesztett PHP [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) -bővítményben érhető el, és a [PECL](https://pecl.php.net/package/mysqlnd_azure)-ben érhető el. Az átirányítás az alkalmazásokban való használatáról további információt az [átirányítás konfigurálása](./howto-redirection.md) című cikkben talál.

> [!IMPORTANT]
> Az átirányítás támogatása a PHP [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) bővítményben jelenleg előzetes verzióban érhető el.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="what-you-need-to-know-about-this-planned-maintenance"></a>Mit kell tudnia a tervezett karbantartásról?
Ez egy DNS-módosítás, amely transzparensvé teszi az ügyfelek számára. Míg a DNS-kiszolgálón a teljes tartománynév IP-címe megváltozik, a helyi DNS-gyorsítótár 5 percen belül frissül, és az operációs rendszerek automatikusan elvégzik. A helyi DNS-frissítés után az összes új kapcsolat csatlakozni fog az új IP-címhez, az összes meglévő kapcsolat továbbra is a régi IP-címhez lesz csatlakoztatva megszakítás nélkül, amíg a régi IP-címeket nem szereli le teljesen. A leszerelés megkezdése előtt a régi IP-cím nagyjából három-négy hetet vesz igénybe; ezért nem befolyásolhatja az ügyfélalkalmazások alkalmazását.

### <a name="what-are-we-decommissioning"></a>Mi a leszerelés?
Csak az átjáró csomópontjai lesznek leszerelve. Amikor a felhasználók csatlakoznak a kiszolgálókhoz, a kapcsolat első leállítása az átjáró csomópontja, a kapcsolatnak a kiszolgálóra való továbbítása előtt. A régi átjárós gyűrűk leszerelése (nem a bérlői gyűrűk, ahol a kiszolgáló fut) a további pontosítás érdekében tekintse meg a [kapcsolati architektúrát](#connectivity-architecture) .

### <a name="how-can-you-validate-if-your-connections-are-going-to-old-gateway-nodes-or-new-gateway-nodes"></a>Hogyan ellenőrizheti, hogy a kapcsolatok a régi átjáró-csomópontok vagy az új átjáró-csomópontok?
Pingelje a kiszolgáló teljes tartománynevét, például:  ``ping xxx.mariadb.database.azure.com`` . Ha a visszaadott IP-cím az átjáró IP-címei (leszerelése) alatt felsorolt IP-címek egyike a fenti dokumentumban, az azt jelenti, hogy a kapcsolatok a régi átjárón keresztül mennek keresztül. Tervezőrendszer, ha a visszaadott IP-cím az átjáró IP-címei területen felsorolt IP-címek egyike, akkor a kapcsolatok az új átjárón keresztül fognak haladni.

Azt is megteheti, hogy [PSPing](https://docs.microsoft.com/sysinternals/downloads/psping) vagy TCPPing az adatbázis-kiszolgálót az ügyfélalkalmazás az 3306-as porton keresztül, és gondoskodik arról, hogy a VISSZATÉRÉSi IP-cím ne legyen a leszerelési IP-címek egyike

### <a name="how-do-i-know-when-the-maintenance-is-over-and-will-i-get-another-notification-when-old-ip-addresses-are-decommissioned"></a>Hogyan tudni, hogy mikor történik a karbantartás, és kapok egy másik értesítést a régi IP-címek leszerelése után?
Egy e-mailt fog kapni, amely tájékoztatja Önt, amikor elkezdjük a karbantartási munkát. A karbantartás akár egy hónapig is eltarthat attól függően, hogy hány kiszolgálót kell migrálni az Al-régiókban. Készítse elő az ügyfelet az adatbázis-kiszolgálóhoz való csatlakozásra a teljes tartománynév használatával vagy az új IP-cím használatával a fenti táblázatból. 

### <a name="what-do-i-do-if-my-client-applications-are-still-connecting-to-old-gateway-server-"></a>Mi a teendő, ha az ügyfélalkalmazások továbbra is a régi átjáró kiszolgálóhoz csatlakoznak?
Ez azt jelzi, hogy az alkalmazások a teljes tartománynév helyett statikus IP-cím használatával csatlakoznak a kiszolgálóhoz. Tekintse át a kapcsolatok karakterláncait és a kapcsolatok készletezési beállításait, az AK-beállításokat vagy akár a forráskódot.

### <a name="is-there-any-impact-for-my-application-connections"></a>Hatással van az alkalmazás kapcsolataira?
Ez a karbantartás csak DNS-változás, ezért transzparens az ügyfél számára. Miután a DNS-gyorsítótár frissült az ügyfélben (az operációs rendszer automatikusan elvégzi), az összes új kapcsolat csatlakozni fog az új IP-címhez, és az összes meglévő kapcsolat továbbra is jól működik, amíg a régi IP-cím le nem fejeződik, és ez általában több héttel később. Az újrapróbálkozási logika nem szükséges ebben az esetben, de érdemes megtekinteni az alkalmazást, hogy az újrapróbálkozási logika konfigurálva van. Használja a teljes tartománynevet az adatbázis-kiszolgálóhoz való kapcsolódáshoz, vagy engedélyezze az új "átjáró IP-címeinek" listáját az alkalmazás kapcsolati karakterláncában.
Ez a karbantartási művelet nem fogja eldobni a meglévő kapcsolatokat. Az új kapcsolódási kérelmek csak az új átjáró gyűrűjét teszik lehetővé.

### <a name="can-i-request-for-a-specific-time-window-for-the-maintenance"></a>Igényelhetek egy adott időablakot a karbantartáshoz? 
Mivel a migrációnak transzparensnek kell lennie, és nincs hatással az ügyfél kapcsolatára, a felhasználók többsége számára nem lesz probléma. Tekintse át az alkalmazást proaktív módon, és győződjön meg arról, hogy a teljes tartománynevet használja az adatbázis-kiszolgálóhoz való kapcsolódáshoz, vagy ha engedélyezi az új "átjáró IP-címeinek" listáját az alkalmazás kapcsolati karakterláncában.

### <a name="i-am-using-private-link-will-my-connections-get-affected"></a>Privát hivatkozást használok, a kapcsolatom hatással lesz a kapcsolatokra?
Nem, ez az átjáró hardveres leszerelése, és nincs kapcsolata a magánhálózati vagy magánhálózati IP-címekkel, csak a leszerelési IP-címek alatt említett nyilvános IP-címeket fogja érinteni.


## <a name="next-steps"></a>Következő lépések

* [Azure Database for MariaDB tűzfalszabályok létrehozása és kezelése a Azure Portal használatával](./howto-manage-firewall-portal.md)
* [Azure Database for MariaDB tűzfalszabályok létrehozása és kezelése az Azure CLI-vel](./howto-manage-firewall-cli.md)
