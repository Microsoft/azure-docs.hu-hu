---
title: Verziókövetés integrálása
description: Nagyvállalati szintű adatbázis-DevOps élmény a dedikált SQL-készlethez az Azure Repos (git és GitHub) használatával történő natív verziókövetés-integrációval.
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql-dw
ms.date: 08/23/2019
ms.author: gaursa
ms.reviewer: igorstan
ms.openlocfilehash: 0864ab90f35e4af0443b418bc139ee5f8a7db665
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104585536"
---
# <a name="source-control-integration-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Verziókövetés integrálása dedikált SQL-készlethez az Azure szinapszis Analyticsben

Ez az oktatóanyag azt ismerteti, hogyan integrálható a SQL Server Data Tools (SSDT) adatbázis-projekt a verziókövetés használatával.  A verziókövetés integrációja az első lépés a folyamatos integrációs és üzembe helyezési folyamat létrehozásához az Azure szinapszis Analytics dedikált SQL Pool-erőforrásával.

## <a name="before-you-begin"></a>Előkészületek

- Regisztráció [Azure DevOps-szervezetre](https://azure.microsoft.com/services/devops/)
- A [Létrehozás és összekapcsolás](create-data-warehouse-portal.md) oktatóanyag átugrása
- [A Visual Studio 2019 telepítése](https://visualstudio.microsoft.com/vs/older-downloads/)

## <a name="set-up-and-connect-to-azure-devops"></a>Az Azure DevOps beállítása és kapcsolódás

1. Az Azure DevOps-szervezetben hozzon létre egy projektet, amely a SSDT adatbázis-projektet egy Azure-tárházon keresztül fogja üzemeltetni.

   ![Projekt létrehozása](./media/sql-data-warehouse-source-control-integration/1-create-project-azure-devops.png "Projekt létrehozása")

2. Nyissa meg a Visual studiót, és kapcsolódjon az Azure DevOps-szervezethez és-projekthez az első lépésben a **kapcsolat kezelése** lehetőség kiválasztásával

   ![Kapcsolatok kezelése](./media/sql-data-warehouse-source-control-integration/2-manage-connections.png "Kapcsolatok kezelése")

3. Kapcsolódjon a projekthez a **kapcsolatok kezelése**, majd **a projekthez való csatlakozás** lehetőség kiválasztásával.
 
    ![Connect1](./media/sql-data-warehouse-source-control-integration/3-connect-project.png "Kapcsolódás")


4. Keresse meg az első lépésben létrehozott projektet, majd válassza a **kapcsolat** lehetőséget.
 
    ![Connect2](./media/sql-data-warehouse-source-control-integration/3.5-connect.png "Kapcsolódás")


3. Az Azure DevOps-tárház klónozása a projektből a helyi gépre.

   ![Klónozási tárház](./media/sql-data-warehouse-source-control-integration/4-clone-repo.png "Klónozási tárház")

A projektek Visual Studióval való összekapcsolásával kapcsolatos további információkért tekintse meg a Kapcsolódás a következőhöz: [Team Explorer](/visualstudio/ide/connect-team-project?view=vs-2019&preserve-view=true). A tárház Visual Studióval történő klónozásával kapcsolatos útmutatásért tekintse át a klónozott a git-tárház [kilépését](/azure/devops/repos/git/clone?tabs=visual-studio) ismertető cikket. 

## <a name="create-and-connect-your-project"></a>Projekt létrehozása és összekapcsolása

1. A Visual Studióban hozzon létre egy új SQL Server adatbázis-projektet a **helyi klónozott tárház** címtár-és helyi git-tárházával.

   ![Új projekt létrehozása](./media/sql-data-warehouse-source-control-integration/5-create-new-project.png "Új projekt létrehozása")  

2. Kattintson a jobb gombbal az üres sqlproject, és importálja az adattárházat az adatbázis-projektbe.

   ![Projekt importálása](./media/sql-data-warehouse-source-control-integration/6-import-new-project.png "Projekt importálása")  

3. A Visual Studióban Team Explorer véglegesítse a módosításokat a helyi git-tárházban.

   ![Véglegesítés](./media/sql-data-warehouse-source-control-integration/6.5-commit-push-changes.png "Véglegesítés")  

4. Most, hogy már elvégezte a módosításokat a klónozott tárházban helyileg, szinkronizálja és leküldheti a módosításokat az Azure-beli DevOps-projektben.

   ![Szinkronizálás és leküldéses előkészítés](./media/sql-data-warehouse-source-control-integration/7-commit-push-changes.png "Szinkronizálás és leküldéses előkészítés")

   ![Szinkronizálás és leküldés](./media/sql-data-warehouse-source-control-integration/7.5-commit-push-changes.png "Szinkronizálás és leküldés")  

## <a name="validation"></a>Érvényesítés

1. Ellenőrizze, hogy az Azure-tárházban történtek-e a módosítások, ha a Visual Studio SQL Server Data Tools (SSDT) segítségével frissíti az adatbázis-projektben lévő Table (tábla) oszlopot.

   ![Frissítési oszlop érvényesítése](./media/sql-data-warehouse-source-control-integration/8-validation-update-column.png "Frissítési oszlop érvényesítése")

2. Véglegesítse és küldje el a változást a helyi adattárból az Azure-tárházba.

   ![Módosítások leküldése](./media/sql-data-warehouse-source-control-integration/9-push-column-change.png "Módosítások leküldése")

3. Ellenőrizze, hogy a változást leküldte-e az Azure-tárház adattárában.

   ![Ellenőrzés](./media/sql-data-warehouse-source-control-integration/10-verify-column-change-pushed.png "Módosítások ellenőrzése")

4. (Nem **kötelező**) Használja a séma összevetését, és frissítse a cél dedikált SQL-készlet módosításait a SSDT használatával, hogy az Azure-tárházban és a helyi tárházban lévő Object-definíciók tükrözzék a dedikált SQL-készletet.

## <a name="next-steps"></a>Következő lépések

- [Fejlesztés dedikált SQL-készlethez](sql-data-warehouse-overview-develop.md)