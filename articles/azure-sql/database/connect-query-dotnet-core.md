---
title: Adatbázis csatlakoztatása és lekérdezése a .NET Core használatával
description: Ez a témakör bemutatja, hogyan használhatja a .NET Core-t egy olyan program létrehozásához, amely egy adatbázishoz csatlakozik a Azure SQL Database-ban, Azure SQL Managed Instance lekérdezi azt Transact-SQL-utasításokkal.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2, devx-track-csharp
ms.devlang: dotnet
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: 221b69d428556b031efd3bd91e16d12cfeb71393
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874800"
---
# <a name="quickstart-use-net-core-c-to-query-a-database"></a>Rövid útmutató: Adatbázis lekérdezése a .NET Core (C#) használatával
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

Ebben a rövid útmutatóban [.NET Core-](https://dotnet.microsoft.com) és C#-kódot fog használni egy adatbázishoz való csatlakozáshoz. Ezután egy Transact-SQL-utasítást fog futtatni az adatok lekérdezéséhez.

> [!TIP]
> A következő Microsoft Learn modul segít ingyenesen megtanulni, hogyan fejleszthet és konfigurálhat egy olyan ASP.NET-alkalmazást, amely [adatbázist](/learn/modules/develop-app-that-queries-azure-sql/) Azure SQL Database

## <a name="prerequisites"></a>Előfeltételek

Ennek a rövid útmutatónak az elvégzéséhez a következőkre lesz szüksége:

- Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [.NET Core SDK telepített operációs rendszerhez.](https://dotnet.microsoft.com/download)
- Egy adatbázis, ahol futtathatja a lekérdezést. 

  [!INCLUDE[create-configure-database](../includes/create-configure-database.md)]
  
## <a name="create-a-new-net-core-project"></a>Új .NET Core-projekt létrehozása

1. Nyisson meg egy parancssort, és hozzon létre egy **sqltest** nevű mappát. Lépjen erre a mappára, és futtassa ezt a parancsot.

    ```cmd
    dotnet new console
    ```

    Ez a parancs új alkalmazásprojekt-fájlokat hoz létre, beleértve a kezdeti C#-kódfájlt (**Program.cs),** egy XML-konfigurációs fájlt (**sqltest.csproj**), valamint a szükséges bináris fájlokat.

2. Egy szövegszerkesztőben nyissa meg az **sqltest.csproj** fájlt, és illessze be a következő XML-t a címkék `<Project>` közé. Ez az XML `System.Data.SqlClient` függőségként lesz hozzáadva.

    ```xml
    <ItemGroup>
        <PackageReference Include="System.Data.SqlClient" Version="4.6.0" />
    </ItemGroup>
    ```

## <a name="insert-code-to-query-the-database-in-azure-sql-database"></a>Kód beszúrása az adatbázis lekérdezéséhez a Azure SQL Database

1. Egy szövegszerkesztőben nyissa meg a **Program.cs programot.**

2. Cserélje le a tartalmát a következő kódra, és adja meg a kiszolgálójának, adatbázisának, felhasználónevének és jelszavának megfelelő értékeket.

> [!NOTE]
> Ha kapcsolati sztringet ADO.NET használni, cserélje le a kiszolgálót, az adatbázist, a felhasználónevet és a jelszót a kódban található 4 sorra az alábbi sorra. A sztringben adja meg a felhasználónevét és jelszavát.
>
>    `builder.ConnectionString="<your_ado_net_connection_string>";`

```csharp
using System;
using System.Data.SqlClient;
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

                builder.DataSource = "<your_server.database.windows.net>"; 
                builder.UserID = "<your_username>";            
                builder.Password = "<your_password>";     
                builder.InitialCatalog = "<your_database>";
         
                using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                {
                    Console.WriteLine("\nQuery data example:");
                    Console.WriteLine("=========================================\n");
                    
                    connection.Open();       

                    String sql = "SELECT name, collation_name FROM sys.databases";

                    using (SqlCommand command = new SqlCommand(sql, connection))
                    {
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
            Console.WriteLine("\nDone. Press enter.");
            Console.ReadLine(); 
        }
    }
}
```

## <a name="run-the-code"></a>A kód futtatása

1. Futtassa a következő parancsokat a parancssorban.

   ```cmd
   dotnet restore
   dotnet run
   ```

2. Ellenőrizze, hogy a sorok vissza vannak-e visszaadva.

   ```text
   Query data example:
   =========================================

   master   SQL_Latin1_General_CP1_CI_AS
   tempdb   SQL_Latin1_General_CP1_CI_AS
   WideWorldImporters   Latin1_General_100_CI_AS

   Done. Press enter.
   ```

3. Az **enter billentyűt** választva zárja be az alkalmazásablakot.

## <a name="next-steps"></a>Következő lépések

- [Bevezetés a .NET Core használatába Windows/Linux/macOS rendszeren a parancssorral](/dotnet/core/tutorials/using-with-xplat-cli).
- Ismerje meg, hogyan csatlakozhat és Azure SQL Database vagy Azure SQL Managed Instance a .NET-keretrendszer [és a Visual Studio.](connect-query-dotnet-visual-studio.md)  
- Ismerje meg, hogyan tervezhatja meg első adatbázisát [az SSMS-sel,](design-first-database-tutorial.md) vagy hogyan tervezhat adatbázist, és hogyan csatlakozhat [a C#](design-first-database-csharp-tutorial.md)és a ADO.NET.
- A .NET-ről a [.NET dokumentációjában](/dotnet/) talál további információt.
