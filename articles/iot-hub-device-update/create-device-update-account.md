---
title: Eszközfrissítési fiók létrehozása az eszközfrissítési Azure IoT Hub | Microsoft Docs
description: Hozzon létre egy eszközfrissítési fiókot az eszközfrissítési Azure IoT Hub.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: d3f7f4e1cdd56675d6084448abc810c9a41992f9
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520139"
---
# <a name="device-update-for-iot-hub-resource-management"></a>Eszközfrissítés az IoT Hub-erőforrások kezeléséhez

Az eszközfrissítés elkezdéséhez létre kell hoznia egy eszközfrissítési fiókot, egy példányt, és be kell állítania a hozzáférés-vezérlési szerepköröket. 

## <a name="prerequisites"></a>Előfeltételek

* Hozzáférés egy IoT Hub. Javasoljuk, hogy S1 (Standard) vagy magasabb szintet használjon. 
* Támogatott böngészők:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

## <a name="create-a-device-update-account"></a>Eszközfrissítési fiók létrehozása

1. Ugrás a [Azure Portal](https://portal.azure.com)

2. Kattintson az Erőforrás létrehozása elemre, és keressen rá az "Eszközfrissítés a IoT Hub" kifejezésre

   :::image type="content" source="media/create-device-update-account/device-update-marketplace.png" alt-text="Képernyőkép a IoT Hub eszközfrissítéséről." lightbox="media/create-device-update-account/device-update-marketplace.png":::

3. Kattintson a Create -> Device Update for IoT Hub

4. Adja meg az eszközfrissítési fiókhoz és az erőforráscsoporthoz társítni kívánt Azure-előfizetést

5. Adja meg az eszközfrissítési fiók nevét és helyét

   :::image type="content" source="media/create-device-update-account/account-details.png" alt-text="Képernyőkép a fiókadatokról." lightbox="media/create-device-update-account/account-details.png":::

 > [!NOTE]
 > Az Azure [Products-by-region (Azure-termékek régiónként)](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub) oldalon felfedezheti a régiókat, ahol elérhető a IoT Hub eszközfrissítése. Ha a IoT Hub eszközfrissítés nem érhető el az Ön régiójában, létrehozhat egy fiókot az Önhöz legközelebbi elérhető régióban. 

6. Kattintson a "Tovább: Áttekintés + létrehozás>" elemre

   :::image type="content" source="media/create-device-update-account/account-review.png" alt-text="Képernyőkép a fiókadatok áttekintésével." lightbox="media/create-device-update-account/account-review.png":::

7. Tekintse át a részleteket, majd válassza a "Létrehozás" lehetőséget. Látni fogja, hogy az üzembe helyezés folyamatban van. 

   :::image type="content" source="media/create-device-update-account/account-deployment-inprogress.png" alt-text="Képernyőkép a fiók üzembe helyezésének folyamatáról." lightbox="media/create-device-update-account/account-deployment-inprogress.png":::

8. Néhány perc múlva az üzembe helyezés állapota "complete" (kész) állapotra változik. Kattintson az "Erőforrás ugrás" elemre

   :::image type="content" source="media/create-device-update-account/account-complete.png" alt-text="Képernyőkép a fiók üzembe helyezésének befejezéséről." lightbox="media/create-device-update-account/account-complete.png":::

## <a name="create-a-device-update-instance"></a>Eszközfrissítési példány létrehozása 

Az eszközfrissítés egy példánya egyetlen IoT Hubhoz van társítva. Válassza ki az Eszközfrissítéssel használni kívánt IoT Hubot. Ebben a lépésben egy új megosztott hozzáférésű szabályzatot hozunk létre, amely biztosítja, hogy az eszközfrissítés csak a szükséges engedélyeket használja a IoT Hub (a beállításjegyzék írása és a szolgáltatáshoz való csatlakozás) kezeléséhez. Ez a szabályzat biztosítja, hogy a hozzáférés csak az eszközfrissítésre korlátozódik.

Eszközfrissítési példány létrehozása a fiók létrehozása után.

1. Miután megnyitotta az újonnan létrehozott fiókerőforrást, a Példánykezelés "Példányok" panelre

   :::image type="content" source="media/create-device-update-account/instance-blade.png" alt-text="Képernyőkép a fiókon belüli példánykezelésről." lightbox="media/create-device-update-account/instance-blade.png":::

2. Kattintson a "Create and specify an instance name and select your IoT Hub

   :::image type="content" source="media/create-device-update-account/instance-details.png" alt-text="A példány részleteinek képernyőképe." lightbox="media/create-device-update-account/instance-details.png":::

   > [!NOTE] 
   > Az IoT Hub az eszközfrissítési erőforrásra mutató hivatkozásnak nem kell ugyanabban a régióban lennie, mint az eszközfrissítési fióknak. A jobb teljesítmény érdekében azonban azt javasoljuk, IoT Hub az eszközfrissítési fiók régiójához közeli vagy ahhoz közeli régióban legyen. 

3. Kattintson a „Létrehozás” elemre. A példány "Létrehozás" állapotban lesz. 

   :::image type="content" source="media/create-device-update-account/instance-creating.png" alt-text="A példány létrehozásának képernyőképe." lightbox="media/create-device-update-account/instance-creating.png":::

4. A példány üzembe helyezésének befejezéséhez hagyjon 5–10 percet. Frissítse az állapotot, amíg a "Kiépítési állapot" állapot "Sikeres" állapotra nem frissül.

   :::image type="content" source="media/create-device-update-account/instance-succeeded.png" alt-text="Képernyőkép a példány sikeres létrehozásáról." lightbox="media/create-device-update-account/instance-succeeded.png":::

## <a name="configure-iot-hub"></a>Az IoT Hub 

Annak érdekében, hogy az eszközfrissítés változásértesítéseket fogad IoT Hub-től, az eszközfrissítés integrálható a "Beépített" eseményközpontba. A "IoT Hub konfigurálása" gombra kattintva konfigurálhatja az IoT-eszközökkel való kommunikációhoz szükséges üzenetútvonalat és hozzáférési szabályzatot. 

Az IoT Hub

1. Ha a "Kiépítési állapot" példány "Sikeres" állapotra vált, válassza ki a példányt a Példánykezelés panelen. Kattintson a "Konfigurálás IoT Hub" elemre

   :::image type="content" source="media/create-device-update-account/instance-configure.png" alt-text="Képernyőkép egy példány IoT Hub konfigurálásához." lightbox="media/create-device-update-account/instance-configure.png":::

2. Válassza az "Elfogadom a módosításokat" lehetőséget

   :::image type="content" source="media/create-device-update-account/instance-configure-selected.png" alt-text="Képernyőkép egy példány IoT Hub konfigurálásról." lightbox="media/create-device-update-account/instance-configure-selected.png":::

3. Kattintson a "Frissítés" gombra

   > [!NOTE] 
   > Ha ingyenes csomagokat használ, Azure IoT Hub az üzenetútvonalak engedélyezett száma legfeljebb 5 lehet. Az eszközfrissítési IoT Hub 4 üzenetútvonalat kell konfigurálnia, hogy a várt módon működjön. 

[Tudnivalók a konfigurált üzenetútvonalakról.](device-update-resources.md) 


## <a name="configure-access-control-roles"></a>Hozzáférés-vezérlési szerepkörök konfigurálása

Ahhoz, hogy más felhasználók hozzáférjenek az eszközfrissítéshez, a felhasználóknak hozzáférést kell biztosítani ehhez az erőforráshoz. 

1. Ugrás a Hozzáférés-vezérlés (IAM) pontra az eszközfrissítési fiókon belül

   :::image type="content" source="media/create-device-update-account/account-access-control.png" alt-text="Képernyőkép a hozzáférés-vezérlésről az eszközfrissítési fiókon belül." lightbox="media/create-device-update-account/account-access-control.png":::

2. Kattintson a "Szerepkör-hozzárendelések hozzáadása" elemre

3. A "Szerepkör kiválasztása" alatt válasszon ki egy Eszközfrissítési szerepkört a megadott lehetőségek közül
     - Eszközfrissítés-rendszergazda
     - Eszközfrissítés olvasója
     - Eszközfrissítési tartalom rendszergazdája
     - Eszközfrissítés tartalomolvasója
     - Eszközfrissítések központi telepítésének rendszergazdája
     - Eszközfrissítések központi telepítésének olvasója
     
   :::image type="content" source="media/create-device-update-account/role-assignment.png" alt-text="Képernyőkép a hozzáférés-vezérlés szerepkör-hozzárendeléséről az eszközfrissítési fiókban." lightbox="media/create-device-update-account/role-assignment.png":::
    
    [Tudnivalók a szerepköralapú hozzáférés-vezérlésről az eszközfrissítési IoT Hub](device-update-control-access.md) 
    
4. Hozzáférés hozzárendelése felhasználóhoz vagy Azure AD-csoporthoz
5. Kattintson a Save (Mentés) gombra.
6. Most már készen áll arra, hogy az eszközfrissítési élményt a saját IoT Hub

## <a name="next-steps"></a>Következő lépések

Próbálja meg frissíteni az eszközt az alábbi gyors oktatóanyagok egyikével:

 - [Eszközfrissítés szimulátoron](device-update-simulator.md)
 - [Eszközfrissítés Raspberry Pi-on](device-update-raspberry-pi.md)
 - [Eszközfrissítés Ubuntu Server 18.04 x64 csomagügynökön](device-update-ubuntu-agent.md)

[Tudnivalók az eszközfrissítési fiókról és a példányról.](device-update-resources.md) 

[Tudnivalók az eszközfrissítések hozzáférés-vezérlési szerepköréről. ](device-update-control-access.md) 

