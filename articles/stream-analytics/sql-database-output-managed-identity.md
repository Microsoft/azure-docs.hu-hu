---
title: Felügyelt identitások használata Azure SQL Database vagy Azure szinapszis Analytics-Azure Stream Analytics eléréséhez
description: Ez a cikk azt ismerteti, hogyan használhatók a felügyelt identitások a Azure Stream Analytics-feladatok hitelesítéséhez Azure SQL Database vagy az Azure szinapszis Analytics kimenetéhez.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 11/30/2020
ms.openlocfilehash: e491c421f4af256b2e74fa61eb442d269bdb9e34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "102487916"
---
# <a name="use-managed-identities-to-access-azure-sql-database-or-azure-synapse-analytics-from-an-azure-stream-analytics-job-preview"></a>Felügyelt identitások használata Azure SQL Database vagy Azure szinapszis Analytics eléréséhez egy Azure Stream Analytics feladatokból (előzetes verzió)

A Azure Stream Analytics támogatja a [felügyelt identitások hitelesítését](../active-directory/managed-identities-azure-resources/overview.md) a Azure SQL Database és az Azure szinapszis Analytics kimeneti nyelők számára. A felügyelt identitások megszüntetik a felhasználó-alapú hitelesítési módszerek korlátozásait, például a jelszó-változtatások vagy a felhasználói jogkivonatok lejárata miatti újrahitelesítés szükségességét, amely minden 90 naponként megtörténik. Ha eltávolítja a manuális hitelesítés szükségességét, a Stream Analytics üzemelő példányok teljes mértékben automatizálva lehetnek.

A felügyelt identitás egy Azure Active Directoryban regisztrált felügyelt alkalmazás, amely egy adott Stream Analytics feladatnak felel meg. A felügyelt alkalmazás használatával történik a hitelesítés egy célként megadott erőforráson. Ez a cikk bemutatja, hogyan engedélyezheti a felügyelt identitást egy Azure SQL Database vagy egy Stream Analytics-feladatokhoz tartozó Azure szinapszis analitikai kimenet (ek) számára a Azure Portalon keresztül.

## <a name="overview"></a>Áttekintés

Ebből a cikkből megtudhatja, hogy milyen lépésekkel csatlakozhat a Stream Analytics-feladatokhoz a Azure SQL Database vagy az Azure szinapszis Analytics SQL-készlethez a felügyelt identitás-hitelesítési mód használatával. 

- Először létre kell hoznia egy rendszerhez rendelt felügyelt identitást a Stream Analytics feladathoz. Ez az Ön feladata Azure Active Directoryban.  

- Vegyen fel egy Active Directory rendszergazdát az SQL Server vagy a szinapszis munkaterületre, amely lehetővé teszi az Azure AD (felügyelt identitás) hitelesítését az adott erőforráshoz.

- Ezután hozzon létre egy tárolt felhasználót, amely a Stream Analytics-feladatokhoz tartozó identitást jelképezi az adatbázisban. Ha a Stream Analytics-feladatot az SQL-adatbázis vagy a szinapszis SQL DB-erőforrással együtt használja, akkor ez az identitás, amelyet a Stream Analytics-feladatokhoz tartozó engedélyek ellenőrzéséhez fog hivatkozni.

- Engedélyek megadása a Stream Analyticsi feladatnak a SQL Database vagy a szinapszis SQL-készletek eléréséhez.

- Végül adja hozzá a Azure SQL Database/Azure szinapszis-elemzést a Stream Analytics feladatokban szereplő kimenetként.

## <a name="prerequisites"></a>Előfeltételek

#### <a name="azure-sql-database"></a>[Azure SQL Database](#tab/azure-sql)

A funkció használatához a következők szükségesek:

- Egy Azure Stream Analytics-feladatot.

- Egy Azure SQL Database erőforrás.

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

A funkció használatához a következők szükségesek:

- Egy Azure Stream Analytics-feladatot.

- Egy Azure szinapszis Analytics SQL-készlet.

- Egy Azure Storage-fiók, amely [a stream Analytics feladatokhoz van konfigurálva](azure-synapse-analytics-output.md).

- Megjegyzés: Stream Analytics a szinapszis SQL MSI-vel integrált fiók Storage MSI jelenleg nem érhető el.

---

## <a name="create-a-managed-identity"></a>Felügyelt identitás létrehozása

Először létre kell hoznia egy felügyelt identitást a Azure Stream Analytics feladatokhoz.

