---
title: 'Gyors útmutató: HoloLens-alkalmazás létrehozása Unity'
description: Ebből a rövid útmutatóból megtudhatja, hogyan építhet ki egy HoloLens Unity-alkalmazást az objektum-horgonyok használatával.
author: craigktreasure
manager: virivera
ms.author: crtreasu
ms.date: 03/02/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: 4f85a258042430d58690ef578db6d21a6c831d50
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102044813"
---
# <a name="quickstart-create-a-hololens-app-with-azure-object-anchors-in-unity"></a>Gyors útmutató: HoloLens-alkalmazás létrehozása Azure Object-Horgonyokkal, Unity

Ebben a rövid útmutatóban létrehoz egy Unity HoloLens-alkalmazást, amely az [Azure Object-horgonyokat](../overview.md)használja. Az Azure Object-horgonyok egy felügyelt felhőalapú szolgáltatás, amely a 3D-eszközöket olyan AI-modellekre alakítja át, amelyek lehetővé teszik az objektum-Aware vegyes valóságot a HoloLens. Ha elkészült, egy olyan egységgel rendelkező HoloLens-alkalmazás fog rendelkezni, amely képes a fizikai világ objektumainak észlelésére.

A következőket fogja megtanulni:

> [!div class="checklist"]
> * Készítse elő az egység létrehozási beállításait.
> * Exportálja a HoloLens Visual Studio-projektet.
> * Telepítse az alkalmazást, és futtassa egy HoloLens 2 eszközön.

[!INCLUDE [Unity quickstart prerequisites](../../../includes/object-anchors-quickstart-unity-prerequisites.md)]

[!INCLUDE [Unity device setup](../../../includes/object-anchors-quickstart-unity-device-setup.md)]

## <a name="open-the-sample-project"></a>A minta projekt megnyitása

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

[!INCLUDE [Download Unity Package](../../../includes/object-anchors-quickstart-unity-download-package.md)]

Az egység területen nyissa meg a `quickstarts/apps/unity/basic` projektet.

[!INCLUDE [Import Unity Package](../../../includes/object-anchors-quickstart-unity-import-package.md)]

[!INCLUDE [Unity build sample scene 1](../../../includes/object-anchors-quickstart-unity-build-sample-scene-1.md)]

[!INCLUDE [Unity build sample scene 2](../../../includes/object-anchors-quickstart-unity-build-sample-scene-2.md)]

[!INCLUDE [Unity build and deploy](../../../includes/object-anchors-quickstart-unity-build-deploy.md)]

### <a name="run-the-sample-app"></a>A mintaalkalmazás futtatása

Kapcsolja be az eszközt, válassza a **minden alkalmazás** lehetőséget, majd keresse meg és indítsa el az alkalmazást. Az Unity splash képernyő után megjelenik egy üzenet, amely jelzi, hogy az objektum megfigyelője inicializálva van. A modellt azonban hozzá kell adnia az alkalmazáshoz.

[!INCLUDE [Unity setup Windows Device Portal](../../../includes/object-anchors-quickstart-unity-setup-device-portal.md)]

[!INCLUDE [Unity upload your model](../../../includes/object-anchors-quickstart-unity-upload-model.md)]

Az alkalmazás az aktuális nézetben keresi az objektumokat, majd az észlelés után nyomon követi azokat. A rendszer eltávolítja a példányt, ha 6 méterre van a felhasználó helyétől. A hibakeresési szöveg egy példány részleteit jeleníti meg, például az azonosítót, a frissített időbélyeget és a felületi lefedettségi arányt.

[!INCLUDE [Unity troubleshooting](../../../includes/object-anchors-quickstart-unity-troubleshooting.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Unity HoloLens és MRTK](get-started-unity-hololens-mrtk.md)

> [!div class="nextstepaction"]
> [Fogalmak: az SDK áttekintése](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [Gyakori kérdések](../faq.md)
