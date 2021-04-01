---
title: 'Gyors útmutató: kapcsolat C#-Azure Database for PostgreSQL-rugalmas kiszolgálóval'
description: Ez a rövid útmutató egy C# (.NET) kódrészletet tartalmaz, amellyel csatlakozhat és lekérdezheti Azure Database for PostgreSQL-rugalmas kiszolgáló adatait.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc, devcenter, devx-track-csharp
ms.devlang: csharp
ms.topic: quickstart
ms.date: 01/16/2021
ms.openlocfilehash: fd6df64482c5eaeec79fe5f2c9a92aaae475dfdc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98606111"
---
# <a name="quickstart-use-net-c-to-connect-and-query-data-in-azure-database-for-postgresql---flexible-server"></a>Rövid útmutató: a .NET (C#) használatával kapcsolódhat és lekérheti az adatlekérdezést Azure Database for PostgreSQL-rugalmas kiszolgálón

Ebben a gyors útmutatóban azt szemléltetjük, hogy miként lehet C# nyelven írt alkalmazás használatával csatlakozni a PostgreSQL-hez készült Azure Database-hez. Bemutatjuk, hogy az SQL-utasítások használatával hogyan kérdezhetők le, illeszthetők be, frissíthetők és törölhetők az adatok az adatbázisban. A cikkben ismertetett lépések feltételezik, hogy Ön rendelkezik fejlesztési tapasztalatokkal a C# használatával kapcsolatban, a PostgreSQL-hez készült Azure Database használatában pedig még járatlan.

