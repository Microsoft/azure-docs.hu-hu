---
title: SQL Information Protection-szabályzat a Azure Security Center
description: Megtudhatja, hogyan szabhatja testre az Information Protection-szabályzatokat a Azure Security Centerban.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2ebf2bc7-232a-45c4-a06a-b3d32aaf2500
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2020
ms.author: memildin
ms.openlocfilehash: aa3492cb67a4ccd1c09a1f1cb55ddc4f2e00953d
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93318656"
---
# <a name="sql-information-protection-policy-in-azure-security-center"></a>SQL Information Protection-szabályzat a Azure Security Center
 
Az SQL Information Protection [adatfelderítési és besorolási mechanizmusa](../azure-sql/database/data-discovery-and-classification-overview.md) [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md), [Azure SQL felügyelt példányba](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)és [Azure szinapszis analyticsbe](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)van építve. Fejlett képességeket biztosít az adatbázisaiban található bizalmas adatok felfedéséhez, besorolásához, címkézéséhez és jelentéséhez.

A besorolási mechanizmus a besorolási besorolást alkotó két elsődleges szerkezeten alapul:

- **Labels (címkék** ) – a fő besorolási attribútumok, amelyek az oszlopban tárolt adatmennyiség érzékenységi szintjének meghatározására szolgálnak. 
- **Adattípusok** – további részletességi adatokat biztosít az oszlopban tárolt adatok típusához.

A Security Centeron belüli adatvédelmi házirend-beállítások olyan előre definiált címkéket és adattípusokat biztosítanak, amelyek a besorolási motor alapértelmezett értékeként szolgálnak. A szabályzatot a szervezet igényeinek megfelelően testreszabhatja az alább leírtak szerint.

:::image type="content" source="./media/security-center-info-protection-policy/sql-information-protection-policy-page.png" alt-text="Az SQL Information Protection-szabályzatot megjelenítő oldal":::
 



## <a name="how-do-i-access-the-sql-information-protection-policy"></a>Hogyan hozzáférni az SQL Information Protection-szabályzathoz?

Az Information Protection-házirend három módon érhető el:

- **(Ajánlott)** A Security Center díjszabása és beállításai oldalon.
- A biztonsági javaslatból "a bizalmas adatokat az SQL-adatbázisokban osztályozni kell".
- Az Azure SQL DB adatfelderítési oldaláról.

Ezek mindegyike az alábbi megfelelő lapon látható.



### <a name="from-security-centers-settings"></a>[**Security Center beállításai**](#tab/sqlip-tenant)

### <a name="access-the-policy-from-security-centers-pricing-and-settings-page"></a>A szabályzat elérése Security Center díjszabási és beállítási oldaláról <a name="sqlip-tenant"></a>

Az Azure-bérlőhöz tartozó Information Protection-házirend testreszabásához [rendszergazdai jogosultságokkal kell rendelkeznie a bérlő legfelső szintű felügyeleti csoportjában](security-center-management-groups.md). 

A Security Center **díjszabása és beállításai** lapon válassza az **SQL Information Protection** lehetőséget.

:::image type="content" source="./media/security-center-info-protection-policy/pricing-settings-link-to-information-protection.png" alt-text="Az SQL Information Protection házirend elérése Azure Security Center":::



### <a name="from-security-centers-recommendation"></a>[**Security Center javaslata**](#tab/sqlip-db)

### <a name="access-the-policy-from-the-security-center-recommendation"></a>A szabályzat elérése a Security Center javaslatból <a name="sqlip-db"></a>

Az adatbázis adatfelderítési és besorolási oldalának megtekintéséhez használja Security Center javaslatát, "az SQL-adatbázisok bizalmas adatait osztályozni kell". Itt láthatja a felderített oszlopokat is, amelyek tartalmazzák a besorolást.

1. A Security Center **ajánlásai** oldalon keresse meg az **SQL-adatbázisaiban található bizalmas adatokra** vonatkozó ajánlást.

    :::image type="content" source="./media/security-center-info-protection-policy/sql-sensitive-data-recommendation.png" alt-text="Az SQL Information Protection-szabályzatokhoz hozzáférést biztosító javaslat megkeresése":::

1. A javaslat részletei lapon válassza ki a megfelelő adatbázist az **kifogástalan** vagy **sérült** lapok közül.

1. Megnyílik az **Adatfelderítési & besorolási** lapja. Válassza a **Konfigurálás** lehetőséget. 

    :::image type="content" source="./media/security-center-info-protection-policy/access-policy-from-security-center-recommendation.png" alt-text="Az SQL Information Protection-szabályzat megnyitása a Azure Security Center vonatkozó javaslat alapján":::



### <a name="from-azure-sql"></a>[**Az Azure SQL-ből**](#tab/sqlip-azuresql)

### <a name="access-the-policy-from-azure-sql"></a>Hozzáférés a Szabályzathoz az Azure SQL-ből <a name="sqlip-azuresql"></a>

