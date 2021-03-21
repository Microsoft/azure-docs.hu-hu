---
title: Beszédfelismerési eszközök SDK Roobo intelligens hang dev Kit v1 – Speech Service
titleSuffix: Azure Cognitive Services
description: Előfeltételek és utasítások a Speech Devices SDK első lépéseihez, Roobo Smart audio dev Kit v1.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 73eb1225ffc5fd01f9a27ca99ad2b059d45a36cf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95015290"
---
# <a name="device-roobo-smart-audio-dev-kit"></a>Eszköz: Roobo Smart audio dev Kit

Ez a cikk a Roobo Smart audio dev Kit eszközre vonatkozó információkat tartalmaz.

## <a name="set-up-the-development-kit"></a>A fejlesztői csomag beállítása

1. A fejlesztői készlet két Micro USB-összekötővel rendelkezik. A bal oldali összekötő a fejlesztői készlet bekapcsolására szolgál, és az alábbi képen Kiemelt teljesítményként jelenik meg. Az egyik az, hogy irányítsa, és a képen hibakeresést jelöl.

    ![A fejlesztői csomag csatlakoztatása](media/speech-devices-sdk/qsg-1.png)

1. A Power port SZÁMÍTÓGÉPekhez vagy hálózati adapterhez való csatlakoztatásához használjon egy Micro USB-kábelt a fejlesztői készlethez. A felső táblán egy zöld energiaellátási kijelző jelenik meg.

1. A fejlesztői csomag vezérléséhez csatlakoztassa a hibakeresési portot egy számítógéphez egy második Micro USB-kábel használatával. A megbízható kommunikáció biztosítása érdekében elengedhetetlen a kiváló minőségű kábelek használata.

1. A körkörös vagy lineáris konfigurációhoz tartozó fejlesztői készlet tájolása.

    |Fejlesztői csomag konfigurációja|Tájolás|
    |-----------------------------|------------|
    |Körkörös|Egyenesen, a felső határt megnéző mikrofonokkal|
    |Lineáris|Az oldalon, az Ön felé irányuló mikrofonokkal (az alábbi képen látható)|

    ![Lineáris fejlesztői csomag tájolása](media/speech-devices-sdk/qsg-2.png)

1. Telepítse a tanúsítványokat, és állítsa be a hangeszköz engedélyeit. Írja be a következő parancsokat a parancssor ablakába:

   ```powershell
   adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/
   adb shell
   cd /data/
   chmod 777 roobo_setup.sh
   ./roobo_setup.sh
   exit
   ```

    > [!NOTE]
    > Ezek a parancsok az androidos hibakeresési hidat használják, `adb.exe` amely a Android Studio telepítésének részét képezi. Ez az eszköz a C:\Users \[ Felhasználónév] \AppData\Local\Android\Sdk\platform-Tools. található. Ezt a könyvtárat hozzáadhatja az elérési úthoz, hogy kényelmesebb legyen a meghívása `adb` . Ellenkező esetben meg kell adnia a adb.exe telepítésének teljes elérési útját minden meghívott parancsban `adb` .
    >
    > Ha hibaüzenet jelenik meg, `no devices/emulators found` ellenőrizze, hogy az USB-kábel csatlakoztatva van-e, és kiváló minőségű kábel-e. A használatával `adb devices` ellenőrizhető, hogy a számítógép tud-e kommunikálni a fejlesztői csomaggal, mert az eszközök listáját fogja visszaadni.
    >
    > [!TIP]
    > A számítógép mikrofonjának és hangszórójának elnémításával győződjön meg arról, hogy dolgozik a fejlesztői csomag mikrofonjában. Így nem fogja véletlenül elindítani az eszközt a számítógép hangján.

1. Ha hangszórót szeretne csatlakoztatni a fejlesztői csomaghoz, csatlakoztathatja azt a hangbemenethez. Válasszon egy kiváló minőségű hangszórót 3,5 mm-es analóg csatlakozóval.

    ![Vysor hang](media/speech-devices-sdk/qsg-14.png)

## <a name="development-information"></a>Fejlesztési információk

További fejlesztési információk: [Roobo fejlesztői útmutató](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf).

## <a name="audio"></a>Audió

A Roobo olyan eszközt biztosít, amely rögzíti az összes hang-és flash-memóriát. Hasznos lehet a hangproblémák elhárításában. Az eszköz egy verzióját az egyes fejlesztői csomagokhoz tartozó konfigurációkhoz biztosítjuk. A  [Roobo oldalon](http://ddk.roobo.com/)válassza ki az eszközt, majd kattintson a lap alján található **Roobo-eszközök** hivatkozásra.

## <a name="next-steps"></a>Következő lépések

* [Az Android-minta alkalmazás futtatása](./speech-devices-sdk-quickstart.md?pivots=platform-android%253fpivots%253dplatform-android)