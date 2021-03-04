---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 02/18/2021
ms.author: crtreasu
ms.openlocfilehash: 4345810292896cf88de19baf419eee025ba5853f
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102044795"
---
A következő lépés az Azure Object-horgonyok csomagjának letöltése az Unity számára.

# <a name="download-with-web-browser"></a>[Letöltés böngészővel](#tab/unity-package-web-ui)

[Itt](https://aka.ms/aoa/unity-sdk/package)megkeresheti az egységhez tartozó Azure Object-horgonyokat tartalmazó csomagot. Válassza ki a kívánt verziót, majd töltse le a csomagot a **Letöltés** gomb használatával.

# <a name="download-with-npm"></a>[Letöltés a NPM](#tab/unity-package-npm)

Ehhez a lépéshez a <a href="https://www.npmjs.com/get-npm" target="_blank">NPM</a> telepítése és elérhetővé tétele szükséges.

Futtassa a következő parancsot `<version_number>` , és cserélje le a letölteni kívánt Azure Object-horgonyok verziójára:

```bash
npm pack com.microsoft.azure.object-anchors.runtime@<version_number> --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry/
```

> [!NOTE]
> Az Azure Object Anchors csomag elérhető verzióinak listázásához futtassa a következőt:
>
> ```bash
> npm view com.microsoft.azure.object-anchors.runtime --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry/ versions
> ```

Az Azure Object-horgonyok csomagja le lesz töltve arra a mappába, ahová a parancsot futtatta.

# <a name="install-with-mixed-reality-feature-tool-beta"></a>[Telepítés vegyes valóság funkciós eszközzel (bétaverzió)](#tab/unity-package-mixed-reality-feature-tool)

Folytassa a következő lépéssel. A <a a href="https://aka.ms/MRFeatureToolDocs" target="_blank">vegyes valóság funkció eszközt</a> egy későbbi lépésben fogja használni.

---
