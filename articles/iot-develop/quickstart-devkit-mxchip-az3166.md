---
title: MXCHIP-AZ3166 összekötése az Azure IoT Central rövid útmutatóval
description: Az Azure RTOS Embedded szoftver használatával összekapcsolhatók egy MXCHIP AZ3166-eszköz az Azure IoT, és elküldhetik a telemetria.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: c
ms.topic: quickstart
ms.date: 03/17/2021
ms.openlocfilehash: 367f527180a310f2cbc74b1ccdc1102e1e53d1cf
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105605984"
---
# <a name="quickstart-connect-an-mxchip-az3166-devkit-to-iot-central"></a>Gyors útmutató: MXCHIP AZ3166 fejlesztői készlet összekötése IoT Central

**A** következőkre vonatkozik: [beágyazott eszközök fejlesztése](about-iot-develop.md#embedded-device-development)<br>
**Teljes befejezési idő**: 30 perc

[![Kód tallózása](media/common/browse-github-code.png)](https://github.com/azure-rtos/getting-started/tree/master/MXChip/AZ3166)

Ebben az oktatóanyagban az Azure RTOS használatával csatlakozik egy MXCHIP AZ3166 IoT fejlesztői készlet (a továbbiakban MXCHIP fejlesztői készlet) az Azure IoT-hoz. A cikk része az [Azure IoT Embedded-eszközök fejlesztésének első lépéseinek](quickstart-device-development.md). A sorozat bevezeti az eszközök fejlesztői az Azure RTOS, és bemutatja, hogyan csatlakoztatható több eszköz-értékelési készlet az Azure IoT-hoz.

Az alábbi feladatokat fogja elvégezni:

* Beágyazott fejlesztői eszközök telepítése a MXCHIP-fejlesztői készlet programozásához C-ben
* Hozzon létre egy rendszerképet, és villogjon a MXCHIP fejlesztői készlet
* Az Azure IoT Central használatával Felhőbeli összetevőket hozhat létre, tulajdonságokat tekinthet meg, megtekintheti az eszközök telemetria, és közvetlen parancsokat hívhat meg

## <a name="prerequisites"></a>Előfeltételek

* Microsoft Windows 10 rendszerű számítógép
* [Git](https://git-scm.com/downloads) az adattár klónozásához
* Hardver

    > * A [MXCHIP AZ3166 IoT fejlesztői készlet](https://aka.ms/iot-devkit) (MXCHIP fejlesztői készlet)
    > * Wi-Fi 2,4 GHz-es
    > * USB 2,0 egy dugaszos Micro USB dugaszos kábel

## <a name="prepare-the-development-environment"></a>A fejlesztési környezet előkészítése

A fejlesztési környezet beállításához először klónozott egy GitHub-tárházat, amely az oktatóanyaghoz szükséges összes eszközt tartalmazza. Ezután telepítheti a programozási eszközök készletét.

### <a name="clone-the-repo-for-the-tutorial"></a>A tárház klónozása az oktatóanyaghoz

A következő tárház klónozásával letöltheti az összes minta eszköz kódját, a telepítési parancsfájlokat és a dokumentáció offline verzióit. Ha korábban egy másik oktatóanyagban klónozotta ezt a tárházat, nem kell újra végrehajtania.

A tárház klónozásához futtassa a következő parancsot:

```shell
git clone --recursive https://github.com/azure-rtos/getting-started.git
```

### <a name="install-the-tools"></a>Az eszközök telepítése

A klónozott tárház tartalmaz egy telepítési parancsfájlt, amely telepíti és konfigurálja a szükséges eszközöket. Ha ezeket az eszközöket egy másik oktatóanyagban telepítette az első lépések útmutatóban, nem kell újra végrehajtania.

> [!NOTE]
> A telepítési parancsfájl a következő eszközöket telepíti:
> * [CMAK](https://cmake.org): létrehozás
> * [ARM GCC](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm): fordítás
> * [Termeszek](https://www.compuphase.com/software_termite.htm): a csatlakoztatott eszközök soros portjának kimenetének figyelése

Az eszközök telepítése:

1. A Fájlkezelőben navigáljon a tárház következő elérési útjához, és futtassa a *get-toolchain.bat* nevű telepítési parancsfájlt:

    > *getting-started\tools\get-toolchain.bat*

1. A telepítés után nyisson meg egy új konzol ablakot a telepítési parancsfájl által végrehajtott konfigurációs módosítások felismeréséhez. Ezzel a konzollal végezheti el a többi programozási feladatot az oktatóanyagban. Használhatja a Windows CMD, a PowerShell vagy a git bash for Windows rendszert.
1. A következő kód futtatásával erősítse meg, hogy a CMak 3,14-es vagy újabb verziója telepítve van.

    ```shell
    cmake --version
    ```

## <a name="create-the-cloud-components"></a>A felhőalapú összetevők létrehozása

### <a name="create-the-iot-central-application"></a>A IoT Central alkalmazás létrehozása

Az eszközök az Azure-IoT való csatlakoztatásának számos módja van. Ebből a szakaszból megtudhatja, hogyan csatlakoztatható egy eszköz az Azure IoT Central használatával. A IoT Central egy IoT-alkalmazás platform, amely csökkenti a IoT-megoldások létrehozásának és kezelésének költségeit és összetettségét.

Új alkalmazás létrehozása:
1. Az [Azure IoT Central portálon](https://apps.azureiotcentral.com/)válassza a **saját alkalmazások** lehetőséget az oldal navigációs menüjében.
1. Válassza az **+ új alkalmazás** lehetőséget.
1. Válassza az **Egyéni alkalmazások** elemet.
1. Adja hozzá az alkalmazás nevét és URL-címét.
1. A 7 napos próbaverzió aktiválásához válassza az **ingyenes** díjszabási csomagot.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-create-custom.png" alt-text="Egyéni alkalmazás létrehozása az Azure IoT Centralban":::

1. Válassza a **Létrehozás** lehetőséget.

    Miután IoT Central kiépíti az alkalmazást, a rendszer automatikusan átirányítja az új alkalmazás-irányítópultra.

    > [!NOTE]
    > Ha meglévő IoT Central alkalmazással rendelkezik, a segítségével elvégezheti az új alkalmazás létrehozása helyett a cikkben ismertetett lépéseket.

### <a name="create-a-new-device"></a>Új eszköz létrehozása

Ebben a szakaszban a IoT Central alkalmazás-irányítópult használatával hozzon létre egy új eszközt. Az újonnan létrehozott eszköz kapcsolati információit fogja használni a fizikai eszköz egy későbbi szakaszban való biztonságos csatlakoztatásához.

Eszköz létrehozása:
1. Az alkalmazás irányítópultján válassza az **eszközök** lehetőséget az oldal navigációs menüjében.
1. Válassza az **+ új** lehetőséget az **új eszköz létrehozása** ablak megnyitásához.
1. Az eszköz sablonjának **kiosztása nincs kiosztva**.
1. Adja meg a kívánt eszköz nevét és az eszköz AZONOSÍTÓját.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-create-device.png" alt-text="Eszköz létrehozása az Azure IoT Centralban":::

1. Válassza a **Létrehozás** gombot.
1. Az újonnan létrehozott eszköz megjelenik a **minden eszköz** listában.  A részletek megjelenítéséhez válassza ki az eszköz nevét.
1. A jobb felső menüsorban kattintson a **Kapcsolódás** lehetőségre az eszköz következő szakaszban való konfigurálásához használt kapcsolati adatok megjelenítéséhez.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-device-connection-info.png" alt-text="Eszköz kapcsolati adatainak megtekintése":::

1. Jegyezze fel a kapcsolati értékeket az alábbi kapcsolati karakterlánc paraméterei **között a** csatlakozási párbeszédpanelen. Ezeket az értékeket egy konfigurációs fájlba adja hozzá a következő lépésben:

    > * `ID scope`
    > * `Device ID`
    > * `Primary key`

## <a name="prepare-the-device"></a>Az eszköz előkészítése

A MXCHIP-fejlesztői készlet az Azure-hoz való csatlakoztatásához módosítania kell a Wi-Fi és az Azure IoT beállításainak konfigurációs fájlját, újra kell építenie a rendszerképet, és a képet az eszközre kell telepítenie.

### <a name="add-configuration"></a>Konfiguráció hozzáadása

1. Nyissa meg a következő fájlt egy szövegszerkesztőben:

    > *getting-started\MXChip\AZ3166\app\ azure_config. h*

1. Állítsa a Wi-Fi állandókat a helyi környezet következő értékeire.

    |Állandó név|Érték|
    |-------------|-----|
    |`WIFI_SSID` |{*A Wi-Fi SSID*}|
    |`WIFI_PASSWORD` |{*A Wi-Fi jelszava*}|
    |`WIFI_MODE` |{A *fájlban szereplő felsorolt Wi-Fii üzemmódok egyike*|

1. Állítsa be az Azure IoT-eszköz információs konstansait az Azure-erőforrások létrehozása után mentett értékekre.

    |Állandó név|Érték|
    |-------------|-----|
    |`IOT_DPS_ID_SCOPE` |{*Az azonosító hatókörének értéke*}|
    |`IOT_DPS_REGISTRATION_ID` |{*Az eszköz azonosítójának értéke*}|
    |`IOT_DEVICE_SAS_KEY` |{*Az elsődleges kulcs értéke*}|

1. Mentse és zárja be a fájlt.

### <a name="build-the-image"></a>Rendszerkép létrehozása

A konzolon vagy a Fájlkezelőben futtassa a parancsfájlt *rebuild.bat* a következő elérési úton a rendszerkép létrehozásához:

> *getting-started\MXChip\AZ3166\tools\rebuild.bat*

A létrehozás befejezését követően győződjön meg arról, hogy a bináris fájl a következő elérési úton lett létrehozva:

> *getting-started\MXChip\AZ3166\build\app\ mxchip_azure_iot. bin*

### <a name="flash-the-image"></a>A rendszerkép villanása

1. A MXCHIP fejlesztői készlet keresse meg az **Alaphelyzetbe állítás** gombot, és a Micro USB-portot. Ezeket az összetevőket a következő lépésekben használhatja. Mindkettő ki van emelve a következő képen:

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/mxchip-iot-devkit.png" alt-text="Főbb összetevők megkeresése a MXChip fejlesztői készlet-táblájában":::

1. Csatlakoztassa a Micro USB-kábelt a MXCHIP fejlesztői készlet Micro USB-portjához, majd csatlakoztassa a számítógéphez.
1. A Fájlkezelőben keresse meg az előző szakaszban létrehozott bináris fájlt.
1. Másolja a *mxchip_azure_iot. bin* bináris fájlt.
1. A Fájlkezelőben keresse meg a számítógéphez csatlakoztatott MXCHIP fejlesztői készlet eszközt. Az eszköz meghajtóként jelenik meg a rendszeren a meghajtó feliratának **AZ3166**.
1. Illessze be a bináris fájlt a MXCHIP fejlesztői készlet gyökérkönyvtárba. A villogás automatikusan elindul, és néhány másodpercen belül befejeződik.

    > [!NOTE]
    > A villogási folyamat során a zöld LED bekapcsolja a MXCHIP fejlesztői készlet.

### <a name="confirm-device-connection-details"></a>Eszköz kapcsolati adatainak megerősítése

A **termeszek** alkalmazás használatával figyelheti a kommunikációt, és ellenőrizheti, hogy az eszköz megfelelően van-e beállítva.

1. A **termeszek** elindítása.
    > [!TIP]
    > Ha nem tud csatlakozni a termeszekhez a fejlesztői készlet, telepítse a [St-link illesztőprogramot](https://my.st.com/content/ccc/resource/technical/software/driver/files/stsw-link009.zip) , és próbálkozzon újra. További lépésekért lásd:  [Hibaelhárítás](https://github.com/azure-rtos/getting-started/blob/master/docs/troubleshooting.md) .
1. Válassza a **Beállítások** lehetőséget.
1. A **soros port beállításai** párbeszédpanelen ellenőrizze a következő beállításokat, és szükség esetén frissítse a frissítést:
    * **Átviteli sebesség**: 115 200
    * **Port**: az a port, amelyhez a MXCHIP fejlesztői készlet csatlakozik. Ha a legördülő menüben több port áll rendelkezésre, akkor a megfelelő portot találja. Nyissa meg a Windows **Eszközkezelőt**, **és tekintse meg a portokat** a használni kívánt port azonosításához.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/termite-settings.png" alt-text="Beállítások megerősítése a termeszek alkalmazásban":::

1. Kattintson az OK gombra.
1. Nyomja meg az eszköz **alaphelyzetbe állítása** gombot. A gomb címkéje az eszközön, a Micro USB-összekötő közelében található.
1. A **termeszek** alkalmazásban ellenőrizze a következő ellenőrzőpont-értékeket az eszköz inicializálásának és az Azure IoT való csatlakozásának megerősítéséhez.

    ```output
    Starting Azure thread

    Initializing WiFi
        Connecting to SSID 'iot'
    SUCCESS: WiFi connected to iot

    Initializing DHCP
        IP address: 10.0.0.123
        Mask: 255.255.255.0
        Gateway: 10.0.0.1
    SUCCESS: DHCP initialized

    Initializing DNS client
        DNS address: 10.0.0.1
    SUCCESS: DNS client initialized

    Initializing SNTP client
        SNTP server 0.pool.ntp.org
        SNTP IP address: 185.242.56.3
        SNTP time update: Nov 16, 2020 23:47:35.385 UTC 
    SUCCESS: SNTP initialized

    Initializing Azure IoT DPS client
        DPS endpoint: global.azure-devices-provisioning.net
        DPS ID scope: ***
        Registration ID: ***
    SUCCESS: Azure IoT DPS client initialized

    Initializing Azure IoT Hub client
        Hub hostname: ***
        Device id: ***
        Model id: dtmi:azurertos:devkit:gsgmxchip;1
    Connected to IoTHub
    SUCCESS: Azure IoT Hub client initialized

    Starting Main loop
    ```

Tartsa nyitva a termeszet az eszköz kimenetének figyeléséhez a következő lépésekben.

## <a name="verify-the-device-status"></a>Az eszköz állapotának ellenőrzése

Az eszköz állapotának megtekintése IoT Central portálon:
1. Az alkalmazás irányítópultján válassza az **eszközök** lehetőséget az oldal navigációs menüjében.
1. Győződjön meg arról, hogy az eszköz állapota frissítve lett a **kiépített** **állapotba** .
1. Győződjön meg arról, hogy az **eszköz sablonja** frissítve lett **első lépések útmutatóra**.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-device-view-status.png" alt-text="Eszköz állapotának megtekintése IoT Central":::

## <a name="view-telemetry"></a>Telemetria megtekintése

A IoT Central segítségével megtekintheti az eszközről a felhőbe irányuló telemetria folyamatát.

A telemetria megtekintése IoT Central portálon:

1. Az alkalmazás irányítópultján válassza az **eszközök** lehetőséget az oldal navigációs menüjében.
1. Válassza ki az eszközt az eszközök listájából.
1. Tekintse meg a telemetria, mivel az eszköz üzeneteket küld a felhőnek az **Áttekintés** lapon.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-device-telemetry.png" alt-text="A IoT Central eszköz telemetria megtekintése":::

    > [!NOTE]
    > A telemetria az eszközről is figyelheti a termeszek alkalmazás használatával.

## <a name="call-a-direct-method-on-the-device"></a>Közvetlen metódus hívása az eszközön

A IoT Central is használhatja az eszközön megvalósított közvetlen metódus meghívására. A közvetlen metódusok névvel rendelkeznek, és szükség esetén JSON-adattartalommal, konfigurálható kapcsolattal és metódus-időtúllépéssel rendelkezhetnek. Ebben a szakaszban egy olyan metódust hív meg, amely lehetővé teszi egy LED be-vagy kikapcsolását.

Metódus meghívása IoT Central portálon:

1. Válassza a **parancs** lapot az eszköz oldaláról.
1. Válassza az **állapot** lehetőséget, majd válassza a **Futtatás** lehetőséget.  A LED-jelzőfény be kell kapcsolni.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-invoke-method.png" alt-text="Közvetlen metódus hívása egy eszközön":::

1. Válassza ki az **állapot** kijelölését, és válassza a **Futtatás** lehetőséget. A LED-jelzőfénynek ki kell kapcsolnia.

## <a name="view-device-information"></a>Eszköz adatainak megtekintése

Az eszköz adatait a IoT Central tekintheti meg.

Az eszköz oldaláról válassza a **Névjegy** lapot.

:::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-device-about.png" alt-text="Az eszközre vonatkozó információk megtekintése IoT Central":::

## <a name="debugging"></a>Hibakeresés

Az alkalmazás hibakereséséhez lásd: [hibakeresés a Visual Studio Code](https://github.com/azure-rtos/getting-started/blob/master/docs/debugging.md)-ban.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az oktatóanyagban létrehozott Azure-erőforrásokra, törölheti őket a IoT Central portálról. Ha továbbra is a Első lépések útmutatóban egy másik oktatóanyagot folytat, megtarthatja a már létrehozott erőforrásokat, és újra felhasználhatja azokat.

A teljes Azure IoT Central minta alkalmazás és annak összes eszköze és erőforrásának eltávolítása:
1. Válassza ki  >  **az alkalmazás** felügyeletét.
1. Válassza a **Törlés** elemet.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létrehozott egy egyéni rendszerképet, amely az Azure RTOS-mintakód részét képezi, majd a rendszerképet a MXCHIP fejlesztői készlet-eszközre villant fel. A IoT Central portál használatával Azure-erőforrásokat is létrehozhat, a MXCHIP-fejlesztői készlet biztonságosan kapcsolódhat az Azure-hoz, megtekintheti a telemetria, és üzeneteket küldhet.

* Az eszközök fejlesztői számára a javasolt következő lépés az, hogy megtekintse a sorozat többi oktatóanyagát, amely az [Azure IoT Embedded-eszközök fejlesztésével kapcsolatos első lépéseket](quickstart-device-development.md)ismerteti.
* Ha az útmutató lépéseinek követése után az eszköz inicializálására vagy csatlakoztatására vonatkozó problémák merültek fel, tekintse meg a [Hibaelhárítás](https://github.com/azure-rtos/getting-started/blob/master/docs/troubleshooting.md)című témakört.
* Ha többet szeretne megtudni arról, hogyan használják az Azure RTOS-összetevőket az oktatóanyaghoz tartozó mintakódban, tekintse meg [Az Azure RTOS használata című részt a első lépések útmutatóban](https://github.com/azure-rtos/getting-started/blob/master/docs/using-azure-rtos.md).

    > [!IMPORTANT]
    > Az Azure RTOS lehetővé teszi a számítógépgyártók számára a kommunikáció biztonságossá tételét, valamint kód-és adatelkülönítést a mögöttes MCU/MPU hardveres védelmi mechanizmusok használatával. Az egyes SZÁMÍTÓGÉPGYÁRTÓK azonban végső soron felelősek annak biztosításáért, hogy az eszközeik megfeleljenek a változó biztonsági követelményeknek.

