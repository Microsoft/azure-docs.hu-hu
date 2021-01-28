---
title: Data Factory-életútadatok leküldése az Azure Purview-ba
description: Tudnivalók a Data Factory Lineage-adatainak az Azure-beli hatáskörébe való leküldéséről
services: data-factory
ms.author: lle
author: lrtoyou1223
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 12/3/2020
ms.openlocfilehash: e87a9d677fee94d410099db1da80a56b5539048c
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98935469"
---
# <a name="push-data-factory-lineage-data-to-azure-purview-preview"></a>Leküldés Data Factory Lineage-adatként az Azure hatáskörébe (előzetes verzió)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ebben az oktatóanyagban a Data Factory felhasználói felületét fogja használni egy olyan folyamat létrehozásához, amely a tevékenységeket és a jelentések Lineage-adatbázisát futtatja az Azure hatáskörébe fiókba. Ezután megtekintheti az Azure-beli hatáskörébe tartozó fiók összes információját.

## <a name="prerequisites"></a>Előfeltételek
* **Azure-előfizetés**. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* **Azure Data Factory**. Ha nem rendelkezik Azure Data Factoryval, tekintse meg [a Azure Data Factory létrehozása](./quickstart-create-data-factory-portal.md)című témakört.
* **Azure-beli hatáskörébe tartozó fiók**. A hatáskörébe tartozó fiók rögzíti az adatelőállító által létrehozott összes Lineage-adatmennyiséget. Ha nem rendelkezik Azure-beli hatáskörébe-fiókkal, tekintse meg [Az Azure-beli rendszer létrehozása](../purview/create-catalog-portal.md)című témakört.


## <a name="run-data-factory-activities-and-push-lineage-data-to-azure-purview"></a>Data Factory tevékenységek futtatása és a leküldés az Azure hatáskörébe
### <a name="step-1--connect-data-factory-to-your-purview-account"></a>1. lépés: a Data Factory kapcsolódása a hatáskörébe-fiókhoz
Jelentkezzen be a hatáskörébe-fiókjába a **felügyeleti központban**. Válassza a **külső kapcsolatok** **Data Factory** lehetőséget, majd kattintson az **új** gombra a kapcsolat új Data Factory való létrehozásához. 
[![Képernyőfelvétel Data Factory és a hatáskörébe tartozó fiók ](./media/data-factory-purview/connect-adf-to-purview.png) közötti kapcsolat létrehozásához ](./media/data-factory-purview/connect-adf-to-purview.png#lightbox)

A felugró lapon kiválaszthatja a Data Factory, amelyhez csatlakozni szeretne ehhez a hatáskörébe-fiókhoz. 
![Képernyőfelvétel új kapcsolatban](./media/data-factory-purview/new-adf-purview-connection.png)

A kapcsolatok létrehozása után az állapotot is megtekintheti. A **csatlakoztatott** beállítás azt jelenti, hogy a Data Factory és a jelen hatáskörébe tartozó kapcsolat sikeresen csatlakoztatva van. 
> [!NOTE]
> Az alábbi szerepkörök közül bármelyiket hozzá kell rendelnie a hatáskörébe fiókban, és Data Factory **közreműködő** szerepkört a Data Factory és az Azure hatáskörébe tartozó kapcsolat létrehozásához.
> - Tulajdonos
> - Felhasználói hozzáférés rendszergazdája

### <a name="step-2-run-copy-and-dataflow-activities-in-data-factory"></a>2. lépés: a másolási és a adatfolyam tevékenységek futtatása a Data Factoryban
Létrehozhat folyamatokat, másolási tevékenységeket és adatfolyam tevékenységeket Data Factoryban. Nincs szükség további konfigurációra a Lineage-adatrögzítéshez. A rendszer a tevékenységek végrehajtása során automatikusan rögzíti a Lineage-adatsorokat.
![Képernyőkép a másolási és a adatfolyam tevékenységről ](./media/data-factory-purview/adf-activities-for-lineage.png) , ha nem tudja, hogyan hozhat létre másolási és adatfolyam tevékenységeket, lásd: [adatok másolása az Azure Blob Storage-ból egy adatbázisba Azure SQL Database az](./tutorial-copy-data-portal.md) adatok [leképezése adatforgalom használatával](./tutorial-data-flow.md)történő Azure Data Factory és átalakításával.

### <a name="step-3-run-execute-ssis-package-activities-in-data-factory"></a>3. lépés: SSIS-csomag végrehajtásának futtatása Data Factory
Létrehozhat folyamatokat, és végrehajthat SSIS-csomagokat Data Factoryban. Nincs szükség további konfigurációra a Lineage-adatrögzítéshez. A rendszer a tevékenységek végrehajtása során automatikusan rögzíti a Lineage-adatsorokat.
![Képernyőkép a SSIS-csomag végrehajtásáról](./media/data-factory-purview/ssis-activities-for-lineage.png)

Ha nem tudja, hogyan hozhatja létre a SSIS-csomag végrehajtását, tekintse [meg a SSIS-csomagok futtatása az Azure-ban](./tutorial-deploy-ssis-packages-azure.md)című témakört.

### <a name="step-4-view-lineage-information-in-your-purview-account"></a>4. lépés: az információs vonal adatainak megtekintése a hatáskörébe tartozó fiókban
Lépjen vissza a hatáskörébe tartozó fiókjába. A kezdőlapon válassza az **eszközök tallózása** lehetőséget. Válassza ki a kívánt eszközt, majd kattintson a Lineage (Lineage) fülre. Ekkor megjelenik az összes Lineage-információ.
[![A hatáskörébe tartozó fiók ](./media/data-factory-purview/view-dataset.png) képernyőképe ](./media/data-factory-purview/view-dataset.png#lightbox)

A másolási tevékenységhez a Lineage-információk láthatók.
[![A másolási vonal ](./media/data-factory-purview/copy-lineage.png) képernyőképe ](./media/data-factory-purview/copy-lineage.png#lightbox)

Megtekintheti a adatfolyam tevékenységhez tartozó adatbányászati tevékenységeket is.
[![Képernyőkép a adatfolyam lineageről ](./media/data-factory-purview/dataflow-lineage.png)](./media/data-factory-purview/dataflow-lineage.png#lightbox)

> [!NOTE] 
> A adatfolyam tevékenység kifejlődéséhez csak a forrás-és a fogadót támogatjuk. A adatfolyam átalakításának leszármazása még nem támogatott.

Megtekintheti az SSIS-csomag végrehajtásához kapcsolódó adatbányászati információt is.
[![Képernyőkép a SSIS lineageről ](./media/data-factory-purview/ssis-lineage.png)](./media/data-factory-purview/ssis-lineage.png#lightbox)

> [!NOTE] 
> A SSIS-csomag végrehajtásának elvégzéséhez csak a forrás-és a célhelyet támogatjuk. Az átalakítási vonal még nem támogatott.

## <a name="next-steps"></a>Következő lépések
[A katalógus Lineage felhasználói útmutatója](../purview/catalog-lineage-user-guide.md)

[Data Factory összekötése az Azure hatáskörébe](connect-data-factory-to-azure-purview.md)