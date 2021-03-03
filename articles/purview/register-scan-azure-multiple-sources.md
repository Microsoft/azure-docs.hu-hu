---
title: Több forrás vizsgálata az Azure-ban
description: Megtudhatja, hogyan vizsgálhat meg egy teljes Azure-előfizetést vagy erőforráscsoportot az Azure hatáskörébe tartozó adatkatalógusban.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 2/26/2021
ms.openlocfilehash: c57ac9ddbebcf02cb0118705b63f97fd1880b0f2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101695961"
---
# <a name="register-and-scan-azure-multiple-sources"></a>Azure-beli több forrás regisztrálása és vizsgálata

Ez a cikk azt ismerteti, hogyan regisztrálhat egy Azure-beli több forrást (Azure-előfizetést vagy erőforráscsoportot) a hatáskörébe, és hogyan állíthat be vizsgálatot.

## <a name="supported-capabilities"></a>Támogatott képességek

Az Azure több forrás támogatja a kereséseket, hogy rögzítse a metaadatokat és a sémát a hatáskörébe tartozó legtöbb Azure-erőforrástípus esetében. Emellett a rendszer és az egyéni besorolási szabályok alapján automatikusan osztályozza az adathalmazokat.

## <a name="prerequisites"></a>Előfeltételek

- Az adatforrások regisztrálása előtt hozzon létre egy Azure-beli hatáskörébe tartozó fiókot. A hatáskörébe tartozó fiókok létrehozásával kapcsolatos további információkért tekintse meg a rövid útmutató [: Azure hatáskörébe tartozó fiók létrehozása](create-catalog-portal.md)című témakört.
- Az Azure-beli adatforrások rendszergazdájának kell lennie
- A hitelesítés beállítása az alábbi szakaszokban leírtak szerint

### <a name="setting-up-authentication-for-enumerating-resources-under-a-subscription-or-resource-group"></a>Az előfizetés vagy az erőforráscsoport alatt lévő erőforrások enumerálásához szükséges hitelesítés beállítása

1. Navigáljon az előfizetéshez vagy az erőforráscsoporthoz a Azure Portal.  
1. A bal oldali navigációs menüből válassza a **Access Control (iam) lehetőséget.**   
1. Ahhoz, hogy szerepkört adjon hozzá az előfizetéshez vagy az erőforráscsoporthoz, tulajdonosi vagy felhasználói hozzáférési rendszergazdának kell lennie. Kattintson a *+ Hozzáadás* gombra. 
1. Állítsa be az **olvasó** szerepkört, és adja meg az Azure-beli hatáskörébe tartozó fiók nevét (amely az MSI-t jelöli) a beviteli mező kiválasztása területen. A szerepkör-hozzárendelés befejezéséhez kattintson a *Mentés* gombra.

### <a name="setting-up-authentication-to-scan-resources-under-a-subscription-or-resource-group"></a>Hitelesítés beállítása egy előfizetés vagy erőforráscsoport alatt lévő erőforrások vizsgálatához

Az Azure több forrás hitelesítését kétféleképpen állíthatja be:

- Felügyelt identitás
- Szolgáltatásnév

> [!NOTE]
> Az előfizetéshez vagy az erőforráscsoporthoz tartozó összes erőforráshoz be kell állítania a hitelesítést, amelyet regisztrálni és ellenőrizni kíván. Az Azure Storage-erőforrástípusok (Azure Blob Storage és Azure Data Lake Storage Gen2) megkönnyítik az MSI vagy az egyszerű szolgáltatásnév hozzáadását az előfizetés/erőforráscsoport szintjén a Storage blob adatolvasóként. Az engedélyek ezután az adott előfizetéshez vagy erőforráscsoporthoz tartozó összes Storage-fiókra leszivárognak. Minden más erőforrástípus esetében minden erőforráson alkalmaznia kell az MSI-t vagy az egyszerű szolgáltatásnevet, vagy egy parancsfájlt kell tennie. A következőképpen adhat hozzá engedélyeket az egyes erőforrás-típusokhoz az előfizetésen vagy az erőforráscsoporton belül.
    
