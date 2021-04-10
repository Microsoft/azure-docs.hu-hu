---
title: 'Azure Portal: dinamikus adatmaszkolás'
description: A Azure SQL Database dinamikus adatmaszkolásának első lépései a Azure Portal
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 04/28/2020
ms.openlocfilehash: e551925bbd1426256a81ac2ca3f245af7697245b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98028012"
---
# <a name="get-started-with-sql-database-dynamic-data-masking-with-the-azure-portal"></a>Ismerkedjen meg SQL Database dinamikus adatmaszkolással a Azure Portal
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ez a cikk bemutatja, hogyan valósítható meg a [dinamikus adatmaszkolás](dynamic-data-masking-overview.md) a Azure Portal. A dinamikus adatmaszkolást [Azure SQL Database parancsmagok](/powershell/module/az.sql/) vagy a [REST API](/rest/api/sql/)használatával is megvalósíthatja.

> [!NOTE]
> Ez a funkció nem állítható be a portálon a felügyelt SQL-példányhoz (PowerShell vagy REST API használata). További információ: [dinamikus adatmaszkolás](/sql/relational-databases/security/dynamic-data-masking).

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>Dinamikus adatmaszkolás beállítása az adatbázishoz a Azure Portal használatával

1. Indítsa el a Azure Portal a következő helyen: [https://portal.azure.com](https://portal.azure.com) .
2. Navigáljon a maszkoláshoz használni kívánt bizalmas adatokat tartalmazó adatbázis beállítások lapjára.
3. Kattintson a **dinamikus adatmaszkolás** panelre az adatbázis **Biztonság** szakaszában.

   ![A dinamikus adatmaszkolást kiemelő biztonsági szakaszt bemutató képernyőkép.](./media/dynamic-data-masking-configure-portal/4_ddm_settings_tile.png)

4. A **dinamikus adatmaszkolás** konfigurálása lapon előfordulhat, hogy egyes adatbázis-oszlopok láthatók, amelyeket a javaslatok motorja megjelöl a maszkoláshoz. A javaslatok elfogadásához egyszerűen kattintson a **maszk hozzáadása** elemre egy vagy több oszlophoz, és az oszlop alapértelmezett típusa alapján létrehoz egy maszkot. A maszkolási függvényt úgy módosíthatja, ha a maszkolási szabályra kattint, és a maszkolási mező formátumát más formátumra szerkeszti. A beállítások mentéséhez kattintson a **Mentés** gombra.

    ![A dinamikus adatmaszkolás konfigurációs lapját bemutató képernyőkép.](./media/dynamic-data-masking-configure-portal/5_ddm_recommendations.png)

5. Ha az adatbázis bármely oszlopához hozzá szeretne adni egy maszkot, a **dinamikus adatmaszkolás** konfigurálása lap tetején kattintson a **maszk hozzáadása** elemre a **maszkolási szabály hozzáadása** konfiguráció lap megnyitásához.

    ![Képernyőfelvétel: a maszkolási szabály hozzáadása konfiguráció lapja.](./media/dynamic-data-masking-configure-portal/6_ddm_add_mask.png)

6. Válassza ki a **sémát**, a **táblát** és az **oszlopot** a maszkoláshoz kijelölt mező definiálásához.
7. **Válassza ki, hogyan legyen maszk** a bizalmas adatmaszkolási kategóriák listájából.

    ![Képernyőkép, amely az érzékeny adatmaszkolási kategóriákat jeleníti meg a válassza ki a maszkolás módját szakaszban.](./media/dynamic-data-masking-configure-portal/7_ddm_mask_field_format.png)

8. Kattintson a **Hozzáadás** elemre az adatmaszkolási szabály lapon a maszkolási szabályok a dinamikus adatmaszkolási házirendben való frissítéséhez.
9. Írja be azokat az SQL-felhasználókat vagy Azure Active Directory (Azure AD) identitásokat, amelyeket ki szeretne zárni a maszkolásból, és hozzáférhessen a nem maszkolt bizalmas adatokhoz. Ennek a felhasználóknak pontosvesszővel tagolt listának kell lennie. A rendszergazdai jogosultsággal rendelkező felhasználók mindig hozzáférhetnek az eredeti, kitakaratlan adatbázisokhoz.

    ![Navigációs panel](./media/dynamic-data-masking-configure-portal/8_ddm_excluded_users.png)

    > [!TIP]
    > Annak érdekében, hogy az alkalmazás rétege bizalmas adatokat jelenítsen meg az alkalmazás privilegizált felhasználói számára, adja hozzá az SQL-felhasználót vagy az Azure AD-identitást, amelyet az alkalmazás az adatbázis lekérdezéséhez használ. Erősen ajánlott, hogy ez a lista minimális számú jogosultsággal rendelkező felhasználót tartalmazzon a bizalmas adatok expozíciójának minimalizálása érdekében.

10. Az új vagy frissített maszkolási szabályzat mentéséhez kattintson a **Mentés** gombra az adatmaszkolás konfigurálása lapon.

## <a name="next-steps"></a>Következő lépések

- A dinamikus adatmaszkolás áttekintését lásd: [dinamikus adatmaszkolás](dynamic-data-masking-overview.md).
- A dinamikus adatmaszkolást [Azure SQL Database parancsmagok](/powershell/module/az.sql/) vagy a [REST API](/rest/api/sql/)használatával is megvalósíthatja.
