---
title: A Unity Remote Rendering-csomagjának telepítése
description: Elmagyarázza, hogyan telepítheti a távoli renderelési ügyféloldali DLL-eket az egységhez
author: florianborn71
ms.author: flborn
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: bfb383a7079e98db1db1f9b5077558c187bcea96
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105047728"
---
# <a name="install-the-remote-rendering-package-for-unity"></a>A Unity Remote Rendering-csomagjának telepítése

Az Azure távoli renderelés egy Unity-csomagot használ az integráció egységbe való beágyazásához.
Ez a csomag tartalmazza a teljes C# API-t, valamint az Azure Remote rendering és az Unity használatával szükséges összes beépülő modult.
A csomagok esetében az egység elnevezési sémája a következő: **com. microsoft. Azure. Remote-rendering**.

A Unity csomag telepítéséhez a következő lehetőségek közül választhat:

## <a name="install-remote-rendering-package-using-the-mixed-reality-feature-tool"></a>Távoli renderelési csomag telepítése a kevert valóság funkció eszköz használatával

[A vegyes valóság funkciós eszköz](/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool) ([Letöltés](https://aka.ms/mrfeaturetool)) egy olyan eszköz, amellyel a vegyes valóság funkcióinak egységes projektjeibe integrálhatók. A csomag nem része az [ARR Samples adattárnak](https://github.com/Azure/azure-remote-rendering), és nem érhető el az egység belső csomagjának beállításjegyzékében.

A csomag egy projekthez való hozzáadásához a következőket kell tennie:
1. [A vegyes valóság funkció eszköz letöltése](https://aka.ms/mrfeaturetool)
1. Kövesse az eszköz használatának [teljes utasításait](/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool) .
1. A **szolgáltatások felderítése** lapon jelölje be a **Microsoft Azure távoli renderelési** csomaghoz tartozó jelölőnégyzetet, és válassza ki a projekthez hozzáadni kívánt csomag verzióját.

![Mixed_Reality_feature_tool_package](media/mixed-reality-feature-tool-package.png)

A helyi csomag frissítéséhez csak válasszon egy újabb verziót a vegyes valóság funkció eszközről, és telepítse. Előfordulhat, hogy a csomag frissítése időnként konzol hibáihoz vezethet. Ha ez történik, próbálkozzon a projekt bezárásával és újbóli megnyitásával.

## <a name="install-remote-rendering-package-manually"></a>Távoli renderelési csomag manuális telepítése

A távoli renderelési csomag manuális telepítéséhez a következőket kell tennie:

1. Töltse le a csomagot a vegyes valóság csomagjai NPM-csatornán `https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry` .
    * Használhatja a [NPM](https://www.npmjs.com/get-npm) , és a következő parancs futtatásával letöltheti a csomagot az aktuális mappába.
      ```
      npm pack com.microsoft.azure.remote-rendering --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry
      ```

    * Vagy használhatja a PowerShell-szkriptet `Scripts/DownloadUnityPackages.ps1` az [Azure-Remote-rendering GitHub-adattárból](https://github.com/Azure/azure-remote-rendering).
        * A tartalmának szerkesztése `Scripts/unity_sample_dependencies.json`
          ```json
          {
            "packages": [
              {
                "name": "com.microsoft.azure.remote-rendering", 
                "version": "latest", 
                "registry": "https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry"
              }
            ]
          }
          ```

        * Futtassa a következő parancsot a PowerShellben a csomag a megadott célhelyre való letöltéséhez.
          ```
          DownloadUnityPackages.ps1 -DownloadDestDir <destination directory>
          ```

1. [Telepítse a letöltött csomagot](https://docs.unity3d.com/Manual/upm-ui-tarball.html) az Unity csomagkezelő segítségével.

A helyi csomag frissítéséhez csak futtassa újra a megfelelő parancsot, és importálja újra a csomagot. Előfordulhat, hogy a csomag frissítése időnként konzol hibáihoz vezethet. Ha ez történik, próbálkozzon a projekt bezárásával és újbóli megnyitásával.

## <a name="unity-render-pipelines"></a>Unity renderelési folyamatok

A távoli renderelés a és a együttesével is működik **:::no-loc text="Universal render pipeline":::** **:::no-loc text="Standard render pipeline":::** . A teljesítménnyel kapcsolatos okokból az univerzális renderelési folyamat ajánlott.

A használatához a **:::no-loc text="Universal render pipeline":::** csomagját az Unity-ben kell telepíteni. Ezt az egység **csomagkezelő** felhasználói felületén (a csomag neve **univerzális RP**, a 7.3.1-es vagy újabb verzióban) vagy a fájlon keresztül teheti meg, az `Packages/manifest.json` [Unity Project Setup oktatóanyagban](../../tutorials/unity/view-remote-models/view-remote-models.md#include-the-azure-remote-rendering-package)leírtak szerint.

## <a name="next-steps"></a>Következő lépések

* [Unity game Objects és Components](objects-components.md)
* [Oktatóanyag: távoli modellek megtekintése](../../tutorials/unity/view-remote-models/view-remote-models.md)