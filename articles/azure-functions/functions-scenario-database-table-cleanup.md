---
title: Adatbázis-tisztítási feladat végrehajtása a Azure Functions használatával
description: A Azure Functions használatával ütemezhet egy olyan feladatot, amely a Azure SQL Databasehoz kapcsolódik a sorok rendszeres tisztításához.
ms.assetid: 076f5f95-f8d2-42c7-b7fd-6798856ba0bb
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 10/02/2019
ms.openlocfilehash: 0b5e255d7d108eb063ece4e5489a8762261a0bed
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88207261"
---
# <a name="use-azure-functions-to-connect-to-an-azure-sql-database"></a>Azure Functions használata Azure SQL Databasehoz való kapcsolódáshoz

Ez a cikk bemutatja, hogyan használható a Azure Functions egy Azure SQL Database vagy Azure SQL felügyelt példányhoz csatlakozó ütemezett feladatok létrehozásához. A függvény kódja megtisztítja a sorokat egy táblában az adatbázisban. Az új C#-függvény egy előre definiált időzítő-trigger sablon alapján jön létre a Visual Studio 2019-ben. Ennek a forgatókönyvnek a támogatásához egy adatbázis-kapcsolódási karakterláncot kell beállítania egy alkalmazás-beállításként a Function alkalmazásban. Az Azure SQL felügyelt példányához engedélyeznie kell a [nyilvános végpontot](../azure-sql/managed-instance/public-endpoint-configure.md) , hogy csatlakozni tudjon a Azure functions. Ez a forgatókönyv egy tömeges műveletet használ az adatbázison. 

Ha első alkalommal használja a C# függvények használatát, olvassa el a [Azure functions C# fejlesztői referenciát](functions-dotnet-class-library.md).

## <a name="prerequisites"></a>Előfeltételek

+ Hajtsa végre az [első függvény létrehozása a Visual Studióval](functions-create-your-first-function-visual-studio.md) című cikkben ismertetett lépéseket a helyi function alkalmazás létrehozásához, amely a 2. x vagy a futtatókörnyezet újabb verzióját célozza meg. Emellett közzé kell tennie a projektet egy Azure-beli Function alkalmazásban.

+ Ez a cikk egy Transact-SQL-parancsot mutat be, amely egy tömeges karbantartási műveletet hajt végre a AdventureWorksLT-mintaadatbázis **SalesOrderHeader** táblájában. A AdventureWorksLT-mintaadatbázis létrehozásához hajtsa végre a következő cikkben ismertetett lépéseket: [Azure SQL Database adatbázis létrehozása a Azure Portal használatával](../azure-sql/database/single-database-create-quickstart.md).

+ A rövid útmutatóhoz használt számítógép nyilvános IP-címéhez hozzá kell adnia egy [kiszolgálói szintű tűzfalszabály-szabályt](../azure-sql/database/firewall-create-server-level-portal-quickstart.md) . Ez a szabály szükséges ahhoz, hogy hozzáférhessen a SQL Database-példányhoz a helyi számítógépről.  

## <a name="get-connection-information"></a>Kapcsolatadatok lekérése

Meg kell adnia a létrehozott adatbázishoz tartozó kapcsolódási karakterláncot, amikor befejezte az [adatbázis létrehozása Azure SQL Database a Azure Portal használatával](../azure-sql/database/single-database-create-quickstart.md).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Válassza ki az **SQL-adatbázisok** elemet a bal oldali menüben, és válassza ki az adatbázist az **SQL-adatbázisok** lapon.

1. Válassza a **kapcsolatok karakterláncok** lehetőséget a **Beállítások** területen, és másolja a teljes **ADO.net** -kapcsolatok karakterláncát. Az Azure SQL felügyelt példánya esetében a nyilvános végponthoz tartozó kapcsolatok karakterláncának másolása.

    ![Másolja az ADO.NET kapcsolati sztringet.](./media/functions-scenario-database-table-cleanup/adonet-connection-string.png)

## <a name="set-the-connection-string"></a>A kapcsolati sztring beállítása

A függvények végrehajtásához szükséges gazdaszolgáltatást az Azure-ban egy függvényalkalmazás biztosítja. Ajánlott biztonsági eljárásként a Function app-beállításokban tárolja a kapcsolatok karakterláncait és egyéb titkait. Az Alkalmazásbeállítások használata megakadályozza a kapcsolatok karakterláncának véletlen közzétételét a kóddal. A Function alkalmazáshoz közvetlenül a Visual studióból férhet hozzá.

