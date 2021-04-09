---
title: Az Azure Container instance receptje
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan helyezhet üzembe Cognitive Services tárolókat az Azure Container instance szolgáltatásban
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: aahi
ms.openlocfilehash: 003b4411ac791898f4a7467b9b03f29aadba2fc7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "97704840"
---
# <a name="deploy-and-run-container-on-azure-container-instance"></a>Tároló üzembe helyezése és futtatása az Azure Container Instancesben

A következő lépésekkel egyszerűen méretezheti az Azure Cognitive Services-alkalmazásait a felhőben az Azure [Container instances](../../container-instances/index.yml). A tárolókra bontás segítségével az infrastruktúra kezelése helyett az alkalmazások kiépítésére koncentrálhat. További információ a tárolók használatáról: [szolgáltatások és előnyök](../cognitive-services-container-support.md#features-and-benefits).

## <a name="prerequisites"></a>Előfeltételek

A recept bármilyen Cognitive Services tárolóval működik. A recept használata előtt létre kell hozni a kognitív szolgáltatás erőforrását. Minden olyan kognitív szolgáltatás, amely támogatja a tárolók használatát, a tárolók szolgáltatásának telepítéséhez és konfigurálásához a "telepítés" című cikk nyújt tájékoztatást. Egyes szolgáltatások a tárolóhoz tartozó fájl-vagy fájl-készletet igényelnek, fontos, hogy megértse és használta a tárolót a megoldás használata előtt.

* Azure-erőforrás az Ön által használt Azure kognitív szolgáltatáshoz.
* A kognitív szolgáltatás **végpontjának URL-címe** – tekintse át az adott szolgáltatás "a telepítésének" módját a tároló számára, hogy megtudja, hol található a végpont URL-címe a Azure Portalon, és hogy az URL-cím helyes példája a következőképpen néz ki:. A pontos formátum a szolgáltatásról a szolgáltatásra változhat.
* Kognitív szolgáltatás **kulcsa** – a kulcsok az Azure-erőforrás **kulcsok** oldalán találhatók. Csak a két kulcs egyikét kell megadnia. A kulcs 32 alfanumerikus karakterekből álló karakterlánc.

* Egyetlen Cognitive Services tároló a helyi gazdagépen (a számítógépen). Győződjön meg arról, hogy a következőket teheti:
  * Húzza le a rendszerképet egy `docker pull` paranccsal.
  * Futtassa a helyi tárolót sikeresen az összes szükséges konfigurációs beállítással a `docker run` paranccsal.
  * Hívja meg a tároló végpontját, válaszoljon a HTTP-2xx és egy JSON-válaszra.

A szögletes zárójelben lévő összes változót `<>` a saját értékeivel kell helyettesíteni. Ez a csere tartalmazza a szögletes zárójeleket.

> [!IMPORTANT]
> A LUIS-tárolóhoz szükség van egy, `.gz` futásidőben lekért modellel. A tárolónak képesnek kell lennie arra, hogy elérje a modell fájlját a Container példányból származó mennyiségi csatlakoztatáson keresztül. A modell feltöltéséhez kövesse az alábbi lépéseket:
> 1. [Hozzon létre egy Azure-fájlmegosztást](../../storage/files/storage-how-to-create-file-share.md). Jegyezze fel az Azure Storage-fiók nevét, kulcsát és a fájlmegosztás nevét, mert később szüksége lesz rájuk.
> 2. [exportálja Luis-modelljét (csomagolt alkalmazás) a Luis portálról](../LUIS/luis-container-howto.md#export-packaged-app-from-luis). 
> 3. A Azure Portal navigáljon a Storage-fiók erőforrásának **Áttekintés** lapjára, és válassza a **fájlmegosztás** lehetőséget. 
> 4. Válassza ki a közelmúltban létrehozott fájlmegosztás nevét, majd válassza a **feltöltés** lehetőséget. Ezután töltse fel a csomagolt alkalmazást. 

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

[!INCLUDE [Portal instructions for creating an ACI instance](includes/create-container-instances-resource.md)]

# <a name="cli"></a>[Parancssori felület](#tab/cli)

[!INCLUDE [CLI instructions for creating an ACI instance](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

---


## <a name="use-the-container-instance"></a>A Container-példány használata

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. Válassza ki az **áttekintést** , és másolja ki az IP-címet. Ez egy numerikus IP-cím lesz, például: `55.55.55.55` .
1. Nyisson meg egy új böngésző fület, és használja az IP-címet, például: `http://<IP-address>:5000 (http://55.55.55.55:5000` ). Ekkor megjelenik a tároló kezdőlapja, amely arról tájékoztatja, hogy a tároló fut.

    ![Tároló kezdőlapja](../../../includes/media/cognitive-services-containers-api-documentation/container-webpage.png)

1. Válassza ki a **szolgáltatás API leírása** elemet a tárolóhoz tartozó felvágási oldal megtekintéséhez.

1. Válassza ki a **post** API-k bármelyikét, és válassza a **kipróbálás** lehetőséget.  A paraméterek megjelennek a bemenettel együtt. Adja meg a paramétereket.

1. Válassza a **végrehajtás** lehetőséget, hogy elküldje a kérést a tároló példányának.

    Sikeresen létrehozta és használta Cognitive Services tárolókat az Azure Container Instanceban.

# <a name="cli"></a>[Parancssori felület](#tab/cli)

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

> [!NOTE]
> Ha a Text Analytics for Health tárolót futtatja, a következő URL-cím használatával küldhet lekérdezéseket: `http://localhost:5000/text/analytics/v3.2-preview.1/entities/health`

---
