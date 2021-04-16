---
title: Azure SQL Database architektúra
description: Ez a dokumentum ismerteti Azure SQL Database azure-beli vagy azure-beli adatbázis-kapcsolatok kapcsolati architektúráját.
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
ms.openlocfilehash: 3442e3003ef8a299beb88cd212602c8713915474
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107499954"
---
# <a name="azure-sql-database-and-azure-synapse-analytics-connectivity-architecture"></a>Az Azure SQL Database és az Azure Synapse Analytics kapcsolati architektúrája
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Ez a cikk ismerteti a különböző összetevők architektúráját, amelyek a hálózati forgalmat egy kiszolgálóra irányítják Azure SQL Database vagy Azure Synapse Analytics. Emellett ismerteti a különböző csatlakozási szabályzatokat, valamint azt, hogy ez milyen hatással van az Azure-ból csatlakozó ügyfelekre és az Azure-n kívülről csatlakozó ügyfelekre.

> [!IMPORTANT]
> Ez a cikk nem *vonatkozik* a **Azure SQL Managed Instance.** Tekintse meg [a felügyelt példány kapcsolati architektúráját.](../managed-instance/connectivity-architecture-overview.md)

## <a name="connectivity-architecture"></a>Kapcsolati architektúra

Az alábbi ábra magas szintű áttekintést nyújt a kapcsolati architektúráról.

![A kapcsolati architektúra magas szintű áttekintését bemutató ábra.](./media/connectivity-architecture/connectivity-overview.png)

A következő lépések azt írják le, hogyan lehet kapcsolatot létesíteni a Azure SQL Database:

- Az ügyfelek az átjáróhoz csatlakoznak, amely nyilvános IP-címmel rendelkezik, és az 1433-as porton figyel.
- Az átjáró a hatályos csatlakozási szabályzattól függően átirányítja vagy proxykat hoz létre a megfelelő adatbázisfürtre.
- Az adatbázisfürtön belül a rendszer a megfelelő adatbázishoz továbbítja a forgalmat.

## <a name="connection-policy"></a>Kapcsolati szabályzat

A SQL Database és Azure Synapse kiszolgálók a következő három beállítást támogatják a kiszolgáló kapcsolati házirend-beállításához:

- **Átirányítás (ajánlott):** Az ügyfelek közvetlenül az adatbázist üzemeltető csomóponttal létesítik a kapcsolatokat, ami kisebb késést és nagyobb átviteli sebességet eredményezhet. Ahhoz, hogy a kapcsolatok ezt a módot használják, az ügyfeleknek a következőkkel kell:
  - Engedélyezze a kimenő kommunikációt az ügyféltől az összes Azure SQL IP-cím felé a régióban az 11000 11999 tartományban található portokon. Az SQL szolgáltatáscímkéivel ezt könnyebben kezelheti.  
  - Engedélyezze az ügyfél kimenő kommunikációját az Azure SQL Database IP-címeinek az 1433-as porton keresztüli kimenő kommunikációjának.

- **Proxy:** Ebben a módban minden kapcsolat a hálózati átjárók Azure SQL Database, ami nagyobb késést és alacsonyabb átviteli sebességet eredményezhet. Ahhoz, hogy a kapcsolatok ezt a módot használják, az ügyfeleknek engedélyezniük kell az ügyfél és az Azure SQL Database közötti kimenő kommunikációt az 1433-as porton.

- **Alapértelmezett érték:** Ez a kapcsolati szabályzat a létrehozás után minden kiszolgálón érvényben van, kivéve, ha explicit módon vagy -re módosítja a kapcsolati `Proxy` `Redirect` szabályzatot. Az alapértelmezett szabályzat az Azure-ból származó összes ügyfélkapcsolatra (például egy Azure-beli virtuális gépről) és a kívülről (például a helyi munkaállomásról származó kapcsolatokra) vonatkozó összes ügyfélkapcsolatra `Redirect` `Proxy` vonatkozik.

