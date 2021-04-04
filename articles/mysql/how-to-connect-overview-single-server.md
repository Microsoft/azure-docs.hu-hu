---
title: Kapcsolat és lekérdezés – egyetlen kiszolgáló MySQL
description: Az Azure saját SQL Database egyetlen kiszolgálóhoz való csatlakozást és a lekérdezések futtatását bemutató rövid útmutatókra mutató hivatkozások.
services: mysql
ms.service: mysql
ms.topic: how-to
author: mksuni
ms.author: sumuth
ms.date: 09/22/2020
ms.openlocfilehash: 0414aaad5a1cf6edb9c2152eed70f8753946cca1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92546432"
---
# <a name="connect-and-query-overview-for-azure-database-for-mysql--single-server"></a>A MySQL-hez készült Azure Database-hez készült kapcsolat és lekérdezés áttekintése – egyetlen kiszolgáló

Az alábbi dokumentum olyan példákra mutató hivatkozásokat tartalmaz, amelyek bemutatják, hogyan csatlakozhat és végezhet lekérdezéseket Azure Database for MySQL egyetlen kiszolgálóval. Ez az útmutató a TLS-ajánlásokat és-könyvtárakat is tartalmazza, amelyek segítségével az alábbi támogatott nyelveken csatlakozhat a kiszolgálóhoz.

## <a name="quickstarts"></a>Rövid útmutatók

| Gyorsútmutató | Leírás |
|---|---|
|[MySQL Workbench](connect-workbench.md)|Ez a rövid útmutató bemutatja, hogyan használható a MySQL Workbench-ügyfél egy adatbázishoz való kapcsolódáshoz. Ezután a MySQL-utasításokkal adatokat lehet lekérdezni, beszúrni, frissíteni és törölni az adatbázisban.|
|[Azure Cloud Shell](./quickstart-create-mysql-server-database-using-azure-cli.md#connect-to-azure-database-for-mysql-server-using-mysql-command-line-client)|Ez a cikk bemutatja, hogyan futtathatja a **mysql.exe** a [Azure Cloud Shell](../cloud-shell/overview.md) a kiszolgálóhoz való kapcsolódáshoz, majd az utasítások futtatásához az adatbázisban lévő adatokat lekérdezni, beszúrni, frissíteni és törölni.|
|[MySQL a Visual Studióval](https://www.mysql.com/why-mysql/windows/visualstudio)|A MySQL-t a Visual studióhoz is használhatja a MySQL-kiszolgálóhoz való csatlakozáshoz. A MySQL a Visual studióhoz közvetlenül a Server Explorerben integrálódik, így egyszerűen beállítható az új kapcsolatok és az adatbázis-objektumok használata.|
|[PHP](connect-php.md)|Ez a rövid útmutató azt ismerteti, hogyan használható a PHP egy olyan program létrehozásához, amely egy adatbázishoz csatlakozik, és hogyan lehet MySQL-utasítások használatával adatokat lekérdezni.|
|[Java](connect-java.md)|Ez a rövid útmutató bemutatja, hogyan használható a Java egy adatbázishoz való kapcsolódáshoz, majd hogyan lehet MySQL-utasításokkal adatokat lekérdezni.|
|[Node.js](connect-nodejs.md)|Ez a rövid útmutató azt ismerteti, hogyan használható a Node.js egy olyan program létrehozásához, amely egy adatbázishoz csatlakozik, és MySQL-utasítások használatával kérdezi le az adatokat.|
|[.NET (C#)](connect-csharp.md)|Ez a rövid útmutató azt ismerteti, hogyan use.NET (C#) egy C#-program létrehozásához egy adatbázishoz való kapcsolódáshoz, és hogyan lehet MySQL-utasítások használatával adatokat lekérdezni.|
|[Ugrás](connect-go.md)|Ez a rövid útmutató bemutatja, hogyan használható a go egy adatbázishoz való kapcsolódáshoz. Emellett az adatok lekérdezéséhez és módosításához használatos Transact-SQL-utasításokat is bemutatja.|
|[Python](connect-python.md)|Ez a rövid útmutató bemutatja, hogyan használható a Python egy adatbázishoz való kapcsolódáshoz, és hogyan lehet MySQL-utasításokkal adatokat lekérdezni. |
|[Ruby](connect-ruby.md)|Ez a rövid útmutató azt ismerteti, hogyan használható a Ruby egy olyan program létrehozásához, amely egy adatbázishoz csatlakozik, és MySQL-utasítások használatával kérdezi le az adatokat.|
|[C++](connect-cpp.md)|Ez a rövid útmutató azt ismerteti, hogyan használható a C++ + egy olyan program létrehozásához, amely adatbázishoz való kapcsolódáshoz és lekérdezési adatként használható.|

## <a name="tls-considerations-for-database-connectivity"></a>TLS-megfontolások az adatbázis-kapcsolathoz

A Transport Layer Security (TLS) a Microsoft által szolgáltatott és a Azure Database for MySQL-adatbázisokhoz való csatlakozást támogató összes illesztőprogram által használható. Nincs szükség speciális konfigurációra, de a TLS 1,2-et az újonnan létrehozott kiszolgálókhoz kell kikényszeríteni. Javasoljuk, hogy a TLS 1,0-es és 1,1-es verzióját használja, majd frissítse a TLS-verziót a kiszolgálókon. Lásd: [a TLS konfigurálása](howto-tls-configurations.md)

## <a name="libraries"></a>Kódtárak

Azure Database for MySQL a MySQL-adatbázis világ legnépszerűbb közösségi kiadását használja. Ezért kompatibilis a programozási nyelvek és illesztőprogramok széles választékával. A cél az, hogy támogassa a legújabb MySQL-illesztőprogramokat, és a nyílt forráskódú Közösségből származó szerzőkkel folytatott erőfeszítésekkel folyamatosan javítsa a MySQL-illesztőprogramok funkcióit és használhatóságát.

Tekintse meg, hogy mely [illesztőprogramok](concepts-compatibility.md) kompatibilisek Azure Database for MySQL egyetlen kiszolgálóval.

## <a name="next-steps"></a>Következő lépések

- [Az adatáttelepítés a memóriakép és a visszaállítás használatával](concepts-migrate-dump-restore.md)
- [Az adatáttelepítés importálással és exportálással](concepts-migrate-import-export.md)