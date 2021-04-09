---
title: Átjáró forgalmi áttelepítési nyilatkozata
description: A cikk a Azure SQL Database átjáró IP-címeinek áttelepítésére vonatkozó értesítéseket nyújt a felhasználóknak
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=1
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 07/01/2019
ms.openlocfilehash: 62e2306abc2d3132651fb40aab8c2f5b4d5043f4
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105960877"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Azure SQL Database forgalom áttelepítése újabb átjáróra
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Az Azure-infrastruktúra fejlesztésekor a Microsoft rendszeresen frissíti a hardvert, hogy biztosítsa a lehető legjobb felhasználói élményt. Az elkövetkező hónapokban az újabb hardveres generációk számára készült átjárók hozzáadását tervezzük, áttelepítjük a forgalmat, és végül leszerelik a régebbi hardverekre épülő átjárókat egyes régiókban.  

Az ügyfelek az egyes régiókban elérhető átjárók változása előtt is tájékoztatják a szolgáltatás állapotáról szóló értesítéseket. Az ügyfelek [a Azure Portal segítségével állíthatják be a tevékenységek naplójának riasztásait](../../service-health/alerts-activity-log-service-notifications-portal.md).

A legfrissebb információk a [Azure SQL Database átjáró IP-címei](connectivity-architecture.md#gateway-ip-addresses) táblában lesznek karbantartva.

## <a name="status-updates"></a>Állapot-frissítések

# <a name="in-progress"></a>[Folyamatban](#tab/in-progress-ip)

## <a name="april-2021"></a>Április 2021
Az új SQL Gateway-átjárók a következő régiókban lesznek hozzáadva:
- USA 2. keleti régiója: 40.70.144.193 ez az SQL-átjáró a 2021 április 30-ig kezdi meg az ügyfelek forgalmának elfogadását.

Az új SQL Gateway-átjárók a következő régiókban lesznek hozzáadva:
- Kelet-Norvégia: 51.120.96.33
- Dél-Kelet-Ázsia: 13.67.16.193
- Dél-Afrika északi régiója: 102.133.152.32
- Dél-Korea: 52.231.151.96
- Észak-Közép: USA 52.162.105.9
- Kelet-Ausztrália, Délkelet-Ausztrália: 13.77.49.32 

Ezek az SQL-átjárók az ügyfelek forgalmának elfogadását az 2021. április 5-én megkezdik.

## <a name="march-2021"></a>Március 2021
A következő SQL-átjárók több régióban vannak folyamatban inaktiválva:

- Dél-Brazília: 104.41.11.5
- Kelet-Ázsia: 191.234.2.139
- USA keleti régiója: 191.238.6.43
- Kelet-Japán: 191.237.240.43
- Nyugat-Japán: 191.238.68.11
- Észak-Európa: 191.235.193.75
- USA déli középső régiója: 23.98.162.75
- Délkelet-Ázsia: 23.100.117.95
- Nyugat-Európa: 191.237.232.75
- USA nyugati régiója: 23.99.34.75

Az ügyfelekre gyakorolt hatás várható, mivel ezek az átjárók (a régebbi hardvereken futnak) nem irányítják az ügyfelek forgalmát. Az átjárók IP-címeit inaktiválni kell a 2021. március 15-én.

## <a name="february-2021"></a>2021. február
Az új SQL Gateway-átjárók a következő régiókban lesznek hozzáadva:

- USA középső régiója: 13.89.169.20

Ezek az SQL-átjárók a 2021. február 28-án kezdődnek az ügyfelek forgalmának elfogadásával.

## <a name="january-2021"></a>2021. január
Az új SQL Gateway-átjárók a következő régiókban lesznek hozzáadva:

- Ausztrália középső régiója: 20.36.104.6, 20.36.104.7 
- Ausztrália középső régiója 2:20.36.112.6 
- Dél-Brazília: 191.234.144.16, 191.234.152.3 
- Kelet-Kanada: 40.69.105.9, 40.69.105.10
- Közép-India: 104.211.86.30, 104.211.86.31 
- Kelet-Ázsia: 13.75.32.14 
- Közép-Franciaország: 40.79.137.8, 40.79.145.12 
- Dél-Franciaország: 40.79.177.10, 40.79.177.12
- Korea középső régiója: 52.231.17.22, 52.231.17.23
- Nyugat-India: 104.211.144.4

Ezek az SQL-átjárók a 2021. január 31-én megkezdik az ügyfelek forgalmának fogadását.

# <a name="completed"></a>[Befejeződött](#tab/completed-ip)
A következő átjárók áttelepítése befejeződött: 

### <a name="october-2020"></a>2020. október

Az új SQL Gateway-átjárók a következő régiókban lesznek hozzáadva:

- Középnyugat-Németország: 51.116.240.0, 51.116.248.0

Ezek az SQL-átjárók a 2020 október 12-én megkezdik az ügyfél forgalmának elfogadását. 

### <a name="september-2020"></a>2020. szeptember
Az új SQL Gateway-átjárók a következő régiókban lesznek hozzáadva. Ezek az SQL-átjárók a **2020. szeptember 15**-én megkezdik az ügyfelek forgalmának fogadását:

- Délkelet-Ausztrália: 13.77.48.10
- Kelet-Kanada: 40.86.226.166, 52.242.30.154
- Egyesült Királyság déli régiója: 51.140.184.11, 51.105.64.0

A meglévő SQL Gateway-átjárók a következő régiókban kezdik el a forgalom fogadását. Ezek az SQL-átjárók a **2020. szeptember 15** -én megkezdik az ügyfelek forgalmának fogadását:

- Délkelet-Ausztrália: 191.239.192.109 és 13.73.109.251
- USA középső régiója: 13.67.215.62, 52.182.137.15, 23.99.160.139, 104.208.16.96 és 104.208.21.1
- Kelet-Ázsia: 191.234.2.139, 52.175.33.150 és 13.75.32.4
- USA keleti régiója: 40.121.158.30, 40.79.153.12, 191.238.6.43 és 40.78.225.32
- USA 2. keleti régiója: 40.79.84.180, 52.177.185.181, 52.167.104.0, 191.239.224.107 és 104.208.150.3
- Közép-Franciaország: 40.79.137.0 és 40.79.129.1
- Nyugat-Japán: 104.214.148.156, 40.74.100.192, 191.238.68.11 és 40.74.97.10
- USA északi középső régiója: 23.96.178.199, 23.98.55.75 és 52.162.104.33
- Délkelet-Ázsia: 104.43.15.0, 23.100.117.95 és 40.78.232.3
- USA nyugati régiója: 104.42.238.205, 23.99.34.75 és 13.86.216.196

Az új SQL Gateway-átjárók a következő régiókban lesznek hozzáadva. Ezek az SQL-átjárók a **2020 szeptember 10**-én megkezdik az ügyfél forgalmának elfogadását:

- USA nyugati középső régiója: 13.78.248.43 
- Dél-Afrika északi régiója: 102.133.120.2  

Az új SQL Gateway-átjárók a következő régiókban lesznek hozzáadva. Ezek az SQL-átjárók a **2020 szeptember 1-jén** kezdik el az ügyfelek forgalmának elfogadását:

- Észak-Európa: 13.74.104.113 
- Nyugat-RÉGIÓJA: 40.78.248.10 
- Nyugat-Európa: 52.236.184.163 
- USA déli középső régiója: 20.45.121.1, 20.49.88.1 

A meglévő SQL Gateway-átjárók a következő régiókban kezdik el a forgalom fogadását. Ezek az SQL-átjárók a **2020 szeptember 1-jén** kezdik el az ügyfelek forgalmának elfogadását:
- Kelet-Japán: 40.79.184.8, 40.79.192.5


### <a name="august-2020"></a>2020. augusztus

Az új SQL Gateway-átjárók a következő régiókban lesznek hozzáadva:

- Kelet-Ausztrália: 13.70.112.9
- Közép-Kanada: 52.246.152.0, 20.38.144.1 
- USA 2. nyugati régiója: 40.78.240.8

Ezek az SQL-átjárók az ügyfelek forgalmának elfogadását az 2020. augusztus 10-én megkezdik. 

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

A forgalom áttelepítése megváltoztathatja azt a nyilvános IP-címet, amelyet a DNS feloldja az adatbázishoz Azure SQL Databaseban.
A következőket lehet befolyásolni:

- A helyszíni tűzfal adott átjárójának IP-címe nem kódolt
- Minden olyan alhálózattal rendelkezik, amely a Microsoft. SQL szolgáltatást használja szolgáltatási végpontként, de nem tud kommunikálni az átjáró IP-címeivel
- Az [általános célú zóna redundáns konfigurációjának](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) használata
- A [zóna redundáns konfigurációjának használata a prémium szintű & üzleti szempontból kritikus szinteken](high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability)

A következőket nem érinti a rendszer:
 
- Átirányítás a kapcsolatok házirendjének megfelelően
- Kapcsolatok SQL Database az Azure-ban és a szolgáltatási címkék használatával
- A SQL Server JDBC illesztőprogram támogatott verzióival létesített kapcsolatok nem lesznek hatással. A támogatott JDBC-verziókért lásd: [a Microsoft JDBC-illesztőprogram letöltése SQL Serverhoz](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server).

## <a name="what-to-do-you-do-if-youre-affected"></a>Mi a teendő, ha érintett

Azt javasoljuk, hogy engedélyezze a kimenő forgalmat az IP-címekre a 1433-es TCP-porton és a 11000-11999-es porton a régióban lévő összes [ÁTJÁRÓ IP-címe](connectivity-architecture.md#gateway-ip-addresses) esetében. Ez a javaslat a helyszíni és a szolgáltatás-végpontokon keresztül csatlakozó ügyfelekre is érvényes. A porttartomány-tartományokkal kapcsolatos további információkért lásd: a [kapcsolatkérelem-házirend](connectivity-architecture.md#connection-policy).

Az alkalmazásból a 4,0-es verziónál régebbi Microsoft JDBC illesztőprogramot használó alkalmazások kapcsolatainak érvényesítése sikertelen lehet. A Microsoft JDBC korábbi verziói a tanúsítvány Tárgy mezőjében a köznapi név (CN) függvényt használják. A megoldás célja, hogy a hostNameInCertificate tulajdonság értéke *. database.windows.net legyen. A hostNameInCertificate tulajdonság beállításával kapcsolatos további információkért lásd: [Csatlakozás a titkosítással](/sql/connect/jdbc/connecting-with-ssl-encryption).

Ha a fenti megoldás nem működik, a következő URL-cím használatával kérjen SQL Database vagy SQL felügyelt példányra vonatkozó támogatási kérelmet: https://aka.ms/getazuresupport

## <a name="next-steps"></a>Következő lépések

- További információ az [Azure SQL kapcsolati architektúráról](connectivity-architecture.md)