A legalacsonyabb késés és a legmagasabb átviteli sebesség érdekében kifejezetten a csatlakozási szabályzatot javasoljuk a csatlakozási `Redirect` `Proxy` szabályzattal kapcsolatban. Azonban meg kell felelnie a fent ismertetett, a hálózati forgalom engedélyezésére vonatkozó további követelményeknek. Ha az ügyfél egy Azure-beli virtuális gép, ezt a hálózati biztonsági csoportokkal (NSG) és [szolgáltatáscímkék használatával valósíthatja meg.](../../virtual-network/network-security-groups-overview.md#service-tags) Ha az ügyfél egy helyszíni munkaállomásról csatlakozik, akkor előfordulhat, hogy a hálózati rendszergazdával kell dolgoznia, hogy engedélyezze a hálózati forgalmat a vállalati tűzfalon keresztül.

## <a name="connectivity-from-within-azure"></a>Csatlakozás az Azure-ból

Ha az Azure-ból csatlakozik, a kapcsolatok alapértelmezés szerint a kapcsolati `Redirect` szabályzattal rendelkezik. A házirend azt jelenti, hogy miután a TCP-munkamenet létrejött az Azure SQL Database-hoz, a rendszer átirányítja az ügyfél-munkamenetet a megfelelő adatbázisfürtre, és módosítja a cél virtuális IP-címet a Azure SQL Database-átjáró és a fürt ip-címe `Redirect` között. Ezt követően minden további csomag közvetlenül a fürtbe áramlik, megkerülve a Azure SQL Database átjárót. Az alábbi ábrán ez a forgalom látható.

![architektúra áttekintése](./media/connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Csatlakozás az Azure-on kívülről

Ha az Azure-ból csatlakozik, a kapcsolatok alapértelmezés szerint a kapcsolati `Proxy` szabályzattal rendelkezik. A házirend azt jelenti, hogy a TCP-munkamenet a Azure SQL Database átjárón keresztül jött létre, és az összes további csomag az `Proxy` átjárón keresztül áramlik. Az alábbi ábrán ez a forgalom látható.

![Diagram, amely bemutatja, hogyan jött létre a TCP-munkamenet Azure SQL Database átjárón keresztül, és az összes további csomag az átjárón keresztül áramlik.](./media/connectivity-architecture/connectivity-onprem.png)

> [!IMPORTANT]
> Ezenkívül nyissa meg az 1434-es és az 14000-14999-es TCP-portot a [DAC-kapcsolat engedélyezéséhez](/sql/database-engine/configure-windows/diagnostic-connection-for-database-administrators#connecting-with-dac)

## <a name="gateway-ip-addresses"></a>Átjáró IP-címei

Az alábbi táblázat az átjárók IP-címeit sorolja fel régiónként. A SQL Database vagy Azure Synapse való csatlakozáshoz engedélyeznie kell a régió  összes átjárójára és átjáróiról származó hálózati forgalmat.

Az egyes régiókban található új átjárókra irányuló forgalom migrálásának részletei a következő cikkben olvashatók: Azure SQL Database forgalom áttelepítése újabb [átjárókra](gateway-migration.md)

| Régió neve          | Átjáró IP-címei |
| --- | --- |
| Ausztrália középső régiója    | 20.36.105.0, 20.36.104.6, 20.36.104.7 |
| Ausztrália 2. középső régiója   | 20.36.113.0, 20.36.112.6 |
| Kelet-Ausztrália       | 13.75.149.87, 40.79.161.1, 13.70.112.9 |
| Délkelet-Ausztrália | 191.239.192.109, 13.73.109.251, 13.77.48.10, 13.77.49.32 |
| Dél-Brazília         | 191.233.200.14, 191.234.144.16, 191.234.152.3 |
| Közép-Kanada       | 40.85.224.249, 52.246.152.0, 20.38.144.1 |
| Kelet-Kanada          | 40.86.226.166, 52.242.30.154, 40.69.105.9 , 40.69.105.10 |
| Az USA középső régiója           | 13.67.215.62, 52.182.137.15, 104.208.16.96, 104.208.21.1, 13.89.169.20 |
| Kelet-Kína           | 139.219.130.35     |
| Kína 2. keleti régiója         | 40.73.82.1         |
| Észak-Kína          | 139.219.15.17      |
| Kína 2. északi régiója        | 40.73.50.0         |
| Kelet-Ázsia            | 52.175.33.150, 13.75.32.4, 13.75.32.14 |
| USA keleti régiója              | 40.121.158.30, 40.79.153.12, 40.78.225.32 |
| USA 2. keleti régiója            | 40.79.84.180, 52.177.185.181, 52.167.104.0, 191.239.224.107, 104.208.150.3, 40.70.144.193 |
| Közép-Franciaország       | 40.79.137.0, 40.79.129.1, 40.79.137.8, 40.79.145.12 |
| Dél-Franciaország         | 40.79.177.0, 40.79.177.10 ,40.79.177.12 |
| Közép-Németország      | 51.4.144.100       |
| Észak-Németország – Keleti régió   | 51.5.144.179       |
| Nyugat-Németország – középső régió | 51.116.240.0, 51.116.248.0, 51.116.152.0 |
| Közép-India        | 104.211.96.159, 104.211.86.30 , 104.211.86.31 |
| Dél-India          | 104.211.224.146    |
| Nyugat-India           | 104.211.160.80, 104.211.144.4 |
| Kelet-Japán           | 13.78.61.196, 40.79.184.8, 13.78.106.224, 40.79.192.5 |
| Nyugat-Japán           | 104.214.148.156, 40.74.100.192, 40.74.97.10 |
| Dél-Korea középső régiója        | 52.231.32.42, 52.231.17.22 ,52.231.17.23 |
| Dél-Korea déli régiója          | 52.231.200.86, 52.231.151.96 |
| USA északi középső régiója     | 23.96.178.199, 23.98.55.75, 52.162.104.33, 52.162.105.9 |
| Észak-Európa         | 40.113.93.91, 52.138.224.1, 13.74.104.113 |
| Kelet-Kelet          | 51.120.96.0, 51.120.96.33 |
| Nyugat-Nyugat-Németország          | 51.120.216.0       |
| Dél-Afrika északi régiója   | 102.133.152.0, 102.133.120.2, 102.133.152.32 |
| Dél-Afrika nyugati régiója    | 102.133.24.0       |
| USA déli középső régiója     | 13.66.62.124, 104.214.16.32, 20.45.121.1, 20.49.88.1   |
| Délkelet-Ázsia      | 104.43.15.0, 40.78.232.3, 13.67.16.193 |
| Észak-Svájc    | 51.107.56.0, 51.107.57.0 |
| Nyugat-Svájc     | 51.107.152.0, 51.107.153.0 |
| Egyesült Arab Emírségek középső          | 20.37.72.64        |
| Egyesült Arab Emírségek északi iránya            | 65.52.248.0        |
| Az Egyesült Királyság déli régiója             | 51.140.184.11, 51.105.64.0, 51.140.144.36, 51.105.72.32 |
| Az Egyesült Királyság nyugati régiója              | 51.141.8.11        |
| USA nyugati középső régiója      | 13.78.145.25, 13.78.248.43, 13.71.193.32, 13.71.193.33 |
| Nyugat-Európa          | 40.68.37.158, 104.40.168.105, 52.236.184.163  |
| USA nyugati régiója              | 104.42.238.205, 13.86.216.196   |
| USA 2. nyugati régiója            | 13.66.226.202, 40.78.240.8, 40.78.248.10  |
| USA 2. nyugati régiója            | 13.66.226.202, 40.78.240.8, 40.78.248.10  |
|                      |                    |


## <a name="next-steps"></a>Következő lépések

- A kiszolgáló csatlakozási szabályzatának Azure SQL Database a [conn-policy (kapcsolati](/cli/azure/sql/server/conn-policy)szabályzat) oldalon található további információ.
- A 4.5 Azure SQL Database ADO.NET vagy újabb verziót Azure SQL Database ügyfelek csatlakozási viselkedéséről a 4.5-ös vagy újabb verziót ADO.NET [1433-as](adonet-v12-develop-direct-route-ports.md)portokat.
- Az alkalmazásfejlesztés áttekintésével kapcsolatos általános információkért tekintse meg az [SQL Database áttekintését.](develop-overview.md)
