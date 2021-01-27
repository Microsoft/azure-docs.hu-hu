---
title: Felügyelt példány regisztrálása és vizsgálata Azure SQL Database
description: Ez az oktatóanyag azt ismerteti, hogyan lehet beolvasni Azure SQL Database felügyelt példányt
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: 2391a36c02e0bdfe0ea98be7f19fc64f1fbb676f
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879932"
---
# <a name="register-and-scan-an-azure-sql-database-managed-instance"></a>Azure SQL Database felügyelt példány regisztrálása és bevizsgálása

Ez a cikk azt ismerteti, hogyan regisztrálhat egy Azure SQL Database felügyelt példány adatforrását a hatáskörébe, és hogyan állíthat be vizsgálatot.

## <a name="supported-capabilities"></a>Támogatott képességek

Az Azure SQL Database felügyelt példány adatforrás a következő funkciókat támogatja:

- **Teljes és növekményes vizsgálatok** a metaadatok és a besorolás Azure SQL Database felügyelt példányban történő rögzítéséhez.

- Adategységek közötti **vonal** a másolási és a adatfolyam tevékenységekhez.

### <a name="known-limitations"></a>Ismert korlátozások

Az Azure-beli hatáskörébe nem használhatók a [nézetek](/sql/relational-databases/views/views?view=azuresqldb-mi-current&preserve-view=true) vizsgálata az Azure SQL felügyelt példányában.

## <a name="prerequisites"></a>Előfeltételek

- Ha még nem rendelkezik ilyennel, hozzon létre egy új hatáskörébe tartozó fiókot.

- [Nyilvános végpont konfigurálása az Azure SQL felügyelt példányában](../azure-sql/managed-instance/public-endpoint-configure.md)
    > [!Note]
    > A szervezetnek képesnek kell lennie arra, hogy nyilvános végpontot engedélyezzen, mivel a hatáskörébe **nem támogatja a saját végpontot** . Ha privát végpontot használ, a vizsgálat sikertelen lesz.

### <a name="setting-up-authentication-for-a-scan"></a>Hitelesítés beállítása vizsgálathoz

Hitelesítés Azure SQL Database felügyelt példány vizsgálatához. Ha új hitelesítést kell létrehoznia, engedélyeznie kell az [adatbázis-hozzáférést SQL Database felügyelt példányhoz](../azure-sql/database/logins-create-manage.md). A hatáskörébe három hitelesítési módszer létezik:

- SQL-hitelesítés
- Szolgáltatásnév
- Felügyelt identitás

#### <a name="sql-authentication"></a>SQL-hitelesítés

> [!Note]
> Csak a kiszolgálói szintű rendszerbiztonsági tag (a kiépítési folyamat által létrehozott) vagy a `loginmanager` Master adatbázisban lévő adatbázis-szerepkör tagjai hozhatnak létre új bejelentkezéseket. Az engedélyek megadása után körülbelül **15 perccel** tart, a hatáskörébe tartozó fióknak rendelkeznie kell a megfelelő engedélyekkel az erőforrás (ok) vizsgálatához.

Ha nem rendelkezik ezzel a lehetőséggel, kövesse a [Bejelentkezés létrehozása](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true#examples-1) Azure SQL Database a felügyelt példányhoz tartozó bejelentkezés létrehozásához című témakör utasításait. A következő lépésekhez szüksége lesz a **felhasználónévre** és a **jelszóra** .

1. Navigáljon a Key vaulthoz a Azure Portal
1. **Beállítások kiválasztása > titkok**
1. Válassza a **+ előállítás/importálás** lehetőséget, és írja be a **nevet** és az **értéket** a Azure SQL Database felügyelt példányának *jelszavaként* .
1. A **Létrehozás** gombra kattintva fejezze be
1. Ha a kulcstartó még nem csatlakozik a hatáskörébe, [létre kell hoznia egy új Key Vault-kapcsolatot](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Végül [hozzon létre egy új hitelesítő adatot](manage-credentials.md#create-a-new-credential) a **Felhasználónév** és **jelszó** használatával a vizsgálat beállításához

#### <a name="service-principal-and-managed-identity"></a>Egyszerű szolgáltatásnév és felügyelt identitás

Számos lépéssel engedélyezheti a hatáskörébe, hogy az egyszerű szolgáltatásnév használatával átvizsgálja a Azure SQL Database felügyelt példányát

##### <a name="create-or-use-an-existing-service-principal"></a>Meglévő egyszerű szolgáltatásnév létrehozása vagy használata

> [!Note]
> Ha **felügyelt identitást** használ, hagyja ki ezt a lépést

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
- Hozzon létre egy Azure AD-felhasználót Azure SQL Database felügyelt példányban az [Azure ad-identitásokhoz hozzárendelt, foglalt felhasználók létrehozásához](../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell#create-contained-users-mapped-to-azure-ad-identities) szükséges előfeltételeket és oktatóanyagot követve
- Hozzárendelés `db_owner` (**ajánlott**) engedély az identitáshoz

##### <a name="add-service-principal-to-key-vault-and-purviews-credential"></a>Egyszerű szolgáltatásnév hozzáadása a Key vaulthoz és a hatáskörébe hitelesítő adataihoz

> [!Note]
> Ha **felügyelt identitást** szeretne használni, akkor kihagyhatja ezt a lépést, mert az alapértelmezett hatáskörébe tartozó identitás már a **hatáskörébe tartozik – MSI**

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

    :::image type="content" source="media/register-scan-azure-sql-database/sql-firewall.png" alt-text="források regisztrálása – beállítások" border="true":::
    
> [!Note]
> Az Azure-beli hatáskörébe jelenleg nem használható a VNET-konfiguráció. Ezért nem végezheti el az IP-alapú tűzfalszabályok beállításait.

## <a name="register-an-azure-sql-database-managed-instance-data-source"></a>Azure SQL Database felügyelt példány adatforrásának regisztrálása

1. Navigáljon a hatáskörébe-fiókjába

1. A bal oldali navigációs sávon válassza a **források** lehetőséget

1. **Regisztráció** kiválasztása

1. Válassza ki **Azure SQL Database felügyelt példányt** , majd **folytassa**

    :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/set-up-the-sql-data-source.png" alt-text="Az SQL-adatforrás beállítása":::

1. Válassza a **manuális bevitel** lehetőséget

1. Adja meg a **nyilvános végpont teljes tartománynevét** és **portszámát**. Ezután válassza a **Befejezés** lehetőséget az adatforrás regisztrálásához.

    :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/add-azure-sql-database-managed-instance.png" alt-text="Azure SQL Database felügyelt példány hozzáadása":::

    Például `foobar.public.123.database.windows.net,3342`

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

> [!NOTE]
> A vizsgálat törlése nem törli a korábbi Azure SQL Database felügyelt példányok vizsgálatainak eszközeit.

## <a name="next-steps"></a>További lépések

- [Az Azure-beli hatáskörébe tartozó adatkatalógus tallózása](how-to-browse-catalog.md)
- [Keresés az Azure-beli hatáskörébe Data Catalog](how-to-search-catalog.md)