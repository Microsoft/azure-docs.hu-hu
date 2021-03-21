---
title: Azure SQL Edge üzembe helyezése turbinákon egy contoso szélerőmű-farmon
description: Ebben az oktatóanyagban az Azure SQL Edge-t fogja használni a turbinákon egy contoso szélerőmű-farmon.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 12/18/2020
ms.openlocfilehash: e966d756744210dc8f6739b96501dd91f0d8d1b5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97723473"
---
# <a name="using-azure-sql-edge-to-build-smarter-renewable-resources"></a>Intelligensebb megújuló erőforrások készítése az Azure SQL Edge használatával

Ez az Azure SQL Edge-bemutató egy contoso megújítható energia, egy szélerőmű-Farm, amely az SQL DB Edge-t használja az adatfeldolgozáshoz a generátorban. 

Ez a bemutató végigvezeti az eszközön észlelt zavarok miatt felmerülő riasztások feloldásán. A modell betanítása és üzembe helyezése az SQL DB Edge-ben történik, amely kijavítani fogja az észlelt szél felébresztését, és végső soron optimalizálja az áramellátást.

Azure SQL Edge – megújuló energia bemutató videó a Channel 9 csatornán:
> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Edge-Demo-Renewable-Energy/player]

## <a name="setting-up-the-demo-on-your-local-computer"></a>A bemutató beállítása a helyi számítógépen
A git a bemutató összes fájljának a helyi számítógépre való másolására szolgál majd. 

1. Telepítse a git-t [innen](https://git-scm.com/download).
2. Nyisson meg egy parancssort, és navigáljon ahhoz a mappához, ahová a tárházat le szeretné tölteni. 
3. Adja ki a parancsot https://github.com/microsoft/sql-server-samples.git .
4. Navigáljon a **"SQL-Server-samples\samples\demos\azure-SQL-Edge-demos\Wind Turbine demo"** azon a helyen, ahol a tárház klónozása történik.
5. Kövesse a README.md utasításait a bemutató környezet beállításához és a bemutató végrehajtásához.