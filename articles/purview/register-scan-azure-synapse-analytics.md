---
title: Az Azure szinapszis Analytics vizsgálata
description: Ez az útmutató ismerteti az Azure szinapszis Analytics vizsgálatának részleteit.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/22/2020
ms.openlocfilehash: d287f5dc239339f79d2d8237e7739de7793920c4
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106108608"
---
# <a name="register-and-scan-azure-synapse-analytics"></a>Az Azure szinapszis Analytics regisztrálása és vizsgálata

Ez a cikk azt ismerteti, hogyan lehet regisztrálni és beolvasni az Azure szinapszis Analytics (korábban SQL DW) egy példányát a hatáskörébe.

## <a name="supported-capabilities"></a>Támogatott képességek

Az Azure szinapszis Analytics (korábbi nevén SQL DW) teljes és növekményes vizsgálatokat támogat a metaadatok és a séma rögzítéséhez. A vizsgálatok a rendszer és az egyéni besorolási szabályok alapján automatikusan is kategorizálják az adathalmazt.

### <a name="known-limitations"></a>Ismert korlátozások

Az Azure-beli hatáskörébe nem támogatott a [nézetek](/sql/relational-databases/views/views?view=azure-sqldw-latest&preserve-view=true) vizsgálata az Azure szinapszis Analyticsben

## <a name="prerequisites"></a>Előfeltételek

- Az adatforrások regisztrálása előtt hozzon létre egy Azure-beli hatáskörébe tartozó fiókot. A hatáskörébe tartozó fiókok létrehozásával kapcsolatos további információkért tekintse meg a rövid útmutató [: Azure hatáskörébe tartozó fiók létrehozása](create-catalog-portal.md)című témakört.
- Az Azure-beli adatforrások rendszergazdájának kell lennie
- Hálózatkezelési hozzáférés a hatáskörébe-fiók és az Azure szinapszis Analytics között.
 
## <a name="setting-up-authentication-for-a-scan"></a>Hitelesítés beállítása vizsgálathoz

Az Azure szinapszis Analytics hitelesítésének Háromféleképpen állítható be:

- Felügyelt identitás
- SQL-hitelesítés
- Szolgáltatásnév

    > [!Note]
    > Csak a kiszolgálói szintű rendszerbiztonsági tag (a kiépítési folyamat által létrehozott) vagy a `loginmanager` Master adatbázisban lévő adatbázis-szerepkör tagjai hozhatnak létre új bejelentkezéseket. Az engedélyek megadása után körülbelül 15 perccel tart, a hatáskörébe tartozó fióknak rendelkeznie kell a megfelelő engedélyekkel az erőforrás (ok) vizsgálatához.

### <a name="managed-identity-recommended"></a>Felügyelt identitás (ajánlott) 
   
A hatáskörébe tartozó fióknak saját felügyelt identitása van, amely alapvetően a hatáskörébe tartozik a létrehozásakor. Létre kell hoznia egy Azure AD-felhasználót az Azure szinapszis Analyticsben (korábbi nevén SQL DW) a hatáskörébe tartozó felügyelt identitás nevét követve az Azure [ad-felhasználók Azure ad-alkalmazásokkal történő létrehozásához](../azure-sql/database/authentication-aad-service-principal-tutorial.md)szükséges előfeltételek és oktatóanyag alapján.

SQL-szintaxis – példa felhasználói és engedélyezési engedélyek létrehozására:

```sql
CREATE USER [PurviewManagedIdentity] FROM EXTERNAL PROVIDER
GO

EXEC sp_addrolemember 'db_owner', [PurviewManagedIdentity]
GO
```

A hitelesítésnek engedéllyel kell rendelkeznie az adatbázis, a sémák és a táblák metaadatainak beszerzéséhez. Emellett képesnek kell lennie a táblázatok lekérdezésére is a besoroláshoz. A javaslat célja, hogy `db_owner` engedélyt rendeljen az identitáshoz.

### <a name="service-principal"></a>Szolgáltatásnév

Ha az egyszerű szolgáltatás hitelesítését szeretné használni a vizsgálatokhoz, használhat egy meglévőt, vagy létrehozhat egy újat. 

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

Az egyszerű szolgáltatás alkalmazás-azonosítójának és titkos kódjának beszerzéséhez a következő szükséges:

