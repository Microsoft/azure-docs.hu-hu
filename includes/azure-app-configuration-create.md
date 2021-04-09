---
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: include
ms.date: 1/31/2020
ms.openlocfilehash: c344d7e1aa1f6d45131295ba9aad1294c5ba548c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96930711"
---
1. Új alkalmazás-konfigurációs tároló létrehozásához jelentkezzen be a [Azure Portalba](https://portal.azure.com). A Kezdőlap bal felső sarkában válassza az **erőforrás létrehozása** lehetőséget. A **Keresés a piactéren** mezőbe írja be az *alkalmazás konfigurációját* , és válassza az <kbd>ENTER billentyűt</kbd>.

    ![Alkalmazás konfigurációjának keresése](media/azure-app-configuration-create/azure-portal-search.png)

1. Válassza ki az **alkalmazás konfigurációját** a keresési eredmények közül, majd válassza a **Létrehozás** lehetőséget.

    ![A Létrehozás lehetőség kiválasztása](media/azure-app-configuration-create/azure-portal-app-configuration-create.png)

1. Az **alkalmazás-konfiguráció létrehozása** panelen adja meg a következő beállításokat:

    | Beállítás | Ajánlott érték | Leírás |
    |---|---|---|
    | **Előfizetés** | Az Ön előfizetése | Válassza ki az alkalmazás konfigurációjának teszteléséhez használni kívánt Azure-előfizetést. Ha a fiókja csak egyetlen előfizetéssel rendelkezik, akkor automatikusan ki van választva, és az **előfizetések** listája nem jelenik meg. |
    | **Erőforráscsoport** | *AppConfigTestResources* | Válasszon ki vagy hozzon létre egy erőforráscsoportot az alkalmazás konfigurációs tárolójának erőforrásához. Ez a csoport akkor lehet hasznos, ha több olyan erőforrást szeretne szervezni, amelyet az erőforráscsoport törlésével egyszerre törölni kíván. További információ: [erőforráscsoportok használata az Azure-erőforrások kezeléséhez](../articles/azure-resource-manager/management/overview.md). |
    | **Erőforrás neve** | Globálisan egyedi név | Adja meg az alkalmazás konfigurációs tárolási erőforrásához használandó egyedi erőforrás nevét. A névnek 5 és 50 karakter közötti sztringnek kell lennie, és csak számokat, betűket és `-` karaktert tartalmazhat. A név nem kezdődhet vagy végződhet a `-` karakterrel. |
    | **Hely** | *USA középső régiója* | A **Location (hely** ) használatával adja meg azt a földrajzi helyet, amelyben az alkalmazás konfigurációs tárolója üzemel. A legjobb teljesítmény érdekében hozza létre az erőforrást ugyanabban a régióban, mint az alkalmazás többi összetevőjét. |
    | **Tarifacsomag** | *Ingyenes* | Válassza ki a kívánt árképzési szintet. További információt az [alkalmazás konfigurációjának díjszabását ismertető oldalon](https://azure.microsoft.com/pricing/details/app-configuration)talál. |

1. Válassza a **felülvizsgálat + létrehozás** lehetőséget a beállítások érvényesítéséhez.

1. Válassza a **Létrehozás** lehetőséget. Az üzembe helyezés néhány percet is igénybe vehet.

1. Az üzembe helyezés befejeződése után navigáljon az alkalmazás konfigurációs erőforrásához. Válassza a **Beállítások** > **Hozzáférési kulcsok** lehetőséget. Jegyezze fel az elsődleges írásvédett kulcs-összekapcsolási karakterláncot. Ezt a kapcsolódási karakterláncot később fogja használni az alkalmazás konfigurálásához az Ön által létrehozott alkalmazás-konfigurációs tárolóval való kommunikációhoz.