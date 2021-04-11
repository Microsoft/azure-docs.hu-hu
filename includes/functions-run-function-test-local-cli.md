---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: d400533039ea74a878cb8e543c22de02ee77e4f5
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106282955"
---
## <a name="run-the-function-locally"></a>A függvény helyi futtatása

1. A függvény futtatásához indítsa el a helyi Azure Functions Runtime-gazdagépet a *LocalFunctionProj* mappából:

    ```
    func start
    ```

    A kimenet vége felé a következő soroknak kell megjelenniük: 
    
    <pre>
    ...
    
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    Http Functions:
    
            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    
    </pre>
    
    >[!NOTE]  
    > Ha a HttpExample nem jelenik meg a fentiekben látható módon, valószínűleg elindította a gazdagépet a projekt gyökérkönyvtárán kívülről. Ebben az esetben a **CTRL** + **C** billentyűkombinációval állítsa le a gazdagépet, navigáljon a projekt gyökérmappa mappájához, és futtassa újra az előző parancsot.

1. Másolja a függvény URL-címét `HttpExample` ebből a kimenetből egy böngészőbe, és fűzze hozzá a lekérdezési karakterláncot `?name=<YOUR_NAME>` , így a teljes URL-címet, például: `http://localhost:7071/api/HttpExample?name=Functions` . A böngészőnek egy válaszüzenetet kell megjelenítenie, amely visszhangot jelez a lekérdezési karakterlánc értékét. A terminál, amelyben elindította a projektet, a naplók kimenetét is megjeleníti a kérések elkészítésekor.

1. Ha elkészült, használja a **CTRL C billentyűt**, +  és válassza `y` a functions gazdagép leállítását.
