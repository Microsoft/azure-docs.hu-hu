---
title: Helyszíni SQL Server-kiszolgáló regisztrálása és vizsgálata
description: Ez az oktatóanyag azt ismerteti, hogyan lehet az SQL Servert egy saját üzemeltetésű integrációs modul használatával beolvasni.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/18/2020
ms.openlocfilehash: b5f4218cfcd5f9ccfbe43efac46e2f70fdc30905
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99574957"
---
# <a name="register-and-scan-an-on-premises-sql-server"></a>Helyszíni SQL Server-kiszolgáló regisztrálása és vizsgálata

Ez a cikk azt ismerteti, hogyan regisztrálhat egy SQL Server-adatforrást a hatáskörébe, és hogyan állíthat be vizsgálatot.

## <a name="supported-capabilities"></a>Támogatott képességek

A helyszíni SQL Server-adatforrás a következő funkciókat támogatja:

- **Teljes és növekményes vizsgálatok** a metaadatok és besorolások rögzítésére egy helyszíni hálózaton vagy egy Azure-beli virtuális GÉPEN telepített SQL Serveren.

- Adategységek közötti **vonal** a másolási/adatfolyam tevékenységekhez

Az SQL Server helyszíni adatforrása a következőket támogatja:

- az SQL Server 2019-es verziójának minden verziója vissza az SQL Server 2000-be

- Hitelesítési módszer: SQL-hitelesítés

### <a name="known-limitations"></a>Ismert korlátozások

Az Azure-beli hatáskörébe nem támogatottak a SQL Server [nézeteinek](/sql/relational-databases/views/views) vizsgálata.

## <a name="prerequisites"></a>Előfeltételek

- Az adatforrások regisztrálása előtt hozzon létre egy Azure-beli hatáskörébe tartozó fiókot. A hatáskörébe tartozó fiókok létrehozásával kapcsolatos további információkért tekintse meg a rövid útmutató [: Azure hatáskörébe tartozó fiók létrehozása](create-catalog-portal.md)című témakört.

- Hozzon létre egy saját üzemeltetésű [integrációs](manage-integration-runtimes.md) modult az adatforrás vizsgálatához.

## <a name="setting-up-authentication-for-a-scan"></a>Hitelesítés beállítása vizsgálathoz

A helyszíni SQL Server hitelesítésének beállítása csak egyetlen módon történik:

- SQL-hitelesítés

### <a name="sql-authentication"></a>SQL-hitelesítés

Az SQL-fióknak hozzáféréssel kell rendelkeznie a **Master** adatbázishoz. Ennek az az oka, hogy a a `sys.databases` Master adatbázisban található. A hatáskörébe tartozó képolvasónak enumerálnia kell, `sys.databases` hogy megtalálja az összes SQL-adatbázist a kiszolgálón.

#### <a name="using-an-existing-server-administrator"></a>Meglévő kiszolgáló-rendszergazda használata

Ha azt tervezi, hogy meglévő kiszolgáló-rendszergazdai (SA) felhasználót használ a helyszíni SQL Server vizsgálatára, ügyeljen a következőkre:

1. `sa` nem Windows-hitelesítési fiók.

2. A használni kívánt kiszolgálói szintű bejelentkezésnek nyilvános és sysadmin kiszolgálói szerepkörrel kell rendelkeznie. Ezt úgy ellenőrizheti, ha csatlakozik a kiszolgálóhoz, navigáljon SQL Server Management Studio (SSMS), lépjen a Biztonság elemre, válassza ki a használni kívánt bejelentkezési azonosítót, kattintson a jobb gombbal a **Tulajdonságok** elemre, majd válassza a **kiszolgálói szerepkörök** lehetőséget.

   :::image type="content" source="media/register-scan-on-premises-sql-server/server-level-login.png" alt-text="Kiszolgálói szintű bejelentkezés.":::

#### <a name="creating-a-new-login-and-user"></a>Új bejelentkezés és felhasználó létrehozása

Ha új bejelentkezési azonosítót szeretne létrehozni, és a felhasználót szeretné ellenőrizni az SQL Servert, kövesse az alábbi lépéseket:

