---
title: Azure-IoT Hub frissítése | Microsoft Docs
description: A IoT Hub díjszabásának és méretezési szintjeinek módosításával további üzenetkezelési és eszközkezelési képességek válnak elérhetővé.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: robinsh
ms.openlocfilehash: 96c3a7b2cfda23f173f4caeff4fb7a92b1ddc438
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "61440233"
---
# <a name="how-to-upgrade-your-iot-hub"></a>Az IoT Hub frissítése

A IoT-megoldás növekedésével az Azure IoT Hub készen áll a vertikális felskálázásra. Az Azure IoT Hub kétszintű, alapszintű (B) és standard (S) szintű szolgáltatást kínál a különböző funkciókat használni kívánó ügyfelek számára. Az egyes szintek három méretben (1, 2 és 3) vannak meghatározva, amelyek meghatározzák az egyes napokon elküldött üzenetek számát.

Ha több eszközzel rendelkezik, és több képességre van szüksége, háromféle módon állíthatja be az IoT hubot az igényeinek megfelelően:

* Vegyen fel egységeket az IoT hub-on belül. Például a B1 IoT hub minden további egysége naponta további 400 000 üzenetet tesz lehetővé.

* Módosítsa az IoT hub méretét. Tegyük fel például, hogy a B1-ről a B2 szintjére migrálja az egyes egységek által naponta támogatott üzenetek számát.

* Váltson magasabb szintre. Például a B1 szintről az S1 szintre való frissítéssel elérheti a speciális szolgáltatásokat ugyanazzal az üzenetkezelési kapacitással.

Ezek a módosítások a meglévő műveletek megszakítása nélkül is előfordulhatnak.

Ha vissza szeretné állítani az IoT hub-t, eltávolíthatja az egységeket, és csökkentheti az IoT hub méretét, de nem lehet alacsonyabb szintre váltani. Például áthelyezheti az S2-es szintet az S1 szintjére, de nem az S2 szintjéről a B1 szintjére. Egy adott rétegen belül csak egy [IOT hub-kiadás](https://azure.microsoft.com/pricing/details/iot-hub/) választható IoT hub. Létrehozhat például egy IoT Hubt az S1 több egységével, de nem különböző kiadásokból származó egységekkel, például S1 és B3, illetve S1 és S2 használatával.

Ezek a példák arra szolgálnak, hogy megtudja, hogyan módosíthatja a IoT hubot a megoldás módosításaival. Az egyes rétegek képességeire vonatkozó konkrét információkért tekintse meg az [Azure IoT hub díjszabását](https://azure.microsoft.com/pricing/details/iot-hub/).

## <a name="upgrade-your-existing-iot-hub"></a>Meglévő IoT hub frissítése

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) , és navigáljon az IoT hubhoz.

2. Válassza ki **a díjszabást és a méretezést**.

   ![Díjszabás és méretezés](./media/iot-hub-upgrade/pricing-scale.png)

3. Ha módosítani szeretné a hub szintjét, válassza a **díjszabás és a skála szintje** lehetőséget. Válassza ki az új szintet, majd kattintson a **kiválasztás** elemre.

   ![Tarifacsomag és méret](./media/iot-hub-upgrade/select-tier.png)

4. A hub egységei számának módosításához adjon meg egy új értéket **IoT hub egység** alatt.

5. A módosítások mentéséhez válassza a **Mentés** gombot.

Az IoT hub már be van állítva, és a konfigurációk változatlanok maradnak.

Az alapszintű csomag IoT Hub és a standard szintű IoT Hub maximális partíciós korlátja 32. A legtöbb IoT-hubhoz csak 4 partíció szükséges. A partíciós korlátot a rendszer a IoT Hub létrehozásakor választja ki, és az eszközről a felhőbe irányuló üzeneteket az üzenetek egyidejű olvasóinak számához kapcsolja. Ez az érték változatlan marad az alapszintről a standard csomagra való Migrálás során.

## <a name="next-steps"></a>Következő lépések

További részletek [a megfelelő IoT hub-szintek kiválasztásáról](iot-hub-scaling.md).