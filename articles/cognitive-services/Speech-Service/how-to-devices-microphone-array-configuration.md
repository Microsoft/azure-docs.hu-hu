---
title: Mikrofonos tömb – beszédfelismerési szolgáltatás konfigurálása
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan konfigurálhat egy mikrofonos tömböt, hogy a Speech Devices SDK használhassa azt.
services: cognitive-services
author: mswellsi
manager: yanbo
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: wellsi
ms.openlocfilehash: cf0580c96f5bf78f0444b2bb39088f2a417fd658
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95025061"
---
# <a name="how-to-configure-a-microphone-array"></a>Mikrofonsor konfigurálásának módja

Ebből a cikkből megtudhatja, hogyan konfigurálhat egy [mikrofonos tömböt](./speech-devices-sdk-microphone.md). Ez magában foglalja a munkaszög beállítását, valamint azt, hogy a Speech Devices SDK melyik mikrofont használja.

A Speech Devices SDK a legjobb megoldás egy olyan mikrofonos tömb, amely az [irányelveknek](./speech-devices-sdk-microphone.md)megfelelően lett kialakítva. A mikrofon-tömb konfigurációját az operációs rendszer adhatja meg, vagy az alábbi módszerek egyikével biztosítható.

A Speech Devices SDK eredetileg a mikrofon-tömböket támogatja, a konfigurációk rögzített készletének kiválasztásával.

```java
private static String DeviceGeometry = "Circular6+1"; // "Circular6+1", "Linear4",
private static String SelectedGeometry = "Circular6+1"; // "Circular6+1", "Circular3+1", "Linear4", "Linear2"
```

Windows rendszeren a hangvezérlő a mikrofon tömbjét is biztosítja.

A v 1.11.0 a Speech Devices SDK a [JSON-fájlokból](https://aka.ms/sdsdk-micarray-json)is támogatja a konfigurációt.


## <a name="windows"></a>Windows
Windows rendszeren a rendszer automatikusan beolvassa a mikrofon tömb geometriájának adatait az audio-illesztőprogramból. Így a tulajdonságok, `DeviceGeometry`  `SelectedGeometry` és a `MicArrayGeometryConfigFile` megadása nem kötelező. A használatával megadott [JSON-fájlt](https://aka.ms/sdsdk-micarray-json) `MicArrayGeometryConfigFile` csak a beamforming-tartomány beszerzésére használjuk.

Ha egy mikrofon-tömb van megadva a használatával `AudioConfig::FromMicrophoneInput` , akkor a megadott mikrofont használjuk. Ha nincs megadva a mikrofon vagy a `AudioConfig::FromDefaultMicrophoneInput` neve, akkor az alapértelmezett mikrofont használjuk, amely a Windows hangbeállításokban van megadva.
A Speech Devices SDK-ban a Microsoft audio stack csak a 16 KHz-es integrált minta arányú mintavételi sebességeket támogatja.

## <a name="linux"></a>Linux
Linux rendszeren a mikrofon geometriájának adatait meg kell adni. A használata `DeviceGeometry` és `SelectedGeometry` továbbra is támogatott. A tulajdonság használatával a JSON-fájlon keresztül is megadható `MicArrayGeometryConfigFile` . A Windowshoz hasonlóan a beamforming tartományt is megadhatja a JSON-fájl.

Ha egy mikrofon-tömb van megadva a használatával `AudioConfig::FromMicrophoneInput` , akkor a megadott mikrofont használjuk. Ha nincs megadva a mikrofon vagy a `AudioConfig::FromDefaultMicrophoneInput` neve, akkor a rendszer az *alapértelmezett* nevű ALSA-eszközről rögzíti a rekordot. Alapértelmezés szerint az *alapértelmezett érték* mindig a 0. eszközre mutat, de a felhasználók módosíthatják a `asound.conf` fájlban. 

A Speech Devices SDK-ban a Microsoft audio stack csak a 16 KHz-es integrált adatmennyiségek esetében támogatja a leegyszerűsítést. Emellett a következő formátumok támogatottak: 32 bites IEEE little endian float, 32 bites kis endian aláírt int, 24 bites kis endian aláírt int, 16 bites kis endian aláírt int, és 8 bites aláírt int.

## <a name="android"></a>Android
A Speech Devices SDK jelenleg csak a [Roobo v1](./speech-devices-sdk-quickstart.md?pivots=platform-android%253fpivots%253dplatform-android) -t támogatja. A viselkedés ugyanaz, mint a korábbi verziók, `MicArrayGeometryConfigFile` a Now tulajdonság kivételével a beamforming-tartományt tartalmazó JSON-fájl megadására is használható.

## <a name="microphone-array-configuration-json"></a>Mikrofon tömb konfigurációjának JSON

A mikrofonos tömb geometriai konfigurációjának JSON-fájlja a [JSON-sémát](https://aka.ms/sdsdk-micarray-json)fogja követni. A következő példák a sémát követik.


```json
{
    "micArrayType": "Linear",
    "geometry": "Linear4"
}
```


Vagy


```json
{
    "micArrayType": "Planar",
    "horizontalAngleBegin": 0,
    "horizontalAngleEnd": 360,
    "numberOfMicrophones": 4,
    "micCoord": [
        {
            "xCoord": 0,
            "yCoord": 0,
            "zCoord": 0
        },
        {
            "xCoord": 40,
            "yCoord": 0,
            "zCoord": 0
        },
        {
            "xCoord": -20,
            "yCoord": -35,
            "zCoord": 0
        },
        {
            "xCoord": -20,
            "yCoord": 35,
            "zCoord": 0
        }
    ]
}
```

Vagy

```json
{
    "micArrayType": "Linear",
    "horizontalAngleBegin": 70,
    "horizontalAngleEnd": 110
}
```


## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Beszédfelismerési eszköz kiválasztása](get-speech-devices-sdk.md)