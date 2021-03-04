---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 04/03/2020
ms.author: crtreasu
ms.openlocfilehash: 43c8c578587c65b6e0317caf77ff2d0604cb4fa3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "102044666"
---
### <a name="build-and-deploy-the-app"></a>Az alkalmazás létrehozása és üzembe helyezése

Nyissa meg az `.sln` Unity által generált fájlt. Módosítsa a Build konfigurációját a következőre.

:::image type="content" source="./media/object-anchors-quickstarts-unity/aoa-unity-vs-build-config.png" alt-text="konfiguráció létrehozása":::

Ezután konfigurálnia kell a **távoli számítógép IP-címét** az alkalmazás üzembe helyezéséhez és hibakereséséhez.

Kattintson a jobb gombbal az alkalmazás projektre, és válassza a **Tulajdonságok** lehetőséget. A Tulajdonságok lapon válassza a **konfigurációs tulajdonságok-> hibakeresés** lehetőséget. Módosítsa a **számítógépnév** értékét a HoloLens-eszköz IP-címére, és kattintson az **alkalmaz** gombra.

:::image type="content" source="./media/object-anchors-quickstarts-unity/aoa-unity-vs-remote-debug.png" alt-text="távoli hibakeresés":::

A tulajdonságlap bezárásához. Kattintson a **távoli gép** elemre. Az alkalmazásnak el kell kezdenie a távoli eszköz kiépítését és üzembe helyezését. Ellenőrizze, hogy az eszköz aktív-e.
