---
title: A sablonok áttekintése
description: Megtudhatja, hogyan használható előre definiált sablon a Azure Data Factory gyors megkezdéséhez.
ms.service: data-factory
ms.topic: conceptual
ms.author: daperlov
author: djpmsft
ms.custom: seo-lt-2019
ms.date: 01/04/2019
ms.openlocfilehash: 8c0e4db2bc686fff2bd718f45c63a0fc26f6cd55
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100375391"
---
# <a name="templates"></a>Sablonok

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A sablonok előre meghatározott Azure Data Factory folyamatok, amelyek lehetővé teszik a Data Factory gyors megkezdését. A sablonok akkor hasznosak, ha új Data Factory, és gyors kezdésre van szükség. Ezek a sablonok csökkentik az adatintegrációs projektek kiépítésének fejlesztési idejét, ezáltal javítják a fejlesztői hatékonyságot.

## <a name="create-data-factory-pipelines-from-templates"></a>Data Factory folyamatok létrehozása sablonokból

A következő két módon teheti meg a Data Factory folyamat létrehozását a sablonból:

1.  Válassza a **folyamat létrehozása sablonból** lehetőséget az Áttekintés lapon a sablon-gyűjtemény megnyitásához.

    ![A sablon-gyűjtemény megnyitása az Áttekintés lapról](media/solution-templates-introduction/templates-intro-image1.png)

1.  A erőforrás-kezelő szerző lapján válassza **+** a, majd a **folyamat sablonból** lehetőséget a sablon-gyűjtemény megnyitásához.

    ![A sablon-gyűjtemény megnyitása a szerző lapról](media/solution-templates-introduction/templates-intro-image2.png)

## <a name="template-gallery"></a>Sablon gyűjteménye

![A sablon gyűjteménye](media/solution-templates-introduction/templates-intro-image3.png)

### <a name="out-of-the-box-data-factory-templates"></a>Data Factory sablonok

A Data Factory Azure Resource Manager sablonokat használ a következő adattároló-sablonok mentéséhez:. Megtekintheti az összes Resource Manager-sablont, valamint a Data Factory sablonokhoz használt jegyzékfájlt a GitHub-tárház [hivatalos Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/templates). A Microsoft által biztosított előre definiált sablonok közé tartoznak a következők, de nem korlátozódnak a következő elemekre:

-   Sablonok másolása:

    -   [Tömeges másolás az adatbázisból](solution-template-bulk-copy-with-control-table.md)
    
    -   [Új fájlok másolása az utolsó módosítás dátuma szerint](solution-template-copy-new-files-lastmodifieddate.md)

    -   [Több file-tároló másolása a fájl alapú tárolók között](solution-template-copy-files-multiple-containers.md)

    -   [Fájlok áthelyezése](solution-template-move-files.md)

    -   [Különbözeti másolat az adatbázisból](solution-template-delta-copy-with-control-table.md)

    -   Másolás innen \<source\>\<destination\>

        -   [Amazon S3-ról Azure Data Lake Store Gen 2](solution-template-migration-s3-azure.md)

        -   A Google Big Queryből a 2. generációs Azure Data Lake Store

        -   HDF-ről Azure Data Lake Store Gen 2-re

        -   Netezza-ről Azure Data Lake Store Gen 1

        -   SQL Server a helyszínen Azure SQL Database

        -   SQL Server a helyszínen az Azure szinapszis Analyticsbe

        -   A helyszíni Oracle-ből az Azure szinapszis Analyticsbe

-   SSIS-sablonok

    -   SSIS-csomagok végrehajtásának ütemezett Azure-SSIS Integration Runtime

-   Sablonok átalakítása

    -   [ETL Azure Databricks](solution-template-databricks-notebook.md)

### <a name="my-templates"></a>Saját sablonok

A folyamatokat sablonként is mentheti a folyamat lapon a **Mentés sablonként** lehetőség kiválasztásával.

![Folyamat mentése sablonként](media/solution-templates-introduction/templates-intro-image4.png)

Megtekintheti a sablonokként mentett folyamatokat a sablon-gyűjtemény **saját sablonok** szakaszában. Ezeket a erőforrás-kezelő **sablonok** szakaszában is megtekintheti.

![Saját sablonok](media/solution-templates-introduction/templates-intro-image5.png)

> [!NOTE]
> A saját sablonok funkció használatához engedélyeznie kell a GIT-integrációt. Az Azure DevOps GIT és a GitHub is támogatott.
