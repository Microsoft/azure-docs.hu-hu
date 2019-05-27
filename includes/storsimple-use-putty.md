---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: a5c62c67cd401c043352b06e6e6070a7fc0f1296
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66118291"
---
#### <a name="to-connect-through-the-serial-console"></a>Csatlakozás soros konzolon keresztül
1. Csatlakoztassa a soros kábelt az eszközhöz (közvetlenül vagy egy USB–soros adapteren keresztül).
2. Nyissa meg a **Vezérlőpultot**, majd az **Eszközkezelőt**.
3. Keresse meg a COM-portot az alábbi ábrának megfelelően.
   
     ![Csatlakozás soros konzolon keresztül](./media/storsimple-use-putty/HCS_ConnectingDeviceS-include.png)
4. Indítsa el a PuTTY alkalmazást. 
5. A jobb oldali panelen állítsa a **Kapcsolat típusa** beállítást **Soros** értékre.
6. A jobb oldali panelen írja be a megfelelő COM-portot. Ügyeljen arra, hogy a soros konfiguráció paraméterei az alábbiaknak megfelelők legyenek:
   
   * Sebesség: 115,200
   * Adatbitek: 8
   * Stopbitek: 1.
   * Paritás: Egyik sem
   * Átvitelvezérlés: Egyik sem
     
     Ezek a beállítások az alábbi ábrán láthatók.
     
     ![PuTTY-beállítások](./media/storsimple-use-putty/HCS_PuttyConfig-include.png) 
     
     > [!NOTE]
     > Ha az alapértelmezett folyamatvezérlési beállítás nem működik, próbálja meg átállítani XON/XOFF értékre.
     > 
     > 
7. Soros munkamenet indításához kattintson a **Megnyitás** elemre.