- [Azure Blob Storage](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [1. generációs Azure Data Lake Storage](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Azure SQL Database](register-scan-azure-sql-database.md)
- [Felügyelt példány Azure SQL Database](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)
 
## <a name="register-an-azure-multiple-source"></a>Azure-beli több forrás regisztrálása

Ha egy új Azure-beli több forrást szeretne regisztrálni az adatkatalógusban, tegye a következőket:

1. Navigáljon a hatáskörébe-fiókjába
1. A bal oldali navigációs sávon válassza a **források** lehetőséget
1. **Regisztráció** kiválasztása
1. A **források regisztrálása** lapon válassza az **Azure (több) lehetőséget.**
1. Válassza a **Folytatás** elemet

   :::image type="content" source="media/register-scan-azure-multiple-sources/register-azure-multiple.png" alt-text="Azure-beli több forrás regisztrálása":::

A **források regisztrálása (Azure Multiple)** képernyőn tegye a következőket:

1. Adja meg azt a **nevet** , amelyben az adatforrás szerepelni fog a katalógusban 
1. Válasszon ki egy **felügyeleti csoportot** a szűréshez
1. **Válasszon egy előfizetést vagy egy adott erőforráscsoportot** a legördülő menüben. A regisztrációs hatókör a kiválasztott előfizetésre vagy az erőforráscsoporthoz lesz beállítva  
1. Válasszon ki egy **gyűjteményt** , vagy hozzon létre egy újat (nem kötelező)
1. Az adatforrás regisztrálásának **befejezése**

   :::image type="content" source="media/register-scan-azure-multiple-sources/azure-multiple-source-setup.png" alt-text="Azure-beli több forrás beállítása":::

## <a name="creating-and-running-a-scan"></a>Vizsgálat létrehozása és futtatása

Új vizsgálat létrehozásához és futtatásához tegye a következőket:

1. Navigáljon a **források** szakaszhoz

1. Válassza ki a regisztrált adatforrást

1. Kattintson a részletek megtekintése elemre, és válassza az **+ új vizsgálat** lehetőséget, vagy a forrás csempén a gyors művelet ellenőrzése ikont használja.

1. Töltse ki a *nevet* , és válassza ki az összes olyan erőforrást, amelyet meg szeretne vizsgálni a forráson belül

    1. Meghagyhatja az *összeset* (ez olyan jövőbeli erőforrástípusok is, amelyek esetleg még nem léteznek az adott előfizetésen vagy az erőforráscsoporton belül)
    1. Kiválaszthatja a beolvasni kívánt **erőforrás-típusokat** . Ha ezt a lehetőséget választja, akkor az adott előfizetésben vagy az erőforráscsoport-ban létrehozható jövőbeli erőforrástípusok nem lesznek belefoglalva a vizsgálatokhoz, kivéve, ha a vizsgálatot a későbbiekben kifejezetten szerkesztik
    
    :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-source-scan.png" alt-text="Azure – több forrás vizsgálata":::

1. Válassza ki a hitelesítő adatokat az adatforráson belüli erőforrásokhoz való kapcsolódáshoz. 
    1. A **szülő szintű hitelesítő adatokat** MSI-ként vagy egy adott egyszerű szolgáltatásnév hitelesítő adataival választhatja ki, amelyet az előfizetés vagy az erőforráscsoport alá tartozó összes erőforrástípus esetében használhat.
    1. Azt is megteheti, hogy **kijelöli az erőforrás típusát, és más hitelesítő adatokat alkalmaz** az adott erőforrás típusára.
    1. Minden hitelesítő adat az adott típushoz tartozó összes erőforrás hitelesítési módszerének tekintendő.
    1. A kiválasztott hitelesítő adatokat az erőforrásokon be kell állítania, hogy sikeresen beolvassa őket a fenti [szakaszban](#Setting-up-authentication-to-scan-resources-under-a-subscription-or-resource-group) leírtak szerint.
1. Az egyes típusokon belül kiválaszthatja, hogy az összes erőforrást vagy egy részhalmazát megkeresi-e név alapján.
    1. Ha ezt a lehetőséget választja, az adott típushoz tartozó **összes** jövőbeli erőforrást a rendszer a következő vizsgálati futtatásokban is megvizsgálja
    1. Ha a megadott Storage-fiókokat vagy SQL-adatbázisokat választja, akkor az ezen az előfizetésen vagy az erőforráscsoporton belül létrehozott jövőbeli erőforrások nem lesznek belefoglalva a vizsgálatokhoz, kivéve, ha a vizsgálatokat a későbbiekben kifejezetten szerkesztik
 
1.  A folytatáshoz kattintson a **tovább** gombra. Teszteljük a hozzáférést annak ellenőrzéséhez, hogy alkalmazta-e a hatáskörébe MSI-t olvasóként az előfizetésben vagy az erőforráscsoporthoz. Ha a rendszer hibaüzenetet küld, kövesse az [alábbi utasításokat.](#Setting-up-authentication-for-enumerating-resources-under-a-subscription-or-resource-group)

1.  Válassza ki az előző lépésben kiválasztott erőforrástípusok **ellenőrzési szabályait** . A betekintő szabálykészlet beágyazottként is létrehozható.
  :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-scan-rule-set.png" alt-text="Azure több vizsgálati szabálykészlet kijelölése":::

1. Válassza ki a vizsgálati triggert. Ütemezheti úgy, hogy **hetente/havonta** vagy **egyszer** fusson

1. Tekintse át a vizsgálatot, és a beállítás befejezéséhez válassza a mentés lehetőséget.   

## <a name="viewing-your-scans-and-scan-runs"></a>A vizsgálatok és a vizsgálat futtatásának megtekintése

1. A forrás részleteinek megtekintéséhez kattintson a **részletek megtekintése** elemre a források szakaszban található csempén. 

      :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-source-detail.png" alt-text="Több forrás az Azure-ban – részletek"::: 

1. Tekintse meg a vizsgálat futtatásával kapcsolatos részleteket a **vizsgálat részletei** lapon.
    1. Az *Állapotsor* röviden összefoglalja a gyermekek erőforrásainak futási állapotát. Ekkor megjelenik az előfizetés vagy az erőforráscsoport szintjén.
    1. A zöld érték sikeres, míg a piros azt jelenti, hogy nem sikerült. A szürke érték azt jelenti, hogy a vizsgálat még folyamatban van
    1. Az egyes vizsgálatokra kattintva részletesebb információkat jeleníthet meg

      :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-scan-full-details.png" alt-text="Több vizsgálati adat az Azure-ban":::

1. Tekintse meg a legutóbbi sikertelen vizsgálat összegzését a forrás részletei lap alján. A futtatásokra vonatkozó részletesebb részletek megtekintéséhez is rákattinthat.

## <a name="manage-your-scans---edit-delete-or-cancel"></a>Ellenőrzések kezelése – Szerkesztés, törlés vagy megszakítás
A vizsgálat kezeléséhez vagy törléséhez tegye a következőket:

- Navigáljon a felügyeleti központhoz. Válassza ki az adatforrásokat a források és vizsgálat szakaszban, majd válassza ki a kívánt adatforrást.

- Válassza ki a kezelni kívánt vizsgálatot. A vizsgálat szerkesztéséhez kattintson a Szerkesztés lehetőségre.

- A vizsgálatot a Delete (Törlés) lehetőség kiválasztásával törölheti.
- Ha egy vizsgálat fut, azt is megszakíthatja

## <a name="next-steps"></a>Következő lépések

- [Az Azure-beli hatáskörébe tartozó adatkatalógus tallózása](how-to-browse-catalog.md)
- [Keresés az Azure-beli hatáskörébe Data Catalog](how-to-search-catalog.md)    
