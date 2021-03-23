---
title: A Defender Micro Agent felépítése a forráskódból (előzetes verzió)
description: A Micro Agent tartalmaz egy infrastruktúrát, amely az eloszlás testreszabására használható.
ms.date: 1/18/2021
ms.topic: quickstart
ms.openlocfilehash: aac9bf224064dd8393acfeb2928f5ed2d1f84381
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781109"
---
# <a name="build-the-defender-micro-agent-from-source-code-preview"></a>A Defender Micro Agent felépítése a forráskódból (előzetes verzió)

A Micro Agent tartalmaz egy infrastruktúrát, amely testreszabható a terjesztéshez. Az elérhető konfigurációs paraméterek listájának megtekintéséhez tekintse meg a `configs/LINUX_BASE.conf` fájlt.

Egyetlen eloszlás esetén módosítsa az `.conf` alapfájlt. 

Ha egynél több terjesztésre van szüksége, az alapkonfigurációból örökölheti az értékeket, és felülbírálhatja annak értékeit. 

Az értékek felülbírálása:

1. Hozzon létre egy új `.dist` fájlt.

1. Hozzáadás `CONF_DEFINE_BASE(${g_plat_config_path} LINUX_BASE.conf)` a tetejéhez.
 
1. Adja meg az új értékeket a szükségesnél, például: 

    `set(ASC_LOW_PRIORITY_INTERVAL 60*60*24)` 

1. Adja meg a `.dist` fájl hivatkozását a létrehozáskor. Példa: 

    `cmake -DCMAKE_BUILD_TYPE=Debug -Dlog_level=DEBUG -Dlog_level_cmdline:BOOL=ON -DIOT_SECURITY_MODULE_DIST_TARGET=UBUNTU1804 ..` 

## <a name="baseline-configuration-signing"></a>Alapterv-konfiguráció aláírása 

Az ügynök alapértelmezés szerint ellenőrzi a lemezre helyezett konfigurációs fájlok valódiságát a módosítás enyhítése érdekében.

Ezt a folyamatot az előfeldolgozó jelző megadásával állíthatja le `ASC_BASELINE_CONF_SIGN_CHECK_DISABLE` .

Nem javasoljuk, hogy az aláírás-ellenőrzési környezetet ne kapcsolja ki éles környezetekben. 

Ha az éles környezetben eltérő konfigurációra van szüksége, lépjen kapcsolatba a Defender for IoT csapatával. 

## <a name="prerequisites"></a>Előfeltételek 

1. Lépjen kapcsolatba a fiók kezelőjével, és kérjen hozzáférést a Defender for IoT forráskódhoz.
 
1. Klónozott vagy kinyerheti a forráskódot a lemez egyik mappájába.

1. Navigáljon a könyvtárba.

1. Az almodulokat a következő kód használatával hívhatja le:

    ```bash
    git submodule update --init
    ```
    
1. Ezután húzza le az Azure IoT SDK almodulját a következő kóddal: 

    ```bash
    git -C deps/azure-iot-sdk-c/ submodule update –init
    ```
 

1. Végrehajtási engedély hozzáadása és a fejlesztői környezet telepítési parancsfájljának futtatása:

    ```bash
    chmod +x scripts/install_development_environment.sh && ./scripts/install_development_environment.sh 
    ```

1. Hozzon létre egy könyvtárat a Build kimenetekhez: 

    ```bash
    mkdir cmake 
    ```

1. Választható A [VSCode](https://code.visualstudio.com/download ) letöltése és telepítése 

1. Választható Telepítse a VSCode [C/C++ bővítményét](https://code.visualstudio.com/docs/languages/cpp ) .

## <a name="building-the-defender-iot-micro-agent"></a>A Defender IoT Micro Agent felépítése 

1. A könyvtár megnyitása a VSCode 

1. Navigáljon a `cmake` címtárhoz. 

1. Futtassa a következő parancsot: 

    ```bash
    cmake -DCMAKE_BUILD_TYPE=Debug -Dlog_level=DEBUG -Dlog_level_cmdline:BOOL=ON -DIOT_SECURITY_MODULE_DIST_TARGET<the appropriate distro configuration file name> .. 
    
    cmake --build . -- -j${env:NPROC}
    ```

    Például: 

    ```bash
    cmake -DCMAKE_BUILD_TYPE=Debug -Dlog_level=DEBUG -Dlog_level_cmdline:BOOL=ON -DIOT_SECURITY_MODULE_DIST_TARGETUBUNTU1804 ..
    
    cmake --build . -- -j${env:NPROC}
    ```

## <a name="next-steps"></a>Következő lépések

[Konfigurálja az Azure Defender IoT-megoldását](quickstart-configure-your-solution.md).
