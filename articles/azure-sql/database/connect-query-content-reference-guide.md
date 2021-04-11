---
title: Csatlakozás és lekérdezés
description: Hivatkozások Azure SQL Database rövid útmutatókra, amelyek azt mutatják be, hogyan lehet csatlakozni a Azure SQL Databasehoz és a lekérdezésekhez, valamint az Azure SQL felügyelt példányához.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: guide
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/17/2021
ms.openlocfilehash: a8419ff2ba269b05d1fbf7e9d08a284085465fd8
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257415"
---
# <a name="azure-sql-database-and-azure-sql-managed-instance-connect-and-query-articles"></a>A Azure SQL Database és az Azure SQL felügyelt példányának csatlakozási és lekérdezési cikkei
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Az alábbi dokumentum olyan Azure-példákra mutató hivatkozásokat tartalmaz, amelyek a Azure SQL Database és az Azure SQL felügyelt példányának összekapcsolását és lekérdezését mutatják be. Az átviteli szintű biztonsággal kapcsolatos egyes javaslatokért lásd: [TLS-megfontolások az adatbázis-kapcsolathoz](#tls-considerations-for-database-connectivity).

## <a name="quickstarts"></a>Rövid útmutatók

| Gyorsútmutató | Description |
|---|---|
|[SQL Server Management Studio](connect-query-ssms.md)|Ez a rövid útmutató bemutatja, hogyan használható a SSMS egy adatbázishoz való kapcsolódáshoz, majd hogyan lehet Transact-SQL-utasításokkal adatokat lekérdezni, beszúrni, frissíteni és törölni az adatbázisban.|
|[Azure Data Studio](/sql/azure-data-studio/quickstart-sql-database?toc=%2fazure%2fsql-database%2ftoc.json)|Ez a rövid útmutató azt ismerteti, hogyan használható a Azure Data Studio egy adatbázishoz való kapcsolódáshoz, majd hogyan lehet Transact-SQL-(T-SQL-) utasítások használatával létrehozni a Azure Data Studio oktatóanyagokban használt oktatóanyagaiban.|
|[Azure Portal](connect-query-portal.md)|Ez a rövid útmutató azt ismerteti, hogyan használható a lekérdezéstervező egy adatbázishoz való kapcsolódáshoz (csak Azure SQL Database), majd hogyan lehet Transact-SQL-utasítások használatával adatokat lekérdezni, beszúrni, frissíteni és törölni az adatbázisban.|
|[Visual Studio Code](connect-query-vscode.md)|Ez a rövid útmutató bemutatja, hogyan használható a Visual Studio Code egy adatbázishoz való kapcsolódáshoz, majd hogyan lehet Transact-SQL-utasításokkal adatokat lekérdezni, beszúrni, frissíteni és törölni az adatbázisban.|
|[.NET Visual Studióval](connect-query-dotnet-visual-studio.md)|Ez a rövid útmutató bemutatja, hogyan használható a .NET-keretrendszer egy C#-program létrehozásához a Visual Studióval egy adatbázishoz való kapcsolódáshoz, és hogyan lehet Transact-SQL-utasítások használatával adatokat lekérdezni.|
|[.NET Core](connect-query-dotnet-core.md)|Ez a rövid útmutató azt ismerteti, hogyan használható a .NET Core a Windows/Linux/macOS rendszeren egy olyan C#-program létrehozásához, amely egy adatbázishoz csatlakozik, és hogyan lehet Transact-SQL-utasítások használatával adatokat lekérdezni.|
|[Ugrás](connect-query-go.md)|Ez a rövid útmutató bemutatja, hogyan használható a go egy adatbázishoz való kapcsolódáshoz. Emellett az adatok lekérdezéséhez és módosításához használatos Transact-SQL-utasításokat is bemutatja.|
|[Java](connect-query-java.md)|Ez a rövid útmutató azt ismerteti, hogyan használható a Java egy adatbázishoz való kapcsolódáshoz, majd hogyan lehet Transact-SQL-utasítások használatával adatokat lekérdezni.|
|[Node.js](connect-query-nodejs.md)|Ez a rövid útmutató bemutatja, hogyan használható a Node.js egy olyan program létrehozásához, amely egy adatbázishoz csatlakozik, és hogyan lehet Transact-SQL-utasítások használatával adatokat lekérdezni.|
|[PHP](connect-query-php.md)|Ez a rövid útmutató bemutatja, hogyan használható a PHP egy olyan program létrehozásához, amely egy adatbázishoz csatlakozik, és hogyan lehet Transact-SQL-utasítások használatával adatokat lekérdezni.|
|[Python](connect-query-python.md)|Ez a rövid útmutató bemutatja, hogyan használható a Python egy adatbázishoz való kapcsolódáshoz, és hogyan lehet Transact-SQL-utasítások használatával adatokat lekérdezni. |
|[Ruby](connect-query-ruby.md)|Ez a rövid útmutató bemutatja, hogyan használható a Ruby egy olyan program létrehozásához, amely egy adatbázishoz csatlakozik, és hogyan lehet Transact-SQL-utasítások használatával adatokat lekérdezni.|
|||

## <a name="get-server-connection-information"></a>Kiszolgáló-kapcsolatok adatainak beolvasása

A Azure SQL Database-adatbázishoz való kapcsolódáshoz szükséges kapcsolati adatok lekérése. A közelgő eljárásokhoz szüksége lesz a teljes kiszolgálónévre vagy az állomásnévre, az adatbázis nevére és a bejelentkezési adatokra.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Navigáljon az **SQL-adatbázisok**  vagy az **SQL-felügyelt példányok** lapra.

3. Az **Áttekintés** lapon tekintse át a teljes kiszolgálónevet a **kiszolgáló neve** mellett, Azure SQL Database vagy a **gazdagép** melletti teljes kiszolgálónevet (vagy IP-címet) egy Azure SQL felügyelt példányhoz, vagy SQL Server az Azure virtuális gépen. A kiszolgálónév vagy az állomásnév másolásához vigye a kurzort a fölé, és válassza a **Másolás** ikont.

> [!NOTE]
> SQL Server Azure-beli virtuális gépen való kapcsolódásával kapcsolatos információkért lásd: [kapcsolódás SQL Server-példányhoz](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server).

## <a name="get-adonet-connection-information-optional---sql-database-only"></a>ADO.NET-kapcsolatok adatainak beolvasása (nem kötelező – csak SQL Database)

1. Navigáljon a Azure Portal adatbázis paneljére, és a **Beállítások** területen válassza a **kapcsolódási karakterláncok** lehetőséget.

2. Tekintse át az **ADO.NET** teljes kapcsolati sztringjét.

    ![ADO.NET kapcsolati sztring](./media/connect-query-dotnet-core/adonet-connection-string2.png)

3. Ha használni kívánja, másolja a **ADO.net** -kapcsolódási karakterláncot.

## <a name="tls-considerations-for-database-connectivity"></a>TLS-megfontolások az adatbázis-kapcsolathoz

A Transport Layer Security (TLS) a Microsoft által szolgáltatott és a Azure SQL Database vagy az Azure SQL felügyelt példányain lévő adatbázisokhoz való csatlakozást támogató összes illesztőprogramhoz használható. Nincs szükség különleges konfigurációra. A SQL Server-példányok, a Azure SQL Database-adatbázisok vagy az Azure SQL felügyelt példányok egy példányának minden kapcsolata esetén azt javasoljuk, hogy minden alkalmazás a következő konfigurációkat vagy azok megfelelőit állítsa be:

- **Encrypt = On**
- **TrustServerCertificate = Off**

Egyes rendszerek eltérő, de egyenértékű kulcsszavakat használnak a fenti konfigurációs kulcsszavak helyett. Ezek a konfigurációk biztosítják, hogy az ügyfélillesztő ellenőrizze a kiszolgálótól kapott TLS-tanúsítvány identitását.

Javasoljuk továbbá, hogy tiltsa le az ügyfélen a TLS 1.1-et és 1.0-t, ha meg kell felelnie a Payment Card Industry – Data Security Standard (PCI-DSS) szabványnak.

Előfordulhat, hogy a nem a Microsofttól származó illesztők alapértelmezés szerint nem a TLS protokollt használják. Ez a Azure SQL Database vagy az Azure SQL felügyelt példányaihoz való csatlakozás során lehet tényező. A beágyazott illesztőkkel rendelkező alkalmazások nem mindig teszik lehetővé a kapcsolatbeállítások szabályozását. Javasoljuk, hogy vizsgálja meg az ilyen illesztők és alkalmazások biztonságát, mielőtt bizalmas adatokat kezelő rendszereken használná őket.

## <a name="libraries"></a>Kódtárak

A Azure SQL Database vagy az Azure SQL felügyelt példányaihoz való kapcsolódáshoz különböző kódtárak és keretrendszerek használhatók. Tekintse meg az első [lépéseket ismertető oktatóanyagokat](https://aka.ms/sqldev) , amelyekkel gyorsan megkezdheti a programozási nyelvek, például a C#, a Java, a Node.js, a PHP és a Python használatát. Ezután hozzon létre egy alkalmazást SQL Server on Linux vagy Windows vagy Docker használatával macOS rendszeren.

A következő táblázat felsorolja azokat a kapcsolódási könyvtárakat és *illesztőprogramokat* , amelyeket az ügyfélalkalmazások számos különböző nyelven használhatnak a helyszíni vagy a felhőben futó SQL Serverhoz való csatlakozáshoz és használatához. A Linuxon, a Windowson vagy a Docker-ben is felhasználhatja őket, hogy csatlakozzanak Azure SQL Databasehoz, az Azure SQL felügyelt példányához és az Azure szinapszis Analyticshez.

| Nyelv | Platform | További források | Letöltés | Bevezetés |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [A SQL Server Microsoft ADO.NET](/sql/connect/ado-net/microsoft-ado-net-sql-server) | [Letöltés](https://www.microsoft.com/net/download/) | [Első lépések](https://www.microsoft.com/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows, Linux, macOS | [Microsoft JDBC-illesztőprogram a SQL Serverhoz](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server/) | [Letöltés](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server) |  [Első lépések](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows, Linux, macOS| [PHP SQL-illesztőprogram a SQL Serverhoz](/sql/connect/php/microsoft-php-driver-for-sql-server) | [Letöltés](/sql/connect/php/download-drivers-php-sql-server) | [Első lépések](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/)
| Node.js | Windows, Linux, macOS | [ SQL ServerNode.js illesztőprogram](/sql/connect/node-js/node-js-driver-for-sql-server/) | [Telepítse](/sql/connect/node-js/step-1-configure-development-environment-for-node-js-development/) |  [Első lépések](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu)
| Python | Windows, Linux, macOS | [Python SQL-illesztőprogram](/sql/connect/python/python-driver-for-sql-server/) | Telepítési lehetőségek: <br/> \*[pymssql](/sql/connect/python/pymssql/step-1-configure-development-environment-for-pymssql-python-development/) <br/> \*[pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development/) |  [Első lépések](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows, Linux, macOS | [Ruby-illesztőprogram a SQL Serverhoz](/sql/connect/ruby/ruby-driver-for-sql-server/) | [Telepítse](/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development/) | [Első lépések](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows, Linux, macOS | [Microsoft ODBC-illesztőprogram a SQL Serverhoz](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server/) | [Letöltés](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server/) |  

Az alábbi táblázat felsorolja azokat az objektum-összehasonlító leképezési (ORM) keretrendszereket és webes keretrendszereket, amelyeket az ügyfélalkalmazások használhatnak SQL Server, Azure SQL Database, Azure SQL felügyelt példány vagy Azure szinapszis Analytics használatával. A keretrendszerek Linux, Windows vagy Docker rendszereken is használhatók.

| Nyelv | Platform | ORM (ok) |
| :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Entity Framework](/ef)<br>[Entity Framework Core](/ef/core/index) |
| Java | Windows, Linux, macOS |[Hibernált ORM](https://hibernate.org/orm)|
| PHP | Windows, Linux, macOS | [Laravel (ékesszóló)](https://laravel.com/docs/eloquent)<br>[Doctrine](https://www.doctrine-project.org/projects/orm.html) |
| Node.js | Windows, Linux, macOS | [Sequelize ORM](https://sequelize.org/) |
| Python | Windows, Linux, macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows, Linux, macOS | [Ruby on Rails](https://rubyonrails.org/) |
||||

## <a name="next-steps"></a>Következő lépések

- A kapcsolati architektúrával kapcsolatos információkért tekintse meg [az Azure SQL Database kapcsolati architektúráját](connectivity-architecture.md) ismertető cikket.
- Megtalálhatja SQL Server az ügyfélalkalmazások közötti kapcsolódáshoz használt [illesztőprogramokat](/sql/connect/sql-connection-libraries/) .
- Kapcsolódás Azure SQL Database vagy Azure SQL felügyelt példányhoz:
  - [A .NET (C#) használatával való kapcsolat és lekérdezés](connect-query-dotnet-core.md)
  - [A PHP-vel való kapcsolat és lekérdezés](connect-query-php.md)
  - [Kapcsolat és lekérdezés Node.jshasználatával ](connect-query-nodejs.md)
  - [Kapcsolat és lekérdezés a Java használatával](connect-query-java.md)
  - [Kapcsolat és lekérdezés a Python használatával](connect-query-python.md)
  - [Csatlakozási és lekérdezés a Ruby használatával](connect-query-ruby.md)
  - [Telepítse a Sqlcmd-t és a BCP-t a SQL Server parancssori eszközöket](/sql/linux/sql-server-linux-setup-tools) Linux rendszerű felhasználók számára, próbáljon meg csatlakozni Azure SQL Database vagy az Azure SQL felügyelt példányához a [Sqlcmd](/sql/ssms/scripting/sqlcmd-use-the-utility)használatával.
- Próbálja újra a logikai kódok példáit:
  - [Rugalmas csatlakozás a ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
  - [Rugalmas csatlakozás a PHP-vel][step-4-connect-resiliently-to-sql-with-php-p42h]

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: /sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: /sql/connect/php/step-4-connect-resiliently-to-sql-with-php
