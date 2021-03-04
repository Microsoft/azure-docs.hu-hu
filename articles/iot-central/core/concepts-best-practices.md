---
title: Az eszközök fejlesztésével kapcsolatos ajánlott eljárások az Azure IoT Centralban | Microsoft Docs
description: Ez a cikk az Azure-IoT Central eszközökhöz való csatlakozás ajánlott eljárásait ismerteti.
author: dominicbetts
ms.author: dobett
ms.date: 03/03/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- device-developer
ms.openlocfilehash: e8ae8b0173e53c0a46ded1a2690175e367997c9f
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054709"
---
# <a name="best-practices-for-device-development"></a>Ajánlott eljárások az eszközök fejlesztéséhez

*Ez a cikk az eszközök fejlesztőire vonatkozik.*

Ezek a javaslatok azt mutatják be, hogyan valósíthatók meg az eszközök a beépített vész-helyreállítás és a IoT Central automatikus skálázás előnyeinek kihasználásához.

Az alábbi lista a magas szintű folyamatot mutatja be, amikor egy eszköz csatlakozik IoT Centralhoz:

1. A DPS használatával kiépítheti az eszközt, és lekérheti az eszköz kapcsolódási karakterláncát.

1. A kapcsolati karakterlánc használatával csatlakoztathatja IoT Central belső IoT Hub végpontját. Adatok küldése és fogadása az IoT Central alkalmazásból.

1. Ha az eszköz csatlakoztatási hibákba kerül, akkor a hiba típusától függően próbálkozzon újra a vagy az eszköz újbóli kiépítésével.

## <a name="use-dps-to-provision-the-device"></a>Az eszköz kiépítése a DPS használatával

Ha az eszközt a DPS-vel szeretné kiépíteni, használja a hatókör-azonosítót, a hitelesítő adatokat és az eszköz AZONOSÍTÓját a IoT Central alkalmazásból. A hitelesítő adatok típusával kapcsolatos további tudnivalókért lásd: [X. 509 csoportos regisztráció](concepts-get-connected.md#x509-group-enrollment) és [sas csoportos regisztráció](concepts-get-connected.md#sas-group-enrollment). További információ az eszközök azonosítói: [eszköz regisztrálása](concepts-get-connected.md#device-registration).

Siker esetén a DPS olyan kapcsolati karakterláncot ad vissza, amelyet az eszköz használhat a IoT Central alkalmazáshoz való kapcsolódáshoz. A kiépítési hibák elhárításához tekintse [meg az eszköz kiépítési állapotának ellenőrzését](troubleshoot-connection.md#check-the-provisioning-status-of-your-device)ismertető témakört.

Az eszköz gyorsítótárazhatja a kapcsolati karakterláncot, amelyet később a kapcsolatokhoz is használhat. Az eszköznek azonban elő kell készülnie a [kapcsolódási hibák kezelésére](#handle-connection-failures).

## <a name="connect-to-iot-central"></a>Kapcsolódás IoT Centralhoz

A kapcsolati karakterlánc használatával csatlakoztathatja IoT Central belső IoT Hub végpontját. A kapcsolódás lehetővé teszi, hogy telemetria küldjön a IoT Central alkalmazásnak, szinkronizálja a tulajdonságértékeket a IoT Central alkalmazással, és válaszoljon a IoT Central alkalmazás által küldött parancsokra.

## <a name="handle-connection-failures"></a>Kapcsolatok hibáinak kezelése

A skálázási vagy vész-helyreállítási célokra IoT Central frissítheti a mögöttes IoT hubot. A kapcsolat fenntartásához az eszköz kódjának bizonyos kapcsolódási hibákat kell kezelnie az új IoT Hub végponttal létesített kapcsolat létrehozásával.

Ha az eszköz a csatlakozáskor a következő hibák valamelyikét kapja meg, akkor újra meg kell ismételni a kiépítési lépést a DPS-vel egy új kapcsolati karakterlánc beszerzéséhez. Ezek a hibák azt jelentik, hogy az eszköz által használt kapcsolatok karakterlánca már nem érvényes:

- Nem érhető el IoT Hub végpont.
- Lejárt biztonsági jogkivonat.
- Az eszköz le van tiltva IoT Hubban.

Ha az eszköz a csatlakozáskor a következő hibák valamelyikét kapja meg, akkor a kapcsolat megismétléséhez használjon egy back-off stratégiát. Ezek a hibák azt jelentik, hogy az eszköz által használt kapcsolati sztring továbbra is érvényes, de az átmeneti feltételek miatt leáll az eszköz csatlakoztatása:

- A kezelő letiltotta az eszközt.
- 500 belső hiba történt a szolgáltatásból.

További információ az eszközök hibakódokról: az [eszközök kapcsolatainak hibaelhárítása](troubleshoot-connection.md).

## <a name="next-steps"></a>Következő lépések

Ha Ön egy eszköz fejlesztője, néhány javasolt lépés a következő:

- Tekintse át a következő kódrészletet, amely bemutatja, hogyan használhatók az SAS-tokenek az [oktatóanyagban: ügyfélalkalmazás létrehozása és összekötése az Azure IoT Central alkalmazással](tutorial-connect-device.md)
- Ismerje meg, hogyan [csatlakoztathatók az X. 509 tanúsítvánnyal rendelkező eszközök a IoT Central alkalmazáshoz készült Node.js ESZKÖZOLDALI SDK-val](how-to-connect-devices-x509.md)
- Ismerje meg, hogyan [figyelheti az eszközök kapcsolatát az Azure CLI használatával](./howto-monitor-devices-azure-cli.md)
- Ismerje meg, hogyan [határozhat meg új IoT-típust az Azure IoT Central-alkalmazásban](./howto-set-up-template.md)
- További információ a [Azure IoT Edge eszközökről és az Azure IoT Central](./concepts-iot-edge.md)
