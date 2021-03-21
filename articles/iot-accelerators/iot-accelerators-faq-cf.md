---
title: Csatlakoztatott Factory megoldás – GYIK – Azure | Microsoft Docs
description: Ez a cikk a csatlakoztatott gyári megoldás-gyorsító szolgáltatással kapcsolatos gyakori kérdésekre ad választ. Ide tartoznak a GitHub-adattárra mutató hivatkozások.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: c6a695030d399cb1609c63ef5652419a9bef1bbf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102198982"
---
# <a name="frequently-asked-questions-for-connected-factory-solution-accelerator"></a>Gyakori kérdések a csatlakoztatott Factory megoldás-gyorsító szolgáltatásról

Lásd még: általános [Gyakori kérdések](iot-accelerators-faq.md) a IoT megoldási gyorssegédekhez.

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerator"></a>Hol találhatók a megoldás-gyorsító forráskódja?

A forráskódot a következő GitHub-adattár tárolja:

* [Csatlakoztatott gyári megoldás gyorsítása](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-is-opc-ua"></a>Mi az OPC UA?

A 2008-ben kiadott OPC Unified Architecture (UA) platform-független, szolgáltatás-orientált együttműködési szabvány. Az OPC UA-t különböző ipari rendszerek és eszközök, például iparági számítógépek, PLC és érzékelők használják. Az OPC UA beépített biztonsággal integrálja az OPC klasszikus specifikációinak funkcióit egy bővíthető keretrendszerbe. Ez az OPC Foundation által vezérelt standard. Az [OPC Foundation](https://opcfoundation.org/) egy nonprofit szervezet, amely több mint 440 taggal rendelkezik. A szervezet célja az OPC-specifikációk használata a több gyártótól származó, többplatformos, biztonságos és megbízható együttműködés megkönnyítésére a következő módszerekkel:

* Infrastruktúra
* Specifikációk
* Technológia
* Folyamatok

### <a name="why-did-microsoft-choose-opc-ua-for-the-connected-factory-solution-accelerator"></a>Miért választotta ki a Microsoft az OPC UA-t a csatlakoztatott Factory megoldás-gyorsító számára?

A Microsoft az OPC UA-t választotta, mivel ez egy nyílt, nem védett platform, független, iparág által elismert és bevált szabvány. Követelmény a Industrie 4,0 (RAMI 4.0) hivatkozási architektúrájának megoldásához, amely biztosítja a különböző gyártási folyamatok és berendezések széles körének együttműködési képességét. A Microsoft az ügyfelektől igényt tart az Industrie 4,0-megoldások létrehozásához. Az OPC UA támogatása segít csökkenteni a korlátot az ügyfelek számára a célok eléréséhez, és azonnali üzleti értéket biztosít számukra.

### <a name="how-do-i-add-a-public-ip-address-to-the-simulation-vm"></a>Hogyan adjon hozzá egy nyilvános IP-címet a szimulációs virtuális géphez?

Az IP-cím hozzáadására két lehetőség áll rendelkezésre:

* Használja a PowerShell-szkriptet `Simulation/Factory/Add-SimulationPublicIp.ps1` a [tárházban](https://github.com/Azure/azure-iot-connected-factory). Adja át a központi telepítési nevet paraméterként. Helyi központi telepítéshez használja a következőt: `<your username>ConnFactoryLocal` . A szkript kinyomtatja a virtuális gép IP-címét.

* A Azure Portal keresse meg a telepítéshez tartozó erőforráscsoportot. A helyi telepítés kivételével az erőforráscsoport a következő névvel van megadva: megoldás vagy központi telepítési név. A Build parancsfájlt használó helyi telepítéshez az erőforráscsoport neve `<your username>ConnFactoryLocal` . Most adjon hozzá egy új **nyilvános IP-cím** erőforrást az erőforráscsoporthoz.

> [!NOTE]
> Mindkét esetben ügyeljen arra, hogy a legújabb javításokat az [Ubuntu webhelyének](https://wiki.ubuntu.com/Security/Upgrades)utasításait követve telepítse. Tartsa naprakészen a telepítést mindaddig, amíg a virtuális gép nyilvános IP-címen keresztül elérhető.

### <a name="how-do-i-remove-the-public-ip-address-to-the-simulation-vm"></a>Hogyan eltávolítja a nyilvános IP-címet a szimulációs virtuális gépre?

Az IP-cím eltávolítására két lehetőség áll rendelkezésre:

* Használja a [tárház](https://github.com/Azure/azure-iot-connected-factory)PowerShell-parancsfájl szimulációját/gyárát/Remove-SimulationPublicIp.ps1. Adja át a központi telepítési nevet paraméterként. Helyi központi telepítéshez használja a következőt: `<your username>ConnFactoryLocal` . A szkript kinyomtatja a virtuális gép IP-címét.

* A Azure Portal keresse meg a telepítéshez tartozó erőforráscsoportot. A helyi telepítés kivételével az erőforráscsoport a következő névvel van megadva: megoldás vagy központi telepítési név. A Build parancsfájlt használó helyi telepítéshez az erőforráscsoport neve `<your username>ConnFactoryLocal` . Most távolítsa el a **nyilvános IP-cím** erőforrást az erőforráscsoporthoz.

### <a name="how-do-i-sign-in-to-the-simulation-vm"></a>Hogyan bejelentkezni a szimulációs virtuális gépre?

A szimulációs virtuális gépre való bejelentkezés csak akkor támogatott, ha a tárházban lévő PowerShell-parancsfájl használatával telepítette a megoldást `build.ps1` . [](https://github.com/Azure/azure-iot-connected-factory)

Ha a www.azureiotsolutions.com-ből telepítette a megoldást, nem tud bejelentkezni a virtuális gépre. Nem lehet bejelentkezni, mert a jelszó véletlenszerűen lett létrehozva, és nem állítható vissza.

1. Adjon hozzá egy nyilvános IP-címet a virtuális géphez. Lásd: [Hogyan nyilvános IP-cím hozzáadása a szimulációs virtuális géphez?](#how-do-i-remove-the-public-ip-address-to-the-simulation-vm)
1. Hozzon létre egy SSH-munkamenetet a virtuális géphez a virtuális gép IP-címének használatával.
1. A használandó Felhasználónév a következő: `docker` .
1. A használandó jelszó az üzembe helyezéshez használt verziótól függ:
    * A build.ps1 parancsfájllal a 2017. június 1. előtt üzembe helyezett megoldások esetében a jelszó a következő: `Passw0rd` .
    * A build.ps1 parancsfájllal, a 2017. június 1. után üzembe helyezett megoldások esetében a fájl jelszava található `<name of your deployment>.config.user` . A jelszót a **VmAdminPassword** -beállítás tárolja. A jelszót véletlenszerűen hozza létre a rendszer a telepítés során, kivéve, ha a `build.ps1` parancsfájl paraméterrel adja meg `-VmAdminPassword`

### <a name="how-do-i-stop-and-start-all-docker-processes-in-the-simulation-vm"></a>Hogyan állítsa le és indítsa el az összes Docker-folyamatot a szimulációs virtuális gépen?

1. Jelentkezzen be a szimulációs virtuális gépre. Lásd: [Hogyan bejelentkezés a szimulációs virtuális gépre?](#how-do-i-sign-in-to-the-simulation-vm)
1. A következő futtatásával ellenőrizhető, hogy mely tárolók aktívak: `docker ps` .
1. Az összes szimulációs tároló leállításához futtassa a következőt: `./stopsimulation` .
1. Az összes szimulációs tároló elindítása:
    * Exportáljon egy **IOTHUB_CONNECTIONSTRING** nevű rendszerhéj-változót. Használja az **IotHubOwnerConnectionString** beállítás értékét a `<name of your deployment>.config.user` fájlban. Például:

        ```sh
        export IOTHUB_CONNECTIONSTRING="HostName={yourdeployment}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={your key}"
        ```

    * Futtassa az `./startsimulation` parancsot.

### <a name="how-do-i-update-the-simulation-in-the-vm"></a>Hogyan frissíteni a szimulációt a virtuális gépen?

Ha módosította a szimulációt, az paranccsal használhatja az adattár PowerShell-parancsfájlját `build.ps1` [](https://github.com/Azure/azure-iot-connected-factory) `updatedimulation` . Ez a szkript létrehozza az összes szimulációs összetevőt, leállítja a szimulációt a virtuális gépen, feltölti, telepíti és elindítja azokat.

### <a name="how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution"></a>Hogyan a megoldás által használt IoT hub-beli kapcsolatok sztringjét?

Ha a megoldást a `build.ps1` [tárházban](https://github.com/Azure/azure-iot-connected-factory)lévő parancsfájllal telepítette, akkor a **IotHubOwnerConnectionString** értéke a `<name of your deployment>.config.user` fájlban található.

A Azure Portal használatával is megkeresheti a kapcsolatok karakterláncát. A telepítés erőforráscsoport erőforrás csoportjában található IoT Hub erőforrásban keresse meg a kapcsolódási karakterlánc beállításait.

### <a name="which-iot-hub-devices-does-the-connected-factory-simulation-use"></a>Mely IoT Hub eszközök használják a csatlakoztatott gyári szimulációt?

A szimuláció saját maga regisztrálja a következő eszközöket:

* proxy. Peking. Corp. contoso
* proxy. capetown. Corp. contoso
* proxy. Mumbai. Corp. contoso
* proxy. munich0. Corp. contoso
* proxy. Rio. Corp. contoso
* proxy. Seattle. Corp. contoso
* kiadó. Peking. Corp. contoso
* közzétevő. capetown. Corp. contoso
* kiadó. Mumbai. Corp. contoso
* közzétevő. munich0. Corp. contoso
* közzétevő. Rio. Corp. contoso
* kiadó. Seattle. Corp. contoso

Az Azure CLI-eszköz [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/) vagy [IoT-bővítményének](https://github.com/Azure/azure-iot-cli-extension) használatával megtekintheti, hogy mely eszközök legyenek regisztrálva a megoldás által használt IoT hub-ban. Az Eszközkezelő használatához szüksége lesz az IoT hub kapcsolódási karakterláncára az üzemelő példányban. Az Azure CLI IoT-bővítményének használatához a IoT Hub neve szükséges.

### <a name="how-can-i-get-log-data-from-the-simulation-components"></a>Hogyan szerezhetők be a szimulációs összetevőkből származó adatok?

A szimulációs napló összes összetevője a naplófájlokban. Ezek a fájlok a virtuális gépen találhatók a mappában `home/docker/Logs` . A naplók beolvasásához használhatja a `Simulation/Factory/Get-SimulationLogs.ps1` [tárházban](https://github.com/Azure/azure-iot-connected-factory)található PowerShell-szkriptet.

A szkriptnek be kell jelentkeznie a virtuális gépre. Előfordulhat, hogy meg kell adnia a bejelentkezéshez szükséges hitelesítő adatokat. Lásd: [Hogyan bejelentkezés a szimulációs virtuális gépre?](#how-do-i-sign-in-to-the-simulation-vm) a hitelesítő adatok megkereséséhez.

A parancsfájl egy nyilvános IP-címet helyez el/távolít el a virtuális géphez, ha még nem rendelkezik ilyennel, és eltávolítja azt. A parancsfájl egy archív fájlba helyezi az összes naplófájlt, és letölti az archívumot a fejlesztői munkaállomásra.

Másik lehetőségként jelentkezzen be a virtuális gépre SSH-n keresztül, és ellenőrizze a naplófájlokat futásidőben.

### <a name="how-can-i-check-if-the-simulation-is-sending-data-to-the-cloud"></a>Hogyan ellenőrizhető, hogy a szimuláció adatokat küld a felhőbe?

Az [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer) vagy az [Azure IoT CLI-figyelő-Events](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-monitor-events) paranccsal ellenőrizheti, hogy az egyes eszközökről IoT hub küldött adatok megtekinthetők-e. Ezeknek az eszközöknek a használatához ismernie kell a IoT hub kapcsolódási karakterláncát az üzemelő példányban. Lásd: [hogyan a saját megoldás által használt IoT hub-beli kapcsolatok karakterláncának megkeresése?](#how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution)

Vizsgálja meg az egyik közzétevő eszköz által továbbított adatmennyiséget:

* kiadó. Peking. Corp. contoso
* közzétevő. capetown. Corp. contoso
* kiadó. Mumbai. Corp. contoso
* közzétevő. munich0. Corp. contoso
* közzétevő. Rio. Corp. contoso
* kiadó. Seattle. Corp. contoso

Ha nem látja a IoT Hubba elküldett információt, akkor probléma van a szimulációval. Első elemzési lépésként elemezni kell a szimulációs összetevők naplófájljait. Lásd: hogyan szerezhetők [be a naplózási adatok a szimulációs összetevőkből?](#how-can-i-get-log-data-from-the-simulation-components) Ezután próbálja meg leállítani és elindítani a szimulációt, és ha még nem küldtek el adatmennyiséget, frissítse a szimulációt teljesen. Lásd: [a virtuális gép szimulációjának frissítése hogyan](#how-do-i-update-the-simulation-in-the-vm)

### <a name="how-do-i-enable-an-interactive-map-in-my-connected-factory-solution"></a>Hogyan interaktív térképet engedélyezni a csatlakoztatott gyári megoldásban?

Ha interaktív leképezést szeretne engedélyezni a csatlakoztatott gyári megoldásban, rendelkeznie kell egy Azure Maps fiókkal.

A [www.azureiotsolutions.com](https://www.azureiotsolutions.com)-ből való üzembe helyezéskor a telepítési folyamat hozzáadja az Azure Maps fiókot a megoldás-gyorsító szolgáltatásokat tartalmazó erőforráscsoporthoz.

A csatlakoztatott Factory GitHub-tárház parancsfájl használatával történő üzembe helyezése során `build.ps1` állítsa be a környezeti változót `$env:MapApiQueryKey` a létrehozási ablakban a [Azure Maps-fiók kulcsaként](../azure-maps/how-to-manage-account-keys.md). Az interaktív térkép automatikusan engedélyezve lesz.

Az üzembe helyezés után hozzáadhat egy Azure Maps-fiókhoz tartozó kulcsot is a megoldás-gyorsító szolgáltatáshoz. Navigáljon a Azure Portal, és nyissa meg a App Service erőforrást a csatlakoztatott gyár üzemelő példányában. Navigáljon az **Alkalmazásbeállítások elemre**, ahol megtalálhatja a szakasz **alkalmazásának beállításait**. Állítsa a **MapApiQueryKey** a [Azure Maps-fiók kulcsára](../azure-maps/how-to-manage-account-keys.md). Mentse a beállításokat, majd navigáljon az **áttekintéshez** , és indítsa újra a app Service.

### <a name="how-do-i-create-an-azure-maps-account"></a>Hogyan hozzon létre egy Azure Maps fiókot?

Lásd: [a Azure Maps-fiók és-kulcsok kezelése](../azure-maps/how-to-manage-account-keys.md).

### <a name="how-to-obtain-your-azure-maps-account-key"></a>A Azure Maps-fiók kulcsának beszerzése

Lásd: [a Azure Maps-fiók és-kulcsok kezelése](../azure-maps/how-to-manage-account-keys.md).

### <a name="how-do-enable-the-interactive-map-while-debugging-locally"></a>Hogyan engedélyezhető az interaktív térkép a helyi hibakeresés során?

Ha a helyi hibakeresés során engedélyezni szeretné az interaktív leképezést, állítsa be a beállítás értékét a `MapApiQueryKey` fájlokban `local.user.config` és a `<yourdeploymentname>.user.config` központi telepítés gyökerében a korábban átmásolt **QueryKey** értékére.

### <a name="how-do-i-use-a-different-image-at-the-home-page-of-my-dashboard"></a>Hogyan az irányítópult kezdőlapján egy másik rendszerképet használok?

Ha módosítani szeretné a statikus képet, amely az irányítópult kezdőlapját mutatja, cserélje le a rendszerképet `WebApp\Content\img\world.jpg` . Ezután építse újra a WebAppot, és telepítse újra.

### <a name="how-do-i-use-non-opc-ua-devices-with-connected-factory"></a>Hogyan nem OPC UA-eszközöket használ a csatlakoztatott gyárral?

Telemetria-adatok küldése nem OPC UA-eszközökről a csatlakoztatott gyárba:

1. [Konfigurálja az új állomást a fájlban lévő csatlakoztatott gyári topológiában](iot-accelerators-connected-factory-configure.md) `ContosoTopologyDescription.json` .

1. A telemetria-adatot a csatlakoztatott gyári kompatibilis JSON-formátumban kell betölteni:

    ```json
    [
      {
        "ApplicationUri": "<the_value_of_OpcUri_of_your_station",
        "DisplayName": "<name_of_the_datapoint>",
        "NodeId": "value_of_NodeId_of_your_datapoint_in_the_station",
        "Value": {
          "Value": <datapoint_value>,
          "SourceTimestamp": "<timestamp>"
        }
      }
    ]
    ```

1. A formátuma `<timestamp>` : `2017-12-08T19:24:51.886753Z`

1. Indítsa újra a csatlakoztatott gyári App Service.

### <a name="next-steps"></a>Következő lépések

Megismerheti az IoT-megoldásgyorsítók egyéb szolgáltatásait és funkcióit is:

* [Csatlakoztatott gyári megoldás-gyorsító üzembe helyezése](quickstart-connected-factory-deploy.md)
* [IoT-biztonság létrehozása az alapoktól](../iot-fundamentals/iot-security-ground-up.md)