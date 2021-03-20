---
title: 'Oktatóanyag: Webalkalmazás létrehozása (gyorsítótár-félretéve) – Azure cache a Redis-hez'
description: Ismerje meg, hogyan hozhat létre egy webalkalmazást az Azure cache használatával a gyorsítótár-feltöltési mintát használó Redis.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: tutorial
ms.custom: devx-track-csharp, mvc
ms.date: 03/30/2018
ms.openlocfilehash: 90e60044e227ea1a18ea032d302b29abda1ea2e8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92536844"
---
# <a name="tutorial-create-a-cache-aside-leaderboard-on-aspnet"></a>Oktatóanyag: Gyorsítótár-feltöltési ranglista létrehozása az ASP.NET-en

Ebben az oktatóanyagban frissíteni fogja a *ContosoTeamStats* ASP.net-webalkalmazást, amely az [Azure cache](cache-web-app-howto.md)-hez készült ASP.net-útmutatóban jön létre a Redis-hez, hogy tartalmazzon egy olyan Leaderboard-t, amely az Azure cache-t használó [gyorsítótár-](/azure/architecture/patterns/cache-aside) feltöltési mintát használja Redis A minta alkalmazás megjeleníti a csapat statisztikáit egy adatbázisból, és bemutatja, hogyan használhatja az Azure cache-t a Redis tárolt adatok tárolására és lekérésére a teljesítmény javítása érdekében. Az oktatóanyag elvégzése után egy futó webalkalmazás rendelkezik, amely egy adatbázisba olvas és ír, amely az Azure cache-vel és az Azure-ban üzemeltetett Redis van optimalizálva.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Javítsa az adatok átviteli sebességét, és csökkentse az adatbázis terhelését azáltal, hogy az Azure cache használatával tárolja és beolvassa az Redis.
> * Egy rendezett Redis-készlet használata az öt legjobb csoport lekérdezéséhez.
> * Azure-erőforrások kiépítése egy Resource Manager-sablont használó alkalmazás számára.
> * Alkalmazás közzététele az Azure-ban a Visual Studio használatával.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbi előfeltételekkel kell rendelkeznie:

