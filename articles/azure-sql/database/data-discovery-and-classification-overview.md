---
title: Adatfelderítés és -besorolás
description: Adatfelderítési & besorolás a Azure SQL Database, az Azure SQL felügyelt példányához és az Azure szinapszis Analytics szolgáltatáshoz
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=1
titleSuffix: Azure SQL Database, Azure SQL Managed Instance, and Azure Synapse
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 02/17/2021
tags: azure-synapse
ms.openlocfilehash: f47303a953dbb06b4c9efdfbbae41715cad81d2a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101690804"
---
# <a name="data-discovery--classification"></a>Adatfelderítés és -besorolás
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Az adatfelderítés és -besorolás az Azure SQL Database, a felügyelt Azure SQL-példány és az Azure Synapse Analytics részét képezi. Alapvető képességeket biztosít az adatbázisokban található bizalmas adatok felderítésére, besorolására, címkézésére és jelentésére.

A legérzékenyebb adatok lehetnek például az üzleti, pénzügyi, egészségügyi vagy személyes adatok. Az adatok felfedezése és besorolása kulcsfontosságú szerepet játszik a szervezet Information Protection-megközelítésében. Infrastruktúraként alkalmas lehet az alábbiakra:

- Az adatvédelemre vonatkozó szabványok és a szabályozásoknak való megfelelőség követelményeinek való megfelelés elősegítése.
- Különböző biztonsági forgatókönyvek, például a figyelés (naplózás) bizalmas adatokhoz való hozzáférése.
- A szigorúan bizalmas adatokat tartalmazó adatbázisok biztonságának szabályozása és a hozzáférés megerősítése.

> [!NOTE]
> További információ a helyszíni SQL Serverról: SQL- [Adatfelderítés & besorolása](/sql/relational-databases/security/sql-data-discovery-and-classification).

## <a name="what-is-data-discovery--classification"></a><a id="what-is-dc"></a>Mi az az adatfelderítési & besorolás?

Az adatfelderítési & besorolása alapszintű szolgáltatásokat és új képességeket vezet be az Azure-ban. Új adatvédelmi paradigmát képezi a SQL Database, az SQL felügyelt példányának és az Azure szinapszisnak, amelynek célja az adatok védelme, és nem csak az adatbázis. A paradigma a következőket foglalja magában:

- **Felderítés és javaslatok:** A besorolási motor megvizsgálja az adatbázist, és azonosítja a potenciálisan bizalmas adatokat tartalmazó oszlopokat. Ezután egyszerűen áttekintheti és alkalmazhatja az ajánlott besorolást a Azure Portal használatával.

- **Címkézés:** Az adatbesorolási címkéket tartósan alkalmazhatja az oszlopokra a SQL Server adatbázis-kezelőhöz hozzáadott új metaadat-attribútumok használatával. Ezt a metaadatokat Ezután felhasználhatja az érzékenységen alapuló naplózási és védelmi forgatókönyvekhez.

- **Lekérdezési eredmény – beállított érzékenység:** A lekérdezési eredményhalmaz érzékenységét valós időben számítjuk ki naplózási célokra.

- **Láthatóság:** Az adatbázis-besorolási állapotot a Azure Portal részletes irányítópultján tekintheti meg. Emellett a jelentés Excel-formátumban is letölthető a megfelelőségi és naplózási célokra, valamint más igényekhez.

## <a name="discover-classify-and-label-sensitive-columns"></a><a id="discover-classify-columns"></a>Bizalmas oszlopok felderítése, besorolása és címkézése

Ez a szakasz a következő lépéseit ismerteti:

- Bizalmas adatokat tartalmazó oszlopok felfedése, osztályozása és címkézése az adatbázisban.
- Az adatbázis aktuális besorolási állapotának és a jelentések exportálásának megtekintése.

A besorolás két metaadat-attribútumot tartalmaz:

- **Címkék**: a fő besorolási attribútumok, amelyek az oszlopban tárolt adatmennyiség érzékenységi szintjének meghatározására szolgálnak.  
- **Adattípusok**: az oszlopban tárolt adatok típusával kapcsolatos részletesebb információkat biztosító attribútumok.

### <a name="define-and-customize-your-classification-taxonomy"></a>Besorolási besorolás meghatározása és testreszabása

Az adatfelderítési & besorolása tartalmaz egy beépített érzékenységi címkét és egy beépített adattípust és felderítési logikát. Mostantól testre szabhatja ezt a taxonómiát, és kifejezetten a környezetre szabott rangsorolási és besorolási konstrukciókat definiálhat.

A besorolási besorolást a teljes Azure-szervezet egyik központi helyén definiálhatja és testreszabhatja. Ez a hely [Azure Security Center](../../security-center/security-center-introduction.md), a biztonsági szabályzat részeként. Ezt a feladatot csak a szervezet legfelső szintű felügyeleti csoportjának rendszergazdai jogosultsággal rendelkező személye teheti meg.

Az Information Protection házirend-kezelésének részeként egyéni címkéket adhat meg, rangsorolhatja őket, és társíthatja őket egy kiválasztott adattípussal. Saját egyéni adattípusokat is hozzáadhat, és karakterlánc-mintázatokkal konfigurálhatja őket. A rendszer hozzáadja a mintákat a felderítési logikához az ilyen típusú adattípusok azonosításához az adatbázisokban.

További információ: [az SQL Information Protection-szabályzat testreszabása Azure Security Center (előzetes verzió)](../../security-center/security-center-info-protection-policy.md).

Az egész szervezetre kiterjedő házirend meghatározása után folytathatja az egyes adatbázisok besorolását a testreszabott házirend használatával.

### <a name="classify-your-database"></a>Az adatbázis besorolása

> [!NOTE]
> Az alábbi példa a Azure SQL Databaset használja, de ki kell választania a megfelelő terméket, amelynek az adatfelderítési & besorolását konfigurálni kívánja.

1. Nyissa meg az [Azure Portal](https://portal.azure.com).

1. Lépjen az **Adatfelderítési & besorolása** elemre a Azure SQL Database panel **biztonsági** fejléce alatt. Az Áttekintés lap az adatbázis aktuális besorolási állapotának összegzését tartalmazza. Az összefoglalás tartalmazza az összes besorolt oszlop részletes listáját, amelyeket szűrheti is, ha csak bizonyos sémákat, adattípusokat és címkéket szeretne megjeleníteni. Ha még nem sorolt be oszlopokat, [ugorjon a 4. lépésre](#step-4).

    ![Áttekintés](./media/data-discovery-and-classification-overview/data-discovery-and-classification.png)

1. Egy jelentés Excel-formátumban való letöltéséhez válassza az **Exportálás** lehetőséget a panel felső menüjében.

1. <a id="step-4"></a>Az adatbesorolás megkezdéséhez válassza a **besorolás** lapot az **adatfelderítési & besorolása** lapon.

    A besorolási motor megvizsgálja az adatbázist a potenciálisan bizalmas adatokat tartalmazó oszlopokra vonatkozóan, és felsorolja a javasolt oszlopok besorolásait.

1. Besorolási javaslatok megtekintése és alkalmazása:

   - A javasolt oszlop besorolások listájának megtekintéséhez kattintson a panel alján található javaslatok panelre.

   - Egy adott oszlopra vonatkozó javaslat elfogadásához jelölje be a megfelelő sor bal oldali oszlopában található jelölőnégyzetet. Az összes javaslat elfogadottként való megjelöléséhez jelölje be a bal szélső jelölőnégyzetet a javaslatok táblázat fejlécében.

   - A kiválasztott javaslatok alkalmazásához válassza a **kiválasztott javaslatok elfogadása** lehetőséget.

   ![Besorolási javaslatok](./media/data-discovery-and-classification-overview/recommendation.png)

1. Az oszlopokat manuálisan is osztályozhatja, Alternatív megoldásként vagy a javaslaton alapuló besorolás mellett:

   1. A panel felső menüjében válassza a **besorolás hozzáadása** elemet.

   1. A megnyíló környezet ablakban válassza ki a minősíteni kívánt sémát, táblázatot és oszlopot, valamint az információ típusát és az érzékenység címkéjét.

   1. A környezet ablakának alján válassza a **besorolás hozzáadása** elemet.

   ![Besorolás manuális hozzáadása](./media/data-discovery-and-classification-overview/manually-add-classification.png)


1. Az új besorolási metaadatokkal rendelkező adatbázis-oszlopok besorolásának és állandó címkézésének (címkézésének) befejezéséhez válassza a **Mentés** lehetőséget a **besorolás** lapon.

## <a name="audit-access-to-sensitive-data"></a><a id="audit-sensitive-data"></a>Bizalmas adatokhoz való hozzáférés naplózása

Az Information-Protection paradigma fontos aspektusa a bizalmas adatokhoz való hozzáférés figyelése. Az [Azure SQL audit](../../azure-sql/database/auditing-overview.md) továbbfejlesztett funkciója, hogy egy új mezőt tartalmazzon a naplóban `data_sensitivity_information` . Ez a mező a lekérdezés által visszaadott adatérzékeny besorolásokat (címkéket) naplózza. Bemutatunk egy példát:

![Az auditnaplóban](./media/data-discovery-and-classification-overview/11_data_classification_audit_log.png)

## <a name="permissions"></a><a id="permissions"></a>Engedélyek

Ezek a beépített szerepkörök az adatbázisok adatbesorolását tudják olvasni:

- Tulajdonos
- Olvasó
- Közreműködő
- SQL-biztonságkezelő
- Felhasználói hozzáférés rendszergazdája

Ezek a beépített szerepkörök módosíthatják egy adatbázis adatbesorolását:

- Tulajdonos
- Közreműködő
- SQL-biztonságkezelő

További információ az [Azure RBAC](../../role-based-access-control/overview.md)szerepköralapú engedélyeiről.

## <a name="manage-classifications"></a><a id="manage-classification"></a>Besorolások kezelése

A besorolások kezeléséhez a T-SQL, a REST API vagy a PowerShell használható.

### <a name="use-t-sql"></a>A T-SQL használata

A T-SQL használatával oszlop besorolásokat adhat hozzá vagy távolíthat el, valamint beolvashatja az összes besorolást a teljes adatbázisra vonatkozóan.

> [!NOTE]
> Ha T-SQL-T használ a címkék kezeléséhez, nem kell érvényesíteni, hogy egy oszlophoz hozzáadott címkék megtalálhatók-e a szervezet Information Protection-szabályzatában (a portál ajánlásaiban megjelenő címkék halmaza). Így Ön is ellenőrizheti ezt.

A T-SQL osztályozási szolgáltatással való használatáról a következő hivatkozásokban talál további információt:

- Egy vagy több oszlop besorolásának hozzáadása vagy frissítése: [érzékenységi besorolás hozzáadása](/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- A besorolás eltávolítása egy vagy több oszlopból: az [érzékenység besorolásának eldobása](/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- Az adatbázis összes besorolásának megtekintése: [sys.sensitivity_classifications](/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

### <a name="use-powershell-cmdlets"></a>PowerShell-parancsmagok használata
Az Azure SQL Database és az Azure SQL felügyelt példányaira vonatkozó besorolások és javaslatok kezelése a PowerShell használatával.

#### <a name="powershell-cmdlets-for-azure-sql-database"></a>PowerShell-parancsmagok a Azure SQL Databasehoz

- [Get-AzSqlDatabaseSensitivityClassification](/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [Set-AzSqlDatabaseSensitivityClassification](/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [Remove-AzSqlDatabaseSensitivityClassification](/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [Get-AzSqlDatabaseSensitivityRecommendation](/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [AzSqlDatabaSesensitivityRecommendation engedélyezése](/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [AzSqlDatabaseSensitivityRecommendation letiltása](/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

#### <a name="powershell-cmdlets-for-azure-sql-managed-instance"></a>PowerShell-parancsmagok az Azure SQL felügyelt példányaihoz

- [Get-AzSqlInstanceDatabaseSensitivityClassification](/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [Set-AzSqlInstanceDatabaseSensitivityClassification](/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [Remove-AzSqlInstanceDatabaseSensitivityClassification](/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [Get-AzSqlInstanceDatabaseSensitivityRecommendation](/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [AzSqlInstanceDatabaseSensitivityRecommendation engedélyezése](/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [AzSqlInstanceDatabaseSensitivityRecommendation letiltása](/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)

### <a name="use-the-rest-api"></a>A REST API használata

A REST API használatával programozott módon kezelheti a besorolásokat és a javaslatokat. A közzétett REST API a következő műveleteket támogatja:

- [Létrehozás vagy frissítés](/rest/api/sql/sensitivitylabels/createorupdate): a megadott oszlop érzékenységi címkéjét hozza létre vagy frissíti.
- [Delete (Törlés](/rest/api/sql/sensitivitylabels/delete)): törli a megadott oszlop érzékenységi címkéjét.
- [Javaslat letiltása](/rest/api/sql/sensitivitylabels/disablerecommendation): a megadott oszlop érzékenységi javaslatainak letiltása.
- [Javaslat engedélyezése](/rest/api/sql/sensitivitylabels/enablerecommendation): a megadott oszlopra vonatkozó érzékenységi javaslatokat tesz lehetővé. (A javaslatok alapértelmezés szerint engedélyezve vannak az összes oszlopban.)
- [Get](/rest/api/sql/sensitivitylabels/get): lekéri a megadott oszlop érzékenységi címkéjét.
- [Aktuális adatbázis listázása](/rest/api/sql/sensitivitylabels/listcurrentbydatabase): lekéri a megadott adatbázis aktuális érzékenységi címkéit.
- Az [adatbázis által ajánlott lista](/rest/api/sql/sensitivitylabels/listrecommendedbydatabase): lekéri a megadott adatbázis javasolt érzékenységi címkéit.


## <a name="faq---advanced-classification-capabilities"></a>Gyakori kérdések – speciális besorolási képességek

**Kérdés**: az [Azure hatáskörébe](../../purview/overview.md) kerül az sql-adatfelderítés & besorolása, vagy az sql-adatfelderítés & a besorolás hamarosan megszűnik?
**Válasz**: továbbra is támogatja az SQL-adatfelderítési & besorolását, és azt javasoljuk, hogy fogadja el az [Azure](../../purview/overview.md) -t, amely gazdagabb képességekkel rendelkezik a fejlett besorolási funkciók és az adatkezelés terén. Ha valamilyen szolgáltatás, funkció, API vagy termékváltozat kivezetése mellett döntünk, arról előzetes értesítést kap a migrálási vagy áttérési tervvel együtt. A Microsoft életciklus-szabályzatairól itt tájékozódhat bővebben.


## <a name="next-steps"></a><a id="next-steps"></a>Következő lépések

- Érdemes lehet az [Azure SQL auditot](../../azure-sql/database/auditing-overview.md) beállítani a minősített bizalmas adatokhoz való hozzáférés figyelésére és naplózására.
- Adatfelderítési & besorolást tartalmazó bemutató esetén tekintse meg a következőt: az [SQL-adatok védelmének észlelése, osztályozása, címkézése & | Az elérhető adatvédelem](https://www.youtube.com/watch?v=itVi9bkJUNc).
- Az Azure SQL-adatbázisok és az Azure-beli szinapszis-elemzések az Azure-beli, T-SQL-parancsokkal történő besorolásáról az Azure [SQL-alapú adatosztályozás Azure-beli hatáskörébe címkékkel](../../sql-database/scripts/sql-database-import-purview-labels.md)című témakörben olvashat