## <a name="prerequisites"></a>Előfeltételek
Ehhez a rövid útmutatóhoz a következőkre lesz szüksége:

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free).
- Azure Database for PostgreSQL rugalmas kiszolgáló létrehozása [Azure Portal](./quickstart-create-server-portal.md) használatával <br/> vagy az [Azure CLI](./quickstart-create-server-cli.md) -vel, ha még nem rendelkezik ilyennel.
- Használja a kiszolgálón elérhető üres *postgres* -adatbázist, vagy hozzon létre egy [új adatbázist](./quickstart-create-server-portal.md#connect-to-the-postgresql-database-using-psql).
- Telepítse a [.NET-keretrendszert](https://www.microsoft.com/net/download) a platformhoz (Windows, Ubuntu Linux vagy MacOS).
- Telepítse a [Visual studiót](https://www.visualstudio.com/downloads/) a projekt felépítéséhez.
- Telepítse a [Npgsql](https://www.nuget.org/packages/Npgsql/) NuGet csomagot a Visual Studióban.

## <a name="get-connection-information"></a>Kapcsolatadatok lekérése
Kérje le a PostgreSQL-hez készült Azure-adatbázishoz való csatlakozáshoz szükséges kapcsolatadatokat. Szüksége lesz a teljes kiszolgálónévre és a bejelentkezési hitelesítő adatokra.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/).
2. Az Azure Portal bal oldali menüjében kattintson a **Minden erőforrás** lehetőségre, és keressen rá a létrehozott kiszolgálóra (például **mydemoserver**).
3. Kattintson a kiszolgálónévre.
4. A kiszolgáló **Áttekintés** paneléről jegyezze fel a **Kiszolgálónevet** és a **Kiszolgáló-rendszergazdai bejelentkezési nevet**. Ha elfelejti a jelszavát, ezen a panelen új jelszót is tud kérni.
 :::image type="content" source="./media/connect-csharp/1-connection-string.png" alt-text="Azure Database for PostgreSQL-kiszolgáló neve":::

## <a name="step-1-connect-and-insert-data"></a>1. lépés: az adatkapcsolat és az adatbeszúrás
Az alábbi kód használatával csatlakozhat és töltheti be az adatokat a **CREATE TABLE** és az **INSERT INTO** SQL-utasítások segítségével. A kód a NpgsqlCommand osztályt használja a metódussal:
- [Nyissa meg a ()](https://www.npgsql.org/doc/api/Npgsql.NpgsqlConnection.html#Npgsql_NpgsqlConnection_Open) kapcsolatot a PostgreSQL-adatbázissal létesített kapcsolat létrehozásához.
- A [CreateCommand ()](https://www.npgsql.org/doc/api/Npgsql.NpgsqlConnection.html#Npgsql_NpgsqlConnection_CreateCommand) beállítja a CommandText tulajdonságot.
- [ExecuteNonQuery ()](https://www.npgsql.org/doc/api/Npgsql.NpgsqlCommand.html#Npgsql_NpgsqlCommand_ExecuteNonQuery) metódus az adatbázis-parancsok futtatásához.

> [!IMPORTANT]
> Cserélje le a gazdagép, az adatbázisnév, a felhasználó és a jelszó paramétereit azokra az értékekre, amelyeket a kiszolgáló és az adatbázis létrehozásakor adott meg.

```csharp
using System;
using Npgsql;

namespace Driver
{
    public class AzurePostgresCreate
    {
        // Obtain connection string information from the portal
        //
        private static string Host = "mydemoserver.postgres.database.azure.com";
        private static string User = "mylogin";
        private static string  DBname = "postgres";
        private static string Password = "<server_admin_password>";
        private static string Port = "5432";

        static void Main(string[] args)
        {
            // Build connection string using parameters from portal
            //
            string connString =
                String.Format(
                    "Server={0};Username={1};Database={2};Port={3};Password={4};SSLMode=Prefer",
                    Host,
                    User,
                    DBname,
                    Port,
                    Password);


            using (var conn = new NpgsqlConnection(connString))

            {
                Console.Out.WriteLine("Opening connection");
                conn.Open();

                using (var command = new NpgsqlCommand("DROP TABLE IF EXISTS inventory", conn))
                {
                    command.ExecuteNonQuery();
                    Console.Out.WriteLine("Finished dropping table (if existed)");

                }

                using (var command = new NpgsqlCommand("CREATE TABLE inventory(id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER)", conn))
                {
                    command.ExecuteNonQuery();
                    Console.Out.WriteLine("Finished creating table");
                }

                using (var command = new NpgsqlCommand("INSERT INTO inventory (name, quantity) VALUES (@n1, @q1), (@n2, @q2), (@n3, @q3)", conn))
                {
                    command.Parameters.AddWithValue("n1", "banana");
                    command.Parameters.AddWithValue("q1", 150);
                    command.Parameters.AddWithValue("n2", "orange");
                    command.Parameters.AddWithValue("q2", 154);
                    command.Parameters.AddWithValue("n3", "apple");
                    command.Parameters.AddWithValue("q3", 100);

                    int nRows = command.ExecuteNonQuery();
                    Console.Out.WriteLine(String.Format("Number of rows inserted={0}", nRows));
                }
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```

[Problémákba ütközik? Tudassa velünk.](https://github.com/MicrosoftDocs/azure-docs/issues)

## <a name="step-2-read-data"></a>2. lépés: az adatolvasás
Az alábbi kód használatával csatlakozhat és végezheti el az adatok olvasását **SELECT** SQL-utasítás segítségével. A kód a NpgsqlCommand osztályt használja a metódussal:
- [Nyissa meg a ()](https://www.npgsql.org/doc/api/Npgsql.NpgsqlConnection.html#Npgsql_NpgsqlConnection_Open) t a PostgreSQL-kapcsolat létrehozásához.
- Az [CreateCommand ()](https://www.npgsql.org/doc/api/Npgsql.NpgsqlConnection.html#Npgsql_NpgsqlConnection_CreateCommand) és a [ExecuteReader ()](https://www.npgsql.org/doc/api/Npgsql.NpgsqlCommand.html#Npgsql_NpgsqlCommand_ExecuteReader) parancsot az adatbázis-parancsok futtatásához.
- [Olvassa el ()](https://www.npgsql.org/doc/api/Npgsql.NpgsqlDataReader.html#Npgsql_NpgsqlDataReader_Read) az eredményekben lévő rekordra való továbblépés előtt.
- A [GetInt32 ()](https://www.npgsql.org/doc/api/Npgsql.NpgsqlDataReader.html#Npgsql_NpgsqlDataReader_GetInt32_System_Int32_) és a [GetString ()](https://www.npgsql.org/doc/api/Npgsql.NpgsqlDataReader.html#Npgsql_NpgsqlDataReader_GetString_System_Int32_) segítségével elemezheti a rekordban lévő értékeket.

> [!IMPORTANT]
> Cserélje le a gazdagép, az adatbázisnév, a felhasználó és a jelszó paramétereit azokra az értékekre, amelyeket a kiszolgáló és az adatbázis létrehozásakor adott meg.

```csharp
using System;
using Npgsql;

namespace Driver
{
    public class AzurePostgresRead
    {
        // Obtain connection string information from the portal
        //
        private static string Host = "mydemoserver.postgres.database.azure.com";
        private static string User = "mylogin";
        private static string  DBname = "postgres";
        private static string Password = "<server_admin_password>";
        private static string Port = "5432";

        static void Main(string[] args)
        {
            // Build connection string using parameters from portal
            //
            string connString =
                String.Format(
                    "Server={0}; User Id={1}; Database={2}; Port={3}; Password={4};SSLMode=Prefer",
                    Host,
                    User,
                    DBname,
                    Port,
                    Password);

            using (var conn = new NpgsqlConnection(connString))
            {

                Console.Out.WriteLine("Opening connection");
                conn.Open();


                using (var command = new NpgsqlCommand("SELECT * FROM inventory", conn))
                {

                    var reader = command.ExecuteReader();
                    while (reader.Read())
                    {
                        Console.WriteLine(
                            string.Format(
                                "Reading from table=({0}, {1}, {2})",
                                reader.GetInt32(0).ToString(),
                                reader.GetString(1),
                                reader.GetInt32(2).ToString()
                                )
                            );
                    }
                    reader.Close();
                }
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```
[Problémákba ütközik? Tudassa velünk.](https://github.com/MicrosoftDocs/azure-docs/issues)


## <a name="step-3-update-data"></a>3. lépés: az Adatfrissítés
A következő kód használatával csatlakozhat és frissítheti az adatokat az **UPDATE** SQL-utasítással. A kód a NpgsqlCommand osztályt használja a metódussal:
- [Nyissa meg a ()](https://www.npgsql.org/doc/api/Npgsql.NpgsqlConnection.html#Npgsql_NpgsqlConnection_Open) t a PostgreSQL-kapcsolat létrehozásához.
- A [CreateCommand ()](https://www.npgsql.org/doc/api/Npgsql.NpgsqlConnection.html#Npgsql_NpgsqlConnection_CreateCommand), beállítja a CommandText tulajdonságot.
- [ExecuteNonQuery ()](https://www.npgsql.org/doc/api/Npgsql.NpgsqlCommand.html#Npgsql_NpgsqlCommand_ExecuteNonQuery) metódus az adatbázis-parancsok futtatásához.

> [!IMPORTANT]
> Cserélje le a gazdagép, az adatbázisnév, a felhasználó és a jelszó paramétereit azokra az értékekre, amelyeket a kiszolgáló és az adatbázis létrehozásakor adott meg.

```csharp
using System;
using Npgsql;

namespace Driver
{
    public class AzurePostgresUpdate
    {
        // Obtain connection string information from the portal
        //
        private static string Host = "mydemoserver.postgres.database.azure.com";
        private static string User = "mylogin";
        private static string  DBname = "postgres";
        private static string Password = "<server_admin_password>";
        private static string Port = "5432";

        static void Main(string[] args)
        {
            // Build connection string using parameters from portal
            //
            string connString =
                String.Format(
                    "Server={0}; User Id={1}; Database={2}; Port={3}; Password={4};SSLMode=Prefer",
                    Host,
                    User,
                    DBname,
                    Port,
                    Password);

            using (var conn = new NpgsqlConnection(connString))
            {

                Console.Out.WriteLine("Opening connection");
                conn.Open();

                using (var command = new NpgsqlCommand("UPDATE inventory SET quantity = @q WHERE name = @n", conn))
                {
                    command.Parameters.AddWithValue("n", "banana");
                    command.Parameters.AddWithValue("q", 200);
                    int nRows = command.ExecuteNonQuery();
                    Console.Out.WriteLine(String.Format("Number of rows updated={0}", nRows));
                }
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}


```

[Problémákba ütközik? Tudassa velünk.](https://github.com/MicrosoftDocs/azure-docs/issues)

## <a name="step-4-delete-data"></a>4. lépés: az adattörlés
Az alábbi kód használatával csatlakozhat és végezheti el az adatok törlését egy **DELETE** SQL-utasítás segítségével.

A kód az NpgsqlCommand osztályt használja az [Open()](https://www.npgsql.org/doc/api/Npgsql.NpgsqlConnection.html#Npgsql_NpgsqlConnection_Open) metódussal, hogy kapcsolatot létesítsen a PostgreSQL-adatbázissal. A kód ezután a [CreateCommand()](https://www.npgsql.org/doc/api/Npgsql.NpgsqlConnection.html#Npgsql_NpgsqlConnection_CreateCommand) metódust használja, beállítja a CommandText tulajdonságot, és meghívja az [ExecuteNonQuery()](https://www.npgsql.org/doc/api/Npgsql.NpgsqlCommand.html#Npgsql_NpgsqlCommand_ExecuteNonQuery) metódust az adatbázisparancsok futtatásához.

> [!IMPORTANT]
> Cserélje le a gazdagép, az adatbázisnév, a felhasználó és a jelszó paramétereit azokra az értékekre, amelyeket a kiszolgáló és az adatbázis létrehozásakor adott meg.

```csharp
using System;
using Npgsql;

namespace Driver
{
    public class AzurePostgresDelete
    {
        // Obtain connection string information from the portal
        //
        private static string Host = "mydemoserver.postgres.database.azure.com";
        private static string User = "mylogin@mydemoserver";
        private static string  DBname = "postgres";
        private static string Password = "<server_admin_password>";
        private static string Port = "5432";

        static void Main(string[] args)
        {
            // Build connection string using parameters from portal
            //
            string connString =
                String.Format(
                    "Server={0}; User Id={1}; Database={2}; Port={3}; Password={4};SSLMode=Prefer",
                    Host,
                    User,
                    DBname,
                    Port,
                    Password);

            using (var conn = new NpgsqlConnection(connString))
            {
                Console.Out.WriteLine("Opening connection");
                conn.Open();

                using (var command = new NpgsqlCommand("DELETE FROM inventory WHERE name = @n", conn))
                {
                    command.Parameters.AddWithValue("n", "orange");

                    int nRows = command.ExecuteNonQuery();
                    Console.Out.WriteLine(String.Format("Number of rows deleted={0}", nRows));
                }
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