Előzőleg közzé kell tennie az alkalmazást az Azure-ban. Ha még nem tette meg, [tegye közzé a Function alkalmazást az Azure](functions-develop-vs.md#publish-to-azure)-ban.

1. A Megoldáskezelőban kattintson a jobb gombbal a Function app projektre, **és válassza a**  >  **Szerkesztés Azure app Service beállítások szerkesztése** lehetőséget. Válassza a **beállítás hozzáadása** lehetőséget, az **új Alkalmazásbeállítás neve** mezőbe írja be a nevet `sqldb_connection` , majd kattintson **az OK gombra**.

    ![A Function alkalmazás Alkalmazásbeállítások.](./media/functions-scenario-database-table-cleanup/functions-app-service-add-setting.png)

1. Az új **sqldb_connection** beállításban illessze be az előző szakaszban a **helyi** mezőbe másolt, a helyére `{your_username}` és a `{your_password}` helyőrzőket valós értékekkel. Válassza a **helyi érték beszúrása** lehetőséget, hogy a frissített értéket a **távoli** mezőbe másolja, majd válassza az **OK** gombot.

    ![Adja meg az SQL-kapcsolatok karakterláncának beállítását.](./media/functions-scenario-database-table-cleanup/functions-app-service-settings-connection-string.png)

    A rendszer az Azure-ban (**távoli**) tárolja a kapcsolatok karakterláncait. A titkok kiszivárgásának megakadályozása érdekében a Project-fájlon (**helyi**) lévő local.settings.jski kell zárni a forrás-vezérlőelemből, például egy. gitignore-fájl használatával.

## <a name="add-the-sqlclient-package-to-the-project"></a>A SqlClient-csomag hozzáadása a projekthez

Hozzá kell adnia a SqlClient könyvtárat tartalmazó NuGet-csomagot. Ez az adatelérési függvénytár szükséges a SQL Databasehoz való kapcsolódáshoz.

1. Nyissa meg a helyi function alkalmazás projektjét a Visual Studio 2019-ben.

1. Megoldáskezelő kattintson a jobb gombbal a Function app projektre, és válassza a **NuGet-csomagok kezelése** lehetőséget.

1. A **Tallózás** fülön keresse meg a(z) ```System.Data.SqlClient``` elemet, majd válassza ki.

1. A **System. SqlClient** lapon válassza a verzió lehetőséget, `4.5.1` majd kattintson a **telepítés** gombra.

1. A telepítés befejezése után tekintse át a módosításokat, majd kattintson az **OK** gombra az **Előnézet** ablak bezárásához.

1. Ha megjelenik egy **License Acceptance** (Licenc elfogadása) ablak, kattintson az **I Accept** (Elfogadom) gombra.

Most hozzáadhatja a SQL Databasehoz csatlakozó C#-függvény kódját.

## <a name="add-a-timer-triggered-function"></a>Időzítő által aktivált hozzáadása

1. Megoldáskezelő kattintson a jobb gombbal a Function app projektre, és válassza az  >  **új Azure-függvény** hozzáadása lehetőséget.

1. A kiválasztott **Azure functions** sablonnal adja meg az új elemet, `DatabaseCleanup.cs` majd válassza a **Hozzáadás** lehetőséget.

1. Az **új Azure-függvény** párbeszédpanelen válassza az **időzítő trigger** lehetőséget, majd **az OK gombot**. Ez a párbeszédablak létrehoz egy programkódot az időzítő által aktivált függvényhez.

1. Nyissa meg az új kódot, és adja hozzá a következő using utasításokat a fájl elejéhez:

    ```cs
    using System.Data.SqlClient;
    using System.Threading.Tasks;
    ```

1. Cserélje le a meglévő `Run` függvényt a következő kódra:

    ```cs
    [FunctionName("DatabaseCleanup")]
    public static async Task Run([TimerTrigger("*/15 * * * * *")]TimerInfo myTimer, ILogger log)
    {
        // Get the connection string from app settings and use it to create a connection.
        var str = Environment.GetEnvironmentVariable("sqldb_connection");
        using (SqlConnection conn = new SqlConnection(str))
        {
            conn.Open();
            var text = "UPDATE SalesLT.SalesOrderHeader " +
                    "SET [Status] = 5  WHERE ShipDate < GetDate();";

            using (SqlCommand cmd = new SqlCommand(text, conn))
            {
                // Execute the command and log the # rows affected.
                var rows = await cmd.ExecuteNonQueryAsync();
                log.LogInformation($"{rows} rows were updated");
            }
        }
    }
    ```

    Ez a függvény 15 másodpercenként fut az `Status` oszlopnak a szállítási dátum alapján történő frissítéséhez. További információ az időzítő triggerről: [időzítő trigger Azure Functionshoz](functions-bindings-timer.md).

1. Nyomja le az **F5** billentyűt a Function alkalmazás elindításához. Megnyílik a [Azure functions Core Tools](functions-develop-local.md) végrehajtási ablak a Visual Studio mögött.

1. Az indítás után 15 másodperccel a függvény fut. Tekintse meg a kimenetet, és jegyezze fel, hogy hány sor frissült a **SalesOrderHeader** táblában.

    ![Tekintse meg a függvények naplóit.](./media/functions-scenario-database-table-cleanup/function-execution-results-log.png)

    Az első végrehajtáskor frissítenie kell az 32-es adatsorokat. A következő futtatások nem frissítik az adatsorokat, hacsak nem módosítja a SalesOrderHeader, hogy az utasításban több sort is kiválasszanak `UPDATE` .

Ha azt tervezi, hogy [közzéteszi ezt a függvényt](functions-develop-vs.md#publish-to-azure), ne felejtse el módosítani az `TimerTrigger` attribútumot egy ésszerű [cron-ütemtervre](functions-bindings-timer.md#ncrontab-expressions) , mint 15 másodpercenként.

## <a name="next-steps"></a>Következő lépések

A következő lépésben megtudhatja, hogyan használható. A más szolgáltatásokkal való integrációhoz Logic Apps függvényekkel.

> [!div class="nextstepaction"]
> [Logic Apps-sel integrált függvény létrehozása](functions-twitter-email.md)

A functions szolgáltatással kapcsolatos további információkért tekintse meg a következő cikkeket:

+ [Az Azure Functions fejlesztői segédanyagai](functions-reference.md)  
  Programozói segédanyagok függvények kódolásához, valamint eseményindítók és kötések meghatározásához.
+ [Az Azure Functions tesztelése](functions-test-a-function.md)  
  Különböző függvénytesztelési eszközöket és technikákat ír le.  