1. Navigáljon az egyszerű szolgáltatáshoz a [Azure Portal](https://portal.azure.com)
1. Másolja az **alkalmazás (ügyfél) azonosítóját** az **Áttekintés** és az **ügyfél titkos kulcsa** a **tanúsítványok & Secrets** elemből.
1. Navigáljon a kulcstartóhoz
1. **Beállítások kiválasztása > titkok**
1. Válassza a **+ előállítás/importálás** lehetőséget, és adja meg az Ön által választott **nevet** és **értéket** az egyszerű **szolgáltatásnév alapján**
1. A **Létrehozás** gombra kattintva fejezze be
1. Ha a kulcstartó még nem csatlakozik a hatáskörébe, [létre kell hoznia egy új Key Vault-kapcsolatot](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Végül [hozzon létre egy új hitelesítő adatot](manage-credentials.md#create-a-new-credential) az egyszerű szolgáltatásnév használatával a vizsgálat beállításához 

#### <a name="granting-the-service-principal-access-to-your-azure-synapse-analytics-formerly-sql-dw"></a>A szolgáltatás egyszerű hozzáférésének biztosítása az Azure szinapszis Analyticshez (korábban SQL DW)

Emellett Azure ad-felhasználót is létre kell hoznia az Azure szinapszis Analyticsben az Azure ad- [felhasználók Azure ad-alkalmazásokkal történő létrehozásához](../azure-sql/database/authentication-aad-service-principal-tutorial.md)szükséges előfeltételek és oktatóanyag alapján. SQL-szintaxis – példa felhasználói és engedélyezési engedélyek létrehozására:

```sql
CREATE USER [ServicePrincipalName] FROM EXTERNAL PROVIDER
GO

ALTER ROLE db_owner ADD MEMBER [ServicePrincipalName]
GO
```

> [!Note]
> A hatáskörébe szüksége lesz az **alkalmazásra (ügyfél)** és az **ügyfél titkos** kódjára a vizsgálathoz.

### <a name="sql-authentication"></a>SQL-hitelesítés

Ha még nem rendelkezik ilyennel, kövesse a [create login (bejelentkezés létrehozása](/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest&preserve-view=true#examples-1) ) című témakör utasításait az Azure szinapszis Analytics (korábban SQL DW) bejelentkezési azonosítójának létrehozásához.

Ha a kiválasztott hitelesítési módszer **SQL-hitelesítés**, a Key vaultban be kell szereznie a jelszavát, és tárolnia kell a következőt:

1. Az SQL-bejelentkezés jelszavának beolvasása
1. Navigáljon a kulcstartóhoz
1. **Beállítások kiválasztása > titkok**
1. Válassza a **+ előállítás/importálás** lehetőséget, és adja meg a **nevet** és az **értéket** az SQL-bejelentkezéshez használt *jelszóként*
1. A **Létrehozás** gombra kattintva fejezze be
1. Ha a kulcstartó még nem csatlakozik a hatáskörébe, [létre kell hoznia egy új Key Vault-kapcsolatot](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Végül [hozzon létre egy új hitelesítő adatot](manage-credentials.md#create-a-new-credential) a kulcs használatával a vizsgálat beállításához

## <a name="register-an-azure-synapse-analytics-instance-formerly-sql-dw"></a>Azure szinapszis Analytics-példány regisztrálása (korábban SQL DW)

Ha új Azure szinapszis Analytics-kiszolgálót szeretne regisztrálni a Data Catalogban, tegye a következőket:

1. Navigáljon a hatáskörébe-fiókjába
1. A bal oldali navigációs sávon válassza a **források** lehetőséget
1. **Regisztráció** kiválasztása
1. A **források regisztrálása** lapon válassza az **Azure szinapszis Analytics (korábban SQL DW) lehetőséget.**
1. Válassza a **Folytatás** elemet

A **források regisztrálása (Azure szinapszis Analytics)** képernyőn tegye a következőket:

1. Adja meg azt a **nevet** , amelyben az adatforrás szerepelni fog a katalógusban.
1. Válassza ki, hogyan szeretné a kívánt logikai SQL Serverra mutatni:
   1. Válasszon az **Azure-előfizetésből**, válassza ki a megfelelő előfizetést az **Azure-előfizetés** legördülő listából, és a megfelelő kiszolgálót a **kiszolgáló neve** legördülő listából.
   1. Az **ENTER billentyűt manuálisan** is kiválaszthatja, és megadhatja a **kiszolgáló nevét**.
1. **Fejezze** be az adatforrás regisztrálását.

:::image type="content" source="media/register-scan-azure-synapse-analytics/register-sources.png" alt-text="források regisztrálása – beállítások" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Következő lépések

- [Az Azure-beli hatáskörébe tartozó adatkatalógus tallózása](how-to-browse-catalog.md)
- [Keresés az Azure-beli hatáskörébe Data Catalog](how-to-search-catalog.md)
