---
title: fájl belefoglalása
description: fájl belefoglalása
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 08/13/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 3221c4f3e196cf1573bd7c0424fa3b4530c0b2ca
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90709660"
---
## <a name="create-azure-context"></a>Azure-környezet létrehozása

Ha a Azure Container Instances tárolók futtatásához Docker-parancsokat szeretne használni, először jelentkezzen be az Azure-ba:

```bash
docker login azure
```

Ha a rendszer kéri, adja meg vagy válassza ki az Azure-beli hitelesítő adatait.


Hozzon létre egy ACI-környezetet a futtatásával `docker context create aci` . Ez a környezet egy Azure-előfizetéssel és-erőforráscsoporthoz társítja a Docker-t, így tároló-példányokat hozhat létre és kezelhet. Például egy *myacicontext* nevű környezet létrehozásához:

```
docker context create aci myacicontext
```

Ha a rendszer kéri, válassza ki az Azure-előfizetés AZONOSÍTÓját, válasszon ki egy meglévő erőforráscsoportot, vagy **hozzon létre egy új erőforráscsoportot**. Ha új erőforráscsoportot választ, akkor a rendszer által létrehozott névvel jön létre. Az Azure Container Instancest – mint minden Azure-erőforrást – egy erőforráscsoportban kell üzembe helyezni. Az erőforráscsoportok lehetővé teszik az egymáshoz kapcsolódó Azure-erőforrások rendszerezését és kezelését.


A futtatásával `docker context ls` erősítse meg, hogy hozzáadta az ACI környezetet a Docker-környezetekhez:

```
docker context ls
```