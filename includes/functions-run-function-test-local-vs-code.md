---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2021
ms.author: glenga
ms.openlocfilehash: 5bc1d2ddb5b9ddb185a8349fb42d5f5db79c1498
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103201384"
---
## <a name="run-the-function-locally"></a>A függvény helyi futtatása

A Visual Studio Code integrálható [Azure functions alapeszközökkel](../articles/azure-functions/functions-run-local.md) , hogy a projektet a helyi fejlesztési számítógépen futtassa, mielőtt közzéteszi az Azure-ban.

1. A függvény meghívásához nyomja le az <kbd>F5</kbd> billentyűt a Function app projekt elindításához. A Core Tools kimenete a **Terminal** (Terminál) panelen jelenik meg. Az alkalmazás elindul a **terminál** paneljén. Megtekintheti a helyileg futtatott HTTP-triggerű függvény URL-végpontját.

    ![Helyi függvény VS Code kimenet](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

    Ha a Windowsban nem fut a probléma, győződjön meg arról, hogy a Visual Studio Code alapértelmezett terminálja nem **WSL bash**.

1. Az alapszintű eszközök futtatásával nyissa meg az **Azure: functions** területen. A **függvények** területen bontsa ki a **helyi Project**  >  **functions** elemet. Kattintson a jobb gombbal a (Windows) vagy a <kbd>CTRL-</kbd> click (MacOS) elemre `HttpExample` , és válassza a **függvény végrehajtása most..**. lehetőséget.

    :::image type="content" source="media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="A függvény végrehajtása most a Visual Studio Code-ból":::
    
1. Az **írja be a kérelem törzsében** megjelenik a kérelem üzenet törzsének értéke `{ "name": "Azure" }` . Nyomja le az ENTER billentyűt a kérelem üzenetének a függvénynek való elküldéséhez. 

   Ehelyett HTTP GET-kérést küldhet <http://localhost:7071/api/HttpExample> egy webböngészőben található címre.

1. Ha a függvény helyileg fut, és egy választ ad vissza, a Visual Studio Code-ban megjelenik egy értesítés. A függvény végrehajtásával kapcsolatos információk a **Terminal** panelen jelennek meg.

1. Nyomja le a <kbd>CTRL + C</kbd> billentyűkombinációt az alapvető eszközök leállításához és a hibakereső leválasztásához.
