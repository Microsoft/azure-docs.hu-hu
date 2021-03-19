---
title: Azure SQL Database regisztrálása és vizsgálata
description: Ez az oktatóanyag a Azure SQL Database vizsgálatát ismerteti
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 10/02/2020
ms.openlocfilehash: a708f0c1884073a2f2ea73092d25006415c59d5f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104655223"
---
# <a name="register-and-scan-an-azure-sql-database"></a>Azure SQL Database regisztrálása és vizsgálata

Ez a cikk azt ismerteti, hogyan regisztrálhat egy Azure SQL Database adatforrást a hatáskörébe, és hogyan állíthat be vizsgálatot.

## <a name="supported-capabilities"></a>Támogatott képességek

A Azure SQL Database adatforrás a következő funkciókat támogatja:

- **Teljes és növekményes vizsgálatok** a metaadatok és a besorolás Azure SQL Databaseban történő rögzítéséhez.

- Adategységek közötti **vonal** a másolási és a adatfolyam tevékenységekhez.

### <a name="known-limitations"></a>Ismert korlátozások

Az Azure-beli hatáskörébe nem támogatottak a Azure SQL Database [nézeteinek](/sql/relational-databases/views/views?view=azuresqldb-current&preserve-view=true) vizsgálata.

## <a name="prerequisites"></a>Előfeltételek

1. Ha még nem rendelkezik ilyennel, hozzon létre egy új hatáskörébe tartozó fiókot.

1. Hálózati hozzáférés a hatáskörébe tartozó fiók és a Azure SQL Database között.


### <a name="set-up-authentication-for-a-scan"></a>Hitelesítés beállítása vizsgálathoz

Hitelesítés Azure SQL Database vizsgálatához. Ha új hitelesítést kell létrehoznia, engedélyeznie kell az [adatbázis-hozzáférést SQL Databasehoz](../azure-sql/database/logins-create-manage.md). A hatáskörébe három hitelesítési módszer létezik:

- SQL-hitelesítés
- Szolgáltatásnév
- Felügyelt identitás

#### <a name="sql-authentication"></a>SQL-hitelesítés

> [!Note]
> Csak a kiszolgálói szintű rendszerbiztonsági tag (a kiépítési folyamat által létrehozott) vagy a `loginmanager` Master adatbázisban lévő adatbázis-szerepkör tagjai hozhatnak létre új bejelentkezéseket. Az engedélyek megadása után körülbelül **15 perccel** tart, a hatáskörébe tartozó fióknak rendelkeznie kell a megfelelő engedélyekkel az erőforrás (ok) vizsgálatához.

