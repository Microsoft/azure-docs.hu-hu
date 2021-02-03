---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2021
ms.author: glenga
ms.openlocfilehash: 4b15fec0f22db740bbd7c24fcc0acf2ad1a2d1cd
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493482"
---
## <a name="run-the-function-in-azure"></a>A függvény futtatása az Azure-ban

1. Vissza az **Azure: functions** területen az oldalsó sávban bontsa ki a **helyi Project**  >  **functions** elemet. Kattintson a jobb gombbal a (Windows) vagy a <kbd>CTRL-</kbd> click (MacOS) elemre `HttpExample` , és válassza a **függvény végrehajtása most..**. lehetőséget.

    :::image type="content" source="media/functions-vs-code-run-remote/execute-function-now.png" alt-text="Az Azure Visual Studio Code-ból való végrehajtása most":::

1. Az **írja be a kérelem törzsében** megjelenik a kérelem üzenet törzsének értéke `{ "name": "Azure" }` . Nyomja le az ENTER billentyűt a kérelem üzenetének a függvénynek való elküldéséhez.  

1. Amikor a függvény végrehajtja az Azure-ban, és választ ad vissza, a Visual Studio Code-ban megjelenik egy értesítés.
