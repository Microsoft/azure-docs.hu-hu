---
title: Több-bérlős alkalmazások RLS-vel és rugalmas adatbázis-eszközökkel
description: Rugalmasan méretezhető adatbázis-eszközöket használhat a soros szintű biztonsággal, hogy magas skálázható adatréteggel hozzon létre egy alkalmazást.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: 6d753a90f2a4cb19c9f3933d007fb3d378af6d81
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92793211"
---
# <a name="multi-tenant-applications-with-elastic-database-tools-and-row-level-security"></a>Több-bérlős alkalmazások rugalmas adatbázis-eszközökkel és sor szintű biztonsággal
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A [rugalmas adatbázis-eszközök](elastic-scale-get-started.md) és a [sorok szintjének biztonsága (RLS)][rls] együttműködve lehetővé teszi egy több-bérlős alkalmazás adatrétegének méretezését Azure SQL Database használatával. Ezek a technológiák együttesen méretezhető adatcsomaggal rendelkező alkalmazások készítését segítik elő. Az adatréteg támogatja a több-bérlős szegmenseket, és **ADO.net SqlClient** vagy **Entity Framework** használ. További információ: [tervezési minták több-bérlős SaaS-alkalmazásokhoz Azure SQL Database](./saas-tenancy-app-design-patterns.md).

- A **rugalmas adatbázis-eszközök** lehetővé teszik a fejlesztők számára, hogy a .net-kódtárak és az Azure-szolgáltatási sablonok használatával horizontálisan horizontálisan bővítsék az adatszintet. A szegmensek [Elastic Database ügyféloldali kódtár][s-d-elastic-database-client-library] használatával történő kezelése segít automatizálni és egyszerűsíteni a horizontális felskálázáshoz jellemzően kapcsolódó infrastrukturális feladatok nagy részét.
- A **sor szintű biztonság** lehetővé teszi a fejlesztők számára, hogy ugyanabban az adatbázisban több bérlő számára is biztonságosan tárolják az adattárolást. Az RLS biztonsági házirendjei kiszűrik azokat a sorokat, amelyek nem tartoznak a lekérdezést végrehajtó bérlőhöz. Az adatbázison belüli szűrési logika egyszerűbbé teszi a karbantartást, és csökkenti a biztonsági hibák kockázatát. A biztonság kikényszeríthető az összes ügyfél kódjára támaszkodva kockázatos.

Ezeknek a szolgáltatásoknak a használatával az alkalmazások több bérlő számára is tárolhatnak egy adott szegmens adatbázisban található több bérlőt. Bérlők számára kevesebb költséggel jár, ha a bérlők megosztanak egy adatbázist. Ugyanakkor Ugyanez az alkalmazás prémium bérlők számára is biztosíthatja a saját dedikált egybérlős szegmensek kifizetését. Az egybérlős elkülönítés egyik előnye, hogy a teljesítmény garantálja a teljesítményt. Egy egybérlős adatbázisban nincs más, az erőforrások számára versengő bérlő.

A cél az, hogy a rugalmas adatbázis ügyféloldali függvénytárának [adat-függő útválasztási](elastic-scale-data-dependent-routing.md) API-jait használja az egyes bérlők megfelelő szegmens-adatbázishoz való automatikus összekapcsolásához. A megadott bérlőhöz csak egy szegmens adott TenantId értéket tartalmaz. A TenantId a horizontális Felskálázási *kulcs*. A kapcsolat létrejötte után az adatbázison belüli RLS biztonsági házirend biztosítja, hogy a megadott bérlő csak azokat az adatsorokat tudja elérni, amelyek tartalmazzák a TenantId.

> [!NOTE]
> A bérlő azonosítója több oszlopból is állhat. A kényelmes használat érdekében ez a vita nem hivatalosan egyoszlopos TenantId feltételezi.

![A blogírás alkalmazás architektúrája][1]

## <a name="download-the-sample-project"></a>A minta projekt letöltése

### <a name="prerequisites"></a>Előfeltételek

