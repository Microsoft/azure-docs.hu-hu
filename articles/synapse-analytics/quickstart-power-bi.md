---
title: 'Gyors útmutató: Power BI munkaterület összekapcsolása egy szinapszis-munkaterülettel'
description: Power BI munkaterület összekapcsolása egy Azure szinapszis Analytics-munkaterülettel az útmutató lépéseinek követésével.
services: synapse-analytics
author: jocaplan
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: business-intelligence
ms.date: 10/27/2020
ms.author: jocaplan
ms.reviewer: jrasnick
ms.openlocfilehash: 9c63e5e24495f373f288d2789780a6c671a7cc24
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98218399"
---
# <a name="quickstart-linking-a-power-bi-workspace-to-a-synapse-workspace"></a>Gyors útmutató: Power BI munkaterület összekapcsolása egy szinapszis-munkaterülettel

Ebből a rövid útmutatóból megtudhatja, hogyan csatlakoztatható egy Power BI munkaterület egy Azure szinapszis Analytics-munkaterülethez új Power BI jelentések és adatkészletek létrehozásához a szinapszis studióból.

Ha nem rendelkezik Azure-előfizetéssel, [a Kezdés előtt hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Előfeltételek

- [Azure-beli szinapszis-munkaterület és társított Storage-fiók létrehozása](quickstart-create-workspace.md)
- [Power BI Professional vagy Premium munkaterületen](/power-bi/service-create-the-new-workspaces)

## <a name="link-power-bi-workspace-to-your-synapse-workspace"></a>Power BI munkaterület összekapcsolása a szinapszis munkaterülettel

1. A szinapszis Studiótól kezdve kattintson a **kezelés** elemre.

    ![A szinapszis Studio kattintson a kezelés elemre.](media/quickstart-link-powerbi/synapse-studio-click-manage.png)

2. A **külső kapcsolatok** területen kattintson a **társított szolgáltatások** elemre.

    ![Társított szolgáltatások kiemelve.](media/quickstart-link-powerbi/manage-click-linked-services.png)

3. Kattintson az **+ Új** elemre.

    ![+ Az új társított szolgáltatások ki vannak emelve.](media/quickstart-link-powerbi/new-highlighted.png)

4. Kattintson a **Power bi** elemre, majd a **Folytatás** gombra.

    ![Power BI társított szolgáltatás megjelenítése.](media/quickstart-link-powerbi/powerbi-linked-service.png)

5. Adja meg a társított szolgáltatás nevét, majd válasszon ki egy munkaterületet a legördülő listából.

    ![Power BI társított szolgáltatás telepítésének megjelenítése.](media/quickstart-link-powerbi/workspace-link-dialog.png)

6. Kattintson a **Létrehozás** lehetőségre.

## <a name="view-power-bi-workspace-in-synapse-studio"></a>Power BI munkaterület megtekintése a szinapszis Studióban

A munkaterületek összekapcsolása után böngészhet a Power BI adatkészletekben, szerkesztheti és létrehozhatja az új Power BI jelentéseket a szinapszis studióból.

1. Kattintson a **fejlesztés** gombra.

    ![A szinapszis Studióban kattintson a fejlesztés elemre.](media/quickstart-link-powerbi/synapse-studio-click-develop.png)

2. Bontsa ki a Power BI és a használni kívánt munkaterületet.

    ![Bontsa ki a Power BI és a munkaterületet.](media/quickstart-link-powerbi/develop-expand-powerbi.png)

Új jelentések hozhatók létre a **+** **fejlesztés** lap tetején. a meglévő jelentések a jelentés nevére kattintva szerkeszthetők. A rendszer a mentett módosításokat a Power BI munkaterületre írja vissza.

![Power BI jelentés megtekintése és szerkesztése.](media/quickstart-link-powerbi/powerbi-report.png)


## <a name="next-steps"></a>Következő lépések

További információ az [Azure Storage-ban tárolt fájlokról Power bi jelentés létrehozásáról](sql/tutorial-connect-power-bi-desktop.md).