* Ez az oktatóanyag folytatja, ahol abbahagyta a [Redis Azure cache](cache-web-app-howto.md)-hez készült ASP.net gyors üzembe helyezését. Ha még nem tette meg, kövesse a rövid útmutató utasításait.
* Telepítse a [Visual Studio 2019](https://www.visualstudio.com/downloads/) -et a következő munkaterhelésekkel:
    * ASP.NET és webfejlesztés
    * Azure-fejlesztés
    * .NET asztali fejlesztés az SQL Server Express LocalDB vagy az [SQL Server 2017 Express kiadásának](https://www.microsoft.com/sql-server/sql-server-editions-express) használatával.

## <a name="add-a-leaderboard-to-the-project"></a>Ranglista hozzáadása a projekthez

Az oktatóanyag ezen szakaszában a *ContosoTeamStats* projektet fogja konfigurálni egy ranglistával, amely képzeletbeli csapatok győzelmeinek, vereségeinek és döntetlenjeinek statisztikáit tartalmazza.

### <a name="add-the-entity-framework-to-the-project"></a>Az Entity Framework hozzáadása a projekthez

1. A Visual Studióban nyissa meg a Redis-hez készült [Azure Cache ASP.net](cache-web-app-howto.md)-útmutatójában létrehozott *ContosoTeamStats* -megoldást.
2. Válassza az **Eszközök > NuGet-csomagkezelő > Csomagkezelő konzol** elemet.
3. Az EntityFramework telepítéséhez futtassa a következő parancsot a **Csomagkezelő konzol** ablakából:

    ```powershell
    Install-Package EntityFramework
    ```

A csomaggal kapcsolatos további információt az [EntityFramework](https://www.nuget.org/packages/EntityFramework/) NuGet-oldalon talál.

### <a name="add-the-team-model"></a>A csapatmodell hozzáadása

1. Kattintson a jobb gombbal a **Models** (Modellek) elemre a **Solution Explorer** (Megoldáskezelő) területén, és válassza az **Add** (Hozzáadás), **Class** (Osztály) lehetőségeket.

1. Az osztály neveként adja meg a `Team` nevet, majd kattintson az **Add** (Hozzáadás) gombra.

    ![Modellosztály hozzáadása](./media/cache-web-app-cache-aside-leaderboard/cache-model-add-class-dialog.png)

1. A *Team.cs* fájl elején cserélje le a `using` utasításokat az alábbi `using` utasításokra:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Data.Entity;
    using System.Data.Entity.SqlServer;
    ```

1. Cserélje le a `Team` osztály definícióját az alábbi kódrészlettel, amely a `Team` osztály frissített definícióját, valamint néhány további Entity Framework-súgóosztályt tartalmaz. Az oktatóanyag a Code First nevű Entity Framework-módszert használja. Ezzel a módszerrel az Entity Framework a kódból tudja létrehozni az adatbázist. További információk a jelen oktatóanyagban használt, Code First nevű Entity Framework-megközelítésról: [Code First alkalmazása egy új adatbázisra](/ef/ef6/modeling/code-first/workflows/new-database).

    ```csharp
    public class Team
    {
        public int ID { get; set; }
        public string Name { get; set; }
        public int Wins { get; set; }
        public int Losses { get; set; }
        public int Ties { get; set; }

        static public void PlayGames(IEnumerable<Team> teams)
        {
            // Simple random generation of statistics.
            Random r = new Random();

            foreach (var t in teams)
            {
                t.Wins = r.Next(33);
                t.Losses = r.Next(33);
                t.Ties = r.Next(0, 5);
            }
        }
    }

    public class TeamContext : DbContext
    {
        public TeamContext()
            : base("TeamContext")
        {
        }

        public DbSet<Team> Teams { get; set; }
    }

    public class TeamInitializer : CreateDatabaseIfNotExists<TeamContext>
    {
        protected override void Seed(TeamContext context)
        {
            var teams = new List<Team>
            {
                new Team{Name="Adventure Works Cycles"},
                new Team{Name="Alpine Ski House"},
                new Team{Name="Blue Yonder Airlines"},
                new Team{Name="Coho Vineyard"},
                new Team{Name="Contoso, Ltd."},
                new Team{Name="Fabrikam, Inc."},
                new Team{Name="Lucerne Publishing"},
                new Team{Name="Northwind Traders"},
                new Team{Name="Consolidated Messenger"},
                new Team{Name="Fourth Coffee"},
                new Team{Name="Graphic Design Institute"},
                new Team{Name="Nod Publishers"}
            };

            Team.PlayGames(teams);

            teams.ForEach(t => context.Teams.Add(t));
            context.SaveChanges();
        }
    }

    public class TeamConfiguration : DbConfiguration
    {
        public TeamConfiguration()
        {
            SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
        }
    }
    ```

1. A **Solution Explorer** (Megoldáskezelő) felületén kattintson duplán a **Web.config** fájlra annak megnyitásához.

    ![Web.config](./media/cache-web-app-cache-aside-leaderboard/cache-web-config.png)

1. A `configuration` szakaszon belül adja hozzá a következő `connectionStrings` szakaszt. A kapcsolati sztring nevének meg kell egyeznie az Entity Framework-adatbáziskörnyezet osztályának nevével, amely a következő: `TeamContext`.

    Ez a kapcsolati karakterlánc azt feltételezi, hogy teljesítette az [előfeltételeket](#prerequisites) , és telepítette SQL Server Express LocalDB, amely a Visual Studio 2019-mel telepített *.net Desktop-fejlesztési* számítási feladathoz tartozik.

    ```xml
    <connectionStrings>
        <add name="TeamContext" connectionString="Data Source=(LocalDB)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True" providerName="System.Data.SqlClient" />
    </connectionStrings>
    ```

    A következő példa bemutatja az új `connectionStrings` szakaszt, amely a `configSections` szakaszt követi a `configuration` szakaszban:

    ```xml
    <configuration>
        <configSections>
        <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
        </configSections>
        <connectionStrings>
        <add name="TeamContext" connectionString="Data Source=(LocalDB)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True"     providerName="System.Data.SqlClient" />
        </connectionStrings>
        ...
    ```

### <a name="add-the-teamscontroller-and-views"></a>A TeamsController és a nézetek hozzáadása

1. Hozza létre a projektet a Visual Studióban. 

1. A **Solution Explorerben** (Megoldáskezelőben) kattintson a jobb gombbal a **Controllers** (Vezérlők) mappára, majd válassza az **Add** (Hozzáadás), **Controller** (Vezérlő) lehetőségeket.

1. Válassza az **MVC 5 Controller with views, using Entity Framework** (MVC 5 vezérlő nézetekkel, az Entity Framework használatával) lehetőséget, majd kattintson az **Add** (Hozzáadás) lehetőségre. Ha az **Add** (Hozzáadás) gombra kattintva a rendszer hibaüzenetet küld, győződjön meg arról, hogy a projekt létrehozása előzetesen megtörtént.

    ![Vezérlőosztály hozzáadása](./media/cache-web-app-cache-aside-leaderboard/cache-add-controller-class.png)

1. Válassza ki a **Team (ContosoTeamStats.Models)** elemet a **Model class** (Modellosztály) legördülő listából. Válassza ki a **TeamContext (ContosoTeamStats.Models)** elemet a **Adatkörnyezet osztálya** (Adatkörnyezet osztálya) legördülő listából. Írja be a `TeamsController` szöveget a **Controller** (Vezérlő) névmezőbe (ha az nincs automatikusan kitöltve). Kattintson az **Add** (Hozzáadás) gombra a vezérlőosztály létrehozásához és az alapértelmezett nézetek hozzáadásához.

    ![Vezérlő konfigurálása](./media/cache-web-app-cache-aside-leaderboard/cache-configure-controller.png)

1. A **Solution Explorerben** (Megoldáskezelőben) bontsa ki a **Global.asax** elemet, majd kattintson duplán a **Global.asax.cs** fájlra annak megnyitásához.

    ![Global.asax.cs](./media/cache-web-app-cache-aside-leaderboard/cache-global-asax.png)

1. Adja hozzá a következő két `using`-utasítást a fájl elejéhez, a többi `using`-utasítás alá:

    ```csharp
    using System.Data.Entity;
    using ContosoTeamStats.Models;
    ```

1. Szúrja be az alábbi kódsort az `Application_Start` metódus végén:

    ```csharp
    Database.SetInitializer<TeamContext>(new TeamInitializer());
    ```

1. A **Solution Explorerben** (Megoldáskezelőben) bontsa ki az `App_Start` elemet, majd kattintson duplán a `RouteConfig.cs` elemre.

    ![RouteConfig.cs](./media/cache-web-app-cache-aside-leaderboard/cache-RouteConfig-cs.png)

1. A `RegisterRoutes` metódusban cserélje le a `controller = "Home"` értéket a `Default` útvonalban a `controller = "Teams"` értékre, a következő példában látható módon:

    ```csharp
    routes.MapRoute(
        name: "Default",
        url: "{controller}/{action}/{id}",
        defaults: new { controller = "Teams", action = "Index", id = UrlParameter.Optional }
    );
    ```

### <a name="configure-the-layout-view"></a>Az Elrendezés nézet konfigurálása

1. A **Solution Explorerben** (Megoldáskezelőben) bontsa ki a **Views**(Nézetek), majd a **Shared** (Közös) mappát, és kattintson duplán a **_Layout.cshtml** fájlra. 

    ![_Layout.cshtml](./media/cache-web-app-cache-aside-leaderboard/cache-layout-cshtml.png)

1. Az alábbi példában látható módon módosítsa a `title` elem tartalmát, majd cserélje le a `My ASP.NET Application` szöveget a `Contoso Team Stats` szövegre:

    ```html
    <title>@ViewBag.Title - Contoso Team Stats</title>
    ```

1. A `body` szakaszban adja hozzá az alábbi új `Html.ActionLink` utasítást a contoso- *csapat statisztikáit* közvetlenül az *Azure cache Redis-teszthez való* hivatkozás alatt.

    ```csharp
    @Html.ActionLink("Contoso Team Stats", "Index", "Teams", new { area = "" }, new { @class = "navbar-brand" })`
    ```

    ![Kódmódosítások](./media/cache-web-app-cache-aside-leaderboard/cache-layout-cshtml-code.png)

1. Az alkalmazás fordításához és futtatásához nyomja le a **Ctrl+F5** billentyűkombinációt. Az alkalmazás ezen verziója az eredményeket közvetlenül az adatbázisból olvassa ki. Figyelje meg, hogy az **Új létrehozása**, a **Szerkesztés**, a **Részletek** és a **Törlés** parancsok az **MVC 5 Controller with views, using Entity Framework** (MVC 5 vezérlő nézetekkel, az Entity Framework használatával) szerkezettel automatikusan bekerültek az alkalmazásba. Az oktatóanyag következő szakaszában hozzáadja az Azure cache-t a Redis-hez az adathozzáférés optimalizálása érdekében, és további funkciókat biztosít az alkalmazás számára.

    ![Kezdő szintű alkalmazás](./media/cache-web-app-cache-aside-leaderboard/cache-starter-application.png)

## <a name="configure-the-app-for-azure-cache-for-redis"></a>Az Azure cache Redis-alkalmazás konfigurálása

Az oktatóanyag ezen szakaszában úgy konfigurálja a minta alkalmazást, hogy a Redis-példányhoz tartozó contoso-csapat statisztikáit a [StackExchange. Redis](https://github.com/StackExchange/StackExchange.Redis) cache-ügyféllel tárolja és beolvassa egy Azure cache-ből.

### <a name="add-a-cache-connection-to-the-teams-controller"></a>Gyorsítótár-kapcsolat hozzáadása a Teams Controllerhez

A gyors útmutató során már telepítette a *StackExchange.Redis* ügyféloldali kódtárcsomagját. Emellett már konfigurálta a *CacheConnection* alkalmazásbeállítást a helyi, illetve a közzétett App Service-szel való használatra. Használja ugyanazon ügyféloldali kódtár és *CacheConnection* információit a *TeamsControllerben*.

1. A **Solution Explorerben** (Megoldáskezelőben) bontsa ki a **Controllers** (Vezérlők) mappát, majd kattintson duplán a **TeamsController.cs** fájlra annak megnyitásához.

    ![Csoportvezérlő](./media/cache-web-app-cache-aside-leaderboard/cache-teamscontroller.png)

1. Adja hozzá a következő két `using` utasítást a **TeamsController. cs**:

    ```csharp
    using System.Configuration;
    using StackExchange.Redis;
    ```

1. Adja hozzá az alábbi két tulajdonságot a `TeamsController` osztályhoz:

    ```csharp
    // Redis Connection string info
    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
        return ConnectionMultiplexer.Connect(cacheConnection);
    });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }
    ```

### <a name="update-the-teamscontroller-to-read-from-the-cache-or-the-database"></a>A TeamsController frissítése a gyorsítótárból vagy az adatbázisból történő beolvasáshoz

Jelen példában a csapatstatisztikák az adatbázisból vagy a gyorsítótárból is lekérdezhetők. A csapatstatisztikák a gyorsítótárban szerializált `List<Team>`, illetve (Redis adattípusok használatával) rendezett készlet formájában vannak tárolva. Rendezett készletből történő lekérdezéskor egyes, az összes vagy bizonyos feltételnek megfelelő elemek lekérésére van lehetőség. Ebben a mintában a győzelmek száma szerint rangsorolt 5 legjobb csapatot kérdezzük le egy rendezett készletből.

A csapat statisztikáit nem szükséges a gyorsítótárban több formátumban tárolni ahhoz, hogy használni lehessen az Azure cache-t a Redis. Ez az oktatóanyag többféle formátumot használ az adatok gyorsítótárazásához használható különböző módszerek és adattípusok példáinak bemutatására.

1. Adja hozzá az alábbi `using`-utasításokat a `TeamsController.cs` fájl elejéhez, a többi `using`-utasítással együtt:

    ```csharp
    using System.Diagnostics;
    using Newtonsoft.Json;
    ```

1. Az aktuális `public ActionResult Index()` metódusimplementációt cserélje le az alábbi implementációra:

    ```csharp
    // GET: Teams
    public ActionResult Index(string actionType, string resultType)
    {
        List<Team> teams = null;

        switch(actionType)
        {
            case "playGames": // Play a new season of games.
                PlayGames();
                break;

            case "clearCache": // Clear the results from the cache.
                ClearCachedTeams();
                break;

            case "rebuildDB": // Rebuild the database with sample data.
                RebuildDB();
                break;
        }

        // Measure the time it takes to retrieve the results.
        Stopwatch sw = Stopwatch.StartNew();

        switch(resultType)
        {
            case "teamsSortedSet": // Retrieve teams from sorted set.
                teams = GetFromSortedSet();
                break;

            case "teamsSortedSetTop5": // Retrieve the top 5 teams from the sorted set.
                teams = GetFromSortedSetTop5();
                break;

            case "teamsList": // Retrieve teams from the cached List<Team>.
                teams = GetFromList();
                break;

            case "fromDB": // Retrieve results from the database.
            default:
                teams = GetFromDB();
                break;
        }

        sw.Stop();
        double ms = sw.ElapsedTicks / (Stopwatch.Frequency / (1000.0));

        // Add the elapsed time of the operation to the ViewBag.msg.
        ViewBag.msg += " MS: " + ms.ToString();

        return View(teams);
    }
    ```

1. Vegye fel az alábbi három módszert a `TeamsController` osztályba azon `playGames`, `clearCache`, és `rebuildDB` művelettípusok megvalósításához, amelyek az előző kódrészletben hozzáadott „switch” utasításból származnak.

    Egy játékszezon szimulálásával a `PlayGames` módszer frissíti a csapatstatisztikákat, az eredményeket elmenti az adatbázisba, majd törli a gyorsítótárból a már elavult adatokat.

    ```csharp
    void PlayGames()
    {
        ViewBag.msg += "Updating team statistics. ";
        // Play a "season" of games.
        var teams = from t in db.Teams
                    select t;

        Team.PlayGames(teams);

        db.SaveChanges();

        // Clear any cached results
        ClearCachedTeams();
    }
    ```

    A `RebuildDB` módszer újrainicializálja az adatbázist az alapértelmezett csapatokkal, statisztikákat állít elő számukra, és törli a gyorsítótárból a már elavult adatokat.

    ```csharp
    void RebuildDB()
    {
        ViewBag.msg += "Rebuilding DB. ";
        // Delete and re-initialize the database with sample data.
        db.Database.Delete();
        db.Database.Initialize(true);

        // Clear any cached results
        ClearCachedTeams();
    }
    ```

    A `ClearCachedTeams` módszer eltávolítja a gyorsítótárazott csapatstatisztikákat a gyorsítótárból.

    ```csharp
    void ClearCachedTeams()
    {
        IDatabase cache = Connection.GetDatabase();
        cache.KeyDelete("teamsList");
        cache.KeyDelete("teamsSortedSet");
        ViewBag.msg += "Team data removed from cache. ";
    }
    ```

1. Vegye fel az alábbi négy módszert a `TeamsController` osztályba a csapatstatisztikák gyorsítótárból és adatbázisból különböző módszerekkel történő lekérdezéséhez. Mindegyik metódus egy `List<Team>` választ ad vissza, amely ezután megjelenik a nézetben.

    A `GetFromDB` módszer beolvassa a csapatstatisztikákat a gyorsítótárból.
    ```csharp
    List<Team> GetFromDB()
    {
        ViewBag.msg += "Results read from DB. ";
        var results = from t in db.Teams
            orderby t.Wins descending
            select t;

        return results.ToList<Team>();
    }
    ```

    A `GetFromList` módszer szerializált `List<Team>` formájában olvassa be a csapatstatisztikákat a gyorsítótárból. Ha a statisztikák nem jelennek meg a gyorsítótárban, akkor gyorsítótár-tévesztés történik. Gyorsítótár-tévesztés esetén a rendszer az adatbázisból olvassa be a statisztikákat, és a gyorsítótárba menti őket a következő kérés számára. Ebben a mintában JSON.NET-szerializálást használunk a .NET-objektumok gyorsítótárba és gyorsítótárból történő szerializálására. További információkért lásd: [.net-objektumok használata az Azure cache-ben a Redis-hez](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

    ```csharp
    List<Team> GetFromList()
    {
        List<Team> teams = null;

        IDatabase cache = Connection.GetDatabase();
        string serializedTeams = cache.StringGet("teamsList");
        if (!String.IsNullOrEmpty(serializedTeams))
        {
            teams = JsonConvert.DeserializeObject<List<Team>>(serializedTeams);

            ViewBag.msg += "List read from cache. ";
        }
        else
        {
            ViewBag.msg += "Teams list cache miss. ";
            // Get from database and store in cache
            teams = GetFromDB();

            ViewBag.msg += "Storing results to cache. ";
            cache.StringSet("teamsList", JsonConvert.SerializeObject(teams));
        }
        return teams;
    }
    ```

    A `GetFromSortedSet` módszer beolvassa a csapatstatisztikákat egy gyorsítótárazott rendezett készletből. Gyorsítótár-tévesztés esetén a rendszer az adatbázisból olvassa be a statisztikákat, és azokat a gyorsítótárba menti, rendezett készletként.

    ```csharp
    List<Team> GetFromSortedSet()
    {
        List<Team> teams = null;
        IDatabase cache = Connection.GetDatabase();
        // If the key teamsSortedSet is not present, this method returns a 0 length collection.
        var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", order: Order.Descending);
        if (teamsSortedSet.Count() > 0)
        {
            ViewBag.msg += "Reading sorted set from cache. ";
            teams = new List<Team>();
            foreach (var t in teamsSortedSet)
            {
                Team tt = JsonConvert.DeserializeObject<Team>(t.Element);
                teams.Add(tt);
            }
        }
        else
        {
            ViewBag.msg += "Teams sorted set cache miss. ";

            // Read from DB
            teams = GetFromDB();

            ViewBag.msg += "Storing results to cache. ";
            foreach (var t in teams)
            {
                Console.WriteLine("Adding to sorted set: {0} - {1}", t.Name, t.Wins);
                cache.SortedSetAdd("teamsSortedSet", JsonConvert.SerializeObject(t), t.Wins);
            }
        }
        return teams;
    }
    ```

    A `GetFromSortedSetTop5` metódus beolvassa az öt legjobb csapatot a gyorsítótárazott rendezett készletből. Első lépésben a `teamsSortedSet` kulcsot keresi meg a gyorsítótárban. Ha a kulcs nem található, a rendszer a `GetFromSortedSet` módszert hívja meg a csapatstatisztikák beolvasásához és azoknak a gyorsítótárban történő tárolásához. Ezt a gyorsítótárazott rendezett készlet lekérdezése követi, amely az öt legjobb csapatot adja vissza.

    ```csharp
    List<Team> GetFromSortedSetTop5()
    {
        List<Team> teams = null;
        IDatabase cache = Connection.GetDatabase();

        // If the key teamsSortedSet is not present, this method returns a 0 length collection.
        var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
        if(teamsSortedSet.Count() == 0)
        {
            // Load the entire sorted set into the cache.
            GetFromSortedSet();

            // Retrieve the top 5 teams.
            teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
        }

        ViewBag.msg += "Retrieving top 5 teams from cache. ";
        // Get the top 5 teams from the sorted set
        teams = new List<Team>();
        foreach (var team in teamsSortedSet)
        {
            teams.Add(JsonConvert.DeserializeObject<Team>(team.Element));
        }
        return teams;
    }
    ```

### <a name="update-the-create-edit-and-delete-methods-to-work-with-the-cache"></a>A Létrehozás, Szerkesztés és Törlés módszerek frissítése a gyorsítótárral való együttműködéshez

A szerkezeti kódot a rendszer ezen minta részeként állítja elő a csapatok hozzáadásához, szerkesztéséhez és törléséhez. Egy csapat hozzáadását, szerkesztését vagy eltávolítását követően a gyorsítótárban található adatok elavulttá válnak. Ebben a szakaszban ezt a három metódust fogja módosítani a gyorsítótárazott csapatok törlése érdekében, hogy a gyorsítótár frissüljön.

1. Keresse meg a `Create(Team team)` módszert a `TeamsController` osztályban. Adja hozzá a `ClearCachedTeams` metódus hívását, ahogy az az alábbi példában is látható:

    ```csharp
    // POST: Teams/Create
    // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
    // more details see https://go.microsoft.com/fwlink/?LinkId=317598.
    [HttpPost]
    [ValidateAntiForgeryToken]
    public ActionResult Create([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
    {
        if (ModelState.IsValid)
        {
            db.Teams.Add(team);
            db.SaveChanges();
            // When a team is added, the cache is out of date.
            // Clear the cached teams.
            ClearCachedTeams();
            return RedirectToAction("Index");
        }

        return View(team);
    }
    ```

2. Keresse meg a `Edit(Team team)` módszert a `TeamsController` osztályban. Adja hozzá a `ClearCachedTeams` metódus hívását, ahogy az az alábbi példában is látható:

    ```csharp
    // POST: Teams/Edit/5
    // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
    // more details see https://go.microsoft.com/fwlink/?LinkId=317598.
    [HttpPost]
    [ValidateAntiForgeryToken]
    public ActionResult Edit([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
    {
        if (ModelState.IsValid)
        {
            db.Entry(team).State = EntityState.Modified;
            db.SaveChanges();
            // When a team is edited, the cache is out of date.
            // Clear the cached teams.
            ClearCachedTeams();
            return RedirectToAction("Index");
        }
        return View(team);
    }
    ```

3. Keresse meg a `DeleteConfirmed(int id)` módszert a `TeamsController` osztályban. Adja hozzá a `ClearCachedTeams` metódus hívását, ahogy az az alábbi példában is látható:

    ```csharp
    // POST: Teams/Delete/5
    [HttpPost, ActionName("Delete")]
    [ValidateAntiForgeryToken]
    public ActionResult DeleteConfirmed(int id)
    {
        Team team = db.Teams.Find(id);
        db.Teams.Remove(team);
        db.SaveChanges();
        // When a team is deleted, the cache is out of date.
        // Clear the cached teams.
        ClearCachedTeams();
        return RedirectToAction("Index");
    }
    ```

### <a name="add-caching-methods-to-the-teams-index-view"></a>Gyorsítótárazási metódusok hozzáadása a Teams Index nézethez

1. A **Solution Explorer** (Megoldáskezelőben) bontsa ki a **Views** (Nézetek), majd a **Teams** (Csapatok) mappát, és kattintson duplán az **Index.cshtml** fájlra.

    ![Index.cshtml](./media/cache-web-app-cache-aside-leaderboard/cache-views-teams-index-cshtml.png)

1. A fájl elején keresse meg az alábbi bekezdéselemet:

    ![Művelettábla](./media/cache-web-app-cache-aside-leaderboard/cache-teams-index-table.png)

    Ez a hivatkozás egy új csapatot hoz létre. Cserélje le a bekezdéselemet az alábbi táblával. Ez a tábla műveleti hivatkozásokkal rendelkezik egy új csapat létrehozása, egy új játékszezon lejátszása, a gyorsítótár kiürítése, a csapatok gyorsítótárból, több formátumban történő lekérdezése, a csapatok adatbázisból történő lekérdezése, valamint az adatbázis friss mintaadatokkal való újraépítése céljából.

    ```html
    <table class="table">
        <tr>
            <td>
                @Html.ActionLink("Create New", "Create")
            </td>
            <td>
                @Html.ActionLink("Play Season", "Index", new { actionType = "playGames" })
            </td>
            <td>
                @Html.ActionLink("Clear Cache", "Index", new { actionType = "clearCache" })
            </td>
            <td>
                @Html.ActionLink("List from Cache", "Index", new { resultType = "teamsList" })
            </td>
            <td>
                @Html.ActionLink("Sorted Set from Cache", "Index", new { resultType = "teamsSortedSet" })
            </td>
            <td>
                @Html.ActionLink("Top 5 Teams from Cache", "Index", new { resultType = "teamsSortedSetTop5" })
            </td>
            <td>
                @Html.ActionLink("Load from DB", "Index", new { resultType = "fromDB" })
            </td>
            <td>
                @Html.ActionLink("Rebuild DB", "Index", new { actionType = "rebuildDB" })
            </td>
        </tr>
    </table>
    ```

1. Görgessen az **index. cshtml** fájl aljához, és adja hozzá a következő `tr` elemet, hogy az a fájl utolsó táblájának utolsó sora legyen:

    ```html
    <tr><td colspan="5">@ViewBag.Msg</td></tr>
    ```
    Ez a sor a `ViewBag.Msg` értékét jeleníti meg, amely az aktuális művelet állapotjelentését tartalmazza. A `ViewBag.Msg` beállítása az előző lépés egyik műveleti hivatkozására kattintva történhet.

    ![Állapotüzenet](./media/cache-web-app-cache-aside-leaderboard/cache-status-message.png)

1. A projekt létrehozásához nyomja le az **F6** billentyűt.

## <a name="run-the-app-locally"></a>Az alkalmazás futtatása helyben

Futtassa az alkalmazást a helyi számítógépen a csapatok támogatása érdekében hozzáadott funkció ellenőrzéséhez.

Ebben a tesztben az alkalmazás és az adatbázis is helyben fut. A Redis Azure cache-t azonban távolról az Azure-ban üzemeltetjük. Ezért valószínű, hogy a gyorsítótár teljesítménye az adatbázisénál kisebb lesz. A legjobb teljesítmény érdekében az ügyfélalkalmazás és az Azure cache for Redis-példánynak ugyanazon a helyen kell lennie. A következő szakaszban a gyorsítótár használatával járó nagyobb teljesítmény kipróbálása érdekében minden erőforrást az Azure-ban fog üzembe helyezni.

Az alkalmazás helyi futtatása:

1. Az alkalmazás futtatásához nyomja le a **Ctrl+F5** billentyűkombinációt.

    ![Helyileg futó alkalmazás](./media/cache-web-app-cache-aside-leaderboard/cache-local-application.png)

1. Tesztelje a nézethez hozzáadott összes új metódust. Mivel a gyorsítótár ezen tesztek során távolról fut, az adatbázisnak a gyorsítótárnál némileg nagyobb teljesítménnyel kell rendelkeznie.

## <a name="publish-and-run-in-azure"></a>Közzététel és futtatás az Azure-ban

### <a name="provision-a-database-for-the-app"></a>Adatbázis kiépítése az alkalmazáshoz

Ebben a szakaszban egy új adatbázist fog kiépíteni a SQL Databaseban, hogy az alkalmazás az Azure-ban üzemeltethető legyen.

1. Az [Azure Portalon](https://portal.azure.com/) kattintson a portál bal felső sarkában található **Erőforrás létrehozása** elemre.

1. Az **Új** lapon kattintson az **Adatbázisok** > **SQL Database** elemre.

1. Használja a következő beállításokat az új SQL Database-adatbázishoz:

   | Beállítás       | Ajánlott érték | Leírás |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Adatbázis neve** | *ContosoTeamsDatabase* | Az érvényes adatbázisnevekkel kapcsolatban lásd az [adatbázis-azonosítókat](/sql/relational-databases/databases/database-identifiers) ismertető cikket. |
   | **Előfizetés** | *Az előfizetése*  | Válassza ki ugyanazt az előfizetést, amelyet a gyorsítótár létrehozásához és az App Service futtatásához használt. |
   | **Erőforráscsoport**  | *TestResourceGroup* | Kattintson a **Meglévő használata** elemre, és használja ugyanazt az erőforráscsoportot, amelyen a gyorsítótárat és az App Service-t elhelyezte. |
   | **Forrás kiválasztása** | **Üres adatbázis** | Kezdje egy üres adatbázissal. |

1. A **Kiszolgáló** területen kattintson a **Kötelező beállítások konfigurálása** > **Új kiszolgáló létrehozása** elemre, és adja meg az alábbi információkat, majd kattintson a **Kiválasztás** gombra:

   | Beállítás       | Ajánlott érték | Leírás |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Kiszolgáló neve** | Bármely globálisan egyedi név | Az érvényes kiszolgálónevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/resource-naming) ismertető cikket. |
   | **Kiszolgáló rendszergazdájának felhasználóneve** | Bármely érvényes név | Az érvényes bejelentkezési nevekkel kapcsolatban lásd az [adatbázis-azonosítókat](/sql/relational-databases/databases/database-identifiers) ismertető cikket. |
   | **Jelszó** | Bármely érvényes jelszó | A jelszónak legalább 8 karakter hosszúságúnak kell lennie, és tartalmaznia kell karaktereket a következő kategóriák közül legalább háromból: nagybetűs karakterek, kisbetűs karakterek, számjegyek és nem alfanumerikus karakterek. |
   | **Hely** | *USA keleti régiója* | Válassza ki ugyanazt a régiót, amelyben a gyorsítótárat és az App Service-t létrehozta. |

1. Kattintson a **Rögzítés az irányítópulton** elemre, majd a **Létrehozás** gombra az új adatbázis és kiszolgáló létrehozásához.

1. Ha az új adatbázis létrejött, kattintson az **Adatbázis kapcsolati sztringjeinek megjelenítése** elemre, és másolja az **ADO.NET** kapcsolati sztringet.

    ![Kapcsolati sztringek megjelenítése](./media/cache-web-app-cache-aside-leaderboard/cache-show-connection-strings.png)

1. Az Azure Portalon navigáljon az App Service-hez, és kattintson az **Alkalmazásbeállítások**, majd az **Új kapcsolati sztring hozzáadása** elemre a Kapcsolati sztringek szakaszban.

1. Adjon hozzá egy *TeamContext* nevű kapcsolati sztringet, amely megegyezik az Entity Framework-adatbáziskörnyezet osztályának nevével. Illessze be az új adatbázis kapcsolati sztringjét az értékként. Cserélje le az alábbi helyőrzőket a kapcsolati sztringben, majd kattintson a **Mentés** elemre:

    | Helyőrző | Ajánlott érték |
    | --- | --- |
    | *{saját_felhasználónév}* | Használja az imént létrehozott kiszolgáló **rendszergazdai bejelentkezési adatait** . |
    | *{saját_jelszó}* | Használja az imént létrehozott kiszolgáló jelszavát. |

    A felhasználónév és a jelszó alkalmazásbeállításként való hozzáadásával a felhasználónév és a jelszó nem szerepel a kódban. Ez a módszer segíti a hitelesítő adatok védelmét.

### <a name="publish-the-application-updates-to-azure"></a>Alkalmazásfrissítések közzététele az Azure-ban

Az oktatóanyag ezen lépésében közzéteszi az alkalmazás frissítéseit az Azure-ban, majd futtatja az alkalmazást a felhőben.

1. Kattintson a jobb gombbal a Visual Studio **ContosoTeamStats** projektjére, majd válassza a **Publish** (Közzététel) lehetőséget

    ![Közzététel](./media/cache-web-app-cache-aside-leaderboard/cache-publish-app.png)

2. Kattintson a **Közzététel** elemre, ha ugyanazt a közzétételi profilt kívánja használni, amelyet a rövid útmutatóban létrehozott.

3. Ha a közzététel befejeződött, a Visual Studio elindítja az alkalmazást az alapértelmezett webböngészőben.

    ![Gyorsítótár hozzáadva](./media/cache-web-app-cache-aside-leaderboard/cache-added-to-application.png)

    A mintaalkalmazás egyes műveleti hivatkozásait a következő táblázat ismerteti:

    | Művelet | Leírás |
    | --- | --- |
    | Új létrehozása |Létrehoz egy új csapatot. |
    | Szezon végigjátszása |Végigjátszik egy szezont, frissíti a csapatstatisztikákat, és törli a gyorsítótárból az elavult adatokat. |
    | Gyorsítótár ürítése |Törli a csapatstatisztikákat a gyorsítótárból. |
    | Lista a gyorsítótárból |Lekérdezi a csapatstatisztikákat a gyorsítótárból. Gyorsítótár-tévesztés esetén az adatbázisból tölti be a statisztikákat, és menti azt a gyorsítótárba a következő alkalomra. |
    | Rendezett készlet a gyorsítótárból |Lekérdezi a csapatstatisztikákat a gyorsítótárból egy rendezett készlet használatával. Gyorsítótár-tévesztés esetén az adatbázisból tölti be a statisztikákat, és menti azt a gyorsítótárba egy rendezett készlet használatával. |
    | Az 5 legjobb csapat a gyorsítótárból |Lekérdezi az 5 legjobb csapatot a gyorsítótárból egy rendezett készlet használatával. Gyorsítótár-tévesztés esetén az adatbázisból tölti be a statisztikákat, és menti azt a gyorsítótárba egy rendezett készlet használatával. |
    | Betöltés adatbázisból |Lekérdezi a csapatstatisztikákat az adatbázisból. |
    | Adatbázis újraépítése |Újraépíti az adatbázist, és ismét feltölti azt minta-csapatadatokkal. |
    | Szerkesztés / Részletek / Törlés |Szerkeszthet egy csapatot, megtekintheti annak részletes adatait, törölhet egy csapatot. |

Kattintson néhány műveletre, és kísérletezzen az adatok különböző forrásokból történő lekérdezésével. Figyelje meg az adatbázisból és a gyorsítótárból történő adatlekérdezés különböző módjainak végrehajtásához szükséges időbeli eltéréseket.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha befejezte az oktatóanyag mintaalkalmazásának használatát, a költség- és erőforrás-takarékosság érdekében törölheti az ott használt Azure-erőforrásokat. Minden erőforrásnak azonos erőforráscsoportban kell szerepelnie, és az erőforráscsoport törlésével egy művelettel, együttesen is törölheti őket. Ebben a témakörben a *TestResources* nevű erőforráscsoportot használtuk.

> [!IMPORTANT]
> Az erőforráscsoport törlése nem visszaállítható; az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen arra, hogy ne töröljön véletlenül erőforráscsoportot vagy erőforrásokat. Ha a jelen mintát üzemeltető erőforrásokat egy meglévő, megtartani kívánt erőforrásokat tartalmazó erőforráscsoportban hozta létre, külön-külön törölheti az erőforrásokat a megfelelő panelekről.
>

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com), és kattintson az **Erőforráscsoportok** elemre.
2. Írja be az erőforráscsoport nevét az **Elemek szűrése...** szövegmezőbe.
3. Kattintson az erőforráscsoporttól jobbra lévő **...** elemre, majd kattintson az **Erőforráscsoport törlése** parancsra.

    ![Törlés](./media/cache-web-app-cache-aside-leaderboard/cache-delete-resource-group.png)

4. A rendszer az erőforráscsoport törlésének megerősítését fogja kérni. A megerősítéshez írja be az erőforráscsoport nevét, és kattintson a **Törlés** gombra.

    A rendszer néhány pillanaton belül törli az erőforráscsoportot és a benne foglalt erőforrásokat.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az Azure cache méretezése a Redis](./cache-how-to-scale.md)