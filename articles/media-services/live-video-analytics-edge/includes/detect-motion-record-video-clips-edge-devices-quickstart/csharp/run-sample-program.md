---
ms.openlocfilehash: c99d2489efe7c46b8d50b08861fcbbcd6f8a1966
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97531820"
---
1. A Visual Studio Code-ban nyissa meg a **bővítmények** lapot (vagy nyomja le a CTRL + SHIFT + X billentyűkombinációt), és keressen rá az Azure IoT hubra.
1. Kattintson a jobb gombbal, és válassza a **bővítmény beállításai** lehetőséget.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/extensions-tab.png" alt-text="Bővítmény beállításai":::
1. Keresse meg és engedélyezze a "részletes üzenet megjelenítése" lehetőséget.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/show-verbose-message.png" alt-text="Részletes üzenet megjelenítése":::
1. Indítsa el a hibakeresési munkamenetet az F5 billentyű kiválasztásával. A **Terminálablak** kinyomtat néhány üzenetet.
1. A kód *operations.js* a közvetlen metódusokat hívja `GraphTopologyList` meg `GraphInstanceList` . Ha az előző rövid útmutatók után törölte az erőforrásokat, akkor ez a folyamat üres listát ad vissza, majd szünetelteti. Nyomja meg az Enter billentyűt.
    
    ```
    --------------------------------------------------------------------------
    Executing operation GraphTopologyList
    -----------------------  Request: GraphTopologyList  --------------------------------------------------
    {
      "@apiVersion": "2.0"
    }
    ---------------  Response: GraphTopologyList - Status: 200  ---------------
    {
      "value": []
    }
    --------------------------------------------------------------------------
    Executing operation WaitForInput
    Press Enter to continue
    ```
  
  A **terminál** ablak a közvetlen metódusok következő készletét jeleníti meg:  
  
  * A következőt `GraphTopologySet` használó hívás `topologyUrl` 
  * A `GraphInstanceSet` következő törzset használó hívás:
  
  ```
  {
    "@apiVersion": "2.0",
    "name": "Sample-Graph",
    "properties": {
      "topologyName": "EVRToFilesOnMotionDetection",
      "description": "Sample graph description",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/lots_015.mkv"
        },
        {
          "name": "rtspUserName",
          "value": "testuser"
        },
        {
          "name": "rtspPassword",
          "value": "testpassword"
        }
      ]
    }
  }
  ```
    
  * Egy hívás, `GraphInstanceActivate` amely elindítja a Graph-példányt és a videó áramlását.
  * Egy második hívás, `GraphInstanceList` amely azt mutatja, hogy a Graph-példány fut állapotban van.
1. A **terminál** ablakban lévő kimenet a következő időpontban szünetel: `Press Enter to continue` . Ne jelölje be az ENTER billentyűt. Görgetéssel tekintse meg a meghívott közvetlen metódusok JSON-válaszának hasznos adatait.
1. Váltson a **kimeneti** ablakra a Visual Studio Code-ban. Láthatja, hogy az élő videó Analytics IoT Edge-modulon az IoT hub-ra küld üzenetet. A rövid útmutató következő szakasza ezeket az üzeneteket tárgyalja.
1. A Media Graph továbbra is fut, és kinyomtatja az eredményeket. Az RTSP-szimulátor megtartja a forrás videóját. A Media Graph leállításához térjen vissza a **terminál** ablakába, és válassza az ENTER billentyűt. 

    A következő hívási sorozat megtisztítja az erőforrásokat:

    * A Graph- `GraphInstanceDeactivate` példány inaktiválására irányuló hívás.
    * A `GraphInstanceDelete` példány törlésére irányuló hívás.
    * A `GraphTopologyDelete` topológia törlésére irányuló hívás.
    * A végső hívás `GraphTopologyList` azt mutatja, hogy a lista már üres.
