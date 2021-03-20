---
title: Kapcsolódás SQL Serverhoz, Azure SQL Databasehoz vagy az Azure SQL felügyelt példányához
description: A helyszínen vagy a felhőben található SQL-adatbázisok feladatainak automatizálása Azure Logic Apps használatával
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 01/07/2021
tags: connectors
ms.openlocfilehash: 75c657236b6e06a7e0f6c717d746bcc8c034d423
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98013447"
---
# <a name="automate-workflows-for-a-sql-database-by-using-azure-logic-apps"></a>Az SQL Database munkafolyamatainak automatizálása Azure Logic Apps használatával

Ez a cikk bemutatja, hogyan érheti el az SQL Database-ben tárolt adatok egy logikai alkalmazásban az SQL Server-összekötő használatával. Így automatizálhatja az SQL-adatok és-erőforrások kezelésére szolgáló feladatokat, folyamatokat és munkafolyamatokat logikai alkalmazások létrehozásával. Az SQL Server-összekötő a [SQL Server](/sql/sql-server/sql-server-technical-documentation) , valamint a [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md) és az [Azure SQL felügyelt példányai](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)esetében működik.

Létrehozhat olyan logikai alkalmazásokat, amelyek az SQL-adatbázisban vagy más rendszerekben, például a Dynamics CRM Online-ban indított események indításakor futnak. A logikai alkalmazások az SQL-lekérdezések és a tárolt eljárások futtatásával is lekérhetik, behelyezhetik és törölhetik az adatforrásokat. Létrehozhat például egy olyan logikai alkalmazást, amely automatikusan ellenőrzi az új rekordokat a Dynamics CRM Online-ban, elemeket ad hozzá az SQL-adatbázishoz bármely új rekordhoz, majd e-mail-riasztásokat küld a hozzáadott elemekről.

Ha most ismerkedik a Logic apps szolgáltatással, tekintse át a [Mi az Azure Logic apps](../logic-apps/logic-apps-overview.md) és a gyors útmutató [: az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)lehetőséget. Az összekötő-specifikus technikai információkkal, korlátozásokkal és ismert problémákkal kapcsolatban tekintse meg az [SQL Server-összekötő hivatkozását ismertető oldalt](/connectors/sql/).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* [SQL Server adatbázis](/sql/relational-databases/databases/create-a-database), [Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md)vagy [Azure SQL felügyelt példány](../azure-sql/managed-instance/instance-create-quickstart.md).

  A tábláknak adatokkal kell rendelkezniük, hogy a logikai alkalmazás a műveletek meghívásakor eredményeket lehessen visszaadni. Ha Azure SQL Database használ, használhat példákat tartalmazó adatbázisokat is.

