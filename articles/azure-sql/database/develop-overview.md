---
title: Alkalmazások fejlesztésének áttekintése
description: Megismerheti az SQL Database-hez elérhető adatkapcsolattárakat és ajánlott eljárásokat az alkalmazások csatlakoztatásához.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: genemi
ms.date: 11/14/2019
ms.custom: sqldbrb=2
ms.openlocfilehash: 7ea3456dbd1d0942cba48c97d70982ae088d73f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92782977"
---
# <a name="application-development-overview---sql-database--sql-managed-instance"></a>Alkalmazásfejlesztés áttekintése – SQL Database & SQL felügyelt példánya

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Ez a cikk végigvezeti a fejlesztőnek az Azure-adatbázishoz való csatlakozáshoz szükséges kód írásakor. Ez a cikk a Azure SQL Database és az Azure SQL felügyelt példányára vonatkozik.

## <a name="language-and-platform"></a>Nyelv és platform

A Azure SQL Database összekapcsolásához és lekérdezéséhez különböző [programozási nyelveket és platformokat](connect-query-content-reference-guide.md) használhat. Megtalálhatja az adatbázishoz való kapcsolódáshoz használható [minta alkalmazásokat](https://azure.microsoft.com/resources/samples/?service=sql-database&sort=0) .

Használhatja a nyílt forráskódú eszközöket, például a [gepárd](https://github.com/wunderlist/cheetah), az [SQL-CLI](https://www.npmjs.com/package/sql-cli)és a [vs Code](https://code.visualstudio.com/)-ot. Ezen kívül az Azure SQL Database olyan Microsoft-eszközöket is támogat, mint például a [Visual Studio](https://www.visualstudio.com/downloads/) és az [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms). A Azure Portal, a PowerShell és a REST API-k segítségével további hatékonyságot érhet el.

## <a name="authentication"></a>Hitelesítés

A Azure SQL Database hozzáférése a bejelentkezésekkel és a tűzfalakkal is védett. A Azure SQL Database SQL Server és [Azure Active Directory hitelesítési](authentication-aad-overview.md) felhasználókat és bejelentkezéseket is támogat. Azure Active Directory bejelentkezések csak az SQL felügyelt példányaiban érhetők el. 

További információ az [adatbázis-hozzáférés és a bejelentkezés kezeléséről](logins-create-manage.md).

## <a name="connections"></a>Kapcsolatok

Az ügyfél csatlakozási logikájában írja felül az alapértelmezett időtúllépési értéket 30 másodpercre. A 15 másodperces időtúllépési érték túl rövid az internetkapcsolattól függő kapcsolatok számára.

Ha [kapcsolatkészletet](/dotnet/framework/data/adonet/sql-server-connection-pooling) használ, azonnal bontsa a kapcsolatot, ha a program már nem használja aktívan, és nem is tervezi az ismételt használatát.

Kerülje a hosszan futó tranzakciók elkerülését, mert az infrastruktúra vagy a kapcsolatok meghibásodása is visszaállíthatja a tranzakciót. Ha lehetséges, Ossza szét a tranzakciót a több kisebb tranzakcióban, és a [teljesítmény növelése érdekében használjon batchs-t](../performance-improve-use-batching.md).

## <a name="resiliency"></a>Rugalmasság

Azure SQL Database egy felhőalapú szolgáltatás, amely az alapul szolgáló infrastruktúrában vagy a felhőalapú entitások közötti kommunikációban felmerülő átmeneti hibákat várhat. Bár a Azure SQL Database rugalmas a tranzitív infrastruktúra meghibásodása esetén, ezek a hibák hatással lehetnek a kapcsolatra. Ha átmeneti hiba történik a SQL Databasehoz való csatlakozáskor, a kódnak [újra kell próbálkoznia a hívással](troubleshoot-common-connectivity-issues.md). Azt javasoljuk, hogy az újrapróbálkozási logika leállítási logikát használjon, hogy ne haladja meg a szolgáltatást egyszerre több ügyfél újrapróbálkozásával. Az újrapróbálkozási logika a [SQL Database-ügyfélprogramok hibaüzenetei](troubleshoot-common-errors-issues.md)függ.

További információ a Azure SQL Database tervezett karbantartási eseményeinek előkészítéséről: az [Azure karbantartási eseményeinek tervezése Azure SQL Databaseban](planned-maintenance.md).

## <a name="network-considerations"></a>Hálózati szempontok

- Győződjön meg róla, hogy az ügyfélprogramot futtató számítógép tűzfala engedélyezi a kimenő TCP-kommunikációt az 1433-as porton.  További információ: [Azure SQL Database tűzfal konfigurálása](firewall-configure.md).
- Ha az ügyfélprogram egy Azure-beli virtuális gépen (VM) fut, akkor a SQL Databasehoz kell csatlakoznia, és meg kell nyitnia bizonyos porttartomány-tartományokat a virtuális gépen. További információ: [a 1433-es, ADO.NET 4,5-es és SQL Database-es porton túli portok](adonet-v12-develop-direct-route-ports.md).
- A Azure SQL Databasehoz való ügyfélkapcsolatok időnként megkerülik a proxyt, és közvetlenül az adatbázissal működnek. Ekkor válnak fontossá az 1433-astól különböző portok. További információért [Azure SQL Database kapcsolati architektúrát](connectivity-architecture.md) és [portokat a 1433 ADO.NET 4,5 és SQL Database](adonet-v12-develop-direct-route-ports.md).
- A felügyelt SQL-példányok példányának hálózati konfigurációja: [SQL felügyelt példány hálózati konfigurációja](../managed-instance/how-to-content-reference-guide.md#network-configuration).

## <a name="next-steps"></a>Következő lépések

Ismerje meg [SQL Database](sql-database-paas-overview.md) és az [SQL felügyelt példányának](../managed-instance/sql-managed-instance-paas-overview.md)összes funkcióját.

Első lépésként tekintse meg a [Azure SQL Database](quickstart-content-reference-guide.md) és az [Azure SQL felügyelt példányainak](../managed-instance/quickstart-content-reference-guide.md)útmutatóit.