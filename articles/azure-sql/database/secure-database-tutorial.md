---
title: Adatbázis biztonságossá tétele
description: Ez az oktatóanyag bemutatja, hogyan védheti meg a Azure SQL Database, legyen szó akár önálló adatbázisról, akár a készletezett technológiákról és szolgáltatásokról.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: ''
ms.date: 09/21/2020
ms.custom: seoapril2019 sqldbrb=1
ms.openlocfilehash: 306a8d78caf36291573d021ed1d814aa4f7a2164
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2021
ms.locfileid: "105642766"
---
# <a name="tutorial-secure-a-database-in-azure-sql-database"></a>Oktatóanyag: adatbázis biztonságossá tétele Azure SQL Databaseban
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
>
> - Kiszolgálói szintű és adatbázis-szintű tűzfalszabályok létrehozása
> - Azure Active Directory (Azure AD) rendszergazdájának konfigurálása
> - Felhasználói hozzáférés kezelése SQL-hitelesítéssel, Azure AD-hitelesítéssel és biztonságos kapcsolati karakterláncokkal
> - Olyan biztonsági funkciók engedélyezése, mint például az SQL-hez készült Azure Defender, a naplózás, az adatmaszkolás és a titkosítás

A Azure SQL Database a következőket teszi biztonságossá:

- Hozzáférés korlátozása tűzfalszabályok használatával
- Identitást igénylő hitelesítési mechanizmusok használata
- Hitelesítés használata szerepköralapú tagságokkal és engedélyekkel
- Biztonsági funkciók engedélyezése

> [!NOTE]
> Az Azure SQL felügyelt példányát hálózati biztonsági szabályok és privát végpontok védik az [Azure SQL felügyelt példány](../managed-instance/sql-managed-instance-paas-overview.md) és [kapcsolat architektúrája](../managed-instance/connectivity-architecture-overview.md)című témakörben leírtak szerint.

További tudnivalókat a [Azure SQL Database biztonsági áttekintése](./security-overview.md) és [képességei](security-overview.md) című cikkben talál.

