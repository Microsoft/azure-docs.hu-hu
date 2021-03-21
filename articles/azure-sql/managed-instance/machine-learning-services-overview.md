---
title: Machine Learning Services az Azure SQL felügyelt példányában
description: Ez a cikk áttekintést nyújt vagy Machine Learning Services az Azure SQL felügyelt példányain.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: sstein, davidph
manager: cgronlun
ms.date: 03/17/2021
ms.openlocfilehash: 94495144c64b3770995a5f67e9129b3ba86e741e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599561"
---
# <a name="machine-learning-services-in-azure-sql-managed-instance"></a>Machine Learning Services az Azure SQL felügyelt példányában

A Machine Learning Services az Azure SQL felügyelt példányának egyik funkciója, amely az adatbázison belüli gépi tanulást nyújt, és támogatja a Python és az R szkripteket is. A szolgáltatás a Microsoft Python és R csomagokat tartalmazza a nagy teljesítményű prediktív elemzéshez és a gépi tanuláshoz. A kapcsolódó adatokat parancsfájlokban tárolt eljárásokkal, a Python-vagy R-utasításokat tartalmazó T-SQL-szkripttel, illetve a T-SQL-T tartalmazó Python vagy R kód használatával lehet használni.

## <a name="what-is-machine-learning-services"></a>Mi az Machine Learning Services?

A felügyelt Azure SQL-példányok Machine Learning Services lehetővé teszi a Python-és R-parancsfájlok futtatását az adatbázisban. Felhasználhatja az adatgyűjtést és-tisztítást, a szolgáltatások fejlesztését, valamint a gépi tanulási modellek adatbázison belüli betanítását, kiértékelését és üzembe helyezését. A szolgáltatás futtatja a parancsfájlokat, ahol az adatok találhatók, és kiküszöböli az adatok átvitelét a hálózaton keresztül egy másik kiszolgálóra.

Machine Learning Services használata R/Python-támogatással az Azure SQL felügyelt példányain:

- **R-és Python-parancsfájlok futtatása az adat-előkészítéshez és az általános célú adatfeldolgozáshoz** : mostantól az r/Python-szkripteket áthelyezheti az Azure SQL felügyelt példányára, ahol az adatai életbe léptek, ahelyett, hogy át kellene helyeznie az adatátvitelt egy másik kiszolgálóra az r-és Python-parancsfájlok A késéssel, a biztonsággal és a megfelelőséggel kapcsolatos adatáthelyezési és kapcsolódó problémák elhárítása nem szükséges.

- **Gépi tanulási modellek betanítása az adatbázisban** – bármely nyílt forráskódú algoritmus használatával betaníthatja a modelleket. A betanítást egyszerűen méretezheti a teljes adathalmazra, nem pedig az adatbázisból kihúzott minta adatkészletekre.

- **Modellek és szkriptek üzembe helyezése tárolt eljárásokban** – a parancsfájlok és a betanított modellek csak a T-SQL tárolt eljárásokban való beágyazással működőképesek. Az Azure SQL felügyelt példányához csatlakozó alkalmazások az ilyen modellek előrejelzéseit és intelligencia előnyeit csak tárolt eljárás meghívásával érhetik el. A natív T-SQL ELŐREJELZÉSi függvényt is használhatja a működővé tenni modellek gyors értékeléséhez a nagy egyidejű valós idejű pontozási forgatókönyvekben.

A Python és az R alapszintű terjesztése a Machine Learning Services része. Olyan nyílt forráskódú csomagokat és keretrendszereket telepíthet és használhat, mint például a PyTorch, a TensorFlow és a scikit-Learn, valamint a Microsoft-csomagok [revoscalepy csomagjai](/sql/machine-learning/python/ref-py-revoscalepy) és [microsoftml](/sql/machine-learning/python/ref-py-microsoftml) a Pythonhoz, valamint [RevoScaleR](/sql/machine-learning/r/ref-r-revoscaler), [microsoftml](/sql/machine-learning/r/ref-r-microsoftml), [OLAP](/sql/machine-learning/r/ref-r-olapr)és [sqlrutils](/sql/machine-learning/r/ref-r-sqlrutils) for R.

## <a name="how-to-enable-machine-learning-services"></a>A Machine Learning Services engedélyezése

Engedélyezheti Machine Learning Services az Azure SQL felügyelt példányában a következő SQL-parancsokkal történő bővíthetőség engedélyezésével (az SQL felügyelt példánya újraindul, és néhány másodpercig nem érhető el):

```sql
sp_configure 'external scripts enabled', 1;
RECONFIGURE WITH OVERRIDE;
```

További információ arról, hogy a parancs hogyan befolyásolja az SQL felügyelt példányok erőforrásait: [erőforrás-szabályozás](machine-learning-services-differences.md#resource-governance).

### <a name="enable-machine-learning-services-in-a-failover-group"></a>Machine Learning Services engedélyezése feladatátvételi csoportban

Egy [feladatátvételi csoportban](failover-group-add-instance-tutorial.md)a rendszeradatbázisok nem replikálódnak a másodlagos példányra (lásd a [feladatátvételi csoportok korlátozásait](../database/auto-failover-group-overview.md#limitations-of-failover-groups) további információért).

Ha az Ön által használt felügyelt példány egy feladatátvételi csoport része, tegye a következőket:

- A `sp_configure` Machine learning Services engedélyezéséhez futtassa a és a `RECONFIGURE` parancsot a feladatátvételi csoport minden példányán.

- A Master adatbázis helyett telepítse az R/Python-kódtárakat egy felhasználói adatbázisba.

## <a name="next-steps"></a>Következő lépések

- Tekintse meg [SQL Server Machine learning Services főbb különbségeit](machine-learning-services-differences.md).
- Ha szeretné megtudni, hogyan használható a Python a Machine Learning Servicesban, tekintse meg a [Python-parancsfájlok futtatása](/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)című témakört.
- Az R-Machine Learning Services használatának megismeréséhez tekintse meg az [r-parancsfájlok futtatása](/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)című témakört.
- A más SQL-platformokon futó gépi tanulásról további információt az [SQL Machine learning dokumentációjában](/sql/machine-learning/index)talál.
