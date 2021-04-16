---
title: Átjáróforgalom migrálási értesítése
description: A cikk tájékoztatást nyújt a felhasználóknak a Azure SQL Database IP-címeinek áttelepítésével kapcsolatban
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=1
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 07/01/2019
ms.openlocfilehash: 07611a3620a2fd8efe0da075b03b55a5be3a5be9
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505377"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Azure SQL Database forgalom áttelepítése újabb átjárókra
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Az Azure-infrastruktúra fejlesztése során a Microsoft rendszeres időközönként frissíti a hardvert, hogy a lehető legjobb felhasználói élményt nyújtsuk. A következő hónapokban tervezzük az újabb hardvergenerációkra épített átjárók hozzáadását, a forgalom áttelepítését, és végül egyes régiókban régebbi hardverre épített átjárók leszerelését.  

Az ügyfeleket a szolgáltatás állapotértesítései már a régiónként elérhető átjárók bármilyen módosítása előtt értesítik. Az ügyfelek [a Azure Portal állíthatnak be tevékenységnapló-riasztásokat.](../../service-health/alerts-activity-log-service-notifications-portal.md)

A legfrissebb információk az átjáró IP-címeinek táblázatában [Azure SQL Database lesznek megőrizve.](connectivity-architecture.md#gateway-ip-addresses)

## <a name="status-updates"></a>Állapotfrissítések

# <a name="in-progress"></a>[Folyamatban](#tab/in-progress-ip)
## <a name="may-2021"></a>2021. május
Az új SQL-átjárók a következő régiókhoz vannak hozzáadva:
- Egyesült Királyság déli régiója: 51.140.144.36, 51.105.72.32  
- USA nyugati középső régiója: 13.71.193.32, 13.71.193.33 

Az SQL Gateway 2021. május 17-én elkezdi az ügyfélforgalom elfogadását.

## <a name="april-2021"></a>2021. április
Az új SQL-átjárók a következő régiókhoz vannak hozzáadva:
- USA 2. keleti régiója: 40.70.144.193

Az SQL Gateway 2021. április 30-án elkezdi az ügyfélforgalom elfogadását.

Az új SQL-átjárók a következő régiókhoz vannak hozzáadva:
- Kelet-Németország: 51.120.96.33
- Dél-Kelet-Ázsia: 13.67.16.193
- Dél-Afrika északi iránya: 102.133.152.32
- Dél-Korea déli iránya: 52.231.151.96
- Észak-középső: USA 52.162.105.9
- Délkelet-Ausztrália: 13.77.49.32 

Ezek az SQL-átjárók 2021. április 5-én elkezdik az ügyfélforgalom elfogadását.

## <a name="march-2021"></a>2021. március
A következő, több régióban található SQL-átjárók inaktiválása folyamatban van:
- Dél-Brazília: 104.41.11.5
- Kelet-Ázsia: 191.234.2.139
- USA keleti régiója: 191.238.6.43
- Kelet-Japán: 191.237.240.43
- Nyugat-Japán: 191.238.68.11
- Észak-Európa: 191.235.193.75
- USA déli középső pontja: 23.98.162.75
- Délkelet-Ázsia: 23.100.117.95
- Nyugat-Európa: 191.237.232.75
- USA nyugati régiója: 23.99.34.75

Nem várható ügyfélhatás, mivel ezek az átjárók (régebbi hardveren futnak) nem útválasztást irányító ügyfélforgalmat. Az átjárók IP-címeit 2021. március 15-én inaktiválni kell.

# <a name="completed"></a>[Befejeződött](#tab/completed-ip)
A következő átjáróáttelepítések befejeződnek: 

## <a name="february-2021"></a>2021. február
Új SQL-átjárók vannak hozzáadva a következő régiókhoz:

- USA középső országa: 13.89.169.20

Ezek az SQL-átjárók 2021. február 28-án elkezdik az ügyfélforgalom elfogadását.

## <a name="january-2021"></a>2021. január
Új SQL-átjárók vannak hozzáadva a következő régiókhoz:

- Ausztrália középső középső országa: 20.36.104.6, 20.36.104.7 
- Ausztrália 2. középső országa: 20.36.112.6 
- Dél-Brazília: 191.234.144.16 ,191.234.152.3 
- Kelet-Kanada: 40.69.105.9 ,40.69.105.10
- Közép-India: 104.211.86.30, 104.211.86.31 
- Kelet-Ázsia: 13.75.32.14 
- Közép-Franciaország: 40.79.137.8, 40.79.145.12 
- Dél-Franciaország: 40.79.177.10 ,40.79.177.12
- Dél-Korea középső középső országa: 52.231.17.22 ,52.231.17.23
- Nyugat-India: 104.211.144.4

Ezek az SQL-átjárók 2021. január 31-én elkezdik az ügyfélforgalom elfogadását.



### <a name="october-2020"></a>2020. október

Új SQL-átjárók vannak hozzáadva a következő régiókhoz:

- Nyugat-Németország középső régiója: 51.116.240.0, 51.116.248.0

Ezek az SQL-átjárók 2020. október 12-én elkezdik az ügyfélforgalom elfogadását. 

### <a name="september-2020"></a>2020. szeptember
Az új SQL-átjárók a következő régiókhoz vannak hozzáadva. Ezeknek az SQL-átjáróknak **2020. szeptember 15-én** meg kell kezdenie az ügyfélforgalom elfogadását:

- Délkelet-Ausztrália: 13.77.48.10
- Kelet-Kanada: 40.86.226.166, 52.242.30.154
- Egyesült Királyság déli régiója: 51.140.184.11, 51.105.64.0

A meglévő SQL-átjárók a következő régiókban kezdik meg a forgalom elfogadását. Ezek az SQL-átjárók **2020. szeptember 15-én** elkezdik az ügyfélforgalom elfogadását:

- Délkelet-Ausztrália: 191.239.192.109 és 13.73.109.251
- USA középső középső országa: 13.67.215.62, 52.182.137.15, 23.99.160.139, 104.208.16.96 és 104.208.21.1
- Kelet-Ázsia: 191.234.2.139, 52.175.33.150 és 13.75.32.4
- USA keleti régiója: 40.121.158.30, 40.79.153.12, 191.238.6.43 és 40.78.225.32
- USA 2. keleti régiója: 40.79.84.180, 52.177.185.181, 52.167.104.0, 191.239.224.107 és 104.208.150.3
- Közép-Franciaország: 40.79.137.0 és 40.79.129.1
- Nyugat-Japán: 104.214.148.156, 40.74.100.192, 191.238.68.11 és 40.74.97.10
- USA északi középső pontja: 23.96.178.199, 23.98.55.75 és 52.162.104.33
- Délkelet-Ázsia: 104.43.15.0, 23.100.117.95 és 40.78.232.3
- USA nyugati régiója: 104.42.238.205, 23.99.34.75 és 13.86.216.196

A következő régiókban új SQL Gateway-átjárók vannak hozzáadva. Ezek az SQL-átjárók **2020. szeptember 10-én** elkezdik az ügyfélforgalom elfogadását:

- USA nyugati középső régiója: 13.78.248.43 
- Dél-Afrika északi részén: 102.133.120.2  

A következő régiókban új SQL Gateway-átjárók vannak hozzáadva. Ezek az SQL-átjárók **2020. szeptember 1-től** elkezdik az ügyfélforgalom elfogadását:

- Észak-Európa: 13.74.104.113 
- USA 2. nyugati régiója: 40.78.248.10 
- Nyugat-Európa: 52.236.184.163 
- USA déli középső pontja: 20.45.121.1, 20.49.88.1 

A meglévő SQL-átjárók a következő régiókban kezdik meg a forgalom elfogadását. Ezek az SQL-átjárók **2020. szeptember 1-től** elkezdik az ügyfélforgalom elfogadását:
- Kelet-Japán: 40.79.184.8, 40.79.192.5


### <a name="august-2020"></a>2020. augusztus

Az új SQL-átjárók a következő régiókhoz vannak hozzáadva:

- Kelet-Ausztrália: 13.70.112.9
- Közép-Kanada: 52.246.152.0, 20.38.144.1 
- USA 2. nyugati régiója: 40.78.240.8

Ezek az SQL-átjárók 2020. augusztus 10-én elkezdik az ügyfélforgalom elfogadását. 

### <a name="october-2019"></a>2019. október
- Dél-Brazília
- USA nyugati régiója
- Nyugat-Európa
- USA keleti régiója
- Az USA középső régiója
- Délkelet-Ázsia
- USA déli középső régiója
- Észak-Európa
- USA északi középső régiója
- Nyugat-Japán
- Kelet-Japán
- USA 2. keleti régiója
- Kelet-Ázsia

---

## <a name="impact-of-this-change"></a>A változás hatása

A forgalomáttelepítés megváltoztathatja a nyilvános IP-címet, amit a DNS felold az adatbázishoz a Azure SQL Database.
A következő esetben lehet érintett:

- A helyszíni tűzfalon található bármely átjáró IP-címének szoftveres megkódlása
- A Microsoft.SQL-t szolgáltatásvégpontként használó alhálózatok nem kommunikálhatnak az átjáró IP-címével
- [Zónaredundáns konfiguráció használata általános célú szinten](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
- [Zónaredundáns konfiguráció használata a prémium & kritikus szintekhez](high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability)

A következő intézkedések nem lesznek hatással a következőre:
 
- Átirányítás csatlakozási szabályzatként
- Kapcsolatok SQL Database Azure-ból származó és szolgáltatáscímkék használatával
- A JDBC Driver for SQL Server támogatott verzióival létesített kapcsolatok nem lesznek hatással. A támogatott JDBC-verziókért lásd: [Download Microsoft JDBC Driver for SQL Server](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server).

## <a name="what-to-do-you-do-if-youre-affected"></a>Mi a mit kell tenni, ha érintett?

Javasoljuk, hogy engedélyezze az IP-címekre irányuló kimenő forgalmat a régióban található összes átjáró [IP-címe](connectivity-architecture.md#gateway-ip-addresses) számára az 1433-as TCP-porton és az 11000–11999-es porttartományon. Ez a javaslat a helyszínről csatlakozó ügyfelekre, valamint a szolgáltatásvégpontokkal csatlakozó ügyfelekre vonatkozik. A porttartományokkal kapcsolatos további információkért lásd: [Csatlakozási szabályzat.](connectivity-architecture.md#connection-policy)

A Microsoft JDBC-illesztőt 4.0-s verziónál régebbi verziójú alkalmazásokból létesített kapcsolatok sikertelenek lehetnek a tanúsítványok ellenőrzése során. A Microsoft JDBC alacsonyabb verziói a tanúsítvány Tárgy mezőjében a köznévre (CN) támaszkodnak. A megoldás annak biztosítása, hogy a hostNameInCertificate tulajdonság *.database.windows.net. A hostNameInCertificate tulajdonság beállításával kapcsolatos további információkért lásd: [Csatlakozás titkosítással.](/sql/connect/jdbc/connecting-with-ssl-encryption)

Ha a fenti megoldás nem működik, a következő URL-címen nyújtsa be SQL Database vagy SQL Managed Instance támogatási kérést: https://aka.ms/getazuresupport

## <a name="next-steps"></a>Következő lépések

- További információ a Azure SQL [architektúráról](connectivity-architecture.md)