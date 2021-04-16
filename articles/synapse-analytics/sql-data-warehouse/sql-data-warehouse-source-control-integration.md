---
title: Verziókövetés integrálása
description: Nagyvállalati szintű Database DevOps-élmény dedikált SQL-készlethez, natív verzióvezérlési integrációval az Azure Repos (Git és GitHub) használatával.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql-dw
ms.date: 08/23/2019
ms.author: jrasnick
ms.reviewer: igorstan
ms.openlocfilehash: 53adbc0288c28c3b18632539c3f812bbca82da92
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566154"
---
# <a name="source-control-integration-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Forrásvezérlés integrálása dedikált SQL-készlethez a Azure Synapse Analytics

Ez az oktatóanyag bemutatja, hogyan integrálhatja a SQL Server Data Tools (SSDT) adatbázisprojektet a forrásvezérlővel.  A forrásvezérlés integrációja az első lépés a folyamatos integrációs és üzembe helyezési folyamat és a dedikált SQL-készlet erőforrással való Azure Synapse Analytics.

## <a name="before-you-begin"></a>Előkészületek

- Regisztráció Azure [DevOps-szervezetre](https://azure.microsoft.com/services/devops/)
- Lépjen végig a Létrehozás és [csatlakozás oktatóanyagon](create-data-warehouse-portal.md)
- [A 2019 Visual Studio telepítése](https://visualstudio.microsoft.com/vs/older-downloads/)

## <a name="set-up-and-connect-to-azure-devops"></a>Az Azure DevOps beállítása és csatlakoztatása

1. Az Azure DevOps-szervezetben hozzon létre egy projektet, amely az SSDT-adatbázisprojektet egy Azure-adattáron keresztül fogja irányítani.

   ![Projekt létrehozása](./media/sql-data-warehouse-source-control-integration/1-create-project-azure-devops.png "Projekt létrehozása")

2. Nyissa Visual Studio, és csatlakozzon az Azure DevOps-szervezethez és -projekthez az első lépésben a **Kapcsolat kezelése lehetőség kiválasztásával.**

   ![Kapcsolatok kezelése](./media/sql-data-warehouse-source-control-integration/2-manage-connections.png "Kapcsolatok kezelése")

3. Csatlakozzon a projekthez a **Kapcsolatok kezelése,** majd **a Csatlakozás projekthez lehetőség kiválasztásával.**
 
    ![Csatlakozás1](./media/sql-data-warehouse-source-control-integration/3-connect-project.png "Kapcsolódás")


4. Keresse meg az első lépésben létrehozott projektet, és válassza a Csatlakozás **lehetőséget.**
 
    ![Csatlakozás2](./media/sql-data-warehouse-source-control-integration/3.5-connect.png "Kapcsolódás")


3. Klónozza az Azure DevOps-adattárat a projektből a helyi gépre.

   ![Clone repo (Az objektum klónozása)](./media/sql-data-warehouse-source-control-integration/4-clone-repo.png "Clone repo (Az objektum klónozása)")

További információ a projektek Visual Studio való csatlakoztatásával kapcsolatban: Csatlakozás projektekhez [a Team Explorer.](/visualstudio/ide/connect-team-project?view=vs-2019&preserve-view=true) Ha útmutatásra van szüksége egy adattár klónozásával Visual Studio, olvassa el a következő cikket: [Clone an exiting Git repo](/azure/devops/repos/git/clone?tabs=visual-studio) (Kilépési Git-adattár klónozása). 

## <a name="create-and-connect-your-project"></a>A projekt létrehozása és csatlakoztatása

1. A Visual Studio hozzon létre egy új SQL Server Database Projectet, amely egy könyvtárat és egy helyi Git-adattárat is a helyi **klónozott adattárban tárolja.**

   ![Új projekt létrehozása](./media/sql-data-warehouse-source-control-integration/5-create-new-project.png "Új projekt létrehozása")  

2. Kattintson a jobb gombbal az üres sqlprojektre, és importálja az adattárházat az adatbázis-projektbe.

   ![Projekt importálása](./media/sql-data-warehouse-source-control-integration/6-import-new-project.png "Projekt importálása")  

3. A Team Explorer a Visual Studio véglegesít a módosításokat a helyi Git-adattárban.

   ![Véglegesítés](./media/sql-data-warehouse-source-control-integration/6.5-commit-push-changes.png "Véglegesítés")  

4. Most, hogy helyben végrehajtotta a módosításokat a klónozott adattárban, szinkronizálja és lekultálja a módosításokat az Azure DevOps-projektBen az Azure-adattárba.

   ![Szinkronizálás és leküldés – előkészítés](./media/sql-data-warehouse-source-control-integration/7-commit-push-changes.png "Szinkronizálás és leküldés – előkészítés")

   ![Szinkronizálás és leküldés](./media/sql-data-warehouse-source-control-integration/7.5-commit-push-changes.png "Szinkronizálás és leküldés")  

## <a name="validation"></a>Érvényesítés

1. A módosítások Azure-adattárba való lekért ellenőrzéséhez frissítsen egy táblaoszlopot az adatbázis-projektben a Visual Studio SQL Server Data Tools (SSDT) használatával.

   ![A frissítési oszlop ellenőrzése](./media/sql-data-warehouse-source-control-integration/8-validation-update-column.png "A frissítési oszlop ellenőrzése")

2. Véglegesítés és a módosítás leküldése a helyi adattárból az Azure-adattárba.

   ![Módosítások leküldése](./media/sql-data-warehouse-source-control-integration/9-push-column-change.png "Módosítások leküldése")

3. Ellenőrizze, hogy a módosítás le lett-e kkézsedve az Azure-adattárban.

   ![Ellenőrzés](./media/sql-data-warehouse-source-control-integration/10-verify-column-change-pushed.png "Módosítások ellenőrzése")

4. (**Nem kötelező**) A Séma összehasonlítása segítségével frissítse a cél dedikált SQL-készlet módosításait az SSDT használatával, hogy az Azure-adattárban és a helyi adattárban található objektumdefiníciók a dedikált SQL-készletet tükrözzék.

## <a name="next-steps"></a>Következő lépések

- [Fejlesztés dedikált SQL-készlethez](sql-data-warehouse-overview-develop.md)