> [!Note]
   > Az alábbi lépéseket az [itt](https://github.com/Azure/Purview-Samples/blob/master/TSQL-Code-Permissions/grant-access-to-on-prem-sql-databases.sql) megadott kóddal hajthatja végre.

1. Navigáljon SQL Server Management Studio (SSMS), kapcsolódjon a kiszolgálóhoz, lépjen a Biztonság elemre, kattintson a jobb gombbal a bejelentkezés elemre, és hozzon létre új bejelentkezést. Győződjön meg arról, hogy az SQL-hitelesítés lehetőséget választotta.

   :::image type="content" source="media/register-scan-on-premises-sql-server/create-new-login-user.png" alt-text="Hozzon létre új bejelentkezési azonosítót és felhasználót.":::

2. Válassza ki a kiszolgálói szerepkörök elemet a bal oldali navigációs sávon, és győződjön meg arról, hogy a nyilvános szerepkör hozzá van rendelve.

3. Válassza ki a felhasználó leképezése elemet a bal oldali navigációs sávon, válassza ki az összes adatbázist a térképen, és válassza ki az adatbázis-szerepkört: **db_datareader**.

   :::image type="content" source="media/register-scan-on-premises-sql-server/user-mapping.png" alt-text="felhasználó leképezése.":::

4. Kattintson az OK gombra a mentéshez.

5. Navigáljon újra a létrehozott felhasználóhoz, kattintson a jobb gombbal, majd válassza a **Tulajdonságok** lehetőséget. Adjon meg egy új jelszót, és erősítse meg. Válassza a "régi jelszó megadása" lehetőséget, és írja be a régi jelszót. **Az új bejelentkezés létrehozásakor szükség van a jelszó módosítására.**

   :::image type="content" source="media/register-scan-on-premises-sql-server/change-password.png" alt-text="módosítsa a jelszót.":::

#### <a name="storing-your-sql-login-password-in-a-key-vault-and-creating-a-credential-in-purview"></a>Az SQL bejelentkezési jelszavának tárolása kulcstartóban és hitelesítő adat létrehozása a hatáskörébe

1. Navigáljon a Key vaulthoz az Azure portal1. **Beállítások kiválasztása > titkok**
1. Válassza a **+ előállítás/importálás** lehetőséget, és adja meg a **nevet** és az **értéket** az SQL Server-bejelentkezéshez használt *jelszóként*
1. A **Létrehozás** gombra kattintva fejezze be
1. Ha a kulcstartó még nem csatlakozik a hatáskörébe, [létre kell hoznia egy új Key Vault-kapcsolatot](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Végül [hozzon létre egy új hitelesítő adatot](manage-credentials.md#create-a-new-credential) a **Felhasználónév** és **jelszó** használatával a vizsgálat beállításához

## <a name="register-a-sql-server-data-source"></a>SQL Server-adatforrás regisztrálása

1. Navigáljon a hatáskörébe-fiókjába

1. A bal oldali navigációs forrás és vizsgálat területén válassza az **integrációs** modulok elemet. Győződjön meg arról, hogy a saját üzemeltetésű integrációs modul van beállítva. Ha nincs beállítva, kövesse az [itt](manage-integration-runtimes.md) leírt lépéseket a saját üzemeltetésű integrációs modul létrehozásához helyszíni vagy Azure virtuális gépen, amely hozzáfér a helyszíni hálózathoz.

1. A bal oldali navigációs sávon válassza a **források** lehetőséget

1. **Regisztráció** kiválasztása

1. Válassza az **SQL Server** lehetőséget, majd **folytassa**

   :::image type="content" source="media/register-scan-on-premises-sql-server/set-up-sql-data-source.png" alt-text="Állítsa be az SQL-adatforrást.":::

5. Adjon meg egy felhasználóbarát nevet és egy kiszolgálói végpontot, majd válassza a **Befejezés** lehetőséget az adatforrás regisztrálásához. Ha például az SQL Server teljes tartományneve **foobar.database.Windows.net**, akkor adja meg a *foobar* kiszolgáló-végpontként.

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Következő lépések

- [Az Azure-beli hatáskörébe tartozó adatkatalógus tallózása](how-to-browse-catalog.md)
- [Keresés az Azure-beli hatáskörébe Data Catalog](how-to-search-catalog.md)