- A Visual Studio használata (2012 vagy újabb)
- Három adatbázis létrehozása Azure SQL Database
- Minta projekt letöltése: [rugalmas adatbázis-eszközök az Azure SQL-több-bérlős szegmensekhez](https://go.microsoft.com/?linkid=9888163)
  - Adja meg az adatbázisainak adatait a **program elején. cs**

Ez a projekt a több-bérlős szegmensek adatbázisainak támogatásával bővíti az [Azure SQL-Entity Framework integrációjának rugalmas adatbázis-eszközeiben](elastic-scale-use-entity-framework-applications-visual-studio.md) leírt lépéseket. A projekt létrehoz egy egyszerű konzolos alkalmazást a blogok és a bejegyzések létrehozásához. A projekt négy bérlőt, valamint két több-bérlős szegmens adatbázist tartalmaz. Ez a konfiguráció az előző ábrán látható.

Hozza létre és futtassa az alkalmazást. Ez a Futtatás beindítja a rugalmas adatbázis-eszközöket a szegmens Map Managerben, és a következő teszteket hajtja végre:

1. A Entity Framework és a LINQ használatával hozzon létre egy új blogot, majd jelenítse meg az egyes bérlők összes blogját.
2. A ADO.NET SqlClient használata a bérlő összes blogjának megjelenítése
3. Próbáljon meg beszúrni egy blogot a rossz bérlőhöz annak ellenőrzéséhez, hogy hiba történt-e

Figyelje meg, hogy mivel az RLS még nincs engedélyezve a szegmens adatbázisaiban, ezek a tesztek egy problémát észlelnek: a bérlők megtekinthetik a hozzájuk tartozó blogokat, és az alkalmazás nem akadályozza meg, hogy nem szúr be blogot a helytelen bérlőhöz. A cikk további része azt ismerteti, Hogyan oldhatók meg ezek a problémák a bérlői elkülönítés RLS-vel való kikényszerítésével. Két lépésből áll:

1. **Alkalmazási** szinten: módosítsa az alkalmazás kódját, hogy a kapcsolat megnyitása után mindig az aktuális TenantId állítsa be a munkamenet \_ környezetében. A minta projekt már ezt a módszert állítja be a TenantId.
2. **Adatcsomag**: hozzon létre egy RLS biztonsági szabályzatot minden egyes szegmens adatbázisban a sorok szűréséhez a munkamenet-környezetben tárolt TenantId alapján \_ . Hozzon létre egy szabályzatot az egyes szegmens adatbázisok számára, máskülönben a több-bérlős szegmensekben lévő sorok nem lesznek szűrve.

## <a name="1-application-tier-set-tenantid-in-the-session_context"></a>1. alkalmazási szintű: TenantId beállítása a munkamenet \_ kontextusában

Először egy szegmens adatbázishoz kell csatlakoznia a rugalmas adatbázis ügyféloldali függvénytárának Adatfüggő útválasztási API-jai használatával. Az alkalmazásnak továbbra is meg kell adnia azt az adatbázist, amelyet a TenantId használ. A TenantId azt jelzi, hogy az RLS biztonsági házirendje alapján a többi bérlőhöz tartozó sorokat ki kell szűrni. Az aktuális TenantId a kapcsolat [munkameneti \_ környezetében](/sql/t-sql/functions/session-context-transact-sql) tárolja.

A munkamenet-környezet alternatívája a \_ [környezeti \_ adatok](/sql/t-sql/functions/context-info-transact-sql)használata. A munkamenet- \_ környezet azonban jobb megoldás. A munkamenet- \_ környezet könnyebben használható, alapértelmezés szerint null értéket ad vissza, és támogatja a kulcs-érték párokat.

### <a name="entity-framework"></a>Entity Framework

Entity Framework használó alkalmazások esetén a legegyszerűbb módszer a munkamenet-környezet beállítása a \_ ElasticScaleContext felülbírálásban az [EF DbContext használatával](elastic-scale-use-entity-framework-applications-visual-studio.md#data-dependent-routing-using-ef-dbcontext). Hozzon létre és hajtson végre egy olyan SqlCommand, amely TenantId állít be a munkamenet- \_ környezetben a kapcsolathoz megadott shardingKey. Ezután adja vissza az Adatfüggő útválasztással felügyelt kapcsolatokat. Így csak egyszer kell kódot írnia a munkamenet-környezet beállításához \_ .

```csharp
// ElasticScaleContext.cs
// Constructor for data-dependent routing.
// This call opens a validated connection that is routed to the
// proper shard by the shard map manager.
// Note that the base class constructor call fails for an open connection
// if migrations need to be done and SQL credentials are used.
// This is the reason for the separation of constructors.
// ...
public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
    : base(
        OpenDDRConnection(shardMap, shardingKey, connectionStr),
        true)  // contextOwnsConnection
{
}

public static SqlConnection OpenDDRConnection(
    ShardMap shardMap,
    T shardingKey,
    string connectionStr)
{
    // No initialization.
    Database.SetInitializer<ElasticScaleContext<T>>(null);

    // Ask shard map to broker a validated connection for the given key.
    SqlConnection conn = null;
    try
    {
        conn = shardMap.OpenConnectionForKey(
            shardingKey,
            connectionStr,
            ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey
        // to enable Row-Level Security filtering.
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText =
            @"exec sp_set_session_context
                @key=N'TenantId', @value=@shardingKey";
        cmd.Parameters.AddWithValue("@shardingKey", shardingKey);
        cmd.ExecuteNonQuery();

        return conn;
    }
    catch (Exception)
    {
        if (conn != null)
        {
            conn.Dispose();
        }
        throw;
    }
}
// ...
```

A munkamenet- \_ környezet most automatikusan a megadott TenantId van beállítva, amikor a ElasticScaleContext meghívja:

```csharp
// Program.cs
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (var db = new ElasticScaleContext<int>(
        sharding.ShardMap, tenantId, connStrBldr.ConnectionString))
    {
        var query = from b in db.Blogs
                    orderby b.Name
                    select b;

        Console.WriteLine("All blogs for TenantId {0}:", tenantId);
        foreach (var item in query)
        {
            Console.WriteLine(item.Name);
        }
    }
});
```

### <a name="adonet-sqlclient"></a>ADO.NET SqlClient

A ADO.NET SqlClient használó alkalmazások esetében hozzon létre egy burkoló függvényt a következő metódusban: ShardMap. OpenConnectionForKey. A burkoló automatikusan beállítja a TenantId a munkamenet- \_ kontextusban az aktuális TenantId, mielőtt visszaadná a kapcsolatot. Annak érdekében, hogy a munkamenet \_ -környezet mindig be legyen állítva, csak a jelen burkoló függvénnyel nyissa meg a kapcsolatokat.

```csharp
// Program.cs
// Wrapper function for ShardMap.OpenConnectionForKey() that
// automatically sets SESSION_CONTEXT with the correct
// tenantId before returning a connection.
// As a best practice, you should only open connections using this method
// to ensure that SESSION_CONTEXT is always set before executing a query.
// ...
public static SqlConnection OpenConnectionForTenant(
    ShardMap shardMap, int tenantId, string connectionStr)
{
    SqlConnection conn = null;
    try
    {
        // Ask shard map to broker a validated connection for the given key.
        conn = shardMap.OpenConnectionForKey(
            tenantId, connectionStr, ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey
        // to enable Row-Level Security filtering.
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText =
            @"exec sp_set_session_context
                @key=N'TenantId', @value=@shardingKey";
        cmd.Parameters.AddWithValue("@shardingKey", tenantId);
        cmd.ExecuteNonQuery();

        return conn;
    }
    catch (Exception)
    {
        if (conn != null)
        {
            conn.Dispose();
        }
        throw;
    }
}

// ...

// Example query via ADO.NET SqlClient.
// If row-level security is enabled, only Tenant 4's blogs are listed.
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (SqlConnection conn = OpenConnectionForTenant(
        sharding.ShardMap, tenantId4, connStrBldr.ConnectionString))
    {
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"SELECT * FROM Blogs";

        Console.WriteLine(@"--
All blogs for TenantId {0} (using ADO.NET SqlClient):", tenantId4);

        SqlDataReader reader = cmd.ExecuteReader();
        while (reader.Read())
        {
            Console.WriteLine("{0}", reader["Name"]);
        }
    }
});

```

## <a name="2-data-tier-create-row-level-security-policy"></a>2. adatszint: sor szintű biztonsági házirend létrehozása

### <a name="create-a-security-policy-to-filter-the-rows-each-tenant-can-access"></a>Biztonsági szabályzat létrehozása az egyes bérlők által elérhető sorok szűréséhez

Most, hogy az alkalmazás az \_ aktuális TENANTID munkamenet-környezetet állít be a lekérdezés előtt, egy RLS biztonsági házirend szűrheti a lekérdezéseket, és kizárja a különböző TenantId rendelkező sorokat.

Az RLS implementálva van a Transact-SQL-ben. A felhasználó által definiált függvény határozza meg a hozzáférési logikát, és a biztonsági szabályzat a függvényt tetszőleges számú táblához köti. Ehhez a projekthez:

1. A függvény ellenőrzi, hogy az alkalmazás csatlakoztatva van-e az adatbázishoz, és hogy a munkamenet-környezetben tárolt TenantId megegyezik-e \_ az adott sor TenantId.
    - Az alkalmazás csatlakoztatva van, és nem egy másik SQL-felhasználó.

2. Egy SZŰRÉSi predikátum lehetővé teszi, hogy a TenantId-szűrőnek megfelelő sorok a SELECT, a UPDATE és a DELETE lekérdezésre legyenek továbbítva.
    - A blokk predikátum megakadályozza a szűrő behelyezését vagy frissítését sikertelenül.
    - Ha \_ a munkamenet-környezet nem lett beállítva, a függvény Null értéket ad vissza, és nem láthatók és nem szúrhatók be sorok.

Az RLS minden szegmensen való engedélyezéséhez hajtsa végre a következő T-SQL-T a Visual Studio (SSDT), a SSMS vagy a projektben található PowerShell-parancsfájl használatával. Ha [Elastic Database feladatokat](./elastic-jobs-overview.md)használ, automatizálhatja a T-SQL összes szegmensen való végrehajtását.

```sql
CREATE SCHEMA rls; -- Separate schema to organize RLS objects.
GO

CREATE FUNCTION rls.fn_tenantAccessPredicate(@TenantId int)
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult
        -- Use the user in your application’s connection string.
        -- Here we use 'dbo' only for demo purposes!
        WHERE DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo')
        AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId;
GO

CREATE SECURITY POLICY rls.tenantAccessPolicy
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts;
GO
```

> [!TIP]
> Összetett projektekben előfordulhat, hogy a predikátumot több száz táblára kell felvennie, ami unalmas lehet. Létezik egy segítő tárolt eljárás, amely automatikusan létrehoz egy biztonsági házirendet, és feltesz egy predikátumot a séma összes táblájába. További információkért tekintse meg a blogbejegyzés [Row-Level biztonság alkalmazása az összes táblára – segítő szkriptet (blog)](https://techcommunity.microsoft.com/t5/sql-server/apply-row-level-security-to-all-tables-helper-script/ba-p/384360).

Most, ha újra futtatja a minta alkalmazást, a bérlők csak azokat a sorokat látják, amelyek tartoznak hozzájuk. Emellett az alkalmazás nem szúrhat be olyan sorokat, amelyek nem a szegmens adatbázishoz jelenleg csatlakozó bérlők közé tartoznak. Emellett az alkalmazás nem tudja frissíteni a TenantId bármely olyan sorban, amelyet láthat. Ha az alkalmazás valamelyikre próbálkozik, a rendszer egy DbUpdateException emel.

Ha később új táblát ad hozzá, módosítsa a biztonsági házirendet az új táblához tartozó SZŰRÉSi és BLOKKOLÁSi predikátumok hozzáadásához.

```sql
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable;
GO
```

### <a name="add-default-constraints-to-automatically-populate-tenantid-for-inserts"></a>Alapértelmezett megkötések hozzáadása a lapkák TenantId automatikus feltöltéséhez

Az egyes táblákon alapértelmezett korlátozásokat állíthat be, hogy automatikusan feltöltse a TenantId a munkamenet-környezetben jelenleg tárolt értékkel a \_ sorok beszúrásakor. Egy példa következik.

```sql
-- Create default constraints to auto-populate TenantId with the
-- value of SESSION_CONTEXT for inserts.
ALTER TABLE Blogs
    ADD CONSTRAINT df_TenantId_Blogs
    DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId;
GO

ALTER TABLE Posts
    ADD CONSTRAINT df_TenantId_Posts
    DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId;
GO
```

Most az alkalmazásnak nem kell TenantId megadnia a sorok beszúrásakor:

```csharp
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (var db = new ElasticScaleContext<int>(
        sharding.ShardMap, tenantId, connStrBldr.ConnectionString))
    {
        // The default constraint sets TenantId automatically!
        var blog = new Blog { Name = name };
        db.Blogs.Add(blog);
        db.SaveChanges();
    }
});
```

> [!NOTE]
> Ha az alapértelmezett korlátozásokat használja egy Entity Framework projekthez, azt javasoljuk, hogy a TenantId oszlopot *ne* foglalja bele az EF-adatmodellbe. Ennek az az oka, hogy Entity Framework lekérdezések automatikusan megadják az alapértelmezett értékeket, amelyek felülbírálják a munkamenet-környezetet használó T-SQL-ben létrehozott alapértelmezett korlátozásokat \_ .
> Ha például a minta projektben szeretné használni az alapértelmezett korlátozásokat, távolítsa el az TenantId-t a DataClasses. cs-ből (és futtassa Add-Migration a Package Manager konzolon), és a T-SQL használatával győződjön meg arról, hogy a mező csak az adatbázis tábláiban van. Így az EF automatikusan helytelenül adja meg az alapértelmezett értékeket az adatok beszúrásakor.

### <a name="optional-enable-a-superuser-to-access-all-rows"></a>Választható Az összes sor elérésének engedélyezése a *rendszergazda* számára

Egyes alkalmazások létrehozhatnak egy olyan *rendszeradminisztrátort* , aki hozzáférhet az összes sorhoz. A rendszergazda engedélyezheti a jelentéskészítést az összes bérlőn az összes szegmensen. Vagy a rendszergazda olyan felosztási műveleteket végezhet a szegmenseken, amelyek a bérlői sorok adatbázisok közötti áthelyezését foglalják magukban.

A rendszergazda engedélyezéséhez hozzon létre egy új SQL-felhasználót ( `superuser` ebben a példában) az egyes szegmensek adatbázisaiban. Ezután módosítsa a biztonsági házirendet egy új predikátum-függvénnyel, amely lehetővé teszi a felhasználó számára az összes sor elérését. A következő függvényt adja meg.

```sql
-- New predicate function that adds superuser logic.
CREATE FUNCTION rls.fn_tenantAccessPredicateWithSuperUser(@TenantId int)
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult
        WHERE
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo') -- Replace 'dbo'.
            AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId
        )
        OR
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('superuser')
        );
GO

-- Atomically swap in the new predicate function on each table.
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER BLOCK  PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts,
    ALTER BLOCK  PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts;
GO
```


### <a name="maintenance"></a>Karbantartás

- **Új** szegmensek hozzáadása: hajtsa végre a T-SQL-szkriptet az RLS bármely új szegmensen való engedélyezéséhez, ellenkező esetben a rendszer nem szűri az ilyen szegmensek lekérdezéseit.
- **Új táblák hozzáadása**: Ha új táblát hoz létre, vegyen fel egy szűrőt és a tiltási predikátumot az összes szegmens biztonsági házirendjébe. Ellenkező esetben a rendszer nem szűri az új tábla lekérdezéseit. Ez a Hozzáadás egy DDL-trigger használatával automatizálható, a [Row-Level biztonság automatikus alkalmazása az újonnan létrehozott táblákra (blog)](https://techcommunity.microsoft.com/t5/SQL-Server/Apply-Row-Level-Security-automatically-to-newly-created-tables/ba-p/384393)című cikkben leírtak szerint.

## <a name="summary"></a>Összefoglalás

A rugalmas adatbázis-eszközök és a sor szintű biztonság együtt használható az alkalmazás adatrétegének horizontális felskálázásához a több-bérlős és az egybérlős szegmensek támogatásával is. Több-bérlős szegmensek használatával hatékonyabban tárolhatók az adathalmazok. Ezt a hatékonyságot akkor kell kimutatni, ha a nagy számú bérlő csak néhány sornyi adattal rendelkezik. Az egybérlős szegmensek olyan prémium bérlőket támogatnak, amelyek szigorúbb teljesítménybeli és elkülönítési követelményekkel rendelkeznek. További információ: [sor szintű biztonsági referenciák][rls].

## <a name="additional-resources"></a>További források

- [Mi az az Azure rugalmas készlet?](elastic-pool-overview.md)
- [Scaling out with Azure SQL Database (Horizontális felskálázás az Azure SQL Database segítségével)](elastic-scale-introduction.md)
- [Tervezési minták az Azure SQL Database-t használó több-bérlős SaaS-alkalmazásokhoz](./saas-tenancy-app-design-patterns.md)
- [Hitelesítés a több-bérlős alkalmazásokban az Azure AD és az OpenID Connect segítségével](/azure/architecture/multitenant-identity/authenticate)
- [A Tailspin Surveys-alkalmazás](/azure/architecture/multitenant-identity/tailspin)

## <a name="questions-and-feature-requests"></a>Kérdések és szolgáltatások kérései

Ha kérdése van, vegye fel velünk a kapcsolatot a [Microsoft Q&a SQL Database vonatkozó kérdés oldalán](/answers/topics/azure-sql-database.html). És vegyen fel semmilyen funkciót a [SQL Database visszajelzési fórumba](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/saas-tenancy-elastic-tools-multi-tenant-row-level-security/blogging-app.png
<!--anchors-->
[rls]: /sql/relational-databases/security/row-level-security
[s-d-elastic-database-client-library]:elastic-database-client-library.md