Ha nem rendelkezik ezzel a lehetőséggel, kövesse a [Bejelentkezés létrehozása](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true#examples-1) Azure SQL Database bejelentkezés létrehozásához című témakör utasításait. A következő lépésekhez szüksége lesz a **felhasználónévre** és a **jelszóra** .

1. Navigáljon a Key vaulthoz a Azure Portal
1. **Beállítások kiválasztása > titkok**
1. Válassza a **+ előállítás/importálás** lehetőséget, és adja meg a **nevet** és az **értéket** a Azure SQL Database *jelszavával*
1. A **Létrehozás** gombra kattintva fejezze be
1. Ha a kulcstartó még nem csatlakozik a hatáskörébe, [létre kell hoznia egy új Key Vault-kapcsolatot](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Végül [hozzon létre egy új hitelesítő adatot](manage-credentials.md#create-a-new-credential) a **Felhasználónév** és **jelszó** használatával a vizsgálat beállításához

#### <a name="service-principal-and-managed-identity"></a>Egyszerű szolgáltatásnév és felügyelt identitás

Számos lépéssel engedélyezheti a hatáskörébe, hogy a szolgáltatásnév vagy a hatáskörébe tartozó **felügyelt identitás** használatával ellenőrizze a Azure SQL Database

   > [!Note]
   > A hatáskörébe szüksége lesz az **alkalmazásra (ügyfél)** és az **ügyfél titkos** kódjára a vizsgálathoz.

##### <a name="create-or-use-an-existing-service-principal"></a>Meglévő egyszerű szolgáltatásnév létrehozása vagy használata

> [!Note]
> Hagyja ki ezt a lépést, ha a hatáskörébe tartozó **felügyelt identitást** használja

Egyszerű szolgáltatásnév használatához használhat egy meglévőt, vagy létrehozhat egy újat. 

> [!Note]
> Ha létre kell hoznia egy új szolgáltatásnevet, kövesse az alábbi lépéseket:
> 1. Navigáljon a [Azure Portal](https://portal.azure.com).
> 1. A bal oldali menüben válassza a **Azure Active Directory** lehetőséget.
> 1. Válassza az **Alkalmazásregisztrációk** lehetőséget.
> 1. Válassza az **+ új alkalmazás regisztrálása** lehetőséget.
> 1. Adja meg az **alkalmazás** nevét (az egyszerű szolgáltatásnév).
> 1. **Csak a szervezeti címtárban** válassza a fiókok elemet.
> 1. Az átirányítási URI esetében válassza a **web** lehetőséget, és adja meg a kívánt URL-címet. nem kell valós vagy munkahelyi.
> 1. Válassza a **Regisztráció** lehetőséget.

##### <a name="configure-azure-ad-authentication-in-the-database-account"></a>Az Azure AD-hitelesítés konfigurálása az adatbázis-fiókban

Az egyszerű szolgáltatásnak vagy a felügyelt identitásnak engedéllyel kell rendelkeznie az adatbázis, a sémák és a táblák metaadatainak beszerzéséhez. Emellett képesnek kell lennie a táblázatok lekérdezésére is a besoroláshoz.

- [Azure AD-hitelesítés konfigurálása és kezelése az Azure SQL-sel](../azure-sql/database/authentication-aad-configure.md)
- Ha felügyelt identitást használ, a hatáskörébe tartozó fióknak saját felügyelt identitása van, amely alapvetően a hatáskörébe tartozik a létrehozásakor. Létre kell hoznia egy Azure AD-felhasználót Azure SQL Database a hatáskörébe tartozó felügyelt identitással vagy a saját egyszerű szolgáltatásával, az [egyszerű szolgáltatás felhasználójának létrehozásához a Azure SQL Databaseban](../azure-sql/database/authentication-aad-service-principal-tutorial.md#create-the-service-principal-user-in-azure-sql-database). Az identitáshoz megfelelő engedélyeket (például vagy) kell rendelnie `db_owner` `db_datareader` . SQL-szintaxis – példa felhasználói és engedélyezési engedélyek létrehozására:

    ```sql
    CREATE USER [Username] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_owner', [Username]
    GO
    ```

    > [!Note]
    > Az a `Username` saját szolgáltatásnév vagy a hatáskörébe tartozó felügyelt identitás. További információk a [rögzített adatbázis-szerepkörökről és azok képességeiről](/sql/relational-databases/security/authentication-access/database-level-roles?view=sql-server-ver15&preserve-view=true#fixed-database-roles).
    
##### <a name="add-service-principal-to-key-vault-and-purviews-credential"></a>Egyszerű szolgáltatásnév hozzáadása a Key vaulthoz és a hatáskörébe hitelesítő adataihoz

> [!Note]
> Ha a hatáskörébe tartozó **felügyelt identitást** tervezi használni, kihagyhatja ezt a lépést, mert az alapértelmezett hatáskörébe felügyelt identitása már a **hatáskörébe-MSI** hitelesítő adatokkal van elválasztva.

Az egyszerű szolgáltatás alkalmazás-azonosítójának és titkos kódjának beszerzéséhez a következő szükséges:

1. Navigáljon az egyszerű szolgáltatáshoz a [Azure Portal](https://portal.azure.com)
1. Másolja az **alkalmazás (ügyfél) azonosítóját** az **Áttekintés** és az **ügyfél titkos kulcsa** a **tanúsítványok & Secrets** elemből.
1. Navigáljon a kulcstartóhoz
1. **Beállítások kiválasztása > titkok**
1. Válassza a **+ előállítás/importálás** lehetőséget, és adja meg az Ön által választott **nevet** és **értéket** az egyszerű **szolgáltatásnév alapján**
1. A **Létrehozás** gombra kattintva fejezze be
1. Ha a kulcstartó még nem csatlakozik a hatáskörébe, [létre kell hoznia egy új Key Vault-kapcsolatot](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Végül [hozzon létre egy új hitelesítő adatot](manage-credentials.md#create-a-new-credential) az egyszerű szolgáltatásnév használatával a vizsgálat beállításához

### <a name="firewall-settings"></a>Tűzfalbeállítások

Az adatbázis-kiszolgálónak engedélyeznie kell az Azure-kapcsolatokat. Ez lehetővé teszi az Azure hatáskörébe, hogy elérje és csatlakozhasson a kiszolgálóhoz. Az Azure-on [belüli kapcsolatok](../azure-sql/database/firewall-configure.md#connections-from-inside-azure)útmutatója a következő témakörben található:.

1. Navigáljon az adatbázis-fiókjához
1. Válassza ki a kiszolgáló nevét az **Áttekintés** oldalon
1. **Biztonsági > tűzfalak és virtuális hálózatok** kiválasztása
1. Válassza az **Igen** lehetőséget az **Azure-szolgáltatások és-erőforrások elérésének engedélyezéséhez ehhez a kiszolgálóhoz**

    :::image type="content" source="media/register-scan-azure-sql-database/sql-firewall.png" alt-text="Ezen kiszolgáló elérésének engedélyezése az Azure-szolgáltatások és-erőforrások számára." border="true":::
    
> [!Note]
> Az Azure-beli hatáskörébe jelenleg nem használható a VNET-konfiguráció. Ezért nem végezheti el az IP-alapú tűzfalszabályok beállításait.

## <a name="register-an-azure-sql-database-data-source"></a>Azure SQL Database adatforrás regisztrálása

Ha új Azure SQL Database szeretne regisztrálni az adatkatalógusban, tegye a következőket:

1. Navigáljon a hatáskörébe-fiókjába

1. A bal oldali navigációs sávon válassza a **források** lehetőséget

1. **Regisztráció** kiválasztása

1. A **források regisztrálása** lapon válassza a **Azure SQL Database** lehetőséget. Válassza a **Folytatás** lehetőséget.

:::image type="content" source="media/register-scan-azure-sql-database/register-new-data-source.png" alt-text="új adatforrás regisztrálása" border="true":::

A **források regisztrálása (Azure SQL Database)** képernyőn tegye a következőket:

1. Adja meg azt a **nevet** , amelyben az adatforrás szerepelni fog a katalógusban.
1. Válasszon az **Azure-előfizetésből**, válassza ki a megfelelő előfizetést az **Azure-előfizetés** legördülő listáról, és a megfelelő kiszolgálót a **kiszolgáló neve** legördülő listából.
1. **Fejezze** be az adatforrás regisztrálását.

:::image type="content" source="media/register-scan-azure-sql-database/add-azure-sql-database.png" alt-text="források regisztrálása – beállítások" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

> [!NOTE]
> * A vizsgálat törlése nem törli a korábbi Azure SQL Database vizsgálatokból származó eszközöket.
> * Az eszköz többé nem frissül a séma változásaival, ha a forrástábla módosítva lett, és a hatáskörébe tartozó séma lapon a Leírás szerkesztése után újból megvizsgálja a forrástábla adatait.

## <a name="next-steps"></a>Következő lépések

- [Az Azure-beli hatáskörébe tartozó adatkatalógus tallózása](how-to-browse-catalog.md)
- [Keresés az Azure-beli hatáskörébe Data Catalog](how-to-search-catalog.md)
