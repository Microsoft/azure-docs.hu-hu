---
title: ML modell üzembe helyezése az Azure SQL Edge-ben a ONNX használatával
description: A három részből álló Azure SQL Edge oktatóanyag harmadik része, amely az Iron Ore-szennyeződések előrejelzését ismerteti, a ONNX Machine learning-modelleket futtathatja az SQL Edge-ben.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 9e5bb037b88b7c370e31d05c2d20fc6f558a8b39
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93422195"
---
# <a name="deploy-ml-model-on-azure-sql-edge-using-onnx"></a>ML modell üzembe helyezése az Azure SQL Edge-ben a ONNX használatával 

A három részből álló, az Azure SQL Edge-beli Iron Ore-szennyeződések előrejelzésére szolgáló oktatóanyag harmadik részében a következőkre lesz szüksége:

1. Az Azure SQL Edge-példányban SQL Databasehoz való kapcsolódáshoz használja a Azure Data Studio.
2. Az Iron Ore-szennyeződések előrejelzése az ONNX az Azure SQL Edge-ben.

## <a name="key-components"></a>Kulcsfontosságú összetevők

1. A megoldás alapértelmezett 500 ezredmásodpercet használ a peremhálózati hubhoz küldött üzenetek között. Ez a **program. cs** fájljában módosítható 
   ```json
   TimeSpan messageDelay = configuration.GetValue("MessageDelay", TimeSpan.FromMilliseconds(500));
   ```
2. A megoldás egy üzenetet generált a következő attribútumokkal. Adja hozzá vagy távolítsa el az attribútumokat a követelmények szerint. 
```json
{
    timestamp 
    cur_Iron_Feed
    cur_Silica_Feed 
    cur_Starch_Flow 
    cur_Amina_Flow 
    cur_Ore_Pulp_pH
    cur_Flotation_Column_01_Air_Flow
    cur_Flotation_Column_02_Air_Flow
    cur_Flotation_Column_03_Air_Flow
    cur_Flotation_Column_04_Air_Flow
    cur_Flotation_Column_01_Level
    cur_Flotation_Column_02_Level
    cur_Flotation_Column_03_Level
    cur_Flotation_Column_04_Level
    cur_Iron_Concentrate
}
```

## <a name="connect-to-the-sql-database-in-the-azure-sql-edge-instance-to-train-deploy-and-test-the-ml-model"></a>Kapcsolódás az Azure SQL Edge-példány SQL Database az ML-modell betanításához, üzembe helyezéséhez és teszteléséhez

1. Nyissa meg az Azure Data Studiót.

2. Az **üdvözlő** lapon kezdjen el egy új kapcsolattal a következő részletekkel:

   |_Mező_|_Érték_|
   |-------|-------|
   |Kapcsolat típusa| Microsoft SQL Server|
   |Kiszolgáló|A bemutatóhoz létrehozott virtuális gépen említett nyilvános IP-cím|
   |Felhasználónév|sa|
   |Jelszó|Az Azure SQL Edge-példány létrehozásakor használt erős jelszó|
   |Adatbázis|Alapértelmezett|
   |Kiszolgálócsoport|Alapértelmezett|
   |Name (Név) (nem kötelező)|Adjon meg egy opcionális nevet|

3. Kattintson a **kapcsolat** gombra.

4. A **fájl** szakaszban nyissa meg a **/deploymentscripts/MiningProcess_ONNX. jpynb** mappát abban a mappában, ahol a projektfájlok klónozása a gépen megtörtént.

5. Állítsa a rendszermagot a Python 3 értékre.


## <a name="next-steps"></a>Következő lépések

A ONNX-modellek Azure SQL Edge-ben történő használatáról további információt a [Machine learning és a AI a ONNX az SQL Edge-ben](onnx-overview.md)című témakörben talál.