---
title: A Visual Studio használata a .NET és a C# használatával a lekérdezéshez
description: A Visual Studióval hozzon létre egy C#-alkalmazást, amely Azure SQL Database vagy az Azure SQL felügyelt példányában található adatbázishoz kapcsolódik, és lekérdezéseket futtat.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: devx-track-csharp, sqldbrb=2
ms.devlang: dotnet
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/10/2020
ms.openlocfilehash: 1d8859f4790610e72ad517f74bbbbf0cf77d9316
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97705201"
---
# <a name="quickstart-use-net-and-c-in-visual-studio-to-connect-to-and-query-a-database"></a>Rövid útmutató: a .NET és a C# használata a Visual Studióban egy adatbázishoz való kapcsolódáshoz és lekérdezéshez
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

Ez a rövid útmutató bemutatja, hogyan használható a [.NET-keretrendszer](https://www.microsoft.com/net/) és a C# kód a Visual Studióban egy adatbázis lekérdezéséhez az Azure SQL-ben vagy a szinapszis SQL-ben Transact-SQL-utasításokkal.

## <a name="prerequisites"></a>Előfeltételek

Ennek a rövid útmutatónak az elvégzéséhez a következőkre lesz szüksége:

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Visual Studio 2019](https://www.visualstudio.com/downloads/) Közösségi, Professional vagy Enterprise kiadás.
- Egy adatbázis, amelyen futtathat egy lekérdezést.

  [!INCLUDE[create-configure-database](../includes/create-configure-database.md)]

## <a name="create-code-to-query-the-database-in-azure-sql-database"></a>Kód létrehozása az adatbázis lekérdezéséhez Azure SQL Database

1. Hozzon létre egy új projektet a Visual Studióban. 
   
1. Az **új projekt** párbeszédpanelen válassza a **Visual C#**, **Console app (.NET-keretrendszer)** elemet.
   
1. Adja meg a *sqltest* a projekt neveként, majd kattintson **az OK gombra**. Létrejön az új projekt. 
   
1. Válassza a **projekt**  >  **NuGet-csomagok kezelése** lehetőséget. 
   
1. A **NuGet csomagkezelő** lapján válassza a **Tallózás** lapot, majd keresse meg és válassza ki a **Microsoft. Form. SqlClient** elemet.
   
1. A **Microsoft. SqlClient** lapon válassza a **telepítés** lehetőséget. 
   - Ha a rendszer kéri, kattintson az **OK** gombra a telepítés folytatásához. 
   - Ha megjelenik a **licenc elfogadására** szolgáló ablak, válassza az **Elfogadom** lehetőséget.
   
1. A telepítés befejezésekor lezárhatja a **NuGet csomagkezelő eszközt**. 
   
1. A Kódszerkesztőben cserélje le a **program. cs** tartalmát a következő kódra. Cserélje le a, `<your_server>` , `<your_username>` `<your_password>` , és a értékeit `<your_database>` .
   
   ```csharp
   using System;
   using Microsoft.Data.SqlClient;
   using System.Text;
   
   namespace sqltest
   {
       class Program
       {
           static void Main(string[] args)
           {
               try 
               { 
                   SqlConnectionStringBuilder builder = new SqlConnectionStringBuilder();
                   builder.DataSource = "<your_server>.database.windows.net"; 
                   builder.UserID = "<your_username>";            
                   builder.Password = "<your_password>";     
                   builder.InitialCatalog = "<your_database>";
   
                   using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                   {
                       Console.WriteLine("\nQuery data example:");
                       Console.WriteLine("=========================================\n");
                       
                       String sql = "SELECT name, collation_name FROM sys.databases";
   
                       using (SqlCommand command = new SqlCommand(sql, connection))
                       {
                           connection.Open();
                           using (SqlDataReader reader = command.ExecuteReader())
                           {
                               while (reader.Read())
                               {
                                   Console.WriteLine("{0} {1}", reader.GetString(0), reader.GetString(1));
                               }
                           }
                       }                    
                   }
               }
               catch (SqlException e)
               {
                   Console.WriteLine(e.ToString());
               }
               Console.ReadLine();
           }
       }
   }
   ```

## <a name="run-the-code"></a>A kód futtatása

1. Az alkalmazás futtatásához válassza a **hibakeresés**  >  **megkezdése** lehetőséget, vagy válassza az eszköztáron az **Indítás** lehetőséget, vagy nyomja le az **F5** billentyűt.
1. Ellenőrizze, hogy a rendszer visszaadja-e az adatbázis nevét és rendezését, majd kattintson az alkalmazás ablakának bezárására.

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [csatlakozhat egy adatbázishoz a Azure SQL Database a .net Core használatával](connect-query-dotnet-core.md) a Windows/Linux/MacOS rendszeren.  
- További információ [a .NET Core használatának első lépéseiről Windows/Linux/macOS rendszeren a parancssorral](/dotnet/core/tutorials/using-with-xplat-cli).
- Megtudhatja, hogyan [tervezheti meg az első adatbázisát Azure SQL Database a SSMS használatával](design-first-database-tutorial.md) , vagy [az első adatbázis megtervezésével Azure SQL Database a .NET használatával](design-first-database-csharp-tutorial.md).
- A .NET-ről a [.NET dokumentációjában](/dotnet/) talál további információt.
- Próbálja ki újra a logikai példát: [kapcsolódjon rugalmasan az Azure SQL-hez a ADO.NET használatával][step-4-connect-resiliently-to-sql-with-ado-net-a78n].


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: /sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net