> [!TIP]
> A következő Microsoft Learn modul segít megtanulni az adatbázis biztonságossá tételét [Azure SQL Databaseban](/learn/modules/secure-your-azure-sql-database/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez győződjön meg arról, hogy rendelkezik a következő előfeltételekkel:

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
- Egy [kiszolgáló](logical-servers.md) és egy önálló adatbázis
  - Létrehozás a [Azure Portal](single-database-create-quickstart.md), a [CLI](az-cli-script-samples-content-guide.md)vagy a [PowerShell használatával](powershell-script-content-guide.md)

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Az oktatóanyagban szereplő összes lépésnél jelentkezzen be a [Azure Portalba](https://portal.azure.com/)

## <a name="create-firewall-rules"></a>Tűzfalszabályok létrehozása

A SQL Databaseban található adatbázisokat az Azure-beli tűzfalak védik. Alapértelmezés szerint a rendszer visszautasítja a kiszolgáló és az adatbázis összes kapcsolatát. További információ: [kiszolgálói szintű és adatbázis-szintű tűzfalszabályok](firewall-configure.md).

Állítsa **be az** **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** beállítást a legbiztonságosabb konfigurációhoz. Ezután hozzon létre egy [fenntartott IP-címet (klasszikus központi telepítést)](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) ahhoz az erőforráshoz, amelyhez csatlakoznia kell, például egy Azure-beli virtuális gépet vagy egy felhőalapú szolgáltatást, és csak az IP-cím elérését engedélyezze a tűzfalon keresztül. Ha a [Resource Manager](../../virtual-network/public-ip-addresses.md) -alapú üzemi modellt használja, az egyes erőforrásokhoz dedikált nyilvános IP-címet kell megadni.

> [!NOTE]
> Az SQL Database az 1433-as porton kommunikál. Ha vállalati hálózaton belülről próbál csatlakozni, lehetséges, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat az 1433-as porton keresztül. Ha igen, nem tud csatlakozni a kiszolgálóhoz, ha a rendszergazda megnyitja a 1433-es portot.

### <a name="set-up-server-level-firewall-rules"></a>Kiszolgálói szintű tűzfalszabályok beállítása

A kiszolgálói szintű IP-tűzfalszabályok az ugyanazon a kiszolgálón belüli összes adatbázisra érvényesek.

Kiszolgáló szintű tűzfalszabály beállítása:

1. A Azure Portal válassza az **SQL-adatbázisok** elemet a bal oldali menüben, majd válassza ki az adatbázist az **SQL-adatbázisok** lapon.

    ![kiszolgálói tűzfalszabály](./media/secure-database-tutorial/server-name.png)

    > [!NOTE]
    > Ügyeljen arra, hogy a teljes kiszolgálónevet (például *YourServer.database.Windows.net*) másolja az oktatóanyag későbbi részében való használatra.

1. Az **Áttekintés** lapon válassza a **kiszolgáló tűzfalának beállítása** lehetőséget. Ekkor megnyílik a kiszolgáló **tűzfalbeállítások** lapja.

   1. Válassza az **ügyfél IP-** címének hozzáadása lehetőséget az eszköztáron az aktuális IP-cím új tűzfalszabályként való hozzáadásához. A szabály az 1433-as portot egyetlen IP-cím vagy IP-címtartomány számára nyithatja meg. Kattintson a **Mentés** gombra.

      ![kiszolgálótűzfal-szabály beállítása](./media/secure-database-tutorial/server-firewall-rule2.png)

   1. Kattintson **az OK gombra** , és a **tűzfalbeállítások** oldal bezárásához.

Mostantól a kiszolgáló bármelyik adatbázisához csatlakozhat a megadott IP-címmel vagy IP-címtartománnyal.

### <a name="setup-database-firewall-rules"></a>Adatbázis-tűzfalszabályok beállítása

Az adatbázis-szintű tűzfalszabályok csak az egyes adatbázisokra érvényesek. Az adatbázis a kiszolgálók feladatátvétele során megőrzi ezeket a szabályokat. Az adatbázis-szintű tűzfalszabályok csak Transact-SQL (T-SQL) utasításokkal konfigurálhatók, és csak a kiszolgálói szintű tűzfalszabály konfigurálása után.

Adatbázis szintű tűzfalszabály beállítása:

1. Kapcsolódjon az adatbázishoz, például [SQL Server Management Studio](connect-query-ssms.md)használatával.

1. **Object Explorer** kattintson a jobb gombbal az adatbázisra, és válassza az **Új lekérdezés** elemet.

1. A lekérdezési ablakban adja hozzá ezt az utasítást, és módosítsa az IP-címet a nyilvános IP-címére:

    ```sql
    EXECUTE sp_set_database_firewall_rule N'Example DB Rule','0.0.0.4','0.0.0.4';
    ```

1. Az eszköztáron kattintson az **Execute (végrehajtás** ) elemre a tűzfalszabály létrehozásához.

> [!NOTE]
> A SSMS-ben a [sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database?view=azuresqldb-current&preserve-view=true) parancs használatával is létrehozhat egy kiszolgálói szintű tűzfalszabály-szabályt, de a *Master* adatbázishoz kell csatlakoznia.

## <a name="create-an-azure-ad-admin"></a>Azure AD-rendszergazda létrehozása

Győződjön meg arról, hogy a megfelelő Azure Active Directory (AD) felügyelt tartományt használja. Az AD-tartomány kiválasztásához használja a Azure Portal jobb felső sarkát. Ez a folyamat megerősíti, hogy ugyanazt az előfizetést használja az Azure AD-hez és az adatbázist vagy az adattárházat üzemeltető logikai SQL Serverhez is.

   ![Válasszon-ad](./media/secure-database-tutorial/8choose-ad.png)

Az Azure AD-rendszergazda beállítása:

1. A Azure Portal az **SQL Server** lapon válassza a **Active Directory rendszergazda** elemet. Ezután válassza a **rendszergazda beállítása** lehetőséget.

    ![az Active Directory kiválasztása](./media/secure-database-tutorial/admin-settings.png)  

    > [!IMPORTANT]
    > A feladat elvégzéséhez globális rendszergazdának kell lennie.

1. A **rendszergazda hozzáadása** lapon keresse meg és válassza ki az ad felhasználót vagy csoportot, majd válassza a **kiválasztás** lehetőséget. A Active Directory összes tagja és csoportja szerepel a felsorolásban, és a szürkén megadott bejegyzések nem támogatottak Azure AD-rendszergazdákként. Lásd: [Azure ad-szolgáltatások és korlátozások](authentication-aad-overview.md#azure-ad-features-and-limitations).

    ![rendszergazda kiválasztása](./media/secure-database-tutorial/admin-select.png)

    > [!IMPORTANT]
    > Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) csak a portálra vonatkozik, és a rendszer nem propagálja SQL Serverra.

1. A **Active Directory-rendszergazda** lap tetején válassza a **Mentés** lehetőséget.

    A rendszergazdák módosításának folyamata több percet is igénybe vehet. Az új rendszergazda megjelenik a **Active Directory felügyeleti** mezőben.

> [!NOTE]
> Az Azure AD-rendszergazda beállításakor az új rendszergazda neve (felhasználó vagy csoport) nem létezhet SQL Server bejelentkezési azonosítóként vagy felhasználóként a *Master* adatbázisban. Ha van ilyen, a telepítés sikertelen lesz, és visszaállítja a módosításokat, ami azt jelzi, hogy a rendszergazda neve már létezik. Mivel a SQL Server bejelentkezés vagy felhasználó nem része az Azure AD-nek, a felhasználó Azure AD-hitelesítéssel való összekapcsolására tett erőfeszítések sikertelenek.

Az Azure AD konfigurálásával kapcsolatos információkért lásd:

- [Helyszíni identitások integrálása az Azure AD-vel](../../active-directory/hybrid/whatis-hybrid-identity.md)
- [Saját tartománynév hozzáadása az Azure AD-hez](../../active-directory/fundamentals/add-custom-domain.md)
- [A Microsoft Azure mostantól támogatja a Windows Server Active Directoryval való összevonást](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)
- [Az Azure AD-címtár felügyelete](../../active-directory/fundamentals/active-directory-whatis.md)
- [Az Azure AD kezelése a PowerShell használatával](/powershell/azure/)
- [Hibrid identitáshoz szükséges portok és protokollok](../../active-directory/hybrid/reference-connect-ports.md)

## <a name="manage-database-access"></a>Adatbázis-hozzáférés kezelése

Az adatbázis-hozzáférés kezeléséhez felhasználókat adhat hozzá az adatbázishoz, vagy engedélyezheti a felhasználók hozzáférését a biztonságos kapcsolati karakterláncokkal. A külső alkalmazások esetében hasznos a kapcsolatok karakterlánca. További információ: a [bejelentkezések és a felhasználói fiókok kezelése](logins-create-manage.md) , valamint az [AD-hitelesítés](authentication-aad-overview.md).

Felhasználók hozzáadásához válassza ki az adatbázis-hitelesítés típusát:

- **SQL-hitelesítés**, Felhasználónév és jelszó használata a bejelentkezésekhez, és csak a kiszolgálón belüli adott adatbázis kontextusában érvényesek.

- **Azure ad-hitelesítés**, az Azure ad által felügyelt identitások használata

### <a name="sql-authentication"></a>SQL-hitelesítés

SQL-hitelesítéssel rendelkező felhasználó hozzáadása:

1. Kapcsolódjon az adatbázishoz, például [SQL Server Management Studio](connect-query-ssms.md)használatával.

1. **Object Explorer** kattintson a jobb gombbal az adatbázisra, és válassza az **Új lekérdezés** elemet.

1. A lekérdezési ablakban adja meg a következő parancsot:

    ```sql
    CREATE USER ApplicationUser WITH PASSWORD = 'YourStrongPassword1';
    ```

1. Az eszköztáron kattintson az **Execute (végrehajtás** ) elemre a felhasználó létrehozásához.

1. Alapértelmezés szerint a felhasználó csatlakozhat az adatbázishoz, de nem jogosult adatok olvasására vagy írására. Az engedélyek megadásához hajtsa végre a következő parancsokat egy új lekérdezési ablakban:

    ```sql
    ALTER ROLE db_datareader ADD MEMBER ApplicationUser;
    ALTER ROLE db_datawriter ADD MEMBER ApplicationUser;
    ```

> [!NOTE]
> Nem rendszergazdai fiókokat hozhat létre az adatbázis szintjén, kivéve, ha olyan rendszergazdai feladatokat kell végrehajtania, mint például az új felhasználók létrehozása.

### <a name="azure-ad-authentication"></a>Azure AD-hitelesítés

Azure Active Directory hitelesítéshez az adatbázis-felhasználók létrehozása szükséges. Egy tárolt adatbázis-felhasználó egy, az adatbázishoz társított Azure AD-címtárban található identitáshoz van társítva, és nincs bejelentkezés a *Master* adatbázisban. Az Azure AD-identitás lehet egyéni felhasználó vagy csoport is. További információ: a [tárolt adatbázis-felhasználók, az adatbázis hordozható,](/sql/relational-databases/security/contained-database-users-making-your-database-portable) valamint az Azure ad-vel történő hitelesítéssel kapcsolatos [Azure ad-oktatóanyag](authentication-aad-configure.md) áttekintése.

> [!NOTE]
> Az adatbázis-felhasználók (a rendszergazdák kivételével) nem hozhatók létre a Azure Portal használatával. Az Azure-szerepkörök nem terjednek ki az SQL-kiszolgálókra,-adatbázisokra vagy-adatraktárokra. Ezeket csak az Azure-erőforrások kezelésére használják, és nem vonatkoznak az adatbázis engedélyeire.
>
> A *SQL Server közreműködő* szerepkör például nem biztosít hozzáférést az adatbázishoz vagy az adattárházhoz való kapcsolódáshoz. Ezt az engedélyt T-SQL-utasítások használatával kell megadni az adatbázisban.

> [!IMPORTANT]
> `:` `&` A T-SQL `CREATE LOGIN` és a utasításokban nem támogatottak a speciális karakterek, például a kettőspont vagy az jel `CREATE USER` .

Azure AD-hitelesítéssel rendelkező felhasználó hozzáadása:

1. Kapcsolódjon az Azure-kiszolgálóhoz egy Azure AD-fiókkal legalább a *felhasználói engedély megváltoztatásával* .

1. **Object Explorer** kattintson a jobb gombbal az adatbázisra, és válassza az **Új lekérdezés** elemet.

1. A lekérdezési ablakban írja be a következő parancsot, és módosítsa az `<Azure_AD_principal_name>` Azure ad-felhasználó egyszerű nevére vagy az Azure ad-csoport megjelenítendő nevére:

   ```sql
   CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
   ```

> [!NOTE]
> Az Azure AD-felhasználók a csoportok típusával és típusával vannak megjelölve az adatbázis-metaadatokban `E (EXTERNAL_USER)` `X (EXTERNAL_GROUPS)` . További információ: [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql).

### <a name="secure-connection-strings"></a>Biztonságos kapcsolatok karakterláncai

Az ügyfélalkalmazás és a SQL Database közötti biztonságos, titkosított kapcsolat biztosításához a következőre kell konfigurálni a kapcsolati karakterláncot:

- Titkosított kapcsolatok kérése
- Nem bízik meg a kiszolgálói tanúsítványban

A rendszer Transport Layer Security (TLS) használatával hozza meg a kapcsolatokat, és csökkenti a támadók általi támadás kockázatát. A kapcsolódási karakterláncok adatbázison keresztül érhetők el, és előre konfigurálva vannak az olyan ügyfelek illesztőprogramjainak támogatásához, mint például a ADO.NET, a JDBC, az ODBC és a PHP. További információ a TLS-ről és a kapcsolatokról: [A TLS megfontolandó szempontjai](connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity).

Biztonságos kapcsolódási sztring másolása:

1. A Azure Portal válassza az **SQL-adatbázisok** elemet a bal oldali menüben, majd válassza ki az adatbázist az **SQL-adatbázisok** lapon.

1. Az **Áttekintés** lapon válassza az **adatbázis-kapcsolati karakterláncok megjelenítése** lehetőséget.

1. Válassza ki az illesztőprogram fület, és másolja a teljes hálózati karakterláncot.

    ![ADO.NET kapcsolati sztring](./media/secure-database-tutorial/connection.png)

## <a name="enable-security-features"></a>Biztonsági funkciók engedélyezése

A Azure SQL Database a Azure Portal használatával elérhető biztonsági funkciókat biztosít. Ezek a funkciók mind az adatbázis, mind a kiszolgáló számára elérhetők, kivéve az adatmaszkolást, amely csak az adatbázison érhető el. További információ: [Az Azure Defender for SQL](azure-defender-for-sql.md), a [naplózás](../../azure-sql/database/auditing-overview.md), a [dinamikus adatmaszkolás](dynamic-data-masking-overview.md)és az [transzparens adattitkosítás](transparent-data-encryption-tde-overview.md).

### <a name="azure-defender-for-sql"></a>Azure Defender SQL-hez

Az Azure Defender for SQL funkció észleli a lehetséges veszélyforrásokat, és biztonsági riasztásokat biztosít a rendellenes tevékenységekről. A felhasználók a naplózási funkcióval megismerhetik ezeket a gyanús eseményeket, és meghatározhatják, hogy az esemény az adatbázisban való hozzáféréshez, az illetéktelen behatoláshoz vagy a biztonsági rés kihasználásához kapcsolódott-e. A felhasználók biztonsági áttekintést is kapnak, amely tartalmazza a sebezhetőségi felmérést, valamint az adatfelderítési és besorolási eszközt.

> [!NOTE]
> Ilyen veszélyforrás például az SQL-injektálás, amely során a támadók rosszindulatú SQL-adatokat szúrnak be az alkalmazásba. Egy alkalmazás ezután nem tudja végrehajtani a rosszindulatú SQL-t, és lehetővé teszi a támadók számára, hogy hozzáférjenek az adatbázishoz, vagy módosíthassák azokat.

Az Azure Defender for SQL engedélyezése:

1. A Azure Portal válassza az **SQL-adatbázisok** elemet a bal oldali menüben, majd válassza ki az adatbázist az **SQL-adatbázisok** lapon.

1. Az **Áttekintés** lapon válassza a **kiszolgáló neve** hivatkozást. Ekkor megnyílik a kiszolgáló lap.

1. Az **SQL Server** oldalon keresse meg a **Biztonság** szakaszt, és válassza a **Security Center** elemet.

   1. A **funkció engedélyezéséhez válassza az** **Azure Defender for SQL** lehetőséget. Válasszon egy Storage-fiókot a sebezhetőségi felmérés eredményeinek mentéséhez. Kattintson a **Mentés** gombra.

      ![Navigációs panel](./media/secure-database-tutorial/threat-settings.png)

      Az e-maileket is konfigurálhatja a biztonsági riasztások, a tárolási adatok és a veszélyforrások észlelési típusai fogadására.

1. Térjen vissza az adatbázis **SQL-adatbázisok** lapjára, és válassza a biztonság szakaszban **a** **Security Center** elemet. Itt számos, az adatbázishoz elérhető biztonsági mutatót talál.

    ![Fenyegetés állapota](./media/secure-database-tutorial/threat-status.png)

Ha a rendszer rendellenes tevékenységeket észlel, az eseményre vonatkozó információkat tartalmazó e-mailt kap. Ez magában foglalja a tevékenység, az adatbázis, a kiszolgáló, az esemény időpontja, a lehetséges okok és a javasolt műveletek természetét a lehetséges fenyegetés kivizsgálásához és enyhítéséhez. Ha ilyen e-mailt kap, válassza az **Azure SQL-naplózási napló** hivatkozást, hogy elindítsa a Azure Portal és megjeleníti a kapcsolódó naplózási rekordokat az esemény időpontjában.

   ![Fenyegetésészlelési e-mail](./media/secure-database-tutorial/threat-email.png)

### <a name="auditing"></a>Naplózás

Az auditálási szolgáltatás nyomon követi az adatbázis eseményeit, és az eseményeket egy Azure Storage-ban, Azure Monitor naplókban vagy egy Event hub-naplóba írja. A naplózás segít a jogszabályi megfelelőség fenntartásában és az adatbázison végzett tevékenység megértésében, valamint a lehetséges biztonsági problémákat jelző rendellenességek feltárásában.

A naplózás engedélyezése:

1. A Azure Portal válassza az **SQL-adatbázisok** elemet a bal oldali menüben, majd válassza ki az adatbázist az **SQL-adatbázisok** lapon.

1. A **Biztonság** szakaszban válassza a **naplózás** lehetőséget.

1. A **naplózási** beállítások területen állítsa be a következő értékeket:

   1. A **Naplózást** állítsa **BE** értékre.

   1. Válassza ki a **napló célhelyét** az alábbi módon:

       - **Storage**, egy Azure Storage-fiók, amely az eseménynaplókat menti, és *. xel* fájlként tölthető le.

          > [!TIP]
          > Ugyanazt a Storage-fiókot használja az összes auditált adatbázishoz, hogy a lehető legtöbbet hozza ki a jelentési sablonokból.

       - **Log Analytics**, amely automatikusan tárolja az eseményeket lekérdezés vagy további elemzés céljából

           > [!NOTE]
           > **Log Analytics munkaterület** szükséges a speciális funkciók, például az elemzés, az egyéni riasztási szabályok és az Excel-vagy Power bi-exportálás támogatásához. Munkaterület nélkül csak a lekérdezés-szerkesztő érhető el.

       - **Event hub**, amely lehetővé teszi, hogy az események más alkalmazásokban való használatra legyenek átirányítva

   1. Kattintson a **Mentés** gombra.

      ![Naplózási beállítások](./media/secure-database-tutorial/audit-settings.png)

1. Most kiválaszthatja a **naplózási naplók megtekintése** lehetőséget az adatbázis-események adatmegjelenítéséhez.

    ![Naplórekordok](./media/secure-database-tutorial/audit-records.png)

> [!IMPORTANT]
> A naplózási események további testreszabásáról a PowerShell vagy a REST API használatával [SQL Database](../../azure-sql/database/auditing-overview.md) .

### <a name="dynamic-data-masking"></a>Dinamikus adatmaszkolás

Az adatmaszkolási funkció automatikusan elrejti a bizalmas adatokat az adatbázisban.

Az adatmaszkolás engedélyezése:

1. A Azure Portal válassza az **SQL-adatbázisok** elemet a bal oldali menüben, majd válassza ki az adatbázist az **SQL-adatbázisok** lapon.

1. A **Biztonság** szakaszban válassza a **dinamikus adatmaszkolás** lehetőséget.

1. A **dinamikus adatmaszkolási** beállítások területen válassza a **maszk hozzáadása** lehetőséget a maszkolási szabály hozzáadásához. Az Azure automatikusan feltölti az elérhető adatbázis-sémákat, táblákat és oszlopokat, amelyek közül választhat.

    ![Maszk beállításai](./media/secure-database-tutorial/mask-settings.png)

1. Kattintson a **Mentés** gombra. A kiválasztott információk mostantól az adatvédelem során maszkoltak.

    ![Példa maszkra](./media/secure-database-tutorial/mask-query.png)

### <a name="transparent-data-encryption"></a>Transzparens adattitkosítás

A titkosítási funkció automatikusan titkosítja az adatait, és nem igényel módosításokat a titkosított adatbázishoz hozzáférő alkalmazásokban. Az új adatbázisok esetében a titkosítás alapértelmezés szerint be van kapcsolva. Az SSMS és az [Always encrypted](always-encrypted-certificate-store-configure.md) funkció használatával is titkosíthatja az adattitkosítást.

A titkosítás engedélyezése vagy ellenőrzése:

1. A Azure Portal válassza az **SQL-adatbázisok** elemet a bal oldali menüben, majd válassza ki az adatbázist az **SQL-adatbázisok** lapon.

1. A **Biztonság** szakaszban válassza az **transzparens adattitkosítás** lehetőséget.

1. Ha szükséges, állítsa be az **adattitkosítást** **a** következőre:. Kattintson a **Mentés** gombra.

    ![Transzparens adattitkosítás](./media/secure-database-tutorial/encryption-settings.png)

> [!NOTE]
> A titkosítási állapot megtekintéséhez kapcsolódjon az adatbázishoz az [SSMS](connect-query-ssms.md) használatával, és kérdezze le a `encryption_state` [sys.dm_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) nézet oszlopát. A állapot `3` azt jelzi, hogy az adatbázis titkosítva van.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogy csak néhány egyszerű lépéssel javítsa az adatbázis biztonságát. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
>
> - Kiszolgálói szintű és adatbázis-szintű tűzfalszabályok létrehozása
> - Azure Active Directory (AD) rendszergazda konfigurálása
> - Felhasználói hozzáférés kezelése SQL-hitelesítéssel, Azure AD-hitelesítéssel és biztonságos kapcsolati karakterláncokkal
> - Olyan biztonsági funkciók engedélyezése, mint például az SQL-hez készült Azure Defender, a naplózás, az adatmaszkolás és a titkosítás

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan valósítható meg a Geo-eloszlás.

> [!div class="nextstepaction"]
>[Földrajzilag elosztott adatbázis implementálása](geo-distributed-application-configure-tutorial.md)