1. A [Azure Portal](https://portal.azure.com)nyissa meg a Azure stream Analytics feladatot.

1. A bal oldali navigációs menüben válassza a **Konfigurálás** területen található **felügyelt identitás** elemet. Ezután jelölje be a **rendszerhez rendelt felügyelt identitás használata** melletti jelölőnégyzetet, majd válassza a **Mentés** lehetőséget.

   ![Rendszer által hozzárendelt felügyelt identitás kiválasztása](./media/sql-db-output-managed-identity/system-assigned-managed-identity.png)

   A Stream Analytics-feladatok identitásához tartozó egyszerű szolgáltatás a Azure Active Directoryban jön létre. Az újonnan létrehozott identitás életciklusát az Azure felügyeli. Ha a Stream Analytics feladatot törli, a társított identitást (azaz az egyszerű szolgáltatásnevet) az Azure automatikusan törli.

1. A konfiguráció mentésekor a szolgáltatásnév objektumazonosító (OID) szerepel a résztvevő AZONOSÍTÓJAként az alábbi ábrán látható módon: 

   ![Elsődleges AZONOSÍTÓként megjelenő objektumazonosító](./media/sql-db-output-managed-identity/principal-id.png)

   Az egyszerű szolgáltatásnév neve megegyezik a Stream Analytics feladatokkal. Ha például a feladatainak neve *MyASAJob*, az egyszerű szolgáltatásnév neve is *MyASAJob*.

## <a name="select-an-active-directory-admin"></a>Active Directory-rendszergazda kiválasztása

A felügyelt identitás létrehozása után ki kell választania egy Active Directory rendszergazdát.

1. Navigáljon a Azure SQL Database vagy az Azure szinapszis Analytics SQL Pool-erőforráshoz, és válassza ki az SQL Server vagy a szinapszis munkaterületet, amely az erőforrás alatt van. A hivatkozásokat az erőforrás-Áttekintés lapon találja, a *kiszolgálónév* vagy a *munkaterület neve* mellett.

1. Az SQL Server és a szinapszis munkaterület **Beállítások** területén válassza a **Active Directory rendszergazda** vagy az **SQL Active Directory rendszergazda** lehetőséget. Ezután válassza a **rendszergazda beállítása** lehetőséget.

   ![Active Directory felügyeleti oldal](./media/sql-db-output-managed-identity/active-directory-admin-page.png)

1. A Active Directory felügyeleti lapon keressen rá egy felhasználóra vagy csoportra, hogy a rendszergazda legyen a SQL Server, majd kattintson a **kiválasztás** gombra. Ez lesz az a felhasználó, aki a következő szakaszban létre tudja hozni a **tárolt adatbázis-felhasználót** .

   ![Active Directory adminisztrátor hozzáadása](./media/sql-db-output-managed-identity/add-admin.png)

   A Active Directory felügyeleti oldal megjeleníti a Active Directory összes tagját és csoportját. A szürke felhasználók vagy csoportok nem választhatók ki, mert Azure Active Directory rendszergazdákként nem támogatottak. Tekintse meg a támogatott rendszergazdák listáját a **Azure Active Directory szolgáltatások és korlátozások**   című szakaszban [Azure Active Directory hitelesítés használata a SQL Database vagy az Azure szinapszis használatával történő hitelesítéshez](../azure-sql/database/authentication-aad-overview.md#azure-ad-features-and-limitations).

1. A **Active Directory felügyeleti** lapon válassza a **Mentés** lehetőséget. A rendszergazda módosításának folyamata néhány percet vesz igénybe.

## <a name="create-a-contained-database-user"></a>Tárolt adatbázis-felhasználó létrehozása

Ezután létre kell hoznia egy tárolt adatbázis-felhasználót az Azure SQL-vagy az Azure szinapszis-adatbázisban, amely a Azure Active Directory identitásra van leképezve. A tárolt adatbázis-felhasználó nem rendelkezik bejelentkezési azonosítóval az elsődleges adatbázishoz, de az adatbázishoz társított címtárban található identitáshoz rendeli. A Azure Active Directory identitás lehet egyéni felhasználói fiók vagy csoport. Ebben az esetben létre kell hoznia egy tárolt adatbázis-felhasználót a Stream Analytics feladatokhoz. 

További információkért tekintse át a következő cikket az Azure AD-integráció hátteréhez: [univerzális hitelesítés a SQL Database és az Azure szinapszis Analytics használatával (SSMS-támogatás az MFA-hoz)](../azure-sql/database/authentication-mfa-ssms-overview.md)

1. Kapcsolódjon az Azure SQL vagy az Azure szinapszis-adatbázishoz SQL Server Management Studio használatával. A **Felhasználónév** egy Azure Active Directory felhasználó, aki módosítja a **felhasználó** engedélyeit. A SQL Server beállított rendszergazda egy példa. **Azure Active Directory – Universal és MFA** hitelesítés használata. 

   ![Csatlakozás az SQL Serverhez](./media/sql-db-output-managed-identity/connect-sql-server.png)

   A kiszolgáló neve eltérő lehet `<SQL Server name>.database.windows.net` a különböző régiókban. A kínai régiónak például a következőt kell használnia: `<SQL Server name>.database.chinacloudapi.cn` .
 
   Megadhat egy adott Azure SQL-vagy Azure-beli szinapszis-adatbázist, ha a **beállítások > kapcsolati tulajdonságok > kapcsolódás az adatbázishoz lehetőségre** kattint.  

   ![SQL Server a kapcsolatok tulajdonságai](./media/sql-db-output-managed-identity/sql-server-connection-properties.png)

1. Amikor első alkalommal kapcsolódik a szolgáltatáshoz, a következő ablak jelenhet meg:

   ![Új tűzfalszabály ablak](./media/sql-db-output-managed-identity/new-firewall-rule.png)

   1. Ha igen, lépjen a SQL Server/szinapszis munkaterület-erőforrásra a Azure Portal. A **Biztonság** szakaszban nyissa meg a **tűzfalak és a virtuális hálózat/tűzfalak** lapot. 
   1. Adjon hozzá egy új szabályt a szabály nevével.
   1. Használja a *from* IP címet a *Start IP-* címhez tartozó **Új tűzfalszabály** ablakában.
   1. A *záró IP*-címhez használja az **Új tűzfalszabály** ablakának *IP-* címét. 
   1. Válassza a **Mentés** lehetőséget, majd próbálkozzon újra a SQL Server Management Studio való kapcsolódással. 

1. A csatlakozás után hozza létre a tárolt adatbázis-felhasználót. A következő SQL-parancs egy olyan tárolt adatbázis-felhasználót hoz létre, amelynek a neve megegyezik a Stream Analytics feladatokkal. Ügyeljen rá, hogy a zárójelek szerepeljenek a *ASA_JOB_NAMEban*. Használja a következő T-SQL szintaxist, és futtassa a lekérdezést. 

   ```sql
   CREATE USER [ASA_JOB_NAME] FROM EXTERNAL PROVIDER; 
   ```
   
    Annak ellenőrzéséhez, hogy helyesen adta-e hozzá a tárolt adatbázis felhasználóját, futtassa a következő parancsot a SSMS-ben a kapcsolódó adatbázis területen, és ellenőrizze, hogy a *ASA_JOB_NAME* a "Name" (név) oszlopban van-e.

   ```sql
   SELECT * FROM <SQL_DB_NAME>.sys.database_principals 
   WHERE type_desc = 'EXTERNAL_USER' 
   ```

1. Ahhoz, hogy a Microsoft Azure Active Directory ellenőrizze, hogy a Stream Analytics-feladatot elérheti-e a SQL Database, Azure Active Directory engedélyt kell adni az adatbázissal való kommunikációra. Ehhez lépjen a "tűzfalak és virtuális hálózat"/"tűzfalak" lapra Azure Portal újra, és engedélyezze az "Azure-szolgáltatások és-erőforrások elérésének engedélyezése ehhez a kiszolgálóhoz/munkaterülethez" lehetőséget.

   ![Tűzfal és virtuális hálózat](./media/sql-db-output-managed-identity/allow-access.png)

## <a name="grant-stream-analytics-job-permissions"></a>Stream Analyticsi feladatok engedélyeinek megadása

#### <a name="azure-sql-database"></a>[Azure SQL Database](#tab/azure-sql)

Miután létrehozott egy tárolt adatbázis-felhasználót, és hozzáférést kapott az Azure-szolgáltatásokhoz a portálon az előző szakaszban leírtak szerint, a Stream Analyticsi feladatnak rendelkeznie kell a felügyelt identitással, hogy felügyelt identitáson keresztül **csatlakozhasson** az Azure SQL Database-erőforráshoz. Javasoljuk, hogy adja meg a **Select** és **INSERT** engedélyeket a stream Analytics-feladathoz, mivel a stream Analytics munkafolyamatban később szükség lesz rájuk. A **Select** engedély lehetővé teszi a feladatoknak az Azure SQL Database-ben lévő táblához való kapcsolódásának tesztelését. Az **Insert** engedély lehetővé teszi a végpontok közötti stream Analytics lekérdezések tesztelését, miután beállította a bemenetet és az Azure SQL Database kimenetét.

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

Miután létrehozott egy tárolt adatbázis-felhasználót, és hozzáférést kapott az Azure-szolgáltatásokhoz a portálon az előző szakaszban leírtak szerint, a Stream Analyticsi feladatnak rendelkeznie kell engedéllyel a felügyelt identitástól az Azure szinapszis adatbázis-erőforráshoz való **kapcsolódáshoz** a felügyelt identitáson keresztül. Javasoljuk, hogy az **adatbázis TÖMEGES műveleteinek** **kiválasztása**, **beszúrása** és felügyelete engedélyeit a stream Analytics feladathoz adja meg, mivel a stream Analytics munkafolyamatban később szükség lesz rájuk. A **Select** engedély lehetővé teszi a feladatoknak, hogy tesztelje a kapcsolatát az Azure szinapszis-adatbázisban lévő táblával. Az **adatbázis TÖMEGES műveleteinek** **beszúrása** és felügyelete lehetővé teszi a végpontok közötti stream Analytics lekérdezések tesztelését, miután konfigurálta a bemenetet és az Azure szinapszis-adatbázis kimenetét.

Ahhoz, hogy a **felügyeleti adatbázis TÖMEGES műveletei** engedélyt kapjanak, meg kell adnia minden olyan engedélyt, amely a **vezérlés alá tartozó vezérlőelemként** van megjelölve [a stream Analytics](/sql/t-sql/statements/grant-database-permissions-transact-sql?view=azure-sqldw-latest&preserve-view=true#remarks) feladatra vonatkozóan. Erre az engedélyre azért van szükség, mert a Stream Analytics feladat végrehajtja a **copy** utasítást, amely megköveteli az [adatbázis tömeges MŰVELETEInek és beszúrásának felügyeletét](/sql/t-sql/statements/copy-into-transact-sql).

---

Ezeket az engedélyeket SQL Server Management Studio használatával adhatja meg a Stream Analytics feladatnak. További információért lásd a GRANT (Transact-SQL) referenciát.

Ha csak egy adott táblához vagy objektumhoz kíván engedélyeket adni az adatbázisban, használja a következő T-SQL szintaxist, és futtassa a lekérdezést. 

#### <a name="azure-sql-database"></a>[Azure SQL Database](#tab/azure-sql)

```sql
GRANT CONNECT, SELECT, INSERT ON OBJECT::TABLE_NAME TO ASA_JOB_NAME;
```

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

```sql
GRANT CONNECT, SELECT, INSERT, CONTROL, ADMINISTER DATABASE BULK OPERATIONS OBJECT::TABLE_NAME TO ASA_JOB_NAME;
```

---

Azt is megteheti, hogy a jobb gombbal az Azure SQL vagy az Azure szinapszis-adatbázisra kattint SQL Server Management Studio és kiválasztja a **tulajdonságok > engedélyeket**. Az engedélyek menüben megtekintheti a korábban hozzáadott Stream Analytics feladatot, és manuálisan is engedélyezheti vagy megtagadhatja az engedélyeket.

Ha szeretné megtekinteni a *ASA_JOB_NAME* felhasználóhoz hozzáadott összes engedélyt, futtassa a következő parancsot a SSMS a vonatkozó adatbázis alatt: 

```sql
SELECT dbprin.name, dbprin.type_desc, dbperm.permission_name, dbperm.state_desc, dbperm.class_desc, object_name(dbperm.major_id) 
FROM sys.database_principals dbprin 
LEFT JOIN sys.database_permissions dbperm 
ON dbperm.grantee_principal_id = dbprin.principal_id 
WHERE dbprin.name = '<ASA_JOB_NAME>' 
```

## <a name="create-an-azure-sql-database-or-azure-synapse-output"></a>Azure SQL Database vagy Azure szinapszis kimenet létrehozása

#### <a name="azure-sql-database"></a>[Azure SQL Database](#tab/azure-sql)

Most, hogy beállította a felügyelt identitását, készen áll Azure SQL Database vagy Azure szinapszis kimenet hozzáadására a Stream Analytics feladatokhoz.

Győződjön meg arról, hogy létrehozott egy táblát a SQL Database a megfelelő kimeneti sémával. A tábla neve azon szükséges tulajdonságok egyike, amelyeket ki kell tölteni, amikor hozzáadja a SQL Database kimenetet a Stream Analytics feladatokhoz. Továbbá győződjön meg arról, hogy a feladatokban **kiválasztott** és **beszúrt** engedélyeket a kapcsolódás teszteléséhez és stream Analytics lekérdezések futtatásához. Ha még nem tette meg, tekintse meg az [engedélyezési stream Analytics feladatok engedélyei](#grant-stream-analytics-job-permissions) szakaszt. 

1. Lépjen vissza a Stream Analytics feladathoz, és navigáljon a **kimenetek** lapra a **feladatok topológiája** alatt. 

1. Válassza a **hozzáadás > SQL Database** lehetőséget. A SQL Database kimeneti fogadó kimeneti tulajdonságok ablakában válassza a **felügyelt identitás** lehetőséget a hitelesítési mód legördülő menüből.

1. Adja meg a többi tulajdonságot. SQL Database kimenet létrehozásával kapcsolatos további tudnivalókért tekintse meg [a SQL Database kimenet létrehozása stream Analytics](sql-database-output.md)használatával című témakört. Ha elkészült, válassza a **Mentés** lehetőséget.

1. A **Save (Mentés**) gombra való kattintás után az erőforráshoz való kapcsolódási tesztet automatikusan el kell indítani. A sikeres befejezést követően sikeresen konfigurálta a Stream Analytics-feladatot, hogy a felügyelt identitás-hitelesítési mód használatával Azure SQL Database vagy szinapszis SQL Databasehoz kapcsolódjon. 

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

Most, hogy beállította a felügyelt identitás-és Storage-fiókját, készen áll egy Azure SQL Database vagy Azure szinapszis kimenet hozzáadására a Stream Analytics feladatokhoz.

Győződjön meg arról, hogy létrehozott egy táblát az Azure szinapszis-adatbázisban a megfelelő kimeneti sémával. A tábla neve azon szükséges tulajdonságok egyike, amelyeket ki kell tölteni, amikor az Azure szinapszis kimenetét hozzáadja a Stream Analytics feladatokhoz. Továbbá győződjön meg arról, hogy a feladatokban **kiválasztott** és **beszúrt** engedélyeket a kapcsolódás teszteléséhez és stream Analytics lekérdezések futtatásához. Ha még nem tette meg, tekintse meg az [engedélyezési stream Analytics feladatok engedélyei](#grant-stream-analytics-job-permissions) szakaszt.

1. Lépjen vissza a Stream Analytics feladathoz, és navigáljon a **kimenetek** lapra a **feladatok topológiája** alatt.

1. Válassza a **hozzáadás > Azure szinapszis Analytics** lehetőséget. A SQL Database kimeneti fogadó kimeneti tulajdonságok ablakában válassza a **felügyelt identitás** lehetőséget a hitelesítési mód legördülő menüből.

1. Adja meg a többi tulajdonságot. További információ az Azure szinapszis-kimenet létrehozásáról: [Az Azure szinapszis Analytics kimenete Azure stream Analytics](azure-synapse-analytics-output.md). Ha elkészült, válassza a **Mentés** lehetőséget.

1. A **Save (Mentés**) gombra való kattintás után az erőforráshoz való kapcsolódási tesztet automatikusan el kell indítani. A sikeres befejezést követően már készen áll arra, hogy az Azure szinapszis Analytics-erőforrás felügyelt identitását Stream Analytics használatával folytassa. 

---

## <a name="remove-managed-identity"></a>Felügyelt identitás eltávolítása

A Stream Analytics feladatokhoz létrehozott felügyelt identitást csak akkor törli a rendszer, ha a feladatot törlik. A felügyelt identitást nem lehet törölni a feladatok törlése nélkül. Ha már nem szeretné használni a felügyelt identitást, módosíthatja a kimenet hitelesítési módszerét. A felügyelt identitás továbbra is érvényben marad, amíg el nem törli a feladatot, és a rendszer akkor fogja használni, ha úgy dönt, hogy újra felügyelt identitás-hitelesítést használ.

## <a name="next-steps"></a>Következő lépések

* [A Azure Stream Analytics kimenetének megismerése](stream-analytics-define-outputs.md)
* [Azure Stream Analytics kimenet Azure SQL Database](stream-analytics-sql-output-perf.md)
* [Az Azure szinapszis Analytics kimenete Azure Stream Analytics](azure-synapse-analytics-output.md)
