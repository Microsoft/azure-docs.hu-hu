---
author: russell-cooks
ms.service: media-services
ms.topic: include
ms.date: 10/05/2020
ms.author: russellcooks
ms.openlocfilehash: 5dd823c618a0d49bef29c473e6c293762b859149
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94358241"
---
1. A Visual Studio Code-ban keresse meg az src/Edge lehetőséget. A létrehozott. env fájlt a telepítési sablonok néhány fájlja tartalmazza.

    A központi telepítési sablon a peremhálózati eszköz központi telepítési jegyzékére hivatkozik néhány helyőrző értékkel. A. env fájl ezen változók értékeit tartalmazhatja.
1. Ezután keresse meg a src/Cloud-to-Device-Console-app mappát. Itt láthatja a appsettings.jsa létrehozott fájlon, néhány további fájllal együtt:

   * operations.json – ez a fájl felsorolja a program által futtatandó különböző műveleteket.
   * main.py – ez a mintakód, amely a következő műveleteket végzi el:
    
        * Betölti az alkalmazás beállításait.
        * Meghívja az élő videó-elemzést IoT Edge modul közvetlen metódusait a topológia létrehozásához, a gráf létrehozásához és a gráf aktiválásához.
        * Szünetelteti a diagram kimenetének vizsgálatát a **terminál** ablakban, valamint az IoT hub számára a **kimeneti** ablakban elküldett eseményeket.
        * Inaktiválja a Graph-példányt, törli a Graph-példányt, és törli a Graph-topológiát.
