---
title: Csatlakozás SQL Server, Azure SQL Database vagy Azure SQL Managed Instance
description: Sql-adatbázisok feladatainak automatizálása a helyszínen vagy a felhőben az Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 03/24/2021
tags: connectors
ms.openlocfilehash: 2e06616914f1e78a71a540fbd64021c0e1bfcbab
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785972"
---
# <a name="automate-workflows-for-a-sql-database-by-using-azure-logic-apps"></a>SQL-adatbázis munkafolyamatának automatizálása a Azure Logic Apps

Ez a cikk bemutatja, hogyan férhet hozzá az SQL-adatbázis adataihoz egy logikai alkalmazásból a SQL Server összekötővel. Így logikai alkalmazások létrehozásával automatizálhatja az SQL-adatokat és -erőforrásokat kezelő feladatokat, folyamatokat és munkafolyamatokat. Az SQL Server összekötő a [(SQL Server)](/sql/sql-server/sql-server-technical-documentation) és a (Azure SQL Database) és [a (Azure SQL Managed Instance)](../azure-sql/database/sql-database-paas-overview.md) Azure SQL Managed Instance. [](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)

Létrehozhat olyan logikai alkalmazásokat, amelyek akkor futnak, ha az SQL-adatbázisban vagy más rendszerekben, például a Dynamics CRM Online-ban események aktiválják őket. A logikai alkalmazások az SQL-lekérdezések és tárolt eljárások futtatásával együtt adatokat is lekérdeznek, beszúrnak és törölnek. Létrehozhat például egy logikai alkalmazást, amely automatikusan ellenőrzi az új rekordokat a Dynamics CRM Online-ban, elemeket ad hozzá az SQL-adatbázishoz az új rekordokhoz, majd e-mailes riasztásokat küld a hozzáadott elemekről.

