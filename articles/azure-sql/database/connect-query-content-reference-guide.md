---
title: Csatlakozás és lekérdezés
description: A Azure SQL Database gyorsútmutatókra mutató hivatkozások, amelyek bemutatják, hogyan lehet csatlakozni a Azure SQL Database és Azure SQL Managed Instance.
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
ms.openlocfilehash: 96b52c80fa06be8c157ad39fd65be4e491e0cbe3
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874854"
---
# <a name="azure-sql-database-and-azure-sql-managed-instance-connect-and-query-articles"></a>Azure SQL Database és Azure SQL Managed Instance és lekérdezési cikkek
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

A következő dokumentum olyan Azure-példákra mutató hivatkozásokat tartalmaz, amelyek bemutatják a csatlakozást és a Azure SQL Database lekérdezését Azure SQL Managed Instance. A Transport Level Securityvel kapcsolatos javaslatokért lásd: [TLS-szempontok az adatbázis-kapcsolathoz.](#tls-considerations-for-database-connectivity)

## <a name="quickstarts"></a>Rövid útmutatók

| Gyorsútmutató | Description |
|---|---|
|[SQL Server Management Studio](connect-query-ssms.md)|Ez a rövid útmutató bemutatja, hogyan használható az SSMS egy adatbázishoz való csatlakozáshoz, majd hogyan lehet Transact-SQL-utasításokkal adatokat lekérdezni, beszúrni, frissíteni és törölni az adatbázisban.|
|[Azure Data Studio](/sql/azure-data-studio/quickstart-sql-database?toc=%2fazure%2fsql-database%2ftoc.json)|Ez a rövid útmutató bemutatja, hogyan használható az Azure Data Studio egy adatbázishoz való csatlakozáshoz, majd hogyan lehet Transact-SQL- (T-SQL-) utasításokkal létrehozni a Azure Data Studio oktatóanyagokban használt TutorialDB adatbázist.|
|[Azure Portal](connect-query-portal.md)|Ez a rövid útmutató azt mutatja be, hogyan használható a Lekérdezésszerkesztő egy adatbázishoz való csatlakozáshoz (csak Azure SQL Database), majd hogyan lehet Transact-SQL-utasításokkal adatokat lekérdezni, beszúrni, frissíteni és törölni az adatbázisban.|
|[Visual Studio Code](connect-query-vscode.md)|Ez a rövid útmutató azt mutatja be, hogyan használható a Visual Studio Code egy adatbázishoz való csatlakozáshoz, majd hogyan lehet Transact-SQL-utasításokkal adatokat lekérdezni, beszúrni, frissíteni és törölni az adatbázisban.|
|[.NET Visual Studióval](connect-query-dotnet-visual-studio.md)|Ez a rövid útmutató bemutatja, hogyan használható a .NET-keretrendszer olyan C#-program létrehozásához, amely a Visual Studio-t használja egy adatbázishoz való csatlakozáshoz, és hogyan lehet Transact-SQL-utasításokkal adatokat lekérdezni.|
|[.NET Core](connect-query-dotnet-core.md)|Ez a rövid útmutató bemutatja, hogyan használható a .NET Core olyan C#-program létrehozásához Windows/Linux/macOS rendszeren, amely egy adatbázishoz csatlakozik, és hogyan lehet Transact-SQL-utasításokkal adatokat lekérdezni.|
|[Ugrás](connect-query-go.md)|Ez a rövid útmutató azt mutatja be, hogyan használható a Go egy adatbázishoz való csatlakozáshoz. Emellett az adatok lekérdezéséhez és módosításához használatos Transact-SQL-utasításokat is bemutatja.|
|[Java](connect-query-java.md)|Ez a rövid útmutató azt mutatja be, hogyan használható a Java egy adatbázishoz való csatlakozáshoz, majd hogyan lehet Transact-SQL-utasításokkal adatokat lekérdezni.|
|[Node.js](connect-query-nodejs.md)|Ez a rövid útmutató bemutatja, hogyan használható Node.js egy olyan program létrehozásához, amely egy adatbázishoz csatlakozik, és hogyan lehet Transact-SQL-utasításokkal adatokat lekérdezni.|
|[PHP](connect-query-php.md)|Ez a rövid útmutató bemutatja, hogyan használható a PHP egy olyan program létrehozásához, amely egy adatbázishoz csatlakozik, és hogyan lehet Transact-SQL-utasításokkal adatokat lekérdezni.|
|[Python](connect-query-python.md)|Ez a rövid útmutató bemutatja, hogyan használható a Python egy adatbázishoz való csatlakozáshoz, és hogyan lehet Transact-SQL-utasításokkal adatokat lekérdezni. |
|[Ruby](connect-query-ruby.md)|Ez a rövid útmutató bemutatja, hogyan használható a Ruby egy olyan program létrehozásához, amely egy adatbázishoz csatlakozik, és hogyan lehet Transact-SQL-utasításokkal adatokat lekérdezni.|
|||

## <a name="get-server-connection-information"></a>Kiszolgáló kapcsolati információinak lekért adatai

Szerezze be az adatbázishoz való csatlakozáshoz szükséges kapcsolatadatokat a Azure SQL Database. A következő eljárásokhoz szüksége lesz a teljes kiszolgálónévre vagy állomásnévre, adatbázisnévre és bejelentkezési adatokra.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Lépjen az **SQL Databases vagy** a Felügyelt **SQL-példányok oldalra.**

3. Az Áttekintés **lapon** tekintse át az adatbázis  kiszolgálóneve melletti teljes kiszolgálónevet az Azure SQL Database-ban, vagy az Azure-beli virtuális gépen található gazdagép vagy virtuális gép Azure SQL Managed Instance SQL Server ip-címe mellett található teljes kiszolgálónevet (vagy IP-címet).  A kiszolgálónév vagy az állomásnév másoláshoz vigye fölé az egérmutatót, és válassza a **Másolás ikont.**

> [!NOTE]
> Az Azure-beli virtuális SQL Server kapcsolati információiért lásd: Csatlakozás SQL Server [példányhoz.](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server)

## <a name="get-adonet-connection-information-optional---sql-database-only"></a>Kapcsolati ADO.NET lekért információ (nem kötelező – csak SQL Database)

1. Lépjen az adatbázis paneljéhez a Azure Portal beállítások **alatt válassza** a Kapcsolati **sztringek lehetőséget.**

2. Tekintse át az **ADO.NET** teljes kapcsolati sztringjét.

    ![ADO.NET kapcsolati sztring](./media/connect-query-dotnet-core/adonet-connection-string2.png)

3. Ha **használni** ADO.NET, másolja ki a kapcsolati sztringet.

## <a name="tls-considerations-for-database-connectivity"></a>TLS-megfontolások az adatbázis-kapcsolathoz

Transport Layer Security -t (TLS) használja a Microsoft által az adatbázisokhoz való csatlakozáshoz a Microsoft által támogatott összes Azure SQL Database vagy Azure SQL Managed Instance. Nincs szükség különleges konfigurációra. Javasoljuk, hogy a SQL Server-példányhoz, a Azure SQL Database-adatbázishoz vagy a Azure SQL Managed Instance egy példányához való csatlakozáshoz minden alkalmazás állítsa be a következő konfigurációkat vagy azok megfelelőit:

- **Encrypt = On**
- **TrustServerCertificate = Off**

Egyes rendszerek eltérő, de egyenértékű kulcsszavakat használnak a fenti konfigurációs kulcsszavak helyett. Ezek a konfigurációk biztosítják, hogy az ügyfélillesztő ellenőrizze a kiszolgálótól kapott TLS-tanúsítvány identitását.

Javasoljuk továbbá, hogy tiltsa le az ügyfélen a TLS 1.1-et és 1.0-t, ha meg kell felelnie a Payment Card Industry – Data Security Standard (PCI-DSS) szabványnak.

Előfordulhat, hogy a nem a Microsofttól származó illesztők alapértelmezés szerint nem a TLS protokollt használják. Ez lehet egy tényező, amikor a Azure SQL Database vagy Azure SQL Managed Instance. A beágyazott illesztőkkel rendelkező alkalmazások nem mindig teszik lehetővé a kapcsolatbeállítások szabályozását. Javasoljuk, hogy vizsgálja meg az ilyen illesztők és alkalmazások biztonságát, mielőtt bizalmas adatokat kezelő rendszereken használná őket.

## <a name="libraries"></a>Kódtárak

Különböző kódtárak és keretrendszerek használatával csatlakozhat a Azure SQL Database vagy Azure SQL Managed Instance. Tekintse meg az Első lépések [oktatóanyagokat,](https://aka.ms/sqldev) amelyek segítségével gyorsan elkezdheti az olyan programozási nyelveket, mint a C#, a Java, a Node.js, a PHP és a Python. Ezután készítse el az alkalmazást a SQL Server on Linux macOS rendszeren a Windows vagy a Docker használatával.

Az alábbi táblázat azokat  a kapcsolati kódtárakat vagy illesztőprogramokat sorolja fel, amelyekhez az ügyfélalkalmazások különböző nyelvekről csatlakozhatnak és használhatnak SQL Server helyszínen vagy a felhőben futó alkalmazásokhoz. Linux, Windows vagy Docker rendszeren is használhatók, és használhatók Azure SQL Database, Azure SQL Managed Instance és Azure Synapse Analytics.

| Nyelv | Platform | További források | Letöltés | Bevezetés |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Microsoft ADO.NET for SQL Server](/sql/connect/ado-net/microsoft-ado-net-sql-server) | [Letöltés](https://dotnet.microsoft.com/download) | [Első lépések](https://www.microsoft.com/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows, Linux, macOS | [Microsoft JDBC-illesztő SQL Server](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server/) | [Letöltés](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server) |  [Első lépések](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows, Linux, macOS| [PHP SQL-illesztő SQL Server](/sql/connect/php/microsoft-php-driver-for-sql-server) | [Letöltés](/sql/connect/php/download-drivers-php-sql-server) | [Első lépések](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/)
| Node.js | Windows, Linux, macOS | [Node.js illesztőprogram a SQL Server](/sql/connect/node-js/node-js-driver-for-sql-server/) | [Telepítse](/sql/connect/node-js/step-1-configure-development-environment-for-node-js-development/) |  [Első lépések](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu)
| Python | Windows, Linux, macOS | [Python SQL-illesztő](/sql/connect/python/python-driver-for-sql-server/) | Telepítési lehetőségek: <br/> \*[pymssql](/sql/connect/python/pymssql/step-1-configure-development-environment-for-pymssql-python-development/) <br/> \*[pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development/) |  [Első lépések](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows, Linux, macOS | [Ruby-illesztőprogram SQL Server](/sql/connect/ruby/ruby-driver-for-sql-server/) | [Telepítse](/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development/) | [Első lépések](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows, Linux, macOS | [Microsoft ODBC-illesztő SQL Server](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server/) | [Letöltés](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server/) |  

Az alábbi táblázat az ügyfélalkalmazások által az SQL Server, Azure SQL Database, Azure SQL Managed Instance vagy Azure Synapse Analytics használható objektumrelációs leképezési (ORM) keretrendszerre és webes keretrendszerre mutat példákat. A keretrendszerek Linux, Windows vagy Docker rendszeren is használhatók.

| Nyelv | Platform | ORM(s) |
| :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Entity Framework](/ef)<br>[Entity Framework Core](/ef/core/index) |
| Java | Windows, Linux, macOS |[Hibernált ORM](https://hibernate.org/orm)|
| PHP | Windows, Linux, macOS | [Laravel (eloquent)](https://laravel.com/docs/eloquent)<br>[Doktrína](https://www.doctrine-project.org/projects/orm.html) |
| Node.js | Windows, Linux, macOS | [Az ORM-et feljavatják](https://sequelize.org/) |
| Python | Windows, Linux, macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows, Linux, macOS | [Ruby on Rails](https://rubyonrails.org/) |
||||

## <a name="next-steps"></a>Következő lépések

- A kapcsolati architektúrával kapcsolatos információkért tekintse meg [az Azure SQL Database kapcsolati architektúráját](connectivity-architecture.md) ismertető cikket.
- Keresse [SQL Server ügyfélalkalmazásból](/sql/connect/sql-connection-libraries/) való csatlakozáshoz használt illesztőprogramokat.
- Csatlakozás Azure SQL Database vagy Azure SQL Managed Instance:
  - [Csatlakozás és lekérdezés a .NET (C#) használatával](connect-query-dotnet-core.md)
  - [Csatlakozás és lekérdezés PHP használatával](connect-query-php.md)
  - [Csatlakozás és lekérdezés Node.js](connect-query-nodejs.md)
  - [Csatlakozás és lekérdezés Java használatával](connect-query-java.md)
  - [Csatlakozás és lekérdezés a Python használatával](connect-query-python.md)
  - [Csatlakozás és lekérdezés a Ruby használatával](connect-query-ruby.md)
  - [Telepítse az sqlcmd-et, és SQL Server](/sql/linux/sql-server-linux-setup-tools) parancssori eszközöket Linux rendszeren – Linux-felhasználók esetén próbáljon meg csatlakozni az Azure SQL Database-hoz vagy Azure SQL Managed Instance [az sqlcmd használatával.](/sql/ssms/scripting/sqlcmd-use-the-utility)
- Logikai kódokkal való újrapróbálkozási példák:
  - [Rugalmas csatlakozás a ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
  - [Rugalmas csatlakozás a PHP-hez][step-4-connect-resiliently-to-sql-with-php-p42h]

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: /sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: /sql/connect/php/step-4-connect-resiliently-to-sql-with-php
