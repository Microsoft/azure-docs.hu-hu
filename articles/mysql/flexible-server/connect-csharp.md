---
title: 'Gyors útmutató: kapcsolat a C# használatával – Azure Database for MySQL rugalmas kiszolgáló'
description: Ez a rövid útmutató egy C# (.NET) mintakód-mintát biztosít, amellyel csatlakozhat Azure Database for MySQL rugalmas kiszolgálóról, és lekérdezheti azokat.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.custom: mvc, devx-track-csharp
ms.devlang: csharp
ms.topic: quickstart
ms.date: 01/16/2021
ms.openlocfilehash: c13b8392a766dc29d8c83f9986d0b43271caabbb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98606118"
---
# <a name="quickstart-use-net-c-to-connect-and-query-data-in-azure-database-for-mysql---flexible-server"></a>Rövid útmutató: a .NET (C#) használatával kapcsolódhat és lekérheti az adatlekérdezést Azure Database for MySQL-rugalmas kiszolgálón

Ebben a rövid útmutatóban azt szemléltetjük, hogy miként lehet C#-alkalmazás használatával csatlakozni az Azure Database for MySQL-hez. Bemutatjuk, hogy az SQL-utasítások használatával hogyan kérdezhetők le, illeszthetők be, frissíthetők és törölhetők az adatok az adatbázisban.

## <a name="prerequisites"></a>Előfeltételek
Ehhez a rövid útmutatóhoz a következőkre lesz szüksége:

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free).
- Azure Database for MySQL rugalmas kiszolgáló létrehozása [Azure Portal](./quickstart-create-server-portal.md) használatával <br/> vagy az [Azure CLI](./quickstart-create-server-cli.md) -vel, ha még nem rendelkezik ilyennel.
- Attól függően, hogy nyilvános vagy privát hozzáférést használ-e, a kapcsolat engedélyezéséhez hajtsa végre az alábbi műveletek **egyikét** .
- [Adatbázis és nem rendszergazda felhasználó létrehozása](../howto-create-users.md)

[Problémák léptek fel? Tudassa velünk](https://github.com/MicrosoftDocs/azure-docs/issues)

## <a name="create-a-c-project"></a>C#-projekt létrehozása
Egy parancssorból futtassa az alábbi parancsot:

```
mkdir AzureMySqlExample
cd AzureMySqlExample
dotnet new console
dotnet add package MySqlConnector
```

## <a name="get-connection-information"></a>Kapcsolatadatok lekérése
Kérje le a MySQL-hez készült Azure Database-hez való csatlakozáshoz szükséges kapcsolatadatokat. Szüksége lesz a teljes kiszolgálónévre és a bejelentkezési hitelesítő adatokra.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/).
2. Azure Portal bal oldali menüjében válassza a **minden erőforrás** elemet, majd keresse meg a létrehozott kiszolgálót (például **mydemoserver**).
3. Válassza ki a kiszolgálónevet.
4. A kiszolgáló **Áttekintés** paneléről jegyezze fel a **Kiszolgálónevet** és a **Kiszolgáló-rendszergazdai bejelentkezési nevet**. Ha elfelejti a jelszavát, ezen a panelen új jelszót is tud kérni.
 :::image type="content" source="./media/connect-csharp/server-overview-name-login.png" alt-text="A MySQL-hez készült Azure Database-kiszolgáló neve":::

## <a name="step-1-connect-and-insert-data"></a>1. lépés: az adatkapcsolat és az adatbeszúrás
A következő kóddal csatlakozhat, és betöltheti az adatokat a `CREATE TABLE` és az `INSERT INTO` SQL-utasítással. A kód a osztály metódusait használja `MySqlConnection` :
- [OpenAsync ()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync) a MySQL-kapcsolat létrehozásához.
- [CreateCommand ()](/dotnet/api/system.data.common.dbconnection.createcommand), beállítja a CommandText tulajdonságot.
- [ExecuteNonQueryAsync ()](/dotnet/api/system.data.common.dbcommand.executenonqueryasync) az adatbázis-parancsok futtatásához.

