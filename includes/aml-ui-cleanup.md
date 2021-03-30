---
title: fájl belefoglalása
description: fájl belefoglalása
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: sgilley
ms.author: sgilley
ms.date: 11/06/2019
ms.openlocfilehash: 754c9799ed4c2fd90cbcf1e9717b0be21edd54cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "75659872"
---
>[!IMPORTANT]
>A létrehozott erőforrásokat felhasználhatja más Azure Machine Learning-oktatóanyagok és útmutatók előfeltételeiként.

### <a name="delete-everything"></a>Mindent törölni

Ha nem tervezi a létrehozott bármit használni, törölje a teljes erőforráscsoportot, így nem számítunk fel díjat.

1. A Azure Portal az ablak bal oldalán válassza az **erőforráscsoportok** lehetőséget.
 
   ![Erőforráscsoport törlése az Azure Portalon](./media/aml-ui-cleanup/delete-resources.png)

1. A listából válassza ki a létrehozott erőforráscsoportot.

1. Válassza az **Erőforráscsoport törlése** elemet.

Az erőforráscsoport törlése a tervezőben létrehozott összes erőforrást is törli. 

### <a name="delete-individual-assets"></a>Egyedi eszközök törlése

A tervezőben, ahol létrehozta a kísérletet, törölje az egyes objektumokat úgy, hogy kiválasztja őket, majd a **Törlés** gombra kattint.

Az itt létrehozott számítási cél *automatikusan automatikus méretezést* végez a nulla csomópontokra, amikor az nincs használatban. Ez a művelet a díjak minimalizálásához szükséges. Ha törölni szeretné a számítási célt, hajtsa végre a következő lépéseket:

![Eszközök törlése](./media/aml-ui-cleanup/delete-asset.png)

Az adatkészletek regisztrációját a munkaterületről törölheti, ha kiválasztja az egyes adatkészleteket, és kiválasztja a **Regisztráció törlése** lehetőséget.

![Adatkészlet regisztrációjának törlése](./media/aml-ui-cleanup/unregister-dataset1225.png)

Az adatkészletek törléséhez nyissa meg a Storage-fiókot a Azure Portal vagy Azure Storage Explorer használatával, és manuálisan törölje ezeket az eszközöket.


