---
author: MightyPen
ms.service: sql-database
ms.topic: include
ms.date: 12/10/2018
ms.author: genemi
ms.openlocfilehash: bca885f9b19086302fd91d29b8ff6553b7205f28
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86544842"
---
## <a name="c-program-example"></a>C# program – példa

A cikk következő fejezetei egy C#-programot jelentenek, amely a ADO.NET használatával Transact-SQL-(T-SQL-) utasítások küldését SQL Database. A C# program a következő műveleteket mutatja be:

- [Kapcsolódás SQL Database a ADO.NET használatával](#cs_1_connect)
- [Metódusok, amelyek T-SQL-utasításokat adnak vissza](#cs_2_return)
    - Táblák létrehozása
    - Táblázatok feltöltése adatokkal
    - Az Adatfrissítés, törlés és kijelölés
- [T-SQL küldése az adatbázisba](#cs_3_submit)

### <a name="entity-relationship-diagram-erd"></a>Entity kapcsolati diagram (ERD)

Az `CREATE TABLE` utasítások a **hivatkozásokat** tartalmazó kulcsszóval hoznak létre egy *külső kulcs* (FK) kapcsolatát két táblázat között. Ha a *tempdb*-t használja, a `--REFERENCES` kulcsszót a kezdő gondolatjelek egy pár használatával írja ki.

Az ERD megjeleníti a két tábla közötti kapcsolatot. Az **tabEmployee. DepartmentCode** *gyermek* oszlopban lévő értékek a **tabDepartment. DepartmentCode** *szülő* oszlop értékeire korlátozódnak.

![Külső kulcsot bemutató ERD](./media/sql-database-csharp-adonet-create-query-2/erd-dept-empl-fky-2.png)

> [!NOTE]
> Lehetősége van arra, hogy a T-SQL-T úgy szerkessze, hogy a `#` tábla neveit adja hozzá, amely ideiglenes táblákként hozza létre őket a *tempdb*-ben. Ez bemutató célokra hasznos, ha nincs elérhető tesztelési adatbázis. A rendszer nem kényszeríti ki a külső kulcsokra mutató hivatkozásokat a használat során, és az ideiglenes táblák automatikusan törlődnek, ha a Kapcsolódás a program futtatása után befejeződik.

### <a name="to-compile-and-run"></a>Fordítás és Futtatás

A C# program logikailag egy. cs fájl, és fizikailag több kódrészletre van osztva, hogy az egyes blokkok könnyebben érthetőek legyenek. A program fordításához és futtatásához hajtsa végre a következő lépéseket:

1. C#-projekt létrehozása a Visual Studióban. A projekt típusának *konzolnak* kell lennie, amely a **sablonok**  >  **Visual C#**  >  **Windows asztali**  >  **konzol alkalmazás (.NET-keretrendszer)** területen található.

1. A file *program. cs* fájlban cserélje le a kód kezdő sorait a következő lépésekre:

    1. Másolja és illessze be az alábbi kódrészleteket ugyanabban a sorozatban, amelyben megjelennek. lásd: [Kapcsolódás az adatbázishoz](#cs_1_connect), a [T-SQL létrehozása](#cs_2_return)és az [adatbázisba való elküldés](#cs_3_submit).

    1. Módosítsa a következő értékeket a `Main` metódusban:

        - *CB. DataSource*
        - *CB. UserID*
        - *CB. Jelszó*
        - *cb.InitialCatalog*

1. Ellenőrizze, hogy a szerelvény *System.Data.dll* hivatkozik-e. Az ellenőrzéshez bontsa ki a **hivatkozások** csomópontot a **megoldáskezelő** ablaktáblán.

1. A program a Visual studióból való létrehozásához és futtatásához kattintson a **Start** gombra. A jelentés kimenete egy program ablakában jelenik meg, bár a GUID értékek a tesztek futtatása során változnak.

    ```Output
    =================================
    T-SQL to 2 - Create-Tables...
    -1 = rows affected.

    =================================
    T-SQL to 3 - Inserts...
    8 = rows affected.

    =================================
    T-SQL to 4 - Update-Join...
    2 = rows affected.

    =================================
    T-SQL to 5 - Delete-Join...
    2 = rows affected.

    =================================
    Now, SelectEmployees (6)...
    8ddeb8f5-9584-4afe-b7ef-d6bdca02bd35 , Alison , 20 , acct , Accounting
    9ce11981-e674-42f7-928b-6cc004079b03 , Barbara , 17 , hres , Human Resources
    315f5230-ec94-4edd-9b1c-dd45fbb61ee7 , Carol , 22 , acct , Accounting
    fcf4840a-8be3-43f7-a319-52304bf0f48d , Elle , 15 , NULL , NULL
    View the report output here, then press any key to end the program...
    ```

<a name="cs_1_connect"></a>

### <a name="connect-to-sql-database-using-adonet"></a>Kapcsolódás SQL Database a ADO.NET használatával

```csharp
using System;
using System.Data.SqlClient;   // System.Data.dll
//using System.Data;           // For:  SqlDbType , ParameterDirection

namespace csharp_db_test
{
    class Program
    {
        static void Main(string[] args)
        {
            try
            {
                var cb = new SqlConnectionStringBuilder();
                cb.DataSource = "your_server.database.windows.net";
                cb.UserID = "your_user";
                cb.Password = "your_password";
                cb.InitialCatalog = "your_database";

                using (var connection = new SqlConnection(cb.ConnectionString))
                {
                    connection.Open();

                    Submit_Tsql_NonQuery(connection, "2 - Create-Tables", Build_2_Tsql_CreateTables());

                    Submit_Tsql_NonQuery(connection, "3 - Inserts", Build_3_Tsql_Inserts());

                    Submit_Tsql_NonQuery(connection, "4 - Update-Join", Build_4_Tsql_UpdateJoin(),
                        "@csharpParmDepartmentName", "Accounting");

                    Submit_Tsql_NonQuery(connection, "5 - Delete-Join", Build_5_Tsql_DeleteJoin(),
                        "@csharpParmDepartmentName", "Legal");

                    Submit_6_Tsql_SelectEmployees(connection);
                }
            }
            catch (SqlException e)
            {
                Console.WriteLine(e.ToString());
            }

            Console.WriteLine("View the report output here, then press any key to end the program...");
            Console.ReadKey();
        }
```

<a name="cs_2_return"></a>

### <a name="methods-that-return-t-sql-statements"></a>Metódusok, amelyek T-SQL-utasításokat adnak vissza

```csharp
static string Build_2_Tsql_CreateTables()
{
    return @"
        DROP TABLE IF EXISTS tabEmployee;
        DROP TABLE IF EXISTS tabDepartment;  -- Drop parent table last.

        CREATE TABLE tabDepartment
        (
            DepartmentCode  nchar(4)          not null    PRIMARY KEY,
            DepartmentName  nvarchar(128)     not null
        );

        CREATE TABLE tabEmployee
        (
            EmployeeGuid    uniqueIdentifier  not null  default NewId()    PRIMARY KEY,
            EmployeeName    nvarchar(128)     not null,
            EmployeeLevel   int               not null,
            DepartmentCode  nchar(4)              null
            REFERENCES tabDepartment (DepartmentCode)  -- (REFERENCES would be disallowed on temporary tables.)
        );
    ";
}

static string Build_3_Tsql_Inserts()
{
    return @"
        -- The company has these departments.
        INSERT INTO tabDepartment (DepartmentCode, DepartmentName)
        VALUES
            ('acct', 'Accounting'),
            ('hres', 'Human Resources'),
            ('legl', 'Legal');

        -- The company has these employees, each in one department.
        INSERT INTO tabEmployee (EmployeeName, EmployeeLevel, DepartmentCode)
        VALUES
            ('Alison'  , 19, 'acct'),
            ('Barbara' , 17, 'hres'),
            ('Carol'   , 21, 'acct'),
            ('Deborah' , 24, 'legl'),
            ('Elle'    , 15, null);
    ";
}

static string Build_4_Tsql_UpdateJoin()
{
    return @"
        DECLARE @DName1  nvarchar(128) = @csharpParmDepartmentName;  --'Accounting';

        -- Promote everyone in one department (see @parm...).
        UPDATE empl
        SET
            empl.EmployeeLevel += 1
        FROM
            tabEmployee   as empl
        INNER JOIN
            tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
        WHERE
            dept.DepartmentName = @DName1;
    ";
}

static string Build_5_Tsql_DeleteJoin()
{
    return @"
        DECLARE @DName2  nvarchar(128);
        SET @DName2 = @csharpParmDepartmentName;  --'Legal';

        -- Right size the Legal department.
        DELETE empl
        FROM
            tabEmployee   as empl
        INNER JOIN
            tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
        WHERE
            dept.DepartmentName = @DName2

        -- Disband the Legal department.
        DELETE tabDepartment
            WHERE DepartmentName = @DName2;
    ";
}

static string Build_6_Tsql_SelectEmployees()
{
    return @"
        -- Look at all the final Employees.
        SELECT
            empl.EmployeeGuid,
            empl.EmployeeName,
            empl.EmployeeLevel,
            empl.DepartmentCode,
            dept.DepartmentName
        FROM
            tabEmployee   as empl
        LEFT OUTER JOIN
            tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
        ORDER BY
            EmployeeName;
    ";
}
```

<a name="cs_3_submit"></a>

### <a name="submit-t-sql-to-the-database"></a>T-SQL küldése az adatbázisba

```csharp
static void Submit_6_Tsql_SelectEmployees(SqlConnection connection)
{
    Console.WriteLine();
    Console.WriteLine("=================================");
    Console.WriteLine("Now, SelectEmployees (6)...");

    string tsql = Build_6_Tsql_SelectEmployees();

    using (var command = new SqlCommand(tsql, connection))
    {
        using (SqlDataReader reader = command.ExecuteReader())
        {
            while (reader.Read())
            {
                Console.WriteLine("{0} , {1} , {2} , {3} , {4}",
                    reader.GetGuid(0),
                    reader.GetString(1),
                    reader.GetInt32(2),
                    (reader.IsDBNull(3)) ? "NULL" : reader.GetString(3),
                    (reader.IsDBNull(4)) ? "NULL" : reader.GetString(4));
            }
        }
    }
}

static void Submit_Tsql_NonQuery(
    SqlConnection connection,
    string tsqlPurpose,
    string tsqlSourceCode,
    string parameterName = null,
    string parameterValue = null
    )
{
    Console.WriteLine();
    Console.WriteLine("=================================");
    Console.WriteLine("T-SQL to {0}...", tsqlPurpose);

    using (var command = new SqlCommand(tsqlSourceCode, connection))
    {
        if (parameterName != null)
        {
            command.Parameters.AddWithValue(  // Or, use SqlParameter class.
                parameterName,
                parameterValue);
        }
        int rowsAffected = command.ExecuteNonQuery();
        Console.WriteLine(rowsAffected + " = rows affected.");
    }
}
} // EndOfClass
}
```