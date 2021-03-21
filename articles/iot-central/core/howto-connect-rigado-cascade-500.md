---
title: A Rigado Cascade 500-es verziójának összekötése az Azure IoT Centralban | Microsoft Docs
description: Megtudhatja, hogyan csatlakoztatható a Rigado Cascade 500-átjáró eszköz a IoT Central alkalmazáshoz.
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 11/27/2019
ms.openlocfilehash: 0000e7690ab92f469a7417e82cb375c524e0b343
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96014762"
---
# <a name="connect-a-rigado-cascade-500-gateway-device-to-your-azure-iot-central-application"></a>Rigado Cascade 500-átjáró eszköz csatlakoztatása az Azure IoT Central-alkalmazáshoz

*Ez a cikk a megoldás-építők esetében érvényes.*

Ez a cikk azt ismerteti, hogyan lehet megoldás-szerkesztőként csatlakozni a Rigado Cascade 500 Gateway-eszközhöz a Microsoft Azure IoT Central alkalmazáshoz. 

## <a name="what-is-cascade-500"></a>Mi az a Cascade 500?

A Cascade 500 IoT-átjáró egy olyan Rigado, amely a saját lépcsőzetes peremhálózati megoldás részeként szerepel. Kereskedelmi IoT-projekteket és-termékeket biztosít rugalmas peremhálózati számítási teljesítménnyel, robusztus tárolós alkalmazási környezettel és számos vezeték nélküli eszköz csatlakozási lehetőséggel, beleértve a Bluetooth 5, HTH, & Wi-Fi-t.

A Cascade 500 előzetes tanúsítvánnyal rendelkezik az Azure IoT Plug and Play (előzetes verzió) szolgáltatásban, amely lehetővé teszi a megoldás-építők számára, hogy az eszköz teljes körű megoldásait egyszerűen előkészítse a végfelhasználók számára. A lépcsőzetes átjáró lehetővé teszi, hogy vezeték nélkül kapcsolódjon az átjáró-eszköz közelében lévő különböző feltételi figyelő érzékelőkhöz. Ezek az érzékelők IoT Centralba helyezhetők az átjáró-eszközön keresztül.

## <a name="prerequisites"></a>Előfeltételek
A útmutató lépéseinek elvégzéséhez a következő erőforrásokra van szükség:

* Egy Rigado lépcsőzetes 500-eszköz. További információért látogasson el a [Rigado](https://www.rigado.com/)webhelyre.
* Azure IoT Central-alkalmazás. További információt az [új alkalmazás létrehozása](./quick-deploy-iot-central.md)című témakörben talál.

## <a name="add-a-device-template"></a>Eszközsablon hozzáadása

A Cascade 500 Gateway-eszköz Azure IoT Central Application-példányba való beléptetéséhez konfigurálnia kell egy megfelelő sablont az alkalmazáson belül.

Lépcsőzetes 500-eszköz sablonjának hozzáadása: 

1. Navigáljon a bal oldali ablaktábla ***eszközök sablonok** _ lapjára, majd válassza az _ * + új * *: ![ új sablon létrehozása elemet.](./media/howto-connect-rigado-cascade-500/device-template-new.png)
1. Az oldalon lehetőség van az ***egyéni sablon**(_ vagy _) létrehozására egy *_előre konfigurált eszköz-sablon használatával_**
1. Válassza ki a C500 az előre konfigurált eszközök listájából az alább látható módon: ![ válassza a C500-eszköz sablonja lehetőséget.](./media/howto-connect-rigado-cascade-500/device-template-preconfigured.png)
1. A ***Tovább gombra*** kattintva folytassa a következő lépéssel. 
1. A következő képernyőn válassza a ***Létrehozás*** elemet a C500-eszköz sablonjának a IoT Central alkalmazásba való beléptetéséhez.

## <a name="retrieve-application-connection-details"></a>Alkalmazás-kapcsolat részleteinek beolvasása

Most le kell kérnie az Azure IoT Central-alkalmazás **hatókör-azonosítóját** és **elsődleges kulcsát** a lépcsőzetes 500-eszköz csatlakoztatásához. 

1. Navigáljon a **felügyelet**  elemre a bal oldali ablaktáblán, és kattintson az **eszköz csatlakoztatása** elemre. 
2. Jegyezze fel IoT Central alkalmazás **hatókör-azonosítóját** .
![Alkalmazás-hatókör azonosítója](./media/howto-connect-rigado-cascade-500/app-scope-id.png)
3. Most kattintson a **kulcsok megtekintése** elemre, és jegyezze fel az elsődleges **kulcs** 
 ![ elsődleges kulcsát](./media/howto-connect-rigado-cascade-500/primary-key-sas.png)  

## <a name="contact-rigado-to-connect-the-gateway"></a>Az átjáróhoz való kapcsolódáshoz forduljon a Rigado-hez 

Ahhoz, hogy a Cascade 500 eszközt csatlakoztatni lehessen a IoT Central alkalmazáshoz, kapcsolatba kell lépnie a Rigado, és meg kell adnia a fenti lépések alkalmazás-csatlakozási részleteit. 

Miután az eszköz csatlakozik az internethez, a Rigado leküldheti a konfigurációs frissítést a lépcsőzetes 500 Gateway eszközre egy biztonságos csatornán keresztül. 

Ez a frissítés alkalmazza a IoT Central kapcsolat részleteit a lépcsőzetes 500 eszközön, és megjelenik az eszközök listájában. 

![Eszközök listája](./media/howto-connect-rigado-cascade-500/devices-list-c500.png)  

Most már készen áll a C500-eszköz használatára a IoT Central alkalmazásban!

## <a name="next-steps"></a>Következő lépések

Ha Ön egy eszköz fejlesztője, néhány javasolt lépés a következő:

- További információ [az eszközök kapcsolatáról az Azure IoT Central](./concepts-get-connected.md)
- Ismerje meg, hogyan [figyelheti az eszközök kapcsolatát az Azure CLI használatával](./howto-monitor-devices-azure-cli.md)
