---
title: 'Gyors útmutató: Azure-Adatkezelő összekötése egy Azure-beli szinapszis Analytics-munkaterülettel'
description: Azure Adatkezelő-fürt összekötése egy Azure szinapszis Analytics-munkaterülettel az Azure szinapszis Analytics Apache Spark használatával.
services: synapse-analytics
author: manojraheja
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: workspace
ms.date: 10/07/2020
ms.author: maraheja
ms.reviewer: jrasnick
ms.openlocfilehash: ee9d137973bfa4eeb28bc6526437e76e781f3199
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92172277"
---
# <a name="connect-to-azure-data-explorer-using-apache-spark-for-azure-synapse-analytics"></a>Csatlakozás az Azure Data Explorerhez az Azure Synapse Analyticshez készült Apache Spark használatával

Ez a cikk azt ismerteti, hogyan lehet hozzáférni egy Azure Adatkezelő-adatbázishoz a szinapszis studióból az Azure szinapszis Analytics Apache Spark használatával.

## <a name="prerequisites"></a>Előfeltételek

* [Hozzon létre egy Azure adatkezelő-fürtöt és-adatbázist](/azure/data-explorer/create-cluster-database-portal).
* Rendelkezzen egy meglévő Azure szinapszis Analytics-munkaterülettel, vagy hozzon létre egy új munkaterületet a gyors útmutató [: Azure szinapszis-munkaterület létrehozása](./quickstart-create-workspace.md)című témakör lépéseit követve.
* Rendelkezzen meglévő Apache Spark készlettel, vagy hozzon létre egy új készletet a gyors útmutató [: Apache Spark-készlet létrehozása a Azure Portal használatával](./quickstart-create-apache-spark-pool-portal.md).
* [Hozzon létre egy Azure Active Directory (Azure ad) alkalmazást egy Azure ad-alkalmazás üzembe](/azure/data-explorer/kusto/management/access-control/how-to-provision-aad-app)helyezésével.
* Adja meg az Azure AD-alkalmazás hozzáférését az adatbázishoz az [azure adatkezelő Database-engedélyek kezelése](/azure/data-explorer/manage-database-permissions)című témakör lépéseit követve.

## <a name="go-to-synapse-studio"></a>Ugrás a szinapszis Studióra

Egy Azure szinapszis-munkaterületről válassza a **szinapszis Studio elindítása** lehetőséget. A szinapszis Studio kezdőlapján **válassza az** **adatObject Explorer**.

## <a name="connect-an-azure-data-explorer-database-to-an-azure-synapse-workspace"></a>Azure Adatkezelő-adatbázis összekötése egy Azure-beli szinapszis-munkaterülettel

Az Azure Adatkezelő-adatbázisok munkaterülethez való csatlakoztatása egy társított szolgáltatáson keresztül történik. Az Azure Adatkezelő társított szolgáltatásokkal böngészhet és megtekintheti az adatApache Spark az Azure szinapszis számára, és megkeresheti és megkeresheti az adatforrásokat. Az integrációs feladatokat egy folyamaton keresztül is futtathatja.

Az adatok Object Explorer egy Azure Adatkezelő-fürt közvetlen összekapcsolásához kövesse az alábbi lépéseket:

1. Válassza ki az **+** ikont a közelében. 
1. A külső adatforrásokhoz való kapcsolódáshoz válassza a **Kapcsolódás** lehetőséget.
1. Válassza az **Azure adatkezelő (Kusto)** lehetőséget.
1. Válassza a **Folytatás** lehetőséget.
1. A társított szolgáltatás neveként adjon meg egy rövid nevet. A név megjelenik az adatObject Explorerban, és az Azure szinapszis-futtatókörnyezetek használják az adatbázishoz való kapcsolódásra.
1. Válassza ki az Azure Adatkezelő-fürtöt az előfizetésből, vagy adja meg az URI-t.
1. Adja meg az **egyszerű szolgáltatás azonosítóját** és a **szolgáltatásnév kulcsát**. Győződjön meg arról, hogy ez a szolgáltatásnév az olvasási művelethez és a betöltéshez való hozzáféréshez tekint hozzáférést az adatbázishoz az adatfeldolgozáshoz.
1. Adja meg az Azure Adatkezelő-adatbázis nevét.
1. Válassza a **Kapcsolódás tesztelése** lehetőséget a megfelelő engedélyek biztosításához.
1. Válassza a **Létrehozás** lehetőséget.

    ![Képernyőkép, amely egy új társított szolgáltatást jelenít meg.](./media/quickstart-connect-azure-data-explorer/003-new-linked-service.png)

    > [!NOTE]
    > Választható A **kapcsolat tesztelése** nem ellenőrzi az írási hozzáférést. Győződjön meg arról, hogy az egyszerű szolgáltatásnév AZONOSÍTÓjának írási hozzáférése van az Azure Adatkezelő adatbázisához.

1. Az Azure Adatkezelő-fürtök és-adatbázisok az **azure adatkezelő** szakaszban található **csatolt** lapon jelennek meg.

    ![A fürtök böngészését bemutató képernyőkép.](./media/quickstart-connect-azure-data-explorer/004-browse-clusters.png)

    > [!NOTE]
    > Az aktuális kiadásban az adatbázis-objektumok az Azure AD-fiók engedélyei alapján vannak feltöltve az Azure Adatkezelő-adatbázisokban. A Apache Spark notebookok vagy integrációs feladatok futtatásakor a rendszer a kapcsolati szolgáltatás hitelesítő adatait fogja használni (például egyszerű szolgáltatásnév).

## <a name="quickly-interact-with-code-generated-actions"></a>Gyors interakció a kód által generált műveletekkel

Ha a jobb gombbal egy adatbázisra vagy táblára kattint, megjelenik a minta Spark-jegyzetfüzetek listája. Válassza ki az Azure Adatkezelőba való beolvasásához, írásához vagy továbbításához szükséges lehetőséget.

[![Képernyőkép, amely új minta-jegyzetfüzeteket jelenít meg.](./media/quickstart-connect-azure-data-explorer/005-new-notebook.png)](./media/quickstart-connect-azure-data-explorer/005-new-notebook.png#lightbox)

Íme egy példa az adatolvasásra. Csatlakoztassa a notebookot a Spark-készlethez, és futtassa a cellát.

[![Képernyőkép, amely egy új olvasási jegyzetfüzetet jelenít meg.](./media/quickstart-connect-azure-data-explorer/006-read-data.png)](./media/quickstart-connect-azure-data-explorer/006-read-data.png#lightbox)

   > [!NOTE]
   > Az első végrehajtás több mint három percet is igénybe vehet a Spark-munkamenet elindításához. A későbbi végrehajtások jelentősen gyorsabbak lesznek.

## <a name="limitations"></a>Korlátozások

Az Azure Adatkezelő-összekötő jelenleg nem támogatott az Azure szinapszis által felügyelt virtuális hálózatokkal.

## <a name="next-steps"></a>Következő lépések

* [Mintakód speciális beállításokkal](https://github.com/Azure/azure-kusto-spark/blob/master/samples/src/main/python/SynapseSample.py)
* [Azure Adatkezelő (Kusto) Spark-összekötő](https://github.com/Azure/azure-kusto-spark)