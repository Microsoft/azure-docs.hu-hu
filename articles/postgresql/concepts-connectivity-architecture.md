---
title: Kapcsolati architektúra – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: A Azure Database for PostgreSQL-egyetlen kiszolgáló kapcsolati architektúráját ismerteti.
author: Bashar-MSFT
ms.author: bahusse
ms.service: postgresql
ms.topic: conceptual
ms.date: 2/11/2021
ms.openlocfilehash: 0c8f55b6eeba4319b0ce9e39085912b8c4829235
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101720800"
---
# <a name="connectivity-architecture-in-azure-database-for-postgresql"></a>Kapcsolati architektúra a Azure Database for PostgreSQLban
Ez a cikk ismerteti a Azure Database for PostgreSQL kapcsolati architektúrát, valamint azt, hogy a forgalom hogyan legyen átirányítva a Azure Database for PostgreSQL adatbázis-példányára az Azure-on belüli és kívüli ügyfelektől.

## <a name="connectivity-architecture"></a>Kapcsolati architektúra
A Azure Database for PostgreSQL kapcsolata egy olyan átjárón keresztül jön létre, amely felelős a bejövő kapcsolatok útválasztásához a fürtben lévő kiszolgáló fizikai helyén. A következő ábra a forgalmat mutatja be.

:::image type="content" source="./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png" alt-text="A kapcsolati architektúra áttekintése":::


Ahogy az ügyfél csatlakozik az adatbázishoz, a kiszolgáló kapcsolati karakterlánca feloldódik az átjáró IP-címével. Az átjáró az IP-címet figyeli az 5432-as porton. Az adatbázis-fürtön belül a rendszer a megfelelő Azure Database for PostgreSQL továbbítja a forgalmat. Ezért ahhoz, hogy csatlakozni tudjon a kiszolgálóhoz, például a vállalati hálózatokból, meg kell nyitni az **ügyféloldali tűzfalat, hogy a kimenő forgalom elérje az átjárókat**. Az alábbiakban megtalálhatja az átjárók által régiónként használt IP-címek teljes listáját.

## <a name="azure-database-for-postgresql-gateway-ip-addresses"></a>Átjáró IP-címeinek Azure Database for PostgreSQL

Az átjáró szolgáltatás az állapot nélküli számítási csomópontok olyan csoportjában fut, amely egy IP-cím mögött található, amelyet az ügyfél a Azure Database for PostgreSQL kiszolgálóhoz való kapcsolódáskor először elérhet. 

A szolgáltatás folyamatos karbantartásának részeként rendszeresen frissítjük az átjárókat üzemeltető számítási hardvereket, hogy biztosítsák a legbiztonságosabb és a teljesítményhez való kapcsolódási élményt. Az átjáró hardverének frissítésekor a rendszer először a számítási csomópontok új gyűrűjét építi ki. Ez az új gyűrű az újonnan létrehozott Azure Database for PostgreSQL-kiszolgálók forgalmát szolgálja ki, és más IP-címmel fog rendelkezni a régebbi átjáró-gyűrűktől az adott régióban, hogy megkülönböztesse a forgalmat. A régebbi átjáró hardvere továbbra is meglévő kiszolgálókat szolgál ki, de a jövőben a leszereléshez tervezték. Az átjáró hardverének leszerelése előtt a kiszolgálókat futtató és a régebbi átjárós gyűrűkhöz csatlakozó ügyfelek értesítést kapnak e-mailben és a Azure Portal, három hónappal korábban, a leszerelés előtt. Az átjárók leszerelése hatással lehet a kiszolgálókhoz való kapcsolatra, ha 

* Az átjáró IP-címeinek kódolása az alkalmazáshoz tartozó kapcsolatok karakterláncában végezhető el. **Nem ajánlott**. A kiszolgáló teljes tartománynevét (FQDN) a (z). postgres.database.azure.com formátumban kell használni az <servername> alkalmazáshoz tartozó kapcsolatok karakterláncában. 
* Az új átjáró IP-címeit nem frissíti az ügyféloldali tűzfalon, hogy a kimenő forgalom elérhetővé váljon az új átjáró-gyűrűkhöz.

A következő táblázat az összes adatterület Azure Database for PostgreSQL átjárójának átjáró IP-címeit sorolja fel. Az átjáró IP-címeinek legnaprakészebb információi az egyes régiókban az alábbi táblázatban láthatók. Az alábbi táblázatban az oszlopok az alábbiakat jelölik:

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
| Kelet-Észak-Németország | 51.5.144.179  | | |
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
| UAE középső régiója | 20.37.72.64  | | |
| Észak-Egyesült Arab | 65.52.248.0    | | |
| Az Egyesült Királyság déli régiója | 51.140.184.11   | | |
| Az Egyesült Királyság nyugati régiója | 51.141.8.11  | | |
| USA nyugati középső régiója | 13.78.145.25     | | |
| Nyugat-Európa |13.69.105.208, 104.40.169.187 | 40.68.37.158 | 191.237.232.75 |
| USA nyugati régiója |13.86.216.212, 13.86.217.212 |104.42.238.205  | 23.99.34.75|
| USA 2. nyugati régiója | 13.66.226.202  | | |
||||

## <a name="next-steps"></a>Következő lépések

* [Azure Database for PostgreSQL tűzfalszabályok létrehozása és kezelése a Azure Portal használatával](./howto-manage-firewall-using-portal.md)
* [Azure Database for PostgreSQL tűzfalszabályok létrehozása és kezelése az Azure CLI-vel](./howto-manage-firewall-using-cli.md)