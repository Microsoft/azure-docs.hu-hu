---
title: Azure SQL Database kapcsolati architektúra
description: Ez a dokumentum ismerteti az Azure-ban vagy az Azure-on kívüli adatbázis-kapcsolatok Azure SQL Database kapcsolati architektúráját.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: fasttrack-edit, sqldbrb=1
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: sstein, vanto
ms.date: 01/25/2021
ms.openlocfilehash: c589f270fe623f1e4e4bbde4fd353ba95249d1b5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102426506"
---
# <a name="azure-sql-database-and-azure-synapse-analytics-connectivity-architecture"></a>Az Azure SQL Database és az Azure Synapse Analytics kapcsolati architektúrája
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Ez a cikk ismerteti a különböző összetevők architektúráját, amelyek közvetlen hálózati forgalmat Azure SQL Database vagy Azure szinapszis Analytics-kiszolgálóra irányítanak. Ismerteti továbbá a különböző kapcsolati szabályzatokat, valamint azt, hogy a hogyan befolyásolja az Azure-ból és az Azure-on kívülről csatlakozó ügyfelektől érkező ügyfeleket.

> [!IMPORTANT]
> Ez a cikk *nem* vonatkozik az **Azure SQL felügyelt példányaira**. Tekintse át a [felügyelt példány kapcsolati architektúráját](../managed-instance/connectivity-architecture-overview.md).

## <a name="connectivity-architecture"></a>Kapcsolati architektúra

A következő ábra a kapcsolati architektúra magas szintű áttekintését tartalmazza.

![A kapcsolati architektúra magas szintű áttekintését bemutató diagram.](./media/connectivity-architecture/connectivity-overview.png)

A következő lépések azt írják le, hogyan történik a kapcsolatok létrehozása a Azure SQL Database:

- Az ügyfelek csatlakoznak az átjáróhoz, amely nyilvános IP-címmel rendelkezik, és az 1433-es porton figyel.
- Az átjáró a hatályos kapcsolódási házirendtől függően átirányítja vagy proxyként továbbítja a forgalmat a megfelelő adatbázis-fürtre.
- Az adatbázis-fürt forgalmát a megfelelő adatbázisba továbbítja a rendszer.

## <a name="connection-policy"></a>Kapcsolatok házirendje

A SQL Database és az Azure szinapszis-kiszolgálók a következő három lehetőséget támogatják a kiszolgáló csatlakoztatási házirendjének beállításához:

- **Átirányítás (ajánlott):** Az ügyfelek közvetlenül az adatbázist üzemeltető csomóponthoz csatlakoznak, ami csökkenti a késést és a jobb teljesítményt. A mód használatára szolgáló kapcsolatok esetén az ügyfeleknek a következőket kell tenniük:
  - Engedélyezi a kimenő kommunikációt az ügyféltől a régió összes Azure SQL IP-címére a 11000 11999 tartományon belüli portoknál. A szolgáltatáshoz tartozó címkék használatával könnyebben kezelheti ezt az SQL-szolgáltatást.  
  - A kimenő kommunikáció engedélyezése az ügyféltől az 1433-es porton keresztüli átjáró IP-címeinek Azure SQL Database.

- **Proxy:** Ebben a módban az összes kapcsolat a Azure SQL Database-átjárón keresztül történik, ami növeli a késést és a kisebb átviteli sebességet. A módot használó kapcsolatok esetében az ügyfeleknek engedélyeznie kell a kimenő kommunikációt az ügyféltől az 1433-es porton Azure SQL Database átjáró IP-címeinek eléréséhez.

- **Alapértelmezett:** Ez a kapcsolódási házirend a létrehozás után minden kiszolgálón érvényben van, kivéve, ha explicit módon módosítja a kapcsolódási házirendet a vagy a rendszerre `Proxy` `Redirect` . Az alapértelmezett házirend az `Redirect` Azure-ból (például egy Azure-beli virtuális gépről) és `Proxy` a kívülről származó összes ügyfélkapcsolatra (például a helyi munkaállomásról létesített kapcsolatokra) vonatkozó összes ügyfélkapcsolat.

