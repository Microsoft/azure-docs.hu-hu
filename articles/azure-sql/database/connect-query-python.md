---
title: A Python használata adatbázis lekérdezéséhez
description: Ez a témakör bemutatja, hogyan használható a Python egy olyan program létrehozásához, amely Azure SQL Database-adatbázishoz kapcsolódik, és hogyan kérdezheti le azt a Transact-SQL-utasítások használatával.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-python-october2019, sqldbrb=2, devx-track-python
ms.devlang: python
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/19/2020
ms.openlocfilehash: f21e11e33d3ddf1489dba3419766a8adaa878d5f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103491962"
---
# <a name="quickstart-use-python-to-query-a-database"></a>Gyors útmutató: adatbázisok lekérdezése a Python használatával

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

Ebben a rövid útmutatóban a Python használatával kapcsolódhat Azure SQL Databasehoz, az Azure SQL felügyelt példányához vagy a szinapszis SQL Database-hez, és T-SQL-utasítások használatával adatokat lehet lekérdezni.

## <a name="prerequisites"></a>Előfeltételek

Ennek a rövid útmutatónak az elvégzéséhez a következőkre lesz szüksége:

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- Egy adatbázis, amelyen le fogja futtatni a lekérdezést.

  [!INCLUDE[create-configure-database](../includes/create-configure-database.md)]

- [Python](https://python.org/downloads) 3 és kapcsolódó szoftverek
    

    |**Művelet**|**macOS**|**Ubuntu**|**Windows**|
    |----------|-----------|------------|---------|
    |Telepítse az ODBC-illesztőt, a SQLCMD-t és a SQL Server Python-illesztőprogramját|Használja a **1,2**, **1,3** és **2,1** lépéseket a [Python-alkalmazások létrehozása a MacOS rendszeren SQL Server használatával](https://www.microsoft.com/sql-server/developer-get-started/python/mac/). Emellett telepíti a Homebrew-t és a Pythont is.       |[Környezet konfigurálása a pyodbc Python-fejlesztéshez](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux)|[Környezet konfigurálása a Pyodbc Python-fejlesztéshez](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows).|
    |Python-és egyéb szükséges csomagok telepítése|    |Használja az `sudo apt-get install python python-pip gcc g++ build-essential` parancsot.|    |
    |További információ|[Microsoft ODBC-illesztőprogram macOS rendszeren](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)  |[Microsoft ODBC-illesztőprogram Linux rendszeren](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)|[Microsoft ODBC-illesztőprogram Linux rendszeren](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)|



A Python és az adatbázis Azure SQL Databaseban való megismeréséhez lásd: [Azure SQL Database kódtárak a Pythonhoz](/python/api/overview/azure/sql), a [pyodbc adattárhoz](https://github.com/mkleehammer/pyodbc/wiki/)és egy [pyodbc-mintához](https://github.com/mkleehammer/pyodbc/wiki/Getting-started).

## <a name="create-code-to-query-your-database"></a>Kód létrehozása az adatbázis lekérdezéséhez 

1. Egy szövegszerkesztőben hozzon létre egy új, *sqltest.py* nevű fájlt.  
   
1. Adja hozzá a következő kódot. Szerezze be a kapcsolatok adatait az előfeltételek szakaszban, és cserélje le a,,, és a saját értékeit \<server> \<database> \<username> \<password> .
   
   ```python
   import pyodbc
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'   
   driver= '{ODBC Driver 17 for SQL Server}'
   
   with pyodbc.connect('DRIVER='+driver+';SERVER='+server+';PORT=1433;DATABASE='+database+';UID='+username+';PWD='+ password) as conn:
       with conn.cursor() as cursor:
           cursor.execute("SELECT TOP 3 name, collation_name FROM sys.databases")
           row = cursor.fetchone()
           while row:
               print (str(row[0]) + " " + str(row[1]))
               row = cursor.fetchone()
   ```
   

## <a name="run-the-code"></a>A kód futtatása

1. A parancssorban futtassa a következő parancsot:

   ```cmd
   python sqltest.py
   ```

1. Győződjön meg arról, hogy az adatbázisokat és azok rendezéseit adja vissza, majd a parancsablakban.

## <a name="next-steps"></a>Következő lépések

- [Az első adatbázis megtervezése Azure SQL Database](design-first-database-tutorial.md)
- [Microsoft Python-illesztőprogramok a SQL Server](/sql/connect/python/python-driver-for-sql-server/)
- [Python fejlesztői központ](https://azure.microsoft.com/develop/python/?v=17.23h)
