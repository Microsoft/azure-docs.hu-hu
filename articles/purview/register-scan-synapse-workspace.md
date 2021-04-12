---
title: Az Azure szinapszis-munkaterületek vizsgálata
description: Ismerje meg, hogyan vizsgálhat meg egy szinapszis-munkaterületet az Azure hatáskörébe tartozó adatkatalógusban.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 3/31/2021
ms.openlocfilehash: 230894b8e474c8d230322fb1e240f0317512d036
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107031404"
---
# <a name="register-and-scan-azure-synapse-workspaces"></a>Azure szinapszis-munkaterületek regisztrálása és vizsgálata

Ez a cikk azt ismerteti, hogyan regisztrálhat egy Azure-beli szinapszis-munkaterületet a hatáskörébe, és hogyan állíthat be vizsgálatot.

## <a name="supported-capabilities"></a>Támogatott képességek

Az Azure szinapszis-munkaterület vizsgálatai támogatják a metaadatok és a séma rögzítését az azokon belül dedikált és kiszolgáló nélküli SQL-adatbázisokhoz. Emellett a rendszer és az egyéni besorolási szabályok alapján automatikusan osztályozza az adathalmazokat.

## <a name="prerequisites"></a>Előfeltételek

- Az adatforrások regisztrálása előtt hozzon létre egy Azure-beli hatáskörébe tartozó fiókot. A hatáskörébe tartozó fiókok létrehozásával kapcsolatos további információkért tekintse meg a rövid útmutató [: Azure hatáskörébe tartozó fiók létrehozása](create-catalog-portal.md)című témakört.
- Az Azure-beli adatforrások rendszergazdájának kell lennie
- A hitelesítés beállítása az alábbi szakaszokban leírtak szerint

### <a name="setting-up-authentication-for-enumerating-dedicated-sql-database-resources-under-a-synapse-workspace"></a>Hitelesítés beállítása a dedikált SQL Database-erőforrások egy szinapszis-munkaterületen való enumerálásához

1. Navigáljon ahhoz az **erőforráscsoporthoz** vagy **előfizetéshez** , amelyhez a szinapszis munkaterület van, a Azure Portal.  
1. A bal oldali navigációs menüből válassza a **Access Control (iam) lehetőséget.**   
1. Ahhoz, hogy szerepkört adjon hozzá az erőforráscsoporthoz vagy az előfizetéshez, tulajdonosi vagy felhasználói hozzáférési rendszergazdának kell lennie. Kattintson a *+ Hozzáadás* gombra. 
1. Állítsa be az **olvasó** szerepkört, és adja meg az Azure-beli hatáskörébe tartozó fiók nevét (amely az MSI-t jelöli) a beviteli mező kiválasztása területen. A szerepkör-hozzárendelés befejezéséhez kattintson a *Mentés* gombra.
1. Kövesse a fenti 2 – 4. lépést a **Storage blob adatolvasói** szerepkör hozzáadásához az Azure hatáskörébe MSI-hez azon erőforráscsoport vagy előfizetés esetében, amelyhez a szinapszis munkaterület tartozik.

### <a name="setting-up-authentication-for-enumerating-serverless-sql-database-resources-under-a-synapse-workspace"></a>A kiszolgáló nélküli SQL Database-erőforrások egy szinapszis-munkaterületen való enumerálásához szükséges hitelesítés beállítása

> [!NOTE]
> A parancsok futtatásához **szinapszis-rendszergazdának** kell lennie a munkaterületen. További információ a szinapszis-engedélyekről [itt](../synapse-analytics/security/how-to-set-up-access-control.md)található.

1. Navigáljon a szinapszis munkaterülethez
1. Navigáljon az **adatszakaszra és az egyik** kiszolgáló nélküli SQL-adatbázisra
1. Kattintson az ellipszis ikonra, és indítsa el az új SQL-szkriptet
1. Adja hozzá az Azure-fiók MSI-jét (amelyet a fiók neve jelöl **) rendszergazdaként** a kiszolgáló nélküli SQL-adatbázisokban az alábbi parancs futtatásával az SQL-parancsfájlban:
    ```sql
    CREATE LOGIN [PurviewAccountName] FROM EXTERNAL PROVIDER;
    ALTER SERVER ROLE sysadmin ADD MEMBER [PurviewAccountName];
    ```

### <a name="setting-up-authentication-to-scan-resources-under-a-synapse-workspace"></a>Hitelesítés beállítása a szinapszis-munkaterület alatt lévő erőforrások vizsgálatához

Az Azure-beli szinapszis-források hitelesítésének beállítása három módon végezhető el:

- Felügyelt identitás
- Szolgáltatásnév
 
