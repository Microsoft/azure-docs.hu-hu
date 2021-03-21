---
title: Azure Data Lake Storage regisztrálása és vizsgálata (ADLS) Gen1
description: Ez az oktatóanyag azt ismerteti, hogyan lehet beolvasni a Azure Data Lake Storage Gen1 adatait az Azure hatáskörébe.
author: shsandeep123
ms.author: sandeepshah
ms.service: data-catalog
ms.subservice: data-catalog-gen2
ms.topic: how-to
ms.date: 11/30/2020
ms.openlocfilehash: a831681f892de9f6aae50fa9a2fcf71e883fe6ba
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97693710"
---
# <a name="register-and-scan-azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1 regisztrálása és vizsgálata

Ez a cikk azt ismerteti, hogyan regisztrálhatók az adatforrások az Azure-ban, és hogyan állítható be a Azure Data Lake Storage Gen1.

> [!Note]
> Az Azure Data Lake Storage Gen2 mostantól általánosan elérhető. Javasoljuk, hogy már ma kezdje el használni. További információkért tekintse meg a [termék oldalát](https://azure.microsoft.com/services/storage/data-lake-storage/).

## <a name="supported-capabilities"></a>Támogatott képességek

A Azure Data Lake Storage Gen1 adatforrás a következő funkciókat támogatja:

- **Teljes és növekményes vizsgálatok** a metaadatok és besorolások rögzítésére Azure Data Lake Storage Gen1

- Adategységek közötti **vonal** a másolási/adatfolyam tevékenységekhez

## <a name="prerequisites"></a>Előfeltételek

- Az adatforrások regisztrálása előtt hozzon létre egy Azure-beli hatáskörébe tartozó fiókot. A hatáskörébe tartozó fiókok létrehozásával kapcsolatos további információkért tekintse meg a rövid útmutató [: Azure hatáskörébe tartozó fiók létrehozása](create-catalog-portal.md)című témakört.
- Az Azure-beli adatforrások rendszergazdájának kell lennie

## <a name="setting-up-authentication-for-a-scan"></a>Hitelesítés beállítása vizsgálathoz

A Azure Data Lake Storage Gen1 a következő hitelesítési módszereket támogatja:

- Felügyelt identitás
- Szolgáltatásnév

### <a name="managed-identity-recommended"></a>Felügyelt identitás (ajánlott)

Az egyszerű és a biztonságos használat érdekében érdemes lehet a hatáskörébe MSI-t használni az adattárral való hitelesítéshez.

Ha a Data Catalog MSI használatával szeretne vizsgálatot beállítani, először meg kell adnia a hatáskörébe tartozó fiókot az adatforrás vizsgálatához. Ezt a lépést a vizsgálat beállítása *előtt* kell elvégezni, vagy a vizsgálat sikertelen lesz.

#### <a name="adding-the-data-catalog-msi-to-an-azure-data-lake-storage-gen1-account"></a>Az Data Catalog MSI hozzáadása egy Azure Data Lake Storage Gen1-fiókhoz

A katalógus MSI-fájlját az előfizetés, az erőforráscsoport vagy az erőforrás szintjén adhatja hozzá attól függően, hogy mit szeretne beolvasni a alkalmazásban.

> [!Note]
> Az előfizetés tulajdonosának kell lennie ahhoz, hogy felügyelt identitást tudjon hozzáadni egy Azure-erőforráshoz.

1. A [Azure Portal](https://portal.azure.com)keresse meg azt az előfizetést, erőforráscsoportot vagy erőforrást (például egy Azure Data Lake Storage Gen1 Storage-fiókot), amelyet engedélyezni szeretne a katalógusnak a vizsgálathoz.

2. Kattintson az **Áttekintés** elemre, majd válassza **az adatkezelő** elemet.

   :::image type="content" source="./media/register-scan-adls-gen1/access-control.png" alt-text="Hozzáférés-vezérlés kiválasztása":::

3. Kattintson a **hozzáférés** gombra a felső navigációs sávon

   :::image type="content" source="./media/register-scan-adls-gen1/access.png" alt-text="Kattintson a hozzáférés gombra":::

4. Kattintson a **Hozzáadás** gombra. Adja hozzá a **hatáskörébe-katalógust** a felhasználó vagy csoport kiválasztása lehetőség kiválasztásával. Válassza az **olvasási** és **végrehajtási** engedélyek lehetőséget. Ügyeljen arra, hogy a Hozzáadás a következő képernyőképen látható módon **és az összes gyermek** elemet válassza, és kattintson az **OK** 
    :::image type="content" source="./media/register-scan-adls-gen1/managed-instance-authentication.png" alt-text="MSI-hitelesítés részletei"::: elemre.

5. Ha a kulcstartó még nincs csatlakoztatva a hatáskörébe, [létre kell hoznia egy új Key Vault-kapcsolatot](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).

6. Végül [hozzon létre egy új hitelesítő adatot](manage-credentials.md#create-a-new-credential) az egyszerű szolgáltatásnév használatával a vizsgálat beállításához
> [!Note]
> Miután hozzáadta a katalógus MSI-fájlját az adatforráshoz, várjon akár 15 percet, amíg az engedélyek propagálása megtörténik a vizsgálat beállítása előtt.

Körülbelül 15 perc elteltével a katalógusnak rendelkeznie kell a megfelelő engedélyekkel ahhoz, hogy ellenőrizni tudja az erőforrás (oka) t.

### <a name="service-principal"></a>Szolgáltatásnév

Egyszerű szolgáltatásnév használatához először létre kell hoznia egyet a következő lépésekkel:

1. Navigáljon a [Azure Portal](https://portal.azure.com).

2. A bal oldali menüben válassza a **Azure Active Directory** lehetőséget.

3. Válassza az **Alkalmazásregisztrációk** lehetőséget.

4. Válassza az **+ új alkalmazás regisztrálása** lehetőséget.

5. Adja meg az **alkalmazás** nevét (az egyszerű szolgáltatásnév).

6. **Csak a szervezeti címtárban** válassza a fiókok elemet.

7. Az **átirányítási URI** esetében válassza a **web** lehetőséget, és adja meg a kívánt URL-címet. nem kell valós vagy munkahelyi.

8. Válassza a **Regisztráció** lehetőséget.

9. Másolja az értékeket a megjelenítendő név és az alkalmazás-azonosító alapján.

#### <a name="adding-the-data-catalog-service-principal-to-an-azure-data-lake-storage-gen1-account"></a>Az Data Catalog egyszerű szolgáltatásnév hozzáadása egy Azure Data Lake Storage Gen1-fiókhoz
1. A [Azure Portal](https://portal.azure.com)keresse meg azt az előfizetést, erőforráscsoportot vagy erőforrást (például egy Azure Data Lake Storage Gen1 Storage-fiókot), amelyet engedélyezni szeretne a katalógusnak a vizsgálathoz.

2. Kattintson az **Áttekintés** elemre, majd válassza **az adatkezelő** elemet.

   :::image type="content" source="./media/register-scan-adls-gen1/access-control.png" alt-text="Hozzáférés-vezérlés kiválasztása":::

3. Kattintson a **hozzáférés** gombra a felső navigációs sávon

   :::image type="content" source="./media/register-scan-adls-gen1/access.png" alt-text="Kattintson a hozzáférés gombra":::

4. Kattintson a **Hozzáadás** gombra. Adja hozzá az **egyszerű szolgáltatásnév alkalmazást** a felhasználó vagy csoport kiválasztása lehetőség kiválasztásával. Válassza az **olvasási** és **végrehajtási** engedélyek lehetőséget. Ügyeljen arra, hogy a Hozzáadás a következő képernyőképen látható módon válassza **ezt a mappát és az összes gyermek** elemet, majd kattintson az **OK** 
    :::image type="content" source="./media/register-scan-adls-gen1/service-principal-authentication.png" alt-text="egyszerű szolgáltatás hitelesítésének részletei"::: elemre.

5. Ha a kulcstartó még nincs csatlakoztatva a hatáskörébe, [létre kell hoznia egy új Key Vault-kapcsolatot](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).

6. Végül [hozzon létre egy új hitelesítő adatot](manage-credentials.md#create-a-new-credential) az egyszerű szolgáltatásnév használatával a vizsgálat beállításához.

## <a name="register-azure-data-lake-storage-gen1-data-source"></a>Azure Data Lake Storage Gen1 adatforrás regisztrálása

Ha új ADLS Gen1 fiókot szeretne regisztrálni a adatkatalógusban, tegye a következőket:

1. Navigáljon a hatáskörébe Data Catalog.
2. A bal oldali navigációs sávon válassza a **források** lehetőséget.
3. **Regisztráció** kiválasztása
4. A **források regisztrálása** lapon válassza a **Azure Data Lake Storage Gen1** lehetőséget. 
5. Válassza a **Folytatás** lehetőséget.

A források regisztrálása (Azure Data Lake Storage Gen1) képernyőn tegye a következőket:

1. Adja meg azt a **nevet** , amelyben az adatforrás szerepelni fog a katalógusban.
2. Válassza ki az előfizetést a Storage-fiókok szűréséhez
3. Válasszon Storage-fiókot
4. Válasszon ki egy gyűjteményt, vagy hozzon létre egy újat (nem kötelező)
5. Fejezze be az adatforrás regisztrálását.

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Következő lépések

- [Az Azure-beli hatáskörébe tartozó adatkatalógus tallózása](how-to-browse-catalog.md)
- [Keresés az Azure-beli hatáskörébe Data Catalog](how-to-search-catalog.md)
