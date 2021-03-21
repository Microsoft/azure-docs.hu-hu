---
title: Betöltés az Azure Storage-környezetbe – csoportos adatelemzési folyamat
description: Megtudhatja, hogyan lehet az adatgyűjtést különböző célpont-környezetekben tárolni és feldolgozni.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 46e911ad6bdf5a478d46e425f8700740ece56c6e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96452665"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>Adatok betöltése a tárolási környezetekbe elemzés céljából

A csoportos adatelemzési folyamat megköveteli, hogy az egyes fázisokban az adatmennyiséget a legmegfelelőbb módon kell betölteni vagy betölteni. Az adatcélhelyek közé tartozhatnak az Azure Blob Storage, a SQL Azure SQL Server-adatbázisok, az Azure-beli virtuális gépek, a HDInsight (Hadoop), az Azure szinapszis Analytics és a Azure Machine Learning. 

Az alábbi cikkek azt ismertetik, hogyan lehet az adatgyűjtést különböző célpont-környezetekben tárolni és feldolgozni.

* [Azure-Blob Storage](move-azure-blob.md)
* SQL Server az Azure-beli [virtuális gépen](move-sql-server-virtual-machine.md)
* [Azure SQL Database](move-sql-azure.md)
* A [struktúra tábláiba](move-hive-tables.md)
* [SQL-particionálású táblákhoz](parallel-load-sql-partitioned-tables.md)
* Helyszíni [SQL Server](move-sql-azure-adf.md)

A technikai és üzleti igények, valamint az adatmennyiség kezdeti helye, formátuma és mérete határozza meg a legjobb adatfeldolgozási tervet. Nem ritka, hogy a legjobb terv több lépésből áll. A feladatok ezen sorozata magában foglalhatja például az adatok feltárását, az előfeldolgozást, a tisztítást, a le-mintavételezést és a modellek betanítását.  Azure Data Factory egy ajánlott Azure-erőforrás az adatáthelyezés és-átalakítás előkészítéséhez.