A kapcsolati szabályzatot a `Redirect` `Proxy` legalacsonyabb késés és a legmagasabb átviteli sebesség érdekében javasoljuk. Azonban meg kell felelnie a fentiekben ismertetett hálózati forgalom engedélyezésének további követelményeinek. Ha az ügyfél egy Azure-beli virtuális gép, ezt a hálózati biztonsági csoportok (NSG) és a [szolgáltatás-címkék](../../virtual-network/network-security-groups-overview.md#service-tags)használatával végezheti el. Ha az ügyfél helyszíni munkaállomásról csatlakozik, akkor előfordulhat, hogy a hálózati rendszergazdával kell dolgoznia a vállalati tűzfalon keresztüli hálózati forgalom engedélyezéséhez.

## <a name="connectivity-from-within-azure"></a>Csatlakozás az Azure-ból

Ha az Azure-on belülről csatlakozik, a kapcsolatokhoz alapértelmezés szerint csatlakozási házirend tartozik `Redirect` . A szabályzat `Redirect` azt jelenti, hogy a TCP-munkamenetnek a Azure SQL Databasehoz való létrehozása után az ügyfél-munkamenetet a rendszer átirányítja a megfelelő adatbázis-fürtre, és a célként megadott virtuális IP-címet a fürt adott Azure SQL Database átjárójának a helyére módosítja. Ezt követően az összes további csomag közvetlenül a fürtre áramlik, és megkerüli a Azure SQL Database-átjárót. A következő ábra szemlélteti ezt a forgalmat.

![architektúra – áttekintés](./media/connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Csatlakozás az Azure-on kívülről

Ha az Azure-on kívülről csatlakozik, a kapcsolatok alapértelmezés szerint csatlakozási házirendtel rendelkeznek `Proxy` . A szabályzat `Proxy` azt jelenti, hogy a TCP-munkamenet a Azure SQL Database átjárón és az összes további, az átjárón keresztüli adatcsomagon keresztül jön. A következő ábra szemlélteti ezt a forgalmat.

![Diagram, amely bemutatja, hogyan történik a TCP-munkamenet létrehozása a Azure SQL Database átjárón és az összes további csomagon az átjárón keresztül.](./media/connectivity-architecture/connectivity-onprem.png)

> [!IMPORTANT]
> Emellett a 1434-es és a 14000-14999-as TCP-porton is megnyitható a [DAC](/sql/database-engine/configure-windows/diagnostic-connection-for-database-administrators#connecting-with-dac)

## <a name="gateway-ip-addresses"></a>Átjáró IP-címei

Az alábbi táblázat az átjárók régió szerinti IP-címeit sorolja fel. SQL Database vagy Azure Szinapszishoz való kapcsolódáshoz engedélyeznie kell a hálózati forgalmat a régió **összes** átjáróján.

A forgalom áttelepítésének részletei az egyes régiókban lévő új átjárók számára a következő cikkben olvashatók: [Azure SQL Database forgalom áttelepítése újabb átjáróra](gateway-migration.md)

| Régió neve          | Átjáró IP-címei |
| --- | --- |
| Ausztrália középső régiója    | 20.36.105.0, 20.36.104.6, 20.36.104.7 |
| Ausztrália 2. középső régiója   | 20.36.113.0, 20.36.112.6 |
| Kelet-Ausztrália       | 13.75.149.87, 40.79.161.1, 13.70.112.9 |
| Délkelet-Ausztrália | 191.239.192.109, 13.73.109.251, 13.77.48.10, 13.77.49.32 |
| Dél-Brazília         | 191.233.200.14, 191.234.144.16, 191.234.152.3 |
| Közép-Kanada       | 40.85.224.249, 52.246.152.0, 20.38.144.1 |
| Kelet-Kanada          | 40.86.226.166, 52.242.30.154, 40.69.105.9 , 40.69.105.10 |
| Az USA középső régiója           | 13.67.215.62, 52.182.137.15, 23.99.160.139, 104.208.16.96, 104.208.21.1, 13.89.169.20 |
| Kelet-Kína           | 139.219.130.35     |
| Kelet-Kína 2         | 40.73.82.1         |
| Észak-Kína          | 139.219.15.17      |
| Észak-Kína 2        | 40.73.50.0         |
| Kelet-Ázsia            | 52.175.33.150, 13.75.32.4, 13.75.32.14 |
| USA keleti régiója              | 40.121.158.30, 40.79.153.12, 40.78.225.32 |
| USA 2. keleti régiója            | 40.79.84.180, 52.177.185.181, 52.167.104.0, 191.239.224.107, 104.208.150.3 |
| Közép-Franciaország       | 40.79.137.0, 40.79.129.1, 40.79.137.8, 40.79.145.12 |
| Dél-Franciaország         | 40.79.177.0, 40.79.177.10 ,40.79.177.12 |
| Közép-Németország      | 51.4.144.100       |
| Kelet-Észak-Németország   | 51.5.144.179       |
| Középnyugat-Németország | 51.116.240.0, 51.116.248.0, 51.116.152.0 |
| Közép-India        | 104.211.96.159, 104.211.86.30 , 104.211.86.31 |
| Dél-India          | 104.211.224.146    |
| Nyugat-India           | 104.211.160.80, 104.211.144.4 |
| Kelet-Japán           | 13.78.61.196, 40.79.184.8, 13.78.106.224, 40.79.192.5 |
| Nyugat-Japán           | 104.214.148.156, 40.74.100.192, 40.74.97.10 |
| Dél-Korea középső régiója        | 52.231.32.42, 52.231.17.22 ,52.231.17.23 |
| Dél-Korea déli régiója          | 52.231.200.86, 52.231.151.96 |
| USA északi középső régiója     | 23.96.178.199, 23.98.55.75, 52.162.104.33, 52.162.105.9 |
| Észak-Európa         | 40.113.93.91, 52.138.224.1, 13.74.104.113 |
| Kelet-Norvégia          | 51.120.96.0, 51.120.96.33 |
| Norvégia nyugati régiója          | 51.120.216.0       |
| Dél-Afrika északi régiója   | 102.133.152.0, 102.133.120.2, 102.133.152.32 |
| Dél-Afrika nyugati régiója    | 102.133.24.0       |
| USA déli középső régiója     | 13.66.62.124, 104.214.16.32, 20.45.121.1, 20.49.88.1   |
| Délkelet-Ázsia      | 104.43.15.0, 40.78.232.3, 13.67.16.193 |
| Észak-Svájc    | 51.107.56.0, 51.107.57.0 |
| Nyugat-Svájc     | 51.107.152.0, 51.107.153.0 |
| UAE középső régiója          | 20.37.72.64        |
| Észak-Egyesült Arab            | 65.52.248.0        |
| Az Egyesült Királyság déli régiója             | 51.140.184.11, 51.105.64.0 |
| Az Egyesült Királyság nyugati régiója              | 51.141.8.11        |
| USA nyugati középső régiója      | 13.78.145.25, 13.78.248.43        |
| Nyugat-Európa          | 40.68.37.158, 104.40.168.105, 52.236.184.163  |
| USA nyugati régiója              | 104.42.238.205, 13.86.216.196   |
| USA 2. nyugati régiója            | 13.66.226.202, 40.78.240.8, 40.78.248.10  |
| USA 2. nyugati régiója            | 13.66.226.202, 40.78.240.8, 40.78.248.10  |
|                      |                    |


## <a name="next-steps"></a>Következő lépések

- A kiszolgálók Azure SQL Database-kapcsolódási házirendjének módosításáról további információt a [Conn-Policy](/cli/azure/sql/server/conn-policy)című témakörben talál.
- A ADO.NET 4,5-as vagy újabb verziót használó ügyfelek Azure SQL Database-kapcsolatainak működéséről további információért lásd: [a 1433-nál nagyobb portok a ADO.NET 4,5](adonet-v12-develop-direct-route-ports.md)-ban.
- Az alkalmazások fejlesztésének általános áttekintését lásd: [SQL Database alkalmazásfejlesztés áttekintése](develop-overview.md).
