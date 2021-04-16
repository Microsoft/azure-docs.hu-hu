---
title: Eltérések az Azure Data Factoryhoz képest
description: Megtudhatja, hogyan különböznek a Azure Synapse Analytics adatintegrációs képességei a Azure Data Factory
services: synapse-analytics
author: kromerm
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: conceptual
ms.date: 12/10/2020
ms.author: makromer
ms.reviewer: jrasnick
ms.openlocfilehash: 144bdf5e94f753090dd73e5839b6c1fd25f11811
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567638"
---
# <a name="data-integration-in-azure-synapse-analytics-versus-azure-data-factory"></a>Adatintegráció a Azure Synapse Analytics és Azure Data Factory

A Azure Synapse Analytics az olyan adatintegrációs képességek, mint a Synapse-folyamatok és az adatfolyamok az adatintegrációs képességeken Azure Data Factory. További információkért lásd [a Azure Data Factory.](../../data-factory/introduction.md)


## <a name="available-features-in-adf--azure-synapse-analytics"></a>Az ADF-ben elérhető & Azure Synapse Analytics

Tekintse meg az alábbi táblázatban a funkciók rendelkezésre állását:

| Kategória                 | Szolgáltatás    |  Azure Data Factory  | Azure Synapse Analytics |
| ------------------------ | ---------- | :------------------: | :---------------------: |
| **Integration Runtime**  | Az SSIS és a SSIS Integration Runtime | ✓ | ✗ |
|                          | Régiók közötti adatforgalom Integration Runtime (adatfolyamok) | ✓ | ✗ |
|                          | Integration Runtime megosztás | ✓<br><small>*Megosztható különböző adat-üzemek között* | ✗ |
|                          | Az idő az élőben | ✓ | ✗ |
| **Folyamattevékenységek** | SSIS-csomagtevékenység | ✓ | ✗ |
|                          | Az Power Query támogatása | ✓ | ✓ |
| **Sablongaléria és tudásközpont** | Megoldássablonok | ✓<br><small>*Azure Data Factory sablongyűjtemény* | ✓<br><small>*Synapse Workspace Knowledge Center* |
| **GIT-adattár integrációja** | GIT-integráció | ✓ | ✓ |
| **Figyelés**           | Spark-feladatok monitorozása Adatfolyam | ✗ | ✓<br><small>*A Synapse Spark-készletek kihasználás* |
|                          | Integráció a Azure Monitor | ✓ | ✗ |
| **Lineage** | Támogatja a folyamatokból való leszárazás adatainak a Végleges nézetben való közzétételét  | ✓ | ✗ |  

> [!Note]
> **Az időtartam** egy Azure Integration Runtime beállítás, amely lehetővé teszi, hogy a Spark-fürt egy ideig meleg maradjon az adatfolyam végrehajtását követően. 
>


## <a name="next-steps"></a>Következő lépések

A Synapse-munkaterület adatintegrációjának első lépésekhez megtanulja, hogyan lehet adatokat Azure Data Lake Storage [gen2-fiókba.](data-integration-data-lake.md)