Cserélje le a `Server`, `Database`, `UserID` és `Password` paramétert azokkal az értékekkel, amelyeket a kiszolgáló és az adatbázis létrehozásakor adott meg.

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlCreate
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "DROP TABLE IF EXISTS inventory;";
                    await command.ExecuteNonQueryAsync();
                    Console.WriteLine("Finished dropping table (if existed)");

                    command.CommandText = "CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);";
                    await command.ExecuteNonQueryAsync();
                    Console.WriteLine("Finished creating table");

                    command.CommandText = @"INSERT INTO inventory (name, quantity) VALUES (@name1, @quantity1),
                        (@name2, @quantity2), (@name3, @quantity3);";
                    command.Parameters.AddWithValue("@name1", "banana");
                    command.Parameters.AddWithValue("@quantity1", 150);
                    command.Parameters.AddWithValue("@name2", "orange");
                    command.Parameters.AddWithValue("@quantity2", 154);
                    command.Parameters.AddWithValue("@name3", "apple");
                    command.Parameters.AddWithValue("@quantity3", 100);

                    int rowCount = await command.ExecuteNonQueryAsync();
                    Console.WriteLine(String.Format("Number of rows inserted={0}", rowCount));
                }

                // connection will be closed by the 'using' block
                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```

[Problémákba ütközik? Tudassa velünk.](https://github.com/MicrosoftDocs/azure-docs/issues)

## <a name="step-2-read-data"></a>2. lépés: az adatolvasás

A következő kóddal csatlakozhat, és beolvashatja az adatokat a `SELECT` SQL-utasítással. A kód a `MySqlConnection` osztályt használja metódusokkal:
- [OpenAsync ()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync) a MySQL-kapcsolat létrehozásához.
- [CreateCommand ()](/dotnet/api/system.data.common.dbconnection.createcommand) a CommandText tulajdonság beállításához.
- [ExecuteReaderAsync ()](/dotnet/api/system.data.common.dbcommand.executereaderasync) az adatbázis-parancsok futtatásához.
- [ReadAsync ()](/dotnet/api/system.data.common.dbdatareader.readasync#System_Data_Common_DbDataReader_ReadAsync) az eredményekben lévő rekordokra való továbblépés céljából. Ezután a kód a GetInt32 és a GetString metódussal elemzi a rekord értékeit.


Cserélje le a `Server`, `Database`, `UserID` és `Password` paramétert azokkal az értékekkel, amelyeket a kiszolgáló és az adatbázis létrehozásakor adott meg.

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlRead
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER@YOUR-SERVER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "SELECT * FROM inventory;";

                    using (var reader = await command.ExecuteReaderAsync())
                    {
                        while (await reader.ReadAsync())
                        {
                            Console.WriteLine(string.Format(
                                "Reading from table=({0}, {1}, {2})",
                                reader.GetInt32(0),
                                reader.GetString(1),
                                reader.GetInt32(2)));
                        }
                    }
                }

                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```

[Problémák léptek fel? Tudassa velünk](https://github.com/MicrosoftDocs/azure-docs/issues)

## <a name="step-3-update-data"></a>3. lépés: az Adatfrissítés
A következő kóddal csatlakozhat, és beolvashatja az adatokat az `UPDATE` SQL-utasítással. A kód a `MySqlConnection` osztályt használja metódussal:
- [OpenAsync ()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync) a MySQL-kapcsolat létrehozásához.
- [CreateCommand ()](/dotnet/api/system.data.common.dbconnection.createcommand) a CommandText tulajdonság beállításához
- [ExecuteNonQueryAsync ()](/dotnet/api/system.data.common.dbcommand.executenonqueryasync) az adatbázis-parancsok futtatásához.


Cserélje le a `Server`, `Database`, `UserID` és `Password` paramétert azokkal az értékekkel, amelyeket a kiszolgáló és az adatbázis létrehozásakor adott meg.

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlUpdate
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "UPDATE inventory SET quantity = @quantity WHERE name = @name;";
                    command.Parameters.AddWithValue("@quantity", 200);
                    command.Parameters.AddWithValue("@name", "banana");

                    int rowCount = await command.ExecuteNonQueryAsync();
                    Console.WriteLine(String.Format("Number of rows updated={0}", rowCount));
                }

                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```


## <a name="step-4-delete-data"></a>4. lépés: az adattörlés
A következő kóddal csatlakozhat, és törölheti az adatokat a `DELETE` SQL-utasítással.

A kód a `MySqlConnection` osztályt használja metódussal
- [OpenAsync ()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync) a MySQL-kapcsolat létrehozásához.
- [CreateCommand ()](/dotnet/api/system.data.common.dbconnection.createcommand) a CommandText tulajdonság beállításához.
- [ExecuteNonQueryAsync ()](/dotnet/api/system.data.common.dbcommand.executenonqueryasync) az adatbázis-parancsok futtatásához.


Cserélje le a `Server`, `Database`, `UserID` és `Password` paramétert azokkal az értékekkel, amelyeket a kiszolgáló és az adatbázis létrehozásakor adott meg.

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlDelete
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "DELETE FROM inventory WHERE name = @name;";
                    command.Parameters.AddWithValue("@name", "orange");

                    int rowCount = await command.ExecuteNonQueryAsync();
                    Console.WriteLine(String.Format("Number of rows deleted={0}", rowCount));
                }

                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az ebben a rövid útmutatóban használt összes erőforrás törléséhez törölje az erőforráscsoportot a következő parancs használatával:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Azure Database for MySQL-kiszolgáló kezelése a portál használatával](./how-to-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [Azure Database for MySQL-kiszolgáló kezelése a parancssori felület használatával](./how-to-manage-server-cli.md)


