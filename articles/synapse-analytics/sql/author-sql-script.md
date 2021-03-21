---
title: SQL-parancsfájlok a szinapszis Studióban
description: Bevezetés a szinapszis Studio SQL-parancsfájljaiba az Azure szinapszis Analytics szolgáltatásban.
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: omafnan
ms.openlocfilehash: 4ed02901aa0d6948e9c6443e5bbcf4ebfbc872f7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98118693"
---
# <a name="synapse-studio-sql-scripts-in-azure-synapse-analytics"></a>A szinapszis Studio SQL-parancsfájljai az Azure szinapszis Analyticsben 

A szinapszis Studio SQL-szkriptek webes felületét biztosít az SQL-lekérdezések létrehozásához. 

## <a name="begin-authoring-in-sql-script"></a>Szerzői műveletek megkezdése az SQL-szkriptben 

A szerzői műveletek többféleképpen is elindíthatók az SQL-szkriptekben. A következő módszerek egyikével hozhat létre új SQL-parancsfájlt.

1. A fejlesztés menüben válassza a **"+"** ikont, és válassza az **SQL-szkript** elemet.

2. A **műveletek** menüben válassza az **új SQL-parancsfájl** lehetőséget.

3. Az SQL-parancsfájlok fejlesztése területen válassza az **Importálás** lehetőséget a **műveletek** menüből. Válasszon ki egy meglévő SQL-parancsfájlt a helyi tárolóból.
![új SQL Script 3 művelet](media/author-sql-script/new-sql-script-3-actions.png)

## <a name="create-your-sql-script"></a>SQL-parancsfájl létrehozása

1. Válassza ki az SQL-szkript nevét, ehhez kattintson a **tulajdonság** gombra, és cserélje le az SQL-parancsfájlhoz rendelt alapértelmezett nevet. 
![új SQL-parancsfájl átnevezése](media/author-sql-script/new-sql-script-rename.png)

2. Válassza ki az adott dedikált SQL-készletet vagy kiszolgáló nélküli SQL-készletet a **Kapcsolódás** legördülő menüből. Vagy szükség esetén válassza ki az adatbázist a **használati adatbázisból**. 
![új SQL-készlet kiválasztása](media/author-sql-script/new-sql-choose-pool.png)

3. Indítsa el az SQL-szkriptet az IntelliSense szolgáltatás használatával.

## <a name="run-your-sql-script"></a>SQL-parancsfájl futtatása

Kattintson a **Futtatás** gombra az SQL-parancsfájl végrehajtásához. Az eredmények alapértelmezés szerint egy táblában jelennek meg.

![új SQL-parancsfájl eredményeinek táblázata](media/author-sql-script/new-sql-script-results-table.png)

## <a name="export-your-results"></a>Az eredmények exportálása

Az eredményeket a helyi tárhelyre különböző formátumokban (beleértve a CSV, az Excel, a JSON és az XML) is exportálhatja, ha az "exportálás eredménye" lehetőséget választja, és kiválasztja a bővítményt.

**A diagram gomb** kiválasztásával megjelenítheti az SQL-parancsfájl eredményeit is. Válassza a "diagram típusa" és a **category (kategória) oszlopot**. A diagramot képként is exportálhatja a **Mentés** képként lehetőség kiválasztásával. 

![új SQL-parancsfájl eredményeinek diagramja](media/author-sql-script/new-sql-script-results-chart.png)

## <a name="explore-data-from-a-parquet-file"></a>Adatok feltárása a Parquet-fájlokból

A Storage-fiókokban lévő parketta-fájlokat az SQL-szkript használatával tekintheti meg a fájl tartalmának megtekintéséhez.

![új script sqlod parketta](media/author-sql-script/new-script-sqlod-parquet.png)

## <a name="sql-tables-external-tables-views"></a>SQL-táblák, külső táblák, nézetek

Ha kiválasztja a **műveletek** menüt az adat területen, több műveletet is kijelölhet, például a következőket:

- Új SQL-parancsfájl
- TOP 1000-sorok kiválasztása
- CREATE
- Eldobás és létrehozás 
 
Az elérhető kézmozdulatok megismeréséhez kattintson a jobb gombbal az SQL-adatbázisok csomópontjaira.
 
![új parancsfájl-adatbázis](media/author-sql-script/new-script-database.png)

## <a name="create-folders-and-move-sql-scripts-into-a-folder"></a>Mappák létrehozása és SQL-parancsfájlok áthelyezése egy mappába

Az SQL-parancsfájlok fejlesztése szakaszban található Műveletek menüben válassza az "új mappa" lehetőséget a "műveletek" menüben az SQL-parancsfájlok fejlesztése területen. És írja be az új mappa nevét az előugró ablakban. 

> [!div class="mx-imgBorder"] 
> ![Képernyőkép, amely egy SQL-parancsfájlt mutat be, amelyben az "új mappa" van kiválasztva.](./media/author-sql-script/new-sql-script-create-folder.png)

Ha egy SQL-parancsfájlt szeretne áthelyezni egy mappába, válassza ki az SQL-parancsfájlt, és válassza a "áthelyezés" lehetőséget a Műveletek menüből. Ezután keresse meg a célmappát az új ablakban, és helyezze át az SQL-parancsfájlt a kiválasztott mappába. Azt is megteheti, hogy gyorsan húzza az SQL-parancsfájlt, és eldobja egy mappába.  

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/new-sql-script-move-folder.png)

## <a name="next-steps"></a>Következő lépések

Az SQL-parancsfájlok létrehozásával kapcsolatos további információkért lásd: az [Azure szinapszis Analytics](../index.yml).