Ha még nem használja a logikai alkalmazásokat, tekintse át a Mi a [Azure Logic Apps:](../logic-apps/logic-apps-overview.md) Az első logikai alkalmazás [létrehozása– rövid útmutatót.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Az összekötőkre vonatkozó műszaki információkért, korlátozásokért és ismert problémákért tekintse meg a SQL Server [összekötők referenciaoldalát.](/connectors/sql/)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Egy [SQL Server adatbázis](/sql/relational-databases/databases/create-a-database), [Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md), vagy [Azure SQL Managed Instance.](../azure-sql/managed-instance/instance-create-quickstart.md)

  A tábláknak adatokat kell tartalmazni, hogy a logikai alkalmazás eredményeket ad vissza a műveletek hívása során. Ha a Azure SQL Database használ, használhatja a benne foglalt mintaadatbázisokat is.

* Az SQL-kiszolgáló neve, az adatbázis neve, a felhasználóneve és a jelszava. Ezekre a hitelesítő adatokra azért van szükség, hogy engedélyezze a logikát az SQL Server eléréséhez.

  * Helyszíni SQL Server a kapcsolati sztringben találja:

    `Server={your-server-address};Database={your-database-name};User Id={your-user-name};Password={your-password};`

  * A Azure SQL Database a kapcsolati sztringben találhatja meg ezeket az adatokat.
  
    Ha például meg kell találnia ezt a sztringet a Azure Portal nyissa meg az adatbázist. Az adatbázis menüjében válassza a Kapcsolati **sztringek vagy** a **Tulajdonságok lehetőséget:**

    `Server=tcp:{your-server-name}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

<a name="multi-tenant-or-ise"></a>

* Attól függően, hogy a logikai alkalmazások globális, több-bérlős Azure-ban vagy integrációs szolgáltatási [környezetben (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)fognak futni, a következő követelmények vonatkoznak a helyszíni virtuális SQL Server:

  * A globális, több-bérlős Azure-beli logikai alkalmazások esetében, amelyek helyszíni SQL Server-hoz csatlakoznak, telepítenie kell a helyszíni adatátjárót egy helyi számítógépre és egy, az [Azure-ban](../logic-apps/logic-apps-gateway-install.md) már létrehozott adatátjáró-erőforrásra. [](../logic-apps/logic-apps-gateway-connection.md)

  * Az ISE-beli logikai alkalmazások esetében, amelyek helyszíni SQL Server és Windows-hitelesítést használnak, az ISE-verzióval ellátott SQL Server összekötő nem támogatja a Windows-hitelesítést. Így továbbra is az adatátjárót és a nem ISE-SQL Server kell használnia. Más hitelesítési típusok esetén nem kell az adatátjárót használnia, és használhatja az ISE-verzióval ellátott összekötőt.

* A logikai alkalmazás, ahol hozzá kell férni az SQL-adatbázishoz. A logikai alkalmazás SQL-eseményindítóval való indításhoz egy üres [logikai alkalmazásra lesz szüksége.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Csatlakozás az adatbázishoz

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

Most folytassa az alábbi lépésekkel:

* [Csatlakozás felhőalapú Azure SQL Database felügyelt példányhoz](#connect-azure-sql-db)
* [Csatlakozás helyszíni SQL Server](#connect-sql-server)

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database-or-managed-instance"></a>Csatlakozás Azure SQL Database vagy felügyelt példányhoz

Ahhoz, Azure SQL Managed Instance helyszíni adatátjáró vagy integrációs szolgáltatási környezet használata nélkül fér hozzá egy alkalmazáshoz, be kell állítania a nyilvános végpontot a [Azure SQL Managed Instance.](../azure-sql/managed-instance/public-endpoint-configure.md) A nyilvános végpont a 3342-es portot használja, ezért ezt a portszámot adja meg, amikor létrehozza a kapcsolatot a logikai alkalmazásból.


Amikor első alkalommal ad hozzá EGY SQL-eseményindítót vagy EGY [SQL-műveletet,](#add-sql-trigger) és még nem hozott létre kapcsolatot az adatbázissal, a rendszer a következő lépésekre kéri: [](#add-sql-action)

1. A **Hitelesítés típusa beállításban** válassza ki a szükséges és engedélyezett hitelesítést az adatbázison Azure SQL Database vagy Azure SQL Managed Instance:

   | Hitelesítés | Description |
   |----------------|-------------|
   | [**Integrált Azure AD**](../azure-sql/database/authentication-aad-overview.md) | – Támogatja a nem ISE és az ISE SQL Server összekötőt. <p><p>– Érvényes identitást igényel a Azure Active Directory (Azure AD), amely hozzáféréssel rendelkezik az adatbázishoz. <p>További információt az alábbi témakörökben talál: <p>- [Azure SQL biztonsági áttekintés – Hitelesítés](../azure-sql/database/security-overview.md#authentication) <br>- [Adatbázis-hozzáférés engedélyezése Azure SQL – Hitelesítés és engedélyezés](../azure-sql/database/logins-create-manage.md#authentication-and-authorization) <br>- [Azure SQL – Integrált Azure AD-hitelesítés](../azure-sql/database/authentication-aad-overview.md) |
   | [**SQL Server hitelesítés**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication) | – Támogatja a nem ISE és az ISE SQL Server összekötőt. <p><p>– Érvényes felhasználónevet és erős jelszót igényel, amelyek az adatbázisban vannak létrehozva és tárolva. <p>További információt az alábbi témakörökben talál: <p>- [Azure SQL biztonsági áttekintés – Hitelesítés](../azure-sql/database/security-overview.md#authentication) <br>- [Adatbázis-hozzáférés engedélyezése Azure SQL – Hitelesítés és engedélyezés](../azure-sql/database/logins-create-manage.md#authentication-and-authorization) |
   |||

   Ez a példa az **Integrált Azure AD-val folytatódik:**

   ![Képernyőkép a "SQL Server" kapcsolati ablakról, a megnyitott "Hitelesítési típus" listával és az "Integrált Azure AD" lehetőség kijelölve.](./media/connectors-create-api-sqlazure/select-azure-ad-authentication.png)

1. Az **Azure AD Integrated (Integrált Azure AD) kiválasztása után** válassza a Sign In **(Bejelentkezés) lehetőséget.** Attól függően, hogy az Azure SQL Database vagy Azure SQL Managed Instance használja, válassza ki a hitelesítő adatait a hitelesítéshez.

1. Válassza ki az alábbi értékeket az adatbázishoz:

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Kiszolgáló neve** | Igen | Az SQL-kiszolgáló címe, például: `Fabrikam-Azure-SQL.database.windows.net` |
   | **Adatbázis neve** | Yes | Az SQL-adatbázis neve, például: `Fabrikam-Azure-SQL-DB` |
   | **Tábla neve** | Yes | A használni kívánt tábla, például: `SalesLT.Customer` |
   ||||

   > [!TIP]
   > Az adatbázis és a tábla információinak megadása érdekében a következő lehetőségek állnak rendelkezésre:
   > 
   > * Ezt az információt az adatbázis kapcsolati sztringjében találja. Például a Azure Portal keresse meg és nyissa meg az adatbázist. Az adatbázis menüjében válassza a Kapcsolati **sztringek** vagy a **Tulajdonságok** lehetőséget, ahol megtalálja a következő sztringet:
   >
   >   `Server=tcp:{your-server-address}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`
   >
   > * Alapértelmezés szerint a rendszeradatbázisok táblái ki vannak szűrve, így előfordulhat, hogy nem jelennek meg automatikusan a rendszeradatbázis kiválasztásakor. Másik lehetőségként manuálisan is megadhatja a tábla nevét, miután az **Enter custom value (Egyéni** érték beírása) lehetőséget választja az adatbázislistában.
   >

   Ez a példa bemutatja, hogyan néznek ki ezek az értékek:

   ![Kapcsolat létrehozása az SQL Database-hez](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)

1. Most folytassa a még nem befejezett lépésekkel az SQL-eseményindító hozzáadása vagy az [SQL-művelet](#add-sql-trigger) [hozzáadása című lépésben.](#add-sql-action)

<a name="connect-sql-server"></a>

### <a name="connect-to-on-premises-sql-server"></a>Csatlakozás helyszíni SQL Server

Amikor első alkalommal ad hozzá EGY SQL-eseményindítót vagy EGY [SQL-műveletet,](#add-sql-trigger) és még nem hozott létre kapcsolatot az adatbázissal, a rendszer a következő lépésekre kéri: [](#add-sql-action)

1. A helyszíni adatátjárót igénylő helyszíni SQL Server-kapcsolatok esetében győződjön meg arról, hogy megfelel a következő [előfeltételeknek:](#multi-tenant-or-ise).

   Ellenkező esetben az adatátjáró erőforrása nem jelenik meg a **Kapcsolatátjáró** listában a kapcsolat létrehozásakor.

1. A **Hitelesítés típusa beállításban** válassza ki a szükséges és engedélyezett hitelesítést a SQL Server:

   | Hitelesítés | Description |
   |----------------|-------------|
   | [**Windows-hitelesítés**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-windows-authentication) | – Csak a nem ISE SQL Server-összekötőt támogatja, amelyhez egy, a kapcsolathoz korábban az Azure-ban létrehozott adatátjáró-erőforrásra van szükség, függetlenül attól, hogy több-bérlős Azure-t vagy ISE-t használ. <p><p>– Érvényes Windows-felhasználónév és -jelszó szükséges az identitás Windows-fiókon keresztüli megerősítéséhez. <p>További információ: [Windows-hitelesítés](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-windows-authentication) |
   | [**SQL Server hitelesítés**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication) | – Támogatja a nem ISE és az ISE SQL Server összekötőt. <p><p>– Érvényes felhasználónevet és erős jelszót igényel, amelyek a fiókban vannak létrehozva és SQL Server. <p>További információ: [SQL Server.](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication) |
   |||

   Ez a példa a **Windows-hitelesítéssel folytatódik:**

   ![Válassza ki a használni kívánt hitelesítési típust](./media/connectors-create-api-sqlazure/select-windows-authentication.png)

1. Válassza ki vagy adja meg a következő értékeket az SQL-adatbázishoz:

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **SQL-kiszolgáló neve** | Yes | Az SQL-kiszolgáló címe, például: `Fabrikam-Azure-SQL.database.windows.net` |
   | **SQL-adatbázis neve** | Yes | A SQL Server neve, például: `Fabrikam-Azure-SQL-DB` |
   | **Felhasználónév** | Yes | Az SQL-kiszolgáló és az adatbázis felhasználóneve |
   | **Jelszó** | Yes | Az SQL-kiszolgáló és az adatbázis jelszava |
   | **Előfizetés** |  Igen, Windows-hitelesítéshez | Az Azure-ban korábban létrehozott adatátjáró-erőforrás Azure-előfizetése |
   | **Kapcsolati átjáró** | Igen, Windows-hitelesítéshez | Az Azure-ban korábban létrehozott adatátjáró-erőforrás neve <p><p>**Tipp:** Ha az átjáró nem jelenik meg a listában, ellenőrizze, hogy megfelelően beállította-e [az átjárót.](../logic-apps/logic-apps-gateway-connection.md) |
   |||

   > [!TIP]
   > Ezt az információt az adatbázis kapcsolati sztringjében találja:
   > 
   > * `Server={your-server-address}`
   > * `Database={your-database-name}`
   > * `User ID={your-user-name}`
   > * `Password={your-password}`

   Ez a példa bemutatja, hogyan néznek ki ezek az értékek:

   ![Kapcsolat SQL Server létrehozása](./media/connectors-create-api-sqlazure/sql-server-create-connection-complete.png)

1. Ha elkészült, válassza a Létrehozás **lehetőséget.**

1. Most folytassa az SQL-eseményindító hozzáadása vagy az [SQL-művelet](#add-sql-trigger) hozzáadása című lépésben [még nem befejezett lépésekkel.](#add-sql-action)

<a name="add-sql-trigger"></a>

## <a name="add-a-sql-trigger"></a>SQL-eseményindító hozzáadása

1. A [Azure Portal](https://portal.azure.com) vagy a Visual Studio hozzon létre egy üres logikai alkalmazást, amely megnyitja a Logikaialkalmazás-tervezőt. Ez a példa a következővel folytatódik: Azure Portal.

1. A tervezőben a keresőmezőbe írja be a következőt: `sql server` . Az eseményindítók listájából válassza ki a kívánt SQL-eseményindítót. Ez a példa a **When an item is created (Elem létrehozásakor) eseményindítót** használja.

   ![Válassza az "Elem létrehozásakor" eseményindítót](./media/connectors-create-api-sqlazure/select-sql-server-trigger.png)

1. Ha először csatlakozik az SQL-adatbázishoz, a rendszer felkéri, hogy hozza létre [az SQL Database-kapcsolatot.](#create-connection) A kapcsolat létrehozása után folytathatja a következő lépéssel.

1. Az eseményindítóban adja meg annak időközét és gyakoriságát, hogy az eseményindító milyen gyakran ellenőrzi a táblát.

1. Az eseményindító további elérhető tulajdonságainak hozzáadásához nyissa meg az **Új paraméter hozzáadása** listát.

   Ez az eseményindító csak egy sort ad vissza a kiválasztott táblából, és semmi mást. Ha más feladatokat szeretne végrehajtani, folytassa egy [](../connectors/apis-list.md) [SQL](#add-sql-action) Connector-művelet vagy egy másik művelet hozzáadásával, amely végrehajtja a logikai alkalmazás munkafolyamatában a következő feladatot.

   Például az ebben a sorban lévő adatok megtekintéséhez hozzáadhat más műveleteket, amelyek létrehoznak egy fájlt, amely tartalmazza a visszaadott sor mezőit, majd e-mailes riasztásokat küldhet. Az összekötő további elérhető műveletekkel kapcsolatos további információért tekintse meg az összekötő [referenciaoldalát.](/connectors/sql/)

1. A tervező eszköztárán válassza a Mentés **lehetőséget.**

   Bár ez a lépés automatikusan engedélyezi és közzéteszi a logikai alkalmazást az Azure-ban, a logikai alkalmazás jelenleg csak az adatbázist ellenőrzi a megadott időköz és gyakoriság alapján.

<a name="trigger-recurrence-shift-drift"></a>

### <a name="trigger-recurrence-shift-and-drift"></a>Ismétlődési váltás és eltérés kiváltása

A kapcsolatalapú eseményindítók, amelyekben először létre kell hoznia egy kapcsolatot, például az SQL-eseményindító, eltérnek a natív módon a Azure Logic Apps-ban futó beépített eseményindítóktól, például az [Ismétlődés eseményindítótól.](../connectors/connectors-native-recurrence.md) Az ismétlődő kapcsolatalapú eseményindítókban nem az ismétlődési ütemezés az egyetlen olyan illesztő, amely szabályozza a végrehajtást, és az időzóna csak a kezdeti kezdési időt határozza meg. A későbbi futtatás az ismétlődési ütemezéstől, a legutóbbi eseményindító-végrehajtástól, valamint egyéb tényezőktől *függ,* amelyek miatt a futási idő eltérést okozhat, vagy váratlan viselkedést okozhat, például nem tarthatja fenn a megadott ütemezést a nyári időszámítás (DST) kezdetekor és végén. Annak érdekében, hogy az ismétlődési idő ne váltsa át a DST effektusát, manuálisan módosítsa az ismétlődést úgy, hogy a logikai alkalmazás továbbra is a várt időpontban fusson. Ellenkező esetben a kezdési idő egy órával előretul, amikor a DST elindul, és egy órával visszafelé, amikor a DST véget ér. További információ: [Ismétlődés kapcsolatalapú eseményindítók esetén.](../connectors/apis-list.md#recurrence-for-connection-based-triggers)

<a name="add-sql-action"></a>

## <a name="add-a-sql-action"></a>SQL-művelet hozzáadása

Ebben a példában a logikai alkalmazás az Ismétlődés eseményindítóval [kezdődik,](../connectors/connectors-native-recurrence.md)és egy olyan műveletet hív meg, amely lekért egy sort egy SQL-adatbázisból.

1. A [Azure Portal](https://portal.azure.com) vagy a Visual Studio nyissa meg a logikai alkalmazást a Logic App Designerben. Ez a példa folytatja a Azure Portal.

1. Az eseményindító vagy művelet alatt, ahol hozzá szeretné adni az SQL-műveletet, válassza az Új **lépés lehetőséget.**

   ![Művelet hozzáadása a logikai alkalmazáshoz](./media/connectors-create-api-sqlazure/select-new-step-logic-app.png)

   Vagy ha egy műveletet szeretne hozzáadni a meglévő lépések között, helyezze az egeret a csatlakozó nyílra. Válassza a megjelenő pluszjelet ( **+** ), majd a Művelet hozzáadása **lehetőséget.**

1. A **Művelet kiválasztása alatt** a keresőmezőbe írja be a következőt: `sql server` . A műveletek listájából válassza ki a kívánt SQL-műveletet. Ebben a példában a **Sor beása műveletet** használjuk, amely egyetlen rekordot kap.

   ![Az SQL "Sor be lekérdezése" műveletének kiválasztása](./media/connectors-create-api-sqlazure/select-sql-get-row-action.png)

1. Ha először csatlakozik az SQL-adatbázishoz, a rendszer felkéri az SQL Database-kapcsolat [létrehozására.](#create-connection) A kapcsolat létrehozása után folytathatja a következő lépéssel.

1. Válassza ki **a tábla nevét,** amely ebben `SalesLT.Customer` a példában található. Adja meg **a kívánt** rekord sorazonosítóját.

   ![Válassza ki a tábla nevét, és adja meg a sorazonosítót](./media/connectors-create-api-sqlazure/specify-table-row-id.png)

   Ez a művelet csak egy sort ad vissza a kiválasztott táblából, semmi mást. Így az ebben a sorban lévő adatok megtekintéséhez hozzáadhat olyan további műveleteket, amelyek létrehoznak egy fájlt, amely tartalmazza a visszaadott sor mezőit, és a fájlt egy felhőalapú tárfiókban tárolja. Az összekötő további elérhető műveletekkel kapcsolatos további információért tekintse meg az összekötő [referenciaoldalát.](/connectors/sql/)

1. Ha végzett, a tervező eszköztárán válassza a Mentés **lehetőséget.**

   Ez a lépés automatikusan engedélyezi és közzéteszi a logikai alkalmazást az Azure-ban.

<a name="handle-bulk-data"></a>

## <a name="handle-bulk-data"></a>Tömeges adatok kezeléséhez

Néha olyan nagy eredményhalmazokkal kell dolgoznia, hogy az összekötő ne egyszerre adja vissza az összes eredményt, vagy jobban szeretné szabályozni az eredményhalmazok méretét és szerkezetét. Az ilyen nagy eredményhalmazok a következő módokon kezelhetők:

* Az eredmények kisebb készletekként való kezeléséhez kapcsolja be a *tördelést.* További információ: Tömeges adatok, rekordok és elemek lekért [száma tördelés használatával.](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md) További információ: [SQL tördelés tömeges adatátvitelhez a Logic Apps.](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx)

* Hozzon [*létre egy tárolt eljárást,*](/sql/relational-databases/stored-procedures/stored-procedures-database-engine) amely a kívánt módon rendezi az eredményeket. Az SQL-összekötő számos háttérszolgáltatásokat nyújt, amelyekhez a Azure Logic Apps használatával férhet hozzá, így könnyebben automatizálhatja az SQL-adatbázistáblákkal használható üzleti feladatokat.

  Több sor beszúrása vagy beszúrása esetén a logikai alkalmazás ezeken a sorokon iterálhat egy [*until*](../logic-apps/logic-apps-control-flow-loops.md#until-loop) hurok használatával ezeken a [korlátokon belül.](../logic-apps/logic-apps-limits-and-config.md) Ha azonban a logikai alkalmazásnak olyan nagy( például több ezer vagy millió sornyi) rekordhalmazokkal kell dolgoznia, hogy minimalizálni szeretné az adatbázis hívásaiból eredő költségeket.

  Az eredmények kívánt módon való rendszerezése érdekében létrehozhat egy tárolt eljárást, amely az SQL-példányban fut, és a **SELECT - ORDER BY** utasítást használja. Ezzel a megoldással jobban szabályozhatja az eredmények méretét és szerkezetét. A logikai alkalmazás a tárolt eljárást a SQL Server összekötő Tárolt eljárás végrehajtása **műveletének használatával hívja** meg. További információ: [SELECT - ORDER BY Clause.](/sql/t-sql/queries/select-order-by-clause-transact-sql)

  > [!NOTE]
  > Az SQL-összekötő tárolt eljárásának időkorlátja kevesebb, mint [2 perc.](/connectors/sql/#known-issues-and-limitations) Egyes tárolt eljárások ennél a korlátnál tovább tarthatnak, ami hibát `504 Timeout` okoz. Ezt a problémát egy SQL-befejezési eseményindítóval, egy natív SQL átmenő lekérdezéssel, egy állapottáblával és kiszolgálóoldali feladatokkal lehet megoldanunk.
  > 
  > Ehhez a feladathoz használhatja az [Azure rugalmasfeladat-ügynököt a](../azure-sql/database/elastic-jobs-overview.md) [Azure SQL Database.](../azure-sql/database/sql-database-paas-overview.md) A [SQL Server és a](/sql/sql-server/sql-server-technical-documentation) [Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)a következőt [SQL Server Agent:](/sql/ssms/agent/sql-server-agent). További tudnivalókért lásd a hosszú ideig futó tárolt eljárások időtúllépésének kezeléséről az [SQL Connector for Azure Logic Apps.](../logic-apps/handle-long-running-stored-procedures-sql-connector.md)

### <a name="handle-dynamic-bulk-data"></a>Dinamikus tömeges adatok kezeléséhez

Ha egy tárolt eljárást a SQL Server hív meg, a visszaadott kimenet néha dinamikus. Ebben a forgatókönyvben kövesse az alábbi lépéseket:

1. A [Azure Portal](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logikaialkalmazás-tervezőben.

1. Tekintse meg a kimeneti formátumot egy tesztfutat elvégzésével. Másolja és mentse a mintakimenetet.

1. A tervezőben a tárolt eljárás hívásának művelete alatt válassza az Új **lépés lehetőséget.**

1. A **Művelet kiválasztása alatt** keresse meg és válassza ki a [**JSON-elemzési**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action) műveletet.

1. A **JSON-adatok elemezése műveletben** válassza **a Séma létrehozása hasznosadat-minta használatával lehetőséget.**

1. Az **Enter or paste a sample JSON payload (JSON hasznosadat-minta)** mezőbe illessze be a mintakimenetet, és válassza a Done (Kész) **lehetőséget.**

   > [!NOTE]
   > Ha olyan hibaüzenetet kap Logic Apps nem tud sémát létrehozni, ellenőrizze, hogy a mintakimenet szintaxisa megfelelően van-e formázva. Ha továbbra sem tudja létrehozni a sémát, a **Séma** mezőben adja meg manuálisan a sémát.

1. A tervező eszköztárán válassza a **Mentés lehetőséget.**

1. A JSON-tartalom tulajdonságaira való hivatkozáshoz kattintson a szerkesztőmezőkre, ahol hivatkozni szeretne a tulajdonságokra, hogy megjelenjen a dinamikus tartalmak listája. A listában, a [**JSON-fájl**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action) szerkesztése fejléc alatt válassza ki a kívánt JSON-tartalomtulajdonságokhoz szükséges adattokeneket.

## <a name="troubleshoot-problems"></a>Problémák elhárítása

<a name="connection-problems"></a>

### <a name="connection-problems"></a>Csatlakozási problémák

Kapcsolati problémák gyakran előfordulhatnak, ezért az ilyen típusú problémák elhárításához és megoldásához tekintse meg a csatlakozási hibák megoldásával kapcsolatos [SQL Server.](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server) Íme néhány példa:

* `A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.`

* `(provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server) (Microsoft SQL Server, Error: 53)`

* `(provider: TCP Provider, error: 0 - No such host is known.) (Microsoft SQL Server, Error: 11001)`

## <a name="connector-specific-details"></a>Összekötő-specifikus részletek

Az összekötő triggerekkel, műveletekkel és korlátokkal kapcsolatos [](/connectors/sql/)műszaki információkért tekintse meg az összekötő Swagger-leírásból létrehozott referenciaoldalát.

## <a name="next-steps"></a>Következő lépések

* További tudnivalók a további [összekötőkről Azure Logic Apps](../connectors/apis-list.md)