#### <a name="using-managed-identity-for-dedicated-sql-databases"></a>Felügyelt identitás használata dedikált SQL-adatbázisokhoz

1. Navigáljon a **szinapszis munkaterülethez**
1. Navigáljon az **adatszakaszra és az egyik** kiszolgáló nélküli SQL-adatbázisra
1. Kattintson az ellipszis ikonra, és indítsa el az új SQL-szkriptet
1. Az alábbi parancs futtatásával adja hozzá az Azure-fiók MSI-jét (amelyet a fiók neve képvisel) **db_ownerként** a dedikált SQL-adatbázison:

    ```sql
    CREATE USER [PurviewAccountName] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_owner', [PurviewAccountName]
    GO
    ```
#### <a name="using-managed-identity-for-serverless-sql-databases"></a>Felügyelt identitás használata kiszolgáló nélküli SQL-adatbázisokhoz

1. Navigáljon a **szinapszis munkaterülethez**
1. Navigáljon az **adatszakaszra és az egyik** kiszolgáló nélküli SQL-adatbázisra
1. Kattintson az ellipszis ikonra, és indítsa el az új SQL-szkriptet
1. Adja hozzá az Azure-fiók MSI-jét (amelyet a fiók neve jelöl **) rendszergazdaként** a kiszolgáló nélküli SQL-adatbázisokban az alábbi parancs futtatásával az SQL-parancsfájlban:
    ```sql
    CREATE LOGIN [PurviewAccountName] FROM EXTERNAL PROVIDER;
    ALTER SERVER ROLE sysadmin ADD MEMBER [PurviewAccountName];
    ```

#### <a name="using-service-principal-for-dedicated-sql-databases"></a>Egyszerű szolgáltatásnév használata dedikált SQL-adatbázisokhoz

> [!NOTE]
> Először be kell állítania az egyszerű szolgáltatásnév új **hitelesítő adatait** az [alábbi utasításokat](manage-credentials.md)követve.

1. Navigáljon a **szinapszis munkaterülethez**
1. Navigáljon az **adatszakaszra és az egyik** kiszolgáló nélküli SQL-adatbázisra
1. Kattintson az ellipszis ikonra, és indítsa el az új SQL-szkriptet
1. Adja hozzá az **egyszerű szolgáltatás azonosítóját** **db_ownerként** a dedikált SQL-adatbázisban az alábbi parancs futtatásával az SQL-parancsfájlban:

    ```sql
    CREATE USER [ServicePrincipalID] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_owner', [ServicePrincipalID]
    GO
    ```

#### <a name="using-service-principal-for-serverless-sql-databases"></a>Egyszerű szolgáltatásnév használata kiszolgáló nélküli SQL-adatbázisokhoz

1. Navigáljon a **szinapszis munkaterülethez**
1. Navigáljon az **adatszakaszra és az egyik** kiszolgáló nélküli SQL-adatbázisra
1. Kattintson az ellipszis ikonra, és indítsa el az új SQL-szkriptet
1. Adja hozzá az Azure-fiók MSI-jét (amelyet a fiók neve jelöl **) rendszergazdaként** a kiszolgáló nélküli SQL-adatbázisokban az alábbi parancs futtatásával az SQL-parancsfájlban:
    ```sql
    CREATE LOGIN [ServicePrincipalID] FROM EXTERNAL PROVIDER;
    ALTER SERVER ROLE sysadmin ADD MEMBER [ServicePrincipalID];
    ```

> [!NOTE]
> Be kell állítania a hitelesítést a szinapszis munkaterületen belül minden olyan dedikált SQL-adatbázison, amelyet regisztrálni és ellenőrizni kíván. A kiszolgáló nélküli SQL Database-hez fent említett engedélyek a munkaterületen belül mindegyikre érvényesek, azaz csak egyszer kell futtatnia.
    
## <a name="register-an-azure-synapse-source"></a>Azure szinapszis-forrás regisztrálása

Ha új Azure szinapszis-forrást szeretne regisztrálni az adatkatalógusban, tegye a következőket:

1. Navigáljon a hatáskörébe-fiókjába
1. A bal oldali navigációs sávon válassza a **források** lehetőséget
1. **Regisztráció** kiválasztása
1. A **források regisztrálása** lapon válassza az **Azure szinapszis Analytics (több) lehetőséget.**
1. Válassza a **Folytatás** elemet

   :::image type="content" source="media/register-scan-synapse-workspace/register-synapse-source.png" alt-text="Az Azure szinapszis forrásának beállítása":::

A **források regisztrálása (Azure szinapszis Analytics)** képernyőn tegye a következőket:

1. Adja meg azt a **nevet** , amelyben az adatforrás szerepelni fog a katalógusban.
1. Választható lehetőségként kiválaszthat egy **előfizetést** , amelyről szűrni szeretne.
1. **Válasszon ki egy szinapszis-munkaterület nevét** a legördülő listából. Az SQL-végpontok automatikusan kitöltésre kerülnek a munkaterület kiválasztása alapján. 
1. Válasszon ki egy **gyűjteményt** , vagy hozzon létre egy újat (nem kötelező)
1. Az adatforrás regisztrálásának **befejezése**

    :::image type="content" source="media/register-scan-synapse-workspace/register-synapse-source-details.png" alt-text="Az Azure szinapszis forrás részleteinek kitöltése":::

## <a name="creating-and-running-a-scan"></a>Vizsgálat létrehozása és futtatása

Új vizsgálat létrehozásához és futtatásához tegye a következőket:

1. Navigáljon a **források** szakaszhoz.

1. Válassza ki a regisztrált adatforrást.

1. Kattintson a részletek megtekintése elemre, és válassza az **+ új vizsgálat** lehetőséget, vagy a forrás csempén a gyors művelet ellenőrzése ikont használja.

1. Adja meg a *nevet* , és válassza ki az összes olyan erőforrást, amelyet meg szeretne vizsgálni a forráson belül. A **SQL Database** az egyetlen olyan típus, amelyet jelenleg a szinapszis munkaterületen belül támogatunk.
   
    :::image type="content" source="media/register-scan-synapse-workspace/synapse-scan-setup.png" alt-text="Azure szinapszis-forrás vizsgálata":::

1. Válassza ki a **hitelesítő adatokat** az adatforráson belüli erőforrásokhoz való kapcsolódáshoz. 
  
1. Az egyes típusokon belül kiválaszthatja, hogy az összes erőforrást vagy egy részhalmazát megkeresi-e név alapján.
1.  A folytatáshoz kattintson a **tovább** gombra. 

1.  Válasszon ki egy Azure szinapszis SQL típusú **ellenőrzési szabálykészlet-készletet** . A betekintő szabálykészlet beágyazottként is létrehozható.

1. Válassza ki a vizsgálati triggert. Ütemezheti úgy, hogy **hetente/havonta** vagy **egyszer** fusson

1. Tekintse át a vizsgálatot, és a beállítás befejezéséhez válassza a mentés lehetőséget.   

## <a name="viewing-your-scans-and-scan-runs"></a>A vizsgálatok és a vizsgálat futtatásának megtekintése

1. A forrás részleteinek megtekintéséhez kattintson a **részletek megtekintése** elemre a források szakaszban található csempén. 

      :::image type="content" source="media/register-scan-synapse-workspace/synapse-source-details.png" alt-text="Azure szinapszis-forrás részletei"::: 

1. Tekintse meg a vizsgálat futtatásával kapcsolatos részleteket a **vizsgálat részletei** lapon.
    1. Az *Állapotsor* röviden összefoglalja a gyermekek erőforrásainak futási állapotát. Ekkor megjelenik a munkaterület szintjének vizsgálatakor.
    1. A zöld érték sikeres, míg a piros azt jelenti, hogy nem sikerült. A szürke érték azt jelenti, hogy a vizsgálat még folyamatban van
    1. Az egyes vizsgálatokra kattintva részletesebb információkat jeleníthet meg

      :::image type="content" source="media/register-scan-synapse-workspace/synapse-scan-details.png" alt-text="Azure szinapszis-vizsgálat részletei" lightbox="media/register-scan-synapse-workspace/synapse-scan-details.png"::: 

1. Tekintse meg a legutóbbi sikertelen vizsgálat összegzését a forrás részletei lap alján. A futtatásokra vonatkozó részletesebb részletek megtekintéséhez is rákattinthat.

## <a name="manage-your-scans---edit-delete-or-cancel"></a>Ellenőrzések kezelése – Szerkesztés, törlés vagy megszakítás
A vizsgálat kezeléséhez vagy törléséhez tegye a következőket:

- Navigáljon a felügyeleti központhoz. Válassza ki az adatforrások lehetőséget a források és vizsgálat szakaszban, majd válassza ki a kívánt adatforrást.

- Válassza ki a kezelni kívánt vizsgálatot. A vizsgálat szerkesztéséhez kattintson a Szerkesztés lehetőségre.

- A vizsgálatot a Delete (Törlés) lehetőség kiválasztásával törölheti.
- Ha egy vizsgálat fut, azt is megszakíthatja.

## <a name="next-steps"></a>Következő lépések

- [Az Azure-beli hatáskörébe tartozó adatkatalógus tallózása](how-to-browse-catalog.md)
- [Keresés az Azure-beli hatáskörébe Data Catalog](how-to-search-catalog.md)   