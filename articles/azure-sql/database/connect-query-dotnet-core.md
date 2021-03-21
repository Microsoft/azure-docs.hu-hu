---
title: Adatbázis összekötése és lekérdezése a .NET Core használatával
description: Ebből a témakörből megtudhatja, hogyan használhatja a .NET Core-t egy olyan program létrehozásához, amely Azure SQL Database vagy Azure SQL felügyelt példányban található adatbázishoz kapcsolódik, és a Transact-SQL-utasítások használatával kérdezi le azokat.
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
ms.openlocfilehash: 1d25f43ef5a694d8b94710055bf1be72a7fcb45c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97705214"
---
# <a name="quickstart-use-net-core-c-to-query-a-database"></a>Gyors útmutató: adatbázis lekérdezése a .NET Core (C#) használatával
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

Ebben a rövid útmutatóban a [.net Core](https://www.microsoft.com/net/) és a C# kódot fogja használni az adatbázishoz való kapcsolódáshoz. Ezután futtasson egy Transact-SQL-utasítást az adatok lekérdezéséhez.

> [!TIP]
> A következő Microsoft Learn modul segítséget nyújt a [ASP.NET-alkalmazások fejlesztéséhez és konfigurálásához, amelyek egy adatbázist kérdeznek le Azure SQL Database](/learn/modules/develop-app-that-queries-azure-sql/)

## <a name="prerequisites"></a>Előfeltételek

Ennek a rövid útmutatónak az elvégzéséhez a következőkre lesz szüksége:

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [.Net Core az operációs rendszer](https://www.microsoft.com/net/core) telepítéséhez.
- Egy adatbázis, amelyen futtathatja a lekérdezést. 

  [!INCLUDE[create-configure-database](../includes/create-configure-database.md)]
  
## <a name="create-a-new-net-core-project"></a>Új .NET Core-projekt létrehozása

1. Nyisson meg egy parancssort, és hozzon létre egy **sqltest** nevű mappát. Navigáljon erre a mappára, és futtassa ezt a parancsot.

    ```cmd
    dotnet new console
    ```

    Ez a parancs új alkalmazás-projektfájlok létrehozását hozza létre, beleértve a kezdeti C#-programkódot (**program. cs**), egy XML-konfigurációs fájlt (**sqltest. csproj**) és a szükséges bináris fájlokat.

2. Egy szövegszerkesztőben nyissa meg a **sqltest. csproj** fájlt, és illessze be a következő XML-kódot a `<Project>` címkék közé. Ez az XML `System.Data.SqlClient` függőségként adja meg.

    ```xml
    <ItemGroup>
        <PackageReference Include="System.Data.SqlClient" Version="4.6.0" />
    </ItemGroup>
    ```

## <a name="insert-code-to-query-the-database-in-azure-sql-database"></a>Kód beszúrása az adatbázis lekérdezéséhez Azure SQL Database

1. Egy szövegszerkesztőben nyissa meg a **program. cs programot**.

2. Cserélje le a tartalmát a következő kódra, és adja hozzá a kiszolgáló, az adatbázis, a Felhasználónév és a jelszó megfelelő értékeit.

> [!NOTE]
> Ha ADO.NET-kapcsolódási karakterláncot szeretne használni, cserélje le a kód 4 sorát a kiszolgáló, az adatbázis, a Felhasználónév és a jelszó beállítására az alábbi sorral. A karakterláncban adja meg a felhasználónevét és a jelszavát.
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

1. A parancssorban futtassa a következő parancsokat.

   ```cmd
   dotnet restore
   dotnet run
   ```

2. Ellenőrizze, hogy a rendszer visszaadja-e a sorokat.

   ```text
   Query data example:
   =========================================

   master   SQL_Latin1_General_CP1_CI_AS
   tempdb   SQL_Latin1_General_CP1_CI_AS
   WideWorldImporters   Latin1_General_100_CI_AS

   Done. Press enter.
   ```

3. Az alkalmazás ablakának bezárásához kattintson az **ENTER** gombra.

## <a name="next-steps"></a>Következő lépések

- [Bevezetés a .NET Core használatába Windows/Linux/macOS rendszeren a parancssorral](/dotnet/core/tutorials/using-with-xplat-cli).
- Ismerkedjen meg [a .NET-keretrendszer és a Visual Studio használatával a Azure SQL Database vagy az Azure SQL felügyelt példányának összekapcsolásával és lekérdezésével](connect-query-dotnet-visual-studio.md).  
- Megtudhatja, hogyan [tervezheti meg első adatbázisát a SSMS](design-first-database-tutorial.md) , vagy [megtervezheti az adatbázist, és csatlakozhat a C# és a ADO.net](design-first-database-csharp-tutorial.md)szolgáltatáshoz.
- A .NET-ről a [.NET dokumentációjában](/dotnet/) talál további információt.
