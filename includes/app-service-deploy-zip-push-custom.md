---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 0019e50615f3e66778709ad8cb28f92967c66e2e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018432"
---
## <a name="deployment-customization"></a>Központi telepítés testreszabása

A telepítési folyamat azt feltételezi, hogy a leküldhető. zip fájl egy használatra kész alkalmazást tartalmaz. Alapértelmezés szerint nincsenek futtatva testreszabások. Ha ugyanazokat a fordítási folyamatokat szeretné engedélyezni, amelyek folyamatos integrációval rendelkeznek, adja hozzá a következőt az alkalmazás beállításaihoz:

`SCM_DO_BUILD_DURING_DEPLOYMENT=true`

Ha a. zip leküldéses telepítést használja, ez a beállítás alapértelmezés szerint **hamis** . Az alapértelmezett érték a folyamatos integrációs telepítések esetében **igaz** . Ha **igaz** értékre van állítva, a rendszer az üzembe helyezéshez kapcsolódó beállításokat használja az üzembe helyezés során. Ezeket a beállításokat alkalmazás-beállításokként vagy a. zip-fájl gyökerében található. Deployment konfigurációs fájlban is konfigurálhatja. További információ: [adattár és központi telepítés – kapcsolódó beállítások](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings) a központi telepítési útmutatóban.