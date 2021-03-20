---
ms.openlocfilehash: 766dd13f58268c044435a22fb30c1de816d4d151
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97532008"
---
A mintakód futtatásához kövesse az alábbi lépéseket:

1. A Visual Studio Code-ban nyissa meg a **bővítmények** lapot (vagy nyomja le a CTRL + SHIFT + X billentyűkombinációt), és keressen rá az Azure IoT hubra.
1. Kattintson a jobb gombbal, és válassza a **bővítmény beállításai** lehetőséget.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/extensions-tab.png" alt-text="Bővítmény beállításai":::
1. Keresse meg és engedélyezze a "részletes üzenet megjelenítése" lehetőséget.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/show-verbose-message.png" alt-text="Részletes üzenet megjelenítése":::
1. A Visual Studio Code-ban lépjen a *src/Cloud-to-Device-Console-app/operations.js* elemre.
1. A **GraphTopologySet** csomóponton ellenőrizze, hogy a következő érték jelenik-e meg:

    `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/motion-detection/2.0/topology.json"`
1. A **GraphInstanceSet** és a **GraphTopologyDelete**  csomóponton győződjön meg arról, hogy a `topologyName` `name` Graph-topológia tulajdonságának értéke megegyezik a következővel:

    `"topologyName" : "MotionDetection"`
    
1. Indítsa el a hibakeresési munkamenetet az F5 billentyű kiválasztásával. A **terminál** ablakban néhány üzenet jelenik meg.
1. A fájl *operations.js* a és a hívásával elindul `GraphTopologyList` `GraphInstanceList` . Ha az előző rövid útmutatók befejezése után törölte az erőforrásokat, akkor ez a folyamat üres listát ad vissza, majd szünetelteti. A folytatáshoz válassza az ENTER billentyűt.

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
     * `GraphTopologySet`Az előzőt használó hívás`topologyUrl`
     * A `GraphInstanceSet` következő törzset használó hívás:
         
    ```
    {
      "@apiVersion": "2.0",
      "name": "Sample-Graph",
      "properties": {
        "topologyName": "MotionDetection",
        "description": "Sample graph description",
        "parameters": [
          {
            "name": "rtspUrl",
            "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
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
1. A **terminál** ablakban lévő kimenet a következő időpontban szünetel: `Press Enter to continue` . Ne jelölje be az ENTER billentyűt. Görgessen felfelé, és tekintse meg a meghívott közvetlen metódusok JSON-válaszának hasznos adatait.
1. Váltson a **kimeneti** ablakra a Visual Studio Code-ban. Láthatja, hogy az élő videó Analytics IoT Edge-modulban az IoT hub-ra küld üzenetet. A rövid útmutató következő szakasza ezeket az üzeneteket tárgyalja.
1. A Media Graph továbbra is fut, és kinyomtatja az eredményeket. Az RTSP-szimulátor megtartja a forrás videóját. A Media Graph leállításához térjen vissza a **terminál** ablakába, és válassza az ENTER billentyűt. 

    A hívások következő sorozata törli az erőforrásokat:

    * A Graph- `GraphInstanceDeactivate` példány inaktiválására irányuló hívás.
    * A `GraphInstanceDelete` példány törlésére irányuló hívás.
    * A `GraphTopologyDelete` topológia törlésére irányuló hívás.
    * Egy végső hívás, `GraphTopologyList` amely azt jelzi, hogy a lista üres.
