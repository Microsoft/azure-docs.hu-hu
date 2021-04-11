---
title: Eszköz-frissítési fiók létrehozása az Azure IoT Hub eszköz frissítése során | Microsoft Docs
description: Eszköz-frissítési fiók létrehozása az Azure IoT Hub eszköz frissítésében.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: 5956b7b74d27a4f9a2b79ee3950c8ac765610c70
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105558482"
---
# <a name="device-update-for-iot-hub-resource-management"></a>Eszközfrissítés az IoT Hub-erőforrások kezeléséhez

Az eszköz frissítésének megkezdéséhez létre kell hoznia egy eszköz-frissítési fiókot, egy példányt, és be kell állítania a hozzáférés-vezérlési szerepköröket. 

## <a name="prerequisites"></a>Előfeltételek

* Hozzáférés egy IoT Hubhoz. Javasoljuk, hogy S1 (standard) vagy újabb szintet használjon. 
* Támogatott böngészők:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

## <a name="create-a-device-update-account"></a>Eszköz-frissítési fiók létrehozása

1. Ugrás a [Azure Portal](https://portal.azure.com)

2. Kattintson az erőforrás létrehozása elemre, és keressen rá az "eszköz frissítése IoT Hub" kifejezésre.

   :::image type="content" source="media/create-device-update-account/device-update-marketplace.png" alt-text="A IoT Hub erőforráshoz tartozó eszköz frissítésének képernyőképe." lightbox="media/create-device-update-account/device-update-marketplace.png":::

3. Kattintson a létrehozás – > eszköz frissítése elemre IoT Hub

4. Adja meg az eszköz frissítési fiókjához és erőforráscsoporthoz társítandó Azure-előfizetést.

5. Adja meg az eszköz frissítési fiókjának nevét és helyét

   :::image type="content" source="media/create-device-update-account/account-details.png" alt-text="Képernyőfelvétel a fiók részleteiről." lightbox="media/create-device-update-account/account-details.png":::

 > [!NOTE]
 > Az [Azure Products-By-Region oldalon](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub) megtekintheti azokat a régiókat, amelyeken az IoT hub eszköz frissítése elérhető. Ha a IoT Hub eszköz frissítése nem érhető el a régióban, akkor dönthet úgy, hogy létrehoz egy fiókot egy Önhöz legközelebb álló régióban. 

6. Kattintson a "tovább: felülvizsgálat +> létrehozása" elemre.

   :::image type="content" source="media/create-device-update-account/account-review.png" alt-text="Képernyőkép a fiók részleteinek áttekintése." lightbox="media/create-device-update-account/account-review.png":::

7. Tekintse át a részleteket, majd válassza a létrehozás lehetőséget. Ekkor megjelenik a telepítés folyamatban. 

   :::image type="content" source="media/create-device-update-account/account-deployment-inprogress.png" alt-text="Képernyőfelvétel a fiók központi telepítéséről folyamatban van." lightbox="media/create-device-update-account/account-deployment-inprogress.png":::

8. A központi telepítés állapota néhány percen belül a "Befejezés" értékre változik. Kattintson az "erőforrás kiugrása" elemre.

   :::image type="content" source="media/create-device-update-account/account-complete.png" alt-text="Képernyőfelvétel: a fiók központi telepítése befejeződött." lightbox="media/create-device-update-account/account-complete.png":::

## <a name="create-a-device-update-instance"></a>Eszköz frissítési példányának létrehozása 

Az eszköz frissítésének egy példánya egyetlen IoT-hubhoz van társítva. Válassza ki az IoT hubot, amelyet az eszköz frissítésével fog használni. Ebben a lépésben egy új, közös hozzáférési szabályzatot hozunk létre, amely biztosítja, hogy az eszköz csak a szükséges engedélyekkel működjön együtt a IoT Hub (a beállításjegyzék írása és a szolgáltatás csatlakoztatása). Ez a szabályzat biztosítja, hogy a hozzáférés csak az eszköz frissítéseire korlátozódik.

Eszköz frissítési példányának létrehozása egy fiók létrehozása után.

1. Ha az újonnan létrehozott fiók-erőforrást választotta, lépjen a példányok kezelése "példányok" panelére

   :::image type="content" source="media/create-device-update-account/instance-blade.png" alt-text="Képernyőfelvétel a fiókban lévő példányok felügyeletéről." lightbox="media/create-device-update-account/instance-blade.png":::

2. Kattintson a Create (létrehozás) elemre, és adja meg a példány nevét, és válassza ki a IoT Hub

   :::image type="content" source="media/create-device-update-account/instance-details.png" alt-text="Képernyőkép a példány részleteiről." lightbox="media/create-device-update-account/instance-details.png":::

   > [!NOTE] 
   > Az eszköz frissítési erőforrásához csatolt IoT Hub nem kell ugyanabban a régióban lennie, mint az eszköz frissítési fiókja. A jobb teljesítmény érdekében azonban ajánlott, hogy a IoT Hub olyan régióban legyenek, mint az eszköz frissítési fiókjának régiója. 

3. Kattintson a „Létrehozás” elemre. A példány "létrehozás" állapotban jelenik meg. 

   :::image type="content" source="media/create-device-update-account/instance-creating.png" alt-text="A példány létrehozásának képernyőképe." lightbox="media/create-device-update-account/instance-creating.png":::

4. A példány telepítésének befejezéséhez engedélyezze a 5-10 percet. Frissítse az állapotot, amíg meg nem jelenik a "kiépítési állapot" a "sikeres" értékre vált.

   :::image type="content" source="media/create-device-update-account/instance-succeeded.png" alt-text="A példány-létrehozás képernyőképe sikeres volt." lightbox="media/create-device-update-account/instance-succeeded.png":::

## <a name="configure-iot-hub"></a>IoT Hub konfigurálása 

Ahhoz, hogy az eszköz frissítse IoT Hub változási értesítéseit, az eszköz frissítése a "beépített" esemény-hubhoz integrálódik. A "IoT Hub konfigurálása" gombra kattintva konfigurálhatja a IoT-eszközökkel való kommunikációhoz szükséges üzenet-útvonalakat és hozzáférési szabályzatot. 

IoT Hub konfigurálása

1. Ha a példány "kiépítési állapota" "sikeres" állapotra vált, válassza ki a példányt a példányok kezelése panelen. Kattintson a "IoT Hub konfigurálása" elemre.

   :::image type="content" source="media/create-device-update-account/instance-configure.png" alt-text="Képernyőkép a példányok IoT Hubának konfigurálásáról." lightbox="media/create-device-update-account/instance-configure.png":::

2. Válassza a "Elfogadom ezeket a módosításokat" lehetőséget.

   :::image type="content" source="media/create-device-update-account/instance-configure-selected.png" alt-text="Képernyőkép az IoT Hub konfigurálásáról egy példányra vonatkozóan." lightbox="media/create-device-update-account/instance-configure-selected.png":::

3. Kattintson a frissítés gombra.

[Ismerje meg a konfigurált üzenet-útvonalakat.](device-update-resources.md) 


## <a name="configure-access-control-roles"></a>Hozzáférés-vezérlési szerepkörök konfigurálása

Ahhoz, hogy más felhasználók hozzáférhessenek az eszköz frissítéséhez, hozzáférést kell biztosítani a felhasználóknak ehhez az erőforráshoz. 

1. Lépjen a hozzáférés-vezérlés (IAM) elemre az eszköz frissítési fiókján belül

   :::image type="content" source="media/create-device-update-account/account-access-control.png" alt-text="Képernyőkép a hozzáférés-vezérlésről az eszköz frissítési fiókján belül." lightbox="media/create-device-update-account/account-access-control.png":::

2. Kattintson a "szerepkör-hozzárendelések hozzáadása" elemre.

3. A "szerepkör kiválasztása" területen válassza ki az eszköz frissítési szerepkörét a megadott beállítások közül.
     - Eszköz frissítésének rendszergazdája
     - Eszköz frissítési olvasója
     - Eszköz frissítése – tartalom rendszergazdája
     - Eszköz frissítési tartalom olvasója
     - Eszköz frissítése központi telepítések rendszergazdája
     - Eszköz frissítése központi telepítések olvasója
     
   :::image type="content" source="media/create-device-update-account/role-assignment.png" alt-text="Képernyőkép a hozzáférés-vezérlési szerepkör-hozzárendelésekről az eszköz frissítési fiókján belül." lightbox="media/create-device-update-account/role-assignment.png":::
    
    [Tudnivalók a szerepköralapú hozzáférés-vezérlésről a IoT Hub eszköz frissítésében](device-update-control-access.md) 
    
4. Hozzáférés kiosztása egy felhasználóhoz vagy egy Azure AD-csoporthoz
5. Kattintson a Save (Mentés) gombra.
6. Most már készen áll az eszköz frissítési élményének használatára a IoT Hub belül

## <a name="next-steps"></a>Következő lépések

Próbálja meg frissíteni az eszközt az alábbi rövid útmutatók egyikével:

 - [Eszköz frissítése szimulátoron](device-update-simulator.md)
 - [Eszköz frissítése a málna PI-ban](device-update-raspberry-pi.md)
 - [Eszköz frissítése Ubuntu Server 18,04 x64 Package Agent-ügynökön](device-update-ubuntu-agent.md)

[Az eszköz frissítési fiókjának és példányának megismerése.](device-update-resources.md) 

[További információ az eszközök frissítési hozzáférés-vezérlési szerepköreiről. ](device-update-control-access.md) 

