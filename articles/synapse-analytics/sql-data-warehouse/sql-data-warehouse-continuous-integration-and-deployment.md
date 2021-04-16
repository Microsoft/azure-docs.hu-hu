---
title: Folyamatos integráció és üzembe helyezés dedikált SQL-készlethez
description: Nagyvállalati szintű Database DevOps-élmény dedikált SQL-készlethez a Azure Synapse Analytics beépített támogatással a folyamatos integrációhoz és üzembe helyezéshez az Azure Pipelines használatával.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 95bf3a8c614b8b7c0269257cb62b3c3a0f60be13
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568284"
---
# <a name="continuous-integration-and-deployment-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Folyamatos integráció és üzembe helyezés dedikált SQL-készlethez a Azure Synapse Analytics

Ez az egyszerű oktatóanyag bemutatja, hogyan integrálhatja az SQL Server Data Tools- (SSDT-) adatbázisprojektet az Azure DevOpsba, és hogyan használhatja az Azure Pipelinest a folyamatos integráció és üzembe helyezés beállításához. Ez az oktatóanyag az adatraktározás folyamatos integrációs és üzembe helyezési folyamatának második lépése.

## <a name="before-you-begin"></a>Előkészületek

- Végig kell mennie a [forrásvezérlés integrációjának oktatóanyagán](sql-data-warehouse-source-control-integration.md)

- Az Azure DevOps beállítása és csatlakoztatása

## <a name="continuous-integration-with-visual-studio-build"></a>Folyamatos integráció a Visual Studio buildbe

1. Lépjen az Azure Pipelinesba, és hozzon létre egy új build folyamatot.

      ![Új folyamat](./media/sql-data-warehouse-continuous-integration-and-deployment/1-new-build-pipeline.png "Új folyamat")

2. Válassza ki a forráskód adattárát (Azure Repos Git), majd válassza a .NET Desktop alkalmazássablont.

      ![Folyamat beállítása](./media/sql-data-warehouse-continuous-integration-and-deployment/2-pipeline-setup.png "Folyamat beállítása")

3. Szerkessze a YAML-fájlt az ügynök megfelelő készletének használatára. A YAML-fájlnak ehhez hasonlónak kell lennie:

      ![YAML](./media/sql-data-warehouse-continuous-integration-and-deployment/3-yaml-file.png "YAML")

Ezen a ponton már van egy egyszerű környezete, amelyben a forrásvezérlő tárház főágába való bejelentkezés automatikusan kiváltja az adatbázisprojekt sikeres Visual Studio buildjét. Ellenőrizze, hogy az automatizálás teljes munkavégi-e, ha módosítja a helyi adatbázisprojektet, és bejelentkezik a főágba.

## <a name="continuous-deployment-with-the-azure-synapse-analytics-or-database-deployment-task"></a>Folyamatos üzembe helyezés az Azure Synapse Analytics (vagy adatbázis) üzembe helyezési feladattal

1. Adjon hozzá egy új feladatot a Azure SQL Database [üzembe](/azure/devops/pipelines/targets/azure-sqldb) helyezési feladattal, és töltse ki a céladattárházhoz való csatlakozáshoz szükséges mezőket. A feladat futtatásakor a rendszer az előző buildfolyamat során létrehozott DACPAC-et telepíti a céladattárházba. Használhatja a központi [telepítési Azure Synapse Analytics is.](https://marketplace.visualstudio.com/items?itemName=ms-sql-dw.SQLDWDeployment)

      ![Üzembe helyezési feladat](./media/sql-data-warehouse-continuous-integration-and-deployment/4-deployment-task.png "Üzembe helyezési feladat")

2. Ha saját maga által üzemeltetett ügynököt használ, ügyeljen arra, hogy a környezeti változót a megfelelő SqlPackage.exe használja Azure Synapse Analytics. Az elérési útnak a következőhez hasonlónak kell lennie:

      ![Környezeti változó](./media/sql-data-warehouse-continuous-integration-and-deployment/5-environment-variable-preview.png "Környezeti változó")

   C:\Program Files (x86)\Microsoft Visual Studio\2019\Preview\Common7\IDE\Extensions\Microsoft\SQLDB\DAC\150  

   Futtassa és ellenőrizze a folyamatot. Helyileg is módosíthat, és ellenőrizheti a forrásvezérlő módosításait, amelyek automatikus buildet és üzembe helyezést hoznak létre.

## <a name="next-steps"></a>Következő lépések

- A [dedikált SQL-készlet (korábban SQL DW) architektúrája](massively-parallel-processing-mpp-architecture.md)
- Dedikált [SQL-készlet (korábban SQL DW) gyors létrehozása](create-data-warehouse-portal.md)
- [Mintaadatok betöltése](./load-data-from-azure-blob-storage-using-copy.md)
- Ismerkedés a [videókkal](sql-data-warehouse-videos.md)