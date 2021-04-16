---
title: Új bérlő regisztrálása Power BI beolvasása (előzetes verzió)
description: Megtudhatja, hogyan regisztrálhat és vizsgálhat meg egy új bérlőt az Azure Purview Power BI használatával.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/19/2020
ms.openlocfilehash: 6646f131488a5ae4aa9b20fe614d7ebb46133444
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538867"
---
# <a name="register-and-scan-a-power-bi-tenant-preview"></a>Új bérlő regisztrálása Power BI beolvasása (előzetes verzió)

Ez a cikk bemutatja, hogyan regisztrálhat és vizsgálhat meg egy új bérlőt az Azure Purview Power BI használatával.

> [!Note]
> Ha a Purview-példány és az Power BI-bérlő ugyanabban az Azure-bérlőben van, csak felügyelt identitással (MSI) való hitelesítéssel állíthatja be a Power BI bérlő vizsgálatának beállítását. 

## <a name="create-a-security-group-for-permissions"></a>Biztonsági csoport létrehozása az engedélyekhez

A hitelesítés beállításához hozzon létre egy biztonsági csoportot, és adja hozzá a Purview felügyelt identitást.

1. A [(Azure Portal)](https://portal.azure.com)között keressen rá a **Azure Active Directory.**
1. Hozzon létre egy új biztonsági csoportot a Azure Active Directory az [Alapszintű](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)csoport létrehozása és tagok hozzáadása a Azure Active Directory.

    > [!Tip]
    > Ezt a lépést kihagyhatja, ha már rendelkezik egy használni kívánt biztonsági csoporttal.

1. **Csoporttípusként** válassza **a Biztonság lehetőséget.**

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/security-group.png" alt-text="Biztonsági csoport típusa":::

1. Adja hozzá a Purview felügyelt identitást ehhez a biztonsági csoporthoz. Válassza **a Tagok,** majd a **+ Tagok hozzáadása lehetőséget.**

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-group-member.png" alt-text="Adja hozzá a katalógus felügyelt példányát a csoporthoz.":::

1. Keresse meg és jelölje ki a Purview felügyelt identitást.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-catalog-to-group-by-search.png" alt-text="Katalógus hozzáadása kereséssel":::

    A sikeres hozzáadásról értesítést kell kapnia.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/success-add-catalog-msi.png" alt-text="Katalógus MSI-sikeres hozzáadása":::

## <a name="associate-the-security-group-with-the-tenant"></a>A biztonsági csoport társítása a bérlővel

1. Jelentkezzen be a [Power BI portálra.](https://app.powerbi.com/admin-portal/tenantSettings)
1. Válassza a **Bérlői beállítások** lapot.

    > [!Important]
    > A bérlői beállítások oldalának Power BI rendszergazdának kell lennie.

1. Válassza **a Rendszergazdai API-beállításokA** szolgáltatásnév számára csak olvasható  >  **api-k Power BI (előzetes verzió) lehetőséget.**
1. Válassza **az Adott biztonsági csoportok lehetőséget.**

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/allow-service-principals-power-bi-admin.png" alt-text="A kép azt mutatja be, hogyan engedélyezheti a szolgáltatásnévnek, hogy csak olvasási Power BI API-engedélyeket":::

    > [!Caution]
    > Ha engedélyezi, hogy a létrehozott biztonsági csoport (amely a Purview felügyelt identitását tagként tartalmazza) csak olvasható Power BI-rendszergazdai API-kat használjon, akkor engedélyezi számára a bérlőben található összes Power BI-összetevő metaadatainak (például irányítópult- és jelentésnevek, tulajdonosok, leírások stb.) hozzáférését is. Miután lekérte a metaadatokat az Azure Purview, a Purview engedélyeibe, és nem a Power BI, határozza meg, hogy ki láthatja a metaadatokat.

    > [!Note]
    > A biztonsági csoportot eltávolíthatja a fejlesztői beállításokból, de a korábban kinyert metaadatok nem lesznek eltávolítva a Purview fiókból. Ha szeretné, külön is törölheti.

## <a name="register-your-power-bi-and-set-up-a-scan"></a>A Power BI regisztrálása és vizsgálat beállítása

Most, hogy a Felügyelt identitás véglegesnézete jogosultságot adott a Power BI-bérlő rendszergazdai API-jának való csatlakozáshoz, beállíthatja a vizsgálatot az Azure Purview Studióban.

1. Válassza a **Felügyeleti központ** ikont.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/management-center.png" alt-text="Felügyeleti központ ikon.":::

1. Ezután válassza **az + Új lehetőséget** az **Adatforrások oldalon.**

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/data-sources.png" alt-text="Új adatforrás gomb képe":::

    Adatforrásként **Power BI** az Adatforrás lehetőséget.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/select-power-bi-data-source.png" alt-text="A kiválasztható adatforrások listáját bemutató kép":::

3. Adjon rövid Power BI a saját példányának.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-friendly-name.png" alt-text="Adatforrás-Power BI nevét bemutató kép":::

    A névnek 3–63 karakter hosszúságúnak kell lennie, és csak betűket, számokat, aláhúzásjeleket és kötőjeleket tartalmazhat.  A szóközök nem engedélyezettek.

    Alapértelmezés szerint a rendszer megkeresi az Power BI bérlőt, amely ugyanabban az Azure-előfizetésben található.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-datasource-registered.png" alt-text="Power BI adatforrás regisztrálva":::

    > [!Note]
    > A Power BI csak egy példányhoz engedélyezett az adatforrások regisztrációja és beolvasása.


4. Nevezze el a vizsgálatot. Ezután válassza ki a személyes munkaterületek be- vagy kizárásának lehetőségét. Figyelje meg, hogy az egyetlen támogatott hitelesítési módszer a **felügyelt identitás.**

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-scan-setup.png" alt-text="Kép a Power BI beállításról":::

    > [!Note]
    > * Ha egy vizsgálat konfigurációját személyes munkaterület felvételére vagy kizárására váltja, az elindítja a PowerBI-forrás teljes vizsgálatot
    > * A vizsgálat nevének 3–63 karakter hosszúságúnak kell lennie, és csak betűket, számokat, aláhúzásjeleket és kötőjeleket tartalmazhat. A szóközök nem engedélyezettek.

5. Beállít egy vizsgálati eseményindítót. A **lehetőségek: Egyszer,** **7 naponta,** és **30 naponta.**

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/scan-trigger.png" alt-text="Eseményindító képének beolvasása":::

6. Az **Új vizsgálat áttekintése oldalon válassza** a Mentés és **futtatás** lehetőséget a vizsgálat elindításához.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/save-run-power-bi-scan.png" alt-text="Képernyő képe Power BI mentéshez és futtatáshoz":::

## <a name="next-steps"></a>Következő lépések

- [Az Azure Purview Data Catalog tallózása](how-to-browse-catalog.md)
- [Keresés az Azure Purview Data Catalog](how-to-search-catalog.md)
