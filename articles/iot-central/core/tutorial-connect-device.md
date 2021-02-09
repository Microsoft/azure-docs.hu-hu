---
title: Oktatóanyag – általános ügyfélalkalmazás összekötése az Azure IoT Central-vel | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan lehet egy eszköz fejlesztőként egy C, C#, Java, JavaScript vagy Python ügyfélalkalmazás futtatására szolgáló eszközt az Azure IoT Central-alkalmazáshoz csatlakozni. Az automatikusan létrehozott sablon úgy módosítható, ha olyan nézeteket ad hozzá, amelyek lehetővé teszik, hogy az operátor kommunikáljon egy csatlakoztatott eszközzel.
author: dominicbetts
ms.author: dobett
ms.date: 11/24/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom:
- mqtt
- device-developer
zone_pivot_groups: programming-languages-set-twenty-six
ms.openlocfilehash: 8f1b5eabe235d107b48dc7b2db5b6d4b1188a3fa
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99833966"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application"></a>Oktatóanyag: ügyfélalkalmazás létrehozása és összekötése az Azure IoT Central-alkalmazással

*Ez a cikk a megoldás-építők és az eszközök fejlesztőire vonatkozik.*

Ebből az oktatóanyagból megtudhatja, hogyan csatlakozhat egy ügyfélalkalmazás az Azure IoT Central-alkalmazásához az eszköz fejlesztőinek. Az alkalmazás szimulálja egy termosztátos eszköz viselkedését. Amikor az alkalmazás csatlakozik a IoT Centralhoz, elküldi a termosztát-eszköz modell-AZONOSÍTÓját. IoT Central a modell AZONOSÍTÓját használja az eszköz modell lekéréséhez és az eszköz sablonjának létrehozásához. A testreszabásokat és nézeteket hozzá kell adni az eszköz sablonhoz, hogy az operátor kapcsolatba lépjen az eszközzel.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozza létre és futtassa az eszköz kódját, és tekintse meg az IoT Central alkalmazáshoz való kapcsolódást.
> * Az eszközről eljuttatott szimulált telemetria megtekintése.
> * Egyéni nézetek hozzáadása egy eszköz sablonhoz.
> * Tegye közzé az eszköz sablonját.
> * Az eszköz tulajdonságainak kezeléséhez használja a nézetet.
> * Az eszköz vezérléséhez hívja meg a parancsot.

:::zone pivot="programming-language-ansi-c"

[!INCLUDE [iot-central-connect-device-c](../../../includes/iot-central-connect-device-c.md)]

:::zone-end

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-central-connect-device-csharp](../../../includes/iot-central-connect-device-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-central-connect-device-java](../../../includes/iot-central-connect-device-java.md)]

:::zone-end

:::zone pivot="programming-language-javascript"

[!INCLUDE [iot-central-connect-device-nodejs](../../../includes/iot-central-connect-device-nodejs.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-central-connect-device-python](../../../includes/iot-central-connect-device-python.md)]

:::zone-end

## <a name="view-raw-data"></a>Nyers adattárolók megtekintése

Eszköz-fejlesztőként a **nyers** adatnézet segítségével megvizsgálhatja, hogy az eszköz milyen nyers adatokat küld IoT Centralnak:

:::image type="content" source="media/tutorial-connect-device/raw-data.png" alt-text="A nyers adatnézet":::

Ebben a nézetben kiválaszthatja a megjelenítendő oszlopokat, és megadhatja a megtekinteni kívánt időtartományt. A nem **modellezett adatok** oszlop az eszköz azon adatait jeleníti meg, amelyek nem felelnek meg az eszköz sablonjának bármely tulajdonság-vagy telemetria-definíciójának.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ha továbbra is szeretne IoT Central oktatóanyagokat használni, és többet szeretne megtudni egy IoT Central megoldás létrehozásáról, tekintse meg a következő témakört:

> [!div class="nextstepaction"]
> [Átjáró-eszközsablon létrehozása](./tutorial-define-gateway-device-type.md)

Most, hogy megismerte, hogyan hozhat létre egy Java-t használó eszközt, néhány javasolt következő lépés:

* Olvassa el a [Mi az eszközök sablonjai?](./concepts-device-templates.md) további információ az eszközök sablonjainak szerepéről az eszköz kódjának megvalósításakor.
* Olvassa el az [Azure IoT Centralhoz való csatlakozást](./concepts-get-connected.md) ismertető témakört, amelyből megtudhatja, hogyan regisztrálhat eszközöket a IoT Central, és hogyan IoT Central biztonságossá teszi az eszközök kapcsolatait.
* Olvassa el a [telemetria, a tulajdonságot és a parancs hasznos](concepts-telemetry-properties-commands.md) adatait, ha többet szeretne megtudni az eszköz IoT Centralsal való cseréjéről.
* Olvassa el a [IoT Plug and Play-eszköz fejlesztői útmutatóját](../../iot-pnp/concepts-developer-guide-device.md).
