---
title: Lekérdezés Visual Studio .NET és C# használatával
description: A Visual Studio létrehozhat egy C#-alkalmazást, amely egy adatbázishoz csatlakozik egy Azure SQL Database vagy Azure SQL Managed Instance futtat lekérdezéseket.
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
ms.openlocfilehash: 1fe39e0ff9acc0c092199ab2dd199cf396f67d01
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874764"
---
# <a name="quickstart-use-net-and-c-in-visual-studio-to-connect-to-and-query-a-database"></a>Rövid útmutató: A .NET és a C# Visual Studio egy adatbázishoz való csatlakozáshoz és lekérdezéshez
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

Ez a rövid útmutató bemutatja, hogyan használhatja a [.NET-keretrendszer-](https://dotnet.microsoft.com) és C#-kódot a Visual Studio-ban egy adatbázis lekérdezéséhez a Azure SQL vagy Synapse SQL Transact-SQL-utasításokkal.

## <a name="prerequisites"></a>Előfeltételek

Ennek a rövid útmutatónak az elvégzéséhez a következőkre lesz szüksége:

- Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Visual Studio 2019](https://www.visualstudio.com/downloads/) Community, Professional vagy Enterprise kiadás.
- Egy adatbázis, ahol lekérdezést futtathat.

  [!INCLUDE[create-configure-database](../includes/create-configure-database.md)]

## <a name="create-code-to-query-the-database-in-azure-sql-database"></a>Kód létrehozása az adatbázis lekérdezéséhez a Azure SQL Database

1. A Visual Studio hozzon létre egy új projektet. 
   
1. A **New Project (Új projekt) párbeszédpanelen** válassza a **Visual C#**, **Console App (.NET-keretrendszer) lehetőséget.**
   
1. Adja *meg az sqltest* nevet a projekt neveként, majd kattintson az **OK gombra.** Az új projekt létrejön. 
   
1. Válassza **a Project** Manage  >  **NuGet Packages (Projekt kezelése NuGet-csomagok) lehetőséget.** 
   
1. A **NuGet Csomagkezelő** válassza a **Tallózás lapot,** majd keresse meg és válassza ki a **Microsoft.Data.SqlClient adatokat.**
   
1. A **Microsoft.Data.SqlClient lapon** válassza a Telepítés **lehetőséget.** 
   - Ha a rendszer kéri, kattintson **az OK gombra** a telepítés folytatásához. 
   - Ha **megjelenik** egy Licenc elfogadása ablak, válassza az **Elfogadom lehetőséget.**
   
1. A telepítés befejezése után bezárhatja a **NuGet-Csomagkezelő.** 
   
1. A kódszerkesztőben cserélje le a **Program.cs** tartalmát a következő kódra. Cserélje le a `<your_server>` , `<your_username>` , és `<your_password>` `<your_database>` értékeket.
   
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

1. Az alkalmazás futtatásához válassza a **Hibakeresési**  >  **hibakeresés hibakeresése hibakeresést,** vagy válassza az **Indítás** lehetőséget az eszköztáron, vagy nyomja le az **F5 billentyűt.**
1. Ellenőrizze, hogy a rendszer visszaadta-e az adatbázisneveket és -rendezéseket, majd zárja be az alkalmazásablakot.

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [csatlakozhat adatbázisokhoz, és](connect-query-dotnet-core.md) hogyan Azure SQL Database a .NET Core használatával Windows/Linux/macOS rendszeren.  
- További információ [a .NET Core használatának első lépéseiről Windows/Linux/macOS rendszeren a parancssorral](/dotnet/core/tutorials/using-with-xplat-cli).
- Ismerje meg, hogyan tervezi meg az első adatbázisát [Azure SQL Database SSMS](design-first-database-tutorial.md) használatával, vagy hogyan tervezi meg az első adatbázisát [Azure SQL Database .NET használatával.](design-first-database-csharp-tutorial.md)
- A .NET-ről a [.NET dokumentációjában](/dotnet/) talál további információt.
- Példa újrapróbálkozási logikára: Rugalmasan csatlakozhat a Azure SQL [a ADO.NET.][step-4-connect-resiliently-to-sql-with-ado-net-a78n]


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: /sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net
