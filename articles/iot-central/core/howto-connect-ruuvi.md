---
title: RuuviTag összekötése az Azure IoT Centralban | Microsoft Docs
description: Ismerje meg, hogyan csatlakoztatható RuuviTag környezeti érzékelő a IoT Central alkalmazáshoz.
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 11/27/2019
ms.openlocfilehash: 7cf0d31895eaeaa41c23f9f07664b27bed1d3126
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96006387"
---
# <a name="connect-a-ruuvitag-sensor-to-your-azure-iot-central-application"></a>RuuviTag-érzékelő összekötése az Azure IoT Central-alkalmazással

*Ez a cikk a megoldás-építők esetében érvényes.*

Ez a cikk azt ismerteti, hogyan csatlakozhat egy RuuviTag-érzékelőt a Microsoft Azure IoT Central alkalmazáshoz.

Mi az a ruuvi címke?

A RuuviTag egy fejlett, nyílt forráskódú érzékelős irányjelző platform, amely az üzleti ügyfelek, a fejlesztők, a döntéshozók, a tanulók és a Hobbik igényeinek kielégítésére szolgál. Az eszköz úgy van beállítva, hogy a lehető leghamarabb működjön, és készen áll arra, hogy üzembe helyezze, ahol szüksége van rá. A Bluetooth LE Beacon egy beépített környezeti érzékelővel és gyorsulásmérővel rendelkezik.

A RuuviTag kommunikál a kapcsolaton keresztül (Bluetooth alacsony energia), és egy átjárót igényel az Azure IoT Central-vel való kommunikációhoz. Győződjön meg arról, hogy van egy átjáró-eszköz, például a Rigado Cascade 500, amely lehetővé teszi, hogy egy RuuviTag csatlakozhasson a IoT Centralhoz.

Kövesse az [itt található utasításokat](./howto-connect-rigado-cascade-500.md) , ha Rigado Cascade 500 Gateway-eszközt szeretne beállítani.

## <a name="prerequisites"></a>Előfeltételek

A RuuviTag-érzékelők összekapcsolásához a következő erőforrásokra van szükség:

* Egy RuuviTag-érzékelő. További információért látogasson el a [RuuviTag](https://ruuvi.com/)webhelyre.
* Egy Rigado lépcsőzetes 500-eszköz vagy egy másik-átjáró. További információért látogasson el a [Rigado](https://www.rigado.com/)webhelyre.
* Azure IoT Central-alkalmazás. További információt az [új alkalmazás létrehozása](./quick-deploy-iot-central.md)című témakörben talál.

## <a name="add-a-ruuvitag-device-template"></a>RuuviTag-eszköz sablonjának hozzáadása

Ahhoz, hogy egy RuuviTag-érzékelőt beillesszen az Azure IoT Central Application-példányba, konfigurálnia kell egy megfelelő sablont az alkalmazáson belül.

RuuviTag-sablon hozzáadása:

1. Navigáljon a bal oldali ablaktáblán a ***Device templates** _ lapra, és válassza a _* + új * *: ![ új sablon létrehozása lehetőséget, amely ](./media/howto-connect-ruuvi/devicetemplate-new.png) lehetővé teszi, hogy ***egyéni sablon létrehozása**_ vagy _ *_konfigurált eszköz használata_**
1. Válassza ki a RuuviTag az előre konfigurált eszközök listájából az alább látható módon:  ![ válassza a RuuviTag-eszköz sablonja lehetőséget.](./media/howto-connect-ruuvi/devicetemplate-preconfigured.png)
1. A ***Tovább gombra*** kattintva folytassa a következő lépéssel.
1. A következő képernyőn válassza a ***Létrehozás*** elemet a C500-eszköz sablonjának a IoT Central alkalmazásba való beléptetéséhez.

## <a name="connect-a-ruuvitag-sensor"></a>RuuviTag-érzékelő összekötése

Amint azt korábban említettük, a RuuviTag és a IoT Central alkalmazás összekapcsolásához be kell állítania egy átjáró-eszközt. Az alábbi lépések feltételezik, hogy beállított egy Rigado Cascade 500 Gateway-eszközt.  

1. Kapcsolja be a Rigado lépcsőzetes 500-eszközét, és csatlakoztatja a hálózati kapcsolathoz (Etherneten vagy vezeték nélküli hálózaton keresztül)
1. A fedél kikapcsolásával kikapcsolhatja a RuuviTag, és lekérheti a műanyag lapot az akkumulátorral való kapcsolódás biztonságossá tételéhez.
1. Helyezze a RuuviTag egy olyan Rigado kaszkád 500-átjáróra, amely már konfigurálva van a IoT Central alkalmazásban.
1. Néhány másodperc elteltével a RuuviTag meg kell jelennie az eszközök listájában a IoT Centralon belül.  
    ![RuuviTag-eszközök listája](./media/howto-connect-ruuvi/ruuvi-devicelist.png)

Mostantól használhatja ezt a RuuviTag a IoT Central alkalmazáson belül.  

## <a name="create-a-simulated-ruuvitag"></a>Szimulált RuuviTag létrehozása

Ha nem rendelkezik fizikai RuuviTag eszközzel, létrehozhat egy szimulált RuuviTag-érzékelőt, amelyet az Azure IoT Central alkalmazásban való teszteléshez használhat.

Szimulált RuuviTag létrehozása:

1. Válassza az **eszközök > RuuviTag** elemet.
1. Válassza a **+ Új** lehetőséget.
1. Adjon meg egy egyedi **azonosítót** és egy felhasználóbarát **eszköznév**.  
1. Engedélyezze a **szimulált** beállítást.
1. Válassza a **Létrehozás** lehetőséget.  

## <a name="next-steps"></a>Következő lépések

Ha Ön egy eszköz fejlesztője, néhány javasolt lépés a következő:

- További információ [az eszközök kapcsolatáról az Azure IoT Central](./concepts-get-connected.md)
- Ismerje meg, hogyan [figyelheti az eszközök kapcsolatát az Azure CLI használatával](./howto-monitor-devices-azure-cli.md)
