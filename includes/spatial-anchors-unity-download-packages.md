---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 03/30/2021
ms.author: parkerra
ms.openlocfilehash: 7faab3340483d99fa276de06f3fd7787457edb9e
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076699"
---
A következő lépés az Azure térbeli horgonyok csomagjainak letöltése az Unity számára. 

> [!WARNING]
> Az ASA SDK-2.7.0 a minimális támogatott verzió. Ha az Unity 2020-et használja, az ASA SDK 2.9.0 a támogatott verzió.

Az Azure térbeli horgonyok Unity-ban való használatához le kell töltenie az alapcsomagot, valamint egy platform-specifikus csomagot minden olyan platformhoz (Android/iOS/HoloLens), amelyet támogatni kíván.

| Platform | Csomag neve                                    |
|----------|-------------------------------------------------|
| Android/iOS/HoloLens  | com. microsoft. Azure. térbeli – horgonyok – sdk.core@ <version_number> |
| Android  | com. microsoft. Azure. térbeli – horgonyok – sdk.android@ <version_number> |
| iOS      | com. microsoft. Azure. térbeli – horgonyok – sdk.ios@ <version_number>     |
| HoloLens | com. microsoft. Azure. térbeli – horgonyok – sdk.windows@ <version_number> |

# <a name="download-with-web-browser"></a>[Letöltés böngészővel](#tab/unity-package-web-ui)

Keresse meg az Azure térbeli horgonyok alapcsomagját az egységhez [itt](https://aka.ms/aoa/unity-sdk/package). Válassza ki a kívánt verziót, majd töltse le a csomagot a **Letöltés** gomb használatával. Ezt a lépést megismételve letöltheti a csomagot minden olyan platformon, amelyet támogatni kíván.

# <a name="download-with-npm"></a>[Letöltés a NPM](#tab/unity-package-npm)

Ehhez a lépéshez a <a href="https://www.npmjs.com/get-npm" target="_blank">NPM</a> telepítése és elérhetővé tétele szükséges.

Futtassa az alábbi parancsot az `<version_number>` aktuális mappába letölteni kívánt Azure térbeli horgonyok verziójának lecserélése után:

```bash
npm pack com.microsoft.azure.spatial-anchors-sdk.core@<version_number> --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry/
```

> [!NOTE]
> Az Azure térbeli horgonyok csomag elérhető verzióinak listázásához futtassa a következőt:
>
> ```bash
> npm view com.microsoft.azure.spatial-anchors-sdk.core --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry/ versions
> ```

Az Azure térbeli horgonyok alapcsomag le lesz töltve arra a mappába, ahová a parancsot futtatta. Ezt a lépést megismételve letöltheti a csomagot minden olyan platformon, amelyet támogatni kíván.

# <a name="install-with-mixed-reality-feature-tool-beta"></a>[Telepítés vegyes valóság funkciós eszközzel (bétaverzió)](#tab/unity-package-mixed-reality-feature-tool)

Folytassa a következő lépéssel. A <a a href="/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool" target="_blank">vegyes valóság funkció eszközt</a> egy későbbi lépésben fogja használni.

---