* Az SQL-kiszolgáló nevét, az adatbázis nevét, a felhasználónevét és a jelszavát. Ezekre a hitelesítő adatokra akkor van szükség, ha engedélyezni szeretné a logikáját az SQL Serverhez való hozzáféréshez.

  * A helyszíni SQL Server a következő adatokat találja meg a kapcsolati karakterláncban:

    `Server={your-server-address};Database={your-database-name};User Id={your-user-name};Password={your-password};`

  * A Azure SQL Database a kapcsolati karakterláncban találhatja meg ezeket az adatokat.
  
    Ha például meg szeretné keresni ezt a karakterláncot a Azure Portalban, nyissa meg az adatbázist. Az adatbázis menüben válassza a **kapcsolatok karakterláncok** vagy **Tulajdonságok** elemet:

    `Server=tcp:{your-server-name}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

<a name="multi-tenant-or-ise"></a>

* Attól függően, hogy a logikai alkalmazásai futnak-e globális, több-bérlős Azure-beli vagy [integrációs szolgáltatási környezetben (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), az alábbi követelmények vonatkoznak a helyszíni SQL Serverhoz való csatlakozásra:

  * A globális, több-bérlős Azure-beli logikai alkalmazások esetében, amelyek helyszíni SQL Serverhoz csatlakoznak, a helyi számítógépre és az [Azure-ban már létrehozott adatátjáró-erőforrásra](../logic-apps/logic-apps-gateway-connection.md)kell telepítenie a helyszíni [adatátjárót](../logic-apps/logic-apps-gateway-install.md) .

  * A helyszíni SQL Serverhoz csatlakozó és a Windows-hitelesítést használó ISE-beli Logic apps esetén az ISE-verzióval ellátott SQL Server-összekötő nem támogatja a Windows-hitelesítést. Ezért továbbra is az adatátjárót és a nem ISE SQL Server összekötőt kell használnia. Más hitelesítési típusok esetében nem kell használnia az adatátjárót, és használhatja az ISE-verzióval ellátott összekötőt.

* Az a logikai alkalmazás, amelyben hozzá kell férnie az SQL-adatbázishoz. A logikai alkalmazás SQL-triggerrel való elindításához [üres logikai alkalmazásra](../logic-apps/quickstart-create-first-logic-app-workflow.md)van szükség.

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Csatlakozás az adatbázishoz

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

Most folytassa a következő lépésekkel:

* [Kapcsolódás felhőalapú Azure SQL Databasehoz vagy felügyelt példányhoz](#connect-azure-sql-db)
* [Kapcsolódás helyszíni SQL Server](#connect-sql-server)

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database-or-managed-instance"></a>Kapcsolódás Azure SQL Database vagy felügyelt példányhoz

Az Azure SQL felügyelt példányainak helyszíni adatátjáró vagy integrációs szolgáltatási környezet használata nélküli eléréséhez [be kell állítania a nyilvános végpontot az Azure SQL felügyelt példányán](../azure-sql/managed-instance/public-endpoint-configure.md). A nyilvános végpont a 3342-es portot használja, ezért ügyeljen arra, hogy ezt a portszámot adja meg a logikai alkalmazással létesített kapcsolatok létrehozásakor.


Amikor először ad hozzá egy [SQL-triggert](#add-sql-trigger) vagy egy [SQL-műveletet](#add-sql-action), és korábban még nem hozott létre kapcsolatokat az adatbázishoz, a rendszer a következő lépések elvégzését kéri:

1. A **Hitelesítés típusa** mezőben válassza ki Azure SQL Database vagy az Azure SQL felügyelt példányán az adatbázishoz szükséges és engedélyezett hitelesítést:

   | Hitelesítés | Description |
   |----------------|-------------|
   | [**Azure AD-integráció**](../azure-sql/database/authentication-aad-overview.md) | -A nem ISE és ISE SQL Server összekötőt is támogatja. <p><p>– Az adatbázishoz hozzáféréssel rendelkező Azure Active Directory (Azure AD) érvényes identitást igényel. <p>További információt az alábbi témakörökben talál: <p>- [Az Azure SQL biztonsági áttekintése – hitelesítés](../azure-sql/database/security-overview.md#authentication) <br>- [Adatbázis-hozzáférés engedélyezése az Azure SQL-hitelesítéshez és engedélyezéshez](../azure-sql/database/logins-create-manage.md#authentication-and-authorization) <br>- [Azure SQL – Azure AD – integrált hitelesítés](../azure-sql/database/authentication-aad-overview.md) |
   | [**SQL Server hitelesítés**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication) | -A nem ISE és ISE SQL Server összekötőt is támogatja. <p><p>– Az adatbázisban létrehozott és tárolt érvényes felhasználónevet és erős jelszót kell megadnia. <p>További információt az alábbi témakörökben talál: <p>- [Az Azure SQL biztonsági áttekintése – hitelesítés](../azure-sql/database/security-overview.md#authentication) <br>- [Adatbázis-hozzáférés engedélyezése az Azure SQL-hitelesítéshez és engedélyezéshez](../azure-sql/database/logins-create-manage.md#authentication-and-authorization) |
   |||

   Ez a példa az **Azure ad**-vel való integrációt folytatja:

   ![Képernyőfelvétel: a "SQL Server" kapcsolatok ablak, amelyen a megnyitott "hitelesítési típus" lista és az "Azure AD Integrated" beállítás látható.](./media/connectors-create-api-sqlazure/select-azure-ad-authentication.png)

1. Miután kiválasztotta az **Azure ad-integrációt**, válassza **a bejelentkezés** lehetőséget. Attól függően, hogy Azure SQL Database vagy Azure SQL felügyelt példányt használ-e, válassza ki a hitelesítéshez használandó felhasználói hitelesítő adatokat.

1. Válassza ki ezeket az értékeket az adatbázishoz:

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Kiszolgáló neve** | Igen | Az SQL-kiszolgáló címe, például: `Fabrikam-Azure-SQL.database.windows.net` |
   | **Adatbázis neve** | Yes | Az SQL-adatbázis neve, például: `Fabrikam-Azure-SQL-DB` |
   | **Tábla neve** | Yes | A használni kívánt tábla, például: `SalesLT.Customer` |
   ||||

   > [!TIP]
   > Az adatbázis és a tábla adatainak megadásához a következő lehetőségek közül választhat:
   > 
   > * Megkeresheti ezeket az adatokat az adatbázisa összekapcsolási karakterláncában. A Azure Portal például keresse meg és nyissa meg az adatbázist. Az adatbázis menüben válassza a **kapcsolatok karakterláncok** vagy **Tulajdonságok** lehetőséget, ahol a következő sztringet találja:
   >
   >   `Server=tcp:{your-server-address}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`
   >
   > * Alapértelmezés szerint a rendszeradatbázisokban lévő táblák ki vannak szűrve, így előfordulhat, hogy a rendszer nem jeleníti meg automatikusan a rendszeradatbázist. Másik lehetőségként manuálisan is megadhatja a táblázat nevét, miután az adatbázis listából kiválasztja az **Egyéni érték megadása** lehetőséget.
   >

   Ez a példa az alábbi értékek megjelenését szemlélteti:

   ![SQL Database-hez való kapcsolódás létrehozása](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)

1. Most folytassa azokat a lépéseket, amelyek még nem fejeződött be [egy SQL-trigger hozzáadásával](#add-sql-trigger) vagy [egy SQL-művelet hozzáadásával](#add-sql-action).

<a name="connect-sql-server"></a>

### <a name="connect-to-on-premises-sql-server"></a>Kapcsolódás helyszíni SQL Server

Amikor először ad hozzá egy [SQL-triggert](#add-sql-trigger) vagy egy [SQL-műveletet](#add-sql-action), és korábban még nem hozott létre kapcsolatokat az adatbázishoz, a rendszer a következő lépések elvégzését kéri:

1. A helyszíni adatátjárót igénylő helyszíni SQL Server-kiszolgálóhoz való kapcsolódás esetén győződjön meg arról, hogy [végrehajtotta ezeket az előfeltételeket](#multi-tenant-or-ise).

   Ellenkező esetben az adatátjáró-erőforrás nem jelenik meg a kapcsolódási **átjáró** listában a kapcsolódás létrehozásakor.

1. A **Hitelesítés típusa** mezőben válassza ki a SQL Server szükséges és engedélyezett hitelesítést:

   | Hitelesítés | Description |
   |----------------|-------------|
   | [**Windows-hitelesítés**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-windows-authentication) | – Csak a nem ISE SQL Server összekötőt támogatja, amelyhez a kapcsolathoz korábban az Azure-ban létrehozott adatátjáró-erőforrás szükséges, függetlenül attól, hogy több-bérlős Azure-t vagy ISE-t használ. <p><p>– Érvényes Windows-felhasználónevet és-jelszót kell megadnia az identitás Windows-fiókon keresztüli megerősítéséhez. <p>További információ: Windows- [hitelesítés](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-windows-authentication) |
   | [**SQL Server hitelesítés**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication) | -A nem ISE és ISE SQL Server összekötőt is támogatja. <p><p>– A SQL Server létrehozott és tárolt érvényes felhasználónevet és erős jelszót igényel. <p>További információ: [SQL Server hitelesítés](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication). |
   |||

   Ez a példa a **Windows-hitelesítéssel** folytatódik:

   ![Válassza ki a használni kívánt hitelesítési típust](./media/connectors-create-api-sqlazure/select-windows-authentication.png)

1. Válassza ki vagy adja meg a következő értékeket az SQL Database-adatbázishoz:

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **SQL-kiszolgáló neve** | Yes | Az SQL-kiszolgáló címe, például: `Fabrikam-Azure-SQL.database.windows.net` |
   | **SQL-adatbázis neve** | Yes | A SQL Server-adatbázis neve, például: `Fabrikam-Azure-SQL-DB` |
   | **Felhasználónév** | Yes | Az SQL Server és az adatbázis felhasználóneve |
   | **Jelszó** | Yes | Az SQL Server és az adatbázis jelszava |
   | **Előfizetés** |  Igen, Windows-hitelesítéshez | A korábban az Azure-ban létrehozott adatátjáró-erőforráshoz tartozó Azure-előfizetés |
   | **Összekötő átjáró** | Igen, Windows-hitelesítéshez | Az Azure-ban korábban létrehozott adatátjáró-erőforrás neve <p><p>**Tipp**: Ha az átjáró nem jelenik meg a listában, ellenőrizze, hogy megfelelően [állította-e be az átjárót](../logic-apps/logic-apps-gateway-connection.md). |
   |||

   > [!TIP]
   > Ezeket az adatokat az adatbázisának a következőhöz tartozó összekapcsolási sztringje szerint találja:
   > 
   > * `Server={your-server-address}`
   > * `Database={your-database-name}`
   > * `User ID={your-user-name}`
   > * `Password={your-password}`

   Ez a példa az alábbi értékek megjelenését szemlélteti:

   ![SQL Server-kapcsolatok létrehozása befejeződött](./media/connectors-create-api-sqlazure/sql-server-create-connection-complete.png)

1. Ha elkészült, válassza a **Létrehozás** lehetőséget.

1. Most folytassa azokat a lépéseket, amelyek még nem fejeződött be [egy SQL-trigger hozzáadásával](#add-sql-trigger) vagy [egy SQL-művelet hozzáadásával](#add-sql-action).

<a name="add-sql-trigger"></a>

## <a name="add-a-sql-trigger"></a>SQL-trigger hozzáadása

1. A [Azure Portal](https://portal.azure.com) vagy a Visual Studióban hozzon létre egy üres logikai alkalmazást, amely megnyitja a Logic app designert. Ez a példa a Azure Portal folytatja.

1. A tervezőben a keresőmezőbe írja be a következőt: `sql server` . Az eseményindítók listából válassza ki a kívánt SQL-eseményindítót. Ez a példa **azt használja, hogy mikor hozzon létre triggert egy elemmel** .

   !["Az elem létrehozásakor" trigger kiválasztása](./media/connectors-create-api-sqlazure/select-sql-server-trigger.png)

1. Ha első alkalommal csatlakozik az SQL-adatbázishoz, a rendszer felszólítja az [SQL Database-kapcsolat létrehozására](#create-connection). A kapcsolatok létrehozása után folytathatja a következő lépéssel.

1. Az triggerben határozza meg az intervallumot és a gyakoriságot, hogy a trigger milyen gyakran ellenőrizze a táblát.

1. Ha további elérhető tulajdonságokat szeretne hozzáadni ehhez az triggerhez, nyissa meg az **új paraméterek hozzáadása** listát.

   Ez az trigger csak egy sort ad vissza a kijelölt táblából, és semmi más nem. Más feladatok végrehajtásához folytassa a műveletet egy SQL- [összekötő](#add-sql-action) vagy egy [másik művelet](../connectors/apis-list.md) hozzáadásával, amely a logikai alkalmazás munkafolyamatában kívánt következő feladatot hajtja végre.

   Ha például meg szeretné tekinteni az ebben a sorban lévő adatok megjelenítését, hozzáadhat más műveleteket is, amelyek a visszaadott sorból származó mezőket tartalmazzák, majd e-mail-riasztásokat küldenek. Ha többet szeretne megtudni az összekötőhöz elérhető egyéb műveletekről, tekintse [meg az összekötő hivatkozási oldalát](/connectors/sql/).

1. A tervező eszköztárán válassza a **Mentés** lehetőséget.

   Bár ez a lépés automatikusan engedélyezi és közzéteszi a logikai alkalmazást az Azure-ban, az egyetlen művelet, amelyet a logikai alkalmazás jelenleg használ, a megadott intervallum és gyakoriság alapján vizsgálja meg az adatbázist.

<a name="trigger-recurrence-shift-drift"></a>

### <a name="trigger-recurrence-shift-and-drift"></a>Eseményindító ismétlődésének eltolása és eltolódása

A kapcsolódáson alapuló eseményindítók, amelyekben először létre kell hozni egy kapcsolatokat, például az SQL-eseményindítót, eltérnek a Azure Logic Apps natív módon futó beépített eseményindítóinak, például az [ismétlődési eseményindítótól](../connectors/connectors-native-recurrence.md). Ismétlődő kapcsolaton alapuló eseményindítók esetében az ismétlődés ütemezése nem az egyetlen illesztőprogram, amely a végrehajtást vezérli, és az időzóna csak a kezdeti kezdési időpontot határozza meg. A későbbi futtatások az ismétlődési ütemtervtől, a legutóbbi indítás végrehajtástól *és* más olyan tényezőktől függenek, amelyek futási időket okozhatnak, vagy váratlan viselkedést okoznak, például nem tartanak fenn a megadott ütemtervet, amikor a nyári időmegtakarítás (DST) elindul és véget ér. Annak ellenőrzéséhez, hogy az ismétlődési idő ne legyen átváltva a DST életbe léptetéséhez, manuálisan módosítsa az ismétlődést, hogy a logikai alkalmazás továbbra is a várt időpontban fusson. Ellenkező esetben a kezdési idő egy óra elteltével, a DST indításakor és egy órával a DST végén halad át. További információ: [Ismétlődés a kapcsolatok alapú eseményindítók esetében](../connectors/apis-list.md#recurrence-connection-based).

<a name="add-sql-action"></a>

## <a name="add-a-sql-action"></a>SQL-művelet hozzáadása

Ebben a példában a logikai alkalmazás az [ismétlődési eseményindítóval](../connectors/connectors-native-recurrence.md)kezdődik, és olyan műveletet hív meg, amely egy SQL-adatbázisból származó sort kap.

1. A [Azure Portal](https://portal.azure.com) vagy a Visual Studióban nyissa meg a logikai alkalmazást a Logic app Designerben. Ez a példa folytatja a Azure Portal.

1. Az aktiválás vagy művelet alatt, ahol az SQL-műveletet hozzá szeretné adni, válassza az **új lépés** lehetőséget.

   ![Művelet hozzáadása a logikai alkalmazáshoz](./media/connectors-create-api-sqlazure/select-new-step-logic-app.png)

   Vagy a meglévő lépések közötti művelet hozzáadásához vigye az egeret a csatlakozás nyíl fölé. Válassza ki a **+** megjelenő pluszjelet (), majd válassza a **művelet hozzáadása** lehetőséget.

1. A **válasszon műveletet** területen a keresőmezőbe írja be a kifejezést `sql server` . A műveletek listából válassza ki a kívánt SQL-műveletet. Ez a példa a **sor beolvasása** műveletet használja, amely egyetlen rekordot kap.

   ![Válassza az SQL "sor beolvasása" műveletet](./media/connectors-create-api-sqlazure/select-sql-get-row-action.png)

1. Ha első alkalommal csatlakozik az SQL-adatbázishoz, a rendszer felszólítja az [SQL Database-kapcsolat létrehozására](#create-connection). A kapcsolatok létrehozása után folytathatja a következő lépéssel.

1. Válassza ki a **táblázat nevét**, amely `SalesLT.Customer` ebben a példában szerepel. Adja meg a kívánt rekordhoz tartozó **sor azonosítóját** .

   ![Válassza ki a táblanév nevet, és adja meg a sor AZONOSÍTÓját](./media/connectors-create-api-sqlazure/specify-table-row-id.png)

   Ez a művelet csak egy sort ad vissza a kijelölt táblából, semmi más. Így az ebben a sorban lévő adatok megtekintéséhez hozzáadhat más műveleteket is, amelyek a visszaadott sorból származó mezőket tartalmazzák, és a fájlt egy felhőalapú Storage-fiókban tárolják. Ha többet szeretne megtudni az összekötőhöz elérhető egyéb műveletekről, tekintse [meg az összekötő hivatkozási oldalát](/connectors/sql/).

1. Ha elkészült, a tervező eszköztárán válassza a **Mentés** lehetőséget.

   Ez a lépés automatikusan engedélyezi és közzéteszi a logikai alkalmazást az Azure-ban.

<a name="handle-bulk-data"></a>

## <a name="handle-bulk-data"></a>Tömeges adatkezelés

Időnként úgy kell dolgoznia az eredményhalmazt, hogy az összekötő ne adja vissza az összes eredményt egyszerre, vagy az eredményhalmaz méretének és szerkezetének jobb szabályozását szeretné. A következő módszerekkel kezelheti az ilyen nagy eredményeket:

* Az eredmények kisebb készletekként való kezeléséhez kapcsolja be a *tördelést*. További információ: [tömeges adatok, rekordok és elemek beolvasása a tördelés használatával](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md). További információ: [az SQL-Lapozás a tömeges adatátvitelhez a Logic apps](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx).

* Hozzon létre egy [*tárolt eljárást*](/sql/relational-databases/stored-procedures/stored-procedures-database-engine) , amely a kívánt módon rendezi az eredményeket. Az SQL-összekötő számos háttérbeli funkciót biztosít, amelyeket Azure Logic Apps használatával érhet el, így könnyebben automatizálhatja az SQL Database-táblákkal működő üzleti feladatokat.

  Több sor beolvasása vagy beillesztése esetén a logikai [alkalmazás ezen](../logic-apps/logic-apps-limits-and-config.md)sorokon keresztül is megismételheti [*ezeket a sorokat*](../logic-apps/logic-apps-control-flow-loops.md#until-loop) . Ha azonban a logikai alkalmazásnak a rekordhalmazokkal együtt kell működnie, például több ezer vagy több millió sorból, akkor az adatbázis felé irányuló hívások költségeinek minimalizálására van szükség.

  Az eredmények a kívánt módon történő rendezéséhez létrehozhat egy tárolt eljárást, amely az SQL-példányban fut, és a **Select-Order by** utasítást használja. Ez a megoldás az eredmények méretének és szerkezetének nagyobb mértékű szabályozását teszi lehetővé. A logikai alkalmazás meghívja a tárolt eljárást az SQL Server-összekötő által **tárolt eljárás végrehajtása** művelettel. További információ: [Select-ORDER BY záradék](/sql/t-sql/queries/select-order-by-clause-transact-sql).

  > [!NOTE]
  > Az SQL-összekötő tárolt eljárásának időtúllépési korlátja [kevesebb, mint 2 perc](/connectors/sql/#known-issues-and-limitations). Előfordulhat, hogy egyes tárolt eljárások a korlátnál hosszabb ideig tartanak, ami `504 Timeout` hibát okoz. A probléma megkerüléséhez használhatja az SQL-befejezési triggert, a natív SQL pass-through lekérdezést, egy állapotinformációkat és a kiszolgálóoldali feladatokat.
  > 
  > Ehhez a feladathoz használhatja a [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md) [Azure rugalmas feladat ügynökét](../azure-sql/database/elastic-jobs-overview.md) . A [helyszíni](/sql/sql-server/sql-server-technical-documentation) és az [Azure SQL felügyelt példányának](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)SQL Server használhatja a [SQL Server Agent](/sql/ssms/agent/sql-server-agent). További információért lásd: [a hosszú ideig futó tárolt eljárási időtúllépések kezelése a Azure Logic apps SQL-összekötőben](../logic-apps/handle-long-running-stored-procedures-sql-connector.md).

### <a name="handle-dynamic-bulk-data"></a>Dinamikus tömeges adatmennyiség kezelése

Ha tárolt eljárást hív meg az SQL Server-összekötő használatával, a visszaadott kimenet néha dinamikus. Ebben az esetben kövesse az alábbi lépéseket:

1. A [Azure Portalban](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic app Designerben.

1. A kimeneti formátum megtekintésével futtasson egy tesztet. Másolja ki és mentse a minta kimenetét.

1. A tervezőben azon művelet alatt, ahol a tárolt eljárást hívja, válassza az **új lépés** lehetőséget.

1. A **válasszon műveletet** területen keresse meg és válassza ki az [**elemzés JSON**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action) -műveletet.

1. A **JSON-elemzés** műveletben válassza a **minta hasznos adatok használata a séma létrehozásához** lehetőséget.

1. Az **írja be vagy illessze be a minta JSON-adattartalmat** mezőbe illessze be a minta kimenetét, és válassza a **kész** lehetőséget.

   > [!NOTE]
   > Ha olyan hibaüzenetet kap, amely Logic Apps nem tud sémát előállítani, ellenőrizze, hogy a minta kimenetének szintaxisa helyesen van-e formázva. Ha továbbra sem tudja előállítani a sémát, a **séma** mezőben adja meg manuálisan a sémát.

1. A tervező eszköztárán válassza a **Mentés** lehetőséget.

1. A JSON-tartalom tulajdonságaira való hivatkozáshoz kattintson a lista azon szerkesztési mezőire, amelyekben hivatkozni kíván ezekre a tulajdonságokra, hogy megjelenjen a dinamikus tartalmak listája. A listában a [**JSON**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action) elemzése alatt válassza ki a kívánt JSON-tartalom tulajdonságaihoz tartozó jogkivonatokat.

## <a name="troubleshoot-problems"></a>Problémák elhárítása

<a name="connection-problems"></a>

### <a name="connection-problems"></a>Csatlakozási problémák

A kapcsolódási problémák általában megtörténhetnek, így a hibák elhárításához és megoldásához tekintse át [a kapcsolódási hibák megoldását SQL Server](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server). Íme néhány példa:

* `A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.`

* `(provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server) (Microsoft SQL Server, Error: 53)`

* `(provider: TCP Provider, error: 0 - No such host is known.) (Microsoft SQL Server, Error: 11001)`

## <a name="connector-specific-details"></a>Összekötő-specifikus részletek

Az összekötő eseményindítókkal, műveletekkel és korlátozásokkal kapcsolatos technikai információkért tekintse [meg az összekötő hivatkozási oldalát](/connectors/sql/), amely a hencegés leírásában jön létre.

## <a name="next-steps"></a>Következő lépések

* További tudnivalók [a Azure Logic apps-összekötők](../connectors/apis-list.md) használatáról
