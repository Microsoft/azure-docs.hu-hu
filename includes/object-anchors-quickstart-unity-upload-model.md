---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 03/02/2021
ms.author: crtreasu
ms.openlocfilehash: d8dfc3d4b7a8447250481b98c1adadc865a29da1
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102532612"
---
### <a name="upload-your-model"></a>A modell feltöltése

Ha még nem rendelkezik objektum-összekötési modellel, kövesse a [modell létrehozása](/azure/object-anchors/quickstarts/get-started-model-conversion) létrehozásával kapcsolatos utasításokat. Ezután térjen vissza ide.

Ha a HoloLens csatlakozik a Windows-eszköz portálhoz, kövesse az alábbi lépéseket az alkalmazás által használandó modell feltöltéséhez:

1. A Windows-eszközök portálon nyissa meg a **System > file explorer > LocalAppData**. Itt látnia kell az alkalmazást az alkalmazások listáján.

    :::image type="content" source="./media/object-anchors-quickstarts-unity/portal-localappdata.png" alt-text="fájlkezelő":::

2. Nyissa meg az alkalmazást, és kattintson a `LocalState` mappára.

    :::image type="content" source="./media/object-anchors-quickstarts-unity/portal-localstate.png" alt-text="A LocalState mappa megnyitása":::

3. Töltse fel a modellt tartalmazó fájlt a `LocalState` mappába.

    :::image type="content" source="./media/object-anchors-quickstarts/portal-upload-model.png" alt-text="a modell feltöltése a portálon":::

    Indítsa el újra az alkalmazást a HoloLens. Mostantól a modellel egyező objektumokat is felderítheti.
