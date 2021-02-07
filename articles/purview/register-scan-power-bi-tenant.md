---
title: Power BI bérlő regisztrálása és bevizsgálása (előzetes verzió)
description: Megtudhatja, hogyan regisztrálhat és vizsgálhat meg egy Power BI bérlőt az Azure hatáskörébe-portál használatával.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/19/2020
ms.openlocfilehash: 78187b2cbb6603a0ae0df55465b9a5ce5e7dca7f
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2021
ms.locfileid: "99807546"
---
# <a name="register-and-scan-a-power-bi-tenant-preview"></a>Power BI bérlő regisztrálása és bevizsgálása (előzetes verzió)

Ez a cikk bemutatja, hogyan regisztrálhat és vizsgálhat meg egy Power BI bérlőt az Azure hatáskörébe-portál használatával.

> [!Note]
> Ha a hatáskörébe tartozó példány és a Power BI bérlő ugyanabban az Azure-bérlőben található, akkor csak a felügyelt identitás (MSI) hitelesítést használhatja egy Power BI bérlő vizsgálatának beállításához. 

## <a name="create-a-security-group-for-permissions"></a>Biztonsági csoport létrehozása engedélyekhez

A hitelesítés beállításához hozzon létre egy biztonsági csoportot, és adja hozzá a katalógus felügyelt identitását.

1. A [Azure Portal](https://portal.azure.com)keresse meg a **Azure Active Directory**.
1. Hozzon létre egy új biztonsági csoportot a Azure Active Directoryban, [és hozzon létre egy alapszintű csoportot, és vegyen fel tagokat a Azure Active Directory használatával](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

    > [!Tip]
    > Ezt a lépést kihagyhatja, ha már rendelkezik egy használni kívánt biztonsági csoporttal.

1. Válassza a **Biztonság** lehetőséget a **csoport típusaként**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/security-group.png" alt-text="Biztonsági csoport típusa":::

1. Adja hozzá a katalógus felügyelt identitását ehhez a biztonsági csoporthoz. Válassza a **tagok**, majd a **+ Tagok hozzáadása** elemet.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-group-member.png" alt-text="Adja hozzá a katalógus felügyelt példányát a csoporthoz.":::

1. Keresse meg a katalógust, és válassza ki.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-catalog-to-group-by-search.png" alt-text="Katalógus hozzáadása a kereséshez":::

    Ekkor megjelenik egy sikeres értesítés, amely azt mutatja, hogy hozzá lett adva.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/success-add-catalog-msi.png" alt-text="A katalógus MSI-fájljának hozzáadása sikeres":::

## <a name="associate-the-security-group-with-the-tenant"></a>A biztonsági csoport hozzárendelése a bérlőhöz

1. Jelentkezzen be a [Power bi felügyeleti portálra](https://app.powerbi.com/admin-portal/tenantSettings).
1. Válassza ki a **bérlői beállítások** lapot.

    > [!Important]
    > A bérlői beállítások lap megtekintéséhez Power BI rendszergazdának kell lennie.

1. Válassza a **felügyeleti API**  >  **-beállítások lehetővé teszik az egyszerű szolgáltatásoknak a csak olvasási jogosultsággal rendelkező Power bi felügyeleti API-k (előzetes verzió) használatát**.
1. Válassza az **egyes biztonsági csoportok** lehetőséget.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/allow-service-principals-power-bi-admin.png" alt-text="Kép: az egyszerű Power BI felügyeleti API-engedélyek beolvasásának engedélyezése a szolgáltatásoknak":::

    > [!Caution]
    > Ha engedélyezi a létrehozott biztonsági csoportot (amelyet a saját adatkatalógus felügyelt identitása tagként használ) a csak olvasási jogosultsággal rendelkező Power BI felügyeleti API-k használatához, akkor azt is lehetővé teszi, hogy a rendszer a metaadatokat (például az irányítópultot, a jelentés nevét, a tulajdonosokat, a leírásokat stb.) a bérlő összes Power BI összetevője számára is hozzáférhessen. Miután behúzta a metaadatokat az Azure hatáskörébe, a hatáskörébe tartozó engedélyek, nem Power BI engedélyek, határozza meg, ki láthatja a metaadatokat.

    > [!Note]
    > A biztonsági csoportot eltávolíthatja a fejlesztői beállításokból, de a korábban kibontott metaadatokat a rendszer nem távolítja el a hatáskörébe tartozó fiókból. Ha kívánja, külön is törölheti.

## <a name="register-your-power-bi-and-set-up-a-scan"></a>A Power BI regisztrálása és a vizsgálat beállítása

Most, hogy megadta a katalógus engedélyeit a Power BI bérlő felügyeleti API-hoz való kapcsolódáshoz, beállíthatja a vizsgálatot a katalógus-portálról.

Első lépésként adjon hozzá egy speciális funkció-jelölőt a hatáskörébe URL-címéhez 

1. Válassza ki a **felügyeleti központ** ikonját.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/management-center.png" alt-text="Felügyeleti központ ikonja":::

1. Ezután válassza az **+ új** lehetőséget az **adatforrások** területen.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/data-sources.png" alt-text="Új adatforrás gombjának képe":::

    Adatforrásként válassza a **Power bi** lehetőséget.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/select-power-bi-data-source.png" alt-text="A választható adatforrások listáját bemutató kép":::

3. Adjon egy rövid nevet a Power BI-példánynak.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-friendly-name.png" alt-text="Kép: Power BI adatforrás-felhasználóbarát név":::

    A névnek 3-63 karakter hosszúnak kell lennie, és csak betűket, számokat, aláhúzásokat és kötőjeleket tartalmazhat.  A szóközök nem engedélyezettek.

    Alapértelmezés szerint a rendszer megkeresi a Power BI bérlőt, amely ugyanabban az Azure-előfizetésben található.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-datasource-registered.png" alt-text="Power BI adatforrás regisztrálva":::

    > [!Note]
    > Power BI esetében az adatforrás regisztrálása és vizsgálata csak egy példány esetében engedélyezett.


4. Adja meg a vizsgálat nevét. Figyelje meg, hogy az egyetlen támogatott hitelesítési módszer a **felügyelt identitás**.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-scan-setup.png" alt-text="A Power BI Scan telepítőjét ábrázoló kép":::

    A vizsgálat nevének 3-63 karakter hosszúnak kell lennie, és csak betűket, számokat, aláhúzásokat és kötőjeleket tartalmazhat.  A szóközök nem engedélyezettek.

5. Egy vizsgálati trigger beállítása. A lehetőségek **egyszer**, **7 naponta** és **30 naponként**.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/scan-trigger.png" alt-text="Trigger rendszerképének vizsgálata":::

6. Az **új vizsgálat áttekintése** lapon válassza a **Mentés és Futtatás** lehetőséget a vizsgálat elindításához.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/save-run-power-bi-scan.png" alt-text="Power BI képernyő rendszerképének mentése és futtatása":::

## <a name="next-steps"></a>Következő lépések

- [Az Azure-beli hatáskörébe tartozó adatkatalógus tallózása](how-to-browse-catalog.md)
- [Keresés az Azure-beli hatáskörébe Data Catalog](how-to-search-catalog.md)