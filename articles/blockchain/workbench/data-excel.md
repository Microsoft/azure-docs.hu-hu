---
title: Azure Blockchain Workbench-adatok használata a Microsoft Excelben
description: Megtudhatja, hogy hogyan töltheti be és tekintheti meg az Azure Blockchain Workbench SQL-adatbázisának adatait a Microsoft Excelben.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/09/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 215d8b8fbc49e9f38dc89655981edce37984163a
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65510671"
---
# <a name="view-azure-blockchain-workbench-data-with-microsoft-excel"></a>Azure Blockchain Workbench-adatok megtekintése a Microsoft Excelben

Megtekintheti a Microsoft Excel alkalmazással az Azure Blockchain Workbench SQL-adatbázisának adatait. Ebben a cikkben az alábbi feladatok lépéseit találhatja meg:

* Csatlakozás Blockchain Workbench-adatbázishoz a Microsoft Excelből
* Blockchain Workbench-adatbázistáblák és -nézetek megtekintése
* Blockchain Workbench-nézetek adatainak betöltése az Excelbe

## <a name="connect-to-the-blockchain-workbench-database"></a>Csatlakozás a Blockchain Workbench adatbázisához

A Blockchain Workbench adatbázisához való csatlakozáshoz:

1. Nyissa meg a Microsoft Excelt.
2. Válassza az **Adatok** lapon az **Adatok lekérése** gombot.
3. Válassza az **Azure-ból**, majd az **Azure SQL Database-ből** lehetőséget.

   ![Kapcsolódás az Azure SQL Database szolgáltatáshoz](./media/data-excel/connect-sql-db.png)

4. Az **SQL Server-adatbázis** párbeszédpanelen:

    * A **Kiszolgáló** mezőben adja meg a Blockchain Workbench-kiszolgáló nevét.
    * Az **Adatbázis (nem kötelező)** mezőben adja meg az adatbázis nevét.

   ![Az adatbázis-kiszolgáló és az adatbázisnév megadása](./media/data-excel/provide-server-db.png)

5. Az **SQL Server-adatbázis** párbeszédpanel navigációs sávján válassza az **Adatbázis** lehetőséget. Írja be a felhasználónevét és a jelszavát a **Felhasználónév** és a **Jelszó** mezőbe, majd válassza a **Csatlakozás** gombot.

    > [!NOTE]
    > Ha az Azure Blockchain Workbench telepítési folyamata során létrehozott hitelesítő adatokat használja, akkor a **Felhasználónév** mezőben a `dbadmin` értéket kell megadnia. A **Jelszó** mezőben a Blockchain Workbench telepítésekor létrehozott jelszót kell megadnia.
    
   ![Az adatbázis eléréséhez szükséges hitelesítő adatok megadása](./media/data-excel/provide-credentials.png)

## <a name="look-at-database-tables-and-views"></a>Adatbázistáblák és -nézetek megtekintése

Az adatbázishoz való csatlakozás után megjelenik az Excel Kezelő párbeszédpanele. A Kezelő párbeszédpanelen tallózhat az adatbázisban lévő táblák és nézetek között. A nézetek jelentéskészítéshez lettek tervezve, és az összes nézet neve **vw** előtaggal kezdődik.

   ![Az Excel Kezelő párbeszédpanele egy nézet mintaképével](./media/data-excel/excel-navigator.png)

## <a name="load-view-data-into-an-excel-workbook"></a>Nézetadatok betöltése egy Excel-munkafüzetbe

A következő példa bemutatja, hogy hogyan tölthet be adatokat egy nézetből egy Excel-munkafüzetbe.

1. A **Kezelő** párbeszédpanelen válassza a **vwContractAction** nézetet. A **vwContractAction** előnézet megjeleníti a Blockchain Workbench-adatbázisban lévő szerződések egyikének összes műveletét.
2. Válassza a **Betöltés** lehetőséget a nézet összes adatának beolvasásához és Excel-munkafüzetbe helyezéséhez.

   ![Nézetből betöltött adatok](./media/data-excel/view-data.png)

Most, hogy betöltötte az adatokat, az Excel funkcióival létrehozhatja a kívánt jelentéseket az Azure Blockchain Workbench-adatbázis metaadatainak és tranzakciós adatainak felhasználásával.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Adatbázisnézetek az Azure Blockchain Workbenchben](database-views.md)