1. A Azure Portal nyissa meg az Azure SQL-t.

    :::image type="content" source="./media/security-center-info-protection-policy/open-azure-sql.png" alt-text="Az Azure SQL megnyitása a Azure Portalról":::

1. Válasszon ki egy adatbázist.

1. A menü **Biztonság** területén nyissa meg az **adatfelderítési & besorolási** oldalát (1), és válassza a **Konfigurálás** (2) lehetőséget.

    :::image type="content" source="./media/security-center-info-protection-policy/access-policy-from-azure-sql.png" alt-text="Az SQL Information Protection-szabályzat megnyitása az Azure SQL-ből":::

--- 


## <a name="customize-your-information-types"></a>Az adattípusok testreszabása

Az adattípusok kezelése és testreszabása:

1. Válassza a **kezelés adattípusok** lehetőséget.

    :::image type="content" source="./media/security-center-info-protection-policy/manage-types.png" alt-text="Az Information Protection-szabályzat adattípusának kezelése":::

1. Új **adattípus** hozzáadásához válassza a felső menüben az **információ létrehozása típust** . Megadhatja az **adattípus** nevét, leírását és a keresési minta sztringjét. A keresési minta sztringek helyettesítő karakterekkel rendelkező kulcsszavakat is használhatnak (a (z) "%" karakterrel), amelyet az automatikus felderítési motor használ a bizalmas adatok azonosításához az adatbázisokban az oszlopok metaadatai alapján.
 
    :::image type="content" source="./media/security-center-info-protection-policy/configure-new-type.png" alt-text="Új adattípus konfigurálása az Information Protection-szabályzathoz":::

1. A beépített **adattípusokat** további keresési minta karakterláncok hozzáadásával, a meglévő sztringek letiltásával vagy a Leírás módosításával is konfigurálhatja. A beépített **adattípusok** nem törölhetők, és nem szerkeszthetők a nevük. 
1. Az **adattípusok** növekvő felderítési rangsorolási sorrendben vannak felsorolva, ami azt jelenti, hogy a listában magasabb típusok először is megpróbálják egyeztetni az adatokat. Az adattípusok rangsorolásának módosításához húzza a típusokat a táblázat jobb oldali helyére, vagy használja a **feljebb és lejjebb gombokat a sorrend** módosításához. **Move down** 
1. Ha elkészült, kattintson **az OK gombra** .
1. Az adattípusok kezelésének befejezése után ügyeljen arra, hogy a megfelelő címkéket társítsa a megfelelő címkékhez, ehhez kattintson a **Konfigurálás** egy adott címkére lehetőségre, és adja hozzá vagy törölje az adattípusokat.
1. A módosítások alkalmazásához kattintson a **Mentés** elemre a fő **címkék** oldalon.
 

## <a name="exporting-and-importing-a-policy"></a>Szabályzat exportálása és importálása

Letölthet egy JSON-fájlt a megadott címkékkel és adattípusokkal, szerkesztheti a fájlt a választott szerkesztőben, majd importálhatja a frissített fájlt. 

:::image type="content" source="./media/security-center-info-protection-policy/export-import.png" alt-text="Az Information Protection-szabályzat exportálása és importálása":::

> [!NOTE]
> A házirend-fájl importálásához bérlői szintű engedélyekre van szükség. 


## <a name="manage-sql-information-protection-using-azure-powershell"></a>Az SQL Information Protection kezelése Azure PowerShell használatával

- [Get-AzSqlInformationProtectionPolicy](/powershell/module/az.security/get-azsqlinformationprotectionpolicy): lekéri az érvényes bérlői SQL Information Protection-házirendet.
- [Set-AzSqlInformationProtectionPolicy](/powershell/module/az.security/set-azsqlinformationprotectionpolicy): beállítja a hatályos bérlői SQL Information Protection-házirendet.
 

## <a name="next-steps"></a>Következő lépések
 
Ebben a cikkben megtanulta, hogyan határozhat meg egy SQL Information Protection szabályzatot a Azure Security Centerban. Ha többet szeretne megtudni az SQL-Information Protection használatáról az SQL-adatbázisokban található bizalmas adatok osztályozásához és védelméhez, olvassa el az [adatfelderítés és-besorolás Azure SQL Database](../azure-sql/database/data-discovery-and-classification-overview.md). 

A Azure Security Center biztonsági házirendjeivel és adatvédelmével kapcsolatos további információkért tekintse meg a következő cikkeket:
 
- [Biztonsági szabályzatok beállítása Azure Security Centerban](tutorial-security-policy.md): Ismerje meg, hogyan konfigurálhatja az Azure-előfizetések és-erőforráscsoportok biztonsági szabályzatait
- [Azure Security Center adatbiztonság](security-center-data-security.md): megtudhatja, hogyan kezeli és védi az adatSecurity Center