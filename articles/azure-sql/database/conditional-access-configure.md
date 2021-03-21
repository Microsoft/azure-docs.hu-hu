---
title: Feltételes hozzáférés
description: Megtudhatja, hogyan konfigurálhatja a feltételes hozzáférést Azure SQL Database, az Azure SQL felügyelt példányához és az Azure szinapszis Analytics szolgáltatáshoz.
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.custom: sqldbrb=1
ms.date: 04/28/2020
tag: azure-synpase
ms.openlocfilehash: c18d235977f1256a10e813fa8e02aa3590366fe1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97936413"
---
# <a name="conditional-access-with-azure-sql-database-and-azure-synapse-analytics"></a>Feltételes hozzáférés a Azure SQL Database és az Azure szinapszis Analytics használatával

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

[Azure SQL Database](sql-database-paas-overview.md), az [Azure SQL felügyelt példánya](../managed-instance/sql-managed-instance-paas-overview.md)és az [Azure szinapszis Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) támogatja a Microsoft feltételes hozzáférését.

A következő lépések bemutatják, hogyan konfigurálhatja az Azure SQL Database, az SQL felügyelt példányát vagy az Azure Szinapszisot egy feltételes hozzáférési szabályzat érvénybe léptetéséhez.  

## <a name="prerequisites"></a>Előfeltételek

- A Azure Active Directory (Azure AD) hitelesítés támogatásához konfigurálnia kell Azure SQL Database, Azure SQL felügyelt példányt vagy dedikált SQL-készletet az Azure Szinapszisban. Konkrét lépések: [Azure Active Directory hitelesítés konfigurálása és kezelése SQL Database vagy Azure szinapszis segítségével](authentication-aad-configure.md).  
- Ha a Multi-Factor Authentication engedélyezve van, egy támogatott eszközzel kell kapcsolódnia, például a legújabb SQL Server Management Studio (SSMS). További információ: [Azure SQL Database multi-Factor Authentication konfigurálása SQL Server Management studiohoz](authentication-mfa-ssms-configure.md).  

## <a name="configure-conditional-access"></a>Feltételes hozzáférés konfigurálása

> [!NOTE]
> Az alábbi példa Azure SQL Database használ, de ki kell választania a megfelelő terméket, amelyhez feltételes hozzáférést kíván konfigurálni.

1. Jelentkezzen be a Azure Portalba, válassza a **Azure Active Directory** lehetőséget, majd válassza a **feltételes hozzáférés** lehetőséget. További információ: [Azure Active Directory feltételes hozzáférés technikai útmutatója](../../active-directory/conditional-access/concept-conditional-access-conditions.md).  
   ![Feltételes hozzáférés panel](./media/conditional-access-configure/conditional-access-blade.png)

2. A **feltételes hozzáférés – szabályzatok** panelen kattintson az **új házirend** elemre, adjon meg egy nevet, majd kattintson a **szabályok konfigurálása** elemre.  
3. A **hozzárendelések** területen válassza a **felhasználók és csoportok**, majd a **felhasználók és csoportok kiválasztása** lehetőséget, majd a feltételes hozzáféréshez válassza ki a felhasználót vagy a csoportot. Kattintson a **kiválasztás elemre, majd** kattintson a **kész** gombra a kijelölés elfogadásához.  
   ![felhasználók és csoportok kiválasztása](./media/conditional-access-configure/select-users-and-groups.png)  

4. Válassza a **Cloud apps** lehetőséget, majd kattintson az **alkalmazások kiválasztása** lehetőségre. A feltételes hozzáféréshez elérhető összes alkalmazás megjelenik. Válassza a **Azure SQL Database** lehetőséget, alul kattintson a **kiválasztás** elemre, majd kattintson a **kész** gombra.  
   ![SQL Database kiválasztása](./media/conditional-access-configure/select-sql-database.png)  
   Ha nem találja az alábbi harmadik képernyőképen felsorolt **Azure SQL Database** , hajtsa végre a következő lépéseket:
   - Kapcsolódjon Azure SQL Database-adatbázishoz egy Azure AD-beli rendszergazdai fiókkal a SSMS használatával.  
   - Végrehajtás `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER` .  
   - Jelentkezzen be az Azure AD-be, és ellenőrizze, hogy a Azure SQL Database, az SQL felügyelt példánya vagy az Azure szinapszis szerepel-e az Azure AD-példányban található alkalmazásokban.  

5. Válassza a **hozzáférés-vezérlés** lehetőséget, válassza a **támogatás** lehetőséget, majd jelölje ki az alkalmazni kívánt szabályzatot. Ebben a példában a **többtényezős hitelesítés megkövetelése** lehetőséget választjuk.  
   ![Válassza a hozzáférés engedélyezése lehetőséget](./media/conditional-access-configure/grant-access.png)  

## <a name="summary"></a>Összefoglalás

A kiválasztott alkalmazás (Azure SQL Database) prémium szintű Azure AD használatával mostantól kikényszeríti a kiválasztott feltételes hozzáférési szabályzatot, a **szükséges multi-Factor Authentication hitelesítést.**

A többtényezős hitelesítéssel kapcsolatos Azure SQL Database és az Azure szinapszis kérdéseivel kapcsolatban forduljon a következőhöz: <MFAforSQLDB@microsoft.com> .  

## <a name="next-steps"></a>Következő lépések  

Oktatóanyagért lásd: [az adatbázis biztonságossá tétele SQL Databaseban](secure-database-tutorial.md).