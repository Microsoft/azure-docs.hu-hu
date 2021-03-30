---
title: fájl belefoglalása
description: fájl belefoglalása
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 10/10/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 0fc6a62a94f31617209bcc60bfaa95bc8927551a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86050490"
---
## <a name="deploy-event-grid-iot-edge-module"></a>Event Grid IoT Edge modul üzembe helyezése

A modulokat többféleképpen is telepítheti egy IoT Edge eszközre, és mindegyik a IoT Edge Azure Event Grid. Ez a cikk azokat a lépéseket ismerteti, amelyekkel telepítheti a Event Gridt a Azure Portal IoT Edge.

>[!NOTE]
> Ebben az oktatóanyagban a Event Grid-modult az adatmegőrzés nélkül fogja telepíteni. Ez azt jelenti, hogy a jelen oktatóanyagban létrehozott összes témakört és előfizetést a modul újbóli üzembe helyezésekor törli a rendszer. Az adatmegőrzés beállításával kapcsolatos további információkért tekintse meg a következő cikkeket: állapot megtartása [Linuxon](../articles/event-grid/edge/persist-state-linux.md) vagy [az állapot megőrzése a Windowsban](../articles/event-grid/edge/persist-state-windows.md). Éles számítási feladatokhoz javasoljuk, hogy az Event Grid modult az adatmegőrzéssel telepítse.

>[!IMPORTANT]
> Ebben az oktatóanyagban Event Grid modult kell telepíteni az ügyfél-hitelesítéssel, és engedélyezni kell a HTTP-előfizetőket. Éles számítási feladatokhoz javasoljuk, hogy csak HTTPS-kérelmeket és-előfizetőket engedélyezzen engedélyezett ügyfél-hitelesítéssel. A Event Grid modul biztonságos konfigurálásával kapcsolatos további információkért lásd: [Biztonság és hitelesítés](../articles/event-grid/edge/security-authentication.md).
 
### <a name="select-your-iot-edge-device"></a>IoT Edge eszköz kiválasztása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com)
1. Navigáljon a IoT Hub.
1. Válassza a **IoT Edge** lehetőséget az **automatikus eszközkezelés** szakasz menüjében. 
1. Kattintson a céleszköz AZONOSÍTÓjának az eszközök listájáról
1. Válassza a **modulok beállítása** lehetőséget. Tartsa meg a lapot. A következő szakaszban ismertetett lépésekkel folytathatja a lépéseket.

### <a name="configure-a-deployment-manifest"></a>Központi telepítési jegyzék konfigurálása

Az üzembe helyezési jegyzék egy JSON-dokumentum, amely leírja, hogy mely modulokat kell telepíteni, hogyan zajlik az adatforgalom a modulok és a modul kívánt tulajdonságai között. A Azure Portal tartalmaz egy varázslót, amely végigvezeti az üzembe helyezési jegyzék létrehozásán, a JSON-dokumentum manuális létrehozása helyett.  Három lépésből áll: **modulok hozzáadása**, **útvonalak megadása** és az **üzembe helyezés áttekintése**.

### <a name="add-modules"></a>Modulok hozzáadása

1. A **telepítési modulok** szakaszban válassza a **Hozzáadás** lehetőséget.
1. A legördülő listában válassza ki a modulok típusait **IoT Edge modult**
1. Adja meg a tároló nevét, képét, tároló-létrehozási beállításait:

[!INCLUDE [event-grid-edge-module-version-update](event-grid-edge-module-version-update.md)]

   * **Név**: eventgridmodule
   * **Rendszerkép URI-ja**: `mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **Tároló-létrehozási beállítások**:

```json
    {
      "Env": [
        "inbound__clientAuth:clientCert__enabled=false",
        "outbound__webhook__httpsOnly=false"
      ],
      "HostConfig": {
        "PortBindings": {
          "4438/tcp": [
            {
              "HostPort": "4438"
            }
          ]
        }
      }
    }
```

 1. Kattintson a **Mentés** gombra.
 1. Az útvonalak szakasz folytatásához kattintson a **tovább** gombra.

    > [!NOTE]
    > Ha Azure-beli virtuális gépet használ peremhálózati eszközként, vegyen fel egy bejövő portszabály, hogy engedélyezze a bejövő forgalmat a 4438-es porton. A szabály hozzáadásával kapcsolatos útmutatásért lásd: [portok megnyitása virtuális géphez](../articles/virtual-machines/windows/nsg-quickstart-portal.md).


### <a name="setup-routes"></a>Telepítési útvonalak

 Tartsa meg az alapértelmezett útvonalakat, és kattintson a **tovább** gombra a felülvizsgálati szakasz folytatásához.

### <a name="review-deployment"></a>Központi telepítés áttekintése

1. A felülvizsgálati szakasz megjeleníti a JSON üzembe helyezési jegyzéket, amelyet az előző két szakaszban megadott beállítások alapján hoztak létre. Győződjön meg arról, hogy a listában a két modul látható: **$edgeAgent** és **$edgeHub**. Ez a két modul hozza létre a IoT Edge futtatókörnyezetet, és minden központi telepítés esetében kötelező alapértelmezett érték.
1. Tekintse át az üzembe helyezési adatokat, majd válassza a **Küldés** lehetőséget.

### <a name="verify-your-deployment"></a>Az üzemelő példány ellenőrzése

1. Miután elküldte az üzembe helyezést, térjen vissza az IoT hub IoT Edge lapjára.
1. Válassza ki azt a **IoT Edge eszközt** , amelyet a központi telepítéshez céloz, hogy megnyissa a részleteit.
1. Az eszköz részletei között ellenőrizze, hogy a Event Grid modul szerepel-e a **telepítésben** , és az **eszköz által jelentett** módon van-e megjelölve.

Néhány percet is igénybe vehet, amíg a modul elindult az eszközön, majd visszaküldhető a IoT Hubra. Frissítse az oldalt, és tekintse meg